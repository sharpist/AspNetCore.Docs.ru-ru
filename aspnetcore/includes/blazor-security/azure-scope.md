> [!NOTE]
> Если портал Azure предоставляет URI области для приложения и приложение создает необработанное исключение при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел. Например, портал Azure может предоставить один из следующих форматов URI области:
>
> * `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Попробуйте указать URI области без схемы и узла:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```
