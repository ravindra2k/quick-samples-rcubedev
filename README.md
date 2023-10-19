# quick-samples-rcubedev
Quick Samples

### Whatsapp Cloud Messaging API Integration;
**OTP Auth Messaging**
```php
function sendWhatsAppMsg($sendTo, $action, $params)
    {
        $template = getWATemplate($action, $params);
        // dd($template);

        $lang = $template['language'];
        $temp_name = $template['template_name'];
        $temp_id = $template['template_id'];

        $curl = curl_init();
        
        // ToDo: Update API Data Dynamic;
        $apiVersion = 'v17.0';
        $apiSenderId = '154391567754698';
        $apiAuthKey = 'EAAPRPZAZCDgBcBO3z8hD7nNZBaJjM7iqPv8ISd41rC2crtCblSDiZC9lfHuS7AKv4oePblPibLZAqsW6ey9Hinw7ilgNVMknnfEkbpiBmqEpI7ohGK8OmeHs7zCKYCIXTA0PUVAZCcZChS2j0ZBNn2y40jPTZB4i2EBiFo5TkX42UDO5Hj4vlo1yAbDEglz1dgq27g3afHyZBdZC9ogYZCNzix8ZD';

        $endpoint = 'https://graph.facebook.com/'.$apiVersion.'/'.$apiSenderId.'/messages';

        $headers = array(
            'Content-Type: application/json',
            'Authorization: Bearer '.$apiAuthKey,
        );

        $body =
        '{
            "messaging_product": "whatsapp",
            "to": "918697034671",
            "type": "template",
            "template": {
                "name": "dummy_otp_request",
                "language": {
                    "code": "en",
                    "policy": "deterministic"
                },
                "components": [
                    {
                        "type": "body",
                        "parameters": [
                            {
                                "type": "text",
                                "text": "08697"
                            }
                        ]
                    },
                    {
                        "type": "button",
                        "sub_type": "url",
                        "index": 0,
                        "parameters": [
                            {
                                "type": "text",
                                "text": "08697"
                            }
                        ]
                    }
                ]
            },

        }';
        
        curl_setopt($curl, CURLOPT_URL, $endpoint);
        curl_setopt($curl, CURLOPT_POST, true);
        curl_setopt($curl, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($curl, CURLOPT_POSTFIELDS, $body);
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);

        $res = curl_exec($curl);
        if(curl_errno($curl)) {
            $error = curl_error($curl);
            dd($error);
            return $error;
        }

        if(!isset($error))
        {
            $response = json_decode($res, true);
            dd($response);
        }
    }
```
