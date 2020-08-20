---
title: Проверка подлинности и авторизация в ASP.NET Core SignalR
author: bradygaster
description: Узнайте, как использовать проверку подлинности и авторизацию в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 3a2ae5c7bc4853bad7b94af0d26ad5cd0358688f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628941"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="c37a4-103">Проверка подлинности и авторизация в ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c37a4-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="c37a4-104">[Эндрю Стантон-медперсонала](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="c37a4-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="c37a4-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(описание загрузки)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c37a4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a><span data-ttu-id="c37a4-106">Проверка подлинности пользователей, подключающихся к SignalR концентратору</span><span class="sxs-lookup"><span data-stu-id="c37a4-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="c37a4-107">SignalR можно использовать с [проверкой подлинности ASP.NET Core](xref:security/authentication/identity) , чтобы связать пользователя с каждым соединением.</span><span class="sxs-lookup"><span data-stu-id="c37a4-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="c37a4-108">В центре данные проверки подлинности можно получить из свойства [хубконнектионконтекст. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) .</span><span class="sxs-lookup"><span data-stu-id="c37a4-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="c37a4-109">Проверка подлинности позволяет концентратору вызывать методы для всех соединений, связанных с пользователем.</span><span class="sxs-lookup"><span data-stu-id="c37a4-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="c37a4-110">Дополнительные сведения см. [в разделе Управление пользователями и группами SignalR в ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="c37a4-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="c37a4-111">Несколько подключений могут быть связаны с одним пользователем.</span><span class="sxs-lookup"><span data-stu-id="c37a4-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="c37a4-112">Ниже приведен пример `Startup.Configure` использования SignalR и ASP.NET Core проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="c37a4-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="c37a4-113">Порядок, в котором регистрируется SignalR и ASP.NET Core по промежуточного слоя проверки подлинности имеет значение.</span><span class="sxs-lookup"><span data-stu-id="c37a4-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="c37a4-114">Всегда вызывайте метод `UseAuthentication` перед `UseSignalR` , чтобы SignalR иметь пользователя в `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="c37a4-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="no-loccookie-authentication"></a><span data-ttu-id="c37a4-115">Cookie аутентификация</span><span class="sxs-lookup"><span data-stu-id="c37a4-115">Cookie authentication</span></span>

<span data-ttu-id="c37a4-116">В приложении, основанном на браузере, cookie Проверка подлинности позволяет существующим учетным данным пользователя автоматически передаваться на SignalR подключения.</span><span class="sxs-lookup"><span data-stu-id="c37a4-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="c37a4-117">При использовании клиента браузера дополнительная настройка не требуется.</span><span class="sxs-lookup"><span data-stu-id="c37a4-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="c37a4-118">Если пользователь вошел в приложение, SignalR подключение автоматически наследует эту проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="c37a4-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="c37a4-119">Cookies — это способ отправки маркеров доступа в браузере, но клиенты, не являющиеся браузерами, могут их отправить.</span><span class="sxs-lookup"><span data-stu-id="c37a4-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="c37a4-120">При использовании [клиента .NET](xref:signalr/dotnet-client) `Cookies` свойство можно настроить в `.WithUrl` вызове, чтобы предоставить cookie .</span><span class="sxs-lookup"><span data-stu-id="c37a4-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="c37a4-121">Однако для использования cookie проверки подлинности от клиента .NET приложение должно предоставить API для обмена данными проверки подлинности для cookie .</span><span class="sxs-lookup"><span data-stu-id="c37a4-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="c37a4-122">Проверка подлинности маркера носителя</span><span class="sxs-lookup"><span data-stu-id="c37a4-122">Bearer token authentication</span></span>

