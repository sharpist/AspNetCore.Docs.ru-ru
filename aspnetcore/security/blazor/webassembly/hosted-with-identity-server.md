---
title: Обеспечение безопасности Blazor размещенного в ASP.NET Core сборки приложения с помощью Identity сервера
author: guardrex
description: Создание нового Blazor размещенного приложения с проверкой подлинности в Visual Studio, использующей серверную часть [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ade2d88c6a2d59e169c9019e871982a74ae46b33
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452321"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="61221-103">Обеспечение безопасности Blazor размещенного в ASP.NET Core сборки приложения с помощью Identity сервера</span><span class="sxs-lookup"><span data-stu-id="61221-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="61221-104">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="61221-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="61221-105">В этой статье объясняется, как создать новое Blazor размещенное приложение, использующее [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="61221-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61221-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61221-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="61221-107">В Visual Studio сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="61221-107">In Visual Studio:</span></span>

1. <span data-ttu-id="61221-108">Создайте новое приложение \*\* Blazor сборки\*\* .</span><span class="sxs-lookup"><span data-stu-id="61221-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="61221-109">Для получения дополнительной информации см. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="61221-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="61221-110">В диалоговом окне **Создание нового Blazor приложения** выберите **изменить** в разделе **Проверка подлинности** .</span><span class="sxs-lookup"><span data-stu-id="61221-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="61221-111">Выберите **учетные записи отдельных пользователей** , а затем нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="61221-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="61221-112">Установите флажок **ASP.NET Core размещено** в разделе **Дополнительно** .</span><span class="sxs-lookup"><span data-stu-id="61221-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="61221-113">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="61221-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="61221-114">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="61221-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="61221-115">Чтобы создать приложение в командной оболочке, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="61221-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="61221-116">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="61221-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="61221-117">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="61221-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="61221-118">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="61221-118">Server app configuration</span></span>

<span data-ttu-id="61221-119">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="61221-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="61221-120">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="61221-120">Startup class</span></span>

<span data-ttu-id="61221-121">`Startup`Класс содержит следующие дополнения.</span><span class="sxs-lookup"><span data-stu-id="61221-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="61221-122">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="61221-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="61221-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="61221-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="61221-124">IdentityServer с дополнительным <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> вспомогательным методом, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="61221-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="61221-125">Проверка подлинности с помощью дополнительного <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> вспомогательного метода, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="61221-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="61221-126">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="61221-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="61221-127">По промежуточного слоя IdentityServer предоставляет конечные точки Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="61221-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="61221-128">По промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="61221-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="61221-129">По промежуточного слоя авторизации обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="61221-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="61221-130">аддапиаусоризатион</span><span class="sxs-lookup"><span data-stu-id="61221-130">AddApiAuthorization</span></span>

<span data-ttu-id="61221-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Вспомогательный метод настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="61221-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="61221-132">IdentityServer — это мощная и расширяемая платформа для обработки проблем безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="61221-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="61221-133">IdentityServer предоставляет ненужную сложность для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="61221-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="61221-134">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые мы рассмотрим хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="61221-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="61221-135">После изменения требований к проверке подлинности доступна полная мощь IdentityServer для настройки проверки подлинности в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="61221-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="61221-136">аддидентитисервержвт</span><span class="sxs-lookup"><span data-stu-id="61221-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="61221-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="61221-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="61221-138">Политика настроена на разрешение Identity обработки всех запросов, направляемых по любому вложенному пути в Identity пространстве URL-адресов `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="61221-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="61221-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="61221-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="61221-140">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="61221-140">Additionally, this method:</span></span>

* <span data-ttu-id="61221-141">Регистрирует `{APPLICATION NAME}API` ресурс API в IdentityServer с областью по умолчанию `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="61221-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="61221-142">Настраивает промежуточное по для токена носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="61221-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="61221-143">веасерфорекастконтроллер</span><span class="sxs-lookup"><span data-stu-id="61221-143">WeatherForecastController</span></span>

<span data-ttu-id="61221-144">В `WeatherForecastController` (*Controllers/веасерфорекастконтроллер. CS*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу.</span><span class="sxs-lookup"><span data-stu-id="61221-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="61221-145">Атрибут указывает, что пользователь должен быть санкционирован на основании политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="61221-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="61221-146">Политика авторизации по умолчанию настроена для использования схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="61221-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="61221-147">Вспомогательный метод настраивается в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="61221-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="61221-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="61221-148">ApplicationDbContext</span></span>

<span data-ttu-id="61221-149">В `ApplicationDbContext` (*Data/ApplicationDbContext. CS*) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяется <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="61221-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="61221-150">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="61221-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="61221-151">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных Identity <xref:Microsoft.EntityFrameworkCore.DbContext> классов и настройте контекст для включения Identity схемы путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> метода в методе.</span><span class="sxs-lookup"><span data-stu-id="61221-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="61221-152">оидкконфигуратионконтроллер</span><span class="sxs-lookup"><span data-stu-id="61221-152">OidcConfigurationController</span></span>

<span data-ttu-id="61221-153">В `OidcConfigurationController` (*Controllers/оидкконфигуратионконтроллер. CS*) конечная точка клиента подготавливается для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="61221-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="61221-154">Файлы параметров приложения</span><span class="sxs-lookup"><span data-stu-id="61221-154">App settings files</span></span>

<span data-ttu-id="61221-155">В файле параметров приложения (*appSettings. JSON*) в корне проекта в `IdentityServer` разделе описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="61221-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="61221-156">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="61221-156">In the following example, there's a single client.</span></span> <span data-ttu-id="61221-157">Имя клиента соответствует имени приложения и сопоставляется по соглашению с `ClientId` параметром OAuth.</span><span class="sxs-lookup"><span data-stu-id="61221-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="61221-158">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="61221-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="61221-159">Профиль используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="61221-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="61221-160">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="61221-160">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="61221-161">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="61221-161">Authentication package</span></span>

<span data-ttu-id="61221-162">Когда приложение создается для использования отдельных учетных записей пользователей ( `Individual` ), оно автоматически получает ссылку на пакет для пакета [Microsoft. AspNetCore. Components. веб-сборки. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="61221-162">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="61221-163">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="61221-163">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="61221-164">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="61221-164">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="61221-165">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="61221-165">API authorization support</span></span>

<span data-ttu-id="61221-166">Поддержка проверки подлинности пользователей подключается к контейнеру службы методом расширения, предоставленным в пакете [Microsoft. AspNetCore. Components. WebService. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="61221-166">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="61221-167">Этот метод настраивает службы, необходимые приложению для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="61221-167">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="61221-168">По умолчанию конфигурация приложения загружается по соглашению из `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="61221-168">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="61221-169">По соглашению идентификатор клиента устанавливается в имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="61221-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="61221-170">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="61221-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="61221-171">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="61221-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="61221-172">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="61221-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="61221-173">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="61221-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="61221-174">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="61221-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="61221-175">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="61221-175">LoginDisplay component</span></span>

<span data-ttu-id="61221-176">`LoginDisplay`Компонент (*Shared/логиндисплай. Razor*) подготавливается к просмотру в `MainLayout` компоненте (*Shared/маинлайаут. Razor*) и управляет следующими поведениями:</span><span class="sxs-lookup"><span data-stu-id="61221-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="61221-177">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="61221-177">For authenticated users:</span></span>
  * <span data-ttu-id="61221-178">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="61221-178">Displays the current user name.</span></span>
  * <span data-ttu-id="61221-179">Содержит ссылку на страницу профиля пользователя в ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="61221-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="61221-180">Предлагает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="61221-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="61221-181">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="61221-181">For anonymous users:</span></span>
  * <span data-ttu-id="61221-182">Предлагает возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="61221-182">Offers the option to register.</span></span>
  * <span data-ttu-id="61221-183">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="61221-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="61221-184">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="61221-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="61221-185">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="61221-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="61221-186">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="61221-186">Run the app</span></span>

<span data-ttu-id="61221-187">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="61221-187">Run the app from the Server project.</span></span> <span data-ttu-id="61221-188">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="61221-188">When using Visual Studio, either:</span></span>

* <span data-ttu-id="61221-189">Задайте раскрывающийся список **запускаемые проекты** на панели инструментов для *приложения API сервера* и нажмите кнопку **выполнить** .</span><span class="sxs-lookup"><span data-stu-id="61221-189">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="61221-190">Выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="61221-190">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="61221-191">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="61221-191">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="61221-192">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="61221-192">Custom user factory</span></span>

<span data-ttu-id="61221-193">В клиентском приложении создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="61221-193">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="61221-194">Сервер отправляет несколько ролей в виде массива JSON в одном `role` утверждении.</span><span class="sxs-lookup"><span data-stu-id="61221-194"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="61221-195">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="61221-195">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="61221-196">Фабрика создает отдельное `role` утверждение для каждой роли пользователя.</span><span class="sxs-lookup"><span data-stu-id="61221-196">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="61221-197">*CustomUserFactory.CS*:</span><span class="sxs-lookup"><span data-stu-id="61221-197">*CustomUserFactory.cs*:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType);

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="61221-198">В клиентском приложении Зарегистрируйте фабрику в `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="61221-198">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="61221-199">В серверном приложении вызовите метод <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в Identity построителе, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="61221-199">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="61221-200">Настройка Identity сервера</span><span class="sxs-lookup"><span data-stu-id="61221-200">Configure Identity Server</span></span>

<span data-ttu-id="61221-201">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="61221-201">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="61221-202">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="61221-202">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="61221-203">Служба профиля</span><span class="sxs-lookup"><span data-stu-id="61221-203">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="61221-204">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="61221-204">API authorization options</span></span>

<span data-ttu-id="61221-205">В серверном приложении:</span><span class="sxs-lookup"><span data-stu-id="61221-205">In the Server app:</span></span>

* <span data-ttu-id="61221-206">Настройте Identity сервер, чтобы разместить `name` и `role` утверждения в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="61221-206">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="61221-207">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="61221-207">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="61221-208">Служба профиля</span><span class="sxs-lookup"><span data-stu-id="61221-208">Profile Service</span></span>

<span data-ttu-id="61221-209">В серверном приложении создайте `ProfileService` реализацию.</span><span class="sxs-lookup"><span data-stu-id="61221-209">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="61221-210">*ProfileService.CS*:</span><span class="sxs-lookup"><span data-stu-id="61221-210">*ProfileService.cs*:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="61221-211">В серверном приложении Зарегистрируйте службу профиля в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="61221-211">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="61221-212">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="61221-212">Use authorization mechanisms</span></span>

<span data-ttu-id="61221-213">В этом месте клиентское приложение работает с подходом к авторизации компонентов.</span><span class="sxs-lookup"><span data-stu-id="61221-213">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="61221-214">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="61221-214">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="61221-215">[Компонент аусоризевиев](xref:security/blazor/index#authorizeview-component) (пример: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="61221-215">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="61221-216">[ `[Authorize]` директива Attribute](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (пример: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="61221-216">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="61221-217">[Процедурная логика](xref:security/blazor/index#procedural-logic) (пример: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="61221-217">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="61221-218">Поддерживаются несколько тестов ролей:</span><span class="sxs-lookup"><span data-stu-id="61221-218">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="61221-219">`User.Identity.Name`заполняется в клиентском приложении именем пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="61221-219">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="61221-220">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="61221-220">Additional resources</span></span>

* [<span data-ttu-id="61221-221">Развертывание в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="61221-221">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="61221-222">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="61221-222">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="61221-223">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="61221-223">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
