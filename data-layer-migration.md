## Data Layer Migration Guide: old.js → new.js

This guide summarizes the structural and schema changes between the current data layer (`old.js`) and the proposed target schema (`new.js`). It highlights what changed, what moved, what was deleted, and introduces a data dictionary for all net-new properties.

### High-level summary
- The top-level `product` array is renamed and restructured to `products`, introducing an `attributes` object per product.
- Payment info moved from top-level `paymentinfo` to `transaction.payments` with multi-method support and numeric amounts.
- Customer membership fields moved into a nested `customer.member` object; new `customer.profile` object added.
- A new `transaction` object captures confirmation identifiers, loyalty points, payments, and pricing summary.
- Several fields switch from string to number and new boolean fields are introduced.

---

### Changes by category

#### Renamed (often also moved)
- `product` → `products` (array name)
- `customer.membernumber` → `customer.member.number`
- `customer.membertierstatus` → `customer.member.tier`
- `customer.memberpointbalance` → `customer.member.pointBalance` (camelCase)
- For room products: `roomtype` value is now carried in `products[].productcode`

#### Moved
- Customer membership fields moved under `customer.member`:
  - `customer.membernumber` → `customer.member.number`
  - `customer.membertierstatus` → `customer.member.tier`
  - `customer.memberpointbalance` → `customer.member.pointBalance`
- Payment information moved and expanded:
  - `paymentinfo.*` → `transaction.payments.*` (multi-method, currency, split flags, amounts numeric)
- Product-specific details moved under `products[].attributes`:
  - Flight: `flighttype` → `products[].attributes.flighttype`
  - Room: `numberofbeds`, `bedtype` → `products[].attributes.beds[]` (typed bed list)

#### Data type changes
- `products[].productprice`: string (e.g., "1168.65") → number (e.g., 0)
- `customer.memberpointbalance` (string) → `customer.member.pointBalance` (number)
- Payments: amounts are numbers; new boolean `transaction.payments.splitevenly`
- Pricing summary values are numbers (`packagetotal`, taxes, etc.)

#### Deleted (replaced or removed)
- Top-level `paymentinfo` (replaced by `transaction.payments`)
  - `paymentinfo.formofpayment` (replaced by `transaction.payments.methods[].type`)
  - `paymentinfo.paymentvisa` (replaced by `transaction.payments.methods[].amount`)
- Product item fields (moved into `attributes`):
  - `product[].flighttype`
  - `product[].roomtype`
  - `product[].numberofbeds`
  - `product[].bedtype`
- Customer membership flat fields (moved into `customer.member`):
  - `customer.membernumber`
  - `customer.membertierstatus`
  - `customer.memberpointbalance`

---

### Structural additions
- `transaction` object with:
  - `transaction.confirmation[]` for identifiers (SPNR, flight, hotel, car, bookingID)
  - `transaction.points` for loyalty accrual/burn
  - `transaction.payments` for multi-method payments, currency, split flag
  - `transaction.pricingsummary` for totals, taxes, postpay breakdown
- `customer.profile` object for PII (first/last name, email, phone)
- `products[].attributes` for per-product metadata (flighttype, upsell, switchsell, beds, etc.)
- New product type values introduced: `addon`

---

### Field-by-field comparison highlights

- Page and session
  - `page.responsivesize`: value changed (e.g., "small" → "medium") but remains a string
  - `customer.authstatus`: value changed (e.g., "cold" → "hot") but remains a string
- Search
  - Same shape; values updated (e.g., `destination` SAN → MCO), types unchanged (strings)
- Products
  - `product` → `products` and item schema standardized
  - Prices now numeric; ancillary details live in `attributes`
  - Room details represented as a structured `beds[]` array
- Payments
  - From single-method, flat fields to multi-method array with numeric amounts
- Loyalty/Points and Confirmation
  - New explicit sections under `transaction`

---

### Net-new properties data dictionary

The following properties exist in `new.js` but not in `old.js`. Types use JSON schema conventions.

| Property | Type | Description | Example |
|---|---|---|---|
| customer.member | object | Grouping for loyalty membership details. | { number: "6008184", tier: "a-list", pointBalance: 23255 } |
| customer.member.number | string | Member/account identifier. | "6008184" |
| customer.member.tier | string | Loyalty tier/status. | "a-list" |
| customer.member.pointBalance | number | Current points balance. | 23255 |
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
| products | array<object> | Unified products collection replacing `product`. | [...] |
| products[].attributes | object | Per-product metadata. | { flighttype, upsell } |
| products[].attributes.flighttype | string | Flight direction/type. | "outbound" |
| products[].attributes.upsell | number | Upsell amount/value (unit TBD). | 100 |
| products[].attributes.switchsell | boolean | Car switch-sell flag. | true |
| products[].attributes.beds | array<object> | Room bedding configuration. | [{ type: "queen", count: 2 }] |
| products[].attributes.beds[].type | string | Bed type name. | "king" |
| products[].attributes.beds[].count | number | Count of beds of given type. | 1 |
| products[].attributes.state | string | Add-on state/region when applicable. | "Texas" |
| products[].attributes.name | string | Human-readable add-on name. | "Travel Insurance" |
| products[].attributes.bounds | number | Add-on bounds or quantity (context-specific). | 2 |

Notes:
- Product `productprice` is now numeric (not listed above because the property existed previously, but its type changed).
- A new product type value `addon` is supported alongside `flight`, `hotel`, `car`, `room`.

---

### Migration guidance (mappings)
- Customer membership
  - Map `customer.membernumber` → `customer.member.number`
  - Map `customer.membertierstatus` → `customer.member.tier`
  - Map `customer.memberpointbalance` (string) → `customer.member.pointBalance` (number)
- Payments
  - Replace `paymentinfo.formofpayment` with appending an entry to `transaction.payments.methods[]` using the specific `type`
  - Replace `paymentinfo.paymentvisa` with the corresponding `methods[].amount` (number) and set `transaction.payments.currency`
- Products
  - Rename `product` → `products`
  - Move per-item details:
    - `flighttype` → `attributes.flighttype`
    - `roomtype`, `numberofbeds`, `bedtype` → `attributes.beds[]` and carry room name in `productcode`
  - Ensure `productprice` is emitted as a number

---

### Validation checklist
- Types
  - All monetary values are numbers and share the same `currency`
  - Boolean flags are emitted as true booleans (not strings)
- Structure
  - `products[].attributes` exists where applicable
  - `transaction.*` sections are present on confirmation pages
- Privacy
  - `customer.profile.*` contains only necessary PII and complies with privacy policies

---

### Examples (from new.js)
- Confirmation IDs: `[ { type: "spnr", value: "FSAGENA" }, { type: "bookingID", value: "1asdf1324fksdf23432d" } ]`
- Payment methods: `[ { type: "visa", amount: 1234 }, { type: "amex", amount: 15000 } ]`
- Room beds: `{ attributes: { beds: [ { type: "queen", count: 2 }, { type: "king", count: 1 } ] } }`
