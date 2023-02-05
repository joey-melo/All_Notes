## Scanning a specific request

It may be beneficial to do a scan for a specific request intead of simply sending it to Repeater or Intruder.

`Proxy tab > HTTP history tab > right click specified request > Do active scan`

## Scanning custom insertion points

It's possible to specify your scan even further by scanning only specific inputs within a request.

`Intruder tab > right click payload position (between §§) > Scan defined insertion points`

Alternatively, you can use the extension **Scan manual insertion point**.

## Scanning non-standard data structures

Consider non-standard payloads, such as the following:

```http
GET /my-account?user=048857-carlos HTTP/1.1
```

you can split `048857-carlos` into `048857` and `carlos`, so your insertion point will look like this: `GET /my-account?user=§048857§-§carlos§ HTTP/1.1`. This way, you can scan the defined insertion points accurately.