# labs
## **Task 1**
Demonstrates the creation of an asynchronous `map` function using callbacks.

### **Main tasks:**
- Implements `asyncMap` for async operations on array elements.
- Supports an optional debounce mechanism to delay execution if the task completes too quickly.

**Usage Example:**
```javascript
asyncMap(
  [1, 2, 3],
  (data, cb) => {
    setTimeout(() => {
      cb(null, data * 2);
    }, 1000);
  },
  (err, result) => {
    console.log(err, result); // null [2, 4, 6]
  }
);
```

---
