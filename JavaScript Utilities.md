# **JavaScript Utilities**

This document provides a collection of useful JavaScript functions for common programming tasks. These functions are designed to be simple, reusable, and beginner-friendly.

---

## **Table of Contents**
1. [Unique ID Generator](#unique-id-generator)
2. [Browser Specifications](#browser-specifications)
3. [Base Converter](#base-converter)

---

## **1. Unique ID Generator**

Generates a unique alphanumeric string with an optional specified length.

### **Function**
```javascript
function unique_id(length = 32) {
  let result = '';
  let characters = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
  // Start with a lowercase letter
  result += characters.charAt(Math.floor(Math.random() * 26));
  // Continue with full character set
  for (let i = 1; i < length; i++) {
    result += characters.charAt(Math.floor(Math.random() * characters.length));
  }
  return result;
}
```

### **Explanation**
- **Purpose**: Generate a unique identifier, useful for session tokens, element IDs, or other cases requiring uniqueness.
- **Starts with Lowercase Letter**: Ensures the first character is a lowercase letter, useful for HTML `id` attributes that should not start with a number.

### **Example Usage**
```javascript
console.log(unique_id()); // Example: "aP9X7kL4w0M6Z2j8n5Q3G1v9C6L7T5a"
console.log(unique_id(16)); // Example: "bX8Z6K4M9N1W3T2Y"
```

---

## **2. Browser Specifications**

Returns an object containing details about the user's browser capabilities and environment.

### **Function**
```javascript
function browserSpecs() {
  let res = {};
  res.navigator = Boolean(Navigator);
  res.storage = Boolean(Storage);
  res.worker = Boolean(Worker);
  res.eventSource = Boolean(EventSource);
  res.serviceWorker = Boolean(navigator.serviceWorker);

  res.cookieEnabled = navigator.cookieEnabled;
  res.platform = navigator.platform;
  res.language = navigator.language;

  return res;
}
```

### **Explanation**
- **Purpose**: Determine the browser's capabilities, such as support for web workers, storage, or service workers.
- **Properties Returned**:
  - `navigator`, `storage`, `worker`, `eventSource`, `serviceWorker`: Boolean values indicating browser feature support.
  - `cookieEnabled`: Whether cookies are enabled.
  - `platform`: The user's operating system or device type.
  - `language`: The preferred language set in the browser.

### **Example Usage**
```javascript
console.log(browserSpecs());
/*
Example Output:
{
  navigator: true,
  storage: true,
  worker: true,
  eventSource: true,
  serviceWorker: true,
  cookieEnabled: true,
  platform: "Win32",
  language: "en-US"
}
*/
```

---

## **3. Base Converter**

Converts numbers between different bases (e.g., decimal to hexadecimal, hexadecimal to decimal).

### **Function**
```javascript
function baseConverter(num, base, newBase) {
  const substitute = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'];

  if (newBase > base) {
    let remainders = [];
    let last = false;

    while (Math.floor(num / newBase) !== 0 || last !== true) {
      if (Math.floor(num / newBase) === 0) {
        last = true;
      }
      remainders.push(substitute[num % newBase]);
      num = Math.floor(num / newBase);
    }

    return remainders.reverse().join('');
  } else {
    let letters = num.split('').reverse();
    let numbers = [];

    letters.forEach((item, i) => {
      numbers.push(substitute.indexOf(item.toUpperCase()) * Math.pow(base, i));
    });

    return numbers.reduce((a, b) => a + b, 0);
  }
}
```

### **Explanation**
- **Purpose**: Convert numbers between different bases, such as binary, decimal, hexadecimal, etc.
- **Supports Two Cases**:
  1. **Base Expansion**: Converts numbers to a higher base (e.g., decimal to hexadecimal).
  2. **Base Reduction**: Converts numbers from a higher base to a lower base (e.g., hexadecimal to decimal).
- **Substitute Array**: Maps digits to characters for bases above 10 (e.g., `A` for 10, `B` for 11).

### **Example Usage**
```javascript
// Convert 1250 (decimal) to hexadecimal
console.log(baseConverter(1250, 10, 16)); // Output: "4E2"

// Convert "4E2" (hexadecimal) to decimal
console.log(baseConverter('4E2', 16, 10)); // Output: 1250
```

### **Limitations**
- Assumes input validity; invalid characters or bases are not handled.
- Supports bases up to 36 due to the English alphabet limit.

---

## **Best Practices**

1. **Error Handling**:
   - Add input validation to ensure valid numbers and bases.
   - Handle edge cases like zero or negative numbers in the base converter.

2. **Browser Capability Checks**:
   - Expand `browserSpecs` to include newer features like WebAssembly or WebRTC.

3. **ID Uniqueness**:
   - For critical use cases (e.g., session tokens), consider using a UUID library like `uuid` for better guarantees.

4. **Optimizing Base Conversion**:
   - Use built-in JavaScript functions like `parseInt` and `toString` for simple conversions:
     ```javascript
     console.log(parseInt('4E2', 16)); // 1250
     console.log((1250).toString(16).toUpperCase()); // "4E2"
     ```

---

## **Conclusion**

These utility functions provide practical solutions for common JavaScript tasks:
- Generating unique IDs for dynamic elements or identifiers.
- Retrieving browser specifications for feature detection.
- Converting numbers between bases for data manipulation or formatting.