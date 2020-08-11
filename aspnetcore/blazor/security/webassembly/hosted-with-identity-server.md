---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью IdentityServer
author: guardrex
description: Создание нового размещенного решения Blazor с поддержкой аутентификации в Visual Studio с использованием [IdentityServer](https://identityserver.io/) в качестве серверной части
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 87424f413ab21ae51fc1b1b2033069f5a41da566
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818889"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="04227-103">Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Identity Server</span><span class="sxs-lookup"><span data-stu-id="04227-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="04227-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="04227-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="04227-105">В этой статье объясняется, как создать новое размещенное решение Blazor, которое использует [IdentityServer](https://identityserver.io/) для аутентификации пользователей и вызовов API.</span><span class="sxs-lookup"><span data-stu-id="04227-105">This article explains how to create a new hosted Blazor solution that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="04227-106">Чтобы настроить изолированное или размещенное приложение Blazor WebAssembly для использования существующего внешнего экземпляра Identity Server, следуйте рекомендациям в статье <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="04227-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="04227-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="04227-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="04227-108">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="04227-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="04227-109">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="04227-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="04227-110">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04227-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="04227-111">Установите флажок **С размещением в ASP.NET Core** в разделе **Дополнительно**.</span><span class="sxs-lookup"><span data-stu-id="04227-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="04227-112">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="04227-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="04227-113">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке, укажите механизм аутентификации `Individual` с параметром `-au|--auth` для хранения пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="04227-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="04227-114">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="04227-114">Placeholder</span></span>  | <span data-ttu-id="04227-115">Пример</span><span class="sxs-lookup"><span data-stu-id="04227-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="04227-116">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="04227-117">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="04227-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="04227-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="04227-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="04227-119">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="04227-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="04227-120">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="04227-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="04227-121">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04227-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="04227-122">Установите флажок **С размещением в ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="04227-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="04227-123">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="04227-123">Server app configuration</span></span>

<span data-ttu-id="04227-124">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="04227-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="04227-125">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="04227-125">Startup class</span></span>

<span data-ttu-id="04227-126">В классе `Startup` представлены следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="04227-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="04227-127">В `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="04227-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="04227-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="04227-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="04227-129">IdentityServer с дополнительным вспомогательным методом <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>, который устанавливает соглашения ASP.NET Core по умолчанию поверх IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="04227-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="04227-130">Аутентификация с помощью дополнительного вспомогательного метода <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="04227-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="04227-131">В `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="04227-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="04227-132">ПО промежуточного слоя IdentityServer предоставляет конечные точки OpenID Connect (OIDC):</span><span class="sxs-lookup"><span data-stu-id="04227-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="04227-133">ПО промежуточного слоя для проверки подлинности отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="04227-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="04227-134">ПО промежуточного слоя для проверки подлинности обеспечивает возможности авторизации:</span><span class="sxs-lookup"><span data-stu-id="04227-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="04227-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="04227-135">AddApiAuthorization</span></span>

<span data-ttu-id="04227-136">Вспомогательный метод <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> настраивает [IdentityServer](https://identityserver.io/) для сценариев ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04227-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="04227-137">IdentityServer — это функциональная и расширяемая платформа для повышения уровня безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="04227-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="04227-138">IdentityServer указывает на ненужную сложность в некоторых распространенных сценариях.</span><span class="sxs-lookup"><span data-stu-id="04227-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="04227-139">Следовательно, предусмотрен набор соглашений и параметров конфигурации, которые можно рассматривать в качестве хорошей отправной точки.</span><span class="sxs-lookup"><span data-stu-id="04227-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="04227-140">Если нужно изменить требования к аутентификации, можно воспользоваться широкими возможностями IdentityServer для настройки аутентификации в соответствии с требованиями приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="04227-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="04227-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="04227-142">Вспомогательный метод <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="04227-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="04227-143">Политика настроена так, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в пространстве URL-адресов Identity `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="04227-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="04227-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="04227-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="04227-145">Кроме того, этот метод:</span><span class="sxs-lookup"><span data-stu-id="04227-145">Additionally, this method:</span></span>

* <span data-ttu-id="04227-146">Регистрирует ресурс API `{APPLICATION NAME}API` в IdentityServer с областью по умолчанию `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="04227-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="04227-147">Настраивает промежуточное ПО для маркера носителя JWT для проверки маркеров, выданных IdentityServer для приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="04227-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="04227-148">WeatherForecastController</span></span>

<span data-ttu-id="04227-149">В `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) к классу применяется атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).</span><span class="sxs-lookup"><span data-stu-id="04227-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="04227-150">Атрибут указывает, что пользователь должен быть авторизован на основе политики по умолчанию, чтобы получить доступ к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="04227-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="04227-151">Политика авторизации по умолчанию настроена на использование схемы проверки подлинности по умолчанию, которая настраивается <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span><span class="sxs-lookup"><span data-stu-id="04227-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="04227-152">Вспомогательный метод настраивает <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> в качестве обработчика по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="04227-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="04227-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="04227-153">ApplicationDbContext</span></span>

