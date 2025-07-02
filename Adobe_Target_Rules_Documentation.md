# Adobe Target Load Rules Documentation
## Southwest Airlines Vacation Platform - Personalization & Testing Implementation

### **Overview**
This document provides comprehensive documentation for all **Adobe Target related load rules** in the Southwest Airlines vacation platform Adobe Launch implementation. The Target implementation uses sophisticated user segmentation, parameter passing, and personalization delivery to enhance the customer experience.

---

## **🎯 Adobe Target Extension Configuration**

### **Extension Settings**
- **Extension**: Adobe Target v2
- **Client Code**: `southwestairlines`
- **IMS Org ID**: `65D316D751E563EC0A490D4C@AdobeOrg`
- **Server Domain**: `soptimize.southwest.com`
- **Timeout**: 3000ms
- **Analytics Logging**: Server-side (A4T enabled)
- **Decisioning Method**: Server-side
- **Page Load Enabled**: ✅ Yes
- **Views Enabled**: ✅ Yes
- **Body Hiding**: ✅ Enabled with opacity: 0

---

## **📋 Target Load Rules Inventory**

### **1. Target Library Load Page Top**
**Rule ID**: `RL0d018e3e98cb492babadebf729814612`

**Purpose**: Primary Target library initialization and global parameter setup

#### **Trigger Configuration**
- **Event**: Library Loaded
- **Rule Order**: 51
- **Delay Next**: ✅ True (1000ms timeout)

#### **Actions**
1. **Load Target Library**
   - **Module**: `adobe-target-v2/lib/loadTarget.js`
   - **Timeout**: 1000ms

2. **Global Parameters Setup** (Custom Code)
   - **Language**: JavaScript
   - **Timeout**: 2000ms
   - **Purpose**: Sets `window.targetPageParamsAll` function

#### **Target Parameters Passed**
```javascript
window.targetPageParamsAll = function() {
  return {
    'pagename': _satellite.getVar('page.pagename'),
    'responsivesize': _satellite.getVar('page.responsivesize'),
    'origin': _satellite.getVar('search.origin'),
    'destination': _satellite.getVar('search.destination'),
    'promocode': _satellite.getVar('search.promocode'),
    'departuredate': _satellite.getVar('search.departuredate'),
    'returndate': _satellite.getVar('search.returndate'),
    'searchtype': _satellite.getVar('search.searchtype'),
    'packagetype': _satellite.getVar('search.packagetype'),
    'bookingcurve': _satellite.getVar('search.bookingcurve'),
    'numberofadults': _satellite.getVar('search.numberofadults'),
    'numberofchildren': _satellite.getVar('search.numberofchildren'),
    'numberoflapchildren': _satellite.getVar('search.numberoflapchildren'),
    'numbernights': _satellite.getVar('search.numberofnights'),
    'numberhotelrooms': _satellite.getVar('search.numberhotelrooms'),
    'accountNumber': _satellite.getVar('customer.membernumber'),
    'membertierstatus': _satellite.getVar('customer.membertierstatus'),
    'memberpointbalance': _satellite.getVar('customer.memberpointbalance'),
    'sortorder': _satellite.getVar('results.sortorder'),
    'productcode': _satellite.getVar('product.productcode'),
    'productprice': _satellite.getVar('product.productprice'),
    'totalpassengercount': _satellite.getVar('total passenger count [cc]'),
    'propertyclass': _satellite.getVar('product.propertyclass'),
    'departuretime': _satellite.getVar('product.departuretime'),
    'carsize': _satellite.getVar('product.carsize'),
    'brand': _satellite.getVar('product.brand'),
    'formofpayment': _satellite.getVar('paymentinfo.formofpayment'),
    'datachannel': _satellite.getVar('datachannel'),
    'authstatus': _satellite.getVar('customer.authstatus')
  }
};
```

#### **Business Intelligence Value**
- **Customer Segmentation**: Member status, tier, points balance
- **Search Context**: Origin, destination, dates, passenger counts
- **Product Context**: Codes, prices, types, features
- **Booking Context**: Curve, payment methods, authentication status

---

### **2. Target Load Page Top Declared ID Hot**
**Rule ID**: `RL41e47085818e4547a4becf784c4b8eda`

**Purpose**: Customer ID declaration for authenticated users (hot traffic)

#### **Trigger Configuration**
- **Events**: 
  - Library Loaded (Rule Order: 6)
  - History Change (Rule Order: 40)

