---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 795709853941f35b1645f72d6865fe1ebf935112
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712367"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="fa3e4-103">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fa3e4-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="fa3e4-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="fa3e4-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fa3e4-105">*При использовании Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этой статье. См. статьи, посвященные AAD и AAD B2C, в этом разделе оглавления.*</span><span class="sxs-lookup"><span data-stu-id="fa3e4-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="fa3e4-106">Чтобы создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), которое использует библиотеку [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication), выполните рекомендации с учетом используемых средств.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa3e4-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa3e4-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fa3e4-108">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="fa3e4-109">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="fa3e4-110">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="fa3e4-111">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fa3e4-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="fa3e4-112">Создайте новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-112">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="fa3e4-113">Задайте механизм аутентификации `Individual` с параметром `-au|--auth`, чтобы сохранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="fa3e4-114">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="fa3e4-114">Placeholder</span></span>  | <span data-ttu-id="fa3e4-115">Пример</span><span class="sxs-lookup"><span data-stu-id="fa3e4-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="fa3e4-116">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="fa3e4-117">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fa3e4-118">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fa3e4-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fa3e4-119">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="fa3e4-120">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="fa3e4-121">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="fa3e4-122">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fa3e4-122">Authentication package</span></span>

<span data-ttu-id="fa3e4-123">Когда приложение создается для использования отдельных учетных записей пользователей, оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="fa3e4-124">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fa3e4-125">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="fa3e4-126">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="fa3e4-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fa3e4-127">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fa3e4-127">Authentication service support</span></span>

<span data-ttu-id="fa3e4-128">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="fa3e4-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="fa3e4-129">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="fa3e4-129">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fa3e4-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="fa3e4-131">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="fa3e4-132">Поддержка проверки подлинности для изолированных приложений обеспечивается посредством OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="fa3e4-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="fa3e4-133">Метод <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="fa3e4-134">Значения, необходимые для настройки приложения, можно получить от поставщика удостоверений, совместимого с OIDC.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="fa3e4-135">Получите эти значения при регистрации приложения, которая обычно производится на веб-портале.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="fa3e4-136">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="fa3e4-136">Access token scopes</span></span>

<span data-ttu-id="fa3e4-137">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="fa3e4-138">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера по умолчанию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-138">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="fa3e4-139">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-139">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="fa3e4-140">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="fa3e4-140">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="fa3e4-141">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="fa3e4-141">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="fa3e4-142">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="fa3e4-142">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="fa3e4-143">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="fa3e4-143">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="fa3e4-144">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="fa3e4-144">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fa3e4-145">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fa3e4-145">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fa3e4-146">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="fa3e4-146">LoginDisplay component</span></span>

<span data-ttu-id="fa3e4-147">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-147">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="fa3e4-148">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="fa3e4-148">For authenticated users:</span></span>
  * <span data-ttu-id="fa3e4-149">отображает имя текущего пользователя;</span><span class="sxs-lookup"><span data-stu-id="fa3e4-149">Displays the current username.</span></span>
  * <span data-ttu-id="fa3e4-150">отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-150">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="fa3e4-151">Для анонимных пользователей предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="fa3e4-151">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a><span data-ttu-id="fa3e4-152">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fa3e4-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fa3e4-153">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fa3e4-153">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="fa3e4-154">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="fa3e4-154">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
