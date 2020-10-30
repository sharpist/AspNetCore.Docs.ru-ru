---
title: Общие сведения о проверке подлинности для одностраничных приложений на ASP.NET Core
author: javiercn
description: 'Используйте :::no-loc(Identity)::: приложение с одним страничным приложением, размещенным в ASP.NET Core приложении.'
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051984"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="6d144-103">Проверка подлинности и авторизация для одностраничные приложения</span><span class="sxs-lookup"><span data-stu-id="6d144-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="6d144-104">Шаблоны ASP.NET Core 3,1 и более поздних версий обеспечивают проверку подлинности в одностраничных приложениях (одностраничные приложения) с помощью поддержки авторизации API.</span><span class="sxs-lookup"><span data-stu-id="6d144-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="6d144-105">:::no-loc(ASP.NET Core Identity):::для проверки подлинности и сохранения пользователей в сочетании с [ :::no-loc(Identity)::: сервером](https://identityserver.io/) для реализации OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="6d144-105">:::no-loc(ASP.NET Core Identity)::: for authenticating and storing users is combined with [:::no-loc(Identity):::Server](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="6d144-106">В шаблоны проектов " **угловой** " и " **отреагировать** " был добавлен параметр проверки подлинности, аналогичный параметру проверки подлинности в шаблонах проектов **веб-приложения (модель-представление-контроллер)** (MVC) и **веб-приложение** ( :::no-loc(Razor)::: страницы).</span><span class="sxs-lookup"><span data-stu-id="6d144-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (:::no-loc(Razor)::: Pages) project templates.</span></span> <span data-ttu-id="6d144-107">Допустимые значения параметра: **None** и **индивидуальных** .</span><span class="sxs-lookup"><span data-stu-id="6d144-107">The allowed parameter values are **None** and **Individual** .</span></span> <span data-ttu-id="6d144-108">Шаблон проекта **React.js и Redux** в настоящее время не поддерживает параметр проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="6d144-109">Создание приложения с поддержкой авторизации API</span><span class="sxs-lookup"><span data-stu-id="6d144-109">Create an app with API authorization support</span></span>

<span data-ttu-id="6d144-110">Проверку подлинности и авторизацию пользователя можно использовать как в радиальном, так и на одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="6d144-111">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="6d144-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="6d144-112">**Угловой** :</span><span class="sxs-lookup"><span data-stu-id="6d144-112">**Angular** :</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="6d144-113">**Реагировать** :</span><span class="sxs-lookup"><span data-stu-id="6d144-113">**React** :</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="6d144-114">Предыдущая команда создает приложение ASP.NET Core с каталогом *ClientApp* , СОДЕРЖАЩИМ значение SPA.</span><span class="sxs-lookup"><span data-stu-id="6d144-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="6d144-115">Общее описание компонентов ASP.NET Core приложения</span><span class="sxs-lookup"><span data-stu-id="6d144-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="6d144-116">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="6d144-117">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="6d144-117">Startup class</span></span>

