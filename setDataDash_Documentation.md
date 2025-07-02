# setDataDash() Function Documentation
## Hotel Impression Tracking & Merchandising Variables

### **Overview**
The `setDataDash()` function is a custom JavaScript implementation that automatically extracts hotel listing data when package containers enter the user's viewport and sets data attributes for downstream analytics tracking. This enables sophisticated product impression tracking and merchandising variable population for Adobe Analytics.

---

## **Function Purpose & Business Value**

### **Primary Functions:**
1. **Viewport-based Impression Tracking** - Captures when hotel packages become visible to users
2. **React Props Extraction** - Dynamically extracts hotel data from React component props
3. **Data Attribute Population** - Sets structured data attributes on DOM elements
4. **Merchandising Variable Preparation** - Prepares data for Adobe Analytics product strings
5. **Error Handling & Monitoring** - Tracks JavaScript errors for debugging

### **Business Benefits:**
- **Accurate Impression Tracking**: Only tracks when hotels are actually viewed
- **Rich Product Data**: Captures comprehensive hotel attributes for analysis
- **Performance Optimization**: Lazy-loads data only when needed
- **Error Resilience**: Continues operation even with partial data failures

---

## **Adobe Launch Rule Configuration**

### **Rule Details:**
- **Name**: `[10] setDataDash() [impression data tracking]`
- **Order**: 10 (High priority for early execution)
- **Type**: Viewport-based tracking rule

### **Event Configuration:**
```javascript
Event Type: "Enters Viewport"
CSS Selector: ".package-info-container"
Frequency: "every time element enters viewport"
```

### **Conditions:**
```javascript
1. Page Name Condition:
   - page.pagename === "hotel selection"
   
2. Custom Code Condition:
   - sessionStorage.getItem('data-dash') != 'off'
   // Allows for temporary disabling of tracking
```

### **Actions:**
```javascript
Action: Custom Code Execution
Function: setDataDash()
Timeout: 2000ms
Delay Next: true
```

---

## **Technical Implementation**

### **Data Extraction Process:**

#### **1. React Props Discovery**
```javascript
// Locates React component props dynamically
var packs = Array.from(document.querySelectorAll('.package-info-container'));
packs.forEach(pack => {
  for (let key in pack) {
    if (key.startsWith('__reactProps')) {
      details.push(pack[key]);
    }
  }
});
```

#### **2. Hotel Details Extraction**
```javascript
var targetDetails = detail.children.props.children[0].props.children[0].props;

// Extracted Hotel Attributes:
{
  "hotelName": // Sanitized hotel name
  "hotelCategory": // Hotel type categories  
  "hotelRating": // Star rating
  "hotelCode": // Unique hotel identifier
  "chainCode": // Hotel chain identifier
  "locationCode": // Location identifier
  "checkInDate": // Check-in date
  "checkOutDate": // Check-out date
  "tripDuration": // Length of stay
  "propertyThumbnail": // Image category
}
```

#### **3. Pricing Information Extraction**
```javascript
var targetPricing = detail.children.props.children[1].props;

// Extracted Pricing Attributes:
{
  "displayPrice": // Total cash amount
  "avgEarn": // Average points earning
  "productEarn": // Product-specific earning
  "promoCodes": // Applied promotional codes
}
```

### **Data Sanitization**
```javascript
// Removes characters that could break product strings
"hotelName": detail.hotelOption.name.replace(/;|,|'|-|:|\|/g,' ')

// Handles array vs string promotional codes
"promoCodes": Array.isArray(detail.price.prepay.promotions[0]?.code) ? 
  detail?.price?.prepay?.promotions[0]?.code : 
  Array(detail?.price?.prepay?.promotions[0]?.code).join(':')
```

---

## **Data Attributes Applied**

### **Hotel Information Attributes:**
- `data-hotelName` - Sanitized hotel name
- `data-hotelCategory` - Hotel type/category
- `data-hotelRating` - Star rating (1-5)
- `data-hotelCode` - Unique hotel identifier
- `data-chainCode` - Hotel chain code
- `data-locationCode` - Destination/location code

### **Booking Details Attributes:**
- `data-checkInDate` - Guest check-in date
- `data-checkOutDate` - Guest check-out date  
- `data-tripDuration` - Length of stay in nights
- `data-propertyThumbnail` - Image classification

### **Pricing Attributes:**
- `data-displayPrice` - Total package price
- `data-avgEarn` - Average points earning potential
- `data-productEarn` - Product-specific points earning
- `data-promoCodes` - Applied promotional codes

