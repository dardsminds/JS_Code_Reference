# Web Crypto API: Encrypt and Decrypt Example

Here's a complete example of how to use the Web Crypto API to encrypt and decrypt data in JavaScript:

```javascript
// Helper function to convert ArrayBuffer to Base64
function arrayBufferToBase64(buffer) {
    return btoa(String.fromCharCode(...new Uint8Array(buffer)));
}

// Helper function to convert Base64 to ArrayBuffer
function base64ToArrayBuffer(base64) {
    const binaryString = atob(base64);
    const bytes = new Uint8Array(binaryString.length);
    for (let i = 0; i < binaryString.length; i++) {
        bytes[i] = binaryString.charCodeAt(i);
    }
    return bytes.buffer;
}

// Generate a crypto key (or import one if you already have it)
async function generateKey() {
    return await window.crypto.subtle.generateKey(
        {
            name: "AES-GCM",
            length: 256, // can be 128, 192, or 256
        },
        true, // whether the key is extractable
        ["encrypt", "decrypt"]
    );
}

// Encrypt data
async function encryptData(secretData, key) {
    // Generate an initialization vector (IV)
    const iv = window.crypto.getRandomValues(new Uint8Array(12));
    
    const encrypted = await window.crypto.subtle.encrypt(
        {
            name: "AES-GCM",
            iv: iv,
            tagLength: 128, // can be 32, 64, 96, 104, 112, 120, or 128
        },
        key,
        new TextEncoder().encode(secretData)
    );
    
    // Combine IV and encrypted data for storage/transmission
    const combined = new Uint8Array(iv.length + encrypted.byteLength);
    combined.set(iv, 0);
    combined.set(new Uint8Array(encrypted), iv.length);
    
    return arrayBufferToBase64(combined.buffer);
}

// Decrypt data
async function decryptData(encryptedData, key) {
    const combined = base64ToArrayBuffer(encryptedData);
    
    // Extract IV and encrypted data
    const iv = combined.slice(0, 12);
    const data = combined.slice(12);
    
    const decrypted = await window.crypto.subtle.decrypt(
        {
            name: "AES-GCM",
            iv: iv,
            tagLength: 128,
        },
        key,
        data
    );
    
    return new TextDecoder().decode(decrypted);
}

// Example usage
(async function() {
    try {
        // Generate a key
        const key = await generateKey();
        
        // Original data
        const originalData = "This is a secret message!";
        console.log("Original:", originalData);
        
        // Encrypt the data
        const encrypted = await encryptData(originalData, key);
        console.log("Encrypted:", encrypted);
        
        // Decrypt the data
        const decrypted = await decryptData(encrypted, key);
        console.log("Decrypted:", decrypted);
        
    } catch (error) {
        console.error("Error:", error);
    }
})();
```

## Important Notes:

1. **Key Management**: In this example, the key is generated each time. In a real application, you would need to securely store and manage the key.

2. **IV (Initialization Vector)**: Always use a unique IV for each encryption operation. Never reuse IVs with the same key.

3. **Error Handling**: The Web Crypto API can throw exceptions for various reasons (invalid key, corrupted data, etc.), so proper error handling is essential.

4. **Browser Support**: The Web Crypto API is widely supported in modern browsers, but not in very old ones.

5. **Security Considerations**:
   - Never hardcode keys in your JavaScript
   - Use HTTPS for all crypto operations
   - Consider additional security measures for production applications

Would you like me to explain any specific part in more detail or show a different encryption algorithm?
