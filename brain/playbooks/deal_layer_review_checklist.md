# Deal Layer PR Review Checklist

Before approving any deal layer PR, verify:

## State Machine
- [ ] No direct `deal.status` writes — must use `state_machine.transition()`
- [ ] State machine uses `db.flush()` not `db.commit()`
- [ ] Callers of `transition()` call `db.commit()` after
- [ ] New DealStatus values are added to VALID_TRANSITIONS

## Notifications
- [ ] Notifications use `NotificationService(db).create_notification()`
- [ ] NOT `notification_service.send_notification()` which doesn't exist
- [ ] _STATUS_NOTIFICATION_MAP has correct mappings (PAID→PAYMENT_RELEASED, not DELIVERABLE_APPROVED)
- [ ] System events (actor_id=None) notify both parties
- [ ] User events notify only the other party
- [ ] Error handling uses `logger.error(exc_info=True)` not bare except

## Event Logging
- [ ] Event logging uses `deal_service.log_event()` not direct DealEvent
- [ ] Correct DealEventType used:
  - OFFER_EXPIRED for offer expiration (not OFFER_WITHDRAWN)
  - DEAL_EXPIRED for deal expiration
- [ ] DealEvent rows are never updated or deleted (append-only)

## Offer Lifecycle
- [ ] reject_offer checks `offer.status == PENDING` first
- [ ] withdraw_offer checks `offer.status == PENDING` first
- [ ] accept_offer uses `with_for_update()` row lock
- [ ] accept_offer checks `offer.expires_at <= now()` before accepting

## Schema Validation
- [ ] Budget range validated (min <= max, both > 0)
- [ ] Timeline validated (end > start)
- [ ] fee_cents > 0
- [ ] deliverables non-empty
- [ ] Due dates in future
- [ ] Use DeliverableType enum not raw str

## Data Types
- [ ] Money values are Integer cents, never Float
- [ ] Status query params use DealStatus enum not raw str
- [ ] New routers registered behind DEAL_LAYER_ENABLED flag

## Mobile (if applicable)
- [ ] Types in mobile/src/types/deals.ts
- [ ] API functions in mobile/src/lib/api.ts
- [ ] Screens in mobile/src/screens/deals/

## Tests
- [ ] Tests assert Notification rows created on transitions
- [ ] Tests check for expired offer rejection (400 error)
- [ ] Tests check for concurrent accept prevention
- [ ] Tests verify correct DealEventType on expiration
## Webhooks & External Integrations (Phase 3+)
- [ ] Webhook handler has idempotency guard at top (terminal-status check)
- [ ] Webhook handler validates signature before parsing body
- [ ] No `try/except: pass` — `logger.exception` at minimum, raise in webhooks
- [ ] Enum fields from JSON use `EnumClass(value)` conversion
- [ ] Routes calling external services commit AFTER the service returns
- [ ] `deals_router` registered in `main.py` DEAL_LAYER_ENABLED block
- [ ] APScheduler block present in `main.py` DEAL_LAYER_ENABLED block
- [ ] No new `skipif(True)` tests added
