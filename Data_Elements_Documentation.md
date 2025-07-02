# Adobe Launch Data Elements Documentation
## Southwest Airlines Vacation Platform - Complete Data Element Reference

### **Overview**
This document provides comprehensive documentation for all **88 data elements** configured in the Southwest Airlines vacation platform Adobe Launch implementation. These data elements capture critical business data across the customer journey from search to booking completion.

---

## **📊 Data Element Categories**

### **🔍 Search & Query Parameters**
These data elements capture search criteria and user intent data.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `search.origin` | Departure airport/city | `search.origin` | Data Layer |
| `search.destination` | Destination airport/city | `search.destination` | Data Layer |
| `search.departuredate` | Outbound travel date | `search.departuredate` | Data Layer |
| `search.returndate` | Return travel date | `search.returndate` | Data Layer |
| `search.numberofadults` | Number of adult passengers | `search.numberofadults` | Data Layer |
| `search.numberofchildren` | Number of child passengers | `search.numberofchildren` | Data Layer |
| `search.numberoflapchildren` | Number of lap children | `search.numberoflapchildren` | Data Layer |
| `search.numberhotelrooms` | Number of hotel rooms requested | `search.numberhotelrooms` | Data Layer |
| `search.numberofnights` | Length of stay in nights | `search.numbernights` | Data Layer |
| `search.packagetype` | Package type (flight+hotel, etc.) | `search.packagetype` | Data Layer |
| `search.searchtype` | Type of search performed | `search.searchtype` | Data Layer |
| `search.bookingcurve` | Days until departure | `search.bookingcurve` | Data Layer |
| `search.promocode` | Promotional code applied | `search.promocode` | Data Layer |
| `search.results` | Search results data object | `search.results` | Data Layer |
| `search.resultsfilterapplied` | Whether filters were applied | `results.filterapplied` | Data Layer |
| `search.resultsfilters` | Applied filters data | `results.filters` | Data Layer |

### **🛍️ Product Information**
Data elements tracking product details, pricing, and attributes.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `product.producttype` | Type of product (hotel, flight, car) | `product.producttype` | Data Layer |
| `product.productcode` | Unique product identifier | `product.productcode` | Data Layer |
| `product.productprice` | Product price | `product.productprice` | Data Layer |
| `product.productposition` | Position in search results | `product.productposition` | Data Layer |
| `product.propertyclass` | Hotel property class/rating | `product.propertyclass` | Data Layer |
| `product.brand` | Hotel/car brand | `product.brand` | Data Layer |
| `product.amenities` | Available amenities | `product.amenities` | Data Layer |
| `product.tripadvisorrating` | TripAdvisor rating | `product.tripadvisorrating` | Data Layer |
| `product.featuredproduct` | Featured product flag | `product.featuredproduct` | Data Layer |
| `product.featuredproperty` | Featured property flag | `product.featuredproperty` | Data Layer |
| `product.urgencymessage` | Urgency messaging displayed | `product.urgencymessage` | Data Layer |
| `product.numberstops` | Number of flight stops | `product.numberstops` | Data Layer |
| `product.flighttype` | Flight type (nonstop, connecting) | `product.flighttype` | Data Layer |
| `product.flightnumber` | Flight number | `product.flightnumber` | Data Layer |
| `product.departuretime` | Flight departure time | `product.departuretime` | Data Layer |
| `product.lengthofflight` | Flight duration | `product.lengthofflight` | Data Layer |
| `product.stoptype` | Type of stops (if any) | `product.stoptype` | Data Layer |
| `product.carsize` | Car rental size category | `product.carsize` | Data Layer |
| `product.carfeatures` | Car rental features | `product.carfeatures` | Data Layer |
| `product.numberroomsdisplayed` | Rooms shown to user | `product.numberroomsdisplayed` | Data Layer |
| `product.priceupgrade` | Price upgrade information | `product.priceupgrade` | Data Layer |

