<span data-ttu-id="4f7c6-101">Сброс позволяет серверу сбросить запрос HTTP/2 с указанным кодом ошибки.</span><span class="sxs-lookup"><span data-stu-id="4f7c6-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="4f7c6-102">Сброшенный запрос считается прерванным.</span><span class="sxs-lookup"><span data-stu-id="4f7c6-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="4f7c6-103">`Reset` в предыдущем примере кода задает код ошибки `INTERNAL_ERROR`.</span><span class="sxs-lookup"><span data-stu-id="4f7c6-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="4f7c6-104">Дополнительные сведения о кодах ошибок HTTP/2 см. в [соответствующем разделе спецификации HTTP/2](https://tools.ietf.org/html/rfc7540#page-50).</span><span class="sxs-lookup"><span data-stu-id="4f7c6-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>