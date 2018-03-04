## curl

cURL is a tool to transfer data from or to a server using URL. It supports almost all major protocols(DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS,  IMAP, IMAPS,  LDAP,  LDAPS,  POP3, POP3S, RTMP, RTSP, SCP, SFTP, SMTP, SMTPS, TELNET and TFTP) in use. 

**Note:** curl can guess which protocol to use based on the URL host name you use. For example, `ftp.example.com` URL is given as an argument, curl will use FTP protocol to fetch data. In case curl cannot guess the protocol, it will default to HTTP, so it's better to specify the protocol exlicitly.

### Download a file

`curl http://example.com` - download the content of a given URL. In this case, it will download an HTML page from a website.

**Note:** by default, curl will display the downloaded content in the stdout (i.e. in your terminal). To save the downloaded content, you need to either redirect the stdout (`>`) or use options like `-o` and `O`.

---

`curl -o test.html http://example.com` - save the downloaded content into a specified file `test.html`.
`curl -O http://example.com/index.html` - save the downloaded content into a file. The name of the file in which to save the result will be taken from the URL (`index.html`).
`curl -O URL1 -O URL2` - fetch multiple files.

### Silence the output

When you download a file with curl, you'll see in the output the download details showing the amount of transferred data, transfer speeds and estimated time left, etc.

```bash
$ curl -O http://example.com/index.html
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15331  100 15331    0     0  47701      0 --:--:-- --:--:-- --:--:-- 47611
```

This is know as a `progress meter`. If you're not interested in any of these details, you can silence it use the following options.

`curl -sO http://example.com/index.html` - `s` (`--silent`) will not show progress meter or error messages.
`curl -sSO http://example.com/index.html` - when  used  with  `-s`, the `S` (`--show-error`) option  makes curl show an error message if it fails.

### Progress bar

If you don't like the look of progress meter and prefer to see a progress bar instead, use the `-#` option:

```bash
$ curl -#O http://example.com/index.html
######################################################################## 100,0%
```

### Follow HTTP redirection

By default curl doesn’t follow the `HTTP Location headers` (also know as `Redirects`). When a requested web page is moved to another place, then an HTTP Location header will be sent as a Response and it will have where the actual web page is located.

For example, when someone types google.com in the browser from India, it will be automatically redirected to `google.co.in`. This is done based on the HTTP Location header as shown below.

```bash
$ curl http://www.google.com

<TITLE>302 Moved</TITLE>
<H1>302 Moved</H1>
The document has moved
<A HREF="http://www.google.co.in/">here</A>
```

The above output says that the requested document is moved to `http://www.google.co.in/`.

We can insists curl to follow the redirection using -L option. Now it will download the google.co.in’s html source code.

`curl -L http://www.google.com`

### Response Headers

If you want to include the headers info in your response, use the `-i` (`--include`) option.

---

If you're only interested in the response headers returned by the server, and not the response content, you can use the `-I` option.

```bash
$ curl -I http://example.com

HTTP/1.1 200 OK
Content-Encoding: gzip
Accept-Ranges: bytes
Cache-Control: max-age=604800
Content-Type: text/html
Date: Sun, 04 Mar 2018 21:25:53 GMT
Etag: "1541025663"
Expires: Sun, 11 Mar 2018 21:25:53 GMT
Last-Modified: Fri, 09 Aug 2013 23:54:35 GMT
Server: ECS (sjc/4E44)
X-Cache: HIT
Content-Length: 606
```


### Ingore SSL certificates

All SSL connections are attempted to be made secure by using the CA certificate bundle installed on the system by default. This makes all connections considered "insecure" (for example, when you use a self-signed certificate) fail unless `-k` (`--insecure`) option is used.

```bash
$ curl -k https://10.20.100.20:9000
```

### Specify transfer rate limit

You can specify the maximum transfer rate that you want curl to use by using the `--limit-rate` option. This can be helpful, when you do not want your curl command to consume much of the available bandwidth.

The given speed is measured in `bytes/second`, unless a suffix is appended. Availabel suffixes:
* `k` (`K`) - kilobytes
* `m` (`M`) - megabytes
* `g` (`G`) - gigabytes

Example:

`curl --limit-rate 100k -O http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.10.tar.gz` - limit the download speed by 100 kilobytes per second.

### Basic Authentication

`curl -u username:password http://example.com/` - pass credentials for basic authentication.

### Verbose

Command's verbose mode is useful for debugging, it gives more information on what is going on. For example, it also give you full information about the request you send.

`curl -v http://example.com/` - enable verbose mode.


### POST and PUT requests

By default, curl sends a `GET` request to a specified URL. To choose a different type of request, use the `-X` (`--request`) option.

`curl -X POST https://example.com` - send a POST request.

To specify a header, there is a `-H` (`--header`) option and for specifying the data to send, use the `-d` (`--data`) option.

```bash
$ curl -d '{"key1":"value1", "key2":"value2"}' -H "Content-Type: application/json" -X POST http://localhost:3000/data
# send data from a file
$ curl -d @data.json -H "Content-Type: application/json" -X POST http://localhost:3000/data
```

**Note:** when sending data via a `POST` or `PUT` request, two formats are very common (specified via the `Content-Type header`):

* `application/json`
* `application/x-www-form-urlencoded`

Many APIs will accept both formats, in which case it can be a bit easier to use the form urlencoded format instead of json because:

* the json format requires a bunch of extra quoting
* curl will use the form urlencoded header by default. That's what your typical browser will use for a plain POST. For json the Content-Type header must be explicitly set.

Example of sending a POST using the default form urlencoded format:

```bash
$ curl -d "param1=hello&param2=world" -X POST http://localhost:3000/data
$ curl -d "param1=hello" -d "param2=world" -X POST http://localhost:3000/data
```

### Resources used to create this document:

* https://www.thegeekstuff.com/2012/04/curl-examples/?utm_source=feedburner
* https://www.slashroot.in/curl-command-tutorial-linux-example-usage
* https://gist.github.com/subfuzion/08c5d85437d5d4f00e58#post