### **🎯 Promotional & Marketing**
Data elements for promotional campaigns and marketing attribution.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `product.promoapplied` | Promo code application status | `product.promoapplied` | Data Layer |
| `product.promoapplication` | Promo application details | `product.promoapplication` | Data Layer |
| `product.promotype` | Type of promotion | `product.promotype` | Data Layer |
| `product.supplierpromo` | Supplier promotional offers | `product.supplierpromo` | Data Layer |
| `product.supplierpromomsg` | Supplier promo messaging | `product.supplierpromomsg` | Data Layer |
| `promocode [qp]` | Promo code from URL parameter | Query Parameter: `promocode` | Query String |
| `campaignID` | Campaign identifier | `campaignID` | Data Layer |
| `clk [qs]` | Click tracking parameter | Query Parameter: `clk` | Query String |

### **👤 Customer Information**
Data elements capturing customer profile and status data.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `customer.authstatus` | Authentication status | `customer.authstatus` | Data Layer |
| `customer.membernumber` | Rapid Rewards member number | `customer.membernumber` | Data Layer |
| `customer.membertierstatus` | Tier status (A-List, etc.) | `customer.membertierstatus` | Data Layer |
| `customer.memberpointbalance` | Points balance | `customer.memberpointbalance` | Data Layer |
| `customer.chase acqsrccode` | Chase acquisition source | `customer.chase_acqsrccode` | Data Layer |
| `customer.chase highvalueindicator` | High value customer flag | `customer.chase_highvalueindicator` | Data Layer |
| `customer.chase offeridentifier` | Chase offer ID | `customer.chase_offeridentifier` | Data Layer |
| `customer rr number [session][cc]` | RR number from session storage | Custom function reading localStorage | Custom Code |

### **💳 Payment Information**
Data elements tracking payment methods and transaction details.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `paymentinfo.formofpayment` | Primary payment method | `paymentinfo.formofpayment` | Data Layer |
| `paymentinfo.numbercreditcards` | Number of cards used | `paymentinfo.numbercreditcards` | Data Layer |
| `paymentinfo.paymentsplitevenly` | Split payment flag | `paymentinfo.paymentsplitevenly` | Data Layer |
| `paymentinfo.paymentvisa` | Visa card usage | `paymentinfo.paymentvisa` | Data Layer |
| `paymentinfo.paymentmastercard` | MasterCard usage | `paymentinfo.paymentmastercard` | Data Layer |
| `paymentinfo.paymentamex` | American Express usage | `paymentinfo.paymentamex` | Data Layer |
| `paymentinfo.paymentapplepay` | Apple Pay usage | `paymentinfo.paymentapplepay` | Data Layer |
| `paymentinfo.paymentpaypal` | PayPal usage | `paymentinfo.paymentpaypal` | Data Layer |
| `paymentinfo.paymentuplift` | Uplift financing usage | `paymentinfo.paymentuplift` | Data Layer |
| `paymentinfo.paymenttravelfund` | Travel fund usage | `paymentinfo.paymenttravelfund` | Data Layer |

### **📄 Page & Content Information**
Data elements capturing page context and content details.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `page.pagename` | Page name identifier | `page.pagename` | Data Layer |
| `page.pageID` | Unique page identifier | `page.pageID` | Data Layer |
| `page.sitesection` | Site section | `page.sitesection` | Data Layer |
| `page.section` | Page section | `page.section` | Data Layer |
| `page.pagedescription` | Page description | `page.pagedescription` | Data Layer |
| `page.eventdescription` | Event description | `page.eventdescription` | Data Layer |
| `page.responsivesize` | Responsive breakpoint | `page.responsivesize` | Data Layer |
| `page.pageresponsiveness` | Page responsiveness data | `page.responsiveness` | Data Layer |
| `content.language` | Content language | `content.language` | Data Layer |
| `content.contentid` | Content identifier | `content.contentid` | Data Layer |
| `url` | Full page URL | Page Info | Core Extension |
| `hostname` | Domain hostname | Page Info | Core Extension |
| `pathname` | URL pathname | Page Info | Core Extension |

