# php-curl-example
Basic curl usage example


# Just get the body

```
<?php

$body = '';
$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => 'http://example.com',
    CURLOPT_CUSTOMREQUEST => 'GET',
    CURLOPT_FOLLOWLOCATION => true,
    CURLOPT_RETURNTRANSFER => false,
    CURLOPT_HEADER => false,
    CURLOPT_TIMEOUT => 10,
    CURLOPT_USERAGENT => "thlib/php-curl-example",
    CURLOPT_REFERER => "https://github.com/thlib/php-curl-example",
    CURLOPT_ENCODING => 'gzip',
    CURLOPT_MAXREDIRS => 3,
    CURLOPT_BUFFERSIZE => (1024*1024),
    CURLOPT_WRITEFUNCTION => function($ch, $content) use(&$body) {
        $body .= $content;
        return strlen($content);
    }
]);
if (false === curl_exec($ch)) {
    throw new \Exception(curl_error($ch), curl_errno($ch));
}

echo $body;
```

# Small file download example

```
<?php

$headers = [];
$body = '';
$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => 'http://example.com',
    CURLOPT_CUSTOMREQUEST => 'GET',
    CURLOPT_FOLLOWLOCATION => true,
    CURLOPT_RETURNTRANSFER => false,
    CURLOPT_HEADER => false,
    CURLOPT_TIMEOUT => 10,
    CURLOPT_USERAGENT => "thlib/php-curl-example",
    CURLOPT_REFERER => "https://github.com/thlib/php-curl-example",
    CURLOPT_ENCODING => 'gzip',
    CURLOPT_MAXREDIRS => 3,
    CURLOPT_BUFFERSIZE => (1024*1024),
    CURLOPT_HEADERFUNCTION => function($ch, $header) use(&$headers) {
        // This is the protocol and status header
        if (empty($headers)) {
            $headers['http-code'] = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        }

        $parts = explode(':', $header, 2);

        // ignore invalid headers
        if (!isset($parts[1]) || trim($parts[0]) === '') {
            return strlen($header);
        }
        $headers[trim(strtolower($parts[0]))] = trim($parts[1]);

        return strlen($header);
    },
    CURLOPT_WRITEFUNCTION => function($ch, $content) use(&$body) {
        $body .= $content;
        return strlen($content);
    }
]);
if (false === curl_exec($ch)) {
    throw new \Exception(curl_error($ch), curl_errno($ch));
}

echo $body;
```



# Large file download example

```
<?php

$headers = [];

// We are using a file handle 
// to demonstrate how the content can be written to a file
// without loading all the content into RAM
// allowing us to download very large files
$fh = fopen('php://temp', 'rw+');
$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => 'http://example.invalid',
    CURLOPT_CUSTOMREQUEST => 'GET',
    CURLOPT_FOLLOWLOCATION => true,
    CURLOPT_RETURNTRANSFER => false,
    CURLOPT_HEADER => false,
    CURLOPT_TIMEOUT => 10,
    CURLOPT_USERAGENT => "thlib/php-curl-example",
    CURLOPT_REFERER => "https://github.com/thlib/php-curl-example",
    CURLOPT_ENCODING => 'gzip',
    CURLOPT_MAXREDIRS => 3,
    CURLOPT_BUFFERSIZE => (1024*1024),
    CURLOPT_HEADERFUNCTION => function($ch, $header) use(&$headers) {
        // This is the protocol and status header
        if (empty($headers)) {
            $headers['http-code'] = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        }

        $parts = explode(':', $header, 2);

        // ignore invalid headers
        if (!isset($parts[1]) || trim($parts[0]) === '') {
            return strlen($header);
        }
        $headers[trim(strtolower($parts[0]))] = trim($parts[1]);

        return strlen($header);
    },
    CURLOPT_WRITEFUNCTION => function($ch, $content) use($fh) {
        return fwrite($fh, $content);
    }
]);
if (false === curl_exec($ch)) {
    throw new \Exception(curl_error($ch), curl_errno($ch));
}

rewind($fh);

while (!feof($fh)) {
 
    // Read in 1K chunks. You could make this 
    // larger, but as a rule of thumb I'd keep it to 1/4 of 
    // your php memory_limit.

    echo fread($fh, 1024);
    flush();
}

fclose($fh);
```

# ReturnTransfer example

```
<?php

$body = '';
$ch = curl_init();
curl_setopt_array($ch, [
    CURLOPT_URL => 'http://example.com',
    CURLOPT_CUSTOMREQUEST => 'GET',
    CURLOPT_FOLLOWLOCATION => true,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HEADER => false,
    CURLOPT_TIMEOUT => 10,
    CURLOPT_USERAGENT => "thlib/php-curl-example",
    CURLOPT_REFERER => "https://github.com/thlib/php-curl-example",
    CURLOPT_ENCODING => 'gzip',
    CURLOPT_MAXREDIRS => 3,
]);
$body = curl_exec($ch);
if (false === $body) {
    throw new \Exception(curl_error($ch), curl_errno($ch));
}

echo $body;
```
