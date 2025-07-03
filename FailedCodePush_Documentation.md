# FailedCodePush Error Tracking System Documentation
## Southwest Airlines Tag Manager - JavaScript Error Monitoring & Debugging

### **Overview**
The `failedCodePush` system is a comprehensive JavaScript error tracking and monitoring solution built into the Southwest Airlines Adobe Launch tag manager implementation. It captures JavaScript errors from custom code blocks, aggregates them, and sends detailed error information to Adobe Analytics for monitoring and debugging purposes.

---

## **🛠️ System Architecture**

### **Core Components**

#### **1. Global Error Array Initialization**
```javascript
window.failedCode = window.failedCode || [];
```
- **Purpose**: Creates a global array to store JavaScript errors
- **Location**: Initialized in Library Loaded event (Rule Order: 50)
- **Scope**: Available throughout the entire session

#### **2. FailedCodePush Function**
```javascript
window.failedCodePush = function failedCodePush(failedComponent) {
    let ruleName = event.$rule.name,
        JSerror = error.message,
        component = !!failedComponent ? `_${failedComponent}` : '';
    window.failedCode.push(`${ruleName}${component} : ${JSerror}`)
}
```
- **Purpose**: Standardized function for pushing errors to the global array
- **Parameters**: 
  - `failedComponent` (optional): Specific component/section that failed
- **Output**: Formatted error string with rule name, component, and error message

#### **3. Analytics Integration (doPlugins)**
```javascript
try {
    if(!!window.failedCode.length) {
        // Set Analytics variables
        s.linkTrackVars = s.linkTrackVars + linkVars.join(',')
        s.list3 = failedCode.join(',');
        
        // Reset failed code array
        window.failedCode = [];
    }
} catch(error) {
    s.linkTrackVars = s.linkTrackVars + linkVars.join(',')
    window.failedCode = [`doPlugins : failedCode array_${error}`];
    s.list3 = failedCode.join(',');
    window.failedCode = [];
}
```
- **Purpose**: Sends collected errors to Adobe Analytics
- **Analytics Variable**: `s.list3` (List Variable 3)
- **Frequency**: Every Analytics call (page views, events)
- **Reset**: Array is cleared after each transmission

---

## **📋 Implementation Guide**


### **Method 1: Using FailedCodePush Function (Recommended)**
```javascript
try {
    // Your custom code logic here
    var data = someComplexOperation();
    console.log('Operation successful:', data);
    
} catch (error) {
    // Using the standardized function
    window.failedCodePush();
}
```

### **Method 2: Component-Specific Error Tracking**
```javascript
try {
    // Component A logic
    var componentA = processComponentA();
    
} catch (error) {
    window.failedCodePush('ComponentA');
}

try {
    // Component B logic  
    var componentB = processComponentB();
    
} catch (error) {
    window.failedCodePush('ComponentB');
}
```

### **Method 3: Multiple Try-Catch Blocks**
```javascript
// Global rule context
var ruleName = event.$rule.name;

// Critical operation 1
try {
    var searchFilters = _satellite.getVar("results.filters");
    s.eVar92 = searchFilters.map(/* processing logic */).join(";");
} catch (error) {
    s.eVar92 = "filter data unavailable";
    window.failedCode.push(`${ruleName}_searchFilters : ${error.message}`);
}

// Critical operation 2  
try {
    var productData = event.message?.product;
    s.products = processProductData(productData);
} catch (error) {
    s.products = "product data unavailable";
    window.failedCode.push(`${ruleName}_productData : ${error.message}`);
}
```
### **Method 4: Direct Array Push**
```javascript
try {
    // Your custom code logic here
    var data = someComplexOperation();
    console.log('Operation successful:', data);
    
} catch (error) {
    // Error tracking implementation
    var ruleName = event.$rule.name,
        JSerror = error.message;
    window.failedCode.push(`${ruleName} : ${JSerror}`);
}
```

---

## **🔍 Real Implementation Examples**

### **Example 1: Global Page Load Rule**
```javascript
// Search Filters Processing
try {
    const filters = _satellite.getVar("results.filters");
    s.eVar92 = filters.map(filter => {
        let value = filter.filtervalue;
        if (typeof value === "object" && value !== null) {
            let parts = [];
            if ("min" in value) parts.push(`Min: ${value.min}`);
            if ("max" in value) parts.push(`Max: ${value.max}`);
            value = parts.join(" ");
        }
        return value;
    }).join(";");
} catch (e) {
    s.eVar92 = "filter data unavailable";
    failedCode.push('searchFilters(e92)');
}

// Product String Building
try {
    window.productArray = [];
    var products = [...eventProduct, ...eventProductAttr];
    
    products.forEach(item => {
        // Product processing logic
        switch (item.producttype) {
            case "hotel":
                // Hotel product logic
                break;
            case "flight":
                // Flight product logic  
                break;
        }
    });
    
    s.products = productArray.join(',');
} catch (error) {
    failedCode.push('s.products');
}
```

