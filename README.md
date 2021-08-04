# Query Caching Laravel

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use App\Traits\ThridPartyApi;

class ExampleController extends Controller {

    use ThirdPartyApi;

    public function test(Request $request)
    {
        $result = $this->getAll();

        return view('test',compact('result'));
    }
}
```

* App\Traits\ThridPartyApi.php

```php
<?php

namespace App\Traits;

use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Cache;

trait ThirdPartyApi {

    public function getAll()
    {
        $client = new \GuzzleHttp\Client();
        $result = Cache::remember('output', now()->addDays(1), function () use ($client) {
            $request = $client->get(config('app.other_api_url') . 'sub-url', [
                'json'=>[
                    'key' => config('app.sub_api_key')
                ],
                'headers' => ['APPKEY' => config('app.other_api_key')]
            ]);
            return (json_decode($request->getBody()));
        });
        return $result;
    }
}
```
