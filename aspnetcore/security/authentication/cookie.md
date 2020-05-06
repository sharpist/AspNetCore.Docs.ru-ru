---
title: Использовать проверку подлинности файлов cookie без ASP.NET CoreIdentity
author: rick-anderson
description: Узнайте, как использовать проверку подлинности Identityфайлов cookie без ASP.NET Core.
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
ms.openlocfilehash: c179b3657ad4cbda960c2afe685a63f3266a7402
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773848"
---
# <a name="use-cookie-authentication-without-aspnet-core-identity"></a><span data-ttu-id="397d4-103">Использовать проверку подлинности файлов cookie без удостоверения ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="397d4-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="397d4-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="397d4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="397d4-105">ASP.NET Core удостоверение — это полнофункциональный поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="397d4-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="397d4-106">Однако можно использовать поставщик проверки подлинности на основе файлов cookie без ASP.NET Core удостоверения.</span><span class="sxs-lookup"><span data-stu-id="397d4-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="397d4-107">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="397d4-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="397d4-108">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="397d4-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="397d4-109">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="397d4-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="397d4-110">Используйте `maria.rodriguez@contoso.com` адрес **электронной почты** и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="397d4-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="397d4-111">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="397d4-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="397d4-112">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="397d4-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="397d4-113">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="397d4-113">Configuration</span></span>

