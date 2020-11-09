---
title: 'Миграция проверки подлинности и Identity в ASP.NET Core 2,0'
author: scottaddie
description: 'В этой статье описаны наиболее распространенные шаги для миграции ASP.NET Core 1. x и Identity ASP.NET Core 2,0.'
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057184"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="46ac6-103">Миграция проверки подлинности и Identity в ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="46ac6-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="46ac6-104">[Скотт Эдди (](https://github.com/scottaddie) и [Хао кунг](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="46ac6-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="46ac6-105">ASP.NET Core 2,0 имеет новую модель для проверки подлинности и [Identity](xref:security/authentication/identity) упрощает настройку с помощью служб.</span><span class="sxs-lookup"><span data-stu-id="46ac6-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="46ac6-106">ASP.NET Core 1. x приложений, использующих проверку подлинности, или Identity можно обновить для использования новой модели, как описано ниже.</span><span class="sxs-lookup"><span data-stu-id="46ac6-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="46ac6-107">Обновить пространства имен</span><span class="sxs-lookup"><span data-stu-id="46ac6-107">Update namespaces</span></span>

<span data-ttu-id="46ac6-108">В 1. x классы, такие `IdentityRole` и, `IdentityUser` были найдены в `Microsoft.AspNetCore.Identity.EntityFrameworkCore` пространстве имен.</span><span class="sxs-lookup"><span data-stu-id="46ac6-108">In 1.x, classes such `IdentityRole` and `IdentityUser` were found in the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="46ac6-109">В 2,0 <xref:Microsoft.AspNetCore.Identity> пространство имен стало новым доменом для нескольких таких классов.</span><span class="sxs-lookup"><span data-stu-id="46ac6-109">In 2.0, the <xref:Microsoft.AspNetCore.Identity> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="46ac6-110">В коде по умолчанию Identity затронутые классы включают `ApplicationUser` и `Startup` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-110">With the default Identity code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="46ac6-111">Настройте `using` инструкции для разрешения затронутых ссылок.</span><span class="sxs-lookup"><span data-stu-id="46ac6-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="46ac6-112">Промежуточное по проверки подлинности и службы</span><span class="sxs-lookup"><span data-stu-id="46ac6-112">Authentication Middleware and services</span></span>

<span data-ttu-id="46ac6-113">В проектах 1. x Проверка подлинности настраивается через по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="46ac6-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="46ac6-114">Для каждой схемы проверки подлинности, которую вы хотите поддерживать, вызывается метод по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="46ac6-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="46ac6-115">Следующий пример 1. x настраивает проверку подлинности Facebook с помощью Identity в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-115">The following 1.x example configures Facebook authentication with Identity in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.UseIdentity();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="46ac6-116">В проектах 2,0 проверка подлинности настраивается с помощью служб.</span><span class="sxs-lookup"><span data-stu-id="46ac6-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="46ac6-117">Каждая схема проверки подлинности регистрируется в `ConfigureServices` методе *Startup.CS* .</span><span class="sxs-lookup"><span data-stu-id="46ac6-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="46ac6-118">`UseIdentity`Метод заменяется на `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-118">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="46ac6-119">Следующий пример 2,0 настраивает проверку подлинности Facebook с помощью Identity в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-119">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak Identity cookies, they're no longer part of IdentityOptions.
    services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="46ac6-120">Этот `UseAuthentication` метод добавляет один компонент промежуточного слоя проверки подлинности, отвечающий за автоматическую проверку подлинности и обработку запросов удаленной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="46ac6-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="46ac6-121">Он заменяет все отдельные компоненты по промежуточного слоя одним общим компонентом по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="46ac6-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="46ac6-122">Ниже приведены инструкции по миграции для каждой основной схемы проверки подлинности 2,0.</span><span class="sxs-lookup"><span data-stu-id="46ac6-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="46ac6-123">CookieПроверка подлинности на основе</span><span class="sxs-lookup"><span data-stu-id="46ac6-123">Cookie-based authentication</span></span>

<span data-ttu-id="46ac6-124">Выберите один из двух параметров ниже и внесите необходимые изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="46ac6-125">Использовать cookie s с Identity</span><span class="sxs-lookup"><span data-stu-id="46ac6-125">Use cookies with Identity</span></span>
    - <span data-ttu-id="46ac6-126">Замените `UseIdentity` на `UseAuthentication` в `Configure` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-126">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="46ac6-127">Вызовите `AddIdentity` метод в `ConfigureServices` методе, чтобы добавить cookie службы проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="46ac6-127">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="46ac6-128">При необходимости вызовите `ConfigureApplicationCookie` метод или `ConfigureExternalCookie` в `ConfigureServices` методе, чтобы настроить Identity cookie Параметры.</span><span class="sxs-lookup"><span data-stu-id="46ac6-128">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="46ac6-129">Использовать cookie s без Identity</span><span class="sxs-lookup"><span data-stu-id="46ac6-129">Use cookies without Identity</span></span>
    - <span data-ttu-id="46ac6-130">Замените `UseCookieAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-130">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="46ac6-131">Вызовите `AddAuthentication` `AddCookie` методы и в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-131">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the cookie to be automatically authenticated and assigned to HttpContext.User,
        // remove the CookieAuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
                .AddCookie(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="46ac6-132">Проверка подлинности носителя JWT</span><span class="sxs-lookup"><span data-stu-id="46ac6-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="46ac6-133">Внесите следующие изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="46ac6-134">Замените `UseJwtBearerAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="46ac6-135">Вызовите `AddJwtBearer` метод в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="46ac6-136">Этот фрагмент кода не используется Identity , поэтому схему по умолчанию следует задать, передав `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` методу.</span><span class="sxs-lookup"><span data-stu-id="46ac6-136">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="46ac6-137">Проверка подлинности OpenID Connect Connect (OIDC)</span><span class="sxs-lookup"><span data-stu-id="46ac6-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="46ac6-138">Внесите следующие изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="46ac6-139">Замените `UseOpenIdConnectAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="46ac6-140">Вызовите `AddOpenIdConnect` метод в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="46ac6-141">Замените `PostLogoutRedirectUri` свойство в `OpenIdConnectOptions` действии на `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="46ac6-142">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="46ac6-142">Facebook authentication</span></span>

<span data-ttu-id="46ac6-143">Внесите следующие изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="46ac6-144">Замените `UseFacebookAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="46ac6-145">Вызовите `AddFacebook` метод в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="46ac6-146">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="46ac6-146">Google authentication</span></span>

<span data-ttu-id="46ac6-147">Внесите следующие изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="46ac6-148">Замените `UseGoogleAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="46ac6-149">Вызовите `AddGoogle` метод в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="46ac6-150">Аутентификация учетной записи Майкрософт</span><span class="sxs-lookup"><span data-stu-id="46ac6-150">Microsoft Account authentication</span></span>

<span data-ttu-id="46ac6-151">Дополнительные сведения о учетная запись Майкрософт проверки подлинности см. в [этой статье о проблемах в GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="46ac6-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="46ac6-152">Внесите следующие изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="46ac6-153">Замените `UseMicrosoftAccountAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="46ac6-154">Вызовите `AddMicrosoftAccount` метод в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="46ac6-155">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="46ac6-155">Twitter authentication</span></span>

<span data-ttu-id="46ac6-156">Внесите следующие изменения в *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="46ac6-157">Замените `UseTwitterAuthentication` вызов метода в `Configure` методе на `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="46ac6-158">Вызовите `AddTwitter` метод в `ConfigureServices` методе:</span><span class="sxs-lookup"><span data-stu-id="46ac6-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="46ac6-159">Настройка схем проверки подлинности по умолчанию</span><span class="sxs-lookup"><span data-stu-id="46ac6-159">Setting default authentication schemes</span></span>

<span data-ttu-id="46ac6-160">В 1. x `AutomaticAuthenticate` `AutomaticChallenge` Свойства и базового класса [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) предназначены для установки в одной схеме проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="46ac6-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="46ac6-161">Не существовало хорошего способа принудительного применения этого.</span><span class="sxs-lookup"><span data-stu-id="46ac6-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="46ac6-162">В 2,0 эти два свойства были удалены как свойства в отдельном `AuthenticationOptions` экземпляре.</span><span class="sxs-lookup"><span data-stu-id="46ac6-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="46ac6-163">Их можно настроить в `AddAuthentication` вызове метода в `ConfigureServices` методе *Startup.CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="46ac6-164">В предыдущем фрагменте кода схема по умолчанию имеет значение `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="46ac6-164">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="46ac6-165">Кроме того, можно использовать перегруженную версию метода, `AddAuthentication` чтобы задать более одного свойства.</span><span class="sxs-lookup"><span data-stu-id="46ac6-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="46ac6-166">В следующем примере перегруженного метода схема по умолчанию имеет значение `CookieAuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-166">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="46ac6-167">Схема проверки подлинности также может быть указана в индивидуальных `[Authorize]` атрибутах или политиках авторизации.</span><span class="sxs-lookup"><span data-stu-id="46ac6-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="46ac6-168">Определите схему по умолчанию в 2,0, если выполняется одно из следующих условий.</span><span class="sxs-lookup"><span data-stu-id="46ac6-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="46ac6-169">Вы хотите, чтобы пользователь автоматически вошел в учетную запись</span><span class="sxs-lookup"><span data-stu-id="46ac6-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="46ac6-170">Использование `[Authorize]` политик атрибутов или авторизации без указания схем</span><span class="sxs-lookup"><span data-stu-id="46ac6-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="46ac6-171">Исключением из этого правила является `AddIdentity` метод.</span><span class="sxs-lookup"><span data-stu-id="46ac6-171">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="46ac6-172">Этот метод добавляет cookie s для вас и задает для приложения схемы проверки подлинности и вызова по умолчанию cookie `IdentityConstants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-172">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="46ac6-173">Кроме того, он задает для схемы входа по умолчанию внешний объект cookie `IdentityConstants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-173">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="46ac6-174">Использование расширений проверки подлинности HttpContext</span><span class="sxs-lookup"><span data-stu-id="46ac6-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="46ac6-175">`IAuthenticationManager`Интерфейс является основной точкой входа в систему проверки подлинности 1. x.</span><span class="sxs-lookup"><span data-stu-id="46ac6-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="46ac6-176">Он был заменен новым набором `HttpContext` методов расширения в `Microsoft.AspNetCore.Authentication` пространстве имен.</span><span class="sxs-lookup"><span data-stu-id="46ac6-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="46ac6-177">Например, проекты 1. x ссылаются на `Authentication` свойство:</span><span class="sxs-lookup"><span data-stu-id="46ac6-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="46ac6-178">В проектах 2,0 импортируйте `Microsoft.AspNetCore.Authentication` пространство имен и удалите ссылки на `Authentication` Свойства:</span><span class="sxs-lookup"><span data-stu-id="46ac6-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="46ac6-179">Проверка подлинности Windows (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="46ac6-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="46ac6-180">Существует два варианта проверки подлинности Windows:</span><span class="sxs-lookup"><span data-stu-id="46ac6-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="46ac6-181">Узел допускает только пользователей, прошедших проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="46ac6-181">The host only allows authenticated users.</span></span> <span data-ttu-id="46ac6-182">На этот вариант не влияют изменения 2,0.</span><span class="sxs-lookup"><span data-stu-id="46ac6-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="46ac6-183">Узел допускает анонимные и прошедшие проверку подлинности пользователи.</span><span class="sxs-lookup"><span data-stu-id="46ac6-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="46ac6-184">На этот вариант влияют изменения 2,0.</span><span class="sxs-lookup"><span data-stu-id="46ac6-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="46ac6-185">Например, приложение должно разрешать анонимных пользователей на уровне [IIS](xref:host-and-deploy/iis/index) или [HTTP.sys](xref:fundamentals/servers/httpsys) , но авторизовать пользователей в контроллере.</span><span class="sxs-lookup"><span data-stu-id="46ac6-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="46ac6-186">В этом сценарии задайте схему по умолчанию в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="46ac6-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="46ac6-187">Для [Microsoft. AspNetCore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)задайте для схемы по умолчанию `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="46ac6-188">Для [Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)задайте для схемы по умолчанию `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="46ac6-189">Если не задать схему по умолчанию, запрос авторизации (запрос) не будет работать со следующим исключением:</span><span class="sxs-lookup"><span data-stu-id="46ac6-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="46ac6-190">`System.InvalidOperationException`: Не указан Аусентикатионсчеме, и Дефаултчалленжесчеме не найден.</span><span class="sxs-lookup"><span data-stu-id="46ac6-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="46ac6-191">Для получения дополнительной информации см. <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="46ac6-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-cookie-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="46ac6-192">IdentityCookieЭкземпляры параметров</span><span class="sxs-lookup"><span data-stu-id="46ac6-192">IdentityCookieOptions instances</span></span>

<span data-ttu-id="46ac6-193">Побочным результатом изменений 2,0 является переключение на использование именованных параметров вместо cookie экземпляров параметров.</span><span class="sxs-lookup"><span data-stu-id="46ac6-193">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="46ac6-194">Возможность настройки Identity cookie имен схем удаляется.</span><span class="sxs-lookup"><span data-stu-id="46ac6-194">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="46ac6-195">Например, проекты 1. x используют [внедрение конструктора](xref:mvc/controllers/dependency-injection#constructor-injection) для передачи `IdentityCookieOptions` параметра в *AccountController.CS* и *ManageController.CS* .</span><span class="sxs-lookup"><span data-stu-id="46ac6-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="46ac6-196">cookieДоступ к внешней схеме проверки подлинности осуществляется из предоставленного экземпляра:</span><span class="sxs-lookup"><span data-stu-id="46ac6-196">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="46ac6-197">Вышеупомянутый внедренный конструктор не нужен в проектах 2,0, и `_externalCookieScheme` поле можно удалить:</span><span class="sxs-lookup"><span data-stu-id="46ac6-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="46ac6-198">в проектах 1. x используется `_externalCookieScheme` поле следующим образом:</span><span class="sxs-lookup"><span data-stu-id="46ac6-198">1.x projects used the `_externalCookieScheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="46ac6-199">В проектах 2,0 замените приведенный выше код следующим.</span><span class="sxs-lookup"><span data-stu-id="46ac6-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="46ac6-200">`IdentityConstants.ExternalScheme`Константу можно использовать напрямую.</span><span class="sxs-lookup"><span data-stu-id="46ac6-200">The `IdentityConstants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="46ac6-201">Разрешите вновь добавленный `SignOutAsync` вызов, импортировав следующее пространство имен:</span><span class="sxs-lookup"><span data-stu-id="46ac6-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="46ac6-202">Добавление Identity свойств навигации POCO пользователя</span><span class="sxs-lookup"><span data-stu-id="46ac6-202">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="46ac6-203">Свойства навигации ядра (EF) для базового `IdentityUser` объекта POCO (обычный объект CLR) были удалены. Entity Framework</span><span class="sxs-lookup"><span data-stu-id="46ac6-203">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="46ac6-204">Если проект 1. x использовал эти свойства, вручную добавьте их обратно в проект 2,0:</span><span class="sxs-lookup"><span data-stu-id="46ac6-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<IdentityUserRole<int>> Roles { get; } = new List<IdentityUserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<IdentityUserClaim<int>> Claims { get; } = new List<IdentityUserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<IdentityUserLogin<int>> Logins { get; } = new List<IdentityUserLogin<int>>();
```

<span data-ttu-id="46ac6-205">Чтобы предотвратить дублирование внешних ключей при выполнении миграции EF Core, добавьте следующий объект в `IdentityDbContext` `OnModelCreating` метод класса (после `base.OnModelCreating();` вызова):</span><span class="sxs-lookup"><span data-stu-id="46ac6-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the ASP.NET Core Identity model and override the defaults if needed.
    // For example, you can rename the ASP.NET Core Identity table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="46ac6-206">Заменить Жетекстерналаусентикатионсчемес</span><span class="sxs-lookup"><span data-stu-id="46ac6-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="46ac6-207">Синхронный метод `GetExternalAuthenticationSchemes` был удален с предпочтением асинхронной версии.</span><span class="sxs-lookup"><span data-stu-id="46ac6-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="46ac6-208">проекты 1. x имеют следующий код в *Controllers/манажеконтроллер. CS* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="46ac6-209">Этот метод отображается в *представлениях, учетной записи или имени входа. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46ac6-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="46ac6-210">В проектах 2,0 используйте <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> метод.</span><span class="sxs-lookup"><span data-stu-id="46ac6-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.Identity.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="46ac6-211">Изменение в *ManageController.CS* напоминает следующий код:</span><span class="sxs-lookup"><span data-stu-id="46ac6-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="46ac6-212">В *Login. cshtml* свойство, `AuthenticationScheme` доступ к которому осуществляется в `foreach` цикле, изменяется на `Name` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="46ac6-213">Изменение свойства Манажелогинсвиевмодел</span><span class="sxs-lookup"><span data-stu-id="46ac6-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="46ac6-214">`ManageLoginsViewModel`Объект используется в `ManageLogins` действии *ManageController.CS* .</span><span class="sxs-lookup"><span data-stu-id="46ac6-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="46ac6-215">В проектах 1. x `OtherLogins` возвращаемым типом свойства объекта является `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="46ac6-216">Этот тип возвращаемого значения требует импорта `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="46ac6-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="46ac6-217">В проектах 2,0 тип возвращаемого значения изменяется на `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="46ac6-218">Этот новый тип возвращаемого значения требует замены импорта импортом `Microsoft.AspNetCore.Http.Authentication` `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="46ac6-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="46ac6-219">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="46ac6-219">Additional resources</span></span>

<span data-ttu-id="46ac6-220">Дополнительные сведения см. в [обсуждении проблемы с проверкой Подлинности 2,0](https://github.com/aspnet/Security/issues/1338) на сайте GitHub.</span><span class="sxs-lookup"><span data-stu-id="46ac6-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
