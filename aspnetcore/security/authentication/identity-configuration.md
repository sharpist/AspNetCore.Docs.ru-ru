---
title: Настройка ASP.NET CoreIdentity
author: AdrienTorris
description: Изучите ASP.NET Core Identity значения по умолчанию и Узнайте, как настроить Identity свойства для использования пользовательских значений.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408972"
---
# <a name="configure-aspnet-core-identity"></a>Настройка ASP.NET CoreIdentity

ASP.NET Core Identity использует значения по умолчанию для таких параметров, как политика паролей, блокировка и конфигурация файлов cookie. Эти параметры можно переопределить в `Startup` классе.

## <a name="identity-options"></a>Параметры Identity

Класс [идентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) представляет параметры, которые можно использовать для настройки Identity системы. `IdentityOptions`необходимо задать **после** вызова метода `AddIdentity` или `AddDefaultIdentity` .

### <a name="claims-identity"></a>ПретензиIdentity

[Идентитйоптионс. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) указывает [клаимсидентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) со свойствами, показанными в следующей таблице.

| Свойство | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [ролеклаимтипе](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Возвращает или задает тип утверждения, используемого для утверждения роли. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [секуритистампклаимтипе](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Возвращает или задает тип утверждения, используемого для утверждения метки безопасности. | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Возвращает или задает тип утверждения, используемого для утверждения идентификатора пользователя. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Возвращает или задает тип утверждения, используемого для утверждения имени пользователя. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Блокирование

Блокировка задается в методе [пассвордсигнинасинк](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) :

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Приведенный выше код основан на `Login` Identity шаблоне. 

Параметры блокировки задаются в `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Приведенный выше код задает [идентитйоптионс](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [локкаутоптионс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) со значениями по умолчанию.

Успешная проверка подлинности сбрасывает количество неудачных попыток доступа и сбрасывает часы.

[Идентитйоптионс. Блокировка](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) задает [локкаутоптионс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) со свойствами, показанными в таблице.

| Свойство | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [алловедфорневусерс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Определяет, можно ли заблокировать нового пользователя. | `true` |
| [дефаултлоккауттимеспан](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Количество времени, в течение которого пользователь блокируется в случае блокировки. | 5 мин |
| [максфаиледакцессаттемптс](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Число неудачных попыток доступа, пока пользователь не будет заблокирован, если блокировка включена. | 5 |

### <a name="password"></a>Пароль

По умолчанию Identity требует, чтобы пароли содержали символы в верхнем регистре, символы нижнего регистра, цифры и символы, отличные от буквенно-цифровых. Пароли должны иметь длину не менее шести символов. [Пассвордоптионс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) можно задать в `Startup.ConfigureServices` .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[Идентитйоптионс. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) указывает [пассвордоптионс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) с свойствами, показанными в таблице.

::: moniker range=">= aspnetcore-2.0"

| Свойство | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [рекуиредигит](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Требуется число от 0-9 в пароле. | `true` |
| [рекуиредленгс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Минимальная длина пароля. | 6 |
| [рекуиреловеркасе](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | В пароле требуется символ нижнего регистра. | `true` |
| [рекуиреноналфанумерик](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | В пароле требуется символ, отличный от буквенно-цифрового. | `true` |
| [рекуиредуникуечарс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Применяется только к ASP.NET Core 2,0 или более поздней версии.<br><br> Требуется число уникальных символов в пароле. | 1 |
| [рекуиреупперкасе](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | В пароле требуется символ верхнего регистра. | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| Свойство | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [рекуиредигит](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Требуется число от 0-9 в пароле. | `true` |
| [рекуиредленгс](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Минимальная длина пароля. | 6 |
| [рекуиреловеркасе](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | В пароле требуется символ нижнего регистра. | `true` |
| [рекуиреноналфанумерик](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | В пароле требуется символ, отличный от буквенно-цифрового. | `true` |
| [рекуиреупперкасе](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | В пароле требуется символ верхнего регистра. | `true` |

::: moniker-end

### <a name="sign-in"></a>Вход

Следующий код задает `SignIn` Параметры (значения по умолчанию):

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[Идентитйоптионс. Signing](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) указывает [сигниноптионс](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) со свойствами, показанными в таблице.

| Свойство | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [рекуиреконфирмедемаил](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Для входа требуется подтвержденное электронное письмо. | `false` |
| [рекуиреконфирмедфоненумбер](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Для входа требуется подтвержденный номер телефона. | `false` |

### <a name="tokens"></a>Токены

[Идентитйоптионс. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) указывает [токеноптионс](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) с помощью свойств, показанных в таблице.

|                                                        Свойство                                                         |                                                                                      Описание:                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [аусентикатортокенпровидер](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       Возвращает или задает объект, `AuthenticatorTokenProvider` используемый для проверки двухфакторной регистрации с помощью средства проверки подлинности.                                       |
|       [чанжеемаилтокенпровидер](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     Возвращает или задает объект, `ChangeEmailTokenProvider` используемый для создания токенов, используемых в сообщениях электронной почты с подтверждением изменений.                                     |
| [чанжефоненумбертокенпровидер](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      Возвращает или задает объект, `ChangePhoneNumberTokenProvider` используемый для создания маркеров, используемых при изменении номеров телефонов.                                      |
| [емаилконфирматионтокенпровидер](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             Возвращает или задает поставщик токенов, используемый для создания токенов, используемых в сообщениях электронной почты с подтверждением учетной записи.                                              |
|     [пассвордресеттокенпровидер](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | Возвращает или задает [иусертвофактортокенпровидер \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) , используемый для создания токенов, используемых в сообщениях электронной почты для сброса пароля. |
|                    [провидермап](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                Используется для создания [поставщика пользовательского токена](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) с ключом, используемым в качестве имени поставщика.                 |

### <a name="user"></a>User (Пользователь)

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[Идентитйоптионс. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) указывает [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) с свойствами, показанными в таблице.

| Свойство | Описание | Значение по умолчанию |
| -------- | ----------- | :-----: |
| [алловедусернамечарактерс](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Допустимые символы в имени пользователя. | абкдефгхижклмнопкрстуввксиз<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [рекуиреуникуимаил](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Требует, чтобы каждый пользователь имел уникальный адрес электронной почты. | `false` |

### <a name="cookie-settings"></a>Параметры файлов cookie

Настройте файл cookie приложения в `Startup.ConfigureServices` . [Конфигуреаппликатионкукие](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) должен вызываться **после** вызова метода `AddIdentity` или `AddDefaultIdentity` .

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

Дополнительные сведения см. в разделе [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Параметры хэша пароля

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Возвращает и задает параметры хэширования паролей.

| Параметр | Описание: |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Режим совместимости, используемый при хэшировании новых паролей. По умолчанию — <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>. Первый байт хэшированного пароля, называемый *маркером формата*, указывает версию алгоритма хэширования, используемого для хэширования пароля. При проверке пароля по хэшу <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> метод выбирает правильный алгоритм на основе первого байта. Клиент может проходить проверку подлинности независимо от того, какая версия алгоритма использовалась для хэширования пароля. Установка режима совместимости влияет на хэширование *новых паролей*. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Число итераций, используемых при хэшировании паролей с помощью PBKDF2. Это значение используется только в том случае, если <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> свойству присвоено значение <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Значение должно быть положительным целым числом, а по умолчанию — `10000` . |

В следующем примере для задано значение <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> `12000` в `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
