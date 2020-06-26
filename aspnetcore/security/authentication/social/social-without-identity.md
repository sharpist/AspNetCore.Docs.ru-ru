---
title: Аутентификация Facebook, Google и внешнего поставщика без ASP.NET CoreIdentity
author: rick-anderson
description: Объяснение использования проверки подлинности пользователей Facebook, Google, Twitter и т. д. без ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: ed908526604b04f9aebb93935aa3ad4719621526
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406047"
---
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Использовать проверку подлинности поставщика социальных сетей без ASP.NET CoreIdentity

[Kirk Ларкин](https://twitter.com/serpent5) и [Рик Андерсон (](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности. Подход, описанный в этом разделе, включает ASP.NET Core Identity в качестве поставщика проверки подлинности.

В этом примере показано, как использовать внешний поставщик проверки подлинности **без** ASP.NET Core Identity . Это полезно для приложений, не требующих всех функций ASP.NET Core Identity , но по-прежнему требуется интеграция с доверенным внешним поставщиком проверки подлинности.

В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) . Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google. Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:

* [Проверка подлинности Facebook](xref:security/authentication/facebook-logins)
* [Проверка подлинности Майкрософт](xref:security/authentication/microsoft-logins)
* [Проверка подлинности Twitter](xref:security/authentication/twitter-logins)
* [Другие поставщики](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Параметр Configuration

В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> методов, и <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Вызов метода <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> задает <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . `DefaultScheme`— Это схема по умолчанию, используемая следующими `HttpContext` методами расширения проверки подлинности:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Если задать для приложения `DefaultScheme` значение [Кукиеаусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), приложение будет использовать файлы cookie в качестве схемы по умолчанию для этих методов расширения. Если задать для приложения <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> значение [Гугледефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync` . `DefaultChallengeScheme`переопределения `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Дополнительные свойства, которые переопределяются при установке, см. в разделе `DefaultScheme` .

В `Startup.Configure` вызов `UseAuthentication` и `UseAuthorization` между вызовом `UseRouting` и `UseEndpoints` . При этом задается `HttpContext.User` свойство и выполняется по промежуточного слоя авторизации для запросов:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts). Дополнительные сведения о проверке подлинности файлов cookie см. в разделе <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Применить авторизацию

Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице. Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Выйти

Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Следующий код добавляет `Logout` обработчик страницы на страницу *индекса* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Обратите внимание, что в вызове метода не `SignOutAsync` указана схема проверки подлинности. Приложение из используется `DefaultScheme` для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности. Подход, описанный в этом разделе, включает ASP.NET Core Identity в качестве поставщика проверки подлинности.

В этом примере показано, как использовать внешний поставщик проверки подлинности **без** ASP.NET Core Identity . Это полезно для приложений, не требующих всех функций ASP.NET Core Identity , но по-прежнему требуется интеграция с доверенным внешним поставщиком проверки подлинности.

В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) . Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google. Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:

* [Проверка подлинности Facebook](xref:security/authentication/facebook-logins)
* [Проверка подлинности Майкрософт](xref:security/authentication/microsoft-logins)
* [Проверка подлинности Twitter](xref:security/authentication/twitter-logins)
* [Другие поставщики](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Параметр Configuration

В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью `AddAuthentication` `AddCookie` методов, и `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Вызов [аддаусентикатион](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) задает [дефаултсчеме](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)приложения. `DefaultScheme`— Это схема по умолчанию, используемая следующими `HttpContext` методами расширения проверки подлинности:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Если задать для приложения `DefaultScheme` значение [Кукиеаусентикатиондефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), приложение будет использовать файлы cookie в качестве схемы по умолчанию для этих методов расширения. Если задать для приложения <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> значение [Гугледефаултс. аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync` . `DefaultChallengeScheme`переопределения `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Дополнительные свойства, которые переопределяются при установке, см. в разделе `DefaultScheme` .

В `Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство. Вызовите `UseAuthentication` метод перед вызовом `UseMvcWithDefaultRoute` или `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts). Дополнительные сведения о проверке подлинности файлов cookie см. в разделе <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Применить авторизацию

Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице. Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Выйти

Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Следующий код добавляет `Logout` обработчик страницы на страницу *индекса* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Обратите внимание, что в вызове метода не `SignOutAsync` указана схема проверки подлинности. Приложение из используется `DefaultScheme` для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
