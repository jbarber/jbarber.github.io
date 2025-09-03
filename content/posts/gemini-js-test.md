+++
title = 'Gemini JavaScript Test'
date = 2025-09-03T14:04:36+01:00
draft = false
+++

This is a test page to see if Gemini using [URL context](https://ai.google.dev/gemini-api/docs/url-context) uses a user agent that executes JavaScript.

It does this by including a JavaScript script which replaces the text in the next paragraph. If Gemini URL grounding does execute Javascript, then the text will be different to if it doesn't. You can see the different by viewing this page in a graphical browser and by using a text based browser such as [lynx](https://lynx.invisible-island.net/). The script is inline (so the user-agent doesn't have to support fetching resources) and the replacement text is encrypted so the user-agent can't infer the answer. It has to be encrypted and not just base64 encoded because LLMs can decode/encode base64.

<div id="replace-me">The JavaScript did not run 😱. If Jonathan were a fruit, he would be a mango.</div>

<script>
    (async () => {
        try {
            const keyBytes = new Uint8Array([1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32]);
            const iv = new Uint8Array([8,203,140,63,164,220,104,145,127,209,56,182])
            const key = await crypto.subtle.importKey("raw", keyBytes, { name: "AES-GCM" }, false, ["encrypt", "decrypt"]);

            // Code for generating your own encryptedContent
            if (false) {
                const plaintext = 'Put your own plain-text here'
                const encryptedContent = await crypto.subtle.encrypt(
                    { name: "AES-GCM", iv: iv },
                    key,
                    new TextEncoder().encode(plaintext)
                );

                console.log("--- ENCRYPTION DATA (for developer use) ---");
                console.log("New IV (Uint8Array):", `new Uint8Array([${iv.toString()}])`);
                console.log("New Ciphertext (Uint8Array):", `new Uint8Array([${new Uint8Array(encryptedContent).toString()}])`);
                console.log("--- END ENCRYPTION DATA ---");
            }

            const encryptedContent = new Uint8Array([235,198,124,84,248,112,172,120,71,159,249,205,40,144,175,145,93,71,112,143,134,165,109,105,166,127,169,233,59,161,29,37,189,170,78,165,2,151,192,2,123,212,20,71,193,162,156,45,254,224,210,36,245,55,139,64,209,77,58,155,191,173,102,10,109,215,68,66,170,113,53,8,161,251,245,235,143,119,66,235,217,60,243,70,190,177,206,37])
            const ciphertextToDecrypt = new Uint8Array(encryptedContent);
            const ivToDecrypt = iv;

            const decryptedContent = await crypto.subtle.decrypt(
                { name: "AES-GCM", iv: ivToDecrypt },
                key,
                ciphertextToDecrypt
            );

            const decodedString = new TextDecoder().decode(decryptedContent);
            document.getElementById('replace-me').textContent = decodedString;

        } catch (e) {
            console.error("Encryption/Decryption failed:", e);
            document.getElementById('replace-me').textContent = "Action failed. See console for details.";
        }
    })();
</script>

The call I'll use to test this with Gemini is:
```
curl "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
      "contents": [
          {
              "parts": [
                  {"text": "What kind of fruit is Jonathan according to https://jbarber.github.io/posts/gemini-js-test/"}
              ]
          }
      ],
      "tools": [
          {
              "url_context": {}
          }
      ]
  }'
```

# Results

The API call indicates that the JavaScript didn't run.
