---
title: Использовать проверку подлинности файлов cookie без ASP.NET CoreIdentity
author: rick-anderson
description: Узнайте, как использовать проверку подлинности файлов cookie без ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/cookie
ms.openlocfilehash: 7d2f338f8ece6bd3cc99d5f2ab8153b5c465c7a4
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724241"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="a3082-103">Использовать проверку подлинности файлов cookie без ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="a3082-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="a3082-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="a3082-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a3082-105">ASP.NET Core — полноценный полнофункциональный Identity поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="a3082-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="a3082-106">Однако можно использовать поставщик проверки подлинности на основе файлов cookie без ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="a3082-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="a3082-107">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="a3082-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="a3082-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3082-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a3082-109">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="a3082-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="a3082-110">Используйте адрес **электронной почты** `maria.rodriguez@contoso.com` и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="a3082-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="a3082-111">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="a3082-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="a3082-112">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="a3082-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="a3082-113">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="a3082-113">Configuration</span></span>

<span data-ttu-id="a3082-114">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для пакета [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="a3082-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="a3082-115">В `Startup.ConfigureServices` методе создайте службы промежуточного слоя для проверки подлинности с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов и:</span><span class="sxs-lookup"><span data-stu-id="a3082-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="a3082-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="a3082-117">`AuthenticationScheme`полезен при наличии нескольких экземпляров проверки подлинности файлов cookie и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="a3082-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="a3082-118">Присвоение параметру значения `AuthenticationScheme` [Кукиеаусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение "cookies" для схемы.</span><span class="sxs-lookup"><span data-stu-id="a3082-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="a3082-119">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="a3082-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="a3082-120">Схема проверки подлинности приложения отличается от схемы проверки подлинности файлов cookie приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="a3082-121">Если схема проверки подлинности файлов cookie не указана в <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , она использует `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").</span><span class="sxs-lookup"><span data-stu-id="a3082-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="a3082-122">Свойство Cookie проверки подлинности по <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> `true` умолчанию имеет значение.</span><span class="sxs-lookup"><span data-stu-id="a3082-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="a3082-123">Файлы cookie проверки подлинности разрешены, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="a3082-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="a3082-124">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="a3082-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="a3082-125">В `Startup.Configure` вызовите метод `UseAuthentication` и, `UseAuthorization` чтобы задать `HttpContext.User` свойство и запустить по промежуточного слоя авторизации для запросов.</span><span class="sxs-lookup"><span data-stu-id="a3082-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="a3082-126">Вызовите `UseAuthentication` `UseAuthorization` методы и перед вызовом `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="a3082-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="a3082-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="a3082-128">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="a3082-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="a3082-129">По промежуточного слоя политики файлов cookie</span><span class="sxs-lookup"><span data-stu-id="a3082-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="a3082-130">По [промежуточного слоя политики файлов cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает возможности политики файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="a3082-131">Добавление по промежуточного слоя в конвейер обработки приложений зависит от порядка, &mdash; оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="a3082-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="a3082-132">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по промежуточного слоя политики файлов cookie для управления глобальными характеристиками обработки файлов cookie и подключения к обработчикам обработки файлов cookie при добавлении или удалении файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="a3082-133">Значение по умолчанию <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> — `SameSiteMode.Lax` Разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="a3082-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="a3082-134">Чтобы строго применить политику того же сайта `SameSiteMode.Strict` , установите `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="a3082-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="a3082-135">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень безопасности файлов cookie для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="a3082-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="a3082-136">Параметр по промежуточного слоя политики cookie для `MinimumSameSitePolicy` может влиять на параметр в параметрах `Cookie.SameSite` в `CookieAuthenticationOptions` соответствии с приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="a3082-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="a3082-137">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="a3082-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="a3082-138">Файл cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="a3082-138">Cookie.SameSite</span></span> | <span data-ttu-id="a3082-139">Результирующий параметр cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="a3082-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="a3082-140">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-140">SameSiteMode.None</span></span>     | <span data-ttu-id="a3082-141">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-141">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-142">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-143">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="a3082-144">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-144">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-145">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-146">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="a3082-147">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="a3082-148">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-148">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-149">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-150">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="a3082-151">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-152">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-153">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="a3082-154">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="a3082-155">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-155">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-156">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-157">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="a3082-158">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="a3082-159">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="a3082-160">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="a3082-161">Создание файла cookie для проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a3082-161">Create an authentication cookie</span></span>

<span data-ttu-id="a3082-162">Чтобы создать файл cookie, содержащий сведения о пользователе, создайте <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="a3082-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="a3082-163">Сведения о пользователе сериализуются и хранятся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="a3082-164">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым параметром <xref:System.Security.Claims.Claim> s и вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="a3082-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="a3082-165">`SignInAsync`создает зашифрованный файл cookie и добавляет его в текущий ответ.</span><span class="sxs-lookup"><span data-stu-id="a3082-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="a3082-166">Если параметр `AuthenticationScheme` не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a3082-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="a3082-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri>используется по умолчанию только для нескольких конкретных путей, например для пути входа и путей выхода.</span><span class="sxs-lookup"><span data-stu-id="a3082-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="a3082-168">Дополнительные сведения см. в разделе [источник кукиеаусентикатионхандлер](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="a3082-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="a3082-169">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3082-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="a3082-170">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="a3082-171">Выйти</span><span class="sxs-lookup"><span data-stu-id="a3082-171">Sign out</span></span>

<span data-ttu-id="a3082-172">Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="a3082-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="a3082-173">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или "cookie") не используется в качестве схемы (например, "контосокукие"), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="a3082-174">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a3082-174">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="a3082-175">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="a3082-175">React to back-end changes</span></span>

<span data-ttu-id="a3082-176">После создания файла cookie файл cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="a3082-176">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="a3082-177">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="a3082-177">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="a3082-178">Система проверки подлинности файлов cookie приложения продолжит обрабатывать запросы на основе файла cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-178">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="a3082-179">Пользователь остается в приложении, если файл cookie проверки подлинности является допустимым.</span><span class="sxs-lookup"><span data-stu-id="a3082-179">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="a3082-180">Это <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> событие можно использовать для перехвата и переопределения проверки удостоверения файла cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-180">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="a3082-181">Проверка файла cookie при каждом запросе снижает риск отзыва пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="a3082-181">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="a3082-182">Один из подходов к проверке файлов cookie основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="a3082-182">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="a3082-183">Если база данных не была изменена с момента выдачи файла cookie пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если его файл cookie остается действительным.</span><span class="sxs-lookup"><span data-stu-id="a3082-183">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="a3082-184">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="a3082-184">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="a3082-185">Когда пользователь обновляется в базе данных, ему `LastChanged` присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="a3082-185">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="a3082-186">Чтобы сделать файл cookie недействительным при изменении базы данных на основе `LastChanged` значения, создайте файл cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="a3082-186">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="a3082-187">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="a3082-187">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="a3082-188">Ниже приведен пример реализации `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="a3082-188">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="a3082-189">Зарегистрируйте экземпляр событий во время регистрации службы файлов cookie в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="a3082-189">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a3082-190">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной области для `CustomCookieAuthenticationEvents` класса:</span><span class="sxs-lookup"><span data-stu-id="a3082-190">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="a3082-191">Рассмотрим ситуацию, в которой имя пользователя обновляется, &mdash; а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="a3082-191">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="a3082-192">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true` .</span><span class="sxs-lookup"><span data-stu-id="a3082-192">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="a3082-193">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="a3082-193">The approach described here is triggered on every request.</span></span> <span data-ttu-id="a3082-194">Проверка файлов cookie проверки подлинности для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-194">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="a3082-195">Постоянные файлы cookie</span><span class="sxs-lookup"><span data-stu-id="a3082-195">Persistent cookies</span></span>

<span data-ttu-id="a3082-196">Возможно, вы хотите, чтобы файл cookie сохранялся в сеансах браузера.</span><span class="sxs-lookup"><span data-stu-id="a3082-196">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="a3082-197">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="a3082-197">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="a3082-198">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который остается недействительным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="a3082-198">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="a3082-199">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="a3082-199">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="a3082-200">Если срок действия файла cookie истекает при закрытии браузера, браузер очищает файл cookie после его перезапуска.</span><span class="sxs-lookup"><span data-stu-id="a3082-200">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="a3082-201">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="a3082-201">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="a3082-202">Абсолютный срок действия файла cookie</span><span class="sxs-lookup"><span data-stu-id="a3082-202">Absolute cookie expiration</span></span>

<span data-ttu-id="a3082-203">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="a3082-203">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="a3082-204">Для создания постоянного файла cookie `IsPersistent` также необходимо задать.</span><span class="sxs-lookup"><span data-stu-id="a3082-204">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="a3082-205">В противном случае файл cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-205">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="a3082-206">Если задан `ExpiresUtc` параметр, он переопределяет значение <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> параметра <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , если оно задано.</span><span class="sxs-lookup"><span data-stu-id="a3082-206">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="a3082-207">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который длится 20 минут.</span><span class="sxs-lookup"><span data-stu-id="a3082-207">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="a3082-208">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="a3082-208">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="a3082-209">ASP.NET Core — полноценный полнофункциональный Identity поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="a3082-209">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="a3082-210">Однако можно использовать поставщик проверки подлинности на основе файлов cookie без ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="a3082-210">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="a3082-211">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="a3082-211">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="a3082-212">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a3082-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a3082-213">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="a3082-213">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="a3082-214">Используйте адрес **электронной почты** `maria.rodriguez@contoso.com` и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="a3082-214">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="a3082-215">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="a3082-215">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="a3082-216">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="a3082-216">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="a3082-217">Конфигурация</span><span class="sxs-lookup"><span data-stu-id="a3082-217">Configuration</span></span>

<span data-ttu-id="a3082-218">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для пакета [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="a3082-218">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="a3082-219">В `Startup.ConfigureServices` методе создайте службу промежуточного слоя проверки подлинности с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов и:</span><span class="sxs-lookup"><span data-stu-id="a3082-219">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="a3082-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-220"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="a3082-221">`AuthenticationScheme`полезен при наличии нескольких экземпляров проверки подлинности файлов cookie и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="a3082-221">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="a3082-222">Присвоение параметру значения `AuthenticationScheme` [Кукиеаусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение "cookies" для схемы.</span><span class="sxs-lookup"><span data-stu-id="a3082-222">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="a3082-223">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="a3082-223">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="a3082-224">Схема проверки подлинности приложения отличается от схемы проверки подлинности файлов cookie приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-224">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="a3082-225">Если схема проверки подлинности файлов cookie не указана в <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> , она использует `CookieAuthenticationDefaults.AuthenticationScheme` ("cookies").</span><span class="sxs-lookup"><span data-stu-id="a3082-225">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="a3082-226">Свойство Cookie проверки подлинности по <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> `true` умолчанию имеет значение.</span><span class="sxs-lookup"><span data-stu-id="a3082-226">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="a3082-227">Файлы cookie проверки подлинности разрешены, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="a3082-227">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="a3082-228">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="a3082-228">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="a3082-229">В `Startup.Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство.</span><span class="sxs-lookup"><span data-stu-id="a3082-229">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="a3082-230">Вызовите `UseAuthentication` метод перед вызовом `UseMvcWithDefaultRoute` или `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="a3082-230">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="a3082-231"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-231">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="a3082-232">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности в `Startup.ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="a3082-232">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="a3082-233">По промежуточного слоя политики файлов cookie</span><span class="sxs-lookup"><span data-stu-id="a3082-233">Cookie Policy Middleware</span></span>

<span data-ttu-id="a3082-234">По [промежуточного слоя политики файлов cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает возможности политики файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-234">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="a3082-235">Добавление по промежуточного слоя в конвейер обработки приложений зависит от порядка, &mdash; оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="a3082-235">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="a3082-236">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по промежуточного слоя политики файлов cookie для управления глобальными характеристиками обработки файлов cookie и подключения к обработчикам обработки файлов cookie при добавлении или удалении файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-236">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="a3082-237">Значение по умолчанию <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> — `SameSiteMode.Lax` Разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="a3082-237">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="a3082-238">Чтобы строго применить политику того же сайта `SameSiteMode.Strict` , установите `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="a3082-238">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="a3082-239">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень безопасности файлов cookie для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="a3082-239">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="a3082-240">Параметр по промежуточного слоя политики cookie для `MinimumSameSitePolicy` может влиять на параметр в параметрах `Cookie.SameSite` в `CookieAuthenticationOptions` соответствии с приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="a3082-240">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="a3082-241">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="a3082-241">MinimumSameSitePolicy</span></span> | <span data-ttu-id="a3082-242">Файл cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="a3082-242">Cookie.SameSite</span></span> | <span data-ttu-id="a3082-243">Результирующий параметр cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="a3082-243">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="a3082-244">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-244">SameSiteMode.None</span></span>     | <span data-ttu-id="a3082-245">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-245">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-246">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-246">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-247">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-247">SameSiteMode.Strict</span></span> | <span data-ttu-id="a3082-248">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-248">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-249">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-249">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-250">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-250">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="a3082-251">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-251">SameSiteMode.Lax</span></span>      | <span data-ttu-id="a3082-252">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-252">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-253">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-254">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-254">SameSiteMode.Strict</span></span> | <span data-ttu-id="a3082-255">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-255">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-256">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-257">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-257">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="a3082-258">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-258">SameSiteMode.Strict</span></span>   | <span data-ttu-id="a3082-259">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="a3082-259">SameSiteMode.None</span></span><br><span data-ttu-id="a3082-260">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="a3082-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="a3082-261">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-261">SameSiteMode.Strict</span></span> | <span data-ttu-id="a3082-262">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-262">SameSiteMode.Strict</span></span><br><span data-ttu-id="a3082-263">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="a3082-264">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="a3082-264">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="a3082-265">Создание файла cookie для проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a3082-265">Create an authentication cookie</span></span>

<span data-ttu-id="a3082-266">Чтобы создать файл cookie, содержащий сведения о пользователе, создайте <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="a3082-266">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="a3082-267">Сведения о пользователе сериализуются и хранятся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-267">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="a3082-268">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым параметром <xref:System.Security.Claims.Claim> s и вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="a3082-268">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="a3082-269">`SignInAsync`создает зашифрованный файл cookie и добавляет его в текущий ответ.</span><span class="sxs-lookup"><span data-stu-id="a3082-269">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="a3082-270">Если параметр `AuthenticationScheme` не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a3082-270">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="a3082-271">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3082-271">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="a3082-272">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-272">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="a3082-273">Выйти</span><span class="sxs-lookup"><span data-stu-id="a3082-273">Sign out</span></span>

<span data-ttu-id="a3082-274">Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="a3082-274">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="a3082-275">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или "cookie") не используется в качестве схемы (например, "контосокукие"), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-275">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="a3082-276">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a3082-276">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="a3082-277">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="a3082-277">React to back-end changes</span></span>

<span data-ttu-id="a3082-278">После создания файла cookie файл cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="a3082-278">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="a3082-279">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="a3082-279">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="a3082-280">Система проверки подлинности файлов cookie приложения продолжит обрабатывать запросы на основе файла cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-280">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="a3082-281">Пользователь остается в приложении, если файл cookie проверки подлинности является допустимым.</span><span class="sxs-lookup"><span data-stu-id="a3082-281">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="a3082-282">Это <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> событие можно использовать для перехвата и переопределения проверки удостоверения файла cookie.</span><span class="sxs-lookup"><span data-stu-id="a3082-282">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="a3082-283">Проверка файла cookie при каждом запросе снижает риск отзыва пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="a3082-283">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="a3082-284">Один из подходов к проверке файлов cookie основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="a3082-284">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="a3082-285">Если база данных не была изменена с момента выдачи файла cookie пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если его файл cookie остается действительным.</span><span class="sxs-lookup"><span data-stu-id="a3082-285">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="a3082-286">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="a3082-286">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="a3082-287">Когда пользователь обновляется в базе данных, ему `LastChanged` присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="a3082-287">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="a3082-288">Чтобы сделать файл cookie недействительным при изменении базы данных на основе `LastChanged` значения, создайте файл cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="a3082-288">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="a3082-289">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="a3082-289">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="a3082-290">Ниже приведен пример реализации `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="a3082-290">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="a3082-291">Зарегистрируйте экземпляр событий во время регистрации службы файлов cookie в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="a3082-291">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a3082-292">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной области для `CustomCookieAuthenticationEvents` класса:</span><span class="sxs-lookup"><span data-stu-id="a3082-292">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="a3082-293">Рассмотрим ситуацию, в которой имя пользователя обновляется, &mdash; а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="a3082-293">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="a3082-294">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true` .</span><span class="sxs-lookup"><span data-stu-id="a3082-294">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="a3082-295">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="a3082-295">The approach described here is triggered on every request.</span></span> <span data-ttu-id="a3082-296">Проверка файлов cookie проверки подлинности для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="a3082-296">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="a3082-297">Постоянные файлы cookie</span><span class="sxs-lookup"><span data-stu-id="a3082-297">Persistent cookies</span></span>

<span data-ttu-id="a3082-298">Возможно, вы хотите, чтобы файл cookie сохранялся в сеансах браузера.</span><span class="sxs-lookup"><span data-stu-id="a3082-298">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="a3082-299">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="a3082-299">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="a3082-300">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который остается недействительным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="a3082-300">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="a3082-301">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="a3082-301">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="a3082-302">Если срок действия файла cookie истекает при закрытии браузера, браузер очищает файл cookie после его перезапуска.</span><span class="sxs-lookup"><span data-stu-id="a3082-302">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="a3082-303">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="a3082-303">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="a3082-304">Абсолютный срок действия файла cookie</span><span class="sxs-lookup"><span data-stu-id="a3082-304">Absolute cookie expiration</span></span>

<span data-ttu-id="a3082-305">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="a3082-305">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="a3082-306">Для создания постоянного файла cookie `IsPersistent` также необходимо задать.</span><span class="sxs-lookup"><span data-stu-id="a3082-306">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="a3082-307">В противном случае файл cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a3082-307">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="a3082-308">Если задан `ExpiresUtc` параметр, он переопределяет значение <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> параметра <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , если оно задано.</span><span class="sxs-lookup"><span data-stu-id="a3082-308">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="a3082-309">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который длится 20 минут.</span><span class="sxs-lookup"><span data-stu-id="a3082-309">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="a3082-310">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="a3082-310">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="a3082-311">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a3082-311">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="a3082-312">Проверки ролей на основе политик</span><span class="sxs-lookup"><span data-stu-id="a3082-312">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
