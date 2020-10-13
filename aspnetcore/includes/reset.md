Сброс позволяет серверу сбросить запрос HTTP/2 с указанным кодом ошибки. Сброшенный запрос считается прерванным.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` в предыдущем примере кода задает код ошибки `INTERNAL_ERROR`. Дополнительные сведения о кодах ошибок HTTP/2 см. в [соответствующем разделе спецификации HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).