<span data-ttu-id="397d4-114">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для пакета [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="397d4-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="397d4-115">В `Startup.ConfigureServices` методе создайте службы промежуточного слоя для проверки подлинности с помощью методов <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> и: <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*></span><span class="sxs-lookup"><span data-stu-id="397d4-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="397d4-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="397d4-117">`AuthenticationScheme`полезен при наличии нескольких экземпляров проверки подлинности файлов cookie и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="397d4-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="397d4-118">`AuthenticationScheme` Присвоение параметру значения [кукиеаусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение "cookies" для схемы.</span><span class="sxs-lookup"><span data-stu-id="397d4-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="397d4-119">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="397d4-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="397d4-120">Схема проверки подлинности приложения отличается от схемы проверки подлинности файлов cookie приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="397d4-121">Если схема проверки подлинности файлов cookie <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>не указана `CookieAuthenticationDefaults.AuthenticationScheme` в, она использует ("cookies").</span><span class="sxs-lookup"><span data-stu-id="397d4-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="397d4-122"><xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> Свойство cookie проверки подлинности по умолчанию имеет значение `true` .</span><span class="sxs-lookup"><span data-stu-id="397d4-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="397d4-123">Файлы cookie проверки подлинности разрешены, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="397d4-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="397d4-124">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="397d4-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="397d4-125">В `Startup.Configure`вызовите `UseAuthentication` метод `UseAuthorization` и, чтобы `HttpContext.User` задать свойство и запустить по промежуточного слоя авторизации для запросов.</span><span class="sxs-lookup"><span data-stu-id="397d4-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="397d4-126">Вызовите `UseAuthentication` методы `UseAuthorization` и перед вызовом `UseEndpoints`:</span><span class="sxs-lookup"><span data-stu-id="397d4-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="397d4-127"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="397d4-128">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности `Startup.ConfigureServices` в методе:</span><span class="sxs-lookup"><span data-stu-id="397d4-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="397d4-129">По промежуточного слоя политики файлов cookie</span><span class="sxs-lookup"><span data-stu-id="397d4-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="397d4-130">По [промежуточного слоя политики файлов cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает возможности политики файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="397d4-131">Добавление по промежуточного слоя в конвейер обработки приложений зависит от&mdash;порядка, оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="397d4-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="397d4-132">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по промежуточного слоя политики файлов cookie для управления глобальными характеристиками обработки файлов cookie и подключения к обработчикам обработки файлов cookie при добавлении или удалении файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="397d4-133">Значение по <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> умолчанию `SameSiteMode.Lax` — разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="397d4-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="397d4-134">Чтобы строго применить политику того же сайта `SameSiteMode.Strict`, установите. `MinimumSameSitePolicy`</span><span class="sxs-lookup"><span data-stu-id="397d4-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="397d4-135">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень безопасности файлов cookie для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="397d4-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="397d4-136">Параметр по промежуточного слоя политики `MinimumSameSitePolicy` cookie для может влиять на `Cookie.SameSite` параметр `CookieAuthenticationOptions` в параметрах в соответствии с приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="397d4-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="397d4-137">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="397d4-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="397d4-138">Файл cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="397d4-138">Cookie.SameSite</span></span> | <span data-ttu-id="397d4-139">Результирующий параметр cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="397d4-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="397d4-140">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-140">SameSiteMode.None</span></span>     | <span data-ttu-id="397d4-141">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-141">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-142">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-143">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="397d4-144">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-144">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-145">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-146">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="397d4-147">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="397d4-148">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-148">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-149">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-150">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="397d4-151">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-152">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-153">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="397d4-154">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="397d4-155">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-155">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-156">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-157">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="397d4-158">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="397d4-159">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="397d4-160">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="397d4-161">Создание файла cookie для проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="397d4-161">Create an authentication cookie</span></span>

<span data-ttu-id="397d4-162">Чтобы создать файл cookie, содержащий сведения о пользователе, <xref:System.Security.Claims.ClaimsPrincipal>создайте.</span><span class="sxs-lookup"><span data-stu-id="397d4-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="397d4-163">Сведения о пользователе сериализуются и хранятся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="397d4-164">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым <xref:System.Security.Claims.Claim>параметром s <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> и вызовите для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="397d4-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="397d4-165">`SignInAsync`создает зашифрованный файл cookie и добавляет его в текущий ответ.</span><span class="sxs-lookup"><span data-stu-id="397d4-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="397d4-166">Если `AuthenticationScheme` параметр не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="397d4-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="397d4-167">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="397d4-167">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="397d4-168">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-168">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="397d4-169">Выход</span><span class="sxs-lookup"><span data-stu-id="397d4-169">Sign out</span></span>

<span data-ttu-id="397d4-170">Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="397d4-170">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="397d4-171">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или "cookie") не используется в качестве схемы (например, "контосокукие"), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-171">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="397d4-172">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="397d4-172">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="397d4-173">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="397d4-173">React to back-end changes</span></span>

<span data-ttu-id="397d4-174">После создания файла cookie файл cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="397d4-174">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="397d4-175">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="397d4-175">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="397d4-176">Система проверки подлинности файлов cookie приложения продолжит обрабатывать запросы на основе файла cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-176">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="397d4-177">Пользователь остается в приложении, если файл cookie проверки подлинности является допустимым.</span><span class="sxs-lookup"><span data-stu-id="397d4-177">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="397d4-178">Это <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> событие можно использовать для перехвата и переопределения проверки удостоверения файла cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-178">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="397d4-179">Проверка файла cookie при каждом запросе снижает риск отзыва пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="397d4-179">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="397d4-180">Один из подходов к проверке файлов cookie основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="397d4-180">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="397d4-181">Если база данных не была изменена с момента выдачи файла cookie пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если его файл cookie остается действительным.</span><span class="sxs-lookup"><span data-stu-id="397d4-181">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="397d4-182">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="397d4-182">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="397d4-183">Когда пользователь обновляется в базе данных, `LastChanged` ему присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="397d4-183">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="397d4-184">Чтобы сделать файл cookie недействительным при изменении базы данных на основе `LastChanged` значения, создайте файл cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="397d4-184">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="397d4-185">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от: <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents></span><span class="sxs-lookup"><span data-stu-id="397d4-185">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="397d4-186">Ниже приведен пример реализации `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="397d4-186">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="397d4-187">Зарегистрируйте экземпляр событий во время регистрации службы файлов cookie `Startup.ConfigureServices` в методе.</span><span class="sxs-lookup"><span data-stu-id="397d4-187">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="397d4-188">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной `CustomCookieAuthenticationEvents` области для класса:</span><span class="sxs-lookup"><span data-stu-id="397d4-188">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="397d4-189">Рассмотрим ситуацию, в которой имя пользователя обновляется&mdash;, а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="397d4-189">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="397d4-190">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true`.</span><span class="sxs-lookup"><span data-stu-id="397d4-190">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="397d4-191">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="397d4-191">The approach described here is triggered on every request.</span></span> <span data-ttu-id="397d4-192">Проверка файлов cookie проверки подлинности для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-192">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="397d4-193">Постоянные файлы cookie</span><span class="sxs-lookup"><span data-stu-id="397d4-193">Persistent cookies</span></span>

<span data-ttu-id="397d4-194">Возможно, вы хотите, чтобы файл cookie сохранялся в сеансах браузера.</span><span class="sxs-lookup"><span data-stu-id="397d4-194">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="397d4-195">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="397d4-195">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="397d4-196">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который остается недействительным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="397d4-196">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="397d4-197">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="397d4-197">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="397d4-198">Если срок действия файла cookie истекает при закрытии браузера, браузер очищает файл cookie после его перезапуска.</span><span class="sxs-lookup"><span data-stu-id="397d4-198">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="397d4-199">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="397d4-199">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="397d4-200">Абсолютный срок действия файла cookie</span><span class="sxs-lookup"><span data-stu-id="397d4-200">Absolute cookie expiration</span></span>

<span data-ttu-id="397d4-201">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="397d4-201">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="397d4-202">Для создания постоянного файла cookie также `IsPersistent` необходимо задать.</span><span class="sxs-lookup"><span data-stu-id="397d4-202">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="397d4-203">В противном случае файл cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-203">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="397d4-204">`ExpiresUtc` Если задан <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> параметр, он переопределяет значение параметра <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, если оно задано.</span><span class="sxs-lookup"><span data-stu-id="397d4-204">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="397d4-205">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который длится 20 минут.</span><span class="sxs-lookup"><span data-stu-id="397d4-205">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="397d4-206">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="397d4-206">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="397d4-207">ASP.NET Core удостоверение — это полнофункциональный поставщик проверки подлинности для создания и обслуживания имен входа.</span><span class="sxs-lookup"><span data-stu-id="397d4-207">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="397d4-208">Однако можно использовать поставщик проверки подлинности на основе файлов cookie без ASP.NET Core удостоверения.</span><span class="sxs-lookup"><span data-stu-id="397d4-208">However, a cookie-based authentication authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="397d4-209">Для получения дополнительной информации см. <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="397d4-209">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="397d4-210">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="397d4-210">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="397d4-211">В демонстрационных целях в примере приложения учетная запись пользователя для гипотетического пользователя, Мария Rodriguez, жестко закодирована в приложении.</span><span class="sxs-lookup"><span data-stu-id="397d4-211">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="397d4-212">Используйте `maria.rodriguez@contoso.com` адрес **электронной почты** и любой пароль для входа пользователя.</span><span class="sxs-lookup"><span data-stu-id="397d4-212">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="397d4-213">Пользователь прошел проверку подлинности в `AuthenticateUser` методе в файле *pages/Account/Login. cshtml. CS* .</span><span class="sxs-lookup"><span data-stu-id="397d4-213">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="397d4-214">В реальном примере пользователь будет проходить проверку подлинности в базе данных.</span><span class="sxs-lookup"><span data-stu-id="397d4-214">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="397d4-215">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="397d4-215">Configuration</span></span>

<span data-ttu-id="397d4-216">Если приложение не использует [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), создайте ссылку на пакет в файле проекта для пакета [Microsoft. AspNetCore. Authentication. cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) .</span><span class="sxs-lookup"><span data-stu-id="397d4-216">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="397d4-217">В `Startup.ConfigureServices` методе создайте службу промежуточного слоя проверки подлинности с помощью методов <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> и <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="397d4-217">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="397d4-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme>переданный `AddAuthentication` параметр задает схему проверки подлинности по умолчанию для приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-218"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="397d4-219">`AuthenticationScheme`полезен при наличии нескольких экземпляров проверки подлинности файлов cookie и необходимости [авторизации с определенной схемой](xref:security/authorization/limitingidentitybyscheme).</span><span class="sxs-lookup"><span data-stu-id="397d4-219">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="397d4-220">`AuthenticationScheme` Присвоение параметру значения [кукиеаусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) предоставляет значение "cookies" для схемы.</span><span class="sxs-lookup"><span data-stu-id="397d4-220">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="397d4-221">Можно указать любое строковое значение, которое различает схему.</span><span class="sxs-lookup"><span data-stu-id="397d4-221">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="397d4-222">Схема проверки подлинности приложения отличается от схемы проверки подлинности файлов cookie приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-222">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="397d4-223">Если схема проверки подлинности файлов cookie <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>не указана `CookieAuthenticationDefaults.AuthenticationScheme` в, она использует ("cookies").</span><span class="sxs-lookup"><span data-stu-id="397d4-223">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="397d4-224"><xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> Свойство cookie проверки подлинности по умолчанию имеет значение `true` .</span><span class="sxs-lookup"><span data-stu-id="397d4-224">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="397d4-225">Файлы cookie проверки подлинности разрешены, когда посетитель сайта не был передан в сбор данных.</span><span class="sxs-lookup"><span data-stu-id="397d4-225">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="397d4-226">Для получения дополнительной информации см. <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="397d4-226">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="397d4-227">В `Startup.Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство.</span><span class="sxs-lookup"><span data-stu-id="397d4-227">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="397d4-228">Вызовите `UseAuthentication` метод перед `UseMvcWithDefaultRoute` вызовом `UseMvc`или:</span><span class="sxs-lookup"><span data-stu-id="397d4-228">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="397d4-229"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Класс используется для настройки параметров поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-229">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="397d4-230">Задайте `CookieAuthenticationOptions` в конфигурации службы для проверки подлинности `Startup.ConfigureServices` в методе:</span><span class="sxs-lookup"><span data-stu-id="397d4-230">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="cookie-policy-middleware"></a><span data-ttu-id="397d4-231">По промежуточного слоя политики файлов cookie</span><span class="sxs-lookup"><span data-stu-id="397d4-231">Cookie Policy Middleware</span></span>

<span data-ttu-id="397d4-232">По [промежуточного слоя политики файлов cookie](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) включает возможности политики файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-232">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="397d4-233">Добавление по промежуточного слоя в конвейер обработки приложений зависит от&mdash;порядка, оно влияет только на нисходящие компоненты, зарегистрированные в конвейере.</span><span class="sxs-lookup"><span data-stu-id="397d4-233">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="397d4-234">Используется <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> по промежуточного слоя политики файлов cookie для управления глобальными характеристиками обработки файлов cookie и подключения к обработчикам обработки файлов cookie при добавлении или удалении файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-234">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="397d4-235">Значение по <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> умолчанию `SameSiteMode.Lax` — разрешение проверки подлинности OAuth2.</span><span class="sxs-lookup"><span data-stu-id="397d4-235">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="397d4-236">Чтобы строго применить политику того же сайта `SameSiteMode.Strict`, установите. `MinimumSameSitePolicy`</span><span class="sxs-lookup"><span data-stu-id="397d4-236">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="397d4-237">Хотя этот параметр нарушает OAuth2 и другие схемы проверки подлинности в разных источниках, он повышает уровень безопасности файлов cookie для других типов приложений, которые не полагаются на обработку запросов между источниками.</span><span class="sxs-lookup"><span data-stu-id="397d4-237">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="397d4-238">Параметр по промежуточного слоя политики `MinimumSameSitePolicy` cookie для может влиять на `Cookie.SameSite` параметр `CookieAuthenticationOptions` в параметрах в соответствии с приведенной ниже матрицей.</span><span class="sxs-lookup"><span data-stu-id="397d4-238">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="397d4-239">минимумсамеситеполици</span><span class="sxs-lookup"><span data-stu-id="397d4-239">MinimumSameSitePolicy</span></span> | <span data-ttu-id="397d4-240">Файл cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="397d4-240">Cookie.SameSite</span></span> | <span data-ttu-id="397d4-241">Результирующий параметр cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="397d4-241">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="397d4-242">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-242">SameSiteMode.None</span></span>     | <span data-ttu-id="397d4-243">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-243">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-244">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-244">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-245">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-245">SameSiteMode.Strict</span></span> | <span data-ttu-id="397d4-246">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-246">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-247">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-248">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-248">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="397d4-249">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-249">SameSiteMode.Lax</span></span>      | <span data-ttu-id="397d4-250">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-250">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-251">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-251">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-252">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-252">SameSiteMode.Strict</span></span> | <span data-ttu-id="397d4-253">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-254">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-255">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-255">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="397d4-256">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-256">SameSiteMode.Strict</span></span>   | <span data-ttu-id="397d4-257">Самеситемоде. None</span><span class="sxs-lookup"><span data-stu-id="397d4-257">SameSiteMode.None</span></span><br><span data-ttu-id="397d4-258">Самеситемоде. нестрогий</span><span class="sxs-lookup"><span data-stu-id="397d4-258">SameSiteMode.Lax</span></span><br><span data-ttu-id="397d4-259">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-259">SameSiteMode.Strict</span></span> | <span data-ttu-id="397d4-260">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-260">SameSiteMode.Strict</span></span><br><span data-ttu-id="397d4-261">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-261">SameSiteMode.Strict</span></span><br><span data-ttu-id="397d4-262">Самеситемоде.</span><span class="sxs-lookup"><span data-stu-id="397d4-262">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-cookie"></a><span data-ttu-id="397d4-263">Создание файла cookie для проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="397d4-263">Create an authentication cookie</span></span>

<span data-ttu-id="397d4-264">Чтобы создать файл cookie, содержащий сведения о пользователе, <xref:System.Security.Claims.ClaimsPrincipal>создайте.</span><span class="sxs-lookup"><span data-stu-id="397d4-264">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="397d4-265">Сведения о пользователе сериализуются и хранятся в файле cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-265">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="397d4-266">Создайте объект <xref:System.Security.Claims.ClaimsIdentity> с любым необходимым <xref:System.Security.Claims.Claim>параметром s <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> и вызовите для входа пользователя:</span><span class="sxs-lookup"><span data-stu-id="397d4-266">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="397d4-267">`SignInAsync`создает зашифрованный файл cookie и добавляет его в текущий ответ.</span><span class="sxs-lookup"><span data-stu-id="397d4-267">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="397d4-268">Если `AuthenticationScheme` параметр не указан, используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="397d4-268">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="397d4-269">Для шифрования используется система [защиты данных](xref:security/data-protection/using-data-protection) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="397d4-269">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="397d4-270">Для приложения, размещенного на нескольких компьютерах, балансировки нагрузки между приложениями или с помощью веб-фермы, [Настройте защиту данных](xref:security/data-protection/configuration/overview) для использования одного и того же типа звонка и идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-270">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="397d4-271">Выход</span><span class="sxs-lookup"><span data-stu-id="397d4-271">Sign out</span></span>

<span data-ttu-id="397d4-272">Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span><span class="sxs-lookup"><span data-stu-id="397d4-272">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="397d4-273">Если `CookieAuthenticationDefaults.AuthenticationScheme` (или "cookie") не используется в качестве схемы (например, "контосокукие"), укажите схему, используемую при настройке поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-273">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="397d4-274">В противном случае используется схема по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="397d4-274">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="397d4-275">Реагирование на изменения серверной части</span><span class="sxs-lookup"><span data-stu-id="397d4-275">React to back-end changes</span></span>

<span data-ttu-id="397d4-276">После создания файла cookie файл cookie является единственным источником удостоверения.</span><span class="sxs-lookup"><span data-stu-id="397d4-276">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="397d4-277">Если учетная запись пользователя отключена в серверных системах:</span><span class="sxs-lookup"><span data-stu-id="397d4-277">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="397d4-278">Система проверки подлинности файлов cookie приложения продолжит обрабатывать запросы на основе файла cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-278">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="397d4-279">Пользователь остается в приложении, если файл cookie проверки подлинности является допустимым.</span><span class="sxs-lookup"><span data-stu-id="397d4-279">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="397d4-280">Это <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> событие можно использовать для перехвата и переопределения проверки удостоверения файла cookie.</span><span class="sxs-lookup"><span data-stu-id="397d4-280">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="397d4-281">Проверка файла cookie при каждом запросе снижает риск отзыва пользователей, обращающихся к приложению.</span><span class="sxs-lookup"><span data-stu-id="397d4-281">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="397d4-282">Один из подходов к проверке файлов cookie основан на отслеживании времени изменения пользовательской базы данных.</span><span class="sxs-lookup"><span data-stu-id="397d4-282">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="397d4-283">Если база данных не была изменена с момента выдачи файла cookie пользователя, нет необходимости повторно пройти проверку подлинности пользователя, если его файл cookie остается действительным.</span><span class="sxs-lookup"><span data-stu-id="397d4-283">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="397d4-284">В примере приложения база данных реализуется в `IUserRepository` и сохраняет `LastChanged` значение.</span><span class="sxs-lookup"><span data-stu-id="397d4-284">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="397d4-285">Когда пользователь обновляется в базе данных, `LastChanged` ему присваивается текущее время.</span><span class="sxs-lookup"><span data-stu-id="397d4-285">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="397d4-286">Чтобы сделать файл cookie недействительным при изменении базы данных на основе `LastChanged` значения, создайте файл cookie с `LastChanged` утверждением, содержащим текущее `LastChanged` значение из базы данных:</span><span class="sxs-lookup"><span data-stu-id="397d4-286">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="397d4-287">Чтобы реализовать переопределение для `ValidatePrincipal` события, напишите метод со следующей сигнатурой в классе, производном от: <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents></span><span class="sxs-lookup"><span data-stu-id="397d4-287">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="397d4-288">Ниже приведен пример реализации `CookieAuthenticationEvents`:</span><span class="sxs-lookup"><span data-stu-id="397d4-288">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="397d4-289">Зарегистрируйте экземпляр событий во время регистрации службы файлов cookie `Startup.ConfigureServices` в методе.</span><span class="sxs-lookup"><span data-stu-id="397d4-289">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="397d4-290">Укажите [регистрацию службы](xref:fundamentals/dependency-injection#service-lifetimes) в заданной `CustomCookieAuthenticationEvents` области для класса:</span><span class="sxs-lookup"><span data-stu-id="397d4-290">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="397d4-291">Рассмотрим ситуацию, в которой имя пользователя обновляется&mdash;, а решение не влияет на безопасность каким-либо образом.</span><span class="sxs-lookup"><span data-stu-id="397d4-291">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="397d4-292">Если необходимо обновить субъект-пользователя без разрушения, вызовите метод `context.ReplacePrincipal` и присвойте `context.ShouldRenew` свойству значение `true`.</span><span class="sxs-lookup"><span data-stu-id="397d4-292">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="397d4-293">Описанный здесь подход срабатывает при каждом запросе.</span><span class="sxs-lookup"><span data-stu-id="397d4-293">The approach described here is triggered on every request.</span></span> <span data-ttu-id="397d4-294">Проверка файлов cookie проверки подлинности для всех пользователей при каждом запросе может привести к значительному снижению производительности приложения.</span><span class="sxs-lookup"><span data-stu-id="397d4-294">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-cookies"></a><span data-ttu-id="397d4-295">Постоянные файлы cookie</span><span class="sxs-lookup"><span data-stu-id="397d4-295">Persistent cookies</span></span>

<span data-ttu-id="397d4-296">Возможно, вы хотите, чтобы файл cookie сохранялся в сеансах браузера.</span><span class="sxs-lookup"><span data-stu-id="397d4-296">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="397d4-297">Это сохраняемость следует включать только при явном согласии пользователей с флажком "Запомнить меня" при входе или аналогичном механизме.</span><span class="sxs-lookup"><span data-stu-id="397d4-297">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="397d4-298">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который остается недействительным через замыкания браузера.</span><span class="sxs-lookup"><span data-stu-id="397d4-298">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="397d4-299">Учитываются все настроенные ранее параметры срока действия.</span><span class="sxs-lookup"><span data-stu-id="397d4-299">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="397d4-300">Если срок действия файла cookie истекает при закрытии браузера, браузер очищает файл cookie после его перезапуска.</span><span class="sxs-lookup"><span data-stu-id="397d4-300">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="397d4-301">Значение <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> `true` в <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span><span class="sxs-lookup"><span data-stu-id="397d4-301">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-cookie-expiration"></a><span data-ttu-id="397d4-302">Абсолютный срок действия файла cookie</span><span class="sxs-lookup"><span data-stu-id="397d4-302">Absolute cookie expiration</span></span>

<span data-ttu-id="397d4-303">Абсолютный срок действия можно задать с помощью <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span><span class="sxs-lookup"><span data-stu-id="397d4-303">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="397d4-304">Для создания постоянного файла cookie также `IsPersistent` необходимо задать.</span><span class="sxs-lookup"><span data-stu-id="397d4-304">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="397d4-305">В противном случае файл cookie создается с временем существования на основе сеанса и может истечь до или после полученного билета проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="397d4-305">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="397d4-306">`ExpiresUtc` Если задан <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> параметр, он переопределяет значение параметра <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, если оно задано.</span><span class="sxs-lookup"><span data-stu-id="397d4-306">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="397d4-307">В следующем фрагменте кода создается удостоверение и соответствующий файл cookie, который длится 20 минут.</span><span class="sxs-lookup"><span data-stu-id="397d4-307">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="397d4-308">При этом игнорируются все параметры скользящего срока действия, настроенные ранее.</span><span class="sxs-lookup"><span data-stu-id="397d4-308">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="397d4-309">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="397d4-309">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="397d4-310">Проверки ролей на основе политик</span><span class="sxs-lookup"><span data-stu-id="397d4-310">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