<span data-ttu-id="6d144-118">В следующих примерах кода используются [Microsoft. AspNetCore. апиаусоризатион. :::no-loc(Identity)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server) Пакет NuGet сервера.</span><span class="sxs-lookup"><span data-stu-id="6d144-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server) NuGet package.</span></span> <span data-ttu-id="6d144-119">В примерах настраивается проверка подлинности и авторизация API с помощью <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiResourceCollection.Add:::no-loc(Identity):::ServerJwt%2A> методов расширения и.</span><span class="sxs-lookup"><span data-stu-id="6d144-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.:::no-loc(Identity):::Server.ApiResourceCollection.Add:::no-loc(Identity):::ServerJwt%2A> extension methods.</span></span> <span data-ttu-id="6d144-120">Проекты, использующие шаблоны проектов "реагирующий" или "угловой SPA" с проверкой подлинности, включают ссылку на этот пакет.</span><span class="sxs-lookup"><span data-stu-id="6d144-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="6d144-121">`Startup`Класс имеет следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="6d144-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="6d144-122">Внутри `Startup.ConfigureServices` метода:</span><span class="sxs-lookup"><span data-stu-id="6d144-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="6d144-123">:::no-loc(Identity)::: с помощью пользовательского интерфейса по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="6d144-123">:::no-loc(Identity)::: with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefault:::no-loc(Identity):::<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="6d144-124">:::no-loc(Identity):::Сервер с дополнительным `AddApiAuthorization` вспомогательным методом, который устанавливает некоторые соглашения ASP.NET Core по умолчанию поверх :::no-loc(Identity)::: сервера:</span><span class="sxs-lookup"><span data-stu-id="6d144-124">:::no-loc(Identity):::Server with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.Add:::no-loc(Identity):::Server()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="6d144-125">Аутентификация с помощью дополнительного вспомогательного метода `Add:::no-loc(Identity):::ServerJwt`, который настраивает приложение для проверки маркеров JWT, созданных :::no-loc(Identity):::Server:</span><span class="sxs-lookup"><span data-stu-id="6d144-125">Authentication with an additional `Add:::no-loc(Identity):::ServerJwt` helper method that configures the app to validate JWT tokens produced by :::no-loc(Identity):::Server:</span></span>

    ```csharp
    services.AddAuthentication()
        .Add:::no-loc(Identity):::ServerJwt();
    ```

* <span data-ttu-id="6d144-126">Внутри `Startup.Configure` метода:</span><span class="sxs-lookup"><span data-stu-id="6d144-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="6d144-127">По промежуточного слоя для проверки подлинности, которое отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="6d144-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="6d144-128">По :::no-loc(Identity)::: промежуточного слоя сервера, предоставляющего конечные точки подключения OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="6d144-128">The :::no-loc(Identity):::Server middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.Use:::no-loc(Identity):::Server();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="6d144-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="6d144-129">AddApiAuthorization</span></span>

<span data-ttu-id="6d144-130">Этот вспомогательный метод настраивает :::no-loc(Identity)::: сервер для использования нашей поддерживаемой конфигурации.</span><span class="sxs-lookup"><span data-stu-id="6d144-130">This helper method configures :::no-loc(Identity):::Server to use our supported configuration.</span></span> <span data-ttu-id="6d144-131">:::no-loc(Identity):::Server — это функциональная и расширяемая платформа для повышения уровня безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="6d144-131">:::no-loc(Identity):::Server is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="6d144-132">В то же время это делает ненужную сложность для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="6d144-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="6d144-133">Следовательно, для вас предоставляется набор соглашений и параметров конфигурации, которые считаются хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="6d144-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="6d144-134">После изменения требований к проверке подлинности все возможности :::no-loc(Identity)::: сервера по-прежнему доступны для настройки проверки подлинности в соответствии с вашими потребностями.</span><span class="sxs-lookup"><span data-stu-id="6d144-134">Once your authentication needs change, the full power of :::no-loc(Identity):::Server is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="6d144-135">Add:::no-loc(Identity):::ServerJwt</span><span class="sxs-lookup"><span data-stu-id="6d144-135">Add:::no-loc(Identity):::ServerJwt</span></span>

<span data-ttu-id="6d144-136">Этот вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="6d144-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="6d144-137">Политика настроена таким путем, чтобы разрешить :::no-loc(Identity)::: обработку всех запросов, направляемых по любому вложенному пути в :::no-loc(Identity)::: пространстве URL-адресов "/ :::no-loc(Identity)::: ".</span><span class="sxs-lookup"><span data-stu-id="6d144-137">The policy is configured to let :::no-loc(Identity)::: handle all requests routed to any subpath in the :::no-loc(Identity)::: URL space "/:::no-loc(Identity):::".</span></span> <span data-ttu-id="6d144-138">`JwtBearerHandler` обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="6d144-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="6d144-139">Кроме того, этот метод регистрирует `<<ApplicationName>>API` ресурс API с :::no-loc(Identity)::: сервером с областью по умолчанию `<<ApplicationName>>API` и настраивает по промежуточного слоя маркера носителя JWT для проверки маркеров, выданных :::no-loc(Identity)::: сервером для приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with :::no-loc(Identity):::Server with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by :::no-loc(Identity):::Server for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="6d144-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="6d144-140">WeatherForecastController</span></span>

