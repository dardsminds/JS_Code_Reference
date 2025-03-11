Creating a jQuery plugin for form handling can simplify tasks like validation, submission, and resetting forms. Below is an example of a jQuery plugin that handles form validation, submission via AJAX, and form resetting.

### Example: jQuery Plugin for Form Handling

```javascript
(function($) {
    // Define the plugin name and default options
    $.fn.formHandler = function(options) {
        // Default settings
        var settings = $.extend({
            validation: true, // Enable or disable validation
            submitHandler: null, // Custom submit handler function
            resetHandler: null, // Custom reset handler function
            errorClass: 'error', // CSS class for error messages
            successClass: 'success', // CSS class for success messages
            ajaxSubmit: false, // Submit form via AJAX
            ajaxUrl: '', // URL for AJAX submission
            ajaxMethod: 'POST', // HTTP method for AJAX submission
            beforeSubmit: null, // Function to run before submission
            afterSubmit: null // Function to run after submission
        }, options);

        // Validate the form
        function validateForm(form) {
            var isValid = true;
            form.find('input, textarea, select').each(function() {
                var $field = $(this);
                if ($field.prop('required') && !$field.val().trim()) {
                    isValid = false;
                    $field.addClass(settings.errorClass);
                    $field.after('<span class="' + settings.errorClass + '">This field is required.</span>');
                } else {
                    $field.removeClass(settings.errorClass);
                    $field.next('.' + settings.errorClass).remove();
                }
            });
            return isValid;
        }

        // Submit the form
        function submitForm(form) {
            if (settings.beforeSubmit) {
                settings.beforeSubmit(form);
            }

            if (settings.ajaxSubmit) {
                $.ajax({
                    url: settings.ajaxUrl,
                    method: settings.ajaxMethod,
                    data: form.serialize(),
                    success: function(response) {
                        form.find('.' + settings.errorClass).remove();
                        form.addClass(settings.successClass);
                        if (settings.afterSubmit) {
                            settings.afterSubmit(response);
                        }
                    },
                    error: function(xhr, status, error) {
                        form.addClass(settings.errorClass);
                        if (settings.afterSubmit) {
                            settings.afterSubmit(xhr.responseText);
                        }
                    }
                });
            } else {
                form[0].submit(); // Standard form submission
            }
        }

        // Reset the form
        function resetForm(form) {
            form[0].reset();
            form.find('.' + settings.errorClass).remove();
            form.removeClass(settings.errorClass).removeClass(settings.successClass);
            if (settings.resetHandler) {
                settings.resetHandler(form);
            }
        }

        // Apply the plugin to each form
        return this.each(function() {
            var $form = $(this);

            // Handle form submission
            $form.on('submit', function(e) {
                e.preventDefault();
                if (settings.validation && !validateForm($form)) {
                    return false;
                }
                if (settings.submitHandler) {
                    settings.submitHandler($form);
                } else {
                    submitForm($form);
                }
            });

            // Handle form reset
            $form.on('reset', function() {
                resetForm($form);
            });
        });
    };
})(jQuery);
```

### How to Use the Plugin

1. Include jQuery in your HTML file.
2. Include the plugin script after jQuery.
3. Call the plugin on your form elements.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery Form Handling Plugin Example</title>
    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="path/to/your/plugin.js"></script>
    <style>
        .error { color: red; }
        .success { color: green; }
    </style>
</head>
<body>
    <form id="exampleForm">
        <label for="name">Name:</label>
        <input type="text" id="name" name="name" required>
        <br>
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required>
        <br>
        <button type="submit">Submit</button>
        <button type="reset">Reset</button>
    </form>

    <script>
        $(document).ready(function() {
            $('#exampleForm').formHandler({
                validation: true,
                ajaxSubmit: true,
                ajaxUrl: 'submit.php',
                ajaxMethod: 'POST',
                beforeSubmit: function(form) {
                    console.log('Form is being submitted...');
                },
                afterSubmit: function(response) {
                    console.log('Form submitted successfully!', response);
                },
                resetHandler: function(form) {
                    console.log('Form has been reset.');
                }
            });
        });
    </script>
</body>
</html>
```

### Explanation

1. **Plugin Definition**:
   - The plugin is defined as `formHandler` and added to `$.fn`.
   - It accepts an `options` object to customize behavior.

2. **Validation**:
   - The `validateForm` function checks if required fields are filled out and adds error messages if not.

3. **Form Submission**:
   - The `submitForm` function handles form submission, either via AJAX or standard submission.
   - If `ajaxSubmit` is enabled, the form data is sent to the specified URL using the specified HTTP method.

4. **Form Reset**:
   - The `resetForm` function resets the form and clears any error or success messages.

5. **Event Handlers**:
   - The plugin attaches `submit` and `reset` event handlers to the form to manage form submission and resetting.

6. **Customization**:
   - You can customize the plugin by passing options like `validation`, `ajaxSubmit`, `beforeSubmit`, `afterSubmit`, and `resetHandler`.

### Notes:
- Ensure that the server-side script (`submit.php` in this example) is set up to handle the form data if using AJAX submission.
- You can extend this plugin to include more advanced validation rules, file uploads, or other features as needed.

This plugin provides a reusable and modular way to handle forms in your web applications.
