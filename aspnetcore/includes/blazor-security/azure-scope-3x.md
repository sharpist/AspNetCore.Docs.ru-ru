Портал Azure предоставляет один из следующих форматов URI для идентификаторов приложений с учетом того, имеет ли арендатор AAD [подтвержденный домен издателя](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Если вы используете два предшествующих URI идентификаторов приложений в клиентском приложении для формирования URI области и авторизация не выполняется успешно, попробуйте предоставить URI области без схемы и узла:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Пример

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
