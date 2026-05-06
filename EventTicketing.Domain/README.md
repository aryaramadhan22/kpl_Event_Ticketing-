# Domain Layer

Contains all core business logic with no external dependencies.

## Aggregates
- **Event** — manages event lifecycle (Draft → Published → Completed/Cancelled) and ticket categories
- **Booking** — manages ticket reservation and payment (PendingPayment → Paid/Expired/Refunded)
- **Refund** — manages refund lifecycle (Requested → Approved/Rejected → PaidOut)

## Value Objects
- **Money** — represents amount + currency, enforces non-negative values

## Domain Events
- EventCreated, EventPublished, EventCancelled
- TicketCategoryCreated, TicketCategoryDisabled
- TicketReserved, BookingPaid, BookingExpired, TicketCheckedIn
- RefundRequested, RefundApproved, RefundRejected, RefundPaidOut
