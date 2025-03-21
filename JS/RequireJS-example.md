RequireJS is a JavaScript library that helps in modularizing your code by loading dependencies asynchronously. Below is an example of how to use RequireJS to load and use modules.

### 1. Directory Structure
Assume you have the following directory structure:

```
project/
│
├── index.html
├── js/
│   ├── main.js
│   ├── require.js
│   ├── module1.js
│   └── module2.js
```

### 2. `index.html`
This is your main HTML file where you include RequireJS and specify the entry point (`main.js`).

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>RequireJS Example</title>
</head>
<body>
    <h1>RequireJS Example</h1>
    <script data-main="js/main" src="js/require.js"></script>
</body>
</html>
```

### 3. `main.js`
This is the entry point for your application. It configures RequireJS and loads the necessary modules.

```javascript
requirejs.config({
    baseUrl: 'js', // Base URL for all modules
    paths: {
        // Define paths to your modules
        module1: 'module1',
        module2: 'module2'
    }
});

// Load the main module and start the application
requirejs(['module1', 'module2'], function(module1, module2) {
    console.log("Main module loaded");

    // Use the modules
    module1.sayHello();
    module2.sayGoodbye();
});
```

### 4. `module1.js`
This is a simple module that exports a function.

```javascript
define(function() {
    return {
        sayHello: function() {
            console.log("Hello from module1!");
        }
    };
});
```

### 5. `module2.js`
This is another simple module that exports a function.

```javascript
define(function() {
    return {
        sayGoodbye: function() {
            console.log("Goodbye from module2!");
        }
    };
});
```

### 6. Running the Example
When you open `index.html` in your browser, the following will happen:

1. RequireJS will load `main.js` as the entry point.
2. `main.js` will configure RequireJS and load `module1.js` and `module2.js`.
3. The functions `sayHello` and `sayGoodbye` will be called, and you should see the following output in the browser's console:

```
Hello from module1!
Goodbye from module2!
```

### 7. Additional Notes
- You can also load third-party libraries like jQuery using RequireJS by specifying the paths in the `requirejs.config` call.
- If you have dependencies between modules, you can specify them in the `define` function.

For example, if `module2` depends on `module1`:

```javascript
define(['module1'], function(module1) {
    return {
        sayGoodbye: function() {
            module1.sayHello();
            console.log("Goodbye from module2!");
        }
    };
});
```

This is a basic example to get you started with RequireJS. You can build more complex applications by organizing your code into multiple modules and managing dependencies effectively.