#### **Conditions**
1. **Authenticated User Check** (Custom Code)
   ```javascript
   var logged = localStorage.getItem("root.user-profile");
   if (logged && JSON.parse(logged) && JSON.parse(logged).value){
     return true;
   } else {
     return false;
   }
   ```

2. **SSO Exclusion** (Path and Query String)
   - **Pattern**: `.*sso\\?code=.*` (Regex)
   - **Negate**: ✅ True (Excludes SSO redirect pages)

#### **Actions**
- **Set Customer IDs** (Adobe MCID)
  - **Integration Code 1**: `SOuthW3st`
    - **Value**: `%customer rr number [session][cc]%`
    - **Auth State**: 1 (Authenticated)
  - **Integration Code 2**: `AdobeCampaignID`
    - **Value**: `%customer rr number [session][cc]%`
    - **Auth State**: 1 (Authenticated)

#### **Business Value**
- **Cross-Device Tracking**: Links authenticated sessions
- **Personalization**: Enables member-specific experiences
- **Campaign Integration**: Connects to Adobe Campaign

---

### **3. Target Load Page Top Declared ID Cold**
**Rule ID**: `RLbbd309bdb0954fa7b6837f464cbca6eb`

**Purpose**: Customer ID declaration for unauthenticated users (cold traffic)

#### **Trigger Configuration**
- **Events**: 
  - Library Loaded (Rule Order: 5)
  - History Change (Rule Order: 41)

#### **Conditions**
1. **Unauthenticated User Check** (Custom Code - Negated)
   ```javascript
   var logged = localStorage.getItem("root.user-profile");
   if (logged && JSON.parse(logged) && JSON.parse(logged).value){
     return true;
   } else {
     return false;
   }
   ```
   - **Negate**: ✅ True (Triggers for unauthenticated users)

#### **Actions**
- **Set Customer IDs** (Adobe MCID)
  - **Integration Code 1**: `SOuthW3st`
    - **Value**: `%customer rr number [session][cc]%`
    - **Auth State**: 2 (Logged Out)
  - **Integration Code 2**: `AdobeCampaignID`
    - **Value**: `%customer rr number [session][cc]%`
    - **Auth State**: 2 (Logged Out)

#### **Business Value**
- **Anonymous Tracking**: Manages non-authenticated visitor profiles
- **Progressive Profiling**: Builds behavioral profiles before authentication
- **Conversion Funnel**: Tracks anonymous to authenticated journey

---

### **4. Target Global LoggedInModal Declared ID Hot**
**Rule ID**: `RLf892b8a0588c4263941e96f46303184c`

**Purpose**: Special handling for authenticated users on SSO completion pages

#### **Trigger Configuration**
- **Event**: Pageload (Data Layer Push)
- **Rule Order**: 0 (Highest priority)

#### **Conditions**
1. **Authenticated User Check** (Custom Code)
   - Same logic as "Hot" rule above

2. **SSO Page Detection** (Path and Query String)
   - **Pattern**: `.*sso\\?code=.*` (Regex)
   - **Negate**: ❌ False (Includes SSO pages)

#### **Actions**
1. **Set Customer IDs** (Adobe MCID)
   - Same configuration as "Hot" rule

2. **Custom Code Execution**
   - **External Script**: Adobe DTM hosted JavaScript
   - **Purpose**: Likely modal-specific personalization logic

#### **Business Value**
- **SSO Experience**: Personalizes post-authentication experience
- **Modal Targeting**: Specific targeting for login modal interactions
- **Authentication Flow**: Handles SSO redirect scenarios

---

### **5. Pageload getOffers [TGM]**
**Rule ID**: `RL4569eff32691412fb3329416846d757a`

**Purpose**: Target Group Manager (TGM) offer retrieval for specific pages

#### **Trigger Configuration**
- **Event**: Pageload (Data Layer Push)
- **Rule Order**: 1 (Very high priority)

#### **Conditions** (Page Exclusions)
The rule fires on pages with content but excludes:
- **Has Content**: `.*[a-z].*` (Regex - any page with letters)
- **Excludes**:
  - Hotel Selection
  - Hotel Details  
  - Car Selection
  - Car Details
  - Select Departing Flight
  - Select Returning Flight

#### **Actions**
- **Custom Code Execution**
  - **External Script**: Adobe DTM hosted JavaScript
  - **Purpose**: Target Group Manager offer retrieval logic

#### **Business Value**
- **Page-Specific Targeting**: Excludes high-interaction pages
- **Performance Optimization**: Avoids conflicts with selection flows
- **TGM Integration**: Leverages Target Group Manager for offer delivery

---

