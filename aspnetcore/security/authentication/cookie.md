---
title: 'Использовать cookie проверку подлинности без ASP.NET Core Identity'
author: rick-anderson
description: 'Узнайте, как использовать cookie проверку подлинности без ASP.NET Core Identity .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: 'security/authentication/cookie'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061357"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="2f34d-103">Использовать cookie проверку подлинности без ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2f34d-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="2f34d-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="2f34d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2f34d-105">ASP.NET Core Identity — полноценный полнофункциональный поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="2f34d-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2f34d-106">Однако cookie поставщик проверки подлинности на основе не ASP.NET Core Identity может использоваться.</span><span class="sxs-lookup"><span data-stu-id="2f34d-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="2f34d-107">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="2f34d-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2f34d-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f34d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2f34d-109">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="2f34d-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2f34d-110">Используйте адрес **электронной почты** `maria.rodriguez@contoso.com` и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="2f34d-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="2f34d-111">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="2f34d-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2f34d-112">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2f34d-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="2f34d-113">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="2f34d-113">Configuration</span></span>

<span data-ttu-id="2f34d-114">В `Startup.ConfigureServices` методе создайте службы промежуточного слоя для проверки подлинности с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов и:</span><span class="sxs-lookup"><span data-stu-id="2f34d-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2f34d-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2f34d-116">`AuthenticationScheme` полезен при наличии нескольких экземпляров cookie проверки подлинности и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="2f34d-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2f34d-117">Присвоение параметру значения `AuthenticationScheme` [ Cookie аусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение " Cookie s" для схемы.</span><span class="sxs-lookup"><span data-stu-id="2f34d-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2f34d-118">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="2f34d-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2f34d-119">Схема проверки подлинности приложения отличается от cookie схемы проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2f34d-120">Если cookie Схема проверки подлинности не указана в <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , она использует `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="2f34d-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2f34d-121">По cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> умолчанию для свойства проверки подлинности задано значение `true` .</span><span class="sxs-lookup"><span data-stu-id="2f34d-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="2f34d-122">Проверка подлинности cookie разрешена, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="2f34d-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="2f34d-123">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="2f34d-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="2f34d-124">В `Startup.Configure` вызовите метод `UseAuthentication` и, `UseAuthorization` чтобы задать `HttpContext.User` свойство и запустить по промежуточного слоя авторизации для запросов.</span><span class="sxs-lookup"><span data-stu-id="2f34d-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="2f34d-125">Вызовите `UseAuthentication` `UseAuthorization` методы и перед вызовом `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="2f34d-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2f34d-126"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f34d-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="2f34d-127">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="2f34d-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="2f34d-128">Cookie По промежуточного слоя политики</span><span class="sxs-lookup"><span data-stu-id="2f34d-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="2f34d-129">По [ Cookie промежуточного слоя политики](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает cookie возможности политики.</span><span class="sxs-lookup"><span data-stu-id="2f34d-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="2f34d-130">Добавление по промежуточного слоя в конвейер обработки приложений зависит от порядка, &mdash; оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="2f34d-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="2f34d-131">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по Cookie промежуточного слоя политики для управления глобальными характеристиками cookie обработки и привязки к cookie обработчикам обработки при cookie добавлении или удалении s.</span><span class="sxs-lookup"><span data-stu-id="2f34d-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="2f34d-132">Значение по умолчанию <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> — `SameSiteMode.Lax` Разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="2f34d-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2f34d-133">Чтобы строго применить политику того же сайта `SameSiteMode.Strict` , установите `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="2f34d-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2f34d-134">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень cookie безопасности для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="2f34d-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2f34d-135">CookieПараметр по промежуточного слоя политики для `MinimumSameSitePolicy` может влиять на параметр в параметрах `Cookie.SameSite` в соответствии с `CookieAuthenticationOptions` приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="2f34d-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2f34d-136">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="2f34d-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2f34d-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="2f34d-137">Cookie.SameSite</span></span> | <span data-ttu-id="2f34d-138">Результат Cookie . Параметр SameSite</span><span class="sxs-lookup"><span data-stu-id="2f34d-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2f34d-139">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-139">SameSiteMode.None</span></span>     | <span data-ttu-id="2f34d-140">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-140">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-141">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-142">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="2f34d-143">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-143">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-144">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-145">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2f34d-146">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2f34d-147">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-147">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-148">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-149">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="2f34d-150">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-151">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-152">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2f34d-153">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2f34d-154">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-154">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-155">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-156">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="2f34d-157">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="2f34d-158">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="2f34d-159">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="2f34d-160">Создание проверки подлинности cookie</span><span class="sxs-lookup"><span data-stu-id="2f34d-160">Create an authentication cookie</span></span>

<span data-ttu-id="2f34d-161">Чтобы создать cookie данные о пользователе, создайте <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="2f34d-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2f34d-162">Сведения о пользователе сериализуются и хранятся в cookie .</span><span class="sxs-lookup"><span data-stu-id="2f34d-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="2f34d-163">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым параметром <xref:System.Security.Claims.Claim> s и вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="2f34d-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="2f34d-164">`SignInAsync` создает зашифрованный объект cookie и добавляет его к текущему ответу.</span><span class="sxs-lookup"><span data-stu-id="2f34d-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2f34d-165">Если параметр `AuthenticationScheme` не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2f34d-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="2f34d-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> используется по умолчанию только для нескольких конкретных путей, например для пути входа и путей выхода.</span><span class="sxs-lookup"><span data-stu-id="2f34d-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="2f34d-167">Дополнительные сведения см. в разделе [ Cookie источник аусентикатионхандлер](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="2f34d-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="2f34d-168">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f34d-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="2f34d-169">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2f34d-170">Выйти</span><span class="sxs-lookup"><span data-stu-id="2f34d-170">Sign out</span></span>

<span data-ttu-id="2f34d-171">Чтобы выйти из текущего пользователя и удалить его cookie , вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="2f34d-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="2f34d-172">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или " Cookie s") не используется в качестве схемы (например, "Contoso Cookie "), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f34d-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="2f34d-173">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2f34d-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="2f34d-174">Когда браузер закрывается, он автоматически удаляет сеансы на основе сеанса cookie (не постоянные cookie ), но cookie при закрытии отдельной вкладки никакие из них не удаляются.</span><span class="sxs-lookup"><span data-stu-id="2f34d-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="2f34d-175">Сервер не уведомлен о событиях закрытия вкладки или браузера.</span><span class="sxs-lookup"><span data-stu-id="2f34d-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2f34d-176">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="2f34d-176">React to back-end changes</span></span>

<span data-ttu-id="2f34d-177">После cookie создания объект cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="2f34d-178">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="2f34d-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="2f34d-179">cookieСистема проверки подлинности приложения продолжит обрабатывать запросы на основе проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="2f34d-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="2f34d-180">Пользователь остается в приложении, если проверка подлинности cookie является допустимой.</span><span class="sxs-lookup"><span data-stu-id="2f34d-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="2f34d-181"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Событие может использоваться для перехвата и переопределения проверки cookie удостоверения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2f34d-182">Проверка cookie при каждом запросе снижает риск аннулированных пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="2f34d-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2f34d-183">Один из подходов к cookie проверке основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="2f34d-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="2f34d-184">Если база данных не была изменена с момента cookie выпуска пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если cookie он все еще действителен.</span><span class="sxs-lookup"><span data-stu-id="2f34d-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2f34d-185">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="2f34d-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="2f34d-186">Когда пользователь обновляется в базе данных, ему `LastChanged` присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="2f34d-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2f34d-187">Чтобы сделать недействительным, cookie когда база данных изменяется на основе `LastChanged` значения, создайте объект cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="2f34d-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="2f34d-188">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="2f34d-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2f34d-189">Ниже приведен пример реализации `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2f34d-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="2f34d-190">Зарегистрируйте экземпляр событий во время cookie регистрации службы в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="2f34d-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2f34d-191">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной области для `CustomCookieAuthenticationEvents` класса:</span><span class="sxs-lookup"><span data-stu-id="2f34d-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="2f34d-192">Рассмотрим ситуацию, в которой имя пользователя обновляется, &mdash; а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="2f34d-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2f34d-193">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true` .</span><span class="sxs-lookup"><span data-stu-id="2f34d-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2f34d-194">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="2f34d-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2f34d-195">Проверка подлинности cookie для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="2f34d-196">Постоянный cookie s</span><span class="sxs-lookup"><span data-stu-id="2f34d-196">Persistent cookies</span></span>

<span data-ttu-id="2f34d-197">Может потребоваться, cookie чтобы объект сохранялся между сеансами браузера.</span><span class="sxs-lookup"><span data-stu-id="2f34d-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2f34d-198">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="2f34d-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="2f34d-199">В следующем фрагменте кода создается удостоверение cookie , которое остается нестабильным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="2f34d-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2f34d-200">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="2f34d-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2f34d-201">Если cookie срок действия истекает при закрытии браузера, браузер очищается cookie после перезапуска.</span><span class="sxs-lookup"><span data-stu-id="2f34d-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="2f34d-202">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="2f34d-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="2f34d-203">Абсолютный cookie срок действия</span><span class="sxs-lookup"><span data-stu-id="2f34d-203">Absolute cookie expiration</span></span>

<span data-ttu-id="2f34d-204">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="2f34d-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="2f34d-205">Для создания постоянного cookie `IsPersistent` необходимо также задать значение.</span><span class="sxs-lookup"><span data-stu-id="2f34d-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="2f34d-206">В противном случае объект cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f34d-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="2f34d-207">Если задан `ExpiresUtc` параметр, он переопределяет значение <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> параметра <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , если оно задано.</span><span class="sxs-lookup"><span data-stu-id="2f34d-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="2f34d-208">В следующем фрагменте кода создается удостоверение cookie , которое продолжается в течение 20 минут.</span><span class="sxs-lookup"><span data-stu-id="2f34d-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2f34d-209">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="2f34d-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f34d-210">ASP.NET Core Identity — полноценный полнофункциональный поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="2f34d-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="2f34d-211">Однако cookie поставщик проверки подлинности на основе не ASP.NET Core Identity может использоваться.</span><span class="sxs-lookup"><span data-stu-id="2f34d-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="2f34d-212">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="2f34d-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="2f34d-213">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2f34d-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2f34d-214">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="2f34d-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="2f34d-215">Используйте адрес **электронной почты** `maria.rodriguez@contoso.com` и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="2f34d-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="2f34d-216">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="2f34d-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="2f34d-217">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2f34d-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="2f34d-218">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="2f34d-218">Configuration</span></span>

<span data-ttu-id="2f34d-219">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для [Microsoft. AspNetCore. Authentication. Cookie пакет.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)</span><span class="sxs-lookup"><span data-stu-id="2f34d-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="2f34d-220">В `Startup.ConfigureServices` методе создайте службу промежуточного слоя проверки подлинности с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов и:</span><span class="sxs-lookup"><span data-stu-id="2f34d-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="2f34d-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="2f34d-222">`AuthenticationScheme` полезен при наличии нескольких экземпляров cookie проверки подлинности и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="2f34d-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="2f34d-223">Присвоение параметру значения `AuthenticationScheme` [ Cookie аусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение " Cookie s" для схемы.</span><span class="sxs-lookup"><span data-stu-id="2f34d-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="2f34d-224">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="2f34d-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="2f34d-225">Схема проверки подлинности приложения отличается от cookie схемы проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="2f34d-226">Если cookie Схема проверки подлинности не указана в <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , она использует `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="2f34d-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="2f34d-227">По cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> умолчанию для свойства проверки подлинности задано значение `true` .</span><span class="sxs-lookup"><span data-stu-id="2f34d-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="2f34d-228">Проверка подлинности cookie разрешена, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="2f34d-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="2f34d-229">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="2f34d-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="2f34d-230">В `Startup.Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство.</span><span class="sxs-lookup"><span data-stu-id="2f34d-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="2f34d-231">Вызовите `UseAuthentication` метод перед вызовом `UseMvcWithDefaultRoute` или `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="2f34d-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="2f34d-232"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f34d-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="2f34d-233">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="2f34d-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="2f34d-234">Cookie По промежуточного слоя политики</span><span class="sxs-lookup"><span data-stu-id="2f34d-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="2f34d-235">По [ Cookie промежуточного слоя политики](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает cookie возможности политики.</span><span class="sxs-lookup"><span data-stu-id="2f34d-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="2f34d-236">Добавление по промежуточного слоя в конвейер обработки приложений зависит от порядка, &mdash; оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="2f34d-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="2f34d-237">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по Cookie промежуточного слоя политики для управления глобальными характеристиками cookie обработки и привязки к cookie обработчикам обработки при cookie добавлении или удалении s.</span><span class="sxs-lookup"><span data-stu-id="2f34d-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="2f34d-238">Значение по умолчанию <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> — `SameSiteMode.Lax` Разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="2f34d-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="2f34d-239">Чтобы строго применить политику того же сайта `SameSiteMode.Strict` , установите `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="2f34d-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="2f34d-240">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень cookie безопасности для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="2f34d-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="2f34d-241">CookieПараметр по промежуточного слоя политики для `MinimumSameSitePolicy` может влиять на параметр в параметрах `Cookie.SameSite` в соответствии с `CookieAuthenticationOptions` приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="2f34d-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="2f34d-242">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="2f34d-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="2f34d-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="2f34d-243">Cookie.SameSite</span></span> | <span data-ttu-id="2f34d-244">Результат Cookie . Параметр SameSite</span><span class="sxs-lookup"><span data-stu-id="2f34d-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="2f34d-245">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-245">SameSiteMode.None</span></span>     | <span data-ttu-id="2f34d-246">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-246">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-247">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-248">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="2f34d-249">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-249">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-250">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-251">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2f34d-252">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="2f34d-253">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-253">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-254">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-255">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="2f34d-256">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-257">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-258">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="2f34d-259">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="2f34d-260">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="2f34d-260">SameSiteMode.None</span></span><br><span data-ttu-id="2f34d-261">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="2f34d-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="2f34d-262">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="2f34d-263">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="2f34d-264">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="2f34d-265">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="2f34d-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="2f34d-266">Создание проверки подлинности cookie</span><span class="sxs-lookup"><span data-stu-id="2f34d-266">Create an authentication cookie</span></span>

<span data-ttu-id="2f34d-267">Чтобы создать cookie данные о пользователе, создайте <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="2f34d-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2f34d-268">Сведения о пользователе сериализуются и хранятся в cookie .</span><span class="sxs-lookup"><span data-stu-id="2f34d-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="2f34d-269">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым параметром <xref:System.Security.Claims.Claim> s и вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="2f34d-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="2f34d-270">`SignInAsync` создает зашифрованный объект cookie и добавляет его к текущему ответу.</span><span class="sxs-lookup"><span data-stu-id="2f34d-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="2f34d-271">Если параметр `AuthenticationScheme` не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2f34d-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="2f34d-272">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2f34d-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="2f34d-273">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="2f34d-274">Выйти</span><span class="sxs-lookup"><span data-stu-id="2f34d-274">Sign out</span></span>

<span data-ttu-id="2f34d-275">Чтобы выйти из текущего пользователя и удалить его cookie , вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="2f34d-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="2f34d-276">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или " Cookie s") не используется в качестве схемы (например, "Contoso Cookie "), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f34d-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="2f34d-277">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2f34d-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="2f34d-278">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="2f34d-278">React to back-end changes</span></span>

<span data-ttu-id="2f34d-279">После cookie создания объект cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="2f34d-280">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="2f34d-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="2f34d-281">cookieСистема проверки подлинности приложения продолжит обрабатывать запросы на основе проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="2f34d-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="2f34d-282">Пользователь остается в приложении, если проверка подлинности cookie является допустимой.</span><span class="sxs-lookup"><span data-stu-id="2f34d-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="2f34d-283"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Событие может использоваться для перехвата и переопределения проверки cookie удостоверения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="2f34d-284">Проверка cookie при каждом запросе снижает риск аннулированных пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="2f34d-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="2f34d-285">Один из подходов к cookie проверке основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="2f34d-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="2f34d-286">Если база данных не была изменена с момента cookie выпуска пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если cookie он все еще действителен.</span><span class="sxs-lookup"><span data-stu-id="2f34d-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="2f34d-287">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="2f34d-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="2f34d-288">Когда пользователь обновляется в базе данных, ему `LastChanged` присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="2f34d-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="2f34d-289">Чтобы сделать недействительным, cookie когда база данных изменяется на основе `LastChanged` значения, создайте объект cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="2f34d-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="2f34d-290">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="2f34d-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="2f34d-291">Ниже приведен пример реализации `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="2f34d-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="2f34d-292">Зарегистрируйте экземпляр событий во время cookie регистрации службы в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="2f34d-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2f34d-293">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной области для `CustomCookieAuthenticationEvents` класса:</span><span class="sxs-lookup"><span data-stu-id="2f34d-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="2f34d-294">Рассмотрим ситуацию, в которой имя пользователя обновляется, &mdash; а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="2f34d-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="2f34d-295">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true` .</span><span class="sxs-lookup"><span data-stu-id="2f34d-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2f34d-296">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="2f34d-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="2f34d-297">Проверка подлинности cookie для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="2f34d-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="2f34d-298">Постоянный cookie s</span><span class="sxs-lookup"><span data-stu-id="2f34d-298">Persistent cookies</span></span>

<span data-ttu-id="2f34d-299">Может потребоваться, cookie чтобы объект сохранялся между сеансами браузера.</span><span class="sxs-lookup"><span data-stu-id="2f34d-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="2f34d-300">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="2f34d-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="2f34d-301">В следующем фрагменте кода создается удостоверение cookie , которое остается нестабильным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="2f34d-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="2f34d-302">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="2f34d-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="2f34d-303">Если cookie срок действия истекает при закрытии браузера, браузер очищается cookie после перезапуска.</span><span class="sxs-lookup"><span data-stu-id="2f34d-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="2f34d-304">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="2f34d-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="2f34d-305">Абсолютный cookie срок действия</span><span class="sxs-lookup"><span data-stu-id="2f34d-305">Absolute cookie expiration</span></span>

<span data-ttu-id="2f34d-306">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="2f34d-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="2f34d-307">Для создания постоянного cookie `IsPersistent` необходимо также задать значение.</span><span class="sxs-lookup"><span data-stu-id="2f34d-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="2f34d-308">В противном случае объект cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f34d-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="2f34d-309">Если задан `ExpiresUtc` параметр, он переопределяет значение <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> параметра <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , если оно задано.</span><span class="sxs-lookup"><span data-stu-id="2f34d-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="2f34d-310">В следующем фрагменте кода создается удостоверение cookie , которое продолжается в течение 20 минут.</span><span class="sxs-lookup"><span data-stu-id="2f34d-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="2f34d-311">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="2f34d-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2f34d-312">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2f34d-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="2f34d-313">Проверки ролей на основе политик</span><span class="sxs-lookup"><span data-stu-id="2f34d-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
