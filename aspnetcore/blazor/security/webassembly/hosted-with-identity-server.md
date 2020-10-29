---
title: 'Защита размещенного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью :::no-loc(Identity):::Server'
author: guardrex
description: 'Узнайте, как защитить размещенное приложение ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью :::no-loc(Identity)::: Server.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: a6fd005e19f532089ac1a1914756fb03eabb24c4
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690475"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="295ea-103">Защита размещенного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью :::no-loc(Identity)::: Server</span><span class="sxs-lookup"><span data-stu-id="295ea-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="295ea-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="295ea-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="295ea-105">В этой статье объясняется, как создать новое [размещенное приложение :::no-loc(Blazor WebAssembly):::](xref:blazor/hosting-models#blazor-webassembly), которое использует [:::no-loc(Identity):::Server](https://identityserver.io/) для аутентификации пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="295ea-105">This article explains how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [:::no-loc(Identity):::Server](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="295ea-106">Чтобы настроить изолированное или размещенное приложение :::no-loc(Blazor WebAssembly)::: для использования существующего внешнего экземпляра :::no-loc(Identity)::: Server, следуйте рекомендациям в статье <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="295ea-106">To configure a standalone or hosted :::no-loc(Blazor WebAssembly)::: app to use an existing, external :::no-loc(Identity)::: Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="295ea-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="295ea-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="295ea-108">Чтобы создать новый проект :::no-loc(Blazor WebAssembly)::: с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="295ea-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="295ea-109">Выбрав шаблон **Приложение :::no-loc(Blazor WebAssembly):::** в диалоговом окне **Создание веб-приложения ASP.NET Core** , щелкните **Изменить** в разделе **Проверка подлинности** .</span><span class="sxs-lookup"><span data-stu-id="295ea-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

1. <span data-ttu-id="295ea-110">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении** , чтобы хранить пользователей в приложении с помощью системы [:::no-loc(Identity):::](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="295ea-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="295ea-111">Установите флажок **С размещением в ASP.NET Core** в разделе **Дополнительно** .</span><span class="sxs-lookup"><span data-stu-id="295ea-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="295ea-112">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="295ea-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="295ea-113">Чтобы создать новый проект :::no-loc(Blazor WebAssembly)::: с механизмом аутентификации в пустой папке, укажите механизм аутентификации `Individual` с параметром `-au|--auth` для хранения пользователей в приложении с помощью системы [:::no-loc(Identity):::](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="295ea-113">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="295ea-114">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="295ea-114">Placeholder</span></span>  | <span data-ttu-id="295ea-115">Пример</span><span class="sxs-lookup"><span data-stu-id="295ea-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="295ea-116">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="295ea-117">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="295ea-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="295ea-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="295ea-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="295ea-119">Чтобы создать новый проект :::no-loc(Blazor WebAssembly)::: с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="295ea-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="295ea-120">На шаге **Настройка нового приложения :::no-loc(Blazor WebAssembly):::** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности** .</span><span class="sxs-lookup"><span data-stu-id="295ea-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="295ea-121">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [:::no-loc(Identity):::](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="295ea-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="295ea-122">Установите флажок **С размещением в ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="295ea-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="295ea-123">Конфигурация приложения *`Server`*</span><span class="sxs-lookup"><span data-stu-id="295ea-123">*`Server`* app configuration</span></span>

<span data-ttu-id="295ea-124">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="295ea-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="295ea-125">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="295ea-125">Startup class</span></span>

<span data-ttu-id="295ea-126">В классе `Startup` представлены следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="295ea-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="295ea-127">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="295ea-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="295ea-128">:::no-loc(ASP.NET Core Identity)::::</span><span class="sxs-lookup"><span data-stu-id="295ea-128">:::no-loc(ASP.NET Core Identity)::::</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="295ea-129">:::no-loc(Identity):::Server с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="295ea-129">:::no-loc(Identity):::Server with an additional <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="295ea-130">Аутентификация с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="295ea-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="295ea-131">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="295ea-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="295ea-132">ПО промежуточного слоя :::no-loc(Identity):::Server предоставляет конечные точки OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="295ea-132">The :::no-loc(Identity):::Server middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

  * <span data-ttu-id="295ea-133">ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="295ea-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="295ea-134">ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="295ea-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="295ea-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="295ea-135">AddApiAuthorization</span></span>

<span data-ttu-id="295ea-136">Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [:::no-loc(Identity):::Server](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="295ea-136">The <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [:::no-loc(Identity):::Server](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="295ea-137">:::no-loc(Identity):::Server — это функциональная и расширяемая платформа для повышения уровня безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="295ea-137">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="295ea-138">:::no-loc(Identity):::Server указывает на ненужную сложность в некоторых распространенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="295ea-138">:::no-loc(Identity):::Server exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="295ea-139">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки.</span><span class="sxs-lookup"><span data-stu-id="295ea-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="295ea-140">Если нужно изменить требования к аутентификации, можно воспользоваться широкими возможностями :::no-loc(Identity):::Server для настройки аутентификации в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-140">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="295ea-141">Add:::no-loc(Identity):::ServerJwt</span><span class="sxs-lookup"><span data-stu-id="295ea-141">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="295ea-142">Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="295ea-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="295ea-143">Политика настроена так, чтобы разрешить :::no-loc(Identity)::: обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов :::no-loc(Identity)::: `/:::no-loc(Identity):::`.</span><span class="sxs-lookup"><span data-stu-id="295ea-143">The policy is configured to allow :::no-loc(Identity)::: to handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space `/:::no-loc(Identity):::`.</span></span> <span data-ttu-id="295ea-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="295ea-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="295ea-145">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="295ea-145">Additionally, this method:</span></span>

* <span data-ttu-id="295ea-146">Регистрирует ресурс API `{APPLICATION NAME}API` в :::no-loc(Identity):::Server с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="295ea-146">Registers an `{APPLICATION NAME}API` API resource with :::no-loc(Identity):::Server with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="295ea-147">Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных :::no-loc(Identity):::Server для приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-147">Configures the JWT Bearer Token Middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="295ea-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="295ea-148">WeatherForecastController</span></span>

<span data-ttu-id="295ea-149">В `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) к классу применяется атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="295ea-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="295ea-150">Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="295ea-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="295ea-151">Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>.</span><span class="sxs-lookup"><span data-stu-id="295ea-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>.</span></span> <span data-ttu-id="295ea-152">Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="295ea-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="295ea-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="295ea-153">ApplicationDbContext</span></span>

<span data-ttu-id="295ea-154">В `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> для включения схемы для :::no-loc(Identity):::Server.</span><span class="sxs-lookup"><span data-stu-id="295ea-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> to include the schema for :::no-loc(Identity):::Server.</span></span> <span data-ttu-id="295ea-155">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span><span class="sxs-lookup"><span data-stu-id="295ea-155"><xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore.:::no-loc(Identity):::DbContext>.</span></span>

<span data-ttu-id="295ea-156">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы :::no-loc(Identity)::: путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="295ea-156">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="295ea-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="295ea-157">OidcConfigurationController</span></span>

<span data-ttu-id="295ea-158">В `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) выполняется подготовка конечной точки клиента для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="295ea-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="295ea-159">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="295ea-159">App settings</span></span>

<span data-ttu-id="295ea-160">В файле параметров приложения (`appsettings.json`) в корневом каталоге проекта в разделе `:::no-loc(Identity):::Server` описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="295ea-160">In the app settings file (`appsettings.json`) at the project root, the `:::no-loc(Identity):::Server` section describes the list of configured clients.</span></span> <span data-ttu-id="295ea-161">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="295ea-161">In the following example, there's a single client.</span></span> <span data-ttu-id="295ea-162">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="295ea-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="295ea-163">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="295ea-164">Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="295ea-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

<span data-ttu-id="295ea-165">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `:::no-loc(Blazor):::Sample.Client`).</span><span class="sxs-lookup"><span data-stu-id="295ea-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="295ea-166">Конфигурация приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="295ea-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="295ea-167">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="295ea-167">Authentication package</span></span>

<span data-ttu-id="295ea-168">Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="295ea-169">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="295ea-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="295ea-170">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="295ea-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="295ea-171">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="295ea-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="295ea-172">Конфигурация `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="295ea-172">`HttpClient` configuration</span></span>

<span data-ttu-id="295ea-173">В `Program.Main` (`Program.cs`) именованный <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) настроен для предоставления экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при отправке запросов к API сервера:</span><span class="sxs-lookup"><span data-stu-id="295ea-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="295ea-174">Если вы хотите настроить приложение :::no-loc(Blazor WebAssembly)::: на использование существующего экземпляра :::no-loc(Identity):::Server, который не является частью размещенного решения :::no-loc(Blazor):::, измените регистрацию базового адреса <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) на URL-адрес конечной точки авторизации API серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-174">If you're configuring a :::no-loc(Blazor WebAssembly)::: app to use an existing :::no-loc(Identity)::: Server instance that isn't part of a hosted :::no-loc(Blazor)::: solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="295ea-175">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="295ea-175">API authorization support</span></span>

<span data-ttu-id="295ea-176">Поддержка проверки подлинности пользователей подключается в контейнере службы с помощью метода расширения, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="295ea-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="295ea-177">Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="295ea-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="295ea-178">По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="295ea-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="295ea-179">По соглашению в качестве идентификатора клиента используется имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="295ea-180">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="295ea-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="295ea-181">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="295ea-181">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="295ea-182">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="295ea-182">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="295ea-183">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="295ea-183">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="295ea-184">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="295ea-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="295ea-185">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="295ea-185">LoginDisplay component</span></span>

<span data-ttu-id="295ea-186">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="295ea-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="295ea-187">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="295ea-187">For authenticated users:</span></span>
  * <span data-ttu-id="295ea-188">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="295ea-188">Displays the current user name.</span></span>
  * <span data-ttu-id="295ea-189">Отображает ссылку на страницу профиля пользователя в :::no-loc(ASP.NET Core Identity):::.</span><span class="sxs-lookup"><span data-stu-id="295ea-189">Offers a link to the user profile page in :::no-loc(ASP.NET Core Identity):::.</span></span>
  * <span data-ttu-id="295ea-190">Отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="295ea-191">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="295ea-191">For anonymous users:</span></span>
  * <span data-ttu-id="295ea-192">Предоставляет возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="295ea-192">Offers the option to register.</span></span>
  * <span data-ttu-id="295ea-193">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="295ea-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.:::no-loc(Identity):::.Name!</a>
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

### <a name="authentication-component"></a><span data-ttu-id="295ea-194">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="295ea-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="295ea-195">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="295ea-195">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="295ea-196">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="295ea-196">Run the app</span></span>

<span data-ttu-id="295ea-197">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="295ea-197">Run the app from the Server project.</span></span> <span data-ttu-id="295ea-198">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="295ea-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="295ea-199">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить** .</span><span class="sxs-lookup"><span data-stu-id="295ea-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="295ea-200">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="295ea-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="295ea-201">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="295ea-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="295ea-202">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="295ea-202">Custom user factory</span></span>

<span data-ttu-id="295ea-203">В приложении *`Client`* создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="295ea-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="295ea-204">:::no-loc(Identity)::: Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`.</span><span class="sxs-lookup"><span data-stu-id="295ea-204">:::no-loc(Identity)::: Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="295ea-205">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="295ea-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="295ea-206">Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="295ea-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="295ea-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="295ea-207">`CustomUserFactory.cs`:</span></span>

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

        if (user.:::no-loc(Identity):::.IsAuthenticated)
        {
            var identity = (Claims:::no-loc(Identity):::)user.:::no-loc(Identity):::;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

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

<span data-ttu-id="295ea-208">В приложении *`Client`* зарегистрируйте фабрику в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="295ea-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="295ea-209">В приложении *`Server`* вызовите <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> в построителе :::no-loc(Identity):::, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="295ea-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Builder.AddRoles*> on the :::no-loc(Identity)::: builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::;

...

services.AddDefault:::no-loc(Identity):::<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<:::no-loc(Identity):::Role>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="295ea-210">Настройка :::no-loc(Identity)::: Server</span><span class="sxs-lookup"><span data-stu-id="295ea-210">Configure :::no-loc(Identity)::: Server</span></span>

<span data-ttu-id="295ea-211">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="295ea-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="295ea-212">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="295ea-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="295ea-213">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="295ea-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="295ea-214">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="295ea-214">API authorization options</span></span>

<span data-ttu-id="295ea-215">В приложении *`Server`* :</span><span class="sxs-lookup"><span data-stu-id="295ea-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="295ea-216">Настройте :::no-loc(Identity)::: Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="295ea-216">Configure :::no-loc(Identity)::: Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="295ea-217">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="295ea-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.:::no-loc(Identity):::Model.Tokens.Jwt;
using System.Linq;

...

services.Add:::no-loc(Identity):::Server()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.:::no-loc(Identity):::Resources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="295ea-218">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="295ea-218">Profile Service</span></span>

<span data-ttu-id="295ea-219">В приложении *`Server`* создайте реализацию `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="295ea-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="295ea-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="295ea-220">`ProfileService.cs`:</span></span>

```csharp
using :::no-loc(Identity):::Model;
using :::no-loc(Identity):::Server4.Models;
using :::no-loc(Identity):::Server4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="295ea-221">В приложении *`Server`* зарегистрируйте службу профиля в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="295ea-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using :::no-loc(Identity):::Server4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="295ea-222">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="295ea-222">Use authorization mechanisms</span></span>

<span data-ttu-id="295ea-223">На этом этапе подходы с авторизацией компонентов в приложении *`Client`* являются функциональными.</span><span class="sxs-lookup"><span data-stu-id="295ea-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="295ea-224">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="295ea-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="295ea-225">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="295ea-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="295ea-226">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="295ea-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="295ea-227">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="295ea-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="295ea-228">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="295ea-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="295ea-229">Для `User.:::no-loc(Identity):::.Name` в приложении *`Client`* указывается имя пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="295ea-229">`User.:::no-loc(Identity):::.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="295ea-230">Размещение в Службе приложений Azure с использованием личного домена</span><span class="sxs-lookup"><span data-stu-id="295ea-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="295ea-231">Ниже объясняется, как развернуть размещенное приложение :::no-loc(Blazor WebAssembly)::: с :::no-loc(Identity)::: Server в [Службе приложений Azure](https://azure.microsoft.com/services/app-service/) с использованием личного домена.</span><span class="sxs-lookup"><span data-stu-id="295ea-231">The following guidance explains how to deploy a hosted :::no-loc(Blazor WebAssembly)::: app with :::no-loc(Identity)::: Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="295ea-232">В этом сценарии размещения **не** используйте один и тот же сертификат для [ключа подписывания токена :::no-loc(Identity)::: Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) и защищенного обмена данными по протоколу HTTPS между сайтом и браузерами.</span><span class="sxs-lookup"><span data-stu-id="295ea-232">For this hosting scenario, do **not** use the same certificate for [:::no-loc(Identity)::: Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="295ea-233">Использование разных сертификатов в этих целях рекомендуется из соображений безопасности, так как позволяет изолировать закрытые ключи разного назначения.</span><span class="sxs-lookup"><span data-stu-id="295ea-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="295ea-234">Управление TLS-сертификатами для обмена данными с браузерами производится независимо и не влияет на подписывание маркеров :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="295ea-234">TLS certificates for communication with browsers is managed independently without affecting :::no-loc(Identity)::: Server's token signing.</span></span>
* <span data-ttu-id="295ea-235">Когда [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) предоставляет сертификат приложению Службы приложений для привязки к личному домену, :::no-loc(Identity)::: Server не может получить тот же сертификат из Azure Key Vault для подписывания токена.</span><span class="sxs-lookup"><span data-stu-id="295ea-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, :::no-loc(Identity)::: Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="295ea-236">Хотя :::no-loc(Identity)::: Server можно настроить так, чтобы он использовал тот же TLS-сертификат по физическому пути, размещать сертификаты безопасности в системе управления версиями **не рекомендуется, в большинстве случаев этого следует избегать** .</span><span class="sxs-lookup"><span data-stu-id="295ea-236">Although configuring :::no-loc(Identity)::: Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios** .</span></span>

<span data-ttu-id="295ea-237">Далее в Azure Key Vault создается самозаверяющий сертификат исключительно для подписывания токена :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="295ea-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="295ea-238">Конфигурация :::no-loc(Identity)::: Server использует сертификат из хранилища ключей посредством хранилища сертификатов `My` > `CurrentUser` приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-238">The :::no-loc(Identity)::: Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="295ea-239">Другие сертификаты, используемые для трафика HTTPS личных доменов, создаются и настраиваются отдельно от сертификата подписывания :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="295ea-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the :::no-loc(Identity)::: Server signing certificate.</span></span>

<span data-ttu-id="295ea-240">Чтобы настроить приложение, Службу приложений Azure и Azure Key Vault для размещения в личном домене с HTTPS, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="295ea-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="295ea-241">Создайте [план Службы приложений](/azure/app-service/overview-hosting-plans) уровня `Basic B1` или выше.</span><span class="sxs-lookup"><span data-stu-id="295ea-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="295ea-242">Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.</span><span class="sxs-lookup"><span data-stu-id="295ea-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="295ea-243">Создайте PFX-сертификат для безопасного обмена данными между сайтом и браузером (по протоколу HTTPS) с полным доменным именем (FQDN) сайта, которое контролирует организация (например, `www.contoso.com`), в качестве общего имени.</span><span class="sxs-lookup"><span data-stu-id="295ea-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="295ea-244">Создайте сертификат с указанными ниже параметрами.</span><span class="sxs-lookup"><span data-stu-id="295ea-244">Create the certificate with:</span></span>
   * <span data-ttu-id="295ea-245">Ключ использует:</span><span class="sxs-lookup"><span data-stu-id="295ea-245">Key uses</span></span>
     * <span data-ttu-id="295ea-246">проверку цифровой подписи (`digitalSignature`);</span><span class="sxs-lookup"><span data-stu-id="295ea-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="295ea-247">шифрование ключей (`keyEncipherment`).</span><span class="sxs-lookup"><span data-stu-id="295ea-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="295ea-248">Расширенное использование ключа:</span><span class="sxs-lookup"><span data-stu-id="295ea-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="295ea-249">проверка подлинности клиента (1.3.6.1.5.5.7.3.2);</span><span class="sxs-lookup"><span data-stu-id="295ea-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="295ea-250">проверка подлинности сервера (1.3.6.1.5.5.7.3.1).</span><span class="sxs-lookup"><span data-stu-id="295ea-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="295ea-251">Чтобы создать сертификат, используйте один из следующих подходов или любое другое подходящее средство или веб-службу.</span><span class="sxs-lookup"><span data-stu-id="295ea-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="295ea-252">Хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="295ea-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="295ea-253">MakeCert в Windows</span><span class="sxs-lookup"><span data-stu-id="295ea-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="295ea-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="295ea-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="295ea-255">Запишите пароль, который будет использоваться позже для импорта сертификата в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="295ea-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="295ea-256">Дополнительные сведения о сертификатах Azure Key Vault см. в статье [Azure Key Vault. Сертификаты](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="295ea-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="295ea-257">Создайте хранилище Azure Key Vault или используйте уже имеющееся в подписке Azure.</span><span class="sxs-lookup"><span data-stu-id="295ea-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="295ea-258">В области **Сертификаты** хранилища ключей импортируйте сертификат сайта PFX.</span><span class="sxs-lookup"><span data-stu-id="295ea-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="295ea-259">Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="295ea-260">В Azure Key Vault создайте самозаверяющий сертификат для подписывания токена :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="295ea-260">In Azure Key Vault, generate a new self-signed certificate for :::no-loc(Identity)::: Server token signing.</span></span> <span data-ttu-id="295ea-261">Укажите **имя сертификата** и **субъект** .</span><span class="sxs-lookup"><span data-stu-id="295ea-261">Give the certificate a **Certificate Name** and **Subject** .</span></span> <span data-ttu-id="295ea-262">**Субъект** указывается в формате `CN={COMMON NAME}`, где заполнитель `{COMMON NAME}` — это общее имя сертификата.</span><span class="sxs-lookup"><span data-stu-id="295ea-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="295ea-263">Общим именем может быть любая буквенно-цифровая строка.</span><span class="sxs-lookup"><span data-stu-id="295ea-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="295ea-264">Например, `CN=:::no-loc(Identity):::ServerSigning` — допустимый **субъект** сертификата.</span><span class="sxs-lookup"><span data-stu-id="295ea-264">For example, `CN=:::no-loc(Identity):::ServerSigning` is a valid certificate **Subject** .</span></span> <span data-ttu-id="295ea-265">В разделе **Расширенная настройка политик** оставьте значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="295ea-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="295ea-266">Запишите отпечаток сертификата, который будет использоваться позже в конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="295ea-267">Перейдите к Службе приложений Azure на портале Azure и создайте Службу приложений со следующей конфигурацией:</span><span class="sxs-lookup"><span data-stu-id="295ea-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="295ea-268">**Опубликовать**  — `Code`;</span><span class="sxs-lookup"><span data-stu-id="295ea-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="295ea-269">**Стек времени выполнения**  — среда выполнения приложения;</span><span class="sxs-lookup"><span data-stu-id="295ea-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="295ea-270">**Номер SKU и размер**  — уровень Службы приложений не ниже `Basic B1`.</span><span class="sxs-lookup"><span data-stu-id="295ea-270">For **Sku and size** , confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="295ea-271">Для использования личных доменов Службе приложений требуется уровень служб `Basic B1` или более высокий.</span><span class="sxs-lookup"><span data-stu-id="295ea-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="295ea-272">После того как в Azure будет создана Служба приложений, откройте **конфигурацию приложения** и добавьте новый параметр, указав записанные ранее отпечатки сертификатов.</span><span class="sxs-lookup"><span data-stu-id="295ea-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="295ea-273">Ключ параметра — `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="295ea-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="295ea-274">В значении параметра разделяйте отпечатки запятыми, как показано в следующем примере.</span><span class="sxs-lookup"><span data-stu-id="295ea-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="295ea-275">Ключ: `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="295ea-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="295ea-276">Значение: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="295ea-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="295ea-277">На портале Azure параметры приложения сохраняются в два шага: сохраните параметр `WEBSITE_LOAD_CERTIFICATES`, а затем в верхней части колонки нажмите кнопку **Сохранить** .</span><span class="sxs-lookup"><span data-stu-id="295ea-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="295ea-278">Выберите **параметры TLS/SSL** приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-278">Select the app's **TLS/SSL settings** .</span></span> <span data-ttu-id="295ea-279">Выберите **Сертификаты закрытых ключей (PFX)** .</span><span class="sxs-lookup"><span data-stu-id="295ea-279">Select **Private Key Certificates (.pfx)** .</span></span> <span data-ttu-id="295ea-280">Дважды выполните процесс **Импорт сертификата Key Vault** , чтобы импортировать и сертификат сайта для обмена данными по протоколу HTTPS, и самозаверяющий сертификат сайта для подписывания токена :::no-loc(Identity)::: Server.</span><span class="sxs-lookup"><span data-stu-id="295ea-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed :::no-loc(Identity)::: Server token signing certificate.</span></span>
1. <span data-ttu-id="295ea-281">Перейдите к колонке **Личные домены** .</span><span class="sxs-lookup"><span data-stu-id="295ea-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="295ea-282">На веб-сайте регистратора своего домена используйте **IP-адрес** и **идентификатор проверки личного домена** для настройки домена.</span><span class="sxs-lookup"><span data-stu-id="295ea-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="295ea-283">Типичная конфигурация домена включает в себя следующие параметры:</span><span class="sxs-lookup"><span data-stu-id="295ea-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="295ea-284">**запись A** с **узлом** `@` и значением IP-адреса с портала Azure;</span><span class="sxs-lookup"><span data-stu-id="295ea-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="295ea-285">**запись TXT** с **узлом** `asuid` и значением идентификатора проверки, созданным Azure и предоставленным на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="295ea-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="295ea-286">Надлежащим образом сохраните изменения на веб-сайте регистратора домена.</span><span class="sxs-lookup"><span data-stu-id="295ea-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="295ea-287">На веб-сайтах некоторых регистраторов записи домена сохраняются в два шага: сначала нужно по отдельности сохранить одну запись или несколько, а затем обновить регистрацию домена с помощью специальной кнопки.</span><span class="sxs-lookup"><span data-stu-id="295ea-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="295ea-288">Вернитесь к колонке **Личные домены** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="295ea-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="295ea-289">Нажмите кнопку **Добавить личный домен** .</span><span class="sxs-lookup"><span data-stu-id="295ea-289">Select **Add custom domain** .</span></span> <span data-ttu-id="295ea-290">Выберите параметр **Запись A** .</span><span class="sxs-lookup"><span data-stu-id="295ea-290">Select the **A Record** option.</span></span> <span data-ttu-id="295ea-291">Укажите домен и выберите команду **Проверить** .</span><span class="sxs-lookup"><span data-stu-id="295ea-291">Provide the domain and select **Validate** .</span></span> <span data-ttu-id="295ea-292">Если записи домена верны и распространены в Интернете, на портале можно будет нажать кнопку **Добавить личный домен** .</span><span class="sxs-lookup"><span data-stu-id="295ea-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="295ea-293">На распространение изменений регистрации домена на серверах доменных имен (DNS) в Интернете после их обработки регистратором домена может потребоваться несколько дней.</span><span class="sxs-lookup"><span data-stu-id="295ea-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="295ea-294">Если записи домена не были обновлены в течение трех рабочих дней, убедитесь в том, что они были правильно заданы на сайте регистратора, и обратитесь в его службу поддержки клиентов.</span><span class="sxs-lookup"><span data-stu-id="295ea-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="295ea-295">В колонке **Личные домены** **СОСТОЯНИЕ SSL** домена должно иметь значение `Not Secure`.</span><span class="sxs-lookup"><span data-stu-id="295ea-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="295ea-296">Щелкните ссылку **Добавить привязку** .</span><span class="sxs-lookup"><span data-stu-id="295ea-296">Select the **Add binding** link.</span></span> <span data-ttu-id="295ea-297">Выберите HTTPS-сертификат сайта из хранилища ключей для привязки личного домена.</span><span class="sxs-lookup"><span data-stu-id="295ea-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="295ea-298">В Visual Studio откройте файл параметров приложения для *серверного* проекта (`appsettings.json` или `appsettings.Production.json`).</span><span class="sxs-lookup"><span data-stu-id="295ea-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="295ea-299">В конфигурации :::no-loc(Identity)::: Server добавьте приведенный ниже раздел `Key`.</span><span class="sxs-lookup"><span data-stu-id="295ea-299">In the :::no-loc(Identity)::: Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="295ea-300">Укажите **субъект** самозаверяющего сертификата для ключа `Name`.</span><span class="sxs-lookup"><span data-stu-id="295ea-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="295ea-301">В следующем примере в хранилище ключей сертификату присвоено общее имя `:::no-loc(Identity):::ServerSigning`, поэтому **субъект**  — `CN=:::no-loc(Identity):::ServerSigning`.</span><span class="sxs-lookup"><span data-stu-id="295ea-301">In the following example, the certificate's common name assigned in the key vault is `:::no-loc(Identity):::ServerSigning`, which yields a **Subject** of `CN=:::no-loc(Identity):::ServerSigning`:</span></span>

   ```json
   ":::no-loc(Identity):::Server": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=:::no-loc(Identity):::ServerSigning"
     }
   },
   ```

1. <span data-ttu-id="295ea-302">В Visual Studio создайте [профиль публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) Службы приложений Azure для *серверного* проекта.</span><span class="sxs-lookup"><span data-stu-id="295ea-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="295ea-303">В строке меню выберите **Сборка** > **Опубликовать** > **Создать** > **Azure** > **Служба приложений Azure** (Windows или Linux).</span><span class="sxs-lookup"><span data-stu-id="295ea-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="295ea-304">Когда Visual Studio подключится к подписке Azure, вы сможете настроить **представление** ресурсов Azure по **типу ресурса** .</span><span class="sxs-lookup"><span data-stu-id="295ea-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type** .</span></span> <span data-ttu-id="295ea-305">В списке **Веб-приложение** найдите Службу приложений для приложения и выберите ее.</span><span class="sxs-lookup"><span data-stu-id="295ea-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="295ea-306">Нажмите кнопку **Готово** .</span><span class="sxs-lookup"><span data-stu-id="295ea-306">Select **Finish** .</span></span>
1. <span data-ttu-id="295ea-307">Когда в Visual Studio снова отобразится окно **Публикация** , зависимости хранилища ключей и службы базы данных SQL Server будут обнаружены автоматически.</span><span class="sxs-lookup"><span data-stu-id="295ea-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="295ea-308">Для службы хранилища ключей не требуется изменять настройки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="295ea-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="295ea-309">В целях тестирования с приложением можно развернуть локальную базу данных [SQLite](https://www.sqlite.org/index.html), которая настраивается по умолчанию на основе шаблона :::no-loc(Blazor):::. Дополнительной настройки она не требует.</span><span class="sxs-lookup"><span data-stu-id="295ea-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the :::no-loc(Blazor)::: template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="295ea-310">Настройка другой базы данных для :::no-loc(Identity)::: Server в рабочей среде выходит за рамки этой статьи.</span><span class="sxs-lookup"><span data-stu-id="295ea-310">Configuring a different database for :::no-loc(Identity)::: Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="295ea-311">Дополнительные сведения см. в ресурсах, посвященных базам данных, в следующих наборах документации.</span><span class="sxs-lookup"><span data-stu-id="295ea-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="295ea-312">Служба приложений</span><span class="sxs-lookup"><span data-stu-id="295ea-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="295ea-313">Сервер :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="295ea-313">:::no-loc(Identity)::: Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="295ea-314">Щелкните ссылку **Изменить** под именем профиля развертывания в верхней части окна.</span><span class="sxs-lookup"><span data-stu-id="295ea-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="295ea-315">Измените URL-адрес назначения на URL-адрес личного домена сайта (например, `https://www.contoso.com`).</span><span class="sxs-lookup"><span data-stu-id="295ea-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="295ea-316">Сохраните параметры.</span><span class="sxs-lookup"><span data-stu-id="295ea-316">Save the settings.</span></span>
1. <span data-ttu-id="295ea-317">Опубликуйте приложение.</span><span class="sxs-lookup"><span data-stu-id="295ea-317">Publish the app.</span></span> <span data-ttu-id="295ea-318">В Visual Studio откроется окно браузера, и будет выполнен запрос к сайту в личном домене.</span><span class="sxs-lookup"><span data-stu-id="295ea-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="295ea-319">В документации по Azure приводятся дополнительные сведения об использовании служб Azure и личных доменов с привязкой TLS в Службе приложений, включая сведения об использовании записей CNAME вместо записей A.</span><span class="sxs-lookup"><span data-stu-id="295ea-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="295ea-320">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="295ea-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="295ea-321">Документация по Службе приложений</span><span class="sxs-lookup"><span data-stu-id="295ea-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="295ea-322">Руководство по Сопоставление существующего настраиваемого DNS-имени со Службой приложений Azure</span><span class="sxs-lookup"><span data-stu-id="295ea-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="295ea-323">Защита пользовательского доменного имени с помощью привязки TLS/SSL в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="295ea-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="295ea-324">Хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="295ea-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="295ea-325">Мы рекомендуем использовать новое окно браузера в режиме InPrivate или инкогнито для каждого тестового запуска после изменения приложения, его конфигурации или служб Azure на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="295ea-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="295ea-326">Оставшиеся после предыдущего тестового запуска файлы :::no-loc(cookie)::: могут привести к ошибкам проверки подлинности или авторизации при тестировании сайта, даже если его конфигурация верна.</span><span class="sxs-lookup"><span data-stu-id="295ea-326">Lingering :::no-loc(cookie):::s from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="295ea-327">Дополнительные сведения о том, как настроить Visual Studio для открытия нового окна браузера в режиме InPrivate или инкогнито для каждого тестового запуска, см. в разделе [Файлы :::no-loc(Cookie)::: и данные сайта](#:::no-loc(cookie):::s-and-site-data).</span><span class="sxs-lookup"><span data-stu-id="295ea-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [:::no-loc(Cookie):::s and site data](#:::no-loc(cookie):::s-and-site-data) section.</span></span>

<span data-ttu-id="295ea-328">При изменении конфигурации Службы приложений на портале Azure обновления обычно вступают в силу быстро, но не мгновенно.</span><span class="sxs-lookup"><span data-stu-id="295ea-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="295ea-329">Иногда, чтобы изменение конфигурации вступило в силу, необходимо немного подождать, пока Служба приложений перезапустится.</span><span class="sxs-lookup"><span data-stu-id="295ea-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="295ea-330">При устранении проблемы с загрузкой сертификата выполните приведенную ниже команду в командной оболочке PowerShell [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="295ea-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="295ea-331">Она выводит список сертификатов, к которым приложение может получить доступ в хранилище сертификатов `My` > `CurrentUser`.</span><span class="sxs-lookup"><span data-stu-id="295ea-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="295ea-332">Выходные данные включают в себя субъекты и отпечатки сертификатов, которые могут быть полезны при отладке приложения.</span><span class="sxs-lookup"><span data-stu-id="295ea-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="295ea-333">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="295ea-333">Additional resources</span></span>

* [<span data-ttu-id="295ea-334">Развертывание в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="295ea-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="295ea-335">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="295ea-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="295ea-336">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="295ea-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
