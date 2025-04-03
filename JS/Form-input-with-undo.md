# JavaScript Form Input with Undo Feature

Here's a complete implementation of a form input with undo functionality:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Form Input with Undo Feature</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 600px;
            margin: 0 auto;
            padding: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input, textarea {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 8px 16px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            margin-right: 10px;
        }
        button:hover {
            background-color: #45a049;
        }
        button:disabled {
            background-color: #cccccc;
            cursor: not-allowed;
        }
        .undo-btn {
            background-color: #f44336;
        }
        .undo-btn:hover {
            background-color: #d32f2f;
        }
        .controls {
            margin-top: 10px;
        }
    </style>
</head>
<body>
    <h1>Form with Undo Feature</h1>
    
    <form id="myForm">
        <div class="form-group">
            <label for="name">Name:</label>
            <input type="text" id="name" data-history>
        </div>
        
        <div class="form-group">
            <label for="email">Email:</label>
            <input type="email" id="email" data-history>
        </div>
        
        <div class="form-group">
            <label for="message">Message:</label>
            <textarea id="message" rows="4" data-history></textarea>
        </div>
        
        <div class="controls">
            <button type="submit">Submit</button>
            <button type="button" id="undoBtn" class="undo-btn" disabled>Undo</button>
        </div>
    </form>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            // Store history for each input
            const historyMap = new Map();
            
            // Get all inputs with data-history attribute
            const inputs = document.querySelectorAll('[data-history]');
            const undoBtn = document.getElementById('undoBtn');
            
            // Initialize history for each input
            inputs.forEach(input => {
                historyMap.set(input, []);
                
                // Save initial value
                saveState(input);
                
                // Add event listeners
                input.addEventListener('input', function() {
                    saveState(this);
                    updateUndoButton();
                });
            });
            
            // Undo button click handler
            undoBtn.addEventListener('click', function() {
                // Find the last modified input
                let lastModifiedInput = null;
                let mostRecentTimestamp = 0;
                
                // Check all inputs to find which one was changed most recently
                inputs.forEach(input => {
                    const history = historyMap.get(input);
                    if (history.length > 1) { // Has previous states
                        const lastChange = history[history.length - 1].timestamp;
                        if (lastChange > mostRecentTimestamp) {
                            mostRecentTimestamp = lastChange;
                            lastModifiedInput = input;
                        }
                    }
                });
                
                if (lastModifiedInput) {
                    undo(lastModifiedInput);
                }
            });
            
            // Form submission handler
            document.getElementById('myForm').addEventListener('submit', function(e) {
                e.preventDefault();
                alert('Form submitted!');
                // Clear history after submission if desired
                // inputs.forEach(input => historyMap.set(input, []));
                // updateUndoButton();
            });
            
            // Save current state of an input to its history
            function saveState(input) {
                const history = historyMap.get(input);
                const currentValue = input.value;
                
                // Don't save if value hasn't changed
                if (history.length > 0 && history[history.length - 1].value === currentValue) {
                    return;
                }
                
                history.push({
                    value: currentValue,
                    timestamp: Date.now()
                });
                
                // Limit history size to prevent memory issues
                if (history.length > 50) {
                    history.shift();
                }
            }
            
            // Undo last change for a specific input
            function undo(input) {
                const history = historyMap.get(input);
                
                if (history.length > 1) {
                    // Remove current state
                    history.pop();
                    
                    // Restore previous state
                    const previousState = history[history.length - 1];
                    input.value = previousState.value;
                    
                    updateUndoButton();
                }
            }
            
            // Enable/disable undo button based on available history
            function updateUndoButton() {
                let canUndo = false;
                
                inputs.forEach(input => {
                    if (historyMap.get(input).length > 1) {
                        canUndo = true;
                    }
                });
                
                undoBtn.disabled = !canUndo;
            }
        });
    </script>
</body>
</html>
```

## How This Works:

1. **History Tracking**: Each input element with the `data-history` attribute has its changes tracked in a Map object.

2. **State Saving**: Whenever an input changes, its current value and timestamp are saved to its history stack.

3. **Undo Functionality**: 
   - The undo button checks which input was modified most recently
   - It restores that input to its previous state
   - The button is disabled when there's nothing to undo

4. **Limitations**:
   - History is limited to 50 states per input to prevent memory issues
   - Only tracks changes to form inputs, not other form modifications

## Enhancements You Could Add:

1. Redo functionality
2. Keyboard shortcuts (Ctrl+Z for undo)
3. Visual indicators of changed fields
4. Local storage to persist history across page refreshes
5. More sophisticated change detection
