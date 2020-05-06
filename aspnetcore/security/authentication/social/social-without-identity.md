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
# <a name="use-social-sign-in-provider-authentication-without-aspnet-core-identity"></a>Использовать проверку подлинности поставщика социальных сетей без ASP.NET CoreIdentity

[Kirk Ларкин](https://twitter.com/serpent5) и [Рик Андерсон (](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index>Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности. Подход, описанный в этом разделе, Identity включает ASP.NET Core в качестве поставщика проверки подлинности.

В этом примере показано, как использовать внешний поставщик проверки подлинности **без** ASP.NET Core Identity. Это полезно для приложений, не требующих всех функций ASP.NET Core Identity, но по-прежнему требуется интеграция с доверенным внешним поставщиком проверки подлинности.

В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) . Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google. Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:

* [Проверка подлинности Facebook](xref:security/authentication/facebook-logins)
* [Проверка подлинности Майкрософт](xref:security/authentication/microsoft-logins)
* [Проверка подлинности Twitter](xref:security/authentication/twitter-logins)
* [Другие поставщики](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Параметр Configuration

В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*>методов <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, и <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Вызов метода <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> задает <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme>. `DefaultScheme` — Это схема по умолчанию, используемая `HttpContext` следующими методами расширения проверки подлинности:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Если задать `DefaultScheme` для приложения значение [кукиеаусентикатиондефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), приложение будет использовать файлы cookie в качестве схемы по умолчанию для этих методов расширения. Если задать <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> для приложения значение [гугледефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync`. `DefaultChallengeScheme`переопределения `DefaultScheme`. Дополнительные <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> свойства, которые переопределяются `DefaultScheme` при установке, см. в разделе.

В `Startup.Configure`вызов `UseAuthentication` и `UseAuthorization` между вызовом `UseRouting` и `UseEndpoints`. При этом задается `HttpContext.User` свойство и выполняется по промежуточного слоя авторизации для запросов:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts). Дополнительные сведения о проверке подлинности файлов <xref:security/authentication/cookie>cookie см. в разделе.

## <a name="apply-authorization"></a>Применить авторизацию

Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице. Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Выход

Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Следующий код добавляет обработчик `Logout` страницы на страницу *индекса* :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Обратите внимание, что `SignOutAsync` в вызове метода не указана схема проверки подлинности. Приложение `DefaultScheme` из используется для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index>Описывает, как разрешить пользователям входить в систему с помощью OAuth 2,0 с учетными данными от внешних поставщиков проверки подлинности. Подход, описанный в этом разделе, Identity включает ASP.NET Core в качестве поставщика проверки подлинности.

В этом примере показано, как использовать внешний поставщик проверки подлинности **без** ASP.NET Core Identity. Это полезно для приложений, не требующих всех функций ASP.NET Core Identity, но по-прежнему требуется интеграция с доверенным внешним поставщиком проверки подлинности.

В этом примере для проверки подлинности пользователей используется [Проверка подлинности Google](xref:security/authentication/google-logins) . Использование аутентификации Google сдвигает многие сложности управления процессом входа в Google. Чтобы выполнить интеграцию с другим внешним поставщиком проверки подлинности, см. следующие разделы:

* [Проверка подлинности Facebook](xref:security/authentication/facebook-logins)
* [Проверка подлинности Майкрософт](xref:security/authentication/microsoft-logins)
* [Проверка подлинности Twitter](xref:security/authentication/twitter-logins)
* [Другие поставщики](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Параметр Configuration

В `ConfigureServices` методе настройте схемы проверки подлинности приложения с помощью `AddAuthentication`методов `AddCookie`, и `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Вызов [аддаусентикатион](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) задает [дефаултсчеме](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)приложения. `DefaultScheme` — Это схема по умолчанию, используемая `HttpContext` следующими методами расширения проверки подлинности:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Если задать `DefaultScheme` для приложения значение [кукиеаусентикатиондефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) ("cookies"), приложение будет использовать файлы cookie в качестве схемы по умолчанию для этих методов расширения. Если задать <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> для приложения значение [гугледефаултс. Аусентикатионсчеме](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google"), приложение будет использовать Google в качестве схемы по умолчанию для вызовов `ChallengeAsync`. `DefaultChallengeScheme`переопределения `DefaultScheme`. Дополнительные <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions> свойства, которые переопределяются `DefaultScheme` при установке, см. в разделе.

В `Configure` методе вызовите `UseAuthentication` метод для вызова по промежуточного слоя проверки подлинности, устанавливающего `HttpContext.User` свойство. Вызовите `UseAuthentication` метод перед `UseMvcWithDefaultRoute` вызовом `UseMvc`или:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Дополнительные сведения о схемах проверки подлинности см. в разделе [Основные понятия проверки подлинности](xref:security/authentication/index#authentication-concepts). Дополнительные сведения о проверке подлинности файлов <xref:security/authentication/cookie>cookie см. в разделе.

## <a name="apply-authorization"></a>Применить авторизацию

Проверьте конфигурацию проверки подлинности приложения, применив `AuthorizeAttribute` атрибут к контроллеру, действию или странице. Следующий код ограничивает доступ к странице *конфиденциальности* для пользователей, которые прошли проверку подлинности:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Выход

Чтобы выйти из текущего пользователя и удалить его файл cookie, вызовите [сигнаутасинк](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*). Следующий код добавляет обработчик `Logout` страницы на страницу *индекса* :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Обратите внимание, что `SignOutAsync` в вызове метода не указана схема проверки подлинности. Приложение `DefaultScheme` из используется для `CookieAuthenticationDefaults.AuthenticationScheme` возврата.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
