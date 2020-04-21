---
title: Защита ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте о защите приложений Blazor WebAssemlby как одностраничных приложений (SPA).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661785"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="28c6f-103">Защита ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="28c6f-103">Secure ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="28c6f-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="28c6f-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="28c6f-105">Защита приложений Blazor WebAssembly обеспечивается аналогично защите одностраничных приложений (SPA).</span><span class="sxs-lookup"><span data-stu-id="28c6f-105">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="28c6f-106">Существует несколько подходов к проверке подлинности пользователей в одностраничных приложениях, но наиболее распространенным и комплексным является использование реализации на основе [протокола OAuth 2.0](https://oauth.net/), например [Open ID Connect (OIDC)](https://openid.net/connect/).</span><span class="sxs-lookup"><span data-stu-id="28c6f-106">There are several approaches for authenticating users to SPAs, but the most common and comprehensive approach is to use an implementation based on the [OAuth 2.0 protocol](https://oauth.net/), such as [Open ID Connect (OIDC)](https://openid.net/connect/).</span></span>

## <a name="authentication-library"></a><span data-ttu-id="28c6f-107">Библиотека проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="28c6f-107">Authentication library</span></span>

Blazor<span data-ttu-id="28c6f-108"> WebAssembly поддерживает проверку подлинности и авторизацию приложений с использованием OIDC с помощью библиотеки `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="28c6f-108"> WebAssembly supports authenticating and authorizing apps using OIDC via the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library.</span></span> <span data-ttu-id="28c6f-109">Библиотека предоставляет набор примитивов для простой проверки подлинности на серверных компонентах ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28c6f-109">The library provides a set of primitives for seamlessly authenticating against ASP.NET Core backends.</span></span> <span data-ttu-id="28c6f-110">Библиотека включает в себя ASP.NET Core Identity с поддержкой авторизации API на основе [сервера удостоверений](https://identityserver.io/).</span><span class="sxs-lookup"><span data-stu-id="28c6f-110">The library integrates ASP.NET Core Identity with API authorization support built on top of [Identity Server](https://identityserver.io/).</span></span> <span data-ttu-id="28c6f-111">Библиотеку можно использовать для проверки подлинности любого стороннего поставщика удостоверений (IP), который поддерживает OIDC (их называют поставщиками OpenID (OP)).</span><span class="sxs-lookup"><span data-stu-id="28c6f-111">The library can authenticate against any third-party Identity Provider (IP) that supports OIDC, which are called OpenID Providers (OP).</span></span>

<span data-ttu-id="28c6f-112">В основе поддержки проверки подлинности в Blazor WebAssembly лежит библиотека *oidc-client.js*, которая используется для управления сведениями о базовом протоколе проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="28c6f-112">The authentication support in Blazor WebAssembly is built on top of the *oidc-client.js* library, which is used to handle the underlying authentication protocol details.</span></span>

<span data-ttu-id="28c6f-113">Кроме того, доступны другие варианты проверки подлинности одностраничных приложений, например использование файлов cookie SameSite.</span><span class="sxs-lookup"><span data-stu-id="28c6f-113">Other options for authenticating SPAs exist, such as the use of SameSite cookies.</span></span> <span data-ttu-id="28c6f-114">Однако технический проект BlazorWebAssembly реализован на основе OAuth и OIDC и представляет собой наилучший вариант для проверки подлинности в приложениях Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="28c6f-114">However, the engineering design of Blazor WebAssembly is settled on OAuth and OIDC as the best option for authentication in Blazor WebAssembly apps.</span></span> <span data-ttu-id="28c6f-115">По соображениям безопасности и в силу функциональных причин вместо [проверки подлинности на основе файлов cookie](xref:security/anti-request-forgery#cookie-based-authentication) была выбрана [проверка подлинности на основе токенов](xref:security/anti-request-forgery#token-based-authentication) на базе [JSON Web Tokens (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).</span><span class="sxs-lookup"><span data-stu-id="28c6f-115">[Token-based authentication](xref:security/anti-request-forgery#token-based-authentication) based on [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) was chosen over [cookie-based authentication](xref:security/anti-request-forgery#cookie-based-authentication) for functional and security reasons:</span></span>

* <span data-ttu-id="28c6f-116">Использование протокола на основе токенов позволяет уменьшить контактную зону атак, так как токены отправляются не во всех запросах.</span><span class="sxs-lookup"><span data-stu-id="28c6f-116">Using a token-based protocol offers a smaller attack surface area, as the tokens aren't sent in all requests.</span></span>
* <span data-ttu-id="28c6f-117">Конечные точки сервера не нуждаются в защите от [подделки межсайтовых запросов (CSRF)](xref:security/anti-request-forgery), так как токены отправляются явным образом.</span><span class="sxs-lookup"><span data-stu-id="28c6f-117">Server endpoints don't require protection against [Cross-Site Request Forgery (CSRF)](xref:security/anti-request-forgery) because the tokens are sent explicitly.</span></span> <span data-ttu-id="28c6f-118">Это позволяет размещать приложения Blazor WebAssembly параллельно с приложениями MVC или Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="28c6f-118">This allows you to host Blazor WebAssembly apps alongside MVC or Razor pages apps.</span></span>
* <span data-ttu-id="28c6f-119">Разрешения токенов более узкие, чем разрешения файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="28c6f-119">Tokens have narrower permissions than cookies.</span></span> <span data-ttu-id="28c6f-120">Например, токены нельзя использовать для управления учетной записью пользователя или изменения пароля пользователя, если такие функции не реализованы явным образом.</span><span class="sxs-lookup"><span data-stu-id="28c6f-120">For example, tokens can't be used to manage the user account or change a user's password unless such functionality is explicitly implemented.</span></span>
* <span data-ttu-id="28c6f-121">У токенов короткий срок жизни (по умолчанию — один час), что ограничивает распространение атаки.</span><span class="sxs-lookup"><span data-stu-id="28c6f-121">Tokens have a short lifetime, one hour by default, which limits the attack window.</span></span> <span data-ttu-id="28c6f-122">Токены можно отозвать в любое время.</span><span class="sxs-lookup"><span data-stu-id="28c6f-122">Tokens can also be revoked at any time.</span></span>
* <span data-ttu-id="28c6f-123">Автономные токены JWT гарантируют выполнение надлежащего процесса проверки подлинности на клиенте и сервере.</span><span class="sxs-lookup"><span data-stu-id="28c6f-123">Self-contained JWTs offer guarantees to the client and server about the authentication process.</span></span> <span data-ttu-id="28c6f-124">Например, на клиенте есть средства для обнаружения и проверки допустимости получаемых токенов, а также подтверждения их выпуска в рамках данного процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="28c6f-124">For example, a client has the means to detect and validate that the tokens it receives are legitimate and were emitted as part of a given authentication process.</span></span> <span data-ttu-id="28c6f-125">Если третья сторона пытается изменить токен в ходе процесса проверки подлинности, клиент может обнаружить измененный токен и не использовать его.</span><span class="sxs-lookup"><span data-stu-id="28c6f-125">If a third party attempts to switch a token in the middle of the authentication process, the client can detect the switched token and avoid using it.</span></span>
* <span data-ttu-id="28c6f-126">Токены с OAuth и OIDC обеспечивают безопасность приложения вне зависимости от поведения агента пользователя.</span><span class="sxs-lookup"><span data-stu-id="28c6f-126">Tokens with OAuth and OIDC don't rely on the user agent behaving correctly to ensure that the app is secure.</span></span>
* <span data-ttu-id="28c6f-127">Протоколы на основе токенов, такие как OAuth и OIDC, позволяют выполнять проверку подлинности и авторизацию размещенных и автономных приложений с одинаковым набором характеристик безопасности.</span><span class="sxs-lookup"><span data-stu-id="28c6f-127">Token-based protocols, such as OAuth and OIDC, allow for authenticating and authorizing hosted and standalone apps with the same set of security characteristics.</span></span>

## <a name="authentication-process-with-oidc"></a><span data-ttu-id="28c6f-128">Процесс проверки подлинности с использованием OIDC</span><span class="sxs-lookup"><span data-stu-id="28c6f-128">Authentication process with OIDC</span></span>

<span data-ttu-id="28c6f-129">Библиотека `Microsoft.AspNetCore.Components.WebAssembly.Authentication` предлагает несколько примитивов для реализации проверки подлинности и авторизации с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="28c6f-129">The `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library offers several primitives to implement authentication and authorization using OIDC.</span></span> <span data-ttu-id="28c6f-130">В общих чертах проверка подлинности работает следующим образом.</span><span class="sxs-lookup"><span data-stu-id="28c6f-130">In broad terms, authentication works as follows:</span></span>

