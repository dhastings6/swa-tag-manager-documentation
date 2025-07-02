# getDataDash() Function Documentation
## Hotel Impression Analytics & Merchandising Variable Tracking

### **Overview**
The `getDataDash()` function is the analytics component of a sophisticated viewport-based impression tracking system. It reads data attributes previously set by `setDataDash()`, maps them to Adobe Analytics merchandising variables, and sends impression tracking beacons when hotel packages become viewable to users.

---

## **Complete Impression Tracking Workflow**

### **Two-Function System:**
1. **`setDataDash()`** - Data extraction and DOM attribute setting (Rule order: 10)
2. **`getDataDash()`** - Analytics variable mapping and beacon firing (Rule order: 50)

### **Sequential Process:**
```javascript
1. User scrolls → Hotel package enters viewport
2. setDataDash() fires → Extracts React props → Sets data attributes
3. getDataDash() fires → Reads data attributes → Maps to eVars → Sends beacon
4. Adobe Analytics receives impression event with rich merchandising data
```

---

## **Adobe Launch Rule Configuration**

### **Rule Details:**
- **Name**: `[50] getDataDash() [viewport change tracking]`
- **Order**: 50 (Fires after setDataDash() order 10)
- **Type**: Impression tracking and analytics beacon

### **Event Configuration:**
```javascript
Event Type: "Enters Viewport"
CSS Selector: ".package-info-container"
Frequency: "first time element enters viewport"
// Note: Different from setDataDash which fires "every time"
```

### **Conditions:**
```javascript
1. Page Name Condition:
   - page.pagename === "hotel selection"
   
2. Custom Code Condition:
   - sessionStorage.getItem('data-dash') != 'off'
   // Same condition as setDataDash for consistency
```

### **Actions Sequence:**
```javascript
1. Adobe Analytics - Clear Variables (WAIT, THEN)
2. s.getDataDash() - Custom Code (WAIT, THEN)  
3. s.tl(o, "viewable impression") - Send Beacon (WAIT, THEN)
4. Adobe Analytics - Clear Variables
```

---

## **Technical Implementation**

### **Data Attribute Reading**
```javascript
// Reads attributes set by setDataDash()
var displayedPrice = event.element.dataset.displayprice,
    hotelCategory = event.element.dataset.hotelcategory,
    propertyChainCode = event.element.dataset.chaincode,
    propertyEarnAverage = event.element.dataset.avgearn,
    productEarn = event.element.dataset.productearn,
    propertyId = event.element.dataset.hotelcode,
    propertyName = event.element.dataset.hotelname,
    propertyOrder = event.element.dataset.order,
    propertyRating = event.element.dataset.hotelrating,
    propertyThumbnail = event.element.dataset.propertythumbnail,
    propertyLocationCode = event.element.dataset.locationcode,
    propertyPromoCodes = event.element.dataset?.promocodes || undefined
```

### **Adobe Analytics Variable Mapping**
```javascript
eVarObject = {
  "eVar123": propertyName,           // Hotel name
  "eVar128": productEarn,            // Product-specific earning points
  "eVar130": propertyRating,         // Star rating (1-5)
  "eVar132": propertyThumbnail,      // Image category
  "eVar133": propertyOrder,          // Position in search results
  "eVar134": propertyEarnAverage,    // Average earning points
  "eVar137": hotelCategory,          // Hotel type/category
  "eVar138": propertyChainCode,      // Hotel chain identifier
  "eVar139": displayedPrice,         // Package price
  "eVar143": propertyLocationCode,   // Destination code
  "eVar154": propertyPromoCodes      // Applied promo codes
}
```

### **Product String Generation**
```javascript
// Creates merchandising-enabled product string
s.products = `hotel;${propertyId};;;event73=1;` + eVarArray.join("|");

// Example output:
// "hotel;GBR123;;;event73=1;eVar123=Grand Beach Resort|eVar128=750|eVar130=4|..."
```

---

## **Adobe Analytics Configuration**

### **Events Fired:**
- **event73**: Hotel impression event (incremental counter)

### **Product String Structure:**
```javascript
Format: "category;productID;quantity;revenue;events;merchandising_eVars"
Example: "hotel;GBR123;;;event73=1;eVar123=Grand Beach Resort|eVar130=4|eVar139=1299.99"

Components:
- category: "hotel"
- productID: Hotel code (e.g., "GBR123")  
- quantity: Empty (not applicable for impressions)
- revenue: Empty (impression, not purchase)
- events: "event73=1" (impression counter)
- merchandising_eVars: Pipe-separated eVar assignments
```

### **Link Tracking Variables:**
```javascript
linkTrackVars = [
  "contextData.ccError",           // Error tracking
  "contextData.load_rule_name",    // Rule identification
  "eVar120", "eVar121", "eVar129", // Additional context variables
  "eVar14", "eVar140", "eVar141",  // Search context
  "eVar154", "eVar16", "eVar162",  // Merchandising variables
  "eVar19", "eVar20", "eVar237",   // User context
  "eVar238", "eVar4", "eVar46",    // Page context
  "eVar47", "eVar55", "eVar88",    // Additional attributes
  "eVar90", "eVar91", "eVar93",    // Extended merchandising
  "eVar94", "eVar95",              // Performance metrics
  "event73",                       // Impression event
  "pageName", "products",          // Core tracking
  "prop25", "prop55"               // Traffic variables
]
```

### **Beacon Type:**
```javascript
s.tl(o, "viewable impression")
// Link tracking call (not page view)
// Custom link type: "viewable impression"
```

---

## **Integration with setDataDash()**

