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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cookie-sharing
ms.openlocfilehash: b8de5ea1da47c7813aac0137719989959d4c8ce4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407035"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="3a625-103">Совместное использование файлов cookie проверки подлинности между приложениями ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3a625-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="3a625-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="3a625-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3a625-105">Веб-сайты часто состоят из отдельных веб-приложений, работающих вместе.</span><span class="sxs-lookup"><span data-stu-id="3a625-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="3a625-106">Чтобы обеспечить единый вход, веб-приложения на сайте должны использовать файлы cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="3a625-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="3a625-107">Для поддержки этого сценария в стеке защиты данных разрешено совместное использование проверки подлинности Katana cookie и ASP.NET Core билетов проверки подлинности файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="3a625-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="3a625-108">В следующих примерах:</span><span class="sxs-lookup"><span data-stu-id="3a625-108">In the examples that follow:</span></span>

* <span data-ttu-id="3a625-109">Для имени файла cookie проверки подлинности задано общее значение `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="3a625-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="3a625-110">Значение `AuthenticationType` задается `Identity.Application` явно или по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3a625-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="3a625-111">Общее имя приложения позволяет системе защиты данных совместно использовать ключи защиты данных ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="3a625-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="3a625-112">`Identity.Application`используется в качестве схемы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="3a625-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="3a625-113">Независимо от используемой схемы, ее необходимо использовать последовательно *в и через* общие приложения cookie либо в качестве схемы по умолчанию, либо путем их явного задания.</span><span class="sxs-lookup"><span data-stu-id="3a625-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="3a625-114">Схема используется при шифровании и расшифровке файлов cookie, поэтому согласованная схема должна использоваться в приложениях.</span><span class="sxs-lookup"><span data-stu-id="3a625-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="3a625-115">Используется общее место хранения [ключей защиты данных](xref:security/data-protection/implementation/key-management) .</span><span class="sxs-lookup"><span data-stu-id="3a625-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="3a625-116">В ASP.NET Core приложениях <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> используется для задания места хранения ключей.</span><span class="sxs-lookup"><span data-stu-id="3a625-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="3a625-117">В .NET Framework приложениях по промежуточного слоя для проверки подлинности файлов использует реализацию <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="3a625-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="3a625-118">`DataProtectionProvider`предоставляет службы защиты данных для шифрования и расшифровки полезных данных cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="3a625-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="3a625-119">`DataProtectionProvider`Экземпляр изолирован от системы защиты данных, используемой другими частями приложения.</span><span class="sxs-lookup"><span data-stu-id="3a625-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="3a625-120">[Датапротектионпровидер. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) принимает, <xref:System.IO.DirectoryInfo> чтобы указать расположение для хранилища ключей защиты данных.</span><span class="sxs-lookup"><span data-stu-id="3a625-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="3a625-121">`DataProtectionProvider`требуется пакет NuGet [Microsoft. AspNetCore. MDAC. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="3a625-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="3a625-122">В ASP.NET Core приложения 2. x сослаться на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3a625-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="3a625-123">В .NET Framework приложения добавьте ссылку на пакет в [Microsoft. AspNetCore. "Защита. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="3a625-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="3a625-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Задает общее имя приложения.</span><span class="sxs-lookup"><span data-stu-id="3a625-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a><span data-ttu-id="3a625-125">Совместное использование файлов cookie проверки подлинности с помощью ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3a625-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="3a625-126">При использовании ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="3a625-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="3a625-127">Ключи защиты данных и имя приложения должны быть общими для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="3a625-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="3a625-128">В следующих примерах для метода предоставляется общий путь к хранилищу ключей <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="3a625-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="3a625-129">Используйте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> для настройки общего имени общего приложения ( `SharedCookieApp` в следующих примерах).</span><span class="sxs-lookup"><span data-stu-id="3a625-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="3a625-130">Для получения дополнительной информации см. <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="3a625-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="3a625-131">Используйте <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> метод расширения, чтобы настроить службу защиты данных для файлов cookie.</span><span class="sxs-lookup"><span data-stu-id="3a625-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="3a625-132">По умолчанию используется тип проверки подлинности `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="3a625-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="3a625-133">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3a625-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a><span data-ttu-id="3a625-134">Совместное использование файлов cookie проверки подлинности без ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="3a625-134">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="3a625-135">При использовании файлов cookie напрямую без ASP.NET Core Identity Настройте защиту данных и проверку подлинности в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3a625-135">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3a625-136">В следующем примере тип проверки подлинности имеет значение `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="3a625-136">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