<span data-ttu-id="6d144-141">В файле *контроллерс\веасерфорекастконтроллер.КС* Обратите внимание на `[Authorize]` атрибут, примененный к классу, который указывает, что пользователь должен быть полномочным, исходя из политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="6d144-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="6d144-142">Политика авторизации по умолчанию должна быть настроена для использования схемы проверки подлинности по умолчанию, которая настраивается в описанной `Add:::no-loc(Identity):::ServerJwt` выше схеме политики, что делает этот `JwtBearerHandler` вспомогательный метод обработчиком по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="6d144-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `Add:::no-loc(Identity):::ServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="6d144-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="6d144-143">ApplicationDbContext</span></span>

<span data-ttu-id="6d144-144">В файле *дата\аппликатиондбконтекст.КС* Обратите внимание на то же, что `DbContext` используется в :::no-loc(Identity)::: с исключением, которое оно расширяет `ApiAuthorizationDbContext` (более производным классом от `:::no-loc(Identity):::DbContext` ), чтобы включить схему для :::no-loc(Identity)::: сервера.</span><span class="sxs-lookup"><span data-stu-id="6d144-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in :::no-loc(Identity)::: with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `:::no-loc(Identity):::DbContext`) to include the schema for :::no-loc(Identity):::Server.</span></span>

<span data-ttu-id="6d144-145">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных :::no-loc(Identity)::: `DbContext` классов и настройте контекст для включения :::no-loc(Identity)::: схемы путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` метода в методе.</span><span class="sxs-lookup"><span data-stu-id="6d144-145">To gain full control of the database schema, inherit from one of the available :::no-loc(Identity)::: `DbContext` classes and configure the context to include the :::no-loc(Identity)::: schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="6d144-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="6d144-146">OidcConfigurationController</span></span>

<span data-ttu-id="6d144-147">В файле *контроллерс\оидкконфигуратионконтроллер.КС* Обратите внимание на конечную точку, подготовленную для обслуживания параметров OIDC, которые необходимо использовать клиенту.</span><span class="sxs-lookup"><span data-stu-id="6d144-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### :::no-loc(appsettings.json):::

<span data-ttu-id="6d144-148">В *:::no-loc(appsettings.json):::* файле корневого каталога проекта имеется новый `:::no-loc(Identity):::Server` раздел, описывающий список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="6d144-148">In the *:::no-loc(appsettings.json):::* file of the project root, there's a new `:::no-loc(Identity):::Server` section that describes the list of configured clients.</span></span> <span data-ttu-id="6d144-149">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="6d144-149">In the following example, there's a single client.</span></span> <span data-ttu-id="6d144-150">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="6d144-150">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="6d144-151">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-151">The profile indicates the app type being configured.</span></span> <span data-ttu-id="6d144-152">Он используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="6d144-152">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="6d144-153">Существует несколько доступных профилей, как описано в разделе [Профили приложений](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="6d144-153">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": ":::no-loc(Identity):::ServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="6d144-154">appsettings.Development.jsна</span><span class="sxs-lookup"><span data-stu-id="6d144-154">appsettings.Development.json</span></span>

<span data-ttu-id="6d144-155">В *appsettings.Development.js* в файле корневого каталога проекта имеется `:::no-loc(Identity):::Server` раздел, описывающий ключ, используемый для подписи маркеров.</span><span class="sxs-lookup"><span data-stu-id="6d144-155">In the *appsettings.Development.json* file of the project root, there's an `:::no-loc(Identity):::Server` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="6d144-156">При развертывании в рабочей среде ключ должен быть подготовлен и развернут вместе с приложением, как описано в разделе [развертывание в производство](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="6d144-156">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="6d144-157">Общее описание углового приложения</span><span class="sxs-lookup"><span data-stu-id="6d144-157">General description of the Angular app</span></span>

<span data-ttu-id="6d144-158">Поддержка проверки подлинности и авторизации API в угловом шаблоне находится в своем собственном вспомогательном модуле в каталоге *клиентапп\срк\апи-аусоризатион* .</span><span class="sxs-lookup"><span data-stu-id="6d144-158">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="6d144-159">Модуль состоит из следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="6d144-159">The module is composed of the following elements:</span></span>

