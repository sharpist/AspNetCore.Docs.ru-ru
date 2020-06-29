---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server
author: guardrex
description: Создание нового размещенного приложения Blazor с проверкой подлинности в Visual Studio с использованием [IdentityServer](https://identityserver.io/) в качестве серверной части
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 8251658a2bb99ec17424ec26b8a44171082fab05
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243486"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="3942b-103">Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="3942b-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="3942b-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="3942b-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3942b-105">В этой статье объясняется, как создать новое размещенное приложение Blazor, которое использует [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="3942b-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3942b-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3942b-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3942b-107">В Visual Studio сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="3942b-107">In Visual Studio:</span></span>

1. <span data-ttu-id="3942b-108">Создайте новое приложение **Blazor WebAssembly**.</span><span class="sxs-lookup"><span data-stu-id="3942b-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="3942b-109">Для получения дополнительной информации см. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="3942b-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="3942b-110">В диалоговом окне **Создание нового приложения Blazor** в разделе **Проверка подлинности** выберите **Изменить**.</span><span class="sxs-lookup"><span data-stu-id="3942b-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="3942b-111">Выберите **Учетные записи отдельных пользователей** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="3942b-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="3942b-112">В разделе **Дополнительно** установите флажок **Размещенный ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="3942b-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="3942b-113">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="3942b-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3942b-114">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="3942b-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3942b-115">Чтобы создать приложение, в командной оболочке выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="3942b-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="3942b-116">Чтобы указать расположение выходных данных для создания папки проекта, если она не существует, включите в команду параметр выходных данных с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="3942b-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="3942b-117">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="3942b-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="3942b-118">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="3942b-118">Server app configuration</span></span>

<span data-ttu-id="3942b-119">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="3942b-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="3942b-120">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="3942b-120">Startup class</span></span>

<span data-ttu-id="3942b-121">В классе `Startup` представлены следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="3942b-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="3942b-122">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3942b-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="3942b-123">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="3942b-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="3942b-124">IdentityServer с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="3942b-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="3942b-125">Проверка подлинности с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="3942b-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="3942b-126">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="3942b-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="3942b-127">ПО промежуточного слоя IdentityServer предоставляет конечные точки Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="3942b-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="3942b-128">ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="3942b-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="3942b-129">ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="3942b-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="3942b-130">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="3942b-130">AddApiAuthorization</span></span>

<span data-ttu-id="3942b-131">Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3942b-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="3942b-132">IdentityServer — это функциональная и расширяемая платформа для устранения проблем с безопасностью приложений.</span><span class="sxs-lookup"><span data-stu-id="3942b-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="3942b-133">IdentityServer усложняет некоторые наиболее распространенные сценарии.</span><span class="sxs-lookup"><span data-stu-id="3942b-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="3942b-134">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки.</span><span class="sxs-lookup"><span data-stu-id="3942b-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="3942b-135">Если требуется изменить требования к проверке подлинности, можно воспользоваться широкими возможностями IdentityServer для настройки проверки подлинности в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="3942b-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="3942b-136">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="3942b-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="3942b-137">Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3942b-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="3942b-138">Политика настроена так, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов Identity `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="3942b-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="3942b-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="3942b-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="3942b-140">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="3942b-140">Additionally, this method:</span></span>

* <span data-ttu-id="3942b-141">Регистрирует ресурс API `{APPLICATION NAME}API` в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="3942b-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="3942b-142">Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="3942b-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="3942b-143">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="3942b-143">WeatherForecastController</span></span>

<span data-ttu-id="3942b-144">В `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) к классу применяется атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="3942b-144">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="3942b-145">Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="3942b-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="3942b-146">Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span><span class="sxs-lookup"><span data-stu-id="3942b-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="3942b-147">Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="3942b-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="3942b-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="3942b-148">ApplicationDbContext</span></span>

<span data-ttu-id="3942b-149">В `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="3942b-149">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="3942b-150">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="3942b-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="3942b-151">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов Identity <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы Identity путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="3942b-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="3942b-152">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="3942b-152">OidcConfigurationController</span></span>

<span data-ttu-id="3942b-153">В `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) выполняется подготовка конечной точки клиента для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="3942b-153">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="3942b-154">Файл параметров приложения</span><span class="sxs-lookup"><span data-stu-id="3942b-154">App settings files</span></span>

<span data-ttu-id="3942b-155">В файле параметров приложения (`appsettings.json`) в корневом каталоге проекта в разделе `IdentityServer` описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="3942b-155">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="3942b-156">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="3942b-156">In the following example, there's a single client.</span></span> <span data-ttu-id="3942b-157">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="3942b-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="3942b-158">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="3942b-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="3942b-159">Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="3942b-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="3942b-160">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="3942b-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="3942b-161">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="3942b-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="3942b-162">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="3942b-162">Authentication package</span></span>

<span data-ttu-id="3942b-163">Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="3942b-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="3942b-164">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="3942b-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="3942b-165">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="3942b-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="3942b-166">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="3942b-166">API authorization support</span></span>

<span data-ttu-id="3942b-167">Поддержка проверки подлинности пользователей подключается в контейнере службы с помощью метода расширения, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="3942b-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="3942b-168">Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="3942b-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="3942b-169">По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="3942b-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="3942b-170">По соглашению в качестве идентификатора клиента используется имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="3942b-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="3942b-171">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="3942b-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="3942b-172">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="3942b-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="3942b-173">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="3942b-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="3942b-174">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="3942b-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="3942b-175">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="3942b-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="3942b-176">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="3942b-176">LoginDisplay component</span></span>

<span data-ttu-id="3942b-177">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="3942b-177">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="3942b-178">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="3942b-178">For authenticated users:</span></span>
  * <span data-ttu-id="3942b-179">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="3942b-179">Displays the current user name.</span></span>
  * <span data-ttu-id="3942b-180">Отображает ссылку на страницу профиля пользователя в ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="3942b-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="3942b-181">Отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="3942b-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="3942b-182">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="3942b-182">For anonymous users:</span></span>
  * <span data-ttu-id="3942b-183">Предоставляет возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="3942b-183">Offers the option to register.</span></span>
  * <span data-ttu-id="3942b-184">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="3942b-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="3942b-185">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="3942b-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="3942b-186">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="3942b-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="3942b-187">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="3942b-187">Run the app</span></span>

<span data-ttu-id="3942b-188">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="3942b-188">Run the app from the Server project.</span></span> <span data-ttu-id="3942b-189">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="3942b-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="3942b-190">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="3942b-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="3942b-191">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="3942b-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="3942b-192">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="3942b-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="3942b-193">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="3942b-193">Custom user factory</span></span>

<span data-ttu-id="3942b-194">В клиентском приложении создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="3942b-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="3942b-195"> Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`.</span><span class="sxs-lookup"><span data-stu-id="3942b-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="3942b-196">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="3942b-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="3942b-197">Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="3942b-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="3942b-198">`CustomUserFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="3942b-198">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="3942b-199">В клиентском приложении зарегистрируйте фабрику в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3942b-199">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="3942b-200">В серверном приложении вызовите <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в построителе Identity, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="3942b-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="3942b-201">Настройка Identity Server</span><span class="sxs-lookup"><span data-stu-id="3942b-201">Configure Identity Server</span></span>

<span data-ttu-id="3942b-202">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="3942b-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="3942b-203">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="3942b-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="3942b-204">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="3942b-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="3942b-205">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="3942b-205">API authorization options</span></span>

<span data-ttu-id="3942b-206">В серверном приложении:</span><span class="sxs-lookup"><span data-stu-id="3942b-206">In the Server app:</span></span>

* <span data-ttu-id="3942b-207">Настройте Identity Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="3942b-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="3942b-208">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="3942b-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="3942b-209">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="3942b-209">Profile Service</span></span>

<span data-ttu-id="3942b-210">В серверном приложении создайте реализацию `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="3942b-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="3942b-211">`ProfileService.cs`.</span><span class="sxs-lookup"><span data-stu-id="3942b-211">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="3942b-212">В приложении сервера зарегистрируйте службу профиля в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3942b-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="3942b-213">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="3942b-213">Use authorization mechanisms</span></span>

<span data-ttu-id="3942b-214">На этом этапе подходы с авторизацией компонентов в клиентском приложении являются функциональными.</span><span class="sxs-lookup"><span data-stu-id="3942b-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="3942b-215">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="3942b-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="3942b-216">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="3942b-216">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="3942b-217">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="3942b-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="3942b-218">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="3942b-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="3942b-219">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="3942b-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="3942b-220">Для `User.Identity.Name` в клиентском приложении указывается имя пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="3942b-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="3942b-221">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3942b-221">Additional resources</span></span>

* [<span data-ttu-id="3942b-222">Развертывание в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="3942b-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="3942b-223">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="3942b-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="3942b-224">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="3942b-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
