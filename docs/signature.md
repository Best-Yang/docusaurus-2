---
id: signature
title: 签名验证
sidebar_label: 签名
---

> YYYY-MM-DD HH mm

### 介绍
> 为保证API及数据的安全，API调用仅支持`HTTPS`协议，且在调用API时，TB服务器会对每个API请求通过签名（Signature）进行身份验证。

> 对于每一个请求，TB服务器会根据请求头部的`Authorization`字段来校验是否合法。第三方须使用与TB服务端一致的签名算法才能通过验证，对于未包含签名字段或者签名错误的请求，函数计算将会返回`HTTP 403`错误。`hmac-sha256`：需要以您的AccessKey Secret为Key。

:::important
注意： 客户端需要保证生成的时间与TB服务端的时间相差不超过15分钟，否则将拒绝此次请求。
:::

### 样例

```php
<?php
    $params = [
        'accessKeyId' => 'TbTestAccessKeyId',
        'accessKeySecret' => 'CESHISECRET123456789',
        'path' => '/open/third',
        'contentType' => 'application/json',
        'date' => gmdate("D, d M Y H:i:s") . " GMT"
    ];
    //print_r(json_encode($params,JSON_PRETTY_PRINT | JSON_UNESCAPED_SLASHES));
    echo PHP_EOL;
    echo 'Authorization: '.calcSignature($params);
        
    function calcSignature($p) {
        $stringToSign = implode("\n", [$p['path'], $p['contentType'], $p['date']]);
        $signature = base64_encode(hash_hmac('sha256', $stringToSign, $p['accessKeySecret'], true));
        return 'TB '.$p['accessKeyId'].':'.$signature;
    }
```

参数说明如下：$path 格式为 /aaaa/bbbb 例如 open.baidu.com/open/third?appid=123456 中 `/open/third`注意：增加在请求头信息中

`CONTENT-TYPE`：请求内容的类型，函数计算的类型是`application/json`。

`DATE`：此次操作的时间，不能为空，时间格式为GMT。
