---
title: Использовать cookie проверку подлинности без ASP.NET Core Identity
author: rick-anderson
description: Узнайте, как использовать cookie проверку подлинности без ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 04d2f0d289e2c9ec13aeb880df47240bec19d3ec
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876767"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="e2b15-103">Использовать cookie проверку подлинности без ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e2b15-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e2b15-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="e2b15-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e2b15-105">ASP.NET Core Identity — полноценный полнофункциональный поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="e2b15-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e2b15-106">Однако cookie поставщик проверки подлинности на основе не ASP.NET Core Identity может использоваться.</span><span class="sxs-lookup"><span data-stu-id="e2b15-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e2b15-107">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e2b15-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e2b15-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2b15-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e2b15-109">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="e2b15-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e2b15-110">Используйте адрес **электронной почты** `maria.rodriguez@contoso.com` и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="e2b15-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e2b15-111">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="e2b15-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e2b15-112">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2b15-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e2b15-113">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="e2b15-113">Configuration</span></span>

<span data-ttu-id="e2b15-114">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для [Microsoft. AspNetCore. Authentication. Cookie пакет.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)</span><span class="sxs-lookup"><span data-stu-id="e2b15-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e2b15-115">В `Startup.ConfigureServices` методе создайте службы промежуточного слоя для проверки подлинности с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов и:</span><span class="sxs-lookup"><span data-stu-id="e2b15-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e2b15-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e2b15-117">`AuthenticationScheme` полезен при наличии нескольких экземпляров cookie проверки подлинности и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="e2b15-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e2b15-118">Присвоение параметру значения `AuthenticationScheme` [ Cookie аусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение " Cookie s" для схемы.</span><span class="sxs-lookup"><span data-stu-id="e2b15-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e2b15-119">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="e2b15-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e2b15-120">Схема проверки подлинности приложения отличается от cookie схемы проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e2b15-121">Если cookie Схема проверки подлинности не указана в <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , она использует `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="e2b15-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e2b15-122">По cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> умолчанию для свойства проверки подлинности задано значение `true` .</span><span class="sxs-lookup"><span data-stu-id="e2b15-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e2b15-123">Проверка подлинности cookie разрешена, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="e2b15-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e2b15-124">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="e2b15-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e2b15-125">В `Startup.Configure` вызовите метод `UseAuthentication` и, `UseAuthorization` чтобы задать `HttpContext.User` свойство и запустить по промежуточного слоя авторизации для запросов.</span><span class="sxs-lookup"><span data-stu-id="e2b15-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="e2b15-126">Вызовите `UseAuthentication` `UseAuthorization` методы и перед вызовом `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="e2b15-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e2b15-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e2b15-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e2b15-128">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="e2b15-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e2b15-129">Cookie По промежуточного слоя политики</span><span class="sxs-lookup"><span data-stu-id="e2b15-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="e2b15-130">По [ Cookie промежуточного слоя политики](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает cookie возможности политики.</span><span class="sxs-lookup"><span data-stu-id="e2b15-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e2b15-131">Добавление по промежуточного слоя в конвейер обработки приложений зависит от порядка, &mdash; оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="e2b15-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e2b15-132">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по Cookie промежуточного слоя политики для управления глобальными характеристиками cookie обработки и привязки к cookie обработчикам обработки при cookie добавлении или удалении s.</span><span class="sxs-lookup"><span data-stu-id="e2b15-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e2b15-133">Значение по умолчанию <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> — `SameSiteMode.Lax` Разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="e2b15-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e2b15-134">Чтобы строго применить политику того же сайта `SameSiteMode.Strict` , установите `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e2b15-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e2b15-135">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень cookie безопасности для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="e2b15-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e2b15-136">CookieПараметр по промежуточного слоя политики для `MinimumSameSitePolicy` может влиять на параметр в параметрах `Cookie.SameSite` в соответствии с `CookieAuthenticationOptions` приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="e2b15-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e2b15-137">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="e2b15-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e2b15-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="e2b15-138">Cookie.SameSite</span></span> | <span data-ttu-id="e2b15-139">Результат Cookie . Параметр SameSite</span><span class="sxs-lookup"><span data-stu-id="e2b15-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e2b15-140">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-140">SameSiteMode.None</span></span>     | <span data-ttu-id="e2b15-141">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-141">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-142">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-143">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="e2b15-144">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-144">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-145">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-146">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e2b15-147">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e2b15-148">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-148">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-149">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-150">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="e2b15-151">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-152">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-153">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e2b15-154">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e2b15-155">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-155">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-156">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-157">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="e2b15-158">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="e2b15-159">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="e2b15-160">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e2b15-161">Создание проверки подлинности cookie</span><span class="sxs-lookup"><span data-stu-id="e2b15-161">Create an authentication cookie</span></span>

<span data-ttu-id="e2b15-162">Чтобы создать cookie данные о пользователе, создайте <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e2b15-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e2b15-163">Сведения о пользователе сериализуются и хранятся в cookie .</span><span class="sxs-lookup"><span data-stu-id="e2b15-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e2b15-164">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым параметром <xref:System.Security.Claims.Claim> s и вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="e2b15-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="e2b15-165">`SignInAsync` создает зашифрованный объект cookie и добавляет его к текущему ответу.</span><span class="sxs-lookup"><span data-stu-id="e2b15-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e2b15-166">Если параметр `AuthenticationScheme` не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2b15-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e2b15-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> используется по умолчанию только для нескольких конкретных путей, например для пути входа и путей выхода.</span><span class="sxs-lookup"><span data-stu-id="e2b15-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="e2b15-168">Дополнительные сведения см. в разделе [ Cookie источник аусентикатионхандлер](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="e2b15-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="e2b15-169">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2b15-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e2b15-170">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e2b15-171">Выйти</span><span class="sxs-lookup"><span data-stu-id="e2b15-171">Sign out</span></span>

<span data-ttu-id="e2b15-172">Чтобы выйти из текущего пользователя и удалить его cookie , вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e2b15-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e2b15-173">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или " Cookie s") не используется в качестве схемы (например, "Contoso Cookie "), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e2b15-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e2b15-174">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2b15-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="e2b15-175">Сервер не управляет браузером клиентов.</span><span class="sxs-lookup"><span data-stu-id="e2b15-175">The server has no control of the clients browser.</span></span> <span data-ttu-id="e2b15-176">Если пользователь закрывает браузер или вкладку, сервер не может выйти из этого пользователя.</span><span class="sxs-lookup"><span data-stu-id="e2b15-176">If the user closes the browser or tab, the server cannot sign out the user.</span></span> <span data-ttu-id="e2b15-177">Чтобы реализовать выход пользователя при закрытии браузера, необходимо определить его с помощью JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e2b15-177">To implement signing out the user when the browser is closed, you must detect that with JavaScript.</span></span> <span data-ttu-id="e2b15-178">Выполните поиск по фразе "как обнаружить событие закрытия вкладки окна браузера?".</span><span class="sxs-lookup"><span data-stu-id="e2b15-178">Search for "How to Detect Browser Window Tab Close Event?".</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e2b15-179">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="e2b15-179">React to back-end changes</span></span>

<span data-ttu-id="e2b15-180">После cookie создания объект cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-180">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e2b15-181">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="e2b15-181">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e2b15-182">cookieСистема проверки подлинности приложения продолжит обрабатывать запросы на основе проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="e2b15-182">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e2b15-183">Пользователь остается в приложении, если проверка подлинности cookie является допустимой.</span><span class="sxs-lookup"><span data-stu-id="e2b15-183">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e2b15-184"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Событие может использоваться для перехвата и переопределения проверки cookie удостоверения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-184">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e2b15-185">Проверка cookie при каждом запросе снижает риск аннулированных пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="e2b15-185">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e2b15-186">Один из подходов к cookie проверке основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2b15-186">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e2b15-187">Если база данных не была изменена с момента cookie выпуска пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если cookie он все еще действителен.</span><span class="sxs-lookup"><span data-stu-id="e2b15-187">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e2b15-188">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="e2b15-188">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e2b15-189">Когда пользователь обновляется в базе данных, ему `LastChanged` присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="e2b15-189">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e2b15-190">Чтобы сделать недействительным, cookie когда база данных изменяется на основе `LastChanged` значения, создайте объект cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="e2b15-190">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e2b15-191">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e2b15-191">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e2b15-192">Ниже приведен пример реализации `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e2b15-192">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e2b15-193">Зарегистрируйте экземпляр событий во время cookie регистрации службы в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="e2b15-193">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2b15-194">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной области для `CustomCookieAuthenticationEvents` класса:</span><span class="sxs-lookup"><span data-stu-id="e2b15-194">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e2b15-195">Рассмотрим ситуацию, в которой имя пользователя обновляется, &mdash; а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="e2b15-195">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e2b15-196">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true` .</span><span class="sxs-lookup"><span data-stu-id="e2b15-196">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e2b15-197">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="e2b15-197">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e2b15-198">Проверка подлинности cookie для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-198">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e2b15-199">Постоянный cookie s</span><span class="sxs-lookup"><span data-stu-id="e2b15-199">Persistent cookies</span></span>

<span data-ttu-id="e2b15-200">Может потребоваться, cookie чтобы объект сохранялся между сеансами браузера.</span><span class="sxs-lookup"><span data-stu-id="e2b15-200">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e2b15-201">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="e2b15-201">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e2b15-202">В следующем фрагменте кода создается удостоверение cookie , которое остается нестабильным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="e2b15-202">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e2b15-203">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="e2b15-203">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e2b15-204">Если cookie срок действия истекает при закрытии браузера, браузер очищается cookie после перезапуска.</span><span class="sxs-lookup"><span data-stu-id="e2b15-204">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e2b15-205">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e2b15-205">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e2b15-206">Абсолютный cookie срок действия</span><span class="sxs-lookup"><span data-stu-id="e2b15-206">Absolute cookie expiration</span></span>

<span data-ttu-id="e2b15-207">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e2b15-207">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e2b15-208">Для создания постоянного cookie `IsPersistent` необходимо также задать значение.</span><span class="sxs-lookup"><span data-stu-id="e2b15-208">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e2b15-209">В противном случае объект cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e2b15-209">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e2b15-210">Если задан `ExpiresUtc` параметр, он переопределяет значение <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> параметра <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , если оно задано.</span><span class="sxs-lookup"><span data-stu-id="e2b15-210">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e2b15-211">В следующем фрагменте кода создается удостоверение cookie , которое продолжается в течение 20 минут.</span><span class="sxs-lookup"><span data-stu-id="e2b15-211">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e2b15-212">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="e2b15-212">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="e2b15-213">ASP.NET Core Identity — полноценный полнофункциональный поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="e2b15-213">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="e2b15-214">Однако cookie поставщик проверки подлинности на основе не ASP.NET Core Identity может использоваться.</span><span class="sxs-lookup"><span data-stu-id="e2b15-214">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="e2b15-215">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="e2b15-215">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="e2b15-216">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e2b15-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e2b15-217">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="e2b15-217">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="e2b15-218">Используйте адрес **электронной почты** `maria.rodriguez@contoso.com` и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="e2b15-218">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="e2b15-219">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="e2b15-219">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="e2b15-220">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="e2b15-220">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="e2b15-221">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="e2b15-221">Configuration</span></span>

<span data-ttu-id="e2b15-222">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для [Microsoft. AspNetCore. Authentication. Cookie пакет.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/)</span><span class="sxs-lookup"><span data-stu-id="e2b15-222">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="e2b15-223">В `Startup.ConfigureServices` методе создайте службу промежуточного слоя проверки подлинности с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов и:</span><span class="sxs-lookup"><span data-stu-id="e2b15-223">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="e2b15-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="e2b15-225">`AuthenticationScheme` полезен при наличии нескольких экземпляров cookie проверки подлинности и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="e2b15-225">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="e2b15-226">Присвоение параметру значения `AuthenticationScheme` [ Cookie аусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение " Cookie s" для схемы.</span><span class="sxs-lookup"><span data-stu-id="e2b15-226">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="e2b15-227">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="e2b15-227">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="e2b15-228">Схема проверки подлинности приложения отличается от cookie схемы проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-228">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="e2b15-229">Если cookie Схема проверки подлинности не указана в <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , она использует `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="e2b15-229">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="e2b15-230">По cookie <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> умолчанию для свойства проверки подлинности задано значение `true` .</span><span class="sxs-lookup"><span data-stu-id="e2b15-230">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="e2b15-231">Проверка подлинности cookie разрешена, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="e2b15-231">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="e2b15-232">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="e2b15-232">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="e2b15-233">В `Startup.Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство.</span><span class="sxs-lookup"><span data-stu-id="e2b15-233">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e2b15-234">Вызовите `UseAuthentication` метод перед вызовом `UseMvcWithDefaultRoute` или `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="e2b15-234">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="e2b15-235"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e2b15-235">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="e2b15-236">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="e2b15-236">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="e2b15-237">Cookie По промежуточного слоя политики</span><span class="sxs-lookup"><span data-stu-id="e2b15-237">Cookie Policy Middleware</span></span>

<span data-ttu-id="e2b15-238">По [ Cookie промежуточного слоя политики](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает cookie возможности политики.</span><span class="sxs-lookup"><span data-stu-id="e2b15-238">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="e2b15-239">Добавление по промежуточного слоя в конвейер обработки приложений зависит от порядка, &mdash; оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="e2b15-239">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="e2b15-240">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по Cookie промежуточного слоя политики для управления глобальными характеристиками cookie обработки и привязки к cookie обработчикам обработки при cookie добавлении или удалении s.</span><span class="sxs-lookup"><span data-stu-id="e2b15-240">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="e2b15-241">Значение по умолчанию <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> — `SameSiteMode.Lax` Разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="e2b15-241">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="e2b15-242">Чтобы строго применить политику того же сайта `SameSiteMode.Strict` , установите `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="e2b15-242">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="e2b15-243">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень cookie безопасности для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="e2b15-243">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="e2b15-244">CookieПараметр по промежуточного слоя политики для `MinimumSameSitePolicy` может влиять на параметр в параметрах `Cookie.SameSite` в соответствии с `CookieAuthenticationOptions` приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="e2b15-244">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="e2b15-245">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="e2b15-245">MinimumSameSitePolicy</span></span> | <span data-ttu-id="e2b15-246">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="e2b15-246">Cookie.SameSite</span></span> | <span data-ttu-id="e2b15-247">Результат Cookie . Параметр SameSite</span><span class="sxs-lookup"><span data-stu-id="e2b15-247">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="e2b15-248">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-248">SameSiteMode.None</span></span>     | <span data-ttu-id="e2b15-249">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-249">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-250">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-251">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-251">SameSiteMode.Strict</span></span> | <span data-ttu-id="e2b15-252">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-252">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-253">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-254">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-254">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e2b15-255">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-255">SameSiteMode.Lax</span></span>      | <span data-ttu-id="e2b15-256">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-256">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-257">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-258">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-258">SameSiteMode.Strict</span></span> | <span data-ttu-id="e2b15-259">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-259">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-260">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-261">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-261">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="e2b15-262">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-262">SameSiteMode.Strict</span></span>   | <span data-ttu-id="e2b15-263">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="e2b15-263">SameSiteMode.None</span></span><br><span data-ttu-id="e2b15-264">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="e2b15-264">SameSiteMode.Lax</span></span><br><span data-ttu-id="e2b15-265">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-265">SameSiteMode.Strict</span></span> | <span data-ttu-id="e2b15-266">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-266">SameSiteMode.Strict</span></span><br><span data-ttu-id="e2b15-267">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-267">SameSiteMode.Strict</span></span><br><span data-ttu-id="e2b15-268">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="e2b15-268">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="e2b15-269">Создание проверки подлинности cookie</span><span class="sxs-lookup"><span data-stu-id="e2b15-269">Create an authentication cookie</span></span>

<span data-ttu-id="e2b15-270">Чтобы создать cookie данные о пользователе, создайте <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="e2b15-270">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="e2b15-271">Сведения о пользователе сериализуются и хранятся в cookie .</span><span class="sxs-lookup"><span data-stu-id="e2b15-271">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="e2b15-272">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым параметром <xref:System.Security.Claims.Claim> s и вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="e2b15-272">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="e2b15-273">`SignInAsync` создает зашифрованный объект cookie и добавляет его к текущему ответу.</span><span class="sxs-lookup"><span data-stu-id="e2b15-273">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="e2b15-274">Если параметр `AuthenticationScheme` не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2b15-274">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="e2b15-275">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e2b15-275">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="e2b15-276">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-276">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="e2b15-277">Выйти</span><span class="sxs-lookup"><span data-stu-id="e2b15-277">Sign out</span></span>

<span data-ttu-id="e2b15-278">Чтобы выйти из текущего пользователя и удалить его cookie , вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="e2b15-278">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="e2b15-279">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или " Cookie s") не используется в качестве схемы (например, "Contoso Cookie "), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e2b15-279">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="e2b15-280">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e2b15-280">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="e2b15-281">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="e2b15-281">React to back-end changes</span></span>

<span data-ttu-id="e2b15-282">После cookie создания объект cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-282">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="e2b15-283">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="e2b15-283">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="e2b15-284">cookieСистема проверки подлинности приложения продолжит обрабатывать запросы на основе проверки подлинности cookie .</span><span class="sxs-lookup"><span data-stu-id="e2b15-284">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="e2b15-285">Пользователь остается в приложении, если проверка подлинности cookie является допустимой.</span><span class="sxs-lookup"><span data-stu-id="e2b15-285">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="e2b15-286"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*>Событие может использоваться для перехвата и переопределения проверки cookie удостоверения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-286">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="e2b15-287">Проверка cookie при каждом запросе снижает риск аннулированных пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="e2b15-287">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="e2b15-288">Один из подходов к cookie проверке основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="e2b15-288">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="e2b15-289">Если база данных не была изменена с момента cookie выпуска пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если cookie он все еще действителен.</span><span class="sxs-lookup"><span data-stu-id="e2b15-289">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="e2b15-290">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="e2b15-290">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="e2b15-291">Когда пользователь обновляется в базе данных, ему `LastChanged` присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="e2b15-291">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="e2b15-292">Чтобы сделать недействительным, cookie когда база данных изменяется на основе `LastChanged` значения, создайте объект cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="e2b15-292">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="e2b15-293">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="e2b15-293">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="e2b15-294">Ниже приведен пример реализации `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="e2b15-294">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="e2b15-295">Зарегистрируйте экземпляр событий во время cookie регистрации службы в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="e2b15-295">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="e2b15-296">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной области для `CustomCookieAuthenticationEvents` класса:</span><span class="sxs-lookup"><span data-stu-id="e2b15-296">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="e2b15-297">Рассмотрим ситуацию, в которой имя пользователя обновляется, &mdash; а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="e2b15-297">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="e2b15-298">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true` .</span><span class="sxs-lookup"><span data-stu-id="e2b15-298">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="e2b15-299">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="e2b15-299">The approach described here is triggered on every request.</span></span> <span data-ttu-id="e2b15-300">Проверка подлинности cookie для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="e2b15-300">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="e2b15-301">Постоянный cookie s</span><span class="sxs-lookup"><span data-stu-id="e2b15-301">Persistent cookies</span></span>

<span data-ttu-id="e2b15-302">Может потребоваться, cookie чтобы объект сохранялся между сеансами браузера.</span><span class="sxs-lookup"><span data-stu-id="e2b15-302">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="e2b15-303">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="e2b15-303">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="e2b15-304">В следующем фрагменте кода создается удостоверение cookie , которое остается нестабильным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="e2b15-304">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="e2b15-305">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="e2b15-305">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="e2b15-306">Если cookie срок действия истекает при закрытии браузера, браузер очищается cookie после перезапуска.</span><span class="sxs-lookup"><span data-stu-id="e2b15-306">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="e2b15-307">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="e2b15-307">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="e2b15-308">Абсолютный cookie срок действия</span><span class="sxs-lookup"><span data-stu-id="e2b15-308">Absolute cookie expiration</span></span>

<span data-ttu-id="e2b15-309">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="e2b15-309">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="e2b15-310">Для создания постоянного cookie `IsPersistent` необходимо также задать значение.</span><span class="sxs-lookup"><span data-stu-id="e2b15-310">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="e2b15-311">В противном случае объект cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e2b15-311">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="e2b15-312">Если задан `ExpiresUtc` параметр, он переопределяет значение <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> параметра <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , если оно задано.</span><span class="sxs-lookup"><span data-stu-id="e2b15-312">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="e2b15-313">В следующем фрагменте кода создается удостоверение cookie , которое продолжается в течение 20 минут.</span><span class="sxs-lookup"><span data-stu-id="e2b15-313">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="e2b15-314">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="e2b15-314">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e2b15-315">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e2b15-315">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="e2b15-316">Проверки ролей на основе политик</span><span class="sxs-lookup"><span data-stu-id="e2b15-316">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