### **Example 2: SetDataDash Implementation**
```javascript
function setDataDash() {
    try {
        if (!event.element.hasAttribute('data-hotelCode')) {
            var packs = Array.from(document.querySelectorAll('.package-info-container'));
            
            // Process React props
            details.forEach(detail => {
                try {
                    var targetDetails = detail.children.props.children[0].props.children[0].props;
                    hotelDetails.push(targetDetails);
                } catch (error) {
                    console.log('setDataDash - targetDetails Error');
                    var ruleName = event.$rule.name,
                        JSerror = error.message;
                    window.failedCode.push(`${ruleName} : ${JSerror}`);
                }
            });
            
        }
    } catch (error) {
        console.log('setDataDash - attributes loop Error');
        var ruleName = event.$rule.name,
            JSerror = error.message;
        window.failedCode.push(`${ruleName} : ${JSerror}`);
    }
}
```

### **Example 3: GetDataDash Implementation**
```javascript
// Hotel impression tracking with error handling
try {
    var hotelCode = event.element.getAttribute('data-hotelCode');
    var eVarArray = [
        `eVar96=${hotelCode}`,
        `eVar123=${hotelName}`,
        `eVar130=${hotelRating}`
    ];
    s.products = `hotel;${hotelCode};;;event73=1;` + eVarArray.join("|");
    
} catch (error) {
    var ruleName = event.$rule.name,
        JSerror = error.message;
    window.failedCode.push(`${ruleName} : ${JSerror}`);
}
```

---

## **📊 Error Tracking Output**

### **Error String Format**
```
{RuleName}_{Component} : {ErrorMessage}
```

### **Example Error Outputs**
```
[100] global pageload_searchFilters : Cannot read property 'map' of undefined
target library load page top : ReferenceError: _satellite is not defined  
setDataDash Hotel Impression : TypeError: Cannot read property 'hotelCode' of null
[50] p hotel results_s.products : Invalid product string format
global pageload_cbid : sessionStorage is not defined
doPlugins : failedCode array_Cannot read property 'length' of undefined
```

### **Analytics Integration**
- **Variable**: Adobe Analytics List Variable 3 (`s.list3`)
- **Delimiter**: Comma (`,`)
- **Max Length**: 255 characters per list item
- **Frequency**: Sent with every Analytics call
- **Reset**: Array cleared after transmission

---

## **🎯 Best Practices**

### **1. Granular Error Tracking**
```javascript
// ❌ Too broad
try {
    // 50 lines of complex logic
} catch (error) {
    window.failedCode.push(`${ruleName} : ${error.message}`);
}

// ✅ Granular tracking
try {
    var data = getSearchData();
} catch (error) {
    window.failedCode.push(`${ruleName}_getSearchData : ${error.message}`);
}

try {
    var processed = processSearchData(data);
} catch (error) {
    window.failedCode.push(`${ruleName}_processSearchData : ${error.message}`);
}
```

### **2. Graceful Degradation**
```javascript
try {
    s.eVar92 = complexFilterProcessing();
} catch (error) {
    s.eVar92 = "filter processing failed"; // Fallback value
    window.failedCode.push(`${ruleName}_filterProcessing : ${error.message}`);
}
```

### **3. Component Identification**
```javascript
// ✅ Clear component identification
window.failedCode.push(`${ruleName}_searchFilters : ${error.message}`);
window.failedCode.push(`${ruleName}_productString : ${error.message}`);
window.failedCode.push(`${ruleName}_cbidTracking : ${error.message}`);
```

### **4. Critical vs Non-Critical Operations**
```javascript
// Critical operation - must not fail silently
try {
    s.pageName = getPageName();
} catch (error) {
    s.pageName = "error getting page name";
    window.failedCode.push(`${ruleName}_pageName : ${error.message}`);
}

// Non-critical operation - can fail gracefully
try {
    s.eVar100 = getOptionalData();
} catch (error) {
    // Optional: track but don't set fallback
    window.failedCode.push(`${ruleName}_optionalData : ${error.message}`);
}
```

