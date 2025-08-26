## Confirmation Page Data Layer Enhancement


### High-level summary
- The products collection remains named `product` (no rename). Each item may include an `attributes` object for per-product metadata (e.g., `upsell`, `switchsell`).
- A new `transaction` object captures confirmation identifiers, loyalty points, payments, and pricing summary.
- Payment info moved from top-level `paymentinfo` to `transaction.payments` with multi-method support and numeric amounts.
- `customer.profile` object (first/last name, email, phone) is newly introduced.
- Several fields switch from string to number (e.g., product prices, payment amounts), and new boolean fields are present.
- Customer membership fields remain flat within `customer`.

---

### Changes by category

#### Renamed
- None

#### Moved
- Payment information:
  - `paymentinfo.*` (old) → `transaction.payments.*` (new)

#### Data type changes
- `product[].productprice`: string (e.g., "1168.65") → number (e.g., 100)
- `transaction.payments.methods[].amount`: numeric amounts introduced (previously a string like `paymentvisa`)
- `transaction.payments.splitevenly`: boolean introduced

#### Deleted
- Top-level `paymentinfo` is removed. Its details are represented in `transaction.payments`.

---

### Structural additions
- `transaction` object with:
  - `transaction.confirmation[]` (SPNR, flight, hotel, car, bookingID)
  - `transaction.points` (earned/burned)
  - `transaction.payments` (currency, numberofmethods, splitevenly, methods[])
  - `transaction.pricingsummary` (totals, taxes, postpay breakdown)
- `customer.profile` with PII fields: `firstName`, `lastName`, `email`, `phone`
- `product[].attributes` for per-product metadata such as `upsell`, `switchsell`, and add-on descriptors
- New product type value: `addon`

---

### Net-new properties, data dictionary

The following properties are net new and not currently in the event object being passed into `adobeDataLayer` on confirmation page. Types use JSON schema conventions.

| Property | Type | Description | Example |
|---|---|---|---|
| customer.profile | object | Customer profile PII container. | { firstName, lastName, email, phone } |
| customer.profile.firstName | string | Given name. | "test" |
| customer.profile.lastName | string | Surname. | "tester" |
| customer.profile.email | string | Email address. | "test@tester.com" |
| customer.profile.phone | string | Phone number. | "214-555-5555" |
| transaction | object | Container for post-purchase details. | {...} |
| transaction.confirmation | array<object> | List of confirmation identifiers by type. | [{ type: "spnr", value: "FSAGENA" }] |
| transaction.confirmation[].type | string | Identifier category. | "bookingID" |
| transaction.confirmation[].value | string | Identifier value. | "1asdf1324fksdf23432d" |
| transaction.points | object | Loyalty points summary. | { earned: {...}, burned: {...} } |
| transaction.points.earned | object | Earned points details. | { total: 2000, average: 1000 } |
| transaction.points.earned.total | number | Total points earned. | 2000 |
| transaction.points.earned.average | number | Average points earned per unit (e.g., segment). | 1000 |
| transaction.points.burned | object | Burned points details. | { total: 1000 } |
| transaction.points.burned.total | number | Total points spent. | 1000 |
| transaction.payments | object | Payment breakdown data. | {...} |
| transaction.payments.currency | string | ISO currency code for amounts. | "USD" |
| transaction.payments.numberofmethods | number | Count of payment methods used. | 5 |
| transaction.payments.splitevenly | boolean | Whether tender was evenly split. | false |
| transaction.payments.methods | array<object> | List of payment methods and amounts. | [{ type: "visa", amount: 1234 }] |
| transaction.payments.methods[].type | string | Payment method type. | "applepay" |
| transaction.payments.methods[].amount | number | Amount charged in `currency`. | 15000 |
| transaction.pricingsummary | object | Price totals, taxes, promotions, and postpay amounts. | {...} |
| transaction.pricingsummary.packagetotal | number | Final package total. | 214577 |
| transaction.pricingsummary.packagesubtotal | number | Package subtotal before promos/taxes. | 247588 |
| transaction.pricingsummary.supplierpromotion | number | Supplier-funded promotion value. | 10 |
| transaction.pricingsummary.customerpromotion | number | Customer/applied promo value. | 10 |
| transaction.pricingsummary.airtaxes | number | Air-related taxes. | 4213 |
| transaction.pricingsummary.othertaxes | number | Other taxes. | 14512 |
| transaction.pricingsummary.totalpaidtoday | number | Amount paid at booking time. | 189362 |
| transaction.pricingsummary.postpay | array<object> | Postpay obligations by type. | [{ type: "hotel", amount: 16875 }] |
| transaction.pricingsummary.postpay[].type | string | Category for postpay amount. | "taxes" |
| transaction.pricingsummary.postpay[].amount | number | Amount due post-purchase. | 250 |
| product[].attributes | object | Per-product metadata. | { upsell, switchsell } |
| product[].attributes.upsell | number | Upsell amount/value (unit TBD). | 100 |
| product[].attributes.switchsell | boolean | Car switch-sell flag. | true |
| product[].attributes.state | string | Add-on state/region when applicable. | "Texas" |
| product[].attributes.name | string | Human-readable add-on name. | "Travel Insurance" |
| product[].attributes.bounds | number | Add-on bounds or quantity (context-specific). | 2 |

Notes:
- `product[].productprice` is now numeric (the property existed previously but its type changed).
- A product type value `addon` is supported alongside `flight`, `hotel`, `car`, `room`.
- `bookingID` should be the the number that you see in the last path level of confirmation page url with `-` removed from string. this value also shows up in a query parameter called `bookingID`
- Boolean flags are emitted as true booleans (not strings)


