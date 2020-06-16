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
ms.openlocfilehash: c85843c04688beefe7ea87d9e8b281d14ab85bc5
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776518"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="05a91-103">Обеспечение безопасности Blazor размещенного в ASP.NET Core сборки приложения с помощью Identity сервера</span><span class="sxs-lookup"><span data-stu-id="05a91-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="05a91-104">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="05a91-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="05a91-105">В этой статье объясняется, как создать новое Blazor размещенное приложение, использующее [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="05a91-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="05a91-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05a91-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05a91-107">В Visual Studio сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="05a91-107">In Visual Studio:</span></span>

1. <span data-ttu-id="05a91-108">Создайте новое приложение \*\* Blazor сборки\*\* .</span><span class="sxs-lookup"><span data-stu-id="05a91-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="05a91-109">Для получения дополнительной информации см. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="05a91-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="05a91-110">В диалоговом окне **Создание нового Blazor приложения** выберите **изменить** в разделе **Проверка подлинности** .</span><span class="sxs-lookup"><span data-stu-id="05a91-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="05a91-111">Выберите **учетные записи отдельных пользователей** , а затем нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="05a91-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="05a91-112">Установите флажок **ASP.NET Core размещено** в разделе **Дополнительно** .</span><span class="sxs-lookup"><span data-stu-id="05a91-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="05a91-113">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="05a91-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="05a91-114">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="05a91-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="05a91-115">Чтобы создать приложение в командной оболочке, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="05a91-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="05a91-116">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="05a91-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="05a91-117">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="05a91-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="05a91-118">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="05a91-118">Server app configuration</span></span>

<span data-ttu-id="05a91-119">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="05a91-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="05a91-120">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="05a91-120">Startup class</span></span>

<span data-ttu-id="05a91-121">`Startup`Класс содержит следующие дополнения.</span><span class="sxs-lookup"><span data-stu-id="05a91-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="05a91-122">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="05a91-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="05a91-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="05a91-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="05a91-124">IdentityServer с дополнительным <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> вспомогательным методом, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="05a91-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="05a91-125">Проверка подлинности с помощью дополнительного <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> вспомогательного метода, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="05a91-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="05a91-126">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="05a91-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="05a91-127">По промежуточного слоя IdentityServer предоставляет конечные точки Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="05a91-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="05a91-128">По промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="05a91-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="05a91-129">По промежуточного слоя авторизации обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="05a91-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="05a91-130">аддапиаусоризатион</span><span class="sxs-lookup"><span data-stu-id="05a91-130">AddApiAuthorization</span></span>

<span data-ttu-id="05a91-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Вспомогательный метод настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="05a91-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="05a91-132">IdentityServer — это мощная и расширяемая платформа для обработки проблем безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="05a91-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="05a91-133">IdentityServer предоставляет ненужную сложность для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="05a91-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="05a91-134">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые мы рассмотрим хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="05a91-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="05a91-135">После изменения требований к проверке подлинности доступна полная мощь IdentityServer для настройки проверки подлинности в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="05a91-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="05a91-136">аддидентитисервержвт</span><span class="sxs-lookup"><span data-stu-id="05a91-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="05a91-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="05a91-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="05a91-138">Политика настроена на разрешение Identity обработки всех запросов, направляемых по любому вложенному пути в Identity пространстве URL-адресов `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="05a91-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="05a91-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="05a91-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="05a91-140">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="05a91-140">Additionally, this method:</span></span>

