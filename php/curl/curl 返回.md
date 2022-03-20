# curl http code 0

使用curl进行post请求后，接收status code ,结果返回的结果是0 ，但是请求返回的数据是正常的。

检查后发现是执行顺序问题：

```php
$response = [
    'statusCode'    => curl_getinfo($curl , CURLINFO_HTTP_CODE),
    'body'          => curl_exec   ($curl),
    'curlErrorCode' => curl_errno  ($curl),
];
```

由于后来改动结构，所以没在意 curl_exec的位置，直接调整就用了，所以在输出时，值一直是0
后来调整后就好了，也就是应该先执行$curl请求，然后才能获得请求的状态码等相关参数（没毛病），由于原来是放在数组外面接收的，所以不存在这个问题

```php
$response = [
    'body'          => curl_exec   ($curl),
    'curlErrorCode' => curl_errno  ($curl),
    'statusCode'    => curl_getinfo($curl , CURLINFO_HTTP_CODE),
];
```