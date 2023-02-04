```python
import requests

# Disable warnings
requests.packages.urllib3.disable_warnings(requests.packages.urllib3.exceptions.InsecureRequestWarning)

# requests objects:
r = requests.get(
	url,
	verify=False,
	allow_redirects=False,
	auth=("user","pass")
	data={"key":"value"},
	headers={"header":"value"},
	params={"parameter":"value"},
	cookies={"cookie":"value"},
	files={"file_field":("file_name", open("file","rb"), "content-type")},
	proxies={"http":"http://127.0.0.1:8080"}
)

# response objects:
encoding = r.enconding #ISO-8859-1
text_content = r.text #'content'
raw_content = r.content #b'content'
json_content = r.json #[{'repository': {'open_issues': 0, 'url': 'https://github.com/...
socket_content = r.raw.read(10) #\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03
status_code = r.status_code #404
raise_on_4xx_and_5xx = r.raise_for_status() #raises exception on status code 4xx and 5xx
url = r.url #https://github.com
history = r.history #[<Response [301]>]
header = r.headers #{'Content-Length': '261', 'Set-Cookie':
cookies = r.cookies #<RequestsCookieJar[<Cookie JSESSIONID_APM_9090=808639988060D663A797DF8EA8019F67
```