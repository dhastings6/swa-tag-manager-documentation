## Adobe Data Layer: Old vs New (Schema Changes and Net-New Dictionary)

### Scope
- Source (old): `data layer documentation/old.js`
- Target (new): `data layer documentation/new.js`

---

### What Changed

- **Renamed**
  - `product[] (where producttype = "room").roomtype` → `product[].productcode`

- **Moved**
  - `paymentinfo` → `transactioninfo.payment`
  - `product[] (flight).flighttype` → `product[].attributes.flighttype`
  - `product[] (room).numberofbeds` → `product[].attributes.numberofbeds`
  - `product[] (room).bedtype` → `product[].attributes.bedtype`

- **Data type changes**
  - `transactioninfo.payment.formofpayment`: string → array of objects `{ type: string, amount: number }`
  - Monetary values that were strings in old (e.g., `product[].productprice`, `paymentinfo.paymentvisa`) are numbers in new where present (e.g., amounts within `transactioninfo.payment`, fields in `pricingsummary`, `product[].upsell`, add-on `productprice`).
  - `product[] (room).numberofbeds`: string → number (and moved under `attributes`).

- **Deleted (entirely removed from target)**
  - Root `paymentinfo` object
  - `product[].productprice` for non-add-on products (flight, hotel, car, room)
  - `product[] (room).roomtype` (replaced by `productcode`)
  - `product[] (flight).flighttype` at product root (moved under `attributes`)
  - `product[] (room).numberofbeds` and `product[] (room).bedtype` at product root (moved under `attributes`)

---

### What’s New (Additions)

- **Customer**
  - `customer.firstname`
  - `customer.lastname`
  - `customer.email`
  - `customer.phone`

- **Transaction info**
  - `transactioninfo` (container)
  - `transactioninfo.confirmationinfo.spnr`
  - `transactioninfo.confirmationinfo.flightconfirmation`
  - `transactioninfo.confirmationinfo.hotelconfirmation`
  - `transactioninfo.confirmationinfo.carconfirmation`
  - `transactioninfo.confirmationinfo.pointsearn`
  - `transactioninfo.confirmationinfo.averagepointsearn`
  - `transactioninfo.payment.numbercreditcards`
  - `transactioninfo.payment.paymentsplitevenly`

- **Pricing summary**
  - `pricingsummary` (container)
  - `pricingsummary.packagetotalamount`
  - `pricingsummary.packagesubtotal`
  - `pricingsummary.travelinsuranceplan`
  - `pricingsummary.earlybird`
  - `pricingsummary.supplierpromotion`
  - `pricingsummary.promotion`
  - `pricingsummary.airtaxes`
  - `pricingsummary.othertaxes`
  - `pricingsummary.totalpaidtoday`
  - `pricingsummary.postpayamounts[]` with `{ type, amount }`

- **Product changes**
  - `product[].upsell` (numeric upsell/upgrade value)
  - `product[].attributes` (container)
  - New product entries with `producttype = "addon"`, including:
    - `product[] (addon).productprice`
    - `product[] (addon).attributes.state`
    - `product[] (addon).attributes.name`
    - `product[] (addon).attributes.bounds` (for Early Bird example)

---

### Notes on Monetary Values
- New fields generally use numbers for currency. Examples: `transactioninfo.payment.*.amount`, `pricingsummary.*`, `product[].upsell`, add-on `productprice`.
- Keep a consistent convention (e.g., minor units/integer cents) if possible across implementations; the examples show both integers and decimals.

---

### Net-New Properties: Data Dictionary

