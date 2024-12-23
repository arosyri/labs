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
## **Task 2**
Explores the use of Promises and async/await for asynchronous operations.

### **Main tasks:**
- Implements `promiseMap` for handling async operations on arrays using Promises.
- Demonstrates both Promise-based and async/await usage.
- Supports optional parallelism control for limiting concurrent tasks.

**Example Implementation:**
```javascript
function promiseMap(array, promiseCb, limit = Infinity) {
    const results = [];
    const taskQueue = [...array];
    let actCount = 0;
    let complCount = 0;

    return new Promise((resolve, reject) => {
        const executeNext = () => {
            if (complCount === array.length) {
                return resolve(results);
            }

            if (actCount < limit && taskQueue.length > 0) {
                const index = array.length - taskQueue.length;
                const item = taskQueue.shift();

                actCount++;
                promiseCb(item, index)
                    .then((result) => {
                        results[index] = result;
                        complCount++;
                        actCount--;
                        executeNext();
                    })
                    .catch(reject);
                executeNext(); 
            }
        };

        executeNext();
    });
}
```

**Usage Example with Promises:**
```javascript
const asyncTask = (value) => // basic transformation
    new Promise((resolve) =>
        setTimeout(() => resolve(value * 2), 1000)
    );

promiseMap([1, 2, 3, 4, 5], asyncTask, 2)
    .then((results) => console.log("Results:", results)) // [2, 4, 6, 8, 10]
    .catch((err) => console.error("Error:", err));

const fetchData = (id) => // API fetching
    fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
        .then((response) => response.json());

promiseMap([1, 2, 3, 4, 5], fetchData, 3)
    .then((results) => console.log(results))
    .catch((err) => console.error("Error:", err));
```

**Usage Example with Async/Await:**
```javascript
(async () => { // basic transformation
    const asyncTask = (value) =>
        new Promise((resolve) =>
            setTimeout(() => resolve(value * 2), 1000)
        );

    const results = await promiseMap([1, 2, 3, 4, 5], asyncTask, 2);
    console.log("Results:", results); // [2, 4, 6, 8, 10]
})();

(async () => { //processing large files
    const processFile = async (filename) => {
        await new Promise((resolve) => setTimeout(resolve, 1000)); 
        return `${filename} processed`;
    };

    const filenames = ["file1.txt", "file2.txt", "file3.txt"];
    const results = await promiseMap(filenames, processFile, 1); 
    console.log(results); // ["file1.txt processed", "file2.txt processed", "file3.txt processed"]
})();
```

---
## **Task 3**
Integrates `AbortController` for canceling asynchronous operations during execution.

### **Main tasks:**
- Adds support for canceling tasks through an `AbortController`.

**Usage Example:**
```javascript
const controller = new AbortController();
asyncMapAbort(
  [1, 2, 3],
  (data, cb) => {
    setTimeout(() => cb(null, data * 2), 1000);
  },
  controller.signal,
  (err, result) => {
    if (err) console.error(err.message); // Aborted
    else console.log(result);
  }
);

// Abort the operation
setTimeout(() => controller.abort(), 500);
```

---
## **Task 4**
Showcases the use of `Readable` streams or async generators for processing large datasets that cannot fit in memory.

### **Main tasks:**
- Implements an async generator and Readable streams for processing large datasets.

**Using Readable Streams:**
```javascript
const fs = require("fs");
const readline = require("readline");


const processFile = async (filePath) => {
    const fileStream = fs.createReadStream(filePath); // create a stream for reading the file

    const rl = readline.createInterface({
        input: fileStream,
        crlfDelay: Infinity, // recognize all instances of CRLF (\r\n) as a single newline
    });

    console.log("Processing large file:");

    for await (const line of rl) {
        console.log(`Line: ${line}`);
    }

    console.log("File processing complete.");
};

processFile("largeFile.txt").catch((err) => console.error("Error:", err));
```

**Using Async Generators:**
```javascript
async function* fetchDataset(data) {
    for (const item of data) {
        await new Promise((resolve) => setTimeout(resolve, 100));
        yield item * 2;
    }
}

const processDataset = async () => {
    const largeDataset = Array.from({ length: 1000 }, (_, i) => i + 1); // simulate a large dataset

    console.log("Processing large dataset:");

    for await (const value of fetchDataset(largeDataset)) {
        console.log(`Processed value: ${value}`);
    }

    console.log("Dataset processing complete.");
};
// process the large dataset
processDataset().catch((err) => console.error("Error:", err));

```

---

