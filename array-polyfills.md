# Array Polyfills in JavaScript

## Map Polyfill

### Description
The `map()` method creates a new array with the results of calling a provided function on every element in the calling array.

### Polyfill Implementation
```javascript
Array.prototype.map = function(logic) {
    let result = [];
    for (let i = 0; i < this.length; i++) {
        result.push(logic(this[i]));
    }   
    return result;
}
```

### Example
```javascript
const arr = [1, 2, 3, 4, 5];
const multiplyBy2 = arr.map((item) => item * 2);
// Result: [2, 4, 6, 8, 10]
```

## Filter Polyfill

### Description
The `filter()` method creates a new array with all elements that pass the test implemented by the provided function.

### Polyfill Implementation
```javascript
Array.prototype.filter = function(logic) {
    let result = [];
    for (let i = 0; i < this.length; i++) {
        if (logic(this[i])) {
            result.push(this[i]);
        }
    }
    return result;
}
```

### Example
```javascript
const arr = [10, 12, 3, 41, 25];
const evenNumbers = arr.filter(item => item % 2 === 0);
// Result: [10, 12]
```

## Reduce Polyfill

### Description
The `reduce()` method executes a reducer function on each element of the array, resulting in a single output value.

### Polyfill Implementation
```javascript
Array.prototype.reduce = function(logic, initialValue) {
    let accumulator = initialValue;
    for (let i = 0; i < this.length; i++) {
        accumulator = logic(accumulator, this[i]);
    }
    return accumulator;
}
```

### Example
```javascript
const arr = [1, 2, 3, 4, 5];
const sum = arr.reduce((acc, item) => acc + item, 0);
// Result: 15
```

## Important Notes
- These polyfills are simplified versions and may not cover all edge cases of native implementations
- Modern browsers support these methods natively
- For production, prefer native methods or well-tested polyfill libraries