* <span data-ttu-id="28c6f-131">Когда анонимный пользователь нажимает кнопку входа или запрашивает страницу с примененным атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), он перенаправляется на страницу входа приложения (`/authentication/login`).</span><span class="sxs-lookup"><span data-stu-id="28c6f-131">When an anonymous user selects the login button or requests a page with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied, the user is redirected to the app's login page (`/authentication/login`).</span></span>
* <span data-ttu-id="28c6f-132">На странице входа библиотека проверки подлинности готовится к перенаправлению на конечную точку авторизации.</span><span class="sxs-lookup"><span data-stu-id="28c6f-132">In the login page, the authentication library prepares for a redirect to the authorization endpoint.</span></span> <span data-ttu-id="28c6f-133">Конечная точка авторизации находится вне приложения Blazor WebAssembly и может размещаться в отдельном источнике.</span><span class="sxs-lookup"><span data-stu-id="28c6f-133">The authorization endpoint is outside of the Blazor WebAssembly app and can be hosted at a separate origin.</span></span> <span data-ttu-id="28c6f-134">Конечная точка определяет, прошел ли пользователь проверку подлинности, и выдает в ответ один или несколько токенов.</span><span class="sxs-lookup"><span data-stu-id="28c6f-134">The endpoint is responsible for determining whether the user is authenticated and for issuing one or more tokens in response.</span></span> <span data-ttu-id="28c6f-135">Библиотека проверки подлинности предоставляет обратный вызов входа для получения ответа проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="28c6f-135">The authentication library provides a login callback to receive the authentication response.</span></span>
  * <span data-ttu-id="28c6f-136">Если пользователь не прошел проверку подлинности, он перенаправляется в базовую систему проверки подлинности. Обычно это ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="28c6f-136">If the user isn't authenticated, the user is redirected to the underlying authentication system, which is usually ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="28c6f-137">Если пользователь уже прошел проверку подлинности, конечная точка авторизации создает соответствующие токены и перенаправляет браузер назад в конечную точку обратного вызова входа (`/authentication/login-callback`).</span><span class="sxs-lookup"><span data-stu-id="28c6f-137">If the user was already authenticated, the authorization endpoint generates the appropriate tokens and redirects the browser back to the login callback endpoint (`/authentication/login-callback`).</span></span>
