---
title: Защита размещенного Blazor в ASP.NET Core приложения сборки с помощью Identity Server
author: guardrex
description: Создание нового Blazor размещенного приложения с проверкой подлинности в Visual Studio, использующей серверную часть [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/22/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: f8de07e2e21ca19b5c4e95839e7b7e621c335ad0
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110955"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="b56e7-103">Защита размещенного Blazor в ASP.NET Core приложения сборки с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="b56e7-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="b56e7-104">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b56e7-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="b56e7-105">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="b56e7-105">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="b56e7-106">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="b56e7-106">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="b56e7-107">Чтобы создать новое Blazor размещенное приложение в Visual Studio, использующее [IdentityServer](https://identityserver.io/) для проверки подлинности пользователей и вызовов API:</span><span class="sxs-lookup"><span data-stu-id="b56e7-107">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="b56e7-108">Создайте новое \*\* Blazor приложение сборки\*\* с помощью Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b56e7-108">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="b56e7-109">Для получения дополнительной информации см. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="b56e7-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="b56e7-110">В диалоговом окне **Создание Blazor нового приложения** выберите **изменить** в разделе **Проверка подлинности** .</span><span class="sxs-lookup"><span data-stu-id="b56e7-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="b56e7-111">Выберите **учетные записи отдельных пользователей** , а затем нажмите **кнопку ОК**.</span><span class="sxs-lookup"><span data-stu-id="b56e7-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="b56e7-112">Установите флажок **ASP.NET Core размещено** в разделе **Дополнительно** .</span><span class="sxs-lookup"><span data-stu-id="b56e7-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="b56e7-113">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="b56e7-113">Select the **Create** button.</span></span>

<span data-ttu-id="b56e7-114">Чтобы создать приложение в командной оболочке, выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b56e7-114">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="b56e7-115">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="b56e7-115">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b56e7-116">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="b56e7-116">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="b56e7-117">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="b56e7-117">Server app configuration</span></span>

<span data-ttu-id="b56e7-118">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="b56e7-118">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="b56e7-119">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="b56e7-119">Startup class</span></span>

<span data-ttu-id="b56e7-120">`Startup` Класс имеет следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="b56e7-120">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="b56e7-121">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b56e7-121">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="b56e7-122">Удостоверение:</span><span class="sxs-lookup"><span data-stu-id="b56e7-122">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="b56e7-123">IdentityServer с дополнительным <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> вспомогательным методом, который настраивает некоторые соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="b56e7-123">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="b56e7-124">Проверка подлинности <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> с помощью дополнительного вспомогательного метода, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="b56e7-124">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="b56e7-125">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b56e7-125">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="b56e7-126">По промежуточного слоя для проверки подлинности, которое отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="b56e7-126">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="b56e7-127">По промежуточного слоя IdentityServer, которое предоставляет конечные точки Open ID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="b56e7-127">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="b56e7-128">аддапиаусоризатион</span><span class="sxs-lookup"><span data-stu-id="b56e7-128">AddApiAuthorization</span></span>

<span data-ttu-id="b56e7-129"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Вспомогательный метод настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b56e7-129">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="b56e7-130">IdentityServer — это мощная и расширяемая платформа для обработки проблем безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="b56e7-130">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="b56e7-131">IdentityServer предоставляет ненужную сложность для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="b56e7-131">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="b56e7-132">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые мы рассмотрим хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="b56e7-132">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="b56e7-133">После изменения требований к проверке подлинности все возможности IdentityServer по-прежнему доступны для настройки проверки подлинности в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="b56e7-133">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="b56e7-134">аддидентитисервержвт</span><span class="sxs-lookup"><span data-stu-id="b56e7-134">AddIdentityServerJwt</span></span>

<span data-ttu-id="b56e7-135"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b56e7-135">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="b56e7-136">Политика настроена на разрешение идентификации для обработки всех запросов, направляемых по любому вложенному пути в пространстве `/Identity`URL-адресов удостоверений.</span><span class="sxs-lookup"><span data-stu-id="b56e7-136">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="b56e7-137"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="b56e7-137">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="b56e7-138">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="b56e7-138">Additionally, this method:</span></span>

* <span data-ttu-id="b56e7-139">Регистрирует ресурс `{APPLICATION NAME}API` API в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="b56e7-139">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="b56e7-140">Настраивает промежуточное по для токена носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="b56e7-140">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="b56e7-141">веасерфорекастконтроллер</span><span class="sxs-lookup"><span data-stu-id="b56e7-141">WeatherForecastController</span></span>

<span data-ttu-id="b56e7-142">В `WeatherForecastController` (*Controllers/веасерфорекастконтроллер. CS*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибут применяется к классу.</span><span class="sxs-lookup"><span data-stu-id="b56e7-142">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="b56e7-143">Атрибут указывает, что пользователь должен быть санкционирован на основании политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="b56e7-143">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="b56e7-144">Политика авторизации по умолчанию настроена для использования схемы проверки подлинности по умолчанию, <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> которая настроена в схеме политики, упомянутой ранее.</span><span class="sxs-lookup"><span data-stu-id="b56e7-144">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="b56e7-145">Вспомогательный метод настраивается в <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="b56e7-145">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="b56e7-146">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="b56e7-146">ApplicationDbContext</span></span>

<span data-ttu-id="b56e7-147">В `ApplicationDbContext` (*Data/ApplicationDbContext. CS*) то же самое <xref:Microsoft.EntityFrameworkCore.DbContext> используется в удостоверении с исключением, которое оно расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="b56e7-147">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="b56e7-148">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="b56e7-148"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="b56e7-149">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов <xref:Microsoft.EntityFrameworkCore.DbContext> удостоверений и настройте контекст для включения схемы идентификации путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` метода в методе.</span><span class="sxs-lookup"><span data-stu-id="b56e7-149">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="b56e7-150">оидкконфигуратионконтроллер</span><span class="sxs-lookup"><span data-stu-id="b56e7-150">OidcConfigurationController</span></span>

<span data-ttu-id="b56e7-151">В `OidcConfigurationController` (*Controllers/оидкконфигуратионконтроллер. CS*) конечная точка клиента подготавливается для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="b56e7-151">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="b56e7-152">Файлы параметров приложения</span><span class="sxs-lookup"><span data-stu-id="b56e7-152">App settings files</span></span>

<span data-ttu-id="b56e7-153">В файле параметров приложения (*appSettings. JSON*) в корне проекта в `IdentityServer` разделе описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="b56e7-153">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="b56e7-154">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="b56e7-154">In the following example, there's a single client.</span></span> <span data-ttu-id="b56e7-155">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром OAuth `ClientId` .</span><span class="sxs-lookup"><span data-stu-id="b56e7-155">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="b56e7-156">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="b56e7-156">The profile indicates the app type being configured.</span></span> <span data-ttu-id="b56e7-157">Профиль используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="b56e7-157">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="b56e7-158">В файле параметров приложения среды разработки (*appSettings. Development. JSON*) в корне проекта `IdentityServer` раздел описывает ключ, используемый для подписи маркеров.</span><span class="sxs-lookup"><span data-stu-id="b56e7-158">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="b56e7-159">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="b56e7-159">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="b56e7-160">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b56e7-160">Authentication package</span></span>

<span data-ttu-id="b56e7-161">При создании приложения для использования отдельных учетных записей`Individual`пользователей приложение автоматически получает ссылку `Microsoft.AspNetCore.Components.WebAssembly.Authentication` на пакет в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="b56e7-161">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="b56e7-162">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="b56e7-162">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b56e7-163">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="b56e7-163">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="b56e7-164">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="b56e7-164">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="b56e7-165">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="b56e7-165">API authorization support</span></span>

<span data-ttu-id="b56e7-166">Поддержка проверки подлинности пользователей подключается к контейнеру службы с помощью метода расширения, предоставляемого `Microsoft.AspNetCore.Components.WebAssembly.Authentication` в пакете.</span><span class="sxs-lookup"><span data-stu-id="b56e7-166">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="b56e7-167">Этот метод настраивает все службы, необходимые для взаимодействия приложения с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="b56e7-167">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="b56e7-168">По умолчанию конфигурация приложения загружается по соглашению из `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="b56e7-168">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="b56e7-169">По соглашению идентификатор клиента устанавливается в имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="b56e7-169">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="b56e7-170">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="b56e7-170">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="b56e7-171">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="b56e7-171">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="b56e7-172">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="b56e7-172">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="b56e7-173">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="b56e7-173">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="b56e7-174">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="b56e7-174">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="b56e7-175">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="b56e7-175">LoginDisplay component</span></span>

<span data-ttu-id="b56e7-176">Компонент (*Shared/логиндисплай. Razor*) подготавливается к `MainLayout` просмотру в компоненте (*Shared/маинлайаут. Razor*) и управляет следующими поведениями: `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="b56e7-176">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="b56e7-177">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="b56e7-177">For authenticated users:</span></span>
  * <span data-ttu-id="b56e7-178">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="b56e7-178">Displays the current user name.</span></span>
  * <span data-ttu-id="b56e7-179">Содержит ссылку на страницу профиля пользователя в ASP.NET Core удостоверение.</span><span class="sxs-lookup"><span data-stu-id="b56e7-179">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="b56e7-180">Предлагает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="b56e7-180">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="b56e7-181">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="b56e7-181">For anonymous users:</span></span>
  * <span data-ttu-id="b56e7-182">Предлагает возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="b56e7-182">Offers the option to register.</span></span>
  * <span data-ttu-id="b56e7-183">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="b56e7-183">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="b56e7-184">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b56e7-184">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="b56e7-185">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="b56e7-185">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="b56e7-186">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="b56e7-186">Run the app</span></span>

<span data-ttu-id="b56e7-187">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="b56e7-187">Run the app from the Server project.</span></span> <span data-ttu-id="b56e7-188">При использовании Visual Studio выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="b56e7-188">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b56e7-189">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b56e7-189">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
