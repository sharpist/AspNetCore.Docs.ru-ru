---
title: Общие сведения о проверке подлинности для одностраничных приложений на ASP.NET Core
author: javiercn
description: Используйте Identity приложение с одним страничным приложением, размещенным в ASP.NET Core приложении.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 21bd1db322a984b5644b817e82a293b6c0b2d91e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019333"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="3b178-103">Проверка подлинности и авторизация для одностраничные приложения</span><span class="sxs-lookup"><span data-stu-id="3b178-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="3b178-104">ASP.NET Core 3,0 или более поздней версии обеспечивает проверку подлинности в одностраничных приложениях (одностраничные приложения) с помощью поддержки авторизации API.</span><span class="sxs-lookup"><span data-stu-id="3b178-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="3b178-105">ASP.NET Core Identity для проверки подлинности и хранения пользователей объединяется с [ Identity сервером](https://identityserver.io/) для реализации OpenID Connect Connect.</span><span class="sxs-lookup"><span data-stu-id="3b178-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="3b178-106">В шаблоны проектов " **угловой** " и " **отреагировать** " был добавлен параметр проверки подлинности, аналогичный параметру проверки подлинности в шаблонах проектов **веб-приложения (модель-представление-контроллер)** (MVC) и **веб-приложение** ( Razor страницы).</span><span class="sxs-lookup"><span data-stu-id="3b178-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="3b178-107">Допустимые значения параметра: **None** и **индивидуальных**.</span><span class="sxs-lookup"><span data-stu-id="3b178-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="3b178-108">Шаблон проекта **React.js и Redux** в настоящее время не поддерживает параметр проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="3b178-109">Создание приложения с поддержкой авторизации API</span><span class="sxs-lookup"><span data-stu-id="3b178-109">Create an app with API authorization support</span></span>

<span data-ttu-id="3b178-110">Проверку подлинности и авторизацию пользователя можно использовать как в радиальном, так и на одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="3b178-111">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="3b178-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="3b178-112">**Угловой**:</span><span class="sxs-lookup"><span data-stu-id="3b178-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="3b178-113">**Реагировать**:</span><span class="sxs-lookup"><span data-stu-id="3b178-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="3b178-114">Предыдущая команда создает приложение ASP.NET Core с каталогом *ClientApp* , СОДЕРЖАЩИМ значение SPA.</span><span class="sxs-lookup"><span data-stu-id="3b178-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="3b178-115">Общее описание компонентов ASP.NET Core приложения</span><span class="sxs-lookup"><span data-stu-id="3b178-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="3b178-116">В следующих разделах описываются дополнения к проекту при включенной поддержке проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="3b178-117">Класс Startup</span><span class="sxs-lookup"><span data-stu-id="3b178-117">Startup class</span></span>

<span data-ttu-id="3b178-118">В следующих примерах кода используются [Microsoft. AspNetCore. апиаусоризатион. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer)Пакет NuGet сервера.</span><span class="sxs-lookup"><span data-stu-id="3b178-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="3b178-119">В примерах настраивается проверка подлинности и авторизация API с помощью <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> методов расширения и.</span><span class="sxs-lookup"><span data-stu-id="3b178-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="3b178-120">Проекты, использующие шаблоны проектов "реагирующий" или "угловой SPA" с проверкой подлинности, включают ссылку на этот пакет.</span><span class="sxs-lookup"><span data-stu-id="3b178-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="3b178-121">`Startup`Класс имеет следующие дополнения:</span><span class="sxs-lookup"><span data-stu-id="3b178-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="3b178-122">Внутри `Startup.ConfigureServices` метода:</span><span class="sxs-lookup"><span data-stu-id="3b178-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="3b178-123">Identityс помощью пользовательского интерфейса по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="3b178-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="3b178-124">IdentityСервер с дополнительным `AddApiAuthorization` вспомогательным методом, который устанавливает некоторые соглашения ASP.NET Core по умолчанию поверх Identity сервера:</span><span class="sxs-lookup"><span data-stu-id="3b178-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="3b178-125">Аутентификация с помощью дополнительного вспомогательного метода `AddIdentityServerJwt`, который настраивает приложение для проверки маркеров JWT, созданных IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="3b178-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="3b178-126">Внутри `Startup.Configure` метода:</span><span class="sxs-lookup"><span data-stu-id="3b178-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="3b178-127">По промежуточного слоя для проверки подлинности, которое отвечает за проверку учетных данных запроса и настройку пользователя в контексте запроса:</span><span class="sxs-lookup"><span data-stu-id="3b178-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="3b178-128">По Identity промежуточного слоя сервера, предоставляющего конечные точки подключения OpenID Connect:</span><span class="sxs-lookup"><span data-stu-id="3b178-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="3b178-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="3b178-129">AddApiAuthorization</span></span>

