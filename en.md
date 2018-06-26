
[Source](http://docs.guzzlephp.org/en/stable/quickstart.html "Permalink to Quickstart — Guzzle Documentation")

# Bắt đầu nhanh — Guzzle Documentation

Trang này cung cấp một phần giới thiệu ngắn về Guzzle và giới thiệu luôn về các ví dụ. Nếu bạn chưa từng cài đặt Guzzle trước đó, hãy xem qua trang [cài đặt][1].

## Tạo một Request

Bạn có thể gửi các request với Guzzle bằng cách sử dụng một đối tượng `GuzzleHttpClientInterface`.

### Tạo ra một Client
    
    
    use GuzzleHttpClient;
    
    $client = new Client([
        // Base URI is used with relative requests
        'base_uri' => 'http://httpbin.org',
        // You can set any number of default request options.
        'timeout'  => 2.0,
    ]);
    

Các Client trong Guzzle 6 không thể thay đổi, có nghĩa mà bạn không thể chỉnh sửa các mặc định được sử dụng bởi client sau khi nó được tạo ra.

Phần khởi tạo client chấp nhận một mảng các tuỳ chọn liên kết:


### `base_uri:` 

(string|UriInterface) Base URI của một client sẽ kết hợp với các URI tương đối. Có thể là một string hoặc đại diện của UriInterface. Khi một URI tương đối được cung cấp tới client, client sẽ kết hợp base URI với URI tương đối bằng cách sử dụng các quy tắc được mô tả trong [RFC 3986, section 2][2].
    
    
    // Tạo một client với một base URI
    $client = new GuzzleHttpClient(['base_uri' => 'https://foo.com/api/']);
    // gửi một request tới https://foo.com/api/test
    $response = $client->request('GET', 'test');
    // Gửi một request tới https://foo.com/root
    $response = $client->request('GET', '/root');
    

Bạn cảm thấy không thích đọc RFC 3986? Đây là vài ví dụ nhỏ về `base_uri` đã giải quyết với các URI khác như thế nào.

| base_uri              | URI              | Kết quả                   |  
| --------------------- | ---------------- | ------------------------  |  
| `http://foo.com`      | `/bar`           | `http://foo.com/bar`      |  
| `http://foo.com/foo`  | `/bar`           | `http://foo.com/bar`      |  
| `http://foo.com/foo`  | `bar`            | `http://foo.com/bar`      |  
| `http://foo.com/foo/` | `bar`            | `http://foo.com/foo/bar`  |  
| `http://foo.com`      | `http://baz.com` | `http://baz.com`          |  
| `http://foo.com/?bar` | `bar`            | `http://foo.com/bar`      |  

### `handler:`
 (Có thể gọi) Tính năng gửi các yêu cầu HTTP thông qua mạng điện. Function này được gọi qua một `Psr7HttpMessageRequestInterface` và mảng các tuỳ chọn về cách truyền, và phải trả về một `GuzzleHttpPromisePromiseInterface` khi thành công và với một `Psr7HttpMessageResponseInterface` khi hoàn thành . `handler` chỉ là một tuỳ chọn khởi tạo mà không thể bị ghi đè trên mỗi tuỳ chọn request.

`...`
: (mixed) Tất cả các tuỳ chọn khác được truyền cho hàm khởi tạo đều được sử dụng như tuỳ chọn request mặc định đối với mọi request được tạo bởi client.

### Gửi các Requests

Các phương thức magic trên client làm nó gửi các request đồng bộ một cách dễ dàng hơn:
    
    
    $response = $client->get('http://httpbin.org/get');
    $response = $client->delete('http://httpbin.org/delete');
    $response = $client->head('http://httpbin.org/get');
    $response = $client->options('http://httpbin.org/get');
    $response = $client->patch('http://httpbin.org/patch');
    $response = $client->post('http://httpbin.org/post');
    $response = $client->put('http://httpbin.org/put');
    

Bạn có thể tạo một request và gửi request này sau đó bằng client khi bạn đã sẵn sàng:
    
    
    use GuzzleHttpPsr7Request;
    
    $request = new Request('PUT', 'http://httpbin.org/put');
    $response = $client->send($request, ['timeout' => 2]);
    

Các đối tượng Client cung cấp một giải pháp linh động về việc làm thế nào để chuyển các request có những option, các bộ xử lý middleware mặc định mà được sử dụng bởi mỗi request, và một base URI cho phép bạn gửi những request với các URI liên quan.

Bạn có thể tìm hiểu thêm về client middleware tại trang  [_Handlers và Middleware_][3] trong tài liệu.

### Async Requests (Request bất đồng bộ)

Bạn có thể gửi các request bất đồng bộ thông qua việc sử dụng các phương thức magic được cung cấp bởi client:
    
    
    $promise = $client->getAsync('http://httpbin.org/get');
    $promise = $client->deleteAsync('http://httpbin.org/delete');
    $promise = $client->headAsync('http://httpbin.org/get');
    $promise = $client->optionsAsync('http://httpbin.org/get');
    $promise = $client->patchAsync('http://httpbin.org/patch');
    $promise = $client->postAsync('http://httpbin.org/post');
    $promise = $client->putAsync('http://httpbin.org/put');
    

Bạn cũng có thể sử dụng phương thức sendAsync() và requestAsync() của một client:
    
    
    use GuzzleHttpPsr7Request;
    
    // Tạo ra một đối tượng PSR-7 request để gửi
    $headers = ['X-Foo' => 'Bar'];
    $body = 'Hello!';
    $request = new Request('HEAD', 'http://httpbin.org/head', $headers, $body);
    
    // hoặc nếu bạn không muốn gửi thông qua một đối tượng Request
    $promise = $client->requestAsync('GET', 'http://httpbin.org/get');
    

Promise được trả về bởi những phương thức kế thừa từ [Promises/A+ spec][4], được cung cấp bởi [Guzzle promises library][5]. Nó có nghĩa mà bạn có thể gọi hàm `then()` ngay sau promise. Những lời gọi sau này phải thoả mãn với một `PsrHttpMessageResponseInterface` khi thành công hoặc bị từ chối với một exception nào đó. 
    
    use PsrHttpMessageResponseInterface;
    use GuzzleHttpExceptionRequestException;
    
    $promise = $client->requestAsync('GET', 'http://httpbin.org/get');
    $promise->then(
        function (ResponseInterface $res) {
            echo $res->getStatusCode() . "n";
        },
        function (RequestException $e) {
            echo $e->getMessage() . "n";
            echo $e->getRequest()->getMethod();
        }
    );
    

### Các requests đồng thời

Bạn có thể gửi nhiều request đồng thời bằng cách sử dụng promises và asynchronous requests.
    
    
    use GuzzleHttpClient;
    use GuzzleHttpPromise;
    
    $client = new Client(['base_uri' => 'http://httpbin.org/']);
    
    // Bắt đầu mỗi request nhưng không chặn nó
    $promises = [
        'image' => $client->getAsync('/image'),
        'png'   => $client->getAsync('/image/png'),
        'jpeg'  => $client->getAsync('/image/jpeg'),
        'webp'  => $client->getAsync('/image/webp')
    ];
    
    // Chờ tất cả các request hoàn thành. đưa ra một ConnectException nếu có bất kỳ request nào thất bại
    $results = Promiseunwrap($promises);
    
    // Chờ cho tất cả các request hoàn thành cho dù nếu có một trong số chúng thất bại
    $results = Promisesettle($promises)->wait();
    
    // Bạn có thể truy cập mỗi kết quả bằng cách sử dụng khoá được cung cấp thông qua hàm unwrap
    echo $results['image']['value']->getHeader('Content-Length')[0]
    echo $results['png']['value']->getHeader('Content-Length')[0]
    

Bạn có thể sử dụng đối tượng `GuzzleHttpPool` khi bạn muốn gửi một lượng request không xác định.
    
    
    use GuzzleHttpPool;
    use GuzzleHttpClient;
    use GuzzleHttpPsr7Request;
    
    $client = new Client();
    
    $requests = function ($total) {
        $uri = 'http://127.0.0.1:8126/guzzle-server/perf';
        for ($i = 0; $i < $total; $i++) {
            yield new Request('GET', $uri);
        }
    };
    
    $pool = new Pool($client, $requests(100), [
        'concurrency' => 5,
        'fulfilled' => function ($response, $index) {
            // this is delivered each successful response
        },
        'rejected' => function ($reason, $index) {
            // this is delivered each failed request
        },
    ]);
    
    // Bắt đầu truyền và tạo một promise
    $promise = $pool->promise();
    
    // Buộc nhóm các request hoàn thành
    $promise->wait();
    

Hoặc sử dụng một closure mà nó sẽ trả về một promise khi có một pool gọi closure.
    
    
    $client = new Client();
    
    $requests = function ($total) use ($client) {
        $uri = 'http://127.0.0.1:8126/guzzle-server/perf';
        for ($i = 0; $i < $total; $i++) {
            yield function() use ($client, $uri) {
                return $client->getAsync($uri);
            };
        }
    };
    
    $pool = new Pool($client, $requests(100));
    

## Sử dụng Responses

Trong ví dụ trên, chúng ra đã lấy một biến `$response` hoặc nhận một response từ một promise. đối tương trả về kế thừa từ một PSR-7 Response, `PsrHttpMessageResponseInterface`, và nó chứa rất nhiều thông tin có ích.

Bạn có thể lấy mã trạng thái và reason phrase của response:
    
    
    $code = $response->getStatusCode(); // 200
    $reason = $response->getReasonPhrase(); // OK
    

Bạn cũng có thể lấy được headers từ response:
    
    
    // Check if a header exists.
    // Kiểm tra nếu header có tồn tại
    if ($response->hasHeader('Content-Length')) {
        echo "It exists";
    }
    
    // Get a header from the response.
    // Lấy một header từ trong response
    echo $response->getHeader('Content-Length');
    
    // Get all of the response headers.
    // Lấy tất cả header trong response
    foreach ($response->getHeaders() as $name => $values) {
        echo $name . ': ' . implode(', ', $values) . "rn";
    }
    

Phần nội dung của response có thể được lấy ra bằng cách sử dụng phương thức `getBody`. Nội dung này có thể đươc sử dụng như một string, chuyển về thành string hoặc sử dụng như một luồng của đối tượng.
    
    
    $body = $response->getBody();
    // Implicitly cast the body to a string and echo it
    echo $body;
    // Explicitly cast the body to a string
    $stringBody = (string) $body;
    // Read 10 bytes from the body
    $tenBytes = $body->read(10);
    // Read the remaining contents of the body as a string
    $remainingBytes = $body->getContents();
    

## Query String Parameters

Bạn có thể cung cấp các tham số trong chuỗi truy vấn với một request với vài cách

Bạn có thể đưa các tham số vào chuỗi truy vấn ngay trên URI request:
    
    
    $response = $client->request('GET', 'http://httpbin.org?foo=bar');
    

Bạn cũng có thể chỉ đinh các tham số truy vấn sử dụng tuỳ chọn request `query` như một mảng.
    
    
    $client->request('GET', 'http://httpbin.org', [
        'query' => ['foo' => 'bar']
    ]);
    

Việc cung cấp các tuỳ chọn như một mảng sẽ sử dụng một hàm của PHP là `http_build_query` để định dạng chuỗi query.

Và cuối cùng, bạn có thể cung cấp một tuỳ chọn request `query` như một chuỗi
    
    
    $client->request('GET', 'http://httpbin.org', ['query' => 'foo=bar']);
    

## Tải lên dữ liệu

Guzzle cung cấp vài phương thức để tải lên dữ liệu.

Bạn có thể gửi các request chứa một luồng dữ liệu bằng cách gửi qua một string, resourse trả về từ `fopen`, haowjc một thực thể cuả một `PsrHttpMessageStreamInterface` cho tuỳ chọn `body` request.
    
    
    // Cung cấp phần nội dung như là một string
    $r = $client->request('POST', 'http://httpbin.org/post', [
        'body' => 'raw data'
    ]);
    
    // Cung cấp một resourse dạng fopen.
    $body = fopen('/path/to/file', 'r');
    $r = $client->request('POST', 'http://httpbin.org/post', ['body' => $body]);
    
    // Use the stream_for() function to create a PSR-7 stream.
    $body = GuzzleHttpPsr7stream_for('hello!');
    $r = $client->request('POST', 'http://httpbin.org/post', ['body' => $body]);
    

Có một cách rất dễ để upload dữ liệu Json và đặt header một cách thích hợp bằng cách sử dụng tuỳ chọn request `json`:
    
    $r = $client->request('PUT', 'http://httpbin.org/put', [
        'json' => ['foo' => 'bar']
    ]);
    

### POST/Form Requests

Ngoài việc chỉ định phần dữ liệu thô của một request bằng cách sử dụng tuỳ chọn `body` request, Guzzle cung cấp các abstraction hữu dụng trong việc POST dữ liệu

#### Gửi các trường từ form

Gửi các POST request dạng `application/x-www-form-urlencoded` yêu cầu bạn phải mô tả được các trường POST như một mảng trong tuỳ chọn request `form_params`.
    
    
    $response = $client->request('POST', 'http://httpbin.org/post', [
        'form_params' => [
            'field_name' => 'abc',
            'other_field' => '123',
            'nested_field' => [
                'nested' => 'hello'
            ]
        ]
    ]);
    

#### Gửi file trong form

Bạn có thể gửi file vói một form (`multipart/form-data` POST requests), sử dụng tuỳ chọn `multipart` request. `multipart` có thể chấp nhận mảng liên kết, mỗi mảng chứa các khoá sau: 

* name: (bắt buộc, kiểu string) ánh xạ khoá tới tên trường trong form.
* contents: (bắt buộc, mixed) Cung cấp một chuỗi để gửi nội dung của file như một string, cung cấp một fopen resourse để gửi nội dung từ một luồng PHP, hoặc cung cấp một `PsrHttpMessageStreamInterface` để truyền nội dung từ một luồng PSR-7.
    
    
    $response = $client->request('POST', 'http://httpbin.org/post', [
        'multipart' => [
            [
                'name'     => 'field_name',
                'contents' => 'abc'
            ],
            [
                'name'     => 'file_name',
                'contents' => fopen('/path/to/file', 'r')
            ],
            [
                'name'     => 'other_file',
                'contents' => 'hello',
                'filename' => 'filename.txt',
                'headers'  => [
                    'X-Foo' => 'this is an extra header to include'
                ]
            ]
        ]
    ]);
    

## Cookies

Guzzle có thể lưu trữ một phiên cookie cho bạn nếu được yêu cầu sử dụng tuỳ chọn `cookies` request. Khi gửi một request,tuỳ chọn `cookies` phải được đặt vào một thực thể của `GuzzleHttpCookieCookieJarInterface`.
    
    // Use a specific cookie jar
    $jar = new GuzzleHttpCookieCookieJar;
    $r = $client->request('GET', 'http://httpbin.org/cookies', [
        'cookies' => $jar
    ]);
    

Bạn có thể đặt `cookies` thành `true` trên phần khởi tạo của client nếu bạn muốn sử dụng một share cookie jar cho tất cả các request.    
    
    
    // Use a shared client cookie jar
    $client = new GuzzleHttpClient(['cookies' => true]);
    $r = $client->request('GET', 'http://httpbin.org/cookies');
    

## Chuyển hướng

Guzzle will automatically follow redirects unless you tell it not to. You can customize the redirect behavior using the `allow_redirects` request option.

* Set to `true` to enable normal redirects with a maximum number of 5 redirects. This is the default setting.
* Set to `false` to disable redirects.
* Pass an associative array containing the 'max' key to specify the maximum number of redirects and optionally provide a 'strict' key value to specify whether or not to use strict RFC compliant redirects (meaning redirect POST requests with POST requests vs. doing what most browsers do which is redirect POST requests with GET requests).
    
    
    $response = $client->request('GET', 'http://github.com');
    echo $response->getStatusCode();
    // 200
    

The following example shows that redirects can be disabled.
    
    
    $response = $client->request('GET', 'http://github.com', [
        'allow_redirects' => false
    ]);
    echo $response->getStatusCode();
    // 301
    

## Ngoại lệ ( Exceptions )

Guzzle throws exceptions for errors that occur during a transfer.

* In the event of a networking error (connection timeout, DNS errors, etc.), a `GuzzleHttpExceptionRequestException` is thrown. This exception extends from `GuzzleHttpExceptionTransferException`. Catching this exception will catch any exception that can be thrown while transferring requests.
    
        use GuzzleHttpPsr7;
    use GuzzleHttpExceptionRequestException;
    
    try {
        $client->request('GET', 'https://github.com/_abc_123_404');
    } catch (RequestException $e) {
        echo Psr7str($e->getRequest());
        if ($e->hasResponse()) {
            echo Psr7str($e->getResponse());
        }
    }
    

* A `GuzzleHttpExceptionConnectException` exception is thrown in the event of a networking error. This exception extends from `GuzzleHttpExceptionRequestException`.
* A `GuzzleHttpExceptionClientException` is thrown for 400 level errors if the `http_errors` request option is set to true. This exception extends from `GuzzleHttpExceptionBadResponseException` and `GuzzleHttpExceptionBadResponseException` extends from `GuzzleHttpExceptionRequestException`.
    
        use GuzzleHttpExceptionClientException;
    
    try {
        $client->request('GET', 'https://github.com/_abc_123_404');
    } catch (ClientException $e) {
        echo Psr7str($e->getRequest());
        echo Psr7str($e->getResponse());
    }
    

* A `GuzzleHttpExceptionServerException` is thrown for 500 level errors if the `http_errors` request option is set to true. This exception extends from `GuzzleHttpExceptionBadResponseException`.
* A `GuzzleHttpExceptionTooManyRedirectsException` is thrown when too many redirects are followed. This exception extends from `GuzzleHttpExceptionRequestException`.

All of the above exceptions extend from `GuzzleHttpExceptionTransferException`.

## Các biến môi trường

Guzzle exposes a few environment variables that can be used to customize the behavior of the library.

`GUZZLE_CURL_SELECT_TIMEOUT`
: Controls the duration in seconds that a curl_multi_* handler will use when selecting on curl handles using `curl_multi_select()`. Some systems have issues with PHP's implementation of `curl_multi_select()` where calling this function always results in waiting for the maximum duration of the timeout.

`HTTP_PROXY`
: 

Defines the proxy to use when sending requests using the "http" protocol.

Note: because the HTTP_PROXY variable may contain arbitrary user input on some (CGI) environments, the variable is only used on the CLI SAPI. See  for more information.

`HTTPS_PROXY`
: Defines the proxy to use when sending requests using the "https" protocol.

[1]: http://docs.guzzlephp.org/overview.html#installation
[2]: http://tools.ietf.org/html/rfc3986#section-5.2
[3]: http://docs.guzzlephp.org/handlers-and-middleware.html
[4]: https://promisesaplus.com/
[5]: https://github.com/guzzle/promises

  