---
title: 'Совместное использование проверки подлинности :::no-loc(cookie)::: между приложениями ASP.NET'
author: rick-anderson
description: 'Узнайте, как совместно использовать проверку подлинности для :::no-loc(cookie)::: ASP.NET 4. x и ASP.NET Core приложений.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059693"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="61b86-103">Совместное использование проверки подлинности :::no-loc(cookie)::: между приложениями ASP.NET</span><span class="sxs-lookup"><span data-stu-id="61b86-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="61b86-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="61b86-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="61b86-105">Веб-сайты часто состоят из отдельных веб-приложений, работающих вместе.</span><span class="sxs-lookup"><span data-stu-id="61b86-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="61b86-106">Чтобы обеспечить единый вход, веб-приложения на сайте должны совместно использовать проверку подлинности :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="61b86-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="61b86-107">Для поддержки этого сценария в стеке защиты данных разрешено совместное использование :::no-loc(cookie)::: проверки подлинности Katana и ASP.NET Core :::no-loc(cookie)::: билетов проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="61b86-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="61b86-108">В следующих примерах:</span><span class="sxs-lookup"><span data-stu-id="61b86-108">In the examples that follow:</span></span>

* <span data-ttu-id="61b86-109">Для имени проверки подлинности :::no-loc(cookie)::: задается общее значение `.AspNet.Shared:::no-loc(Cookie):::` .</span><span class="sxs-lookup"><span data-stu-id="61b86-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="61b86-110">Значение `AuthenticationType` задается `:::no-loc(Identity):::.Application` явно или по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="61b86-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="61b86-111">Общее имя приложения позволяет системе защиты данных совместно использовать ключи защиты данных ( `Shared:::no-loc(Cookie):::App` ).</span><span class="sxs-lookup"><span data-stu-id="61b86-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="61b86-112">`:::no-loc(Identity):::.Application` используется в качестве схемы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="61b86-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="61b86-113">Независимо от используемой схемы, ее необходимо использовать постоянно *в* общих приложениях и в :::no-loc(cookie)::: качестве схемы по умолчанию или путем явного задания.</span><span class="sxs-lookup"><span data-stu-id="61b86-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="61b86-114">Схема используется при шифровании и расшифровке :::no-loc(cookie)::: s, поэтому согласованная схема должна использоваться в приложениях.</span><span class="sxs-lookup"><span data-stu-id="61b86-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="61b86-115">Используется общее место хранения [ключей защиты данных](xref:security/data-protection/implementation/key-management) .</span><span class="sxs-lookup"><span data-stu-id="61b86-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="61b86-116">В ASP.NET Core приложениях <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> используется для задания места хранения ключей.</span><span class="sxs-lookup"><span data-stu-id="61b86-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="61b86-117">В .NET Framework приложениях по :::no-loc(Cookie)::: промежуточного слоя для проверки подлинности использует реализацию <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="61b86-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="61b86-118">`DataProtectionProvider` предоставляет службы защиты данных для шифрования и расшифровки полезных данных проверки подлинности :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="61b86-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="61b86-119">`DataProtectionProvider`Экземпляр изолирован от системы защиты данных, используемой другими частями приложения.</span><span class="sxs-lookup"><span data-stu-id="61b86-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="61b86-120">[Датапротектионпровидер. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) принимает, <xref:System.IO.DirectoryInfo> чтобы указать расположение для хранилища ключей защиты данных.</span><span class="sxs-lookup"><span data-stu-id="61b86-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="61b86-121">`DataProtectionProvider` требуется пакет NuGet [Microsoft. AspNetCore. MDAC. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="61b86-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="61b86-122">В ASP.NET Core приложения 2. x сослаться на [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="61b86-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="61b86-123">В .NET Framework приложения добавьте ссылку на пакет в [Microsoft. AspNetCore. "Защита. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="61b86-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="61b86-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Задает общее имя приложения.</span><span class="sxs-lookup"><span data-stu-id="61b86-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="61b86-125">Совместное использование проверки подлинности :::no-loc(cookie)::: с помощью :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="61b86-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="61b86-126">Применяя :::no-loc(ASP.NET Core Identity):::, помните следующие правила.</span><span class="sxs-lookup"><span data-stu-id="61b86-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="61b86-127">Ключи защиты данных и имя приложения должны быть общими для всех приложений.</span><span class="sxs-lookup"><span data-stu-id="61b86-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="61b86-128">В следующих примерах для метода предоставляется общий путь к хранилищу ключей <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="61b86-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="61b86-129">Используйте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> для настройки общего имени общего приложения ( `Shared:::no-loc(Cookie):::App` в следующих примерах).</span><span class="sxs-lookup"><span data-stu-id="61b86-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="61b86-130">Для получения дополнительной информации см. <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="61b86-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="61b86-131">Используйте <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> метод расширения, чтобы настроить службу защиты данных для :::no-loc(cookie)::: s.</span><span class="sxs-lookup"><span data-stu-id="61b86-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="61b86-132">По умолчанию используется тип проверки подлинности `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="61b86-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="61b86-133">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="61b86-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="61b86-134">Совместное использование проверки подлинности :::no-loc(cookie)::: без :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="61b86-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="61b86-135">Если вы используете :::no-loc(cookie)::: s напрямую без :::no-loc(ASP.NET Core Identity)::: , настройте защиту данных и проверку подлинности в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="61b86-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="61b86-136">В следующем примере тип проверки подлинности имеет значение `:::no-loc(Identity):::.Application` :</span><span class="sxs-lookup"><span data-stu-id="61b86-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="61b86-137">Совместное использование :::no-loc(cookie)::: в разных базовых путях</span><span class="sxs-lookup"><span data-stu-id="61b86-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="61b86-138">При проверке подлинности :::no-loc(cookie)::: по умолчанию используется [HttpRequest. пасбасе](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) [ :::no-loc(Cookie)::: . Путь](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span><span class="sxs-lookup"><span data-stu-id="61b86-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="61b86-139">Если приложение :::no-loc(cookie)::: должно совместно использоваться в разных базовых путях, `Path` необходимо переопределить:</span><span class="sxs-lookup"><span data-stu-id="61b86-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="61b86-140">Совместное использование :::no-loc(cookie)::: между поддоменами</span><span class="sxs-lookup"><span data-stu-id="61b86-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="61b86-141">При размещении приложений, совместно использующих :::no-loc(cookie)::: поддомены, укажите общий домен в [ :::no-loc(Cookie)::: . Свойство домена](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) .</span><span class="sxs-lookup"><span data-stu-id="61b86-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="61b86-142">Чтобы предоставить общий доступ к :::no-loc(cookie)::: s в приложениях `contoso.com` , например `first_subdomain.contoso.com` и `second_subdomain.contoso.com` , укажите для него значение `:::no-loc(Cookie):::.Domain` `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="61b86-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="61b86-143">Шифрование неактивных ключей защиты данных</span><span class="sxs-lookup"><span data-stu-id="61b86-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="61b86-144">Для рабочих развертываний настройте `DataProtectionProvider` для шифрования неактивных ключей с помощью DPAPI или x509.</span><span class="sxs-lookup"><span data-stu-id="61b86-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="61b86-145">Для получения дополнительной информации см. <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="61b86-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="61b86-146">В следующем примере отпечаток сертификата предоставляется для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="61b86-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="61b86-147">Совместное использование проверки подлинности :::no-loc(cookie)::: между ASP.NET 4. x и приложениями ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61b86-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="61b86-148">Приложения ASP.NET 4. x, использующие :::no-loc(Cookie)::: по промежуточного слоя проверки подлинности Katana, можно настроить для создания проверки подлинности :::no-loc(cookie)::: , совместимых с :::no-loc(Cookie)::: промежуточным по ASP.NET Core проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="61b86-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="61b86-149">Это позволяет обновлять отдельные приложения большого сайта за несколько этапов, предоставляя возможность беспрепятственного единого входа на сайте.</span><span class="sxs-lookup"><span data-stu-id="61b86-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="61b86-150">Когда приложение использует по :::no-loc(Cookie)::: промежуточного слоя проверки подлинности Katana, оно вызывает `Use:::no-loc(Cookie):::Authentication` в файле *Startup.auth.CS* проекта.</span><span class="sxs-lookup"><span data-stu-id="61b86-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="61b86-151">Проекты веб-приложений ASP.NET 4. x, созданные с помощью Visual Studio 2013 и позднее, :::no-loc(Cookie)::: по умолчанию используют по промежуточного слоя проверки подлинности Katana.</span><span class="sxs-lookup"><span data-stu-id="61b86-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="61b86-152">Хотя `Use:::no-loc(Cookie):::Authentication` является устаревшим и не поддерживается для ASP.NET Core приложений, вызов `Use:::no-loc(Cookie):::Authentication` в приложении ASP.NET 4. x, который использует по :::no-loc(Cookie)::: промежуточного слоя проверки подлинности Katana, является допустимым.</span><span class="sxs-lookup"><span data-stu-id="61b86-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="61b86-153">Приложение ASP.NET 4. x должно быть предназначено для .NET Framework 4.5.1 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="61b86-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="61b86-154">В противном случае не удается установить необходимые пакеты NuGet.</span><span class="sxs-lookup"><span data-stu-id="61b86-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="61b86-155">Чтобы предоставить общий доступ к проверкам подлинности :::no-loc(cookie)::: между приложением ASP.NET 4. x и ASP.NET Core приложением, настройте ASP.NET Core приложение, как указано в разделе [совместное использование проверки подлинности :::no-loc(cookie)::: между приложениями ASP.NET Core](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) , а затем настройте приложение ASP.NET 4. x следующим образом.</span><span class="sxs-lookup"><span data-stu-id="61b86-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="61b86-156">Убедитесь, что пакеты приложения обновлены до последних выпусков.</span><span class="sxs-lookup"><span data-stu-id="61b86-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="61b86-157">Установите пакет [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) в каждое приложение ASP.NET 4. x.</span><span class="sxs-lookup"><span data-stu-id="61b86-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="61b86-158">Нахождение и изменение вызова `Use:::no-loc(Cookie):::Authentication` :</span><span class="sxs-lookup"><span data-stu-id="61b86-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="61b86-159">Измените :::no-loc(cookie)::: имя, чтобы оно совпадало с именем, используемым по :::no-loc(Cookie)::: промежуточного слоя проверки подлинности ASP.NET Core ( `.AspNet.Shared:::no-loc(Cookie):::` в примере).</span><span class="sxs-lookup"><span data-stu-id="61b86-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="61b86-160">В следующем примере тип проверки подлинности имеет значение `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="61b86-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="61b86-161">Укажите экземпляр, `DataProtectionProvider` инициализированный в общем расположении хранилища ключей для защиты данных.</span><span class="sxs-lookup"><span data-stu-id="61b86-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="61b86-162">Убедитесь, что для имени приложения задано общее имя приложения, используемое всеми приложениями, которые совместно используют проверку подлинности :::no-loc(cookie)::: ( `Shared:::no-loc(Cookie):::App` в примере).</span><span class="sxs-lookup"><span data-stu-id="61b86-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="61b86-163">Если не задано значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` и `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , задайте <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> утверждение, которое различает уникальных пользователей.</span><span class="sxs-lookup"><span data-stu-id="61b86-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="61b86-164">*App_Start/стартуп.АУС.КС* :</span><span class="sxs-lookup"><span data-stu-id="61b86-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="61b86-165">При создании удостоверения пользователя тип проверки подлинности ( `:::no-loc(Identity):::.Application` ) должен соответствовать типу, определенному в `AuthenticationType` наборе, `Use:::no-loc(Cookie):::Authentication` в *App_Start/стартуп.АУС.КС* .</span><span class="sxs-lookup"><span data-stu-id="61b86-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="61b86-166">*Модели/ :::no-loc(Identity)::: Models.cs* :</span><span class="sxs-lookup"><span data-stu-id="61b86-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="61b86-167">Использование общей пользовательской базы данных</span><span class="sxs-lookup"><span data-stu-id="61b86-167">Use a common user database</span></span>

<span data-ttu-id="61b86-168">Если приложения используют одну и ту же :::no-loc(Identity)::: схему (та же версия :::no-loc(Identity)::: ), убедитесь, что :::no-loc(Identity)::: система для каждого приложения указывает на одну и ту же базу данных пользователя.</span><span class="sxs-lookup"><span data-stu-id="61b86-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="61b86-169">В противном случае система идентификации выдает ошибки во время выполнения, когда пытается сопоставить информацию в проверке подлинности :::no-loc(cookie)::: с данными в своей базе данных.</span><span class="sxs-lookup"><span data-stu-id="61b86-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="61b86-170">Если :::no-loc(Identity)::: схема отличается от приложений, обычно поскольку приложения используют разные :::no-loc(Identity)::: версии, общий доступ к общей базе данных на основе последней версии :::no-loc(Identity)::: невозможен без повторного сопоставления и добавления столбцов в схемах других приложений :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="61b86-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="61b86-171">Часто более эффективно обновлять другие приложения, чтобы использовать последнюю версию, чтобы общая :::no-loc(Identity)::: база данных могла использоваться приложениями.</span><span class="sxs-lookup"><span data-stu-id="61b86-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="61b86-172">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="61b86-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