* <span data-ttu-id="6d144-160">3 компонента:</span><span class="sxs-lookup"><span data-stu-id="6d144-160">3 components:</span></span>
  * <span data-ttu-id="6d144-161">*Login. Component. TS* : обрабатывает поток входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="6d144-161">*login.component.ts* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="6d144-162">*logout. Component. TS* : обрабатывает поток выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-162">*logout.component.ts* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="6d144-163">*имя входа. Component. TS* : мини-приложение, которое отображает один из следующих наборов ссылок:</span><span class="sxs-lookup"><span data-stu-id="6d144-163">*login-menu.component.ts* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="6d144-164">Управление профилями пользователей и ссылки для выхода, когда пользователь прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-164">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="6d144-165">Регистрация и вход в систему, если пользователь не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-165">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="6d144-166">Предохранитель маршрута `AuthorizeGuard` , который можно добавить к маршрутам и требует проверки подлинности пользователя перед посещением маршрута.</span><span class="sxs-lookup"><span data-stu-id="6d144-166">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="6d144-167">Перехватчик HTTP `AuthorizeInterceptor` , который прикрепляет маркер доступа к исходящим HTTP-запросам, направленным на API, когда пользователь прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-167">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="6d144-168">Служба `AuthorizeService` , которая обрабатывает сведения о процессе проверки подлинности на более низком уровне и предоставляет сведения о пользователе, прошедшем проверку подлинности, в оставшейся части приложения для использования.</span><span class="sxs-lookup"><span data-stu-id="6d144-168">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="6d144-169">Угловой модуль, который определяет маршруты, связанные с элементами проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-169">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="6d144-170">Он предоставляет компонент меню входа, перехватчик, условие и службу для использования в остальной части приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-170">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="6d144-171">Общее описание приложения для реагирования</span><span class="sxs-lookup"><span data-stu-id="6d144-171">General description of the React app</span></span>

<span data-ttu-id="6d144-172">Поддержка проверки подлинности и авторизации API в шаблоне отклика находится в каталоге *клиентапп\срк\компонентс\апи-аусоризатион* .</span><span class="sxs-lookup"><span data-stu-id="6d144-172">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="6d144-173">Он состоит из следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="6d144-173">It's composed of the following elements:</span></span>

* <span data-ttu-id="6d144-174">4 компонента:</span><span class="sxs-lookup"><span data-stu-id="6d144-174">4 components:</span></span>
  * <span data-ttu-id="6d144-175">*Login.js* : обрабатывает поток входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="6d144-175">*Login.js* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="6d144-176">*Logout.js* : обрабатывает поток выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-176">*Logout.js* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="6d144-177">*LoginMenu.js* : мини-приложение, отображающее один из следующих наборов ссылок:</span><span class="sxs-lookup"><span data-stu-id="6d144-177">*LoginMenu.js* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="6d144-178">Управление профилями пользователей и ссылки для выхода, когда пользователь прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-178">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="6d144-179">Регистрация и вход в систему, если пользователь не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-179">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="6d144-180">*AuthorizeRoute.js* : компонент маршрута, для которого требуется проверка подлинности пользователя перед отрисовкой компонента, указанного в `Component` параметре.</span><span class="sxs-lookup"><span data-stu-id="6d144-180">*AuthorizeRoute.js* : A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="6d144-181">Экспортированный `authService` экземпляр класса `AuthorizeService` , который обрабатывает сведения о процессе проверки подлинности на более низком уровне и предоставляет сведения о пользователе, прошедшем проверку подлинности, в оставшейся части приложения для использования.</span><span class="sxs-lookup"><span data-stu-id="6d144-181">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="6d144-182">Теперь, когда вы видели основные компоненты решения, вы можете более подробно изучить отдельные сценарии для приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-182">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="6d144-183">Требовать авторизацию для нового API</span><span class="sxs-lookup"><span data-stu-id="6d144-183">Require authorization on a new API</span></span>

