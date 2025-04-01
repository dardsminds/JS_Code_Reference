# Decrypting Web Crypto API Data in PHP Backend

Here's how to decrypt data in PHP that was encrypted using the Web Crypto API in JavaScript. This assumes you're using AES-GCM encryption as shown in the previous examples.

## PHP Decryption Code

```php
<?php
function decryptData($encryptedDataBase64, $keyBase64) {
    // Decode the base64 strings
    $combined = base64_decode($encryptedDataBase64);
    $key = base64_decode($keyBase64);
    
    // Extract IV (first 12 bytes) and ciphertext (the rest)
    $iv = substr($combined, 0, 12);
    $ciphertext = substr($combined, 12);
    
    // Decrypt using AES-256-GCM
    $decrypted = openssl_decrypt(
        $ciphertext,
        'aes-256-gcm',
        $key,
        OPENSSL_RAW_DATA,
        $iv
    );
    
    if ($decrypted === false) {
        throw new Exception('Decryption failed: ' . openssl_error_string());
    }
    
    return $decrypted;
}

// Example usage
try {
    // These values would come from your frontend
    $encryptedFromJS = "YOUR_ENCRYPTED_BASE64_FROM_JS"; // Replace with actual encrypted data
    $manualKeyBase64 = "qBS9uRn+dXg5r6Px3JivAe1RDXpA1Xh5eMV4e6bH0D0="; // Same key as used in JS
    
    $decrypted = decryptData($encryptedFromJS, $manualKeyBase64);
    echo "Decrypted: " . $decrypted;
} catch (Exception $e) {
    echo "Error: " . $e->getMessage();
}
?>
```

## Complete JavaScript-to-PHP Example

Here's the full flow from JavaScript encryption to PHP decryption:

### 1. JavaScript Encryption (Frontend)
```javascript
// (Use the same Web Crypto API code from previous examples)

(async function() {
    try {
        const manualKeyBase64 = "qBS9uRn+dXg5r6Px3JivAe1RDXpA1Xh5eMV4e6bH0D0=";
        const key = await importKey(manualKeyBase64);
        
        const originalData = "This is a secret message!";
        const encrypted = await encryptData(originalData, key);
        
        // Send this to your PHP backend
        console.log("Data to send to PHP:", encrypted);
        
        // Example using fetch API:
        const response = await fetch('decrypt.php', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ encryptedData: encrypted })
        });
        
        const result = await response.json();
        console.log("PHP response:", result);
        
    } catch (error) {
        console.error("Error:", error);
    }
})();
```

### 2. PHP Decryption (Backend) - decrypt.php
```php
<?php
header('Content-Type: application/json');

try {
    // Get input data
    $input = json_decode(file_get_contents('php://input'), true);
    $encryptedData = $input['encryptedData'] ?? null;
    
    if (!$encryptedData) {
        throw new Exception('No encrypted data received');
    }
    
    // Your secret key (must match the JavaScript key)
    $manualKeyBase64 = "qBS9uRn+dXg5r6Px3JivAe1RDXpA1Xh5eMV4e6bH0D0=";
    
    // Decrypt the data
    $decrypted = decryptData($encryptedData, $manualKeyBase64);
    
    // Return the decrypted data
    echo json_encode([
        'success' => true,
        'decrypted' => $decrypted
    ]);
    
} catch (Exception $e) {
    http_response_code(400);
    echo json_encode([
        'success' => false,
        'error' => $e->getMessage()
    ]);
}

function decryptData($encryptedDataBase64, $keyBase64) {
    $combined = base64_decode($encryptedDataBase64);
    $key = base64_decode($keyBase64);
    
    $iv = substr($combined, 0, 12);
    $ciphertext = substr($combined, 12);
    $tag = substr($ciphertext, -16); // GCM tag is last 16 bytes
    $ciphertext = substr($ciphertext, 0, -16);
    
    $decrypted = openssl_decrypt(
        $ciphertext,
        'aes-256-gcm',
        $key,
        OPENSSL_RAW_DATA,
        $iv,
        $tag
    );
    
    if ($decrypted === false) {
        throw new Exception('Decryption failed: ' . openssl_error_string());
    }
    
    return $decrypted;
}
?>
```

## Important Notes:

1. **Key Management**: The key must be identical on both JavaScript and PHP sides. Store it securely in your backend.

2. **IV Handling**: The JavaScript code prepends the IV to the ciphertext, and the PHP code extracts it.

3. **GCM Tag**: In AES-GCM, the authentication tag is important for security. The updated PHP code properly handles the tag.

4. **Error Handling**: Both sides should implement proper error handling for production use.

5. **Security Considerations**:
   - Always use HTTPS for transmission
   - Consider adding additional authentication mechanisms
   - Never expose your decryption key to the client

6. **PHP Requirements**: Your PHP installation must have OpenSSL support enabled.

Would you like me to modify any part of this implementation or explain any aspect in more detail?
