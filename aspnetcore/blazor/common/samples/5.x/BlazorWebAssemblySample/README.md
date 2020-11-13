# <a name="blazor-webassembly-sample-app"></a>Пример приложения Blazor WebAssembly

Этот пример показывает использование сценариев Blazor, описанных в документации по Blazor.

## <a name="call-web-api-example"></a>Пример вызова веб-API

Для этого примера веб-API требуется работающий интерфейс веб-API на основе примера приложения для статьи <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Создание веб-API с помощью ASP.NET Core</a>, который по умолчанию использует тот же порт HTTPS (5001), что и пример приложения Blazor. Чтобы одновременно использовать оба приложения на одном компьютере, измените порт веб-API (например, используйте порт 10000). Пример приложения выполняет запросы к веб-API по адресу `https://localhost:10000/api/TodoItems`. Если используется другой адрес веб-API, измените значение константы `ServiceEndpoint` в блоке `@code` компонента Razor.</p>

Пример приложения выполняет запрос на <a href="https://docs.microsoft.com/aspnet/core/security/cors">общий доступ к ресурсам независимо от источника (CORS)</a> с адреса `http://localhost:5000` или `https://localhost:5001` к веб-API. Учетные данные (файлы cookie и заголовки авторизации) разрешены. Добавьте следующую конфигурацию ПО промежуточного слоя CORS в метод `Startup.Configure` веб-API:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Настройте домены и порты `WithOrigins` для приложения Blazor требуемым образом.

Веб-API настраивается для CORS так, чтобы разрешать файлы cookie, заголовки и запросы авторизации из клиентского кода, однако веб-API, созданный с помощью руководства, не авторизует запросы. Указания по реализации см. в <a href="https://docs.microsoft.com/aspnet/core/security/">статьях о безопасности и идентификации в ASP.NET Core</a>.
