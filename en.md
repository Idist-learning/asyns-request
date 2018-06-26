
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

Guzzle sẽ tự động chuyển hướng nếu bạn không nói nó đừng làm vậy. Bạn có thể tuỳ chỉnh việc chuyển hướng bằng cách sử dụng tuỳ chọn `allow_redirect` request.

* Đặt thành `true` để bật điều hướng bình thường với tối đa 5 lần. Đây là cài đặt mặc định.
* Đặt thành `false` để tắt điều hướng.
* Truyền một mảng liên kết với khoá `max` để mô tả số lần điều hướng tối đa và có thể cung cáp một giá trị khoá `strict` để áp dụng hoặc không các chuẩn điều hướng của RFC ( có nghĩa là điều hướng các POST request với POST request thưc hiện việc mà hầu hết các trình duyệt thực hiện như là chuyển hướng POST request với GET request).  
    
    
    $response = $client->request('GET', 'http://github.com');
    echo $response->getStatusCode();
    // 200
    

Ví dụ sau cho thấy việc redirect có thể được tắt đi.
    
    
    $response = $client->request('GET', 'http://github.com', [
        'allow_redirects' => false
    ]);
    echo $response->getStatusCode();
    // 301
    

## Ngoại lệ ( Exceptions )

Guzzle đưa ra các ngoại lệ cho những lỗi xảy ra trong khi truyền.

* với Lỗi trên các sự kiện của mạng (hết thời gian kết nối, lỗi DNS, etc,...), một `GuzzleHttpExceptionRequestException` được đưa ra. ngoại lệ này kế thừa từ `GuzzleHttpExceptionTransferException`. Bắt ngoại lệ này sẽ bắt mọi ngoại lệ mà được đưa ra trong suốt quá trình truyền request. 
    
    
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
    

* 1 ngoại lệ `GuzzleHttpExceptionConnectException` được ném ra trong trường hợp lỗi mạng. Ngoại lệ này extends từ `GuzzleHttpExceptionRequestException`.
*  `GuzzleHttpExceptionClientException` được ném ra khi lỗi 400 nếu tùy chọn request `http_errors` được đặt thành `true`. Ngoại lệ này extends từ `GuzzleHttpExceptionBadResponseException` và `GuzzleHttpExceptionBadResponseException` kế thừa từ `GuzzleHttpExceptionRequestException`.
    
    
        use GuzzleHttpExceptionClientException;
    
    try {
        $client->request('GET', 'https://github.com/_abc_123_404');
    } catch (ClientException $e) {
        echo Psr7str($e->getRequest());
        echo Psr7str($e->getResponse());
    }
    

* `GuzzleHttpExceptionServerException` được ném ra cho lỗi mức 500 nếu tùy chọn request `http_errors` được đặt thành true. Ngoại lệ này kế thừa từ `GuzzleHttpExceptionBadResponseException`.
* `GuzzleHttpExceptionTooManyRedirectsException` sẽ được ném ra khi có quá nhiều lần điều hướng xảy ra. Ngoại lệ này kế thừa từ `GuzzleHttpExceptionRequestException`.

Tất cả những ngoại lệ trên đều kế thừa từ `GuzzleHttpExceptionTransferException`.

## Các biến môi trường

guzzle cung cấp vài biến môi trường có thể sử udngj để tuỳ biến hành động của thư viện

`GUZZLE_CURL_SELECT_TIMEOUT`: Điều khiển được thời gina bằng giây mà 1 hàm xử lý curl_multi_* sẽ sử dụng khi lựa chọn trên các xử lý của curl có sử dụng `curl_multi_select()`. Vài hệ thống có lỗi với việc thực hiện `curl_multi_select()` của PHP khi gọi hàm này luôn trả về kết quả bị quá thời gian chờ tối đa.

`HTTP_PROXY`: Định nghĩa proxy để sử dụng khi gửi request có sử dụng giao thức `http`.



Ghi chú: vì biến HTTP_PROXY có thể chứa đầu vào tùy ý từ người dùng trên 1 vài môi trường (CGI), nên nó chỉ được sử dụng trên CLI SAPI. Xem thêm thông tin sau đây.

`HTTPS_PROXY`
: Định nghĩa proxy được sử dụng khi gửi các request sử dụng giao thức "https".

[1]: http://docs.guzzlephp.org/overview.html#installation
[2]: http://tools.ietf.org/html/rfc3986#section-5.2
[3]: http://docs.guzzlephp.org/handlers-and-middleware.html
[4]: https://promisesaplus.com/
[5]: https://github.com/guzzle/promises

  