<span data-ttu-id="c37a4-123">Клиент может предоставить маркер доступа вместо использования cookie .</span><span class="sxs-lookup"><span data-stu-id="c37a4-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="c37a4-124">Сервер проверяет маркер и использует его для обнаружения пользователя.</span><span class="sxs-lookup"><span data-stu-id="c37a4-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="c37a4-125">Эта проверка выполняется только при установленном соединении.</span><span class="sxs-lookup"><span data-stu-id="c37a4-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="c37a4-126">Во время жизни соединения сервер не выполняет автоматическую повторную проверку, чтобы проверить отзыв маркера.</span><span class="sxs-lookup"><span data-stu-id="c37a4-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="c37a4-127">На сервере проверка подлинности маркера носителя настраивается с помощью [По промежуточного слоя JWT Bearer](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span><span class="sxs-lookup"><span data-stu-id="c37a4-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="c37a4-128">В клиенте JavaScript маркер можно указать с помощью параметра [акцесстокенфактори](xref:signalr/configuration#configure-bearer-authentication) .</span><span class="sxs-lookup"><span data-stu-id="c37a4-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="c37a4-129">В клиенте .NET существует аналогичное свойство [акцесстокенпровидер](xref:signalr/configuration#configure-bearer-authentication) , которое можно использовать для настройки маркера:</span><span class="sxs-lookup"><span data-stu-id="c37a4-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="c37a4-130">Предоставляемая функция токена доступа вызывается перед **каждым** HTTP-запросом, выполненным SignalR .</span><span class="sxs-lookup"><span data-stu-id="c37a4-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="c37a4-131">Если необходимо обновить токен, чтобы подключение было активно (так как оно может истечь во время подключения), сделайте это в этой функции и возвратите обновленный маркер.</span><span class="sxs-lookup"><span data-stu-id="c37a4-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="c37a4-132">В стандартных веб-API токены носителя отправляются в заголовке HTTP.</span><span class="sxs-lookup"><span data-stu-id="c37a4-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="c37a4-133">Однако SignalR не может задать эти заголовки в браузерах при использовании некоторых транспортов.</span><span class="sxs-lookup"><span data-stu-id="c37a4-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="c37a4-134">При использовании соединений WebSockets и серверных событий токен передается как параметр строки запроса.</span><span class="sxs-lookup"><span data-stu-id="c37a4-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="c37a4-135">Для поддержки этого на сервере требуется дополнительная настройка:</span><span class="sxs-lookup"><span data-stu-id="c37a4-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="c37a4-136">Строка запроса используется в браузерах при соединении с веб-сокетами и событиями, отправленными сервером из-за ограничений API браузера.</span><span class="sxs-lookup"><span data-stu-id="c37a4-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="c37a4-137">При использовании HTTPS значения строки запроса защищаются с помощью подключения TLS.</span><span class="sxs-lookup"><span data-stu-id="c37a4-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="c37a4-138">Однако многие серверы заносить в журнал значения строки запроса.</span><span class="sxs-lookup"><span data-stu-id="c37a4-138">However, many servers log query string values.</span></span> <span data-ttu-id="c37a4-139">Дополнительные сведения см. [в разделе вопросы безопасности в SignalR ASP.NET Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="c37a4-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> <span data-ttu-id="c37a4-140">SignalR использует заголовки для передачи токенов в средах, которые их поддерживают (например, клиенты .NET и Java).</span><span class="sxs-lookup"><span data-stu-id="c37a4-140">SignalR uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="no-loccookies-vs-bearer-tokens"></a><span data-ttu-id="c37a4-141">Cookieи токены носителя</span><span class="sxs-lookup"><span data-stu-id="c37a4-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="c37a4-142">Cookieотносятся только к браузерам.</span><span class="sxs-lookup"><span data-stu-id="c37a4-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="c37a4-143">Их отправка из других типов клиентов повышает сложность по сравнению с отправкой токенов носителя.</span><span class="sxs-lookup"><span data-stu-id="c37a4-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="c37a4-144">Следовательно, cookie Проверка подлинности не рекомендуется, если приложению требуется только проверка подлинности пользователей только от клиента браузера.</span><span class="sxs-lookup"><span data-stu-id="c37a4-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="c37a4-145">Проверка подлинности маркера носителя является рекомендуемым подходом при использовании клиентов, отличных от клиента браузера.</span><span class="sxs-lookup"><span data-stu-id="c37a4-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="c37a4-146">Проверка подлинности Windows</span><span class="sxs-lookup"><span data-stu-id="c37a4-146">Windows authentication</span></span>

<span data-ttu-id="c37a4-147">Если в приложении настроена [Проверка подлинности Windows](xref:security/authentication/windowsauth) , SignalR может использовать это удостоверение для защиты концентраторов.</span><span class="sxs-lookup"><span data-stu-id="c37a4-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="c37a4-148">Однако для отправки сообщений отдельным пользователям необходимо добавить настраиваемого поставщика ИДЕНТИФИКАТОРов пользователей.</span><span class="sxs-lookup"><span data-stu-id="c37a4-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="c37a4-149">Система проверки подлинности Windows не предоставляет утверждение "идентификатор имени".</span><span class="sxs-lookup"><span data-stu-id="c37a4-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="c37a4-150">SignalR использует утверждение для определения имени пользователя.</span><span class="sxs-lookup"><span data-stu-id="c37a4-150">SignalR uses the claim to determine the user name.</span></span>

<span data-ttu-id="c37a4-151">Добавьте новый класс, который реализует `IUserIdProvider` и извлекает одно из утверждений пользователя для использования в качестве идентификатора.</span><span class="sxs-lookup"><span data-stu-id="c37a4-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="c37a4-152">Например, чтобы использовать утверждение "Name" (имя пользователя Windows в форме `[Domain]\[Username]` ), создайте следующий класс:</span><span class="sxs-lookup"><span data-stu-id="c37a4-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="c37a4-153">Вместо `ClaimTypes.Name` можно использовать любое значение из `User` (например, идентификатор SID Windows и т. д.).</span><span class="sxs-lookup"><span data-stu-id="c37a4-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="c37a4-154">Выбранное значение должно быть уникальным для всех пользователей в системе.</span><span class="sxs-lookup"><span data-stu-id="c37a4-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="c37a4-155">В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="c37a4-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="c37a4-156">Зарегистрируйте этот компонент в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="c37a4-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="c37a4-157">В клиенте .NET проверка подлинности Windows должна быть включена путем установки свойства [уседефаулткредентиалс](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) :</span><span class="sxs-lookup"><span data-stu-id="c37a4-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="c37a4-158">Проверка подлинности Windows поддерживается в Internet Explorer и Microsoft погранично, но не во всех браузерах.</span><span class="sxs-lookup"><span data-stu-id="c37a4-158">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="c37a4-159">Например, в Chrome и Safari попытка использовать проверку подлинности Windows и WebSockets завершается ошибкой.</span><span class="sxs-lookup"><span data-stu-id="c37a4-159">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="c37a4-160">При сбое проверки подлинности Windows клиент пытается вернуться к другим транспортам, которые могут работать.</span><span class="sxs-lookup"><span data-stu-id="c37a4-160">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="c37a4-161">Использование утверждений для настройки обработки удостоверений</span><span class="sxs-lookup"><span data-stu-id="c37a4-161">Use claims to customize identity handling</span></span>

<span data-ttu-id="c37a4-162">Приложение, выполняющее проверку подлинности пользователей, может наследовать SignalR идентификаторы пользователей от утверждений пользователей.</span><span class="sxs-lookup"><span data-stu-id="c37a4-162">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="c37a4-163">Чтобы указать SignalR , как создаются идентификаторы пользователей, реализуйте `IUserIdProvider` и зарегистрируйте реализацию.</span><span class="sxs-lookup"><span data-stu-id="c37a4-163">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="c37a4-164">В примере кода показано использование утверждений для выбора адреса электронной почты пользователя в качестве идентифицирующего свойства.</span><span class="sxs-lookup"><span data-stu-id="c37a4-164">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="c37a4-165">Выбранное значение должно быть уникальным для всех пользователей в системе.</span><span class="sxs-lookup"><span data-stu-id="c37a4-165">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="c37a4-166">В противном случае сообщение, предназначенное для одного пользователя, может завершить работу другого пользователя.</span><span class="sxs-lookup"><span data-stu-id="c37a4-166">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="c37a4-167">При регистрации учетной записи в `ClaimsTypes.Email` базу данных удостоверений ASP.NET добавляется утверждение с типом.</span><span class="sxs-lookup"><span data-stu-id="c37a4-167">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="c37a4-168">Зарегистрируйте этот компонент в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c37a4-168">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="c37a4-169">Авторизация пользователей для доступа к концентраторам и методам концентратора</span><span class="sxs-lookup"><span data-stu-id="c37a4-169">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="c37a4-170">По умолчанию все методы в концентраторе могут вызываться пользователем, не прошедшим проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="c37a4-170">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="c37a4-171">Чтобы требовать проверку подлинности, примените к концентратору атрибут [авторизации](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) :</span><span class="sxs-lookup"><span data-stu-id="c37a4-171">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="c37a4-172">Можно использовать аргументы конструктора и свойства атрибута `[Authorize]`, чтобы ограничить доступ только пользователями, соответствующими определенным [политикам авторизации](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="c37a4-172">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="c37a4-173">Например, при наличии пользовательской политики авторизации `MyAuthorizationPolicy` можно убедиться, что только пользователи, соответствующие этой политике, могут получить доступ к концентратору с помощью следующего кода:</span><span class="sxs-lookup"><span data-stu-id="c37a4-173">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="c37a4-174">Отдельные методы концентратора могут `[Authorize]` также применять атрибут.</span><span class="sxs-lookup"><span data-stu-id="c37a4-174">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="c37a4-175">Если текущий пользователь не соответствует политике, примененной к методу, вызывающему объекту будет возвращена ошибка.</span><span class="sxs-lookup"><span data-stu-id="c37a4-175">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="c37a4-176">Использование обработчиков авторизации для настройки авторизации метода концентратора</span><span class="sxs-lookup"><span data-stu-id="c37a4-176">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="c37a4-177">SignalR предоставляет пользовательский ресурс для обработчиков авторизации, когда методу концентратора требуется авторизация.</span><span class="sxs-lookup"><span data-stu-id="c37a4-177">SignalR provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="c37a4-178">Ресурс является экземпляром `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="c37a4-178">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="c37a4-179">`HubInvocationContext`Включает `HubCallerContext` , имя вызываемого метода концентратора и аргументы для метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="c37a4-179">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="c37a4-180">Рассмотрим пример комнаты чатов, позволяющей нескольким организациям входить в систему через Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="c37a4-180">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="c37a4-181">Любой пользователь, у которого есть учетная запись Майкрософт, может войти в чат, но только члены владеющей Организации должны иметь возможность запрещать пользователям или просматривать историю разговора пользователей.</span><span class="sxs-lookup"><span data-stu-id="c37a4-181">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="c37a4-182">Кроме того, может потребоваться ограничить некоторые функции от определенных пользователей.</span><span class="sxs-lookup"><span data-stu-id="c37a4-182">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="c37a4-183">Использование обновленных функций в ASP.NET Core 3,0 — это вполне возможно.</span><span class="sxs-lookup"><span data-stu-id="c37a4-183">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="c37a4-184">Обратите внимание, что `DomainRestrictedRequirement` служит в качестве пользовательского `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="c37a4-184">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="c37a4-185">Теперь, когда `HubInvocationContext` передается параметр ресурса, внутренняя логика может проверить контекст, в котором вызывается концентратор, и принимать решения о том, чтобы разрешить пользователю выполнять отдельные методы концентратора.</span><span class="sxs-lookup"><span data-stu-id="c37a4-185">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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

<span data-ttu-id="c37a4-186">В `Startup.ConfigureServices` добавьте новую политику, предоставив настраиваемое требование в `DomainRestrictedRequirement` качестве параметра для создания `DomainRestricted` политики.</span><span class="sxs-lookup"><span data-stu-id="c37a4-186">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="c37a4-187">В предыдущем примере `DomainRestrictedRequirement` класс является `IAuthorizationRequirement` и, и его собственным `AuthorizationHandler` для этого требования.</span><span class="sxs-lookup"><span data-stu-id="c37a4-187">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="c37a4-188">Можно разделить эти два компонента на отдельные классы для разделения проблем.</span><span class="sxs-lookup"><span data-stu-id="c37a4-188">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="c37a4-189">Преимуществом этого примера является отсутствие необходимости внедрять `AuthorizationHandler` во время запуска, так как требование и обработчик одинаковы.</span><span class="sxs-lookup"><span data-stu-id="c37a4-189">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c37a4-190">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c37a4-190">Additional resources</span></span>

* [<span data-ttu-id="c37a4-191">Проверка подлинности токена носителя в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c37a4-191">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="c37a4-192">Авторизация на основе ресурсов</span><span class="sxs-lookup"><span data-stu-id="c37a4-192">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