### **5. Rule Name Context**
```javascript
// Get rule name at the top of your custom code
var ruleName = event.$rule.name;

// Use consistently throughout the rule
try {
    // logic
} catch (error) {
    window.failedCode.push(`${ruleName}_operation : ${error.message}`);
}
```

---

## **🔧 Implementation Checklist**

### **For New Custom Code Blocks:**
- [ ] Initialize rule name variable: `var ruleName = event.$rule.name;`
- [ ] Wrap critical operations in try-catch blocks
- [ ] Use descriptive component names for error tracking
- [ ] Provide fallback values for essential Analytics variables
- [ ] Test error scenarios during development

### **For Existing Custom Code Blocks:**
- [ ] Identify critical operations that could fail
- [ ] Add try-catch blocks around data processing logic
- [ ] Implement failedCode tracking for each operation
- [ ] Test with invalid data to ensure error handling works
- [ ] Verify error messages appear in Adobe Analytics List3

### **For Complex Operations:**
- [ ] Break into smaller, trackable components
- [ ] Use component-specific error identification
- [ ] Implement progressive fallbacks
- [ ] Add console logging for development debugging
- [ ] Document expected error scenarios

---

## **📈 Monitoring & Alerting**

### **Adobe Analytics Reporting**
- **Variable**: List Variable 3 (`s.list3`)
- **Reporting**: Create custom reports on JavaScript errors
- **Segmentation**: Filter by rule names or error types
- **Trending**: Monitor error frequency over time

### **Common Error Patterns to Monitor**
1. **Data Layer Errors**: `Cannot read property 'X' of undefined`
2. **Product String Errors**: Invalid formatting or missing data
3. **Search Filter Errors**: Mapping or processing failures
4. **Impression Tracking Errors**: DOM element access issues
5. **Session Storage Errors**: Browser storage access issues

### **Alerting Recommendations**
- Set up alerts for error spikes in specific rules
- Monitor for new error patterns after deployments
- Track error rates vs. total page views
- Alert on critical rule failures (page name, product strings)

---

## **🚀 Advanced Usage**

### **Custom Error Context**
```javascript
try {
    // Operation with additional context
    var result = complexOperation(data);
} catch (error) {
    var context = `dataType:${typeof data}, length:${data?.length}`;
    window.failedCode.push(`${ruleName}_operation : ${error.message} | ${context}`);
}
```

### **Error Severity Levels**
```javascript
// Critical error - affects core functionality
window.failedCode.push(`CRITICAL_${ruleName}_pageName : ${error.message}`);

// Warning - affects optional features  
window.failedCode.push(`WARNING_${ruleName}_optionalData : ${error.message}`);

// Info - for debugging purposes
window.failedCode.push(`INFO_${ruleName}_debug : ${error.message}`);
```

### **Bulk Error Processing**
```javascript
var errors = [];

// Collect multiple potential errors
try { processA(); } catch (e) { errors.push(`processA:${e.message}`); }
try { processB(); } catch (e) { errors.push(`processB:${e.message}`); }
try { processC(); } catch (e) { errors.push(`processC:${e.message}`); }

// Push all errors at once
if (errors.length > 0) {
    window.failedCode.push(`${ruleName}_bulkProcessing : ${errors.join(' | ')}`);
}
```

---

## **📋 Team Guidelines**

### **When to Implement**
- ✅ **Always**: In custom code blocks that process data layer information
- ✅ **Always**: When setting critical Analytics variables (pageName, products, events)
- ✅ **Always**: In impression tracking and dynamic content generation
- ✅ **Recommended**: For API calls and external data processing
- ❌ **Not Required**: In simple variable assignments with static values

### **Code Review Checklist**
- [ ] Are critical operations wrapped in try-catch?
- [ ] Are error messages descriptive and component-specific?
- [ ] Are fallback values provided for essential variables?
- [ ] Is the rule name captured consistently?
- [ ] Are errors tested with invalid data scenarios?

### **Testing Protocol**
1. **Test with valid data**: Ensure normal operation works
2. **Test with invalid data**: Verify error handling triggers
3. **Test with missing data**: Confirm graceful degradation
4. **Check Analytics**: Verify errors appear in List3
5. **Monitor console**: Look for expected error logs

---

*This error tracking system provides comprehensive monitoring and debugging capabilities for the Southwest Airlines tag manager implementation. By following these guidelines, the team can proactively identify and resolve JavaScript issues while maintaining robust Analytics data collection.* 