<span data-ttu-id="3b178-130">Этот вспомогательный метод настраивает Identity сервер для использования нашей поддерживаемой конфигурации.</span><span class="sxs-lookup"><span data-stu-id="3b178-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="3b178-131">IdentityServer — это функциональная и расширяемая платформа для повышения уровня безопасности приложений.</span><span class="sxs-lookup"><span data-stu-id="3b178-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="3b178-132">В то же время это делает ненужную сложность для наиболее распространенных сценариев.</span><span class="sxs-lookup"><span data-stu-id="3b178-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="3b178-133">Следовательно, для вас предоставляется набор соглашений и параметров конфигурации, которые считаются хорошей отправной точкой.</span><span class="sxs-lookup"><span data-stu-id="3b178-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="3b178-134">После изменения требований к проверке подлинности все возможности Identity сервера по-прежнему доступны для настройки проверки подлинности в соответствии с вашими потребностями.</span><span class="sxs-lookup"><span data-stu-id="3b178-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="3b178-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="3b178-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="3b178-136">Этот вспомогательный метод настраивает схему политики для приложения в качестве обработчика проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b178-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="3b178-137">Политика настроена таким путем, чтобы разрешить Identity обработку всех запросов, направляемых по любому вложенному пути в Identity пространстве URL-адресов "/ Identity ".</span><span class="sxs-lookup"><span data-stu-id="3b178-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="3b178-138">`JwtBearerHandler` обрабатывает все остальные запросы.</span><span class="sxs-lookup"><span data-stu-id="3b178-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="3b178-139">Кроме того, этот метод регистрирует `<<ApplicationName>>API` ресурс API с Identity сервером с областью по умолчанию `<<ApplicationName>>API` и настраивает по промежуточного слоя маркера носителя JWT для проверки маркеров, выданных Identity сервером для приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="3b178-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="3b178-140">WeatherForecastController</span></span>

<span data-ttu-id="3b178-141">В файле *контроллерс\веасерфорекастконтроллер.КС* Обратите внимание на `[Authorize]` атрибут, примененный к классу, который указывает, что пользователь должен быть полномочным, исходя из политики по умолчанию для доступа к ресурсу.</span><span class="sxs-lookup"><span data-stu-id="3b178-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="3b178-142">Политика авторизации по умолчанию должна быть настроена для использования схемы проверки подлинности по умолчанию, которая настраивается в описанной `AddIdentityServerJwt` выше схеме политики, что делает этот `JwtBearerHandler` вспомогательный метод обработчиком по умолчанию для запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="3b178-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="3b178-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="3b178-143">ApplicationDbContext</span></span>

<span data-ttu-id="3b178-144">В файле *дата\аппликатиондбконтекст.КС* Обратите внимание на то же, что `DbContext` используется в Identity с исключением, которое оно расширяет `ApiAuthorizationDbContext` (более производным классом от `IdentityDbContext` ), чтобы включить схему для Identity сервера.</span><span class="sxs-lookup"><span data-stu-id="3b178-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="3b178-145">Чтобы получить полный контроль над схемой базы данных, наследуйте один из доступных Identity `DbContext` классов и настройте контекст для включения Identity схемы путем вызова `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` метода в методе.</span><span class="sxs-lookup"><span data-stu-id="3b178-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="3b178-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="3b178-146">OidcConfigurationController</span></span>

