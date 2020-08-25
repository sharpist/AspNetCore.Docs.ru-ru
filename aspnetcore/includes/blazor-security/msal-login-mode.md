По умолчанию платформа использует режим входа в систему и возвращается в режим перенаправления входа, если не удается открыть всплывающее окно. Настройте MSAL для использования режима перенаправления входа, задав для свойства `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> значение `redirect`.

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Значением по умолчанию является `popup`, и это строковое значение без учета регистра.
