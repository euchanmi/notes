# Curl cheatsheet

Curl is an awesome tool. I keep forgetting the switches though, so here’s a cheatsheet.

### Simple GET request

```bash
curl -k "https://localhost/foo?bar=baz&abc=def"
```

### JSON POST or PUT request

```bash
curl -k -H "Content-Type: application/json" -X POST -d '{"accountName":"test","value":"hello"}' https://localhost/foo
curl -X "PUT"
… for a PUT
```

### POST a file

```bash
curl ... --data-binary @filename
```

### Fake a /etc/hosts entry and a Host: header with curl

```bash
curl -vvv --resolve 'book.mixu.net:80:123.145.167.189' http://book.mixu.net/
```

### Make a request with basic auth enabled

```bash
curl -vvv -u name@foo.com:password http://www.example.com
or:

curl --user name:password http://www.example.com
```

### Set the Referer header

```bash
curl -e http://curl.haxx.se daniel.haxx.se
```

### Set the User Agent header

```bash
curl -A "Mozilla/4.73"
or

curl --user-agent "Mozilla".
```

### Set Cookies

```bash
curl -b "name=Daniel"
or

curl --cookie "name=Daniel"
```

### Time a request (connect time + time to first byte + total tile)

```bash
curl -o /dev/null -w "Connect: %{time_connect} TTFB: %{time_starttransfer} Total time: %{time_total} \n" http://google.com
```

### Downloading files from Github

```bash
curl -O  https://raw.github.com/username/reponame/master/filename
```