## <a name="share-cookies-across-different-base-paths"></a><span data-ttu-id="3a625-137">Совместное использование файлов cookie в разных базовых путях</span><span class="sxs-lookup"><span data-stu-id="3a625-137">Share cookies across different base paths</span></span>

<span data-ttu-id="3a625-138">Файл cookie для проверки подлинности использует [HttpRequest. пасбасе](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) в качестве [файла cookie. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3a625-138">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="3a625-139">Если файл cookie приложения должен совместно использоваться в разных базовых путях, `Path` необходимо переопределить:</span><span class="sxs-lookup"><span data-stu-id="3a625-139">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a><span data-ttu-id="3a625-140">Совместное использование файлов cookie в поддоменах</span><span class="sxs-lookup"><span data-stu-id="3a625-140">Share cookies across subdomains</span></span>

<span data-ttu-id="3a625-141">При размещении приложений, которые совместно используют файлы cookie в поддоменах, укажите общий домен в свойстве [cookie. domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="3a625-141">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="3a625-142">Для совместного использования файлов cookie в приложениях в `contoso.com` , например `first_subdomain.contoso.com` и `second_subdomain.contoso.com` , `Cookie.Domain` укажите `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="3a625-142">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="3a625-143">Шифрование неактивных ключей защиты данных</span><span class="sxs-lookup"><span data-stu-id="3a625-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="3a625-144">Для рабочих развертываний настройте `DataProtectionProvider` для шифрования неактивных ключей с помощью DPAPI или x509.</span><span class="sxs-lookup"><span data-stu-id="3a625-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="3a625-145">Для получения дополнительной информации см. <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="3a625-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="3a625-146">В следующем примере отпечаток сертификата предоставляется для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="3a625-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="3a625-147">Совместное использование файлов cookie проверки подлинности в приложениях ASP.NET 4. x и ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a625-147">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="3a625-148">Приложения ASP.NET 4. x, использующие по промежуточного слоя для проверки подлинности Katana cookie, можно настроить для создания файлов cookie проверки подлинности, совместимых с по промежуточного слоя ASP.NET Core cookie Authentication.</span><span class="sxs-lookup"><span data-stu-id="3a625-148">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="3a625-149">Это позволяет обновлять отдельные приложения большого сайта за несколько этапов, предоставляя возможность беспрепятственного единого входа на сайте.</span><span class="sxs-lookup"><span data-stu-id="3a625-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="3a625-150">Когда приложение использует по промежуточного слоя проверки подлинности Katana cookie, оно вызывает `UseCookieAuthentication` в файле *Startup.auth.CS* проекта.</span><span class="sxs-lookup"><span data-stu-id="3a625-150">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="3a625-151">Проекты веб-приложений ASP.NET 4. x, созданные с помощью Visual Studio 2013 и позднее, по умолчанию используют по промежуточного слоя проверки подлинности Katana cookie.</span><span class="sxs-lookup"><span data-stu-id="3a625-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="3a625-152">Хотя `UseCookieAuthentication` является устаревшим и не поддерживается для ASP.NET Core приложений, вызов `UseCookieAuthentication` в приложении ASP.NET 4. x, использующий по промежуточного слоя проверки подлинности Katana cookie, является допустимым.</span><span class="sxs-lookup"><span data-stu-id="3a625-152">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="3a625-153">Приложение ASP.NET 4. x должно быть предназначено для .NET Framework 4.5.1 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="3a625-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="3a625-154">В противном случае не удается установить необходимые пакеты NuGet.</span><span class="sxs-lookup"><span data-stu-id="3a625-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="3a625-155">Чтобы предоставить общий доступ к файлам cookie проверки подлинности между приложением ASP.NET 4. x и ASP.NET Core приложением, настройте ASP.NET Core приложение, как указано в разделе [общий доступ к файлам cookie проверки подлинности между приложениями ASP.NET Core](#share-authentication-cookies-with-aspnet-core-identity) , а затем настройте приложение ASP.NET 4. x следующим образом.</span><span class="sxs-lookup"><span data-stu-id="3a625-155">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="3a625-156">Убедитесь, что пакеты приложения обновлены до последних выпусков.</span><span class="sxs-lookup"><span data-stu-id="3a625-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="3a625-157">Установите пакет [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) в каждое приложение ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="3a625-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="3a625-158">Нахождение и изменение вызова `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="3a625-158">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="3a625-159">Измените имя файла cookie, чтобы оно совпадало с именем, используемым по промежуточного слоя проверки подлинности ASP.NET Core cookie ( `.AspNet.SharedCookie` в примере).</span><span class="sxs-lookup"><span data-stu-id="3a625-159">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="3a625-160">В следующем примере тип проверки подлинности имеет значение `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="3a625-160">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="3a625-161">Укажите экземпляр, `DataProtectionProvider` инициализированный в общем расположении хранилища ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="3a625-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="3a625-162">Убедитесь, что для имени приложения задано общее имя приложения, используемое всеми приложениями, которые совместно используют файлы cookie проверки подлинности ( `SharedCookieApp` в примере).</span><span class="sxs-lookup"><span data-stu-id="3a625-162">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="3a625-163">Если не задано значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` и `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , задайте <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> утверждение, которое различает уникальных пользователей.</span><span class="sxs-lookup"><span data-stu-id="3a625-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="3a625-164">*App_Start/стартуп.АУС.КС*:</span><span class="sxs-lookup"><span data-stu-id="3a625-164">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="3a625-165">При создании удостоверения пользователя тип проверки подлинности ( `Identity.Application` ) должен соответствовать типу, определенному в `AuthenticationType` наборе, `UseCookieAuthentication` в *App_Start/стартуп.АУС.КС*.</span><span class="sxs-lookup"><span data-stu-id="3a625-165">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="3a625-166">*Models/идентитимоделс. CS*:</span><span class="sxs-lookup"><span data-stu-id="3a625-166">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="3a625-167">Использование общей пользовательской базы данных</span><span class="sxs-lookup"><span data-stu-id="3a625-167">Use a common user database</span></span>

<span data-ttu-id="3a625-168">Если приложения используют одну и ту же Identity схему (та же версия Identity ), убедитесь, что Identity система для каждого приложения указывает на одну и ту же базу данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="3a625-168">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="3a625-169">В противном случае система идентификации выдает ошибки во время выполнения, когда пытается сопоставить информацию в файле cookie проверки подлинности с данными в своей базе данных.</span><span class="sxs-lookup"><span data-stu-id="3a625-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="3a625-170">Если Identity схема отличается от приложений, обычно поскольку приложения используют разные Identity версии, общий доступ к общей базе данных на основе последней версии Identity невозможен без повторного сопоставления и добавления столбцов в схемах других приложений Identity .</span><span class="sxs-lookup"><span data-stu-id="3a625-170">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="3a625-171">Часто более эффективно обновлять другие приложения, чтобы использовать последнюю версию, чтобы общая Identity база данных могла использоваться приложениями.</span><span class="sxs-lookup"><span data-stu-id="3a625-171">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a625-172">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3a625-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
