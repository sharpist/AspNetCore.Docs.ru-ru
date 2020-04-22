---
title: Защищайте Blazor ASP.NET приложение Core WebAssembly с помощью Identity Server
author: guardrex
description: Для создания Blazor нового размещенного приложения с аутентификацией внутри Visual Studio, используюшего бэкэнд [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 4c51200159ced16132e15bb4a1f0915ca0cf5945
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791614"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="8a1d5-103">Защищайте Blazor ASP.NET приложение Core WebAssembly с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="8a1d5-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="8a1d5-104">[Хавьер Кальварро Нельсон](https://github.com/javiercn) и Люк [Лэйтам](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8a1d5-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="8a1d5-105">Для создания Blazor нового размещенного приложения в Visual Studio, которое использует [IdentityServer](https://identityserver.io/) для аутентификации пользователей и вызовов API:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="8a1d5-106">Используйте Visual Studio \*\* Blazor \*\* для создания нового приложения WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="8a1d5-107">Для получения дополнительной информации см. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="8a1d5-108">В **журнале Blazor «Создание нового диалога приложений»** выберите **«Изменение** в разделе **аутентификация».**</span><span class="sxs-lookup"><span data-stu-id="8a1d5-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="8a1d5-109">Выберите **индивидуальные учетные записи пользователей,** за которыми следует **OK**.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="8a1d5-110">Выберите **ASP.NET Core размещает** флажок в разделе **Advanced.**</span><span class="sxs-lookup"><span data-stu-id="8a1d5-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="8a1d5-111">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-111">Select the **Create** button.</span></span>

<span data-ttu-id="8a1d5-112">Чтобы создать приложение в командной оболочке, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="8a1d5-113">Чтобы указать местона вывода, которое создает папку проекта, если она не существует, включите опцию вывода в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="8a1d5-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="8a1d5-114">Имя папки также становится частью названия проекта.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="8a1d5-115">Конфигурация приложения сервера</span><span class="sxs-lookup"><span data-stu-id="8a1d5-115">Server app configuration</span></span>

<span data-ttu-id="8a1d5-116">В следующих разделах описаны дополнения к проекту при включении поддержки аутентификации.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="8a1d5-117">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="8a1d5-117">Startup class</span></span>

<span data-ttu-id="8a1d5-118">Класс `Startup` имеет следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="8a1d5-119">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="8a1d5-120">Удостоверение:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="8a1d5-121">IdentityServer с <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> дополнительным методом помощника, который настраивает некоторые ASP.NET основных конвенций по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="8a1d5-122">Аутентификация <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> с помощью дополнительного метода помощника, который настраивает приложение для проверки токенов JWT, производимых IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="8a1d5-123">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="8a1d5-124">Промежуточная проверка подлинности, которая отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="8a1d5-125">Промежуточное программное обеспечение IdentityServer, которое предоставляет конечные точки Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="8a1d5-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="8a1d5-126">АддапиАвторизация</span><span class="sxs-lookup"><span data-stu-id="8a1d5-126">AddApiAuthorization</span></span>

<span data-ttu-id="8a1d5-127">Метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> помощника настраивает [IdentityServer](https://identityserver.io/) для ASP.NET основных сценариев.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="8a1d5-128">IdentityServer — это мощная и расширяемая платформа для решения проблем безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="8a1d5-129">IdentityServer предоставляет ненужные сложности для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="8a1d5-130">Следовательно, набор конвенций и параметров конфигурации предоставляется, что мы считаем хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="8a1d5-131">После изменения потребности в аутентификации вся мощь IdentityServer по-прежнему доступна для настройки аутентификации в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="8a1d5-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="8a1d5-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="8a1d5-133">Метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> помощника настраивает схему политики для приложения в качестве обработчика аутентификации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="8a1d5-134">Политика настроена таким образом, чтобы identity обрабатывал все запросы, `/Identity`направимые на любой подпуть в пространстве URL-адреса Identity.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="8a1d5-135">Обрабатывает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="8a1d5-136">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-136">Additionally, this method:</span></span>

* <span data-ttu-id="8a1d5-137">Регистрирует ресурс `{APPLICATION NAME}API` API с IdentityServer с `{APPLICATION NAME}API`областью по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="8a1d5-138">Настраивает JWT Bearer Token Middleware для проверки токенов, выпущенных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="8a1d5-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="8a1d5-139">WeatherForecastController</span></span>

<span data-ttu-id="8a1d5-140">В `WeatherForecastController` *(Controllers/WeatherForecastController.cs)* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="8a1d5-141">Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="8a1d5-142">Политика авторизации по умолчанию настроена на использование схемы <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> проверки подлинности по умолчанию, которая настроена на схему политики, упомянутую ранее.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="8a1d5-143">Метод помощника настраивается <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> как обработчик по умолчанию для запросов в приложение.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="8a1d5-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="8a1d5-144">ApplicationDbContext</span></span>

<span data-ttu-id="8a1d5-145">В `ApplicationDbContext` (*Data/ApplicationDbContext.cs),* <xref:Microsoft.EntityFrameworkCore.DbContext> то же самое используется в Identity, за исключением того, что он распространяется <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> на включение схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="8a1d5-146">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="8a1d5-147">Чтобы получить полный контроль над схемой базы данных, наследуете от одного из доступных классов identity <xref:Microsoft.EntityFrameworkCore.DbContext> и назначайте контекст, чтобы включить схему идентификации, вызывая `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в `OnModelCreating` методе.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="8a1d5-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="8a1d5-148">OidcConfigurationController</span></span>

<span data-ttu-id="8a1d5-149">В `OidcConfigurationController` *(Controllers/OidcConfigurationController.cs)* конечная точка клиента предназначена для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="8a1d5-150">Файлы настроек приложения</span><span class="sxs-lookup"><span data-stu-id="8a1d5-150">App settings files</span></span>

<span data-ttu-id="8a1d5-151">В файле настроек приложения *(appsettings.json)* `IdentityServer` в корне проекта раздел описывает список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="8a1d5-152">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-152">In the following example, there's a single client.</span></span> <span data-ttu-id="8a1d5-153">Имя клиента соответствует названию приложения и отображается `ClientId` по конвенции к параметру OAuth.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="8a1d5-154">Профиль указывает на настроенный тип приложения.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="8a1d5-155">Профиль используется внутренне для управления конюмитами, упрощающие процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="8a1d5-156">В файле настроек среды разработки *(приложения. Development.json*) в корне `IdentityServer` проекта, в разделе описывается ключ, используемый для подписания токенов.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="8a1d5-157">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="8a1d5-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="8a1d5-158">Пакет аутентификации</span><span class="sxs-lookup"><span data-stu-id="8a1d5-158">Authentication package</span></span>

<span data-ttu-id="8a1d5-159">Когда приложение создается для использования`Individual`индивидуальных учетных записей пользователей (), приложение автоматически получает ссылку на `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет для пакета в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="8a1d5-160">Пакет предоставляет набор примитивов, которые помогают приложению аутентифицировать пользователей и получать токены для вызова защищенных AIS.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8a1d5-161">При добавлении аутентификации в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="8a1d5-162">Замените `{VERSION}` в предыдущем пакете ссылку на версию `Microsoft.AspNetCore.Blazor.Templates` пакета, показанную <xref:blazor/get-started> в статье.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="8a1d5-163">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="8a1d5-163">API authorization support</span></span>

<span data-ttu-id="8a1d5-164">Поддержка аутентификации пользователей подключается к сервисному контейнеру методом расширения, предусмотренным `Microsoft.AspNetCore.Components.WebAssembly.Authentication` внутри пакета.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="8a1d5-165">Этот метод настраивает все службы, необходимые для взаимодействия приложения с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="8a1d5-166">По умолчанию он загружает конфигурацию `_configuration/{client-id}`для приложения по конвенции от .</span><span class="sxs-lookup"><span data-stu-id="8a1d5-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="8a1d5-167">По конвенции идентификатор клиента устанавливается на имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="8a1d5-168">Этот URL может быть изменен, чтобы указать на отдельную конечную точку, вызовив перегрузку с опциями.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="8a1d5-169">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="8a1d5-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="8a1d5-170">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="8a1d5-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="8a1d5-171">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="8a1d5-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="8a1d5-172">ПеренаправлениеКомпонентToLogin</span><span class="sxs-lookup"><span data-stu-id="8a1d5-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="8a1d5-173">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="8a1d5-173">LoginDisplay component</span></span>

<span data-ttu-id="8a1d5-174">Компонент `LoginDisplay` *(Общий/LoginDisplay.razor)* отображается `MainLayout` в компоненте *(Общий/MainLayout.razor)* и управляет следующим поведением:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="8a1d5-175">Для аутентифицированных пользователей:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-175">For authenticated users:</span></span>
  * <span data-ttu-id="8a1d5-176">Отображает текущее имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-176">Displays the current user name.</span></span>
  * <span data-ttu-id="8a1d5-177">Предлагает ссылку на страницу профиля пользователя в ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="8a1d5-178">Предлагает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="8a1d5-179">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="8a1d5-179">For anonymous users:</span></span>
  * <span data-ttu-id="8a1d5-180">Предлагает возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-180">Offers the option to register.</span></span>
  * <span data-ttu-id="8a1d5-181">Предлагает возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-181">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="8a1d5-182">Компонент аутентификации</span><span class="sxs-lookup"><span data-stu-id="8a1d5-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="8a1d5-183">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="8a1d5-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="8a1d5-184">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="8a1d5-184">Run the app</span></span>

<span data-ttu-id="8a1d5-185">Выполнить приложение из проекта Server.</span><span class="sxs-lookup"><span data-stu-id="8a1d5-185">Run the app from the Server project.</span></span> <span data-ttu-id="8a1d5-186">При использовании Visual Studio выберите проект Server в **Solution Explorer** и выберите кнопку **Run** в панели инструментов или запустите приложение из меню **Debug.**</span><span class="sxs-lookup"><span data-stu-id="8a1d5-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8a1d5-187">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8a1d5-187">Additional resources</span></span>

* [<span data-ttu-id="8a1d5-188">Запрос дополнительных токенов доступа</span><span class="sxs-lookup"><span data-stu-id="8a1d5-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