<span data-ttu-id="3b178-147">В файле *контроллерс\оидкконфигуратионконтроллер.КС* Обратите внимание на конечную точку, подготовленную для обслуживания параметров OIDC, которые необходимо использовать клиенту.</span><span class="sxs-lookup"><span data-stu-id="3b178-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="3b178-148">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="3b178-148">appsettings.json</span></span>

<span data-ttu-id="3b178-149">В *appsettings.js* в файле корневого каталога проекта имеется новый `IdentityServer` раздел, описывающий список настроенных клиентов.</span><span class="sxs-lookup"><span data-stu-id="3b178-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="3b178-150">В следующем примере есть один клиент.</span><span class="sxs-lookup"><span data-stu-id="3b178-150">In the following example, there's a single client.</span></span> <span data-ttu-id="3b178-151">Имя клиента соответствует имени приложения и сопоставляется по соглашению с параметром `ClientId` OAuth.</span><span class="sxs-lookup"><span data-stu-id="3b178-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="3b178-152">Профиль указывает на настраиваемый тип приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="3b178-153">Он используется внутренне для обозначения соглашений, упрощающих процесс настройки сервера.</span><span class="sxs-lookup"><span data-stu-id="3b178-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="3b178-154">Существует несколько доступных профилей, как описано в разделе [Профили приложений](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="3b178-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="3b178-155">appsettings.Development.jsна</span><span class="sxs-lookup"><span data-stu-id="3b178-155">appsettings.Development.json</span></span>

<span data-ttu-id="3b178-156">В *appsettings.Development.js* в файле корневого каталога проекта имеется `IdentityServer` раздел, описывающий ключ, используемый для подписи маркеров.</span><span class="sxs-lookup"><span data-stu-id="3b178-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="3b178-157">При развертывании в рабочей среде ключ должен быть подготовлен и развернут вместе с приложением, как описано в разделе [развертывание в производство](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="3b178-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="3b178-158">Общее описание углового приложения</span><span class="sxs-lookup"><span data-stu-id="3b178-158">General description of the Angular app</span></span>

<span data-ttu-id="3b178-159">Поддержка проверки подлинности и авторизации API в угловом шаблоне находится в своем собственном вспомогательном модуле в каталоге *клиентапп\срк\апи-аусоризатион* .</span><span class="sxs-lookup"><span data-stu-id="3b178-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="3b178-160">Модуль состоит из следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="3b178-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="3b178-161">3 компонента:</span><span class="sxs-lookup"><span data-stu-id="3b178-161">3 components:</span></span>
  * <span data-ttu-id="3b178-162">*Login. Component. TS*: обрабатывает поток входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="3b178-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="3b178-163">*logout. Component. TS*: обрабатывает поток выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="3b178-164">*имя входа. Component. TS*: мини-приложение, которое отображает один из следующих наборов ссылок:</span><span class="sxs-lookup"><span data-stu-id="3b178-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="3b178-165">Управление профилями пользователей и ссылки для выхода, когда пользователь прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="3b178-166">Регистрация и вход в систему, если пользователь не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="3b178-167">Предохранитель маршрута `AuthorizeGuard` , который можно добавить к маршрутам и требует проверки подлинности пользователя перед посещением маршрута.</span><span class="sxs-lookup"><span data-stu-id="3b178-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="3b178-168">Перехватчик HTTP `AuthorizeInterceptor` , который прикрепляет маркер доступа к исходящим HTTP-запросам, направленным на API, когда пользователь прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="3b178-169">Служба `AuthorizeService` , которая обрабатывает сведения о процессе проверки подлинности на более низком уровне и предоставляет сведения о пользователе, прошедшем проверку подлинности, в оставшейся части приложения для использования.</span><span class="sxs-lookup"><span data-stu-id="3b178-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="3b178-170">Угловой модуль, который определяет маршруты, связанные с элементами проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="3b178-171">Он предоставляет компонент меню входа, перехватчик, условие и службу для использования в остальной части приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="3b178-172">Общее описание приложения для реагирования</span><span class="sxs-lookup"><span data-stu-id="3b178-172">General description of the React app</span></span>

