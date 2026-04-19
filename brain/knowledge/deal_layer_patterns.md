# Deal Layer Patterns and Anti-Patterns

## Critical Rules (violations cause data corruption)

### 1. State transitions
CORRECT:
```python
state_machine.transition(deal.id, DealStatus.BRIEF_SENT, actor_id, payload, db)
db.commit()  # route commits, not state machine
```

WRONG:
```python
deal.status = DealStatus.BRIEF_SENT  # never do this
db.commit()
```

### 2. State machine flush rule
transition() calls db.flush() only, never db.commit().
The route or job that calls transition() owns db.commit().

CORRECT in state machine:
```python
db.flush()
db.refresh(deal)
return deal
```

WRONG in state machine:
```python
db.commit()  # breaks transactional safety
```

### 3. Notification wiring
CORRECT:
```python
NotificationService(db).create_notification(
    user_id=recipient_id,
    notification_type=notification_type.value,
    title=title,
    message=message,
    actor_id=actor_id,
    related_entity_type="deal",
    related_entity_id=deal.id,
)
```

WRONG:
```python
notification_service.send_notification(...)  # does not exist
```

### 4. Event logging
CORRECT:
```python
deal_service.log_event(deal_id, event_type, actor_id, payload, db)
```

WRONG:
```python
db.add(DealEvent(...))  # bypasses validation
```

### 5. Audit event types
CORRECT event types for specific actions:
- Offer expired → `DealEventType.OFFER_EXPIRED`
- Deal expired → `DealEventType.DEAL_EXPIRED`
- Offer rejected by other party → `DealEventType.OFFER_REJECTED`
- Offer withdrawn by proposer → `DealEventType.OFFER_WITHDRAWN`

WRONG:
Using `OFFER_WITHDRAWN` for expiration (corrupts audit trail)

### 6. Money
CORRECT: `fee_cents: int = 2500` (meaning $25.00)
WRONG: `fee: float = 25.0`

### 7. Status filter validation
CORRECT:
```python
status: Optional[DealStatus] = Query(None)
```

WRONG:
```python
status: Optional[str] = Query(None)
```

### 8. Offer lifecycle preconditions
- Every offer mutation must check `offer.status == PENDING` first
- accept_offer must use `with_for_update()` row lock
- accept_offer must check `offer.expires_at <= now()` and reject

### 9. DealEvent is append-only
- Never: `db.query(DealEvent).filter(...).update(...)`
- Never: `db.delete(event)`
- Only: `db.add(DealEvent(...)); db.flush()`

### 10. System event notifications
When `actor_id` is None (system-initiated transition):
- Notify BOTH parties, not just one

When `actor_id` is set (user-initiated):
- Notify only the other party

### 11. Notification map accuracy
CORRECT mapping:
```python
_STATUS_NOTIFICATION_MAP = {
    DealStatus.BRIEF_SENT:     NotificationType.DEAL_BRIEF_RECEIVED,
    DealStatus.NEGOTIATING:    NotificationType.DEAL_OFFER_RECEIVED,
    DealStatus.OFFER_ACCEPTED: NotificationType.DEAL_OFFER_ACCEPTED,
    DealStatus.CONTRACTED:     NotificationType.DEAL_CONTRACT_READY,
    DealStatus.DELIVERED:      NotificationType.DEAL_DELIVERABLE_APPROVED,
    DealStatus.PAID:           NotificationType.DEAL_PAYMENT_RELEASED,
    DealStatus.DISPUTED:       NotificationType.DEAL_DISPUTE_OPENED,
}
```

WRONG (shifted by one):
- PAID → DELIVERABLE_APPROVED (should be PAYMENT_RELEASED)
- COMPLETE → PAYMENT_RELEASED (incorrect, should not map)

### 12. Skip redundant OUTREACH state
CORRECT:
```python
deal = Deal(..., status=DealStatus.BRIEF_SENT, ...)
log_event(deal.id, DealEventType.BRIEF_SENT, actor_id, payload, db)
db.commit()
```

WRONG:
```python
deal = Deal(..., status=DealStatus.OUTREACH, ...)
transition(deal.id, DealStatus.BRIEF_SENT, ...)  # Creates 2 events for 1 action
```

### 13. N+1 queries in get_thread()
CORRECT:
```python
actor_ids = {e.actor_id for e in events if e.actor_id}
actors = {}
if actor_ids:
    users = db.query(User).filter(User.id.in_(actor_ids)).all()
    actors = {u.id: u for u in users}
```

WRONG:
```python
for event in events:
    actor = db.query(User).filter(User.id == event.actor_id).first()  # N+1 queries
```

### 14. Schema validation
Always validate:
- Budget range (min <= max, both > 0)
- Timeline (end > start)
- Fee_cents > 0
- Deliverables non-empty
- Due dates in future
- Use Enum types not raw strings for deliverable types