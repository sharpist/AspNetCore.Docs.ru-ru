---
title: Проверка подлинности и авторизация в ASP.NET CoreSignalR
author: bradygaster
description: Узнайте, как использовать проверку подлинности и SignalRавторизацию в ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 0f0bb2040d2407817c91f64a4769e6601c37a07d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775288"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a>Проверка подлинности и авторизация в ASP.NET CoreSignalR

[Эндрю Стантон-медперсонала](https://twitter.com/anurse)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a>Проверка подлинности пользователей SignalR , подключающихся к концентратору

SignalRможно использовать с [проверкой подлинности ASP.NET Core](xref:security/authentication/identity) , чтобы связать пользователя с каждым соединением. В центре данные проверки подлинности можно получить из свойства [хубконнектионконтекст. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) . Проверка подлинности позволяет концентратору вызывать методы для всех соединений, связанных с пользователем. Дополнительные сведения см. [в разделе Управление пользователями и группами SignalRв ](xref:signalr/groups). Несколько подключений могут быть связаны с одним пользователем.

Ниже приведен пример `Startup.Configure` использования SignalR и ASP.NET Core проверки подлинности.

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> Порядок, в котором регистрируется и SignalR ASP.NET Core по промежуточного слоя проверки подлинности имеет значение. `UseAuthentication` Всегда вызывайте `UseSignalR` метод перед SignalR , чтобы иметь пользователя в `HttpContext`.

::: moniker-end

### <a name="cookie-authentication"></a>Проверка подлинности файлов cookie

В приложении на основе браузера проверка подлинности файлов cookie позволяет существующим учетным данным пользователя автоматически SignalR передаваться на подключения. При использовании клиента браузера дополнительная настройка не требуется. Если пользователь вошел в приложение, SignalR подключение автоматически наследует эту проверку подлинности.

Файлы cookie — это ориентированный на браузер способ отправки маркеров доступа, но клиенты, не являющиеся браузерами, могут их отправить. При использовании [клиента .NET](xref:signalr/dotnet-client) `Cookies` свойство можно настроить в `.WithUrl` вызове для предоставления файла cookie. Однако для использования проверки подлинности файлов cookie из клиента .NET приложение должно предоставить API для обмена данными проверки подлинности для файла cookie.

### <a name="bearer-token-authentication"></a>Проверка подлинности токена носителя

Клиент может предоставить маркер доступа вместо использования файла cookie. Сервер проверяет маркер и использует его для обнаружения пользователя. Эта проверка выполняется только при установленном соединении. Во время жизни соединения сервер не выполняет автоматическую повторную проверку, чтобы проверить отзыв маркера.

На сервере проверка подлинности токена носителя настраивается с помощью по [промежуточного слоя JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).

В клиенте JavaScript маркер можно указать с помощью параметра [акцесстокенфактори](xref:signalr/configuration#configure-bearer-authentication) .

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

В клиенте .NET существует аналогичное свойство [акцесстокенпровидер](xref:signalr/configuration#configure-bearer-authentication) , которое можно использовать для настройки маркера:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Предоставляемая функция токена доступа вызывается перед **каждым** HTTP-запросом SignalR, выполненным. Если необходимо обновить токен, чтобы подключение было активно (так как оно может истечь во время подключения), сделайте это в этой функции и возвратите обновленный маркер.

В стандартных веб-API токены носителя отправляются в заголовке HTTP. Однако SignalR не может задать эти заголовки в браузерах при использовании некоторых транспортов. При использовании соединений WebSockets и серверных событий токен передается как параметр строки запроса. Для поддержки этого на сервере требуется дополнительная настройка:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> Строка запроса используется в браузерах при соединении с веб-сокетами и событиями, отправленными сервером из-за ограничений API браузера. При использовании HTTPS значения строки запроса защищаются с помощью подключения TLS. Однако многие серверы заносить в журнал значения строки запроса. Дополнительные сведения см. [в разделе вопросы безопасности в SignalRASP.NET Core ](xref:signalr/security). SignalRиспользует заголовки для передачи токенов в средах, которые их поддерживают (например, клиенты .NET и Java).

### <a name="cookies-vs-bearer-tokens"></a>Файлы cookie и токены носителя 

Файлы cookie относятся только к браузерам. Их отправка из других типов клиентов повышает сложность по сравнению с отправкой токенов носителя. Следовательно, проверка подлинности файлов cookie не рекомендуется, если приложению не требуется проверять подлинность пользователей только от клиента браузера. Проверка подлинности маркера носителя является рекомендуемым подходом при использовании клиентов, отличных от клиента браузера.

### <a name="windows-authentication"></a>Проверка подлинности Windows

Если в приложении настроена [Проверка подлинности Windows](xref:security/authentication/windowsauth) , SignalR может использовать это удостоверение для защиты концентраторов. Однако для отправки сообщений отдельным пользователям необходимо добавить настраиваемого поставщика ИДЕНТИФИКАТОРов пользователей. Система проверки подлинности Windows не предоставляет утверждение "идентификатор имени". SignalRиспользует утверждение для определения имени пользователя.

Добавьте новый класс, который реализует `IUserIdProvider` и извлекает одно из утверждений пользователя для использования в качестве идентификатора. Например, чтобы использовать утверждение "Name" (имя пользователя Windows в форме `[Domain]\[Username]`), создайте следующий класс:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

`ClaimTypes.Name`Вместо можно использовать любое значение из `User` (например, идентификатор SID Windows и т. д.).

> [!NOTE]
> Выбранное значение должно быть уникальным для всех пользователей в системе. В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.

Зарегистрируйте этот компонент в `Startup.ConfigureServices` методе.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

В клиенте .NET проверка подлинности Windows должна быть включена путем установки свойства [уседефаулткредентиалс](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Проверка подлинности Windows поддерживается только клиентом браузера при использовании Microsoft Internet Explorer или Microsoft погранично.

### <a name="use-claims-to-customize-identity-handling"></a>Использование утверждений для настройки обработки удостоверений

Приложение, выполняющее проверку подлинности пользователей SignalR , может наследовать идентификаторы пользователей от утверждений пользователей. Чтобы указать, SignalR как создаются идентификаторы пользователей `IUserIdProvider` , реализуйте и зарегистрируйте реализацию.

В примере кода показано использование утверждений для выбора адреса электронной почты пользователя в качестве идентифицирующего свойства. 

> [!NOTE]
> Выбранное значение должно быть уникальным для всех пользователей в системе. В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

При регистрации учетной записи в базу данных `ClaimsTypes.Email` удостоверений ASP.NET добавляется утверждение с типом.

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Зарегистрируйте этот компонент в `Startup.ConfigureServices`.

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Авторизация пользователей для доступа к концентраторам и методам концентратора

По умолчанию все методы в концентраторе могут вызываться пользователем, не прошедшим проверку подлинности. Чтобы требовать проверку подлинности, примените к концентратору атрибут [авторизации](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) :

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Можно использовать аргументы конструктора и свойства `[Authorize]` атрибута, чтобы ограничить доступ только тем пользователям, которые соответствуют определенным [политикам авторизации](xref:security/authorization/policies). Например, при наличии пользовательской политики `MyAuthorizationPolicy` авторизации можно убедиться, что только пользователи, соответствующие этой политике, могут получить доступ к концентратору с помощью следующего кода:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Отдельные методы концентратора могут также `[Authorize]` применять атрибут. Если текущий пользователь не соответствует политике, примененной к методу, вызывающему объекту будет возвращена ошибка.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Использование обработчиков авторизации для настройки авторизации метода концентратора

SignalRпредоставляет пользовательский ресурс для обработчиков авторизации, когда методу концентратора требуется авторизация. Ресурс является экземпляром `HubInvocationContext`. `HubInvocationContext` Включает `HubCallerContext`, имя вызываемого метода концентратора и аргументы для метода концентратора.

Рассмотрим пример комнаты чатов, позволяющей нескольким организациям входить в систему через Azure Active Directory. Любой пользователь, у которого есть учетная запись Майкрософт, может войти в чат, но только члены владеющей Организации должны иметь возможность запрещать пользователям или просматривать историю разговора пользователей. Кроме того, может потребоваться ограничить некоторые функции от определенных пользователей. Использование обновленных функций в ASP.NET Core 3,0 — это вполне возможно. Обратите внимание `DomainRestrictedRequirement` , что служит в `IAuthorizationRequirement`качестве пользовательского. Теперь, когда `HubInvocationContext` передается параметр ресурса, внутренняя логика может проверить контекст, в котором вызывается концентратор, и принимать решения о том, чтобы разрешить пользователю выполнять отдельные методы концентратора.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

В `Startup.ConfigureServices`добавьте новую политику, предоставив настраиваемое `DomainRestrictedRequirement` требование в качестве параметра для создания `DomainRestricted` политики.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

В предыдущем примере `DomainRestrictedRequirement` класс является `IAuthorizationRequirement` и, и его собственным `AuthorizationHandler` для этого требования. Можно разделить эти два компонента на отдельные классы для разделения проблем. Преимуществом этого примера является отсутствие необходимости внедрять `AuthorizationHandler` во время запуска, так как требование и обработчик одинаковы.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Проверка подлинности токена носителя в ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Авторизация на основе ресурсов](xref:security/authorization/resourcebased)
