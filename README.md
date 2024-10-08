# Steganography

### Encrypt text messages inside images  
**IMAGE-BASED TEXT ENCODING AND DECODING**

This project demonstrates how to encode a string of text into an image's pixel data using Least Significant Bit (LSB) Steganography and later decode that text from the modified image. The encoding is performed by manipulating the least significant bits of the RGB values of an image's pixels to store the binary representation of ASCII characters.

## Features
- **Text Encoding**: Convert a string of up to 50 characters into binary and embed it within the image's pixel data.
- **Text Decoding**: Extract the binary data from the image and convert it back into readable text.
- **Downloadable Image**: After encoding the text, you can download the modified image.

## Process Overview
The process involves converting each character of the string into its binary representation and embedding that binary data into the image's pixel data. The decoding process reverses this to recover the original string.

---

### Encoding Process

1. **Input Handling**  
   The encoding process begins by accepting a string input from the user. The input is limited to 50 characters, and if the string is shorter, it is padded with null characters (`\0`).

   **Key Code Snippet (JavaScript):**

   ```javascript
   textForm.addEventListener('submit', function(event) {
       event.preventDefault();
       textToEncode = document.getElementById('textToEncode').value;

       // Pad the text with null characters if it's shorter than 50 characters
       while (textToEncode.length < 50) {
           textToEncode += String.fromCharCode(0); // Add null characters
       }
   });
   ```

   **Null Padding**: This ensures that the message is always 50 characters long by appending null characters (ASCII code 0). The decoder interprets these null characters as the end of the message.

2. **Binary Conversion**  
   Each character of the input string is converted into its ASCII value and transformed into an 8-bit binary representation.

   **Key Code Snippet (JavaScript):**

   ```javascript
   const messageBinary = textToEncode.split('').map(char => {
       const binary = char.charCodeAt(0).toString(2).padStart(8, '0');
       return binary;
   }).join('');
   ```

   **Explanation**: Each character is converted to its ASCII code using `charCodeAt(0)` and then converted to an 8-bit binary string using `toString(2)`. The `padStart(8, '0')` ensures that each binary string is exactly 8 bits long.

3. **Modifying Pixel Data**  
   Once the binary representation of the text is ready, we embed it into the image's pixel data by modifying the least significant bits of the Red, Green, and Blue channels.

   **Key Code Snippet (JavaScript):**

   ```javascript
   for (let i = 0, bitIndex = 0; i < pixels.length && bitIndex < messageBinary.length; i += 4) {
       // Red channel
       pixels[i] = (pixels[i] & ~1) | parseInt(messageBinary[bitIndex], 2);
       bitIndex++;
       // Green channel
       if (bitIndex < messageBinary.length) {
           pixels[i + 1] = (pixels[i + 1] & ~1) | parseInt(messageBinary[bitIndex], 2);
           bitIndex++;
       }
       // Blue channel
       if (bitIndex < messageBinary.length) {
           pixels[i + 2] = (pixels[i + 2] & ~1) | parseInt(messageBinary[bitIndex], 2);
           bitIndex++;
       }
   }
   ```

   **LSB Encoding**: The least significant bit of each RGB channel is replaced by one bit from the binary message. For example, if a pixel's Red value is `10101010` (in binary) and the next message bit is `1`, it becomes `10101011`.

4. **Canvas and Image Manipulation**  
   The modified pixel data is placed back into the image, and the user is given the option to download the modified image with the encoded message.

   ```javascript
   ctx.putImageData(imageData, 0, 0);
   ```

---

### Decoding Process

The decoding process extracts the binary data from the image’s pixel data and converts it back into readable text.

1. **Extracting Binary Data**  
   The process reads the least significant bits of the Red, Green, and Blue channels of each pixel to retrieve the embedded binary message.

   **Key Code Snippet (JavaScript):**

   ```javascript
   for (let i = 0; i < pixels.length; i += 4) {
       // Extract LSB from Red, Green, and Blue channels
       binaryMessage += (pixels[i] & 1).toString();
       binaryMessage += (pixels[i + 1] & 1).toString();
       binaryMessage += (pixels[i + 2] & 1).toString();
       
       // Stop after 400 bits (50 characters)
       if (binaryMessage.length >= 400) break;
   }
   ```

   **LSB Extraction**: The least significant bit of each color channel is extracted and concatenated into a string of bits.

2. **Binary to Text Conversion**  
   The binary string is split into groups of 8 bits and converted back into ASCII characters.

   **Key Code Snippet (JavaScript):**

   ```javascript
   for (let i = 0; i < binaryMessage.length; i += 8) {
       const byte = binaryMessage.slice(i, i + 8);
       const charCode = parseInt(byte, 2);
       if (charCode === 0) break; // Stop at null character
       decodedMessage += String.fromCharCode(charCode);
   }
   ```

   **Null Character Handling**: The loop breaks when it encounters a null character (`ASCII 0`), marking the end of the encoded message.

---

## How to Run the Project

### Encoding Process:
1. Open the encoding HTML page.
2. Enter a message of up to 50 characters.
3. Upload an image.
4. Download the modified image with the encoded message.

### Decoding Process:
1. Open the decoding HTML page.
2. Upload the image with the encoded text.
3. The decoded message will be displayed.

---

### Example

1. **Input Text**: `"Hello World"`
2. **Binary Conversion**:
   - ASCII for `"H"` is `72`, which is `01001000` in binary.
   - ASCII for `"e"` is `101`, which is `01100101` in binary.
   - The full message `"Hello World"` is converted into a binary string.
3. **LSB Encoding**:  
   The first 8 bits (`01001000`) are embedded into the first few pixels' RGB values using the LSB technique.
4. **Decoding**:  
   The image is loaded, and the LSBs are extracted to reconstruct the binary string.  
   The binary string is converted back into the original characters `"Hello World"`.

---

## Limitations
- The text length is limited to 50 characters (400 bits) in this implementation.
- The process depends on image size and the number of pixels available to encode the message.

---

## Conclusion
This project demonstrates a simple yet powerful way to hide messages within an image by manipulating its pixel data. By using the Least Significant Bit (LSB) technique, the visual changes to the image are imperceptible, making this an effective method for image-based steganography.

---

This format improves readability and makes the document visually structured and easy to follow. Let me know if you'd like any further adjustments!