<span data-ttu-id="6d144-184">По умолчанию система настроена для простоты авторизации новых API-интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="6d144-184">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="6d144-185">Для этого создайте новый контроллер и добавьте `[Authorize]` атрибут в класс Controller или в любое действие в контроллере.</span><span class="sxs-lookup"><span data-stu-id="6d144-185">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="6d144-186">Настройка обработчика проверки подлинности API</span><span class="sxs-lookup"><span data-stu-id="6d144-186">Customize the API authentication handler</span></span>

<span data-ttu-id="6d144-187">Чтобы настроить конфигурацию обработчика JWT API, настройте его <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> экземпляр:</span><span class="sxs-lookup"><span data-stu-id="6d144-187">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .Add:::no-loc(Identity):::ServerJwt();

services.Configure<JwtBearerOptions>(
    :::no-loc(Identity):::ServerJwtConstants.:::no-loc(Identity):::ServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="6d144-188">Обработчик JWT API создает события, которые обеспечивают контроль над процессом проверки подлинности с помощью `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="6d144-188">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="6d144-189">Чтобы обеспечить поддержку авторизации API, `Add:::no-loc(Identity):::ServerJwt` регистрирует собственные обработчики событий.</span><span class="sxs-lookup"><span data-stu-id="6d144-189">To provide support for API authorization, `Add:::no-loc(Identity):::ServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="6d144-190">Чтобы настроить обработку события, заключите существующий обработчик событий в требуемую дополнительную логику.</span><span class="sxs-lookup"><span data-stu-id="6d144-190">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="6d144-191">Пример:</span><span class="sxs-lookup"><span data-stu-id="6d144-191">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    :::no-loc(Identity):::ServerJwtConstants.:::no-loc(Identity):::ServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="6d144-192">В приведенном выше коде `OnTokenValidated` обработчик событий заменяется пользовательской реализацией.</span><span class="sxs-lookup"><span data-stu-id="6d144-192">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="6d144-193">Эта реализация:</span><span class="sxs-lookup"><span data-stu-id="6d144-193">This implementation:</span></span>

1. <span data-ttu-id="6d144-194">Вызывает исходную реализацию, предоставляемую службой поддержки авторизации API.</span><span class="sxs-lookup"><span data-stu-id="6d144-194">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="6d144-195">Запустите собственную пользовательскую логику.</span><span class="sxs-lookup"><span data-stu-id="6d144-195">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="6d144-196">Защита маршрута на стороне клиента (угловой)</span><span class="sxs-lookup"><span data-stu-id="6d144-196">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="6d144-197">Защита маршрута на стороне клиента выполняется путем добавления защиты авторизовать в список условий, выполняемых при настройке маршрута.</span><span class="sxs-lookup"><span data-stu-id="6d144-197">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="6d144-198">В качестве примера можно увидеть, как `fetch-data` настраивается маршрут в главном модуле приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-198">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="6d144-199">Важно упомянуть, что защита маршрута не защищает фактическую конечную точку (к которой по-прежнему требуется `[Authorize]` примененный атрибут), но она только запрещает пользователю переходить к конкретному маршруту на стороне клиента, если он не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-199">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="6d144-200">Проверка подлинности запросов API (угловой)</span><span class="sxs-lookup"><span data-stu-id="6d144-200">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="6d144-201">Проверка подлинности запросов к API, размещенным вместе с приложением, выполняется автоматически с помощью перехватчика клиента HTTP, определенного приложением.</span><span class="sxs-lookup"><span data-stu-id="6d144-201">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="6d144-202">Защита маршрута на стороне клиента (реакция)</span><span class="sxs-lookup"><span data-stu-id="6d144-202">Protect a client-side route (React)</span></span>

<span data-ttu-id="6d144-203">Защитите клиентский маршрут, используя компонент, `AuthorizeRoute` а не обычный `Route` компонент.</span><span class="sxs-lookup"><span data-stu-id="6d144-203">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="6d144-204">Например, обратите внимание, что `fetch-data` маршрут настроен в `App` компоненте:</span><span class="sxs-lookup"><span data-stu-id="6d144-204">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="6d144-205">Защита маршрута:</span><span class="sxs-lookup"><span data-stu-id="6d144-205">Protecting a route:</span></span>

* <span data-ttu-id="6d144-206">Не защищает фактическую конечную точку (к которой по-прежнему требуется `[Authorize]` примененный атрибут).</span><span class="sxs-lookup"><span data-stu-id="6d144-206">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="6d144-207">Не позволяет пользователю перейти к данному маршруту на стороне клиента, если он не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="6d144-207">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="6d144-208">Проверка подлинности запросов API (реакция)</span><span class="sxs-lookup"><span data-stu-id="6d144-208">Authenticate API requests (React)</span></span>

<span data-ttu-id="6d144-209">Проверка подлинности запросов с реагированием выполняется путем первоначального импорта `authService` экземпляра из `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="6d144-209">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="6d144-210">Маркер доступа извлекается из `authService` и присоединяется к запросу, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="6d144-210">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="6d144-211">В окне «реагирующие компоненты» эта работа обычно выполняется в `componentDidMount` методе жизненного цикла или в результате какого-либо взаимодействия с пользователем.</span><span class="sxs-lookup"><span data-stu-id="6d144-211">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="6d144-212">Импорт Ауссервице в компонент</span><span class="sxs-lookup"><span data-stu-id="6d144-212">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="6d144-213">Получение и присоединение маркера доступа к ответу</span><span class="sxs-lookup"><span data-stu-id="6d144-213">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="6d144-214">Развертывание в рабочую среду</span><span class="sxs-lookup"><span data-stu-id="6d144-214">Deploy to production</span></span>

<span data-ttu-id="6d144-215">Чтобы развернуть приложение в рабочей среде, необходимо подготовить следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="6d144-215">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="6d144-216">База данных для хранения :::no-loc(Identity)::: учетных записей пользователей и :::no-loc(Identity)::: серверов.</span><span class="sxs-lookup"><span data-stu-id="6d144-216">A database to store the :::no-loc(Identity)::: user accounts and the :::no-loc(Identity):::Server grants.</span></span>
* <span data-ttu-id="6d144-217">Рабочий сертификат, используемый для подписи маркеров.</span><span class="sxs-lookup"><span data-stu-id="6d144-217">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="6d144-218">Для этого сертификата нет особых требований; Это может быть самозаверяющий сертификат или сертификат, подготовленный через центр сертификации.</span><span class="sxs-lookup"><span data-stu-id="6d144-218">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="6d144-219">Его можно создать с помощью стандартных средств, таких как PowerShell или OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="6d144-219">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="6d144-220">Его можно установить в хранилище сертификатов на целевых компьютерах или развернуть в виде *PFX* -файла с надежным паролем.</span><span class="sxs-lookup"><span data-stu-id="6d144-220">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="6d144-221">Пример. Развертывание в поставщик услуг размещения веб-сайтов, не относящегося к Azure</span><span class="sxs-lookup"><span data-stu-id="6d144-221">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="6d144-222">На панели размещения веб-сайтов создайте или загрузите сертификат.</span><span class="sxs-lookup"><span data-stu-id="6d144-222">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="6d144-223">Затем в *:::no-loc(appsettings.json):::* файле приложения измените `:::no-loc(Identity):::Server` раздел, включив в него ключевые сведения.</span><span class="sxs-lookup"><span data-stu-id="6d144-223">Then in the app's *:::no-loc(appsettings.json):::* file, modify the `:::no-loc(Identity):::Server` section to include the key details.</span></span> <span data-ttu-id="6d144-224">Пример:</span><span class="sxs-lookup"><span data-stu-id="6d144-224">For example:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="6d144-225">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="6d144-225">In the preceding example:</span></span>

* <span data-ttu-id="6d144-226">`StoreName` представляет имя хранилища сертификатов, в котором хранится сертификат.</span><span class="sxs-lookup"><span data-stu-id="6d144-226">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="6d144-227">В этом случае он указывает на хранилище веб-хостинга.</span><span class="sxs-lookup"><span data-stu-id="6d144-227">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="6d144-228">`StoreLocation` представляет место загрузки сертификата ( `CurrentUser` в данном случае).</span><span class="sxs-lookup"><span data-stu-id="6d144-228">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="6d144-229">`Name` соответствует отличительной теме сертификата.</span><span class="sxs-lookup"><span data-stu-id="6d144-229">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="6d144-230">Пример. Развертывание в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="6d144-230">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="6d144-231">В этом разделе описывается развертывание приложения в службе приложений Azure с помощью сертификата, хранящегося в хранилище сертификатов.</span><span class="sxs-lookup"><span data-stu-id="6d144-231">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="6d144-232">Чтобы изменить приложение для загрузки сертификата из хранилища сертификатов, при настройке приложения в портал Azure на более позднем этапе требуется план обслуживания уровня "Стандартный" или более подходящий.</span><span class="sxs-lookup"><span data-stu-id="6d144-232">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="6d144-233">В *:::no-loc(appsettings.json):::* файле приложения измените `:::no-loc(Identity):::Server` раздел, включив в него ключевые сведения:</span><span class="sxs-lookup"><span data-stu-id="6d144-233">In the app's *:::no-loc(appsettings.json):::* file, modify the `:::no-loc(Identity):::Server` section to include the key details:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="6d144-234">Имя хранилища представляет имя хранилища сертификатов, в котором хранится сертификат.</span><span class="sxs-lookup"><span data-stu-id="6d144-234">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="6d144-235">В этом случае он указывает на личное хранилище пользователей.</span><span class="sxs-lookup"><span data-stu-id="6d144-235">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="6d144-236">Расположение хранилища представляет место загрузки сертификата из ( `CurrentUser` или `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="6d144-236">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="6d144-237">Свойство Name в сертификате соответствует отличительной теме сертификата.</span><span class="sxs-lookup"><span data-stu-id="6d144-237">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="6d144-238">Чтобы выполнить развертывание в службе приложений Azure, выполните действия, описанные в разделе [развертывание приложения в Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), в котором объясняется, как создать необходимые ресурсы Azure и развернуть приложение в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="6d144-238">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="6d144-239">После выполнения указанных выше инструкций приложение развертывается в Azure, но еще не работает.</span><span class="sxs-lookup"><span data-stu-id="6d144-239">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="6d144-240">Сертификат, используемый приложением, должен быть настроен в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="6d144-240">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="6d144-241">Перейдите к отпечатку сертификата и выполните действия, описанные в разделе [Загрузка сертификатов](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="6d144-241">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="6d144-242">Хотя эти действия упоминают SSL, в портал Azure, где можно передать подготовленный сертификат для использования с приложением, имеется раздел **частные сертификаты** .</span><span class="sxs-lookup"><span data-stu-id="6d144-242">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="6d144-243">После настройки приложения и параметров приложения в портал Azure перезапустите приложение на портале.</span><span class="sxs-lookup"><span data-stu-id="6d144-243">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="6d144-244">Другие параметры конфигурации</span><span class="sxs-lookup"><span data-stu-id="6d144-244">Other configuration options</span></span>

<span data-ttu-id="6d144-245">Поддержка авторизации API основана на :::no-loc(Identity)::: сервере с набором соглашений, значениями по умолчанию и усовершенствованиями для упрощения работы одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-245">The support for API authorization builds on top of :::no-loc(Identity):::Server with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="6d144-246">Нет нужды говорить, что все возможности сервера доступны в фоновом режиме, :::no-loc(Identity)::: если ASP.NET Core интеграции не охватывают ваш сценарий.</span><span class="sxs-lookup"><span data-stu-id="6d144-246">Needless to say, the full power of :::no-loc(Identity):::Server is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="6d144-247">Поддержка ASP.NET Coreов сосредоточена на «первых» приложениях, где все приложения создаются и развертываются в нашей Организации.</span><span class="sxs-lookup"><span data-stu-id="6d144-247">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="6d144-248">Таким образом, поддержка не предоставляется для таких вещей, как согласие или Федерация.</span><span class="sxs-lookup"><span data-stu-id="6d144-248">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="6d144-249">Для этих сценариев используйте :::no-loc(Identity)::: сервер и следуйте его документации.</span><span class="sxs-lookup"><span data-stu-id="6d144-249">For those scenarios, use :::no-loc(Identity):::Server and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="6d144-250">Профили приложений</span><span class="sxs-lookup"><span data-stu-id="6d144-250">Application profiles</span></span>

<span data-ttu-id="6d144-251">Профили приложений — это предопределенные конфигурации для приложений, которые дополнительно определяют свои параметры.</span><span class="sxs-lookup"><span data-stu-id="6d144-251">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="6d144-252">В настоящее время поддерживаются следующие профили:</span><span class="sxs-lookup"><span data-stu-id="6d144-252">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="6d144-253">`:::no-loc(Identity):::ServerSPA`— Представляет защищенный, размещенный вместе с :::no-loc(Identity)::: сервером объект как единое целое.</span><span class="sxs-lookup"><span data-stu-id="6d144-253">`:::no-loc(Identity):::ServerSPA`: Represents a SPA hosted alongside :::no-loc(Identity):::Server as a single unit.</span></span>
  * <span data-ttu-id="6d144-254">Значение `redirect_uri` по умолчанию — `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6d144-254">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="6d144-255">Значение `post_logout_redirect_uri` по умолчанию — `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="6d144-255">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="6d144-256">Набор областей включает `openid` , `profile` и все области, определенные для API-интерфейсов в приложении.</span><span class="sxs-lookup"><span data-stu-id="6d144-256">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="6d144-257">Набор разрешенных типов ответов OIDC — `id_token token` или каждый из них по отдельности ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="6d144-257">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="6d144-258">Допустимый режим ответа — `fragment` .</span><span class="sxs-lookup"><span data-stu-id="6d144-258">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="6d144-259">`SPA`— Это SPA, которая не размещена на :::no-loc(Identity)::: сервере.</span><span class="sxs-lookup"><span data-stu-id="6d144-259">`SPA`: Represents a SPA that isn't hosted with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="6d144-260">Набор областей включает `openid` , `profile` и все области, определенные для API-интерфейсов в приложении.</span><span class="sxs-lookup"><span data-stu-id="6d144-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="6d144-261">Набор разрешенных типов ответов OIDC — `id_token token` или каждый из них по отдельности ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="6d144-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="6d144-262">Допустимый режим ответа — `fragment` .</span><span class="sxs-lookup"><span data-stu-id="6d144-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="6d144-263">`:::no-loc(Identity):::ServerJwt`— Представляет API, размещенный вместе с :::no-loc(Identity)::: сервером.</span><span class="sxs-lookup"><span data-stu-id="6d144-263">`:::no-loc(Identity):::ServerJwt`: Represents an API that is hosted alongside with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="6d144-264">В приложении настроена одна область, которая по умолчанию имеет имя приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-264">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="6d144-265">`API`— Представляет API, который не размещен на :::no-loc(Identity)::: сервере.</span><span class="sxs-lookup"><span data-stu-id="6d144-265">`API`: Represents an API that isn't hosted with :::no-loc(Identity):::Server.</span></span>
  * <span data-ttu-id="6d144-266">В приложении настроена одна область, которая по умолчанию имеет имя приложения.</span><span class="sxs-lookup"><span data-stu-id="6d144-266">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="6d144-267">Настройка с помощью AppSettings</span><span class="sxs-lookup"><span data-stu-id="6d144-267">Configuration through AppSettings</span></span>

<span data-ttu-id="6d144-268">Настройте приложения в системе конфигурации, добавив их в список `Clients` или `Resources` .</span><span class="sxs-lookup"><span data-stu-id="6d144-268">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="6d144-269">Настройте каждое клиентское `redirect_uri` `post_logout_redirect_uri` свойство и, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6d144-269">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="6d144-270">При настройке ресурсов можно настроить области для ресурса, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="6d144-270">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
":::no-loc(Identity):::Server": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="6d144-271">Настройка с помощью кода</span><span class="sxs-lookup"><span data-stu-id="6d144-271">Configuration through code</span></span>

<span data-ttu-id="6d144-272">Можно также настроить клиенты и ресурсы с помощью кода, используя перегрузку `AddApiAuthorization` , которая принимает действие для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="6d144-272">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="6d144-273">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6d144-273">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
