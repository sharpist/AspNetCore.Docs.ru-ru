---
title: 'Аутентификация Facebook, Google и внешнего поставщика без ASP.NET Core Identity'
author: rick-anderson
description: 'Объяснение использования аутентификации Facebook, Google, Twitter и т. д. без учета пользователей ASP.NET Core Identity .'
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060291"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="5c158-103">Использовать проверку подлинности поставщика социальных сетей без ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5c158-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="5c158-104">[Kirk Ларкин](https://twitter.com/serpent5) и [Рик Андерсон (](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c158-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c158-105"><xref:security/authentication/social/index> Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5c158-106">Подход, описанный в этом разделе, включает в себя ASP.NET Core Identity поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5c158-107">В этом примере демонстрируется использование внешнего поставщика проверки подлинности **без** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5c158-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5c158-108">Это полезно для приложений, которые не занимают все функции ASP.NET Core Identity , но по-прежнему нуждаются в интеграции с доверенным внешним поставщиком проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5c158-109">В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) .</span><span class="sxs-lookup"><span data-stu-id="5c158-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5c158-110">Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google.</span><span class="sxs-lookup"><span data-stu-id="5c158-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5c158-111">Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:</span><span class="sxs-lookup"><span data-stu-id="5c158-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5c158-112">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="5c158-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5c158-113">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="5c158-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5c158-114">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="5c158-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5c158-115">Другие поставщики</span><span class="sxs-lookup"><span data-stu-id="5c158-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5c158-116">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="5c158-116">Configuration</span></span>

<span data-ttu-id="5c158-117">В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов, и <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="5c158-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5c158-118">Вызов метода <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> задает <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> .</span><span class="sxs-lookup"><span data-stu-id="5c158-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="5c158-119">`DefaultScheme`— Это схема по умолчанию, используемая следующими `HttpContext` методами расширения проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="5c158-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5c158-120">Если задать для приложения `DefaultScheme` значение [ Cookie аусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), приложение будет использовать s в Cookie качестве схемы по умолчанию для этих методов расширения.</span><span class="sxs-lookup"><span data-stu-id="5c158-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5c158-121">Если задать для приложения <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> значение [Гугледефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="5c158-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5c158-122">`DefaultChallengeScheme` переопределения `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5c158-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5c158-123"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Дополнительные свойства, которые переопределяются при установке, см. в разделе `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5c158-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5c158-124">В `Startup.Configure` вызов `UseAuthentication` и `UseAuthorization` между вызовом `UseRouting` и `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="5c158-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="5c158-125">При этом задается `HttpContext.User` свойство и выполняется по промежуточного слоя авторизации для запросов:</span><span class="sxs-lookup"><span data-stu-id="5c158-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="5c158-126">Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="5c158-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5c158-127">Дополнительные сведения о cookie проверке подлинности см. в разделе <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="5c158-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5c158-128">Применить авторизацию</span><span class="sxs-lookup"><span data-stu-id="5c158-128">Apply authorization</span></span>

<span data-ttu-id="5c158-129">Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице.</span><span class="sxs-lookup"><span data-stu-id="5c158-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5c158-130">Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="5c158-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5c158-131">Выйти</span><span class="sxs-lookup"><span data-stu-id="5c158-131">Sign out</span></span>

<span data-ttu-id="5c158-132">Чтобы выйти из текущего пользователя и удалить его cookie , вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="5c158-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5c158-133">Следующий код добавляет `Logout` обработчик страницы на страницу *индекса* :</span><span class="sxs-lookup"><span data-stu-id="5c158-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5c158-134">Обратите внимание, что в вызове метода не `SignOutAsync` указана схема проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5c158-135">Приложение из используется `DefaultScheme` для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.</span><span class="sxs-lookup"><span data-stu-id="5c158-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c158-136">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5c158-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c158-137"><xref:security/authentication/social/index> Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="5c158-138">Подход, описанный в этом разделе, включает в себя ASP.NET Core Identity поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="5c158-139">В этом примере демонстрируется использование внешнего поставщика проверки подлинности **без** ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5c158-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="5c158-140">Это полезно для приложений, которые не занимают все функции ASP.NET Core Identity , но по-прежнему нуждаются в интеграции с доверенным внешним поставщиком проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="5c158-141">В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) .</span><span class="sxs-lookup"><span data-stu-id="5c158-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="5c158-142">Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google.</span><span class="sxs-lookup"><span data-stu-id="5c158-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="5c158-143">Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:</span><span class="sxs-lookup"><span data-stu-id="5c158-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="5c158-144">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="5c158-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="5c158-145">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="5c158-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="5c158-146">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="5c158-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="5c158-147">Другие поставщики</span><span class="sxs-lookup"><span data-stu-id="5c158-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="5c158-148">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="5c158-148">Configuration</span></span>

<span data-ttu-id="5c158-149">В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью `AddAuthentication` `AddCookie` методов, и `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="5c158-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="5c158-150">Вызов [аддаусентикатион](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) задает [дефаултсчеме](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)приложения.</span><span class="sxs-lookup"><span data-stu-id="5c158-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="5c158-151">`DefaultScheme`— Это схема по умолчанию, используемая следующими `HttpContext` методами расширения проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="5c158-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="5c158-152">Если задать для приложения `DefaultScheme` значение [ Cookie аусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s"), приложение будет использовать s в Cookie качестве схемы по умолчанию для этих методов расширения.</span><span class="sxs-lookup"><span data-stu-id="5c158-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="5c158-153">Если задать для приложения <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> значение [Гугледефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync` .</span><span class="sxs-lookup"><span data-stu-id="5c158-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="5c158-154">`DefaultChallengeScheme` переопределения `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5c158-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="5c158-155"><xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Дополнительные свойства, которые переопределяются при установке, см. в разделе `DefaultScheme` .</span><span class="sxs-lookup"><span data-stu-id="5c158-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="5c158-156">В `Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство.</span><span class="sxs-lookup"><span data-stu-id="5c158-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="5c158-157">Вызовите `UseAuthentication` метод перед вызовом `UseMvcWithDefaultRoute` или `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="5c158-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="5c158-158">Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="5c158-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="5c158-159">Дополнительные сведения о cookie проверке подлинности см. в разделе <xref:security/authentication/cookie> .</span><span class="sxs-lookup"><span data-stu-id="5c158-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="5c158-160">Применить авторизацию</span><span class="sxs-lookup"><span data-stu-id="5c158-160">Apply authorization</span></span>

<span data-ttu-id="5c158-161">Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице.</span><span class="sxs-lookup"><span data-stu-id="5c158-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="5c158-162">Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="5c158-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="5c158-163">Выйти</span><span class="sxs-lookup"><span data-stu-id="5c158-163">Sign out</span></span>

<span data-ttu-id="5c158-164">Чтобы выйти из текущего пользователя и удалить его cookie , вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="5c158-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="5c158-165">Следующий код добавляет `Logout` обработчик страницы на страницу *индекса* :</span><span class="sxs-lookup"><span data-stu-id="5c158-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="5c158-166">Обратите внимание, что в вызове метода не `SignOutAsync` указана схема проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5c158-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="5c158-167">Приложение из используется `DefaultScheme` для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.</span><span class="sxs-lookup"><span data-stu-id="5c158-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c158-168">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5c158-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
