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

### Working with Laravel Excel Package;
Laravel Excel - [Docs](https://docs.laravel-excel.com/3.1/getting-started/)

```php
<?php

namespace App\Imports;

use App\Models\Pincode;
use App\Models\RewardCategory;
use App\Models\RewardPartner;
use Maatwebsite\Excel\Concerns\ToModel;
use Maatwebsite\Excel\Concerns\WithStartRow;

class RewardPartnersImport implements ToModel, WithStartRow
{
    /**
    * @param array $row
    *
    * @return \Illuminate\Database\Eloquent\Model|null
    */
    private $ucode;
    private $rows = 0;

    public function __construct() {
        $this->ucode = 'RRP'.date("y");
    }

    public function startRow(): int
    {
        return 2;
    }

    public function model(array $row)
    {
        // Model Insertion;
        ++$this->rows;

        // UUID->
        $rp = RewardPartner::orderBy('id', 'DESC')->limit(1)->first();
        if(empty($rp)) {
            $new_number = "0001";
            $uuid = $this->ucode.$new_number;
        } else {
            $rp_uuid = $rp['uuid'];
            $ain_num = substr($rp_uuid, 5);
            // $ain_num = explode($curr_ucode, $rp_uuid);
            $new_number = str_pad($ain_num+1,4,"0",STR_PAD_LEFT);
            $uuid = $this->ucode.$new_number;
        }
        
        //Rows;
        $pincode = $row[0];
        $category = $row[1];
        $brand_title = $row[2];
        $address = $row[3];
        $phone = $row[4];
        $phone2 = $row[5];
        $email = $row[6];
        $website = $row[8];


        // Find Pincode_ID;
        $pc = Pincode::where('pincode', $pincode)->first();
        
        // Find Reward_Category ID;
        $cat= RewardCategory::where('title', $category)->first();
        if($cat) {
            $cat_id = $cat->id; 
        } else {
            $new_cat = RewardCategory::create([
                'title' => $category,
                'parent' => null,
                'published' => 'Yes',
            ]);

            $cat_id = $new_cat->id;
        }

        // Insert Record;
        return new RewardPartner([
            'uuid' => $uuid,
            'reward_category' => $cat_id,
            'pincode' => $pc->id ?? 0,
            'brand_name' => $brand_title,
            'phone_number' => $phone,
            'contact_number' => $phone2,
            'address' => $address,
            'logo' => null,
            'email' => $email,
            'website' => $website,
            'gstin' => null,
            'published' => 'Yes',
            'created_by' => session('associate_id'),
            'created_at' => now(),
            'tagged_by' => null,
            'tagged_on' => null,
            'onboarded_by' => null,
            'onboarded_on' => null,
            'onboard_type' => null,
            'is_sponsored' => 'No'             
        ]);
    }

    public function getRowCount(): int
    {
        return $this->rows;
    }
}
```

### Custom Filtering using Datatables;

```html
<div id="typeFilter-cont" class="col-md-3 mb-3">
    <label for="" class="form-label">Filter Partner Type</label>
    <select name="typeFilter" id="typeFilter" class="select2 form-select form-control border">
        <option value="">All Partners</option>
        // Types for Filtering;
        @foreach ($partnerTypes as $type)
            <option value="{{ $type->title }}">{{ $type->title }}</option>
        @endforeach
    </select>
</div>
```

```js
$(document).ready(function() {
        $('#myDataTable').dataTable({
            "searching": true
        });

        var table = $('#myDataTable').DataTable();
        
        var typeIndex = 0;
        $("#myDataTable th").each(function (i) {
            console.log($($(this)).html());
            if ($($(this)).html() == "Partner Type") {
                typeIndex = i; return false;
            }
        });

        $.fn.dataTable.ext.search.push(
            function (settings, data, dataIndex) {
            var selectedItem = $('#typeFilter').val()
            var type = data[typeIndex];
            if (selectedItem === "" || type.includes(selectedItem)) {
                return true;
            }
            return false;
            }
        );


        $('#typeFilter').change(function (e) {
            table.draw();
        });

        table.draw();
});
```