### **6. Car Details getOffers**
**Rule ID**: `RLfee3ab21fbf94ca6a3d7008e1d465e83`

**Purpose**: Target offer retrieval specifically for car details page

#### **Trigger Configuration**
- **Event**: Pageload (Data Layer Push)
- **Rule Order**: 50

#### **Conditions**
- **Page Name**: Equals "car details" (case insensitive)

#### **Actions**
- **Custom Code Execution**
  - **External Script**: Adobe DTM hosted JavaScript
  - **Purpose**: Car details page specific targeting

#### **Business Value**
- **Product-Specific Targeting**: Car rental upselling and cross-selling
- **Conversion Optimization**: Targets high-intent car selection page
- **Revenue Enhancement**: Likely promotes car upgrades or add-ons

---

## **🔧 Technical Implementation Details**

### **Customer Identification Strategy**
The implementation uses a sophisticated 3-tier customer identification approach:

1. **Hot Traffic (Authenticated)**: Auth State 1
   - Full member profile available
   - Personalized experiences
   - Member-specific offers

2. **Cold Traffic (Unauthenticated)**: Auth State 2  
   - Anonymous behavioral tracking
   - Progressive profiling
   - Generic targeting

3. **SSO Transition**: Special handling
   - Post-authentication targeting
   - Modal-specific experiences
   - Seamless transition targeting

### **Parameter Architecture**
Target receives **25 key parameters** on every page load:

#### **Search Context** (11 parameters)
- Origin, destination, dates
- Passenger counts and types
- Package and search types
- Booking curve timing

#### **Customer Context** (4 parameters)
- Member number and tier status
- Points balance and auth status

#### **Product Context** (6 parameters)
- Product codes, prices, classes
- Departure times, car sizes, brands

#### **System Context** (4 parameters)
- Page name, responsive size
- Data channel, payment methods

### **Rule Execution Order**
```
Order 0:  Target Global LoggedInModal (SSO handling)
Order 1:  Pageload getOffers [TGM] (General targeting)
Order 5:  Target Load Cold (Unauthenticated ID)
Order 6:  Target Load Hot (Authenticated ID)
Order 40: History Change Hot (SPA navigation)
Order 41: History Change Cold (SPA navigation)
Order 50: Car Details getOffers (Product-specific)
Order 51: Target Library Load (Core initialization)
```

### **A4T Integration**
- **Analytics Logging**: Server-side
- **Automatic Tracking**: Target activities tracked in Adobe Analytics
- **Reporting**: Unified reporting across Target and Analytics

### **Performance Optimization**
- **Timeout**: 3000ms maximum
- **Body Hiding**: Prevents flicker during personalization
- **Async Loading**: Non-blocking implementation
- **Page Exclusions**: Avoids conflicts on selection pages

---

## **🚀 Business Applications**

### **Personalization Use Cases**
1. **Member-Specific Offers**: Tier-based promotions and upgrades
2. **Search-Based Targeting**: Origin/destination specific offers
3. **Booking Curve Optimization**: Time-sensitive offers
4. **Product Cross-Sell**: Car rentals on hotel pages
5. **Payment Method Targeting**: Payment-specific incentives

### **Testing Capabilities**
1. **A/B Testing**: Page layouts, messaging, offers
2. **Multivariate Testing**: Complex experience variations
3. **Audience Targeting**: Member tiers, behavioral segments
4. **Geographic Targeting**: Origin/destination based tests
5. **Temporal Targeting**: Booking curve and seasonal tests

### **Customer Journey Optimization**
1. **Anonymous to Authenticated**: Progressive experience enhancement
2. **Search to Selection**: Funnel optimization testing
3. **Cross-Product Promotion**: Package upselling tests
4. **Member Retention**: Loyalty program optimization
5. **Conversion Rate Optimization**: Checkout flow improvements

---

## **📊 Analytics & Reporting Integration**

### **Adobe Analytics Integration (A4T)**
- **Activity Reporting**: Target activities appear in Analytics
- **Success Metrics**: Conversion tracking with business KPIs
- **Audience Insights**: Target audiences in Analytics segments
- **Pathing Analysis**: Journey analysis with Target experiences

### **Customer Profile Enhancement**
- **Behavioral Targeting**: Page views, search patterns, selections
- **Transactional Targeting**: Booking history, payment preferences
- **Demographic Targeting**: Member tier, points balance, geography
- **Temporal Targeting**: Booking curve, seasonal patterns

---

*This documentation represents the complete Adobe Target implementation for Southwest Airlines' vacation platform, enabling sophisticated personalization and testing capabilities across the entire customer journey.* 