| Property path | Type | Example | Description |
|---|---|---|---|
| `customer.firstname` | string | `"test"` | Member’s first name. |
| `customer.lastname` | string | `"tester"` | Member’s last name. |
| `customer.email` | string | `"test@tester.com"` | Member’s email address. |
| `customer.phone` | string | `"214-555-5555"` | Member’s phone number. |
| `transactioninfo` | object | `{ ... }` | Transaction container grouping confirmation and payment details. |
| `transactioninfo.confirmationinfo.spnr` | string | `"FSAGENA"` | Supplier/provider or record locator (SPNR). |
| `transactioninfo.confirmationinfo.flightconfirmation` | string | `"A5V3AT"` | Flight confirmation code. |
| `transactioninfo.confirmationinfo.hotelconfirmation` | string | `"A5V3AT-1005"` | Hotel confirmation code. |
| `transactioninfo.confirmationinfo.carconfirmation` | string | `"13783825U56"` | Car confirmation code. |
| `transactioninfo.confirmationinfo.pointsearn` | number (integer) | `2000` | Rapid Rewards points earned per member. |
| `transactioninfo.confirmationinfo.averagepointsearn` | number (integer) | `1000` | Average points earned per member. |
| `transactioninfo.payment.numbercreditcards` | number (integer) | `3` | Count of distinct credit cards used on the transaction. |
| `transactioninfo.payment.paymentsplitevenly` | string (`"0"`/`"1"`) | `"0"` | Indicates whether the payment was split evenly across cards. |
| `pricingsummary` | object | `{ ... }` | Pricing container for totals, taxes, and post-pay amounts. |
| `pricingsummary.packagetotalamount` | number | `214577` | Package total amount. |
| `pricingsummary.packagesubtotal` | number | `247588` | Package subtotal before selected adjustments. |
| `pricingsummary.travelinsuranceplan` | number | `5932` | Cost associated with the travel insurance plan. |
| `pricingsummary.earlybird` | number | `5000` | EarlyBird fees total. |
| `pricingsummary.supplierpromotion` | number | `1` | Supplier (hotel/car) promotion amount automatically applied. |
| `pricingsummary.promotion` | number | `10.00` | Promotional dollars applied by customer. |
| `pricingsummary.airtaxes` | number | `4213` | Total air taxes. |
| `pricingsummary.othertaxes` | number | `14512` | Total non-air taxes. |
| `pricingsummary.totalpaidtoday` | number | `189362` | Total collected at purchase time. |
| `pricingsummary.postpayamounts[]` | array<object> | `[{type:"hotel",amount:16875}, ...]` | Post-pay obligations by category. |
| `pricingsummary.postpayamounts[].type` | string | `"hotel"` | Post-pay category (`hotel`, `car`, `excursion`, `other`, `taxes`, `totalpostpaydue`). |
| `pricingsummary.postpayamounts[].amount` | number | `16875` | Amount due for the given post-pay category. |
| `product[].upsell` | number | `100` | Upsell/upgrade amount at the product level. |
| `product[].attributes` | object | `{ ... }` | Container for type-specific attributes (e.g., flighttype, room details). |
| `product[] (addon).productprice` | number | `75` | Price of the ancillary/add-on. |
| `product[] (addon).attributes.state` | string | `"Texas"` | Selected insurance state (for insurance add-on). |
| `product[] (addon).attributes.name` | string | `"Travel Insurance"` | Add-on name (e.g., Travel Insurance, Early Bird, Excursion). |
| `product[] (addon).attributes.bounds` | number (integer) | `2` | Number of bounds (Early Bird example). |

---

### Developer Guidance
- Prefer numbers for currency/amounts in new implementations; avoid stringified numbers.
- When adding new product types, place type-specific metadata under `product[].attributes`.
- For payments, report each tender in `transactioninfo.payment.formofpayment[]` as `{ type, amount }`.
- For rooms, use `productcode` for the room descriptor and move room details under `attributes`.

---

### Legacy → Target Quick Map (selected)
- `paymentinfo.formofpayment` → `transactioninfo.payment.formofpayment[]` (string → array of `{ type, amount }`)
- `paymentinfo.*` → `transactioninfo.payment.*`
- `product[].flight.flighttype` → `product[].attributes.flighttype`
- `product[].room.roomtype` → `product[].productcode`
- `product[].room.{numberofbeds, bedtype}` → `product[].attributes.{numberofbeds, bedtype}`
- `product[].productprice` (non-add-ons) → removed; use `upsell` if applicable 