<span data-ttu-id="04227-154">В `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) <xref:Microsoft.EntityFrameworkCore.DbContext> расширяет <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> для включения схемы для IdentityServer.</span><span class="sxs-lookup"><span data-stu-id="04227-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="04227-155">Класс <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> является производным от <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="04227-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="04227-156">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных классов Identity <xref:Microsoft.EntityFrameworkCore.DbContext> и настройте контекст для включения схемы Identity путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` в методе <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="04227-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="04227-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="04227-157">OidcConfigurationController</span></span>

<span data-ttu-id="04227-158">В `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) выполняется подготовка конечной точки клиента для обслуживания параметров OIDC.</span><span class="sxs-lookup"><span data-stu-id="04227-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="04227-159">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="04227-159">App settings</span></span>

<span data-ttu-id="04227-160">В файле параметров приложения (`appsettings.json`) в корневом каталоге проекта в разделе `IdentityServer` описывается список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="04227-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="04227-161">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="04227-161">In the following example, there's a single client.</span></span> <span data-ttu-id="04227-162">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="04227-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="04227-163">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="04227-164">Профиль используется внутри для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="04227-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="04227-165">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="04227-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="04227-166">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="04227-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="04227-167">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="04227-167">Authentication package</span></span>

<span data-ttu-id="04227-168">Когда приложение создается для использования отдельных учетных записей пользователей (`Individual`), оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="04227-169">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="04227-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="04227-170">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="04227-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="04227-171">Конфигурация `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="04227-171">`HttpClient` configuration</span></span>

<span data-ttu-id="04227-172">В `Program.Main` (`Program.cs`) именованный <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) настроен для предоставления экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при отправке запросов к API сервера:</span><span class="sxs-lookup"><span data-stu-id="04227-172">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="04227-173">Если вы хотите настроить приложение Blazor WebAssembly на использование существующего экземпляра IdentityServer, который не является частью размещенного решения Blazor, измените регистрацию базового адреса <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) на URL-адрес конечной точки авторизации API серверного приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-173">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="04227-174">Поддержка авторизации API</span><span class="sxs-lookup"><span data-stu-id="04227-174">API authorization support</span></span>

