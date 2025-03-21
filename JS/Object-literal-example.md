
## Example of a JavaScript object literal

```javascript
// Creating an object literal
const person = {
    firstName: "John",
    lastName: "Doe",
    age: 30,
    isEmployed: true,
    address: {
        street: "123 Main St",
        city: "Anytown",
        state: "CA",
        zipCode: "12345"
    },
    hobbies: ["reading", "traveling", "cycling"],
    greet: function() {
        console.log(`Hello, my name is ${this.firstName} ${this.lastName}.`);
    }
};

// Accessing properties of the object
console.log(person.firstName); // Output: John
console.log(person.address.city); // Output: Anytown
console.log(person.hobbies[1]); // Output: traveling

// Calling a method of the object
person.greet(); // Output: Hello, my name is John Doe.

// Adding a new property
person.email = "john.doe@example.com";
console.log(person.email); // Output: john.doe@example.com

// Modifying an existing property
person.age = 31;
console.log(person.age); // Output: 31

// Deleting a property
delete person.isEmployed;
console.log(person.isEmployed); // Output: undefined
```

### Explanation:
- **Object Literal**: The `person` object is created using curly braces `{}` and contains key-value pairs.
- **Properties**: The object has properties like `firstName`, `lastName`, `age`, `isEmployed`, `address`, and `hobbies`.
- **Nested Object**: The `address` property is itself an object with its own properties (`street`, `city`, `state`, `zipCode`).
- **Array**: The `hobbies` property is an array of strings.
- **Method**: The `greet` property is a function (method) that logs a greeting to the console.
- **Accessing Properties**: You can access properties using dot notation (`person.firstName`) or bracket notation (`person['firstName']`).
- **Adding/Modifying Properties**: You can add new properties or modify existing ones using assignment.
- **Deleting Properties**: You can delete properties using the `delete` operator.

This is a basic example, but object literals can be much more complex and are a fundamental part of JavaScript.
