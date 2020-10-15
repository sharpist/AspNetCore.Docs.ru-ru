<span data-ttu-id="139d4-101">Портал Azure предоставляет один из следующих форматов URI для идентификаторов приложений с учетом того, имеет ли арендатор AAD [подтвержденный домен издателя](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="139d4-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="139d4-102">Если вы используете два предшествующих URI идентификаторов приложений в клиентском приложении для формирования URI области и авторизация не выполняется успешно, попробуйте предоставить URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="139d4-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="139d4-103">Пример</span><span class="sxs-lookup"><span data-stu-id="139d4-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
