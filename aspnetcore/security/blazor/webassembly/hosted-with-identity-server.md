---
title: Защита размещенного Blazor в ASP.NET Core приложения сборки с помощью Identity Server
author: guardrex
description: Создание нового Blazor размещенного приложения с проверкой подлинности в Visual Studio, использующей серверную часть [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138614"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="d76d1-103">Защита размещенного Blazor в ASP.NET Core приложения сборки с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="d76d1-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="d76d1-104">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d76d1-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d76d1-105">Чтобы создать новое Blazor размещенное приложение в Visual Studio, использующее [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API:</span><span class="sxs-lookup"><span data-stu-id="d76d1-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="d76d1-106">Создайте новое \*\* Blazor приложение сборки\*\* с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="d76d1-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="d76d1-107">Для получения дополнительной информации см. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="d76d1-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="d76d1-108">В диалоговом окне **Создание Blazor нового приложения** выберите **изменить** в разделе **Проверка подлинности** .</span><span class="sxs-lookup"><span data-stu-id="d76d1-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="d76d1-109">Выберите **учетные записи отдельных пользователей** , а затем нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="d76d1-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="d76d1-110">Установите флажок **ASP.NET Core размещено** в разделе **Дополнительно** .</span><span class="sxs-lookup"><span data-stu-id="d76d1-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="d76d1-111">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="d76d1-111">Select the **Create** button.</span></span>

<span data-ttu-id="d76d1-112">Чтобы создать приложение в командной оболочке, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="d76d1-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="d76d1-113">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="d76d1-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d76d1-114">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="d76d1-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="d76d1-115">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="d76d1-115">Server app configuration</span></span>

<span data-ttu-id="d76d1-116">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="d76d1-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="d76d1-117">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="d76d1-117">Startup class</span></span>

<span data-ttu-id="d76d1-118">`Startup` Класс имеет следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="d76d1-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="d76d1-119">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d76d1-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="d76d1-120">Удостоверение:</span><span class="sxs-lookup"><span data-stu-id="d76d1-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="d76d1-121">IdentityServer с дополнительным <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> вспомогательным методом, который настраивает некоторые соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="d76d1-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="d76d1-122">Проверка подлинности <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> с помощью дополнительного вспомогательного метода, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="d76d1-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="d76d1-123">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="d76d1-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="d76d1-124">По промежуточного слоя для проверки подлинности, которое отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="d76d1-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="d76d1-125">По промежуточного слоя IdentityServer, которое предоставляет конечные точки Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="d76d1-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="d76d1-126">По промежуточного слоя для проверки подлинности и авторизации:</span><span class="sxs-lookup"><span data-stu-id="d76d1-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="d76d1-127">аддапиаусоризатион</span><span class="sxs-lookup"><span data-stu-id="d76d1-127">AddApiAuthorization</span></span>

<span data-ttu-id="d76d1-128"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Вспомогательный метод настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d76d1-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="d76d1-129">IdentityServer — это мощная и расширяемая платформа для обработки проблем безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="d76d1-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="d76d1-130">IdentityServer предоставляет ненужную сложность для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="d76d1-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="d76d1-131">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые мы рассмотрим хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="d76d1-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="d76d1-132">После изменения требований к проверке подлинности все возможности IdentityServer по-прежнему доступны для настройки проверки подлинности в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="d76d1-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="d76d1-133">аддидентитисервержвт</span><span class="sxs-lookup"><span data-stu-id="d76d1-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="d76d1-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="d76d1-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="d76d1-135">Политика настроена на разрешение идентификации для обработки всех запросов, направляемых по любому вложенному пути в пространстве `/Identity`URL-адресов удостоверений.</span><span class="sxs-lookup"><span data-stu-id="d76d1-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="d76d1-136"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="d76d1-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="d76d1-137">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="d76d1-137">Additionally, this method:</span></span>

* <span data-ttu-id="d76d1-138">Регистрирует ресурс `{APPLICATION NAME}API` API в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="d76d1-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="d76d1-139">Настраивает промежуточное по для токена носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="d76d1-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="d76d1-140">веасерфорекастконтроллер</span><span class="sxs-lookup"><span data-stu-id="d76d1-140">WeatherForecastController</span></span>

<span data-ttu-id="d76d1-141">В `WeatherForecastController` (*Controllers/веасерфорекастконтроллер. CS*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу.</span><span class="sxs-lookup"><span data-stu-id="d76d1-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="d76d1-142">Атрибут указывает, что пользователь должен быть санкционирован на основании политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="d76d1-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="d76d1-143">Политика авторизации по умолчанию настроена для использования схемы проверки подлинности по умолчанию, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> которая настроена в схеме политики, упомянутой ранее.</span><span class="sxs-lookup"><span data-stu-id="d76d1-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="d76d1-144">Вспомогательный метод настраивается в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="d76d1-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="d76d1-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="d76d1-145">ApplicationDbContext</span></span>

<span data-ttu-id="d76d1-146">В `ApplicationDbContext` (*Data/ApplicationDbContext. CS*) то же самое <xref:Microsoft.EntityFrameworkCore.DbContext> используется в удостоверении с исключением, которое оно расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="d76d1-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="d76d1-147">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="d76d1-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="d76d1-148">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов <xref:Microsoft.EntityFrameworkCore.DbContext> удостоверений и настройте контекст для включения схемы идентификации путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` метода в методе.</span><span class="sxs-lookup"><span data-stu-id="d76d1-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="d76d1-149">оидкконфигуратионконтроллер</span><span class="sxs-lookup"><span data-stu-id="d76d1-149">OidcConfigurationController</span></span>

<span data-ttu-id="d76d1-150">В `OidcConfigurationController` (*Controllers/оидкконфигуратионконтроллер. CS*) конечная точка клиента подготавливается для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="d76d1-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="d76d1-151">Файлы параметров приложения</span><span class="sxs-lookup"><span data-stu-id="d76d1-151">App settings files</span></span>

<span data-ttu-id="d76d1-152">В файле параметров приложения (*appSettings. JSON*) в корне проекта в `IdentityServer` разделе описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="d76d1-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="d76d1-153">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="d76d1-153">In the following example, there's a single client.</span></span> <span data-ttu-id="d76d1-154">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром OAuth `ClientId` .</span><span class="sxs-lookup"><span data-stu-id="d76d1-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="d76d1-155">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="d76d1-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="d76d1-156">Профиль используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="d76d1-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="d76d1-157">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="d76d1-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="d76d1-158">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="d76d1-158">Authentication package</span></span>

<span data-ttu-id="d76d1-159">При создании приложения для использования отдельных учетных записей`Individual`пользователей приложение автоматически получает ссылку `Microsoft.AspNetCore.Components.WebAssembly.Authentication` на пакет в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="d76d1-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="d76d1-160">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="d76d1-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d76d1-161">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="d76d1-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="d76d1-162">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="d76d1-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="d76d1-163">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="d76d1-163">API authorization support</span></span>

<span data-ttu-id="d76d1-164">Поддержка проверки подлинности пользователей подключается к контейнеру службы с помощью метода расширения, предоставляемого `Microsoft.AspNetCore.Components.WebAssembly.Authentication` в пакете.</span><span class="sxs-lookup"><span data-stu-id="d76d1-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="d76d1-165">Этот метод настраивает все службы, необходимые для взаимодействия приложения с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="d76d1-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="d76d1-166">По умолчанию конфигурация приложения загружается по соглашению из `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="d76d1-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="d76d1-167">По соглашению идентификатор клиента устанавливается в имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="d76d1-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="d76d1-168">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="d76d1-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="d76d1-169">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="d76d1-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="d76d1-170">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="d76d1-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="d76d1-171">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="d76d1-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="d76d1-172">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="d76d1-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="d76d1-173">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="d76d1-173">LoginDisplay component</span></span>

<span data-ttu-id="d76d1-174">Компонент (*Shared/логиндисплай. Razor*) подготавливается к `MainLayout` просмотру в компоненте (*Shared/маинлайаут. Razor*) и управляет следующими поведениями: `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="d76d1-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="d76d1-175">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="d76d1-175">For authenticated users:</span></span>
  * <span data-ttu-id="d76d1-176">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="d76d1-176">Displays the current user name.</span></span>
  * <span data-ttu-id="d76d1-177">Содержит ссылку на страницу профиля пользователя в ASP.NET Core удостоверение.</span><span class="sxs-lookup"><span data-stu-id="d76d1-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="d76d1-178">Предлагает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="d76d1-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="d76d1-179">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="d76d1-179">For anonymous users:</span></span>
  * <span data-ttu-id="d76d1-180">Предлагает возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="d76d1-180">Offers the option to register.</span></span>
  * <span data-ttu-id="d76d1-181">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="d76d1-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="d76d1-182">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="d76d1-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="d76d1-183">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="d76d1-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="d76d1-184">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="d76d1-184">Run the app</span></span>

<span data-ttu-id="d76d1-185">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="d76d1-185">Run the app from the Server project.</span></span> <span data-ttu-id="d76d1-186">При использовании Visual Studio выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="d76d1-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d76d1-187">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d76d1-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
