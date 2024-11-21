# Promise Polyfills in JavaScript

## Promise.all() Polyfill

### Description
`Promise.all()` takes an array of promises and returns a single promise that resolves when all promises resolve, or rejects if any promise rejects.

### Polyfill Implementation
```javascript
Promise.all = function(promises) {
    return new Promise((resolve, reject) => {
        if (!Array.isArray(promises)) {
            return reject(new TypeError('Argument must be an array'));
        }

        const results = new Array(promises.length);
        let completed = 0;

        promises.forEach((promise, index) => {
            Promise.resolve(promise)
                .then(value => {
                    results[index] = value;
                    completed++;

                    if (completed === promises.length) {
                        resolve(results);
                    }
                })
                .catch(reject);
        });

        if (promises.length === 0) {
            resolve(results);
        }
    });
}
```

### Example
```javascript
const promise1 = Promise.resolve(3);
const promise2 = 42;
const promise3 = new Promise((resolve) => {
    setTimeout(resolve, 100, 'foo');
});

Promise.all([promise1, promise2, promise3])
    .then(values => console.log(values));
// Output: [3, 42, 'foo']
```

## Promise.allSettled() Polyfill

### Description
`Promise.allSettled()` waits for all promises to settle (resolve or reject) and returns their results.

### Polyfill Implementation
```javascript
Promise.allSettled = function(promises) {
    return new Promise((resolve) => {
        if (!Array.isArray(promises)) {
            return resolve([{ status: 'rejected', reason: new TypeError('Argument must be an array') }]);
        }

        const results = new Array(promises.length);
        let completed = 0;

        promises.forEach((promise, index) => {
            Promise.resolve(promise)
                .then(value => {
                    results[index] = { status: 'fulfilled', value };
                    completed++;
                    if (completed === promises.length) {
                        resolve(results);
                    }
                })
                .catch(reason => {
                    results[index] = { status: 'rejected', reason };
                    completed++;
                    if (completed === promises.length) {
                        resolve(results);
                    }
                });
        });

        if (promises.length === 0) {
            resolve(results);
        }
    });
}
```

### Example
```javascript
const promises = [
    Promise.resolve(3),
    new Promise((resolve, reject) => setTimeout(reject, 100, 'error'))
];

Promise.allSettled(promises)
    .then(results => console.log(results));
// Output: [
//   { status: 'fulfilled', value: 3 },
//   { status: 'rejected', reason: 'error' }
// ]
```

## Promise.race() Polyfill

### Description
`Promise.race()` returns a promise that resolves or rejects as soon as one of the promises in the iterable resolves or rejects.

### Polyfill Implementation
```javascript
Promise.race = function(promises) {
    return new Promise((resolve, reject) => {
        if (!Array.isArray(promises)) {
            return reject(new TypeError('Argument must be an array'));
        }

        if (promises.length === 0) {
            return;
        }

        promises.forEach(promise => {
            Promise.resolve(promise)
                .then(resolve)
                .catch(reject);
        });
    });
}
```

### Example
```javascript
const promise1 = new Promise((resolve) => setTimeout(resolve, 500, 'one'));
const promise2 = new Promise((resolve) => setTimeout(resolve, 100, 'two'));

Promise.race([promise1, promise2])
    .then(value => console.log(value));
// Output: 'two'
```

## Promise.any() Polyfill

### Description
`Promise.any()` takes an iterable of promises and returns a promise that resolves with the value of the first promise that fulfills.

### Polyfill Implementation
```javascript
Promise.any = function(promises) {
    return new Promise((resolve, reject) => {
        if (!Array.isArray(promises)) {
            return reject(new TypeError('Argument must be an array'));
        }

        const errors = new Array(promises.length);
        let rejectedCount = 0;

        promises.forEach((promise, index) => {
            Promise.resolve(promise)
                .then(resolve)
                .catch(error => {
                    errors[index] = error;
                    rejectedCount++;

                    if (rejectedCount === promises.length) {
                        reject(new AggregateError(errors, 'All promises were rejected'));
                    }
                });
        });

        if (promises.length === 0) {
            reject(new AggregateError([], 'No promise in promises'));
        }
    });
}
```

### Example
```javascript
const promise1 = Promise.reject('Error 1');
const promise2 = new Promise((resolve) => setTimeout(resolve, 100, 'quick'));
const promise3 = new Promise((resolve) => setTimeout(resolve, 500, 'slow'));

Promise.any([promise1, promise2, promise3])
    .then(value => console.log(value))
    .catch(error => console.log(error));
// Output: 'quick'
```

## 🚨 Important Notes
- These polyfills are simplified implementations
- They may not cover all edge cases of native Promise methods
- Recommended for learning purposes, not production use

## Contributing
Feel free to contribute to this document by creating a pull request or opening an issue!

---
Created with 💻 by AsumaCodes