<span data-ttu-id="04227-175">Поддержка проверки подлинности пользователей подключается в контейнере службы с помощью метода расширения, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="04227-175">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="04227-176">Этот метод настраивает службы, необходимые для взаимодействия с существующей системой авторизации.</span><span class="sxs-lookup"><span data-stu-id="04227-176">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="04227-177">По умолчанию конфигурация приложения загружается по соглашению от `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="04227-177">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="04227-178">По соглашению в качестве идентификатора клиента используется имя сборки приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-178">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="04227-179">Этот URL-адрес можно изменить, чтобы он указывал на отдельную конечную точку, вызвав перегрузку с параметрами.</span><span class="sxs-lookup"><span data-stu-id="04227-179">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="04227-180">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="04227-180">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="04227-181">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="04227-181">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="04227-182">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="04227-182">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="04227-183">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="04227-183">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="04227-184">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="04227-184">LoginDisplay component</span></span>

<span data-ttu-id="04227-185">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="04227-185">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="04227-186">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="04227-186">For authenticated users:</span></span>
  * <span data-ttu-id="04227-187">Отображает имя текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="04227-187">Displays the current user name.</span></span>
  * <span data-ttu-id="04227-188">Отображает ссылку на страницу профиля пользователя в ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="04227-188">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="04227-189">Отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="04227-189">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="04227-190">Для анонимных пользователей:</span><span class="sxs-lookup"><span data-stu-id="04227-190">For anonymous users:</span></span>
  * <span data-ttu-id="04227-191">Предоставляет возможность регистрации.</span><span class="sxs-lookup"><span data-stu-id="04227-191">Offers the option to register.</span></span>
  * <span data-ttu-id="04227-192">Предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="04227-192">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="04227-193">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="04227-193">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="04227-194">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="04227-194">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="04227-195">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="04227-195">Run the app</span></span>

<span data-ttu-id="04227-196">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="04227-196">Run the app from the Server project.</span></span> <span data-ttu-id="04227-197">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="04227-197">When using Visual Studio, either:</span></span>

* <span data-ttu-id="04227-198">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="04227-198">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="04227-199">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="04227-199">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="04227-200">Утверждение имени и роли с помощью авторизации API</span><span class="sxs-lookup"><span data-stu-id="04227-200">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="04227-201">Настраиваемая фабрика пользователей</span><span class="sxs-lookup"><span data-stu-id="04227-201">Custom user factory</span></span>

<span data-ttu-id="04227-202">В клиентском приложении создайте настраиваемую фабрику пользователей.</span><span class="sxs-lookup"><span data-stu-id="04227-202">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="04227-203">Identity Server отправляет несколько ролей в виде массива JSON в одном утверждении `role`.</span><span class="sxs-lookup"><span data-stu-id="04227-203">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="04227-204">Одна роль отправляется как строковое значение в утверждении.</span><span class="sxs-lookup"><span data-stu-id="04227-204">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="04227-205">Фабрика создает отдельное утверждение `role` для каждой из ролей пользователя.</span><span class="sxs-lookup"><span data-stu-id="04227-205">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="04227-206">`CustomUserFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="04227-206">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="04227-207">В клиентском приложении зарегистрируйте фабрику в `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="04227-207">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="04227-208">В серверном приложении вызовите <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> в построителе Identity, который добавляет службы, связанные с ролями:</span><span class="sxs-lookup"><span data-stu-id="04227-208">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="04227-209">Настройка Identity Server</span><span class="sxs-lookup"><span data-stu-id="04227-209">Configure Identity Server</span></span>

<span data-ttu-id="04227-210">Воспользуйтесь **одним** из перечисленных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="04227-210">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="04227-211">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="04227-211">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="04227-212">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="04227-212">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="04227-213">Параметры авторизации API</span><span class="sxs-lookup"><span data-stu-id="04227-213">API authorization options</span></span>

<span data-ttu-id="04227-214">В серверном приложении:</span><span class="sxs-lookup"><span data-stu-id="04227-214">In the Server app:</span></span>

* <span data-ttu-id="04227-215">Настройте Identity Server для размещения утверждений `name` и `role` в маркере идентификации и маркере доступа.</span><span class="sxs-lookup"><span data-stu-id="04227-215">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="04227-216">Запретите сопоставление по умолчанию для ролей в обработчике маркера JWT.</span><span class="sxs-lookup"><span data-stu-id="04227-216">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="04227-217">Служба профилей</span><span class="sxs-lookup"><span data-stu-id="04227-217">Profile Service</span></span>

<span data-ttu-id="04227-218">В серверном приложении создайте реализацию `ProfileService`.</span><span class="sxs-lookup"><span data-stu-id="04227-218">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="04227-219">`ProfileService.cs`.</span><span class="sxs-lookup"><span data-stu-id="04227-219">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="04227-220">В приложении сервера зарегистрируйте службу профиля в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="04227-220">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="04227-221">Использование механизмов авторизации</span><span class="sxs-lookup"><span data-stu-id="04227-221">Use authorization mechanisms</span></span>

<span data-ttu-id="04227-222">На этом этапе подходы с авторизацией компонентов в клиентском приложении являются функциональными.</span><span class="sxs-lookup"><span data-stu-id="04227-222">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="04227-223">Любой из механизмов авторизации в компонентах может использовать роль для авторизации пользователя:</span><span class="sxs-lookup"><span data-stu-id="04227-223">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="04227-224">[Компонент `AuthorizeView`](xref:blazor/security/index#authorizeview-component) (например: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="04227-224">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="04227-225">[Директива атрибута `[Authorize]`](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (например: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="04227-225">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="04227-226">[Процедурная логика](xref:blazor/security/index#procedural-logic) (например: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="04227-226">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="04227-227">Поддерживается тестирование с использованием нескольких ролей:</span><span class="sxs-lookup"><span data-stu-id="04227-227">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="04227-228">Для `User.Identity.Name` в клиентском приложении указывается имя пользователя, которое обычно является адресом электронной почты для входа.</span><span class="sxs-lookup"><span data-stu-id="04227-228">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="04227-229">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="04227-229">Additional resources</span></span>

* [<span data-ttu-id="04227-230">Развертывание в Службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="04227-230">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="04227-231">Импорт сертификата из Key Vault (документация Azure)</span><span class="sxs-lookup"><span data-stu-id="04227-231">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="04227-232">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="04227-232">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
