# quick-samples-rcubedev
Quick Samples

### Whatsapp Cloud Messaging API Integration;
**OTP Requests**
```php
// POST Body
{
  "name": "<NAME>",
  "language": "<LANGUAGE>",
  "category": "AUTHENTICATION",
  "message_send_ttl_seconds": <TIME_T0_LIVE>, // Optional
  "components": [
    {
      "type": "BODY", 
      "add_security_recommendation": <ADD_SECURITY_RECOMMENDATION> // Optional property
    },
    {
      "type": "FOOTER", 
      "code_expiration_minutes": <CODE_EXPIRATION_MINUTES> // Optional property
    },
    { 
      "type": "BUTTONS",
      "buttons": [
        {
          "type": "OTP",
          "otp_type": "<OTP_TYPE>",
          "text": "<TEXT>",  // Optional property
          "autofill_text": "<AUTOFILL_TEXT>", // Optional property, one-tap buttons only
          "package_name": "<PACKAGE_NAME>", // One-tap buttons only
          "signature_hash": "<SIGNATURE_HASH>" // One-tap buttons only
        }
      ]
    }
  ]
}
```
