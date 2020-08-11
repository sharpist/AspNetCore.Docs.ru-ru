---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: d3611e26414f5dd1103ca9af15b17f9813023e21
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818863"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="45ee8-102">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью библиотеки проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="45ee8-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="45ee8-103">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="45ee8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="45ee8-104">*При использовании Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этой статье. См. статьи, посвященные AAD и AAD B2C, в этом разделе оглавления.*</span><span class="sxs-lookup"><span data-stu-id="45ee8-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="45ee8-105">Чтобы создать изолированное приложение Blazor WebAssembly, которое использует библиотеку [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/), выполните рекомендации с учетом используемых средств.</span><span class="sxs-lookup"><span data-stu-id="45ee8-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="45ee8-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45ee8-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="45ee8-107">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="45ee8-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="45ee8-108">Выбрав шаблон **Приложение Blazor WebAssembly** в диалоговом окне **Создание веб-приложения ASP.NET Core**, щелкните **Изменить** в разделе **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="45ee8-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="45ee8-109">Выберите **Учетные записи отдельных пользователей** с параметром **Хранить учетные записи пользователей в приложении**, чтобы хранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="45ee8-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="45ee8-110">Visual Studio Code или .NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="45ee8-110">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="45ee8-111">Создайте новый проект Blazor WebAssembly с механизмом аутентификации в пустой папке.</span><span class="sxs-lookup"><span data-stu-id="45ee8-111">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="45ee8-112">Задайте механизм аутентификации `Individual` с параметром `-au|--auth`, чтобы сохранить пользователей в приложении с помощью системы [Identity](xref:security/authentication/identity) ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="45ee8-112">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="45ee8-113">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="45ee8-113">Placeholder</span></span>  | <span data-ttu-id="45ee8-114">Пример</span><span class="sxs-lookup"><span data-stu-id="45ee8-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="45ee8-115">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="45ee8-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="45ee8-116">См. сведения о команде [`dotnet new`](/dotnet/core/tools/dotnet-new) в руководстве по .NET Core.</span><span class="sxs-lookup"><span data-stu-id="45ee8-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="45ee8-117">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="45ee8-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="45ee8-118">Чтобы создать новый проект Blazor WebAssembly с механизмом аутентификации, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="45ee8-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="45ee8-119">На шаге **Настройка нового приложения Blazor WebAssembly** выберите **Индивидуальная проверка подлинности (в приложении)** из раскрывающегося списка **Проверка подлинности**.</span><span class="sxs-lookup"><span data-stu-id="45ee8-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="45ee8-120">Приложение будет создано для отдельных пользователей, хранимых в приложении, с помощью [Identity](xref:security/authentication/identity) ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="45ee8-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="45ee8-121">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="45ee8-121">Authentication package</span></span>

<span data-ttu-id="45ee8-122">Когда приложение создается для использования отдельных учетных записей пользователей, оно автоматически получает ссылку на пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="45ee8-122">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="45ee8-123">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="45ee8-123">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="45ee8-124">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="45ee8-124">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="45ee8-125">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="45ee8-125">Authentication service support</span></span>

<span data-ttu-id="45ee8-126">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>, предоставляемого в пакете [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="45ee8-126">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="45ee8-127">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="45ee8-127">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="45ee8-128">`Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="45ee8-128">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="45ee8-129">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="45ee8-129">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="45ee8-130">Поддержка проверки подлинности для изолированных приложений обеспечивается посредством OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="45ee8-130">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="45ee8-131">Метод <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="45ee8-131">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="45ee8-132">Значения, необходимые для настройки приложения, можно получить от поставщика удостоверений, совместимого с OIDC.</span><span class="sxs-lookup"><span data-stu-id="45ee8-132">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="45ee8-133">Получите эти значения при регистрации приложения, которая обычно производится на веб-портале.</span><span class="sxs-lookup"><span data-stu-id="45ee8-133">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="45ee8-134">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="45ee8-134">Access token scopes</span></span>

<span data-ttu-id="45ee8-135">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="45ee8-135">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="45ee8-136">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера по умолчанию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="45ee8-136">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="45ee8-137">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="45ee8-137">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="45ee8-138">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="45ee8-138">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="45ee8-139">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="45ee8-139">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="45ee8-140">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="45ee8-140">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="45ee8-141">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="45ee8-141">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="45ee8-142">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="45ee8-142">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="45ee8-143">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="45ee8-143">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="45ee8-144">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="45ee8-144">LoginDisplay component</span></span>

<span data-ttu-id="45ee8-145">Компонент `LoginDisplay` (`Shared/LoginDisplay.razor`) отображается в компоненте `MainLayout` (`Shared/MainLayout.razor`) и управляет следующими поведениями.</span><span class="sxs-lookup"><span data-stu-id="45ee8-145">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="45ee8-146">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="45ee8-146">For authenticated users:</span></span>
  * <span data-ttu-id="45ee8-147">отображает имя текущего пользователя;</span><span class="sxs-lookup"><span data-stu-id="45ee8-147">Displays the current username.</span></span>
  * <span data-ttu-id="45ee8-148">отображает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="45ee8-148">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="45ee8-149">Для анонимных пользователей предоставляет возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="45ee8-149">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="45ee8-150">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="45ee8-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="45ee8-151">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="45ee8-151">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="45ee8-152">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="45ee8-152">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
