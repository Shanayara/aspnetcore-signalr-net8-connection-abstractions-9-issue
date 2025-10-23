Minimal example to reproduce a bug.

When you start the app in Kestrel (e.g. via "Run"), the following POST (here we use CURL, you can use any other tool) produces a valid response:
```
curl -v "http://localhost:9099/chatHub/negotiate?negotiateVersion=1" --data ""
* Host localhost:9099 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:9099...
* Connected to localhost (::1) port 9099
* using HTTP/1.x
> POST /chatHub/negotiate?negotiateVersion=1 HTTP/1.1
> Host: localhost:9099
> User-Agent: curl/8.14.1
> Accept: */*
> Content-Length: 0
> Content-Type: application/x-www-form-urlencoded
>
< HTTP/1.1 200 OK
< Content-Length: 316
< Content-Type: application/json
< Date: Thu, 23 Oct 2025 14:04:42 GMT
< Server: Kestrel
<
{"negotiateVersion":1,"connectionId":"O8N6_5VUkHfrNQxyubSBdg","connectionToken":"oDPrSzh08JlYgypVW0888g","availableTransports":[{"transport":"WebSockets","transferFormats":["Text","Binary"]},{"transport":"ServerSentEvents","transferFormats":["Text"]},{"transport":"LongPolling","transferFormats":["Text","Binary"]}]}* Connection #0 to host localhost left intact```

However, if you publish the app to a folder and then host it in IIS (make a new AppPool with Unmanaged Code, and host it at http localhost:9098, and run:

```
curl -v "http://localhost:9098/chatHub/negotiate?negotiateVersion=1" --data ""
* Host localhost:9098 was resolved.
* IPv6: ::1
* IPv4: 127.0.0.1
*   Trying [::1]:9098...
* Connected to localhost (::1) port 9098
* using HTTP/1.x
> POST /chatHub/negotiate?negotiateVersion=1 HTTP/1.1
> Host: localhost:9098
> User-Agent: curl/8.14.1
> Accept: */*
> Content-Length: 0
> Content-Type: application/x-www-form-urlencoded
>
< HTTP/1.1 200 OK
< Server: Microsoft-IIS/10.0
< X-Powered-By: ASP.NET
< Date: Thu, 23 Oct 2025 14:06:50 GMT
< Content-Length: 0
<
* Connection #0 to host localhost left intact
```

Deleting the `<PackageReference Include="Microsoft.AspNetCore.Connections.Abstractions" Version="9.0.0" />` from WebApplication1.csproj makes it so the IIS call also produces a valid response. This should not be necessary, the most up to date version of the package should work.