### **Data Flow Architecture:**
```javascript
setDataDash() [Order 10] → DOM Attributes → getDataDash() [Order 50] → Analytics

Step 1: setDataDash() extracts React props
  ↓
Step 2: Sets data-* attributes on DOM elements  
  ↓
Step 3: getDataDash() reads those attributes
  ↓
Step 4: Maps to Adobe Analytics variables
  ↓
Step 5: Sends impression tracking beacon
```

### **Attribute Mapping:**
| setDataDash() Output | getDataDash() Input | Adobe Analytics Variable |
|---------------------|-------------------|-------------------------|
| `data-hotelname` | `dataset.hotelname` | `eVar123` |
| `data-productearn` | `dataset.productearn` | `eVar128` |
| `data-hotelrating` | `dataset.hotelrating` | `eVar130` |
| `data-propertythumbnail` | `dataset.propertythumbnail` | `eVar132` |
| `data-order` | `dataset.order` | `eVar133` |
| `data-avgearn` | `dataset.avgearn` | `eVar134` |
| `data-hotelcategory` | `dataset.hotelcategory` | `eVar137` |
| `data-chaincode` | `dataset.chaincode` | `eVar138` |
| `data-displayprice` | `dataset.displayprice` | `eVar139` |
| `data-locationcode` | `dataset.locationcode` | `eVar143` |
| `data-promocodes` | `dataset.promocodes` | `eVar154` |

---

## **Error Handling & Monitoring**

### **Error Tracking:**
```javascript
try {
  // Main function logic
} catch(e) {
  var ruleName = event.$rule.name,
      JSerror = error.message;
  window.failedCode.push(`${ruleName} : ${JSerror}`)
}
```

### **Context Data for Debugging:**
```javascript
s.contextData["load_rule_name"] = event.$rule.name;
// Helps identify which rule fired the beacon
```

### **Data Quality Safeguards:**
- Uses optional chaining for promo codes: `dataset?.promocodes || undefined`
- Error isolation prevents beacon failure
- Link tracking variables ensure data completeness

---

## **Business Intelligence & Reporting**

### **Analytics Capabilities Enabled:**

#### **Hotel Performance Analysis:**
- **Impression tracking**: Which hotels are viewed most frequently
- **Position analysis**: Impact of search result position on viewability
- **Price correlation**: Relationship between price and impression rates

#### **User Behavior Insights:**
- **Scroll depth**: How far users scroll through results
- **Category preferences**: Which hotel types get most impressions
- **Promotional effectiveness**: Impact of promo codes on viewability

#### **Revenue Optimization:**
- **Merchandising effectiveness**: Which attributes drive engagement
- **Pricing analysis**: Optimal price positioning
- **Inventory prioritization**: Which hotels to feature prominently

### **Reporting Dimensions Available:**
```javascript
- Hotel Name (eVar123)
- Hotel Category (eVar137) 
- Star Rating (eVar130)
- Chain Code (eVar138)
- Location Code (eVar143)
- Price Point (eVar139)
- Search Position (eVar133)
- Promo Codes (eVar154)
```

---

## **Performance Considerations**

### **Optimization Features:**
- **Single beacon per impression**: Prevents duplicate tracking
- **Efficient data reading**: Direct dataset access
- **Minimal processing**: Simple attribute-to-variable mapping
- **Link tracking**: Lighter than page view calls

### **Viewport Timing:**
- **First time only**: Prevents multiple impressions per scroll
- **Immediate firing**: No delay for accurate impression timing
- **Clear variables**: Prevents data contamination between beacons

---

## **Usage Examples**

### **Typical Analytics Hit:**
```javascript
// When user scrolls to see "Grand Beach Resort"
Event: event73=1
Products: "hotel;GBR123;;;event73=1;eVar123=Grand Beach Resort|eVar130=4|eVar139=1299.99|eVar137=Resort|Beach|eVar133=3"
Context: load_rule_name="[50] getDataDash() [viewport change tracking]"
Link Type: "viewable impression"
```

### **Data Studio/Workspace Queries:**
```javascript
// Top viewed hotels
Metric: event73 (Hotel Impressions)
Dimension: eVar123 (Hotel Name)

// Price vs Impression analysis  
Metric: event73 (Hotel Impressions)
Dimension: eVar139 (Display Price)

// Position effectiveness
Metric: event73 (Hotel Impressions) 
Dimension: eVar133 (Property Order)
```

---

## **Troubleshooting Guide**

### **Common Issues:**

#### **1. No Analytics Beacons Firing**
- **Check**: setDataDash() ran first (data attributes present)
- **Check**: Rule conditions met (page name, data-dash setting)
- **Check**: Event sequence in Network tab

#### **2. Missing Merchandising Variables**
- **Cause**: Data attributes not set by setDataDash()
- **Debug**: `console.log(event.element.dataset)`
- **Fix**: Verify setDataDash() execution order

#### **3. Duplicate Impressions**
- **Cause**: Event firing frequency set to "every time" 
- **Fix**: Ensure "first time element enters viewport"

### **Debugging Commands:**
```javascript
// Check if data attributes exist
console.log(event.element.dataset)

// Verify analytics variables
console.log(s.products)
console.log(s.events)

// Check error tracking
console.log(window.failedCode)

// Manual function execution
getDataDash()
```

---

## **Advanced Implementation Notes**

### **Extensibility:**
- Easy to add new merchandising variables
- Modular eVar mapping object
- Configurable link tracking variables

### **A/B Testing Integration:**
- Can include test variation in context data
- Product strings enable segment analysis
- Impression data supports test measurement

### **Future Enhancements:**
- Real-time pricing updates
- Dynamic promo code application
- Enhanced error reporting
- Performance timing metrics

This implementation represents a sophisticated, production-ready impression tracking system that provides rich analytics data for hotel search optimization and business intelligence in the travel industry. 