Here's a clear breakdown of JavaScript object literal syntax with examples:

### Basic Object Literal Syntax
```javascript
const objectName = {
  // Properties (key-value pairs)
  property1: value1,
  property2: value2,
  
  // Methods (function properties)
  method1: function() { /* ... */ },
  
  // Shorthand method syntax (ES6+)
  method2() { /* ... */ },
  
  // Computed property names (ES6+)
  [computedProperty]: 'computed value',
  
  // Getters and setters
  get getterMethod() { /* ... */ },
  set setterMethod(value) { /* ... */ }
};
```

### Complete Example with Different Features
```javascript
const person = {
  // Regular properties
  firstName: 'John',
  lastName: 'Doe',
  age: 30,
  
  // Nested object
  address: {
    street: '123 Main St',
    city: 'New York'
  },
  
  // Array property
  hobbies: ['reading', 'hiking'],
  
  // Method (traditional syntax)
  greet: function() {
    return `Hello, I'm ${this.firstName}!`;
  },
  
  // Method shorthand (ES6+)
  bio() {
    return `${this.firstName} ${this.lastName}, age ${this.age}`;
  },
  
  // Computed property name
  ['full' + 'Name']: 'John Doe',
  
  // Getter
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  
  // Setter
  set fullName(name) {
    const parts = name.split(' ');
    this.firstName = parts[0];
    this.lastName = parts[1];
  }
};
```

### Key Features Explained:

1. **Property Syntax**:
   ```javascript
   const obj = { key: 'value' };
   ```

2. **Method Syntax**:
   ```javascript
   const obj = {
     // Traditional
     method1: function() { ... },
     
     // ES6 shorthand
     method2() { ... }
   };
   ```

3. **Computed Property Names**:
   ```javascript
   const propName = 'status';
   const obj = {
     [propName]: 'active',
     ['item_' + 42]: 'Special Item'
   };
   ```

4. **Getters/Setters**:
   ```javascript
   const obj = {
     _value: 0,
     get value() { return this._value },
     set value(v) { this._value = v }
   };
   ```

5. **Object Initialization**:
   ```javascript
   const point = { x: 10, y: 20 };
   ```

6. **Accessing Properties**:
   ```javascript
   // Dot notation
   person.firstName;
   
   // Bracket notation
   person['lastName'];
   ```

7. **Calling Methods**:
   ```javascript
   person.greet();
   person.bio();
   ```

8. **`this` Context**:
   - Inside methods, `this` refers to the object itself
   - Arrow functions don't have their own `this` context

### Practical Usage Example
```javascript
const calculator = {
  // Properties
  lastResult: 0,
  
  // Methods
  add(a, b) {
    this.lastResult = a + b;
    return this.lastResult;
  },
  
  multiply(a, b) {
    this.lastResult = a * b;
    return this.lastResult;
  },
  
  // Getter
  get result() {
    return this.lastResult;
  },
  
  // Setter
  set result(value) {
    console.log('Setting result to:', value);
    this.lastResult = value;
  }
};

// Usage
calculator.add(5, 3);      // 8
calculator.multiply(4, 2);  // 8
calculator.result;          // 8 (getter)
calculator.result = 10;     // (setter)
```

This syntax provides a clean way to organize related data and functionality in JavaScript.