### **📊 Search Results & Filtering**
Data elements for search results and filter interactions.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `results.page` | Current results page | `results.page` | Data Layer |
| `results.numberofpages` | Total pages available | `results.numberofpages` | Data Layer |
| `results.searchresultcount` | Number of results found | `results.searchresultcount` | Data Layer |
| `results.passengercount` | Passenger count in results | `results.passengercount` | Data Layer |
| `results.triptype` | Trip type (roundtrip, oneway) | `results.triptype` | Data Layer |
| `results.faretype` | Fare type in results | `results.faretype` | Data Layer |
| `results.sortorder` | Current sort order | `results.sortorder` | Data Layer |
| `results.flightsunavailable` | Flight unavailability flag | `results.flightsunavailable` | Data Layer |
| `results.filterapplied` | Filter application status | `results.filterapplied` | Data Layer |
| `results.filters` | Applied filters object | `results.filters` | Data Layer |
| `results.filters.filtername` | Filter name | `[0].results.filters[0].filtername` | Data Layer |
| `results.filters.filtervalue` | Filter value | `results.filters.filtervalue` | Data Layer |
| `results.filters.filtervalue.min` | Minimum filter value | `results.filters.filtervalue.min` | Data Layer |
| `results.filters.filtervalue.max` | Maximum filter value | `results.filters.filtervalue.max` | Data Layer |
| `search.results.filter.name` | First filter name | `results.filters[0].filtername` | Data Layer |
| `search.resultsfiltersfiltervalue` | First filter value | `results.filters[0].filtervalue` | Data Layer |

### **👁️ Impression Tracking**
Data elements for viewport-based impression tracking (works with setDataDash/getDataDash).

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `impressions` | Complete impressions object | `impressions` | Data Layer |
| `impressions.impressiontype` | Type of impression | `impressions.impressiontype` | Data Layer |
| `impressions.productcode` | Product code for impression | `impressions.productcode` | Data Layer |
| `impressions.productorigin` | Product origin for impression | `impressions.productorigin` | Data Layer |
| `impressions.productdestination` | Product destination | `impressions.productdestination` | Data Layer |
| `impressions.productposition` | Position in search results | `impressions.productposition` | Data Layer |
| `impressions.productprice` | Product price for impression | `impressions.productprice` | Data Layer |
| `impressions.producttype` | Product type for impression | `impressions.producttype` | Data Layer |
| `impressions.productcarousel` | Carousel impression flag | `impressions.productcarousel` | Data Layer |

### **📋 Booking & Transaction**
Data elements for booking confirmation and transaction data.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `booking` | Complete booking object | `booking` | Data Layer |
| `booking.productpnr` | Product PNR | `booking.productpnr` | Data Layer |
| `booking.superpnr` | Super PNR | `booking.superpnr` | Data Layer |
| `pnr` | Primary PNR | `pnr` | Data Layer |
| `hotelselectionproductstring` | Hotel selection product string | `hotelselectionproductstring` | Data Layer |

### **🔧 Technical & System Information**
Technical data elements for device, browser, and system tracking.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `device.devicetype` | Device type | `device.devicetype` | Data Layer |
| `datachannel` | Data channel | `datachannel` | Data Layer |
| `currency` | Transaction currency | `currency` | Data Layer |
| `subchannel` | Traffic subchannel | `adobeDataLayer.subchannel` | JavaScript Variable |
| `paxtype` | Passenger type | `paxtype` | Data Layer |
| `daystonexttrip` | Days to next trip | `daystonexttrip` | Data Layer |
| `traveladvisory` | Travel advisory displayed | `travel_advisory_displayed` | Data Layer |
| `ecid` | Experience Cloud ID | ECID service | Adobe MCID |

### **⚠️ Error & Event Tracking**
Data elements for error handling and event management.

