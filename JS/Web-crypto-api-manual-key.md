# Web Crypto API with Manual Key Specification

Here's an updated version of the code where you can manually specify the encryption key (as a base64 string):

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

// Import a manually specified key (in Base64 format)
async function importKey(base64Key) {
    // Convert Base64 key to ArrayBuffer
    const keyBuffer = base64ToArrayBuffer(base64Key);
    
    return await window.crypto.subtle.importKey(
        "raw",
        keyBuffer,
        {
            name: "AES-GCM",
            length: 256,
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
            tagLength: 128,
        },
        key,
        new TextEncoder().encode(secretData)
    );
    
    // Combine IV and encrypted data
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

// Example usage with manual key
(async function() {
    try {
        // Manually specified key (256-bit AES key in Base64 format)
        // This should be a 44-character Base64 string (32 bytes when decoded)
        const manualKeyBase64 = "qBS9uRn+dXg5r6Px3JivAe1RDXpA1Xh5eMV4e6bH0D0=";
        
        // Import the key
        const key = await importKey(manualKeyBase64);
        
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

## How to Generate a Manual Key

If you need to generate a valid AES-256 key in Base64 format, you can use this code:

```javascript
// Generate a key and get its Base64 representation
async function generateAndExportKey() {
    const key = await window.crypto.subtle.generateKey(
        {
            name: "AES-GCM",
            length: 256,
        },
        true, // extractable
        ["encrypt", "decrypt"]
    );
    
    const exported = await window.crypto.subtle.exportKey("raw", key);
    const base64Key = arrayBufferToBase64(exported);
    console.log("Your Base64 key:", base64Key);
    return base64Key;
}

// Run this once to get a key you can use in the manual example
// generateAndExportKey().then(key => console.log("Use this key:", key));
```

## Important Security Notes:

1. **Key Security**: Never hardcode keys in production code. This example is for demonstration only.

2. **Key Storage**: In a real application, store keys securely (environment variables, secure key management systems).

3. **Key Generation**: Always use cryptographically secure methods to generate keys.

4. **Key Length**: The example uses AES-256 (32 bytes). Make sure your manual key is the correct length.

Would you like me to modify any part of this implementation or explain any aspect in more detail?