* <span data-ttu-id="05a91-141">Регистрирует `{APPLICATION NAME}API` ресурс API в IdentityServer с областью по умолчанию `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="05a91-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="05a91-142">Настраивает промежуточное по для токена носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="05a91-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="05a91-143">веасерфорекастконтроллер</span><span class="sxs-lookup"><span data-stu-id="05a91-143">WeatherForecastController</span></span>

<span data-ttu-id="05a91-144">В `WeatherForecastController` (*Controllers/веасерфорекастконтроллер. CS*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу.</span><span class="sxs-lookup"><span data-stu-id="05a91-144">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="05a91-145">Атрибут указывает, что пользователь должен быть санкционирован на основании политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="05a91-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="05a91-146">Политика авторизации по умолчанию настроена для использования схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="05a91-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="05a91-147">Вспомогательный метод настраивается в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="05a91-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="05a91-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="05a91-148">ApplicationDbContext</span></span>

<span data-ttu-id="05a91-149">В `ApplicationDbContext` (*Data/ApplicationDbContext. CS*) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяется <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="05a91-149">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="05a91-150">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="05a91-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="05a91-151">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных Identity <xref:Microsoft.EntityFrameworkCore.DbContext> классов и настройте контекст для включения Identity схемы путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> метода в методе.</span><span class="sxs-lookup"><span data-stu-id="05a91-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="05a91-152">оидкконфигуратионконтроллер</span><span class="sxs-lookup"><span data-stu-id="05a91-152">OidcConfigurationController</span></span>

<span data-ttu-id="05a91-153">В `OidcConfigurationController` (*Controllers/оидкконфигуратионконтроллер. CS*) конечная точка клиента подготавливается для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="05a91-153">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="05a91-154">Файлы параметров приложения</span><span class="sxs-lookup"><span data-stu-id="05a91-154">App settings files</span></span>

<span data-ttu-id="05a91-155">В файле параметров приложения (*appsettings.json*) в корневом каталоге проекта в `IdentityServer` разделе описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="05a91-155">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="05a91-156">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="05a91-156">In the following example, there's a single client.</span></span> <span data-ttu-id="05a91-157">Имя клиента соответствует имени приложения и сопоставляется по соглашению с `ClientId` параметром OAuth.</span><span class="sxs-lookup"><span data-stu-id="05a91-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="05a91-158">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="05a91-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="05a91-159">Профиль используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="05a91-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="05a91-160">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="05a91-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="05a91-161">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="05a91-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="05a91-162">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="05a91-162">Authentication package</span></span>

<span data-ttu-id="05a91-163">Когда приложение создается для использования отдельных учетных записей пользователей ( `Individual` ), оно автоматически получает ссылку на пакет для пакета [Microsoft. AspNetCore. Components. веб-сборки. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="05a91-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="05a91-164">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="05a91-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="05a91-165">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="05a91-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="05a91-166">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="05a91-166">API authorization support</span></span>

<span data-ttu-id="05a91-167">Поддержка проверки подлинности пользователей подключается к контейнеру службы методом расширения, предоставленным в пакете [Microsoft. AspNetCore. Components. WebService. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="05a91-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="05a91-168">Этот метод настраивает службы, необходимые приложению для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="05a91-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="05a91-169">По умолчанию конфигурация приложения загружается по соглашению из `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="05a91-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="05a91-170">По соглашению идентификатор клиента устанавливается в имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="05a91-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="05a91-171">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="05a91-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="05a91-172">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="05a91-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="05a91-173">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="05a91-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="05a91-174">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="05a91-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="05a91-175">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="05a91-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="05a91-176">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="05a91-176">LoginDisplay component</span></span>

<span data-ttu-id="05a91-177">`LoginDisplay`Компонент (*Shared/логиндисплай. Razor*) подготавливается к просмотру в `MainLayout` компоненте (*Shared/маинлайаут. Razor*) и управляет следующими поведениями:</span><span class="sxs-lookup"><span data-stu-id="05a91-177">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="05a91-178">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="05a91-178">For authenticated users:</span></span>
  * <span data-ttu-id="05a91-179">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="05a91-179">Displays the current user name.</span></span>
  * <span data-ttu-id="05a91-180">Содержит ссылку на страницу профиля пользователя в ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="05a91-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="05a91-181">Предлагает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="05a91-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="05a91-182">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="05a91-182">For anonymous users:</span></span>
  * <span data-ttu-id="05a91-183">Предлагает возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="05a91-183">Offers the option to register.</span></span>
  * <span data-ttu-id="05a91-184">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="05a91-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="05a91-185">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="05a91-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="05a91-186">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="05a91-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="05a91-187">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="05a91-187">Run the app</span></span>

