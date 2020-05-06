---
title: Аутентификация Facebook, Google и внешнего поставщика без ASP.NET CoreIdentity
author: rick-anderson
description: Объяснение использования проверки подлинности пользователей Facebook, Google, Twitter и т. д. Identityбез ASP.NET Core.
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cc44eb83947540ca9a5a04ffad4fdb8522fab26a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775743"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a><span data-ttu-id="e855e-103">Использовать проверку подлинности поставщика социальных сетей без ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="e855e-103">Use social sign-in provider authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="e855e-104">[Kirk Ларкин](https://twitter.com/serpent5) и [Рик Андерсон (](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e855e-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e855e-105"><xref:security/authentication/social/index>Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-105"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="e855e-106">Подход, описанный в этом разделе, Identity включает ASP.NET Core в качестве поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-106">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="e855e-107">В этом примере показано, как использовать внешний поставщик проверки подлинности **без** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="e855e-107">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="e855e-108">Это полезно для приложений, не требующих всех функций ASP.NET Core Identity, но по-прежнему требуется интеграция с доверенным внешним поставщиком проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-108">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="e855e-109">В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) .</span><span class="sxs-lookup"><span data-stu-id="e855e-109">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="e855e-110">Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google.</span><span class="sxs-lookup"><span data-stu-id="e855e-110">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="e855e-111">Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:</span><span class="sxs-lookup"><span data-stu-id="e855e-111">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="e855e-112">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="e855e-112">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="e855e-113">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="e855e-113">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="e855e-114">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="e855e-114">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="e855e-115">Другие поставщики</span><span class="sxs-lookup"><span data-stu-id="e855e-115">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="e855e-116">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="e855e-116">Configuration</span></span>

<span data-ttu-id="e855e-117">В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>методов <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, и <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :</span><span class="sxs-lookup"><span data-stu-id="e855e-117">In the `ConfigureServices` method, configure the app's authentication schemes with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>, <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, and <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

<span data-ttu-id="e855e-118">Вызов метода <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> задает <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span><span class="sxs-lookup"><span data-stu-id="e855e-118">The call to <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> sets the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>.</span></span> <span data-ttu-id="e855e-119">`DefaultScheme` — Это схема по умолчанию, используемая `HttpContext` следующими методами расширения проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="e855e-119">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="e855e-120">Если задать `DefaultScheme` для приложения значение [кукиеаусентикатиондефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), приложение будет использовать файлы cookie в качестве схемы по умолчанию для этих методов расширения.</span><span class="sxs-lookup"><span data-stu-id="e855e-120">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="e855e-121">Если задать <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> для приложения значение [гугледефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="e855e-121">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="e855e-122">`DefaultChallengeScheme`переопределения `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="e855e-122">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="e855e-123">Дополнительные <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> свойства, которые переопределяются `DefaultScheme` при установке, см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="e855e-123">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="e855e-124">В `Startup.Configure`вызов `UseAuthentication` и `UseAuthorization` между вызовом `UseRouting` и `UseEndpoints`.</span><span class="sxs-lookup"><span data-stu-id="e855e-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` between calling `UseRouting` and `UseEndpoints`.</span></span> <span data-ttu-id="e855e-125">При этом задается `HttpContext.User` свойство и выполняется по промежуточного слоя авторизации для запросов:</span><span class="sxs-lookup"><span data-stu-id="e855e-125">This sets the `HttpContext.User` property and runs the Authorization Middleware for requests:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

<span data-ttu-id="e855e-126">Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="e855e-126">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="e855e-127">Дополнительные сведения о проверке подлинности файлов <xref:security/authentication/cookie>cookie см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="e855e-127">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="e855e-128">Применить авторизацию</span><span class="sxs-lookup"><span data-stu-id="e855e-128">Apply authorization</span></span>

<span data-ttu-id="e855e-129">Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице.</span><span class="sxs-lookup"><span data-stu-id="e855e-129">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="e855e-130">Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="e855e-130">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="e855e-131">Выход</span><span class="sxs-lookup"><span data-stu-id="e855e-131">Sign out</span></span>

<span data-ttu-id="e855e-132">Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="e855e-132">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="e855e-133">Следующий код добавляет обработчик `Logout` страницы на страницу *индекса* :</span><span class="sxs-lookup"><span data-stu-id="e855e-133">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="e855e-134">Обратите внимание, что `SignOutAsync` в вызове метода не указана схема проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-134">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="e855e-135">Приложение `DefaultScheme` из используется для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.</span><span class="sxs-lookup"><span data-stu-id="e855e-135">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e855e-136">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e855e-136">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e855e-137"><xref:security/authentication/social/index>Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-137"><xref:security/authentication/social/index> describes how to enable users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span> <span data-ttu-id="e855e-138">Подход, описанный в этом разделе, Identity включает ASP.NET Core в качестве поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-138">The approach described in that topic includes ASP.NET Core Identity as an authentication provider.</span></span>

<span data-ttu-id="e855e-139">В этом примере показано, как использовать внешний поставщик проверки подлинности **без** ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="e855e-139">This sample demonstrates how to use an external authentication provider **without** ASP.NET Core Identity.</span></span> <span data-ttu-id="e855e-140">Это полезно для приложений, не требующих всех функций ASP.NET Core Identity, но по-прежнему требуется интеграция с доверенным внешним поставщиком проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-140">This is useful for apps that don't require all of the features of ASP.NET Core Identity, but still require integration with a trusted external authentication provider.</span></span>

<span data-ttu-id="e855e-141">В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) .</span><span class="sxs-lookup"><span data-stu-id="e855e-141">This sample uses [Google authentication](xref:security/authentication/google-logins) for authenticating users.</span></span> <span data-ttu-id="e855e-142">Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google.</span><span class="sxs-lookup"><span data-stu-id="e855e-142">Using Google authentication shifts many of the complexities of managing the sign-in process to Google.</span></span> <span data-ttu-id="e855e-143">Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:</span><span class="sxs-lookup"><span data-stu-id="e855e-143">To integrate with a different external authentication provider, see the following topics:</span></span>