<span data-ttu-id="3b178-173">Поддержка проверки подлинности и авторизации API в шаблоне отклика находится в каталоге *клиентапп\срк\компонентс\апи-аусоризатион* .</span><span class="sxs-lookup"><span data-stu-id="3b178-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="3b178-174">Он состоит из следующих элементов:</span><span class="sxs-lookup"><span data-stu-id="3b178-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="3b178-175">4 компонента:</span><span class="sxs-lookup"><span data-stu-id="3b178-175">4 components:</span></span>
  * <span data-ttu-id="3b178-176">*Login.js*: обрабатывает поток входа в приложение.</span><span class="sxs-lookup"><span data-stu-id="3b178-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="3b178-177">*Logout.js*: обрабатывает поток выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="3b178-178">*LoginMenu.js*: мини-приложение, отображающее один из следующих наборов ссылок:</span><span class="sxs-lookup"><span data-stu-id="3b178-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="3b178-179">Управление профилями пользователей и ссылки для выхода, когда пользователь прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="3b178-180">Регистрация и вход в систему, если пользователь не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="3b178-181">*AuthorizeRoute.js*: компонент маршрута, для которого требуется проверка подлинности пользователя перед отрисовкой компонента, указанного в `Component` параметре.</span><span class="sxs-lookup"><span data-stu-id="3b178-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="3b178-182">Экспортированный `authService` экземпляр класса `AuthorizeService` , который обрабатывает сведения о процессе проверки подлинности на более низком уровне и предоставляет сведения о пользователе, прошедшем проверку подлинности, в оставшейся части приложения для использования.</span><span class="sxs-lookup"><span data-stu-id="3b178-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="3b178-183">Теперь, когда вы видели основные компоненты решения, вы можете более подробно изучить отдельные сценарии для приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="3b178-184">Требовать авторизацию для нового API</span><span class="sxs-lookup"><span data-stu-id="3b178-184">Require authorization on a new API</span></span>

<span data-ttu-id="3b178-185">По умолчанию система настроена для простоты авторизации новых API-интерфейсов.</span><span class="sxs-lookup"><span data-stu-id="3b178-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="3b178-186">Для этого создайте новый контроллер и добавьте `[Authorize]` атрибут в класс Controller или в любое действие в контроллере.</span><span class="sxs-lookup"><span data-stu-id="3b178-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="3b178-187">Настройка обработчика проверки подлинности API</span><span class="sxs-lookup"><span data-stu-id="3b178-187">Customize the API authentication handler</span></span>

<span data-ttu-id="3b178-188">Чтобы настроить конфигурацию обработчика JWT API, настройте его <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> экземпляр:</span><span class="sxs-lookup"><span data-stu-id="3b178-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="3b178-189">Обработчик JWT API создает события, которые обеспечивают контроль над процессом проверки подлинности с помощью `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="3b178-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="3b178-190">Чтобы обеспечить поддержку авторизации API, `AddIdentityServerJwt` регистрирует собственные обработчики событий.</span><span class="sxs-lookup"><span data-stu-id="3b178-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="3b178-191">Чтобы настроить обработку события, заключите существующий обработчик событий в требуемую дополнительную логику.</span><span class="sxs-lookup"><span data-stu-id="3b178-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="3b178-192">Например:</span><span class="sxs-lookup"><span data-stu-id="3b178-192">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
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

