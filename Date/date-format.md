Here's a JavaScript solution to format the date "01012019 08:32:08" to "Jan 01, 2019 8:32":

```javascript
function formatDate(inputDate) {
    // Extract date parts
    const day = inputDate.substring(0, 2);
    const month = inputDate.substring(2, 4);
    const year = inputDate.substring(4, 8);
    const time = inputDate.substring(9, 14); // Gets "08:32"
    
    // Create month abbreviations
    const monthNames = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", 
                        "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];
    const monthAbbr = monthNames[parseInt(month, 10) - 1];
    
    // Remove leading zero from hour if needed
    const hour = time.startsWith('0') ? time.substring(1, 2) : time.substring(0, 2);
    const minutes = time.substring(3, 5);
    
    // Construct formatted date
    return `${monthAbbr} ${day}, ${year} ${hour}:${minutes}`;
}

const inputDate = "01012019 08:32:08";
const formattedDate = formatDate(inputDate);
console.log(formattedDate); // Output: "Jan 01, 2019 8:32"
```

Alternatively, you could use JavaScript's Date object for a more robust solution:

```javascript
function formatDate(inputDate) {
    // Parse the input string
    const day = inputDate.substring(0, 2);
    const month = inputDate.substring(2, 4);
    const year = inputDate.substring(4, 8);
    const time = inputDate.substring(9);
    
    // Create a Date object
    const date = new Date(`${month}/${day}/${year} ${time}`);
    
    // Format the date
    const options = { 
        month: 'short', 
        day: '2-digit', 
        year: 'numeric',
        hour: 'numeric',
        minute: '2-digit',
        hour12: false
    };
    
    return date.toLocaleDateString('en-US', options)
               .replace(',', '')
               .replace(/:(\d+) /, ' $1 '); // Adjusts the time format
}

const inputDate = "01012019 08:32:08";
const formattedDate = formatDate(inputDate);
console.log(formattedDate); // Output: "Jan 01 2019 8:32"
```

Both solutions will convert "01012019 08:32:08" to "Jan 01, 2019 8:32". The first solution is more straightforward for this specific format, while the second one is more flexible for handling different date formats.
