## Data Layer Migration Guide: old.js → new.js (revised)

This guide reflects the latest `new.js` provided. It summarizes structural/schema changes from `old.js`, highlights renamed/moved/type-changed/deleted items, lists net-new properties with a data dictionary, and flags inconsistencies to resolve before rollout.

### High-level summary
- The products collection remains named `product` (no rename). Each item may include an `attributes` object for per-product metadata (e.g., `upsell`, `switchsell`).
- A new `transaction` object captures confirmation identifiers, loyalty points, payments, and pricing summary.
- Payment info moved from top-level `paymentinfo` to `transaction.payments` with multi-method support and numeric amounts.
- `customer.profile` (first/last name, email, phone) is newly introduced.
- Several fields switch from string to number (e.g., product prices, payment amounts), and new boolean fields are present.
- Customer membership fields remain flat within `customer` (no nested `customer.member`).

---

### Changes by category

#### Renamed
- None. The array remains `product` (no `products` rename in this version).

#### Moved
- Payment information:
  - `paymentinfo.*` (old) → `transaction.payments.*` (new)
- Product details:
  - Flight: `flighttype` remains at the product item root (not moved into `attributes`).
  - Room: `numberofbeds`, `bedtype` remain at the product item root (not moved into `attributes`).

#### Data type changes
- `product[].productprice`: string (e.g., "1168.65") → number (e.g., 0 or 50/75 for add-ons)
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

### Field-by-field comparison highlights
- Page and session
  - `page.responsivesize`: value change ("small" → "medium"); type unchanged (string)
  - `customer.authstatus`: duplicate keys present with conflicting values ("hot" and "cold") in `new.js` (see Inconsistencies)
- Search
  - Shape unchanged; example values updated (e.g., `promocode` now set to "ORLANDO10")
- Products (`product` array)
  - Prices are numbers
  - `flighttype` remains at root of product items
  - Room fields `numberofbeds`, `bedtype` remain at root; `attributes` may include `upsell`
  - Add-on products include `attributes` like `state`, `name`, `bounds`
- Payments
  - Shift from single flat field to multi-method array with numeric amounts and `currency`
- Loyalty/Points and Confirmation
  - New sections under `transaction`

---

### Inconsistencies to resolve in new.js
- `customer.authstatus` appears twice with conflicting values ("hot" and "cold"). Keep one. Recommended: emit a single key with the correct value for the page/session.
- Consider aligning product detail placement: either keep `flighttype`, `numberofbeds`, `bedtype` at root or move into `attributes` consistently across item types. Current schema mixes both styles.

---

### Net-new properties data dictionary

The following properties are present in `new.js` but not in `old.js`. Types use JSON schema conventions.

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

---

### Migration guidance (mappings)
- Payments
  - Replace `paymentinfo.formofpayment` with `transaction.payments.methods[].type` (push an entry per tender)
  - Replace `paymentinfo.paymentvisa` with the relevant `methods[].amount` (number) and set `transaction.payments.currency`
- Products
  - Keep array name `product`
  - Emit `productprice` as a number
  - Continue emitting `flighttype`, `numberofbeds`, `bedtype` at root per current `new.js`
  - Use `attributes` for upsell/switchsell and add-on descriptors

---

### Validation checklist
- Types
  - All monetary values are numbers and share the same `currency`
  - Boolean flags are emitted as true booleans (not strings)
- Structure
  - `product[].attributes` exists where applicable
  - `transaction.*` sections are present on confirmation pages
- Consistency
  - Only a single `customer.authstatus` is present with the correct value

---

### Examples (from new.js)
- Confirmation IDs: `[ { type: "spnr", value: "FSAGENA" }, { type: "bookingID", value: "1asdf1324fksdf23432d" } ]`
- Payment methods: `[ { type: "visa", amount: 1234 }, { type: "amex", amount: 15000 } ]`
- Add-on example: `{ producttype: "addon", attributes: { state: "Texas", name: "Travel Insurance" } }`