<span data-ttu-id="3b178-193">В приведенном выше коде `OnTokenValidated` обработчик событий заменяется пользовательской реализацией.</span><span class="sxs-lookup"><span data-stu-id="3b178-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="3b178-194">Эта реализация:</span><span class="sxs-lookup"><span data-stu-id="3b178-194">This implementation:</span></span>

1. <span data-ttu-id="3b178-195">Вызывает исходную реализацию, предоставляемую службой поддержки авторизации API.</span><span class="sxs-lookup"><span data-stu-id="3b178-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="3b178-196">Запустите собственную пользовательскую логику.</span><span class="sxs-lookup"><span data-stu-id="3b178-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="3b178-197">Защита маршрута на стороне клиента (угловой)</span><span class="sxs-lookup"><span data-stu-id="3b178-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="3b178-198">Защита маршрута на стороне клиента выполняется путем добавления защиты авторизовать в список условий, выполняемых при настройке маршрута.</span><span class="sxs-lookup"><span data-stu-id="3b178-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="3b178-199">В качестве примера можно увидеть, как `fetch-data` настраивается маршрут в главном модуле приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="3b178-200">Важно упомянуть, что защита маршрута не защищает фактическую конечную точку (к которой по-прежнему требуется `[Authorize]` примененный атрибут), но она только запрещает пользователю переходить к конкретному маршруту на стороне клиента, если он не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="3b178-201">Проверка подлинности запросов API (угловой)</span><span class="sxs-lookup"><span data-stu-id="3b178-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="3b178-202">Проверка подлинности запросов к API, размещенным вместе с приложением, выполняется автоматически с помощью перехватчика клиента HTTP, определенного приложением.</span><span class="sxs-lookup"><span data-stu-id="3b178-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="3b178-203">Защита маршрута на стороне клиента (реакция)</span><span class="sxs-lookup"><span data-stu-id="3b178-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="3b178-204">Защитите клиентский маршрут, используя компонент, `AuthorizeRoute` а не обычный `Route` компонент.</span><span class="sxs-lookup"><span data-stu-id="3b178-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="3b178-205">Например, обратите внимание, что `fetch-data` маршрут настроен в `App` компоненте:</span><span class="sxs-lookup"><span data-stu-id="3b178-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="3b178-206">Защита маршрута:</span><span class="sxs-lookup"><span data-stu-id="3b178-206">Protecting a route:</span></span>

* <span data-ttu-id="3b178-207">Не защищает фактическую конечную точку (к которой по-прежнему требуется `[Authorize]` примененный атрибут).</span><span class="sxs-lookup"><span data-stu-id="3b178-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="3b178-208">Не позволяет пользователю перейти к данному маршруту на стороне клиента, если он не прошел проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="3b178-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="3b178-209">Проверка подлинности запросов API (реакция)</span><span class="sxs-lookup"><span data-stu-id="3b178-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="3b178-210">Проверка подлинности запросов с реагированием выполняется путем первоначального импорта `authService` экземпляра из `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="3b178-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="3b178-211">Маркер доступа извлекается из `authService` и присоединяется к запросу, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="3b178-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="3b178-212">В окне «реагирующие компоненты» эта работа обычно выполняется в `componentDidMount` методе жизненного цикла или в результате какого-либо взаимодействия с пользователем.</span><span class="sxs-lookup"><span data-stu-id="3b178-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="3b178-213">Импорт Ауссервице в компонент</span><span class="sxs-lookup"><span data-stu-id="3b178-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="3b178-214">Получение и присоединение маркера доступа к ответу</span><span class="sxs-lookup"><span data-stu-id="3b178-214">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="3b178-215">Развертывание в рабочую среду</span><span class="sxs-lookup"><span data-stu-id="3b178-215">Deploy to production</span></span>

