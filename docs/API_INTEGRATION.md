# Posting from other applications

Chopdi keeps the books. Other applications, such as billing, fee-collection or e-commerce systems, keep their own workflows and post the accounting side to Chopdi through its API. Chopdi never changes the other application's data. This is a design outline; it is built in build-order step 6 (`docs/SCOPE.md`).

## How an application connects

1. **An admin creates an API user** for the application, with a role that allows create and submit on the documents it posts. The role covers only the companies it posts for, through a user permission on Company.
2. **The admin generates its API key and secret.** The application stores them as a secret, so they're never shown again.
3. **Every request carries two headers:**
   - `Authorization: token <key>:<secret>`;
   - `Idempotency-Key`: the application's own id for the event.

   The same key with the same body returns the first result. The same key with a different body is refused with 409.
4. **The application posts when its own transaction commits,** and retries failures from its own job queue. A delay in Chopdi never blocks the other application.

## Typical events

| Event in the application | Chopdi document | Notes |
| --- | --- | --- |
| Amount agreed or billed | Sales Invoice to the customer, one line per component | The customer is matched or created by the application's own id, stored in a custom field. |
| Payment received | Payment (Receive) against that invoice | The payment mode maps to a configured account. The application's receipt number goes in the reference. |
| Refund made | Payment (Pay) against the same invoice | Records a return completed elsewhere. |
| Discount or concession | Credit note, or Journal Entry to a discount account | |
| Payment reversed (bounced cheque) | The payment is cancelled, reversing its ledger rows | Never deleted. |

- **Formats:** amounts are decimal strings in the company currency, and dates are ISO.
- **Mapping:** how the application's components and payment modes map to Chopdi's items and accounts is set on a settings screen per company.

## To decide when built

- Several sites of one organisation: separate companies, or one company with an accounting dimension.
- Accrual (post the invoice when billed) or cash basis (post only payments).
- Backfill: posting history from a chosen date.