* [<span data-ttu-id="e855e-144">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="e855e-144">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="e855e-145">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="e855e-145">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="e855e-146">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="e855e-146">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="e855e-147">Другие поставщики</span><span class="sxs-lookup"><span data-stu-id="e855e-147">Other providers</span></span>](xref:security/authentication/otherlogins)

## <a name="configuration"></a><span data-ttu-id="e855e-148">Параметр Configuration</span><span class="sxs-lookup"><span data-stu-id="e855e-148">Configuration</span></span>

<span data-ttu-id="e855e-149">В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью `AddAuthentication`методов `AddCookie`, и `AddGoogle` :</span><span class="sxs-lookup"><span data-stu-id="e855e-149">In the `ConfigureServices` method, configure the app's authentication schemes with the `AddAuthentication`, `AddCookie`, and `AddGoogle` methods:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

<span data-ttu-id="e855e-150">Вызов [аддаусентикатион](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) задает [дефаултсчеме](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)приложения.</span><span class="sxs-lookup"><span data-stu-id="e855e-150">The call to [AddAuthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) sets the app's [DefaultScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme).</span></span> <span data-ttu-id="e855e-151">`DefaultScheme` — Это схема по умолчанию, используемая `HttpContext` следующими методами расширения проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="e855e-151">The `DefaultScheme` is the default scheme used by the following `HttpContext` authentication extension methods:</span></span>

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

<span data-ttu-id="e855e-152">Если задать `DefaultScheme` для приложения значение [кукиеаусентикатиондефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), приложение будет использовать файлы cookie в качестве схемы по умолчанию для этих методов расширения.</span><span class="sxs-lookup"><span data-stu-id="e855e-152">Setting the app's `DefaultScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("Cookies") configures the app to use Cookies as the default scheme for these extension methods.</span></span> <span data-ttu-id="e855e-153">Если задать <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> для приложения значение [гугледефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync`.</span><span class="sxs-lookup"><span data-stu-id="e855e-153">Setting the app's <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> to [GoogleDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") configures the app to use Google as the default scheme for calls to `ChallengeAsync`.</span></span> <span data-ttu-id="e855e-154">`DefaultChallengeScheme`переопределения `DefaultScheme`.</span><span class="sxs-lookup"><span data-stu-id="e855e-154">`DefaultChallengeScheme` overrides `DefaultScheme`.</span></span> <span data-ttu-id="e855e-155">Дополнительные <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> свойства, которые переопределяются `DefaultScheme` при установке, см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="e855e-155">See <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> for additional properties that override `DefaultScheme` when set.</span></span>

<span data-ttu-id="e855e-156">В `Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство.</span><span class="sxs-lookup"><span data-stu-id="e855e-156">In the `Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="e855e-157">Вызовите `UseAuthentication` метод перед `UseMvcWithDefaultRoute` вызовом `UseMvc`или:</span><span class="sxs-lookup"><span data-stu-id="e855e-157">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

<span data-ttu-id="e855e-158">Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts).</span><span class="sxs-lookup"><span data-stu-id="e855e-158">To learn more about authentication schemes, see [Authentication Concepts](xref:security/authentication/index#authentication-concepts).</span></span> <span data-ttu-id="e855e-159">Дополнительные сведения о проверке подлинности файлов <xref:security/authentication/cookie>cookie см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="e855e-159">To learn more about cookie authentication, see <xref:security/authentication/cookie>.</span></span>

## <a name="apply-authorization"></a><span data-ttu-id="e855e-160">Применить авторизацию</span><span class="sxs-lookup"><span data-stu-id="e855e-160">Apply authorization</span></span>

<span data-ttu-id="e855e-161">Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице.</span><span class="sxs-lookup"><span data-stu-id="e855e-161">Test the app's authentication configuration by applying the `AuthorizeAttribute` attribute to a controller, action, or page.</span></span> <span data-ttu-id="e855e-162">Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="e855e-162">The following code limits access to the *Privacy* page to users that have been authenticated:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a><span data-ttu-id="e855e-163">Выход</span><span class="sxs-lookup"><span data-stu-id="e855e-163">Sign out</span></span>

<span data-ttu-id="e855e-164">Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span><span class="sxs-lookup"><span data-stu-id="e855e-164">To sign out the current user and delete their cookie, call [SignOutAsync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*).</span></span> <span data-ttu-id="e855e-165">Следующий код добавляет обработчик `Logout` страницы на страницу *индекса* :</span><span class="sxs-lookup"><span data-stu-id="e855e-165">The following code adds a `Logout` page handler to the *Index* page:</span></span>

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

<span data-ttu-id="e855e-166">Обратите внимание, что `SignOutAsync` в вызове метода не указана схема проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e855e-166">Notice that the call to `SignOutAsync` does not specify an authentication scheme.</span></span> <span data-ttu-id="e855e-167">Приложение `DefaultScheme` из используется для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.</span><span class="sxs-lookup"><span data-stu-id="e855e-167">The app's `DefaultScheme` of `CookieAuthenticationDefaults.AuthenticationScheme` is used as a fall back.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e855e-168">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e855e-168">Additional resources</span></span>

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