<span data-ttu-id="05a91-188">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="05a91-188">Run the app from the Server project.</span></span> <span data-ttu-id="05a91-189">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="05a91-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="05a91-190">Задайте раскрывающийся список **запускаемые проекты** на панели инструментов для *приложения API сервера* и нажмите кнопку **выполнить** .</span><span class="sxs-lookup"><span data-stu-id="05a91-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="05a91-191">Выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="05a91-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="05a91-192">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="05a91-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="05a91-193">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="05a91-193">Custom user factory</span></span>

<span data-ttu-id="05a91-194">В клиентском приложении создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="05a91-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="05a91-195">Сервер отправляет несколько ролей в виде массива JSON в одном `role` утверждении.</span><span class="sxs-lookup"><span data-stu-id="05a91-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="05a91-196">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="05a91-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="05a91-197">Фабрика создает отдельное `role` утверждение для каждой роли пользователя.</span><span class="sxs-lookup"><span data-stu-id="05a91-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="05a91-198">*CustomUserFactory.CS*:</span><span class="sxs-lookup"><span data-stu-id="05a91-198">*CustomUserFactory.cs*:</span></span>

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

<span data-ttu-id="05a91-199">В клиентском приложении Зарегистрируйте фабрику в `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="05a91-199">In the Client app, register the factory in `Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="05a91-200">В серверном приложении вызовите метод <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в Identity построителе, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="05a91-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="05a91-201">Настройка Identity сервера</span><span class="sxs-lookup"><span data-stu-id="05a91-201">Configure Identity Server</span></span>

<span data-ttu-id="05a91-202">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="05a91-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="05a91-203">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="05a91-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="05a91-204">Служба профиля</span><span class="sxs-lookup"><span data-stu-id="05a91-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="05a91-205">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="05a91-205">API authorization options</span></span>

<span data-ttu-id="05a91-206">В серверном приложении:</span><span class="sxs-lookup"><span data-stu-id="05a91-206">In the Server app:</span></span>

* <span data-ttu-id="05a91-207">Настройте Identity сервер, чтобы разместить `name` и `role` утверждения в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="05a91-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="05a91-208">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="05a91-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="05a91-209">Служба профиля</span><span class="sxs-lookup"><span data-stu-id="05a91-209">Profile Service</span></span>

<span data-ttu-id="05a91-210">В серверном приложении создайте `ProfileService` реализацию.</span><span class="sxs-lookup"><span data-stu-id="05a91-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="05a91-211">*ProfileService.CS*:</span><span class="sxs-lookup"><span data-stu-id="05a91-211">*ProfileService.cs*:</span></span>

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

<span data-ttu-id="05a91-212">В серверном приложении Зарегистрируйте службу профиля в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="05a91-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="05a91-213">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="05a91-213">Use authorization mechanisms</span></span>

<span data-ttu-id="05a91-214">В этом месте клиентское приложение работает с подходом к авторизации компонентов.</span><span class="sxs-lookup"><span data-stu-id="05a91-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="05a91-215">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="05a91-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="05a91-216">[Компонент аусоризевиев](xref:security/blazor/index#authorizeview-component) (пример: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="05a91-216">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="05a91-217">[ `[Authorize]` директива Attribute](xref:security/blazor/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (пример: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="05a91-217">[`[Authorize]` attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="05a91-218">[Процедурная логика](xref:security/blazor/index#procedural-logic) (пример: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="05a91-218">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="05a91-219">Поддерживаются несколько тестов ролей:</span><span class="sxs-lookup"><span data-stu-id="05a91-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="05a91-220">`User.Identity.Name`заполняется в клиентском приложении именем пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="05a91-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="05a91-221">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="05a91-221">Additional resources</span></span>

* [<span data-ttu-id="05a91-222">Развертывание в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="05a91-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="05a91-223">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="05a91-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="05a91-224">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="05a91-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
