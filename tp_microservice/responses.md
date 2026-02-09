# TP Docker — Application microservices

## Résultat

**Requête :**
``bash
curl http://localhost:8080/health
``

**Résultat :** 
```json
StatusCode        : 200
StatusDescription : OK
Content           : OK
RawContent        : HTTP/1.1 200 OK
Connection: keep-alive
Content-Length: 2
Content-Type: text/plain,text/plain
Date: Mon, 09 Feb 2026 14:28:34 GMT
Server: nginx/1.29.5

                    OK
Forms             : {}
Headers           : {[Connection, keep-alive], [Content-Length, 2], [Content-Type, text/plain,text/plain], [Date, Mon, 09 Feb 2026 14:28:34 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2

```

**Requête :**
``bash
curl http://localhost:8080/catalog/health
``

**Résultat :**
```json
StatusCode        : 200
StatusDescription : OK
Content           : {"status":"UP"}
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Mon, 09 Feb 2026 14:43:50 GMT
                    ETag: W/"f-RQ8OySFd+KR+AvtJ7qImjtT0D/0"
                    Server: nginx/...
Forms             : {}
Headers           : {[Connection, keep-alive], [Content-Length, 15], [Content-Type, application/json; charset=utf-8], [Date, Mon, 09 Feb 2026 14:43:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 15

```
**Requête :**
``bash
curl http://localhost:8080/orders/health 
``

**Résultat :**
```json
StatusCode        : 200
StatusDescription : OK
Content           : {"status":"UP"}
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Mon, 09 Feb 2026 14:44:11 GMT
                    ETag: W/"f-RQ8OySFd+KR+AvtJ7qImjtT0D/0"
                    Server: nginx/...
Forms             : {}
Headers           : {[Connection, keep-alive], [Content-Length, 15], [Content-Type, application/json; charset=utf-8], [Date, Mon, 09 Feb 2026 14:44:11 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 15

```
**Requête :**
``bash
curl http://localhost:8080/catalog/products 
``

**Résultat :**
```json
StatusCode        : 200
StatusDescription : OK
Content           : [{"id":"p1","name":"Clavier","price":49.9},{"id":"p2","name":"Souris","price":19.9},{"id":"p3","name":"Ecran","price":179.9}]
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Content-Length: 125
                    Content-Type: application/json; charset=utf-8
                    Date: Mon, 09 Feb 2026 14:44:36 GMT
                    ETag: W/"7d-LfaqQ9rzll+rtfwRtt/HwKDaiM0"
                    Server: ngin...
Forms             : {}
Headers           : {[Connection, keep-alive], [Content-Length, 125], [Content-Type, application/json; charset=utf-8], [Date, Mon, 09 Feb 2026 14:44:36 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 125

```
**Requête :**
``bash
curl http://localhost:8080/orders/orders 
``

**Résultat :**
```json
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Content-Length: 2
                    Content-Type: application/json; charset=utf-8
                    Date: Mon, 09 Feb 2026 14:45:04 GMT
                    ETag: W/"2-l9Fw4VUO7kr8CvBlt4zaMCqXZ0w"
                    Server: nginx/1...
Forms             : {}
Headers           : {[Connection, keep-alive], [Content-Length, 2], [Content-Type, application/json; charset=utf-8], [Date, Mon, 09 Feb 2026 14:45:04 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

**Requête :**
```
Invoke-RestMethod `
  -Uri http://localhost:8080/orders/orders `
  -Method POST `
  -ContentType "application/json" `
  -Body '{"productId":"p1","quantity":2}'
```

**Résultat :**
```json
id        : 1
productId : p1
quantity  : 2
status    : CREATED
createdAt : 2026-02-09T14:47:00.066Z
```

**Nettoyage :**
```bash
docker compose down -v
```