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
        $apiVersion = 'v17.0'; // Your Version Here
        $apiSenderId = '000000000000000'; // Your Sender Id Here;
        $apiAuthKey = ''; // Your AUTH Key Here;

        $endpoint = 'https://graph.facebook.com/'.$apiVersion.'/'.$apiSenderId.'/messages';

        $headers = array(
            'Content-Type: application/json',
            'Authorization: Bearer '.$apiAuthKey,
        );

        $body =
        '{
            "messaging_product": "whatsapp",
            "to": "", // Sender Number;
            "type": "template",
            "template": {
                "name": "dummy_otp_request", // Template Name
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
            'module' => $module
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
            ... // updates here;         
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

### Custom Toast Notification Using Alpine JS & Tailwind

```notifications.blade.php
<div x-on:mouseenter="$dispatch('pause-auto-dismiss')" x-on:mouseleave="$dispatch('resume-auto-dismiss')" class="group pointer-events-none fixed inset-x-8 top-0 z-99 flex max-w-full flex-col gap-2 bg-transparent px-6 py-6 md:bottom-0 md:left-[unset] md:right-0 md:top-[unset] md:max-w-sm">
    <template x-for="(notification, index) in notifications" x-bind:key="notification.id">
        <!-- root div holds all of the notifications  -->    
        <div>
            <!-- Info Notification  -->
            <template x-if="notification.variant === 'info'">                
                <div x-data="{ isVisible: false, timeout: null }" x-cloak x-show="isVisible" class="pointer-events-auto relative rounded-2xl border border-sky-500 bg-white text-neutral-600 dark:bg-neutral-950 dark:text-neutral-300" role="alert" x-on:pause-auto-dismiss.window="clearTimeout(timeout)" x-on:resume-auto-dismiss.window=" timeout = setTimeout(() => {(isVisible = false), removeNotification(notification.id) }, displayDuration)" x-init="$nextTick(() => { isVisible = true }), (timeout = setTimeout(() => { isVisible = false, removeNotification(notification.id)}, displayDuration))" x-transition:enter="transition duration-300 ease-out" x-transition:enter-end="translate-y-0" x-transition:enter-start="translate-y-8" x-transition:leave="transition duration-300 ease-in" x-transition:leave-end="-translate-x-24 opacity-0 md:translate-x-24" x-transition:leave-start="translate-x-0 opacity-100">
                    <div class="flex w-full items-center gap-2.5 bg-sky-500/10 rounded-2xl p-4 transition-all duration-300">

                        <!-- Icon -->
                        <div class="rounded-full bg-sky-500/15 p-0.5 text-sky-500" aria-hidden="true">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="size-5" aria-hidden="true">
                                <path fill-rule="evenodd" d="M18 10a8 8 0 1 1-16 0 8 8 0 0 1 16 0Zm-7-4a1 1 0 1 1-2 0 1 1 0 0 1 2 0ZM9 9a.75.75 0 0 0 0 1.5h.253a.25.25 0 0 1 .244.304l-.459 2.066A1.75 1.75 0 0 0 10.747 15H11a.75.75 0 0 0 0-1.5h-.253a.25.25 0 0 1-.244-.304l.459-2.066A1.75 1.75 0 0 0 9.253 9H9Z" clip-rule="evenodd" />
                            </svg>
                        </div>

                        <!-- Title & Message -->
                        <div class="flex flex-col gap-2">
                            <h3 x-cloak x-show="notification.title" class="text-sm font-semibold text-sky-500" x-text="notification.title"></h3>
                            <p x-cloak x-show="notification.message" class="text-pretty text-sm" x-text="notification.message"></p>
                        </div>

                        <!--Dismiss Button -->
                        <button type="button" class="ml-auto" aria-label="dismiss notification" x-on:click="(isVisible = false), removeNotification(notification.id)">
                            <svg xmlns="http://www.w3.org/2000/svg viewBox="0 0 24 24 stroke="currentColor" fill="none" stroke-width="2" class="size-5 shrink-0" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                            </svg>
                        </button>
                    </div>
                </div>
            </template>

            <!-- Success Notification  -->
            <template x-if="notification.variant === 'success'">                
                <div x-data="{ isVisible: false, timeout: null }" x-cloak x-show="isVisible" class="pointer-events-auto relative rounded-2xl border border-green-500 bg-white text-neutral-600 dark:bg-neutral-950 dark:text-neutral-300" role="alert" x-on:pause-auto-dismiss.window="clearTimeout(timeout)" x-on:resume-auto-dismiss.window=" timeout = setTimeout(() => {(isVisible = false), removeNotification(notification.id) }, displayDuration)" x-init="$nextTick(() => { isVisible = true }), (timeout = setTimeout(() => { isVisible = false, removeNotification(notification.id)}, displayDuration))" x-transition:enter="transition duration-300 ease-out" x-transition:enter-end="translate-y-0" x-transition:enter-start="translate-y-8" x-transition:leave="transition duration-300 ease-in" x-transition:leave-end="-translate-x-24 opacity-0 md:translate-x-24" x-transition:leave-start="translate-x-0 opacity-100">
                    <div class="flex w-full items-center gap-2.5 bg-green-500/10 rounded-2xl p-4 transition-all duration-300">

                        <!-- Icon -->
                        <div class="rounded-full bg-green-500/15 p-0.5 text-green-500" aria-hidden="true">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="size-5" aria-hidden="true">
                                <path fill-rule="evenodd" d="M10 18a8 8 0 1 0 0-16 8 8 0 0 0 0 16Zm3.857-9.809a.75.75 0 0 0-1.214-.882l-3.483 4.79-1.88-1.88a.75.75 0 1 0-1.06 1.061l2.5 2.5a.75.75 0 0 0 1.137-.089l4-5.5Z" clip-rule="evenodd" />
                            </svg>
                        </div>

                        <!-- Title & Message -->
                        <div class="flex flex-col gap-2">
                            <h3 x-cloak x-show="notification.title" class="text-sm font-semibold text-green-500" x-text="notification.title"></h3>
                            <p x-cloak x-show="notification.message" class="text-pretty text-sm" x-text="notification.message"></p>
                        </div>

                        <!--Dismiss Button -->
                        <button type="button" class="ml-auto" aria-label="dismiss notification" x-on:click="(isVisible = false), removeNotification(notification.id)">
                            <svg xmlns="http://www.w3.org/2000/svg viewBox="0 0 24 24 stroke="currentColor" fill="none" stroke-width="2" class="size-5 shrink-0" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                            </svg>
                        </button>
                    </div>
                </div>
            </template>

            <!-- Warning Notification  -->
            <template x-if="notification.variant === 'warning'">                
                <div x-data="{ isVisible: false, timeout: null }" x-cloak x-show="isVisible" class="pointer-events-auto relative rounded-2xl border border-amber-500 bg-white text-neutral-600 dark:bg-neutral-950 dark:text-neutral-300" role="alert" x-on:pause-auto-dismiss.window="clearTimeout(timeout)" x-on:resume-auto-dismiss.window=" timeout = setTimeout(() => {(isVisible = false), removeNotification(notification.id) }, displayDuration)" x-init="$nextTick(() => { isVisible = true }), (timeout = setTimeout(() => { isVisible = false, removeNotification(notification.id)}, displayDuration))" x-transition:enter="transition duration-300 ease-out" x-transition:enter-end="translate-y-0" x-transition:enter-start="translate-y-8" x-transition:leave="transition duration-300 ease-in" x-transition:leave-end="-translate-x-24 opacity-0 md:translate-x-24" x-transition:leave-start="translate-x-0 opacity-100">
                    <div class="flex w-full items-center gap-2.5 bg-amber-500/10 rounded-2xl p-4 transition-all duration-300">

                        <!-- Icon -->
                        <div class="rounded-full bg-amber-500/15 p-0.5 text-amber-500" aria-hidden="true">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="size-5" aria-hidden="true">
                                <path fill-rule="evenodd" d="M18 10a8 8 0 1 1-16 0 8 8 0 0 1 16 0Zm-8-5a.75.75 0 0 1 .75.75v4.5a.75.75 0 0 1-1.5 0v-4.5A.75.75 0 0 1 10 5Zm0 10a1 1 0 1 0 0-2 1 1 0 0 0 0 2Z" clip-rule="evenodd" />
                            </svg>
                        </div>

                        <!-- Title & Message -->
                        <div class="flex flex-col gap-2">
                            <h3 x-cloak x-show="notification.title" class="text-sm font-semibold text-amber-500" x-text="notification.title"></h3>
                            <p x-cloak x-show="notification.message" class="text-pretty text-sm" x-text="notification.message"></p>
                        </div>

                        <!--Dismiss Button -->
                        <button type="button" class="ml-auto" aria-label="dismiss notification" x-on:click="(isVisible = false), removeNotification(notification.id)">
                            <svg xmlns="http://www.w3.org/2000/svg viewBox="0 0 24 24 stroke="currentColor" fill="none" stroke-width="2" class="size-5 shrink-0" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                            </svg>
                        </button>
                    </div>
                </div>
            </template>

            <!-- Danger Notification  -->
            <template x-if="notification.variant === 'danger'">                
                <div x-data="{ isVisible: false, timeout: null }" x-cloak x-show="isVisible" class="pointer-events-auto relative rounded-2xl border border-red-500 bg-white text-neutral-600 dark:bg-neutral-950 dark:text-neutral-300" role="alert" x-on:pause-auto-dismiss.window="clearTimeout(timeout)" x-on:resume-auto-dismiss.window=" timeout = setTimeout(() => {(isVisible = false), removeNotification(notification.id) }, displayDuration)" x-init="$nextTick(() => { isVisible = true }), (timeout = setTimeout(() => { isVisible = false, removeNotification(notification.id)}, displayDuration))" x-transition:enter="transition duration-300 ease-out" x-transition:enter-end="translate-y-0" x-transition:enter-start="translate-y-8" x-transition:leave="transition duration-300 ease-in" x-transition:leave-end="-translate-x-24 opacity-0 md:translate-x-24" x-transition:leave-start="translate-x-0 opacity-100">
                    <div class="flex w-full items-center gap-2.5 bg-red-500/10 rounded-2xl p-4 transition-all duration-300">

                        <!-- Icon -->
                        <div class="rounded-full bg-red-500/15 p-0.5 text-red-500" aria-hidden="true">
                            <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20" fill="currentColor" class="size-5" aria-hidden="true">
                                <path fill-rule="evenodd" d="M18 10a8 8 0 1 1-16 0 8 8 0 0 1 16 0Zm-8-5a.75.75 0 0 1 .75.75v4.5a.75.75 0 0 1-1.5 0v-4.5A.75.75 0 0 1 10 5Zm0 10a1 1 0 1 0 0-2 1 1 0 0 0 0 2Z" clip-rule="evenodd" />
                            </svg>
                        </div>

                        <!-- Title & Message -->
                        <div class="flex flex-col gap-2">
                            <h3 x-cloak x-show="notification.title" class="text-sm font-semibold text-red-500" x-text="notification.title"></h3>
                            <p x-cloak x-show="notification.message" class="text-pretty text-sm" x-text="notification.message"></p>
                        </div>

                        <!--Dismiss Button -->
                        <button type="button" class="ml-auto" aria-label="dismiss notification" x-on:click="(isVisible = false), removeNotification(notification.id)">
                            <svg xmlns="http://www.w3.org/2000/svg viewBox="0 0 24 24 stroke="currentColor" fill="none" stroke-width="2" class="size-5 shrink-0" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                            </svg>
                        </button>
                    </div>
                </div>
            </template>
        
            <!-- Message Notification  -->
            <template x-if="notification.variant === 'message'">                
                <div x-data="{ isVisible: false, timeout: null }" x-cloak x-show="isVisible" class="pointer-events-auto relative rounded-2xl border border-neutral-300 bg-white text-neutral-600 dark:border-neutral-700 dark:bg-neutral-950 dark:text-neutral-300" role="alert" x-on:pause-auto-dismiss.window="clearTimeout(timeout)" x-on:resume-auto-dismiss.window="timeout = setTimeout(() => { isVisible = false, removeNotification(notification.id) }, displayDuration)" x-init="$nextTick(() => { isVisible = true }), (timeout = setTimeout(() => { isVisible = false, removeNotification(notification.id) }, displayDuration))" x-transition:enter="transition duration-300 ease-out" x-transition:enter-end="translate-y-0" x-transition:enter-start="translate-y-8" x-transition:leave="transition duration-300 ease-in" x-transition:leave-end="-translate-x-24 opacity-0 md:translate-x-24" x-transition:leave-start="translate-x-0 opacity-100">
                    <div class="flex w-full rounded-2xl items-center gap-2.5 bg-neutral-50 p-4 transition-all duration-300 dark:bg-neutral-900">
                        <div class="flex w-full items-center gap-2.5">
                                
                            <!-- Avatar -->    
                            <img x-cloak x-show="notification.sender.avatar" class="mr-2 size-12 rounded-full" alt="avatar" aria-hidden="true" x-bind:src="notification.sender.avatar"/>
                            <div class="flex flex-col items-start gap-2">
                                <!-- Title & Message -->     
                                <h3 x-cloak x-show="notification.sender.name" class="text-sm font-semibold text-neutral-900 dark:text-white" x-text="notification.sender.name"></h3>
                                <p x-cloak x-show="notification.message" class="text-pretty text-sm" x-text="notification.message"></p>

                                <!-- Action Buttons -->
                                <div class="flex items-center gap-4">
                                    <button type="button" class="whitespace-nowrap bg-transparent text-center text-sm font-bold tracking-wide text-black transition hover:opacity-75 active:opacity-100 dark:text-white">Reply</button>
                                    <button type="button" class="whitespace-nowrap bg-transparent text-center text-sm font-bold tracking-wide text-neutral-600 transition hover:opacity-75 active:opacity-100 dark:text-neutral-300" x-on:click=" (isVisible = false), setTimeout(() => { removeNotification(notification.id) }, 400)">Dismiss</button>
                                </div>
                            </div>
                        </div>

                        <!-- Dismiss Button -->
                        <button type="button" class="ml-auto" aria-label="dismiss notification" x-on:click="(isVisible = false), removeNotification(notification.id)">
                            <svg xmlns="http://www.w3.org/2000/svg viewBox="0 0 24 24 stroke="currentColor" fill="none" stroke-width="2" class="size-5 shrink-0" aria-hidden="true">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M6 18L18 6M6 6l12 12"/>
                            </svg>
                        </button>
                    </div>
                </div>
            </template>

        </div>
    </template>
</div>
```

This Notification should extends in you layout master, or in main html file; where `body` is set to relative.

```js
// Alpine Component for Notification
    document.addEventListener('alpine:init', () => {
        Alpine.data('registerNotification', () => ({
            notifications: [],
            displayDuration: 8000,
            soundEffect: false,
            addNotification({ variant = 'info', sender = null, title = null, message = null}) {
                const id = Date.now()
                const notification = { id, variant, sender, title, message }

                // Keep only the most recent 20 notifications
                if (this.notifications.length >= 20) {
                    this.notifications.splice(0, this.notifications.length - 19)
                }

                // Add the new notification to the notifications stack
                this.notifications.push(notification)

                if (this.soundEffect) {
                    // Play the notification sound
                    const notificationSound = new Audio('https://res.cloudinary.com/ds8pgw1pf/video/upload/v1728571480/penguinui/component-assets/sounds/ding.mp3')
                    notificationSound.play().catch((error) => {
                        console.error('Error playing the sound:', error)
                    })
                }
            },
            removeNotification(id) {
                setTimeout(() => {
                    this.notifications = this.notifications.filter(
                        (notification) => notification.id !== id,
                    )
                }, 400);
            },
        }));
    });

    async function notifyUser(variant = "info", title = null, message = null, duration = null) {
        window.dispatchEvent(new CustomEvent('notify', {
            detail: {
                variant: variant,
                title: title,
                message: message 
            }
        }))
    }
```

Now a common function `notifyUser` can be used;
**Use Case**

````js
notifyUser('info', 'Just a Information', 'Github is just awesome');
notifyUser('success', 'Action Success', 'Customer Data has been updated');
notifyUser('warning', '1 step to go.', 'Please solve the captcha');
notifyUser('danger', 'Action Failed', 'Internal Server Error');
```

### Files Structure in Node JS, Express Application

```
├── src/
│   ├── config/        # Configuration files (database, environment variables)
│   ├── controllers/   # Handle HTTP requests (e.g., UserController.ts)
│   ├── models/        # Sequelize models (database schema definitions)
│   ├── middlewares/   # Custom middlewares (auth, logging, validation)
│   ├── routes/        # API route definitions
│   ├── services/      # Business logic (separating from controllers)
│   ├── utils/         # Helper functions
│   ├── server.ts      # Main entry point
│   ├── public/        # For Serving Static Files (e.g., images, CSS, JS)
│
├── dist/              # Compiled TypeScript files
├── node_modules/      # Dependencies
├── .env               # Environment variables (excluded in .gitignore)
├── package.json       # Project dependencies and scripts
├── tsconfig.json      # TypeScript configuration
├── .gitignore         # Ignored files
└── documentation.md   # Project documentation
```
