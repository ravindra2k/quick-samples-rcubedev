# quick-samples-rcubedev

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

### Random String Generation
```php
if (!function_exists('getRandomString')) {
    function getRandomString($n)
    {
        $characters = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';
        $randomString = '';

        for ($i = 0; $i < $n; $i++) {
            $index = rand(0, strlen($characters) - 1);
            $randomString .= $characters[$index];
        }

        return $randomString;
    }
}

if (!function_exists('getRandomStringSmall')) {
    function getRandomStringSmall($n)
    {
        $characters = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
        $randomString = '';

        for ($i = 0; $i < $n; $i++) {
            $index = rand(0, strlen($characters) - 1);
            $randomString .= $characters[$index];
        }

        return $randomString;
    }
}
```

### Patch Log
```php
if (!function_exists('patchLog')) {
    function patchLog(
        $refId = null,
        $refName = null,
        $initiator = null,
        $module = null,
        $action = null,
        $beforeValue = null,
        $afterValue = null,
        $remarks = null,
        $logDevice = 'Web'
    ) {
        Log::create([
            'ref_id' => $refId,
            'ref_name' => $refName,
            'initiator' => session('associate_id'),
            'module' => $module,
            'action' => $action,
            'before_value' => $beforeValue,
            'after_value' => $afterValue,
            'remarks' => $remarks,
            'log_ip' => request()->ip(),
            'log_device' => $logDevice,
            'log_agent' => request()->header('User-Agent'),
        ]);
    }
}
```
