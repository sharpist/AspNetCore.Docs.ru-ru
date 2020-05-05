---
title: Совместное использование файлов cookie проверки подлинности между приложениями ASP.NET
author: rick-anderson
description: Узнайте, как совместно использовать файлы cookie проверки подлинности в приложениях ASP.NET 4. x и ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: a6aac53008e634e87b18bd34b3d2babdad74ae50
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776146"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Совместное использование файлов cookie проверки подлинности между приложениями ASP.NET

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

Веб-сайты часто состоят из отдельных веб-приложений, работающих вместе. Чтобы обеспечить единый вход, веб-приложения на сайте должны использовать файлы cookie проверки подлинности. Для поддержки этого сценария в стеке защиты данных разрешено совместное использование проверки подлинности Katana cookie и ASP.NET Core билетов проверки подлинности файлов cookie.

В следующих примерах:

* Для имени файла cookie проверки подлинности задано общее `.AspNet.SharedCookie`значение.
* `AuthenticationType` Значение задается `Identity.Application` явно или по умолчанию.
* Общее имя приложения позволяет системе защиты данных совместно использовать ключи защиты данных (`SharedCookieApp`).
* `Identity.Application`используется в качестве схемы проверки подлинности. Независимо от используемой схемы, ее необходимо использовать последовательно *в и через* общие приложения cookie либо в качестве схемы по умолчанию, либо путем их явного задания. Схема используется при шифровании и расшифровке файлов cookie, поэтому согласованная схема должна использоваться в приложениях.
* Используется общее место хранения [ключей защиты данных](xref:security/data-protection/implementation/key-management) .
  * В ASP.NET Core приложениях <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> используется для задания места хранения ключей.
  * В .NET Framework приложениях по промежуточного слоя для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>проверки подлинности файлов использует реализацию. `DataProtectionProvider`предоставляет службы защиты данных для шифрования и расшифровки полезных данных cookie проверки подлинности. `DataProtectionProvider` Экземпляр изолирован от системы защиты данных, используемой другими частями приложения. [Датапротектионпровидер. Create (System. IO. DirectoryInfo, Action\<идатапротектионбуилдер>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) принимает, <xref:System.IO.DirectoryInfo> чтобы указать расположение для хранилища ключей защиты данных.
* `DataProtectionProvider`требуется пакет NuGet [Microsoft. AspNetCore. MDAC. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * В ASP.NET Core приложения 2. x сослаться на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).
  * В .NET Framework приложения добавьте ссылку на пакет в [Microsoft. AspNetCore. "Защита. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Задает общее имя приложения.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Совместное использование файлов cookie проверки подлинности с помощью ASP.NET CoreIdentity

При использовании ASP.NET Core Identity:

* Ключи защиты данных и имя приложения должны быть общими для всех приложений. В следующих примерах для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> метода предоставляется общий путь к хранилищу ключей. Используйте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> для настройки общего имени общего приложения (`SharedCookieApp` в следующих примерах). Для получения дополнительной информации см. <xref:security/data-protection/configuration/overview>.
* Используйте метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> расширения, чтобы настроить службу защиты данных для файлов cookie.
* По умолчанию используется `Identity.Application`тип проверки подлинности.

В `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Совместное использование файлов cookie проверки подлинности без ASP.NET CoreIdentity

При использовании файлов cookie напрямую без IdentityASP.NET Core настройте защиту данных и проверку подлинности в `Startup.ConfigureServices`. В следующем примере тип проверки подлинности имеет значение `Identity.Application`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>Совместное использование файлов cookie в разных базовых путях

Файл cookie для проверки подлинности использует [HttpRequest. пасбасе](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) в качестве [файла cookie. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)по умолчанию. Если файл cookie приложения должен совместно использоваться в разных базовых путях, `Path` необходимо переопределить:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>Совместное использование файлов cookie в поддоменах

При размещении приложений, которые совместно используют файлы cookie в поддоменах, укажите общий домен в свойстве [cookie. domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) . Для совместного использования файлов cookie `contoso.com`в приложениях в `first_subdomain.contoso.com` , `second_subdomain.contoso.com`например и, `Cookie.Domain` укажите `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Шифрование неактивных ключей защиты данных

Для рабочих развертываний настройте `DataProtectionProvider` для шифрования неактивных ключей с помощью DPAPI или x509. Для получения дополнительной информации см. <xref:security/data-protection/implementation/key-encryption-at-rest>. В следующем примере отпечаток сертификата предоставляется для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Совместное использование файлов cookie проверки подлинности в приложениях ASP.NET 4. x и ASP.NET Core

Приложения ASP.NET 4. x, использующие по промежуточного слоя для проверки подлинности Katana cookie, можно настроить для создания файлов cookie проверки подлинности, совместимых с по промежуточного слоя ASP.NET Core cookie Authentication. Это позволяет обновлять отдельные приложения большого сайта за несколько этапов, предоставляя возможность беспрепятственного единого входа на сайте.

Когда приложение использует по промежуточного слоя проверки подлинности `UseCookieAuthentication` Katana cookie, оно вызывает в файле *Startup.auth.CS* проекта. Проекты веб-приложений ASP.NET 4. x, созданные с помощью Visual Studio 2013 и позднее, по умолчанию используют по промежуточного слоя проверки подлинности Katana cookie. Хотя `UseCookieAuthentication` является устаревшим и не поддерживается для ASP.NET Core приложений, вызов `UseCookieAuthentication` в приложении ASP.NET 4. x, использующий по промежуточного слоя проверки подлинности Katana cookie, является допустимым.

Приложение ASP.NET 4. x должно быть предназначено для .NET Framework 4.5.1 или более поздней версии. В противном случае не удается установить необходимые пакеты NuGet.

Чтобы предоставить общий доступ к файлам cookie проверки подлинности между приложением ASP.NET 4. x и ASP.NET Core приложением, настройте ASP.NET Core приложение, как указано в разделе [общий доступ к файлам cookie проверки подлинности между приложениями ASP.NET Core](#share-authentication-cookies-with-aspnet-core-identity) , а затем настройте приложение ASP.NET 4. x следующим образом.

Убедитесь, что пакеты приложения обновлены до последних выпусков. Установите пакет [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) в каждое приложение ASP.NET 4. x.

Нахождение и изменение вызова `UseCookieAuthentication`:

* Измените имя файла cookie, чтобы оно совпадало с именем, используемым по промежуточного слоя проверки подлинности ASP.NET Core cookie (`.AspNet.SharedCookie` в примере).
* В следующем примере тип проверки подлинности имеет значение `Identity.Application`.
* Укажите экземпляр, `DataProtectionProvider` инициализированный в общем расположении хранилища ключей для защиты данных.
* Убедитесь, что для имени приложения задано общее имя приложения, используемое всеми приложениями, которые совместно используют файлы`SharedCookieApp` cookie проверки подлинности (в примере).

Если не `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` задано `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`значение и <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> , задайте утверждение, которое различает уникальных пользователей.

*App_Start/стартуп.АУС.КС*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

При создании удостоверения пользователя тип проверки подлинности (`Identity.Application`) должен соответствовать типу, определенному `AuthenticationType` в наборе, `UseCookieAuthentication` в *App_Start/стартуп.АУС.КС*.

*Models/идентитимоделс. CS*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Использование общей пользовательской базы данных

Если приложения используют одну и Identity ту же схему (та Identityже версия), убедитесь Identity , что система для каждого приложения указывает на одну и ту же базу данных пользователя. В противном случае система идентификации выдает ошибки во время выполнения, когда пытается сопоставить информацию в файле cookie проверки подлинности с данными в своей базе данных.

Если Identity схема отличается от приложений, обычно поскольку приложения используют разные Identity версии, общий доступ к общей базе данных на основе последней версии Identity невозможен без повторного сопоставления и добавления столбцов в Identity схемах других приложений. Часто более эффективно обновлять другие приложения, чтобы использовать последнюю Identity версию, чтобы общая база данных могла использоваться приложениями.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:host-and-deploy/web-farm>
