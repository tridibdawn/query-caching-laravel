# Query Caching Laravel

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;
use Illuminate\Support\Facades\Cache;

class ExampleController extends Controller {

    public function test(Request $request)
    {
        $result = $this->getAll();

        return view('test',compact('result'));
    }
    
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