<span data-ttu-id="3b178-216">Чтобы развернуть приложение в рабочей среде, необходимо подготовить следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="3b178-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="3b178-217">База данных для хранения Identity учетных записей пользователей и Identity серверов.</span><span class="sxs-lookup"><span data-stu-id="3b178-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="3b178-218">Рабочий сертификат, используемый для подписи маркеров.</span><span class="sxs-lookup"><span data-stu-id="3b178-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="3b178-219">Для этого сертификата нет особых требований; Это может быть самозаверяющий сертификат или сертификат, подготовленный через центр сертификации.</span><span class="sxs-lookup"><span data-stu-id="3b178-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="3b178-220">Его можно создать с помощью стандартных средств, таких как PowerShell или OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="3b178-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="3b178-221">Его можно установить в хранилище сертификатов на целевых компьютерах или развернуть в виде *PFX* -файла с надежным паролем.</span><span class="sxs-lookup"><span data-stu-id="3b178-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="3b178-222">Пример. Развертывание в службе приложений Azure</span><span class="sxs-lookup"><span data-stu-id="3b178-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="3b178-223">В этом разделе описывается развертывание приложения в службе приложений Azure с помощью сертификата, хранящегося в хранилище сертификатов.</span><span class="sxs-lookup"><span data-stu-id="3b178-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="3b178-224">Чтобы изменить приложение для загрузки сертификата из хранилища сертификатов, при настройке приложения в портал Azure на более позднем этапе требуется план обслуживания уровня "Стандартный" или более подходящий.</span><span class="sxs-lookup"><span data-stu-id="3b178-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="3b178-225">В файле *appsettings.js* приложения измените `IdentityServer` раздел, включив в него ключевые сведения:</span><span class="sxs-lookup"><span data-stu-id="3b178-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="3b178-226">Имя хранилища представляет имя хранилища сертификатов, в котором хранится сертификат.</span><span class="sxs-lookup"><span data-stu-id="3b178-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="3b178-227">В этом случае он указывает на личное хранилище пользователей.</span><span class="sxs-lookup"><span data-stu-id="3b178-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="3b178-228">Расположение хранилища представляет место загрузки сертификата из ( `CurrentUser` или `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="3b178-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="3b178-229">Свойство Name в сертификате соответствует отличительной теме сертификата.</span><span class="sxs-lookup"><span data-stu-id="3b178-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="3b178-230">Чтобы выполнить развертывание в службе приложений Azure, выполните действия, описанные в разделе [развертывание приложения в Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), в котором объясняется, как создать необходимые ресурсы Azure и развернуть приложение в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="3b178-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="3b178-231">После выполнения указанных выше инструкций приложение развертывается в Azure, но еще не работает.</span><span class="sxs-lookup"><span data-stu-id="3b178-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="3b178-232">Сертификат, используемый приложением, должен быть настроен в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="3b178-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="3b178-233">Перейдите к отпечатку сертификата и выполните действия, описанные в разделе [Загрузка сертификатов](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="3b178-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="3b178-234">Хотя эти действия упоминают SSL, в портал Azure, где можно передать подготовленный сертификат для использования с приложением, имеется раздел **частные сертификаты** .</span><span class="sxs-lookup"><span data-stu-id="3b178-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="3b178-235">После настройки приложения и параметров приложения в портал Azure перезапустите приложение на портале.</span><span class="sxs-lookup"><span data-stu-id="3b178-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="3b178-236">Другие параметры конфигурации</span><span class="sxs-lookup"><span data-stu-id="3b178-236">Other configuration options</span></span>

<span data-ttu-id="3b178-237">Поддержка авторизации API основана на Identity сервере с набором соглашений, значениями по умолчанию и усовершенствованиями для упрощения работы одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="3b178-238">Нет нужды говорить, что все возможности сервера доступны в фоновом режиме, Identity если ASP.NET Core интеграции не охватывают ваш сценарий.</span><span class="sxs-lookup"><span data-stu-id="3b178-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="3b178-239">Поддержка ASP.NET Coreов сосредоточена на «первых» приложениях, где все приложения создаются и развертываются в нашей Организации.</span><span class="sxs-lookup"><span data-stu-id="3b178-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="3b178-240">Таким образом, поддержка не предоставляется для таких вещей, как согласие или Федерация.</span><span class="sxs-lookup"><span data-stu-id="3b178-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="3b178-241">Для этих сценариев используйте Identity сервер и следуйте его документации.</span><span class="sxs-lookup"><span data-stu-id="3b178-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="3b178-242">Профили приложений</span><span class="sxs-lookup"><span data-stu-id="3b178-242">Application profiles</span></span>

<span data-ttu-id="3b178-243">Профили приложений — это предопределенные конфигурации для приложений, которые дополнительно определяют свои параметры.</span><span class="sxs-lookup"><span data-stu-id="3b178-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="3b178-244">В настоящее время поддерживаются следующие профили:</span><span class="sxs-lookup"><span data-stu-id="3b178-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="3b178-245">`IdentityServerSPA`— Представляет защищенный, размещенный вместе с Identity сервером объект как единое целое.</span><span class="sxs-lookup"><span data-stu-id="3b178-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="3b178-246">Значение `redirect_uri` по умолчанию — `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="3b178-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="3b178-247">Значение `post_logout_redirect_uri` по умолчанию — `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="3b178-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="3b178-248">Набор областей включает `openid` , `profile` и все области, определенные для API-интерфейсов в приложении.</span><span class="sxs-lookup"><span data-stu-id="3b178-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="3b178-249">Набор разрешенных типов ответов OIDC — `id_token token` или каждый из них по отдельности ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="3b178-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="3b178-250">Допустимый режим ответа — `fragment` .</span><span class="sxs-lookup"><span data-stu-id="3b178-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="3b178-251">`SPA`— Это SPA, которая не размещена на Identity сервере.</span><span class="sxs-lookup"><span data-stu-id="3b178-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="3b178-252">Набор областей включает `openid` , `profile` и все области, определенные для API-интерфейсов в приложении.</span><span class="sxs-lookup"><span data-stu-id="3b178-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="3b178-253">Набор разрешенных типов ответов OIDC — `id_token token` или каждый из них по отдельности ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="3b178-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="3b178-254">Допустимый режим ответа — `fragment` .</span><span class="sxs-lookup"><span data-stu-id="3b178-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="3b178-255">`IdentityServerJwt`— Представляет API, размещенный вместе с Identity сервером.</span><span class="sxs-lookup"><span data-stu-id="3b178-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="3b178-256">В приложении настроена одна область, которая по умолчанию имеет имя приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="3b178-257">`API`— Представляет API, который не размещен на Identity сервере.</span><span class="sxs-lookup"><span data-stu-id="3b178-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="3b178-258">В приложении настроена одна область, которая по умолчанию имеет имя приложения.</span><span class="sxs-lookup"><span data-stu-id="3b178-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="3b178-259">Настройка с помощью AppSettings</span><span class="sxs-lookup"><span data-stu-id="3b178-259">Configuration through AppSettings</span></span>

<span data-ttu-id="3b178-260">Настройте приложения в системе конфигурации, добавив их в список `Clients` или `Resources` .</span><span class="sxs-lookup"><span data-stu-id="3b178-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="3b178-261">Настройте каждое клиентское `redirect_uri` `post_logout_redirect_uri` свойство и, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="3b178-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="3b178-262">При настройке ресурсов можно настроить области для ресурса, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="3b178-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="3b178-263">Настройка с помощью кода</span><span class="sxs-lookup"><span data-stu-id="3b178-263">Configuration through code</span></span>

<span data-ttu-id="3b178-264">Можно также настроить клиенты и ресурсы с помощью кода, используя перегрузку `AddApiAuthorization` , которая принимает действие для настройки параметров.</span><span class="sxs-lookup"><span data-stu-id="3b178-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="3b178-265">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3b178-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
