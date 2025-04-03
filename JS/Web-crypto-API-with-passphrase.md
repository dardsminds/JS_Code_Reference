# Web Crypto API Encryption and Decryption with Passphrase

Here's a complete example of using the Web Crypto API to encrypt and decrypt data using a passphrase as the key:

```javascript
/**
 * Derive a cryptographic key from a passphrase
 * @param {string} passphrase - The passphrase to use
 * @param {Uint8Array} salt - Random salt for key derivation
 * @returns {Promise<CryptoKey>} Derived key
 */
async function deriveKeyFromPassphrase(passphrase, salt) {
    // Convert passphrase to ArrayBuffer
    const passphraseBuffer = new TextEncoder().encode(passphrase);
    
    // Import the passphrase as a raw key for PBKDF2
    const importedKey = await crypto.subtle.importKey(
        'raw',
        passphraseBuffer,
        { name: 'PBKDF2' },
        false,
        ['deriveKey']
    );
    
    // Derive the key using PBKDF2
    return await crypto.subtle.deriveKey(
        {
            name: 'PBKDF2',
            salt: salt,
            iterations: 100000,
            hash: 'SHA-256'
        },
        importedKey,
        { name: 'AES-GCM', length: 256 },
        false,
        ['encrypt', 'decrypt']
    );
}

/**
 * Encrypt data using a passphrase
 * @param {string} data - Data to encrypt
 * @param {string} passphrase - Passphrase to use for encryption
 * @returns {Promise<Object>} Object containing ciphertext, salt, and iv
 */
async function encryptWithPassphrase(data, passphrase) {
    // Generate a random salt
    const salt = crypto.getRandomValues(new Uint8Array(16));
    
    // Generate a random initialization vector
    const iv = crypto.getRandomValues(new Uint8Array(12));
    
    // Derive key from passphrase
    const key = await deriveKeyFromPassphrase(passphrase, salt);
    
    // Convert data to ArrayBuffer
    const dataBuffer = new TextEncoder().encode(data);
    
    // Encrypt the data
    const ciphertext = await crypto.subtle.encrypt(
        {
            name: 'AES-GCM',
            iv: iv
        },
        key,
        dataBuffer
    );
    
    return {
        ciphertext: new Uint8Array(ciphertext),
        salt: salt,
        iv: iv
    };
}

/**
 * Decrypt data using a passphrase
 * @param {Object} encryptedData - Object containing ciphertext, salt, and iv
 * @param {string} passphrase - Passphrase used for encryption
 * @returns {Promise<string>} Decrypted data
 */
async function decryptWithPassphrase(encryptedData, passphrase) {
    const { ciphertext, salt, iv } = encryptedData;
    
    // Derive key from passphrase
    const key = await deriveKeyFromPassphrase(passphrase, salt);
    
    // Decrypt the data
    const decryptedData = await crypto.subtle.decrypt(
        {
            name: 'AES-GCM',
            iv: iv
        },
        key,
        ciphertext
    );
    
    // Convert ArrayBuffer to string
    return new TextDecoder().decode(decryptedData);
}

// Example usage
async function exampleUsage() {
    const originalText = "This is a secret message!";
    const passphrase = "mySuperSecretPassphrase123";
    
    console.log("Original:", originalText);
    
    // Encrypt the data
    const encrypted = await encryptWithPassphrase(originalText, passphrase);
    console.log("Encrypted:", encrypted);
    
    // Convert to base64 for storage/transmission
    const encryptedForStorage = {
        ciphertext: btoa(String.fromCharCode(...encrypted.ciphertext)),
        salt: btoa(String.fromCharCode(...encrypted.salt)),
        iv: btoa(String.fromCharCode(...encrypted.iv))
    };
    console.log("For storage:", encryptedForStorage);
    
    // Convert back from base64
    const encryptedForDecryption = {
        ciphertext: new Uint8Array([...atob(encryptedForStorage.ciphertext)].map(c => c.charCodeAt(0))),
        salt: new Uint8Array([...atob(encryptedForStorage.salt)].map(c => c.charCodeAt(0))),
        iv: new Uint8Array([...atob(encryptedForStorage.iv)].map(c => c.charCodeAt(0)))
    };
    
    // Decrypt the data
    const decryptedText = await decryptWithPassphrase(encryptedForDecryption, passphrase);
    console.log("Decrypted:", decryptedText);
}

// Run the example
exampleUsage().catch(console.error);
```

## Key Points:

1. **Key Derivation**: Uses PBKDF2 to derive a secure cryptographic key from the passphrase
2. **Encryption**: Uses AES-GCM (256-bit) for authenticated encryption
3. **Security Parameters**:
   - Random salt for each encryption (prevents rainbow table attacks)
   - Random IV for each encryption
   - 100,000 iterations for PBKDF2 (can be increased for more security)
4. **Storage**: Shows how to convert binary data to/from base64 for storage

## Usage Notes:

- The same passphrase must be used for decryption
- The salt and IV must be stored/transmitted along with the ciphertext (they're not secret)
- In a real application, you might want to adjust the PBKDF2 iterations based on your performance/security needs
- This code works in modern browsers that support the Web Crypto API