### **Position Tracking:**
- `data-order` - Sequential position in search results (1, 2, 3...)

---

## **Error Handling & Monitoring**

### **Multi-Level Error Catching:**
```javascript
1. React Props Extraction Errors
   - Handles missing or malformed React component data
   
2. Data Attribute Mapping Errors  
   - Manages partial data extraction failures
   
3. DOM Manipulation Errors
   - Catches issues with setting data attributes
```

### **Error Tracking Integration:**
```javascript
// Feeds into global error monitoring system
var ruleName = event.$rule.name,
    JSerror = error.message;
window.failedCode.push(`${ruleName} : ${JSerror}`)
```

### **Graceful Degradation:**
- Continues processing remaining elements even if one fails
- Logs specific error contexts for debugging
- Prevents duplicate processing with attribute checks

---

## **Performance Considerations**

### **Optimization Features:**
- **Duplicate Prevention**: Checks for existing `data-hotelCode` attribute
- **Viewport-based Firing**: Only processes visible elements
- **Efficient DOM Querying**: Uses modern `querySelectorAll` with array conversion
- **Error Isolation**: Individual element failures don't stop batch processing

### **Resource Usage:**
- **Memory**: Temporary arrays for data processing
- **CPU**: React props traversal and attribute setting
- **Network**: No external requests, DOM-only operations

---

## **Integration with Analytics**

### **Downstream Usage:**
The data attributes set by `setDataDash()` are consumed by:

1. **Product String Generation** - For Adobe Analytics merchandising variables
2. **Impression Event Tracking** - When elements enter viewport
3. **Click Event Enhancement** - Adding context to user interactions
4. **A/B Testing Variables** - For personalization and testing

### **Analytics Variables Populated:**
- **Product Strings**: `s.products` with hotel-specific merchandising eVars
- **Custom Events**: Impression tracking events
- **Context Data**: Hotel attributes for processing rules

---

## **Usage Examples**

### **Typical Flow:**
```javascript
1. User scrolls down hotel search results
2. Package container enters viewport
3. setDataDash() fires automatically
4. Data attributes applied to DOM element
5. Subsequent analytics rules use these attributes
6. Product impressions tracked in Adobe Analytics
```

### **Data Attribute Output Example:**
```html
<div class="package-info-container" 
     data-hotelname="Grand Beach Resort"
     data-hotelcategory="Resort|Beach"
     data-hotelrating="4"
     data-hotelcode="GBR123"
     data-chaincode="GRAND"
     data-locationcode="CUN"
     data-checkindate="2024-03-15"
     data-checkoutdate="2024-03-18"
     data-tripduration="3"
     data-displayprice="1299.99"
     data-avgEarn="650"
     data-productEarn="750"
     data-promocodes="SAVE20"
     data-order="1">
```

---

## **Troubleshooting Guide**

### **Common Issues:**

#### **1. No Data Attributes Applied**
- **Cause**: React props structure changed
- **Fix**: Verify component hierarchy in browser dev tools
- **Check**: Console for "setDataDash - targetDetails Error"

#### **2. Partial Data Missing**
- **Cause**: Optional fields not present in some hotels
- **Behavior**: Function continues with available data
- **Monitoring**: Check `window.failedCode` array

#### **3. Function Not Firing**
- **Check**: Page name condition (`page.pagename === "hotel selection"`)
- **Check**: SessionStorage setting (`data-dash` not set to 'off')
- **Check**: CSS selector matches (`.package-info-container`)

### **Debugging Commands:**
```javascript
// Check if function has run
document.querySelector('.package-info-container').hasAttribute('data-hotelCode')

// View collected errors
console.log(window.failedCode)

// Manual function execution
setDataDash()

// Check data-dash setting
sessionStorage.getItem('data-dash')
```

---

## **Maintenance & Updates**

### **Regular Monitoring:**
- **Error Rate**: Monitor `window.failedCode` for increase in failures
- **Data Quality**: Verify all expected attributes are being set
- **Performance**: Check for viewport event timing issues

### **Update Triggers:**
- React component structure changes
- New hotel data fields required
- Analytics requirements changes
- Performance optimization needs

### **Version History:**
- **Current**: Multi-level error handling with comprehensive hotel data
- **Future**: Consider adding A/B testing data attributes
- **Future**: Potential real-time pricing update integration

This implementation represents a sophisticated approach to viewport-based impression tracking with robust error handling and comprehensive data extraction for travel industry analytics. 