* <span data-ttu-id="28c6f-138">Когда приложение Blazor WebAssembly загружает конечную точку обратного вызова входа (`/authentication/login-callback`), происходит обработка ответа проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="28c6f-138">When the Blazor WebAssembly app loads the login callback endpoint (`/authentication/login-callback`), the authentication response is processed.</span></span>
  * <span data-ttu-id="28c6f-139">Если процесс проверки подлинности завершается успешно, пользователь проходит проверку подлинности и при необходимости перенаправляется по запрошенному исходному защищенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="28c6f-139">If the authentication process completes successfully, the user is authenticated and optionally sent back to the original protected URL that the user requested.</span></span>
  * <span data-ttu-id="28c6f-140">Если по какой-либо причине проверка подлинности завершается сбоем, пользователь направляется на страницу ошибки входа (`/authentication/login-failed`), и выводится сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="28c6f-140">If the authentication process fails for any reason, the user is sent to the login failed page (`/authentication/login-failed`), and an error is displayed.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="28c6f-141">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="28c6f-141">Support prerendering with authentication</span></span>

<span data-ttu-id="28c6f-142">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="28c6f-142">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="28c6f-143">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="28c6f-143">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="28c6f-144">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="28c6f-144">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="28c6f-145">В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="28c6f-145">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="28c6f-146">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="28c6f-146">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="28c6f-147">В методе `Startup.Configure` серверного приложения замените `endpoints.MapFallbackToFile("index.html")` на `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="28c6f-147">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="28c6f-148">В серверном приложении создайте папку *Pages*, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="28c6f-148">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="28c6f-149">В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="28c6f-149">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="28c6f-150">Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="28c6f-150">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="28c6f-151">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="28c6f-151">Update the file's contents:</span></span>

* <span data-ttu-id="28c6f-152">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="28c6f-152">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="28c6f-153">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="28c6f-153">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="28c6f-154">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="28c6f-154">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="28c6f-155">При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="28c6f-155">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="28c6f-156">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="28c6f-156">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="28c6f-157">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="28c6f-157">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="28c6f-158">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="28c6f-158">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="28c6f-159">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="28c6f-159">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="28c6f-160">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="28c6f-160">In this scenario:</span></span>

* <span data-ttu-id="28c6f-161">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="28c6f-161">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="28c6f-162">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="28c6f-162">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="28c6f-163">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="28c6f-163">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="28c6f-164">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="28c6f-164">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="28c6f-165">Настройте удостоверение с помощью стороннего поставщика входа.</span><span class="sxs-lookup"><span data-stu-id="28c6f-165">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="28c6f-166">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="28c6f-166">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="28c6f-167">При входе пользователя в систему удостоверение собирает токены доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="28c6f-167">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="28c6f-168">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="28c6f-168">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="28c6f-169">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="28c6f-169">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="28c6f-170">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="28c6f-170">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="28c6f-171">Затем воспользуйтесь сторонним токеном доступа для вызова ресурсов стороннего API непосредственно из удостоверения на клиенте.</span><span class="sxs-lookup"><span data-stu-id="28c6f-171">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="28c6f-172">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="28c6f-172">We don't recommend this approach.</span></span> <span data-ttu-id="28c6f-173">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="28c6f-173">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="28c6f-174">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="28c6f-174">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="28c6f-175">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="28c6f-175">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="28c6f-176">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="28c6f-176">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="28c6f-177">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="28c6f-177">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="28c6f-178">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="28c6f-178">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="28c6f-179">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="28c6f-179">Make an API call from the client to the server API.</span></span> <span data-ttu-id="28c6f-180">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="28c6f-180">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="28c6f-181">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="28c6f-181">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="28c6f-182">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="28c6f-182">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="28c6f-183">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="28c6f-183">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
