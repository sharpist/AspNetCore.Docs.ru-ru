<span data-ttu-id="3328e-101">По умолчанию платформа использует режим входа в систему и возвращается в режим перенаправления входа, если не удается открыть всплывающее окно.</span><span class="sxs-lookup"><span data-stu-id="3328e-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="3328e-102">Настройте MSAL для использования режима перенаправления входа, задав для свойства `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> значение `redirect`.</span><span class="sxs-lookup"><span data-stu-id="3328e-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="3328e-103">Значением по умолчанию является `popup`, и это строковое значение без учета регистра.</span><span class="sxs-lookup"><span data-stu-id="3328e-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