| **Data Element Name** | **Purpose** | **Data Path** | **Type** |
|----------------------|-------------|---------------|----------|
| `errordetails.errormessage` | Error message content | `errordetails.errormessage` | Data Layer |
| `event` | Current event | `event` | Data Layer |
| `eventinfo` | Event information object | `eventinfo` | Data Layer |
| `eventinfo.eventtype` | Type of event | `eventinfo.eventtype` | Data Layer |
| `eventinfo.adobeevent` | Adobe-specific event | `eventinfo.adobeevent` | Data Layer |
| `productattribute` | Product attribute data | `productattribute` | Data Layer |

### **🧮 Custom Calculated Data Elements**
Advanced data elements using custom JavaScript functions.

| **Data Element Name** | **Purpose** | **Logic** | **Storage** |
|----------------------|-------------|-----------|-------------|
| `total passenger count [cc]` | Total passengers calculation | `adults + children + lap children` | None |
| `global: launch library (CC)` | Launch build information | `property + environment + build date` | None |
| `display details [cc]` | Device display details | `responsive size + webview + device type` | None |
| `uniqueDestinationSearch` | New destination search flag | Session storage destination tracking | Session |
| `combined_event_info` | Combined event information | Page load + impression events | Session |
| `customer rr number [session][cc]` | Customer RR number from storage | Reads from localStorage user profile | Session |

---

## **🔧 Data Element Configuration Types**

### **Data Layer Computed State**
- **Extension**: `gcoe-adobe-client-data-layer`
- **Module**: `datalayerComputedState.js`
- **Purpose**: Reads from centralized data layer state
- **Usage**: Most business data elements (82 total)

### **Custom Code**
- **Extension**: Core
- **Module**: `customCode.js`
- **Purpose**: Complex calculations and data processing
- **Usage**: Advanced business logic (7 total)

### **Page Info**
- **Extension**: Core
- **Module**: `pageInfo.js`
- **Purpose**: Browser page information
- **Usage**: URL, hostname, pathname (3 total)

### **Query String Parameter**
- **Extension**: Core
- **Module**: `queryStringParameter.js`
- **Purpose**: URL parameter extraction
- **Usage**: Campaign tracking (2 total)

### **JavaScript Variable**
- **Extension**: Core
- **Module**: `javascriptVariable.js`
- **Purpose**: Global JavaScript variable access
- **Usage**: Legacy data layer access (1 total)

### **Adobe Experience Cloud ID**
- **Extension**: Adobe MCID
- **Module**: `ecid.js`
- **Purpose**: Visitor identification
- **Usage**: Cross-solution visitor tracking (1 total)

---

## **📈 Business Intelligence Value**

### **Customer Journey Tracking**
- Complete search-to-booking funnel visibility
- Multi-product package tracking
- Cross-session attribution

### **Product Performance**
- Impression-based product analytics
- Position and pricing optimization
- Supplier and brand performance

### **Personalization Data**
- Customer tier and status tracking
- Chase partnership integration
- Historical behavior patterns

### **Revenue Analytics**
- Payment method analysis
- Promotional code effectiveness
- Booking conversion optimization

---

## **🚀 Advanced Implementation Notes**

### **Session Storage Integration**
Several data elements leverage session storage for:
- Destination search uniqueness tracking
- Impression event combination
- Customer authentication persistence

### **Data Layer Architecture**
The implementation uses a sophisticated computed state pattern that:
- Centralizes all business data
- Provides consistent data access
- Enables real-time data layer updates

### **Custom Business Logic**
Advanced calculations include:
- Dynamic passenger counting
- Destination search uniqueness
- Multi-event information combination
- Launch library versioning

### **Performance Considerations**
- Default values prevent data collection failures
- Error handling in custom code functions
- Efficient data layer state management
- Session storage for temporary data persistence

---

*This documentation represents the complete data element configuration for Southwest Airlines' vacation platform Adobe Launch implementation. Each data element plays a critical role in the comprehensive analytics and personalization strategy.* 