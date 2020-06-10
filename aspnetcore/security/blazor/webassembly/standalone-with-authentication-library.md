---
title: Защита Blazor автономного приложения ASP.NET Coreной сборки с помощью библиотеки проверки подлинности
author: guardrex
description: ''
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: efd63c2fc533175b034b8311c2b7a6b9e6f4375b
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851131"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="01ba2-102">Защита Blazor автономного приложения ASP.NET Coreной сборки с помощью библиотеки проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="01ba2-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="01ba2-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="01ba2-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="01ba2-104">*Для Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этом разделе. См. разделы, посвященные AAD и AAD B2C, в этом узле оглавления.*</span><span class="sxs-lookup"><span data-stu-id="01ba2-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="01ba2-105">Чтобы создать Blazor изолированное приложение для веб-сборки, использующее библиотеку [Microsoft. AspNetCore. Components. Assembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) , выполните в командной оболочке следующую команду:</span><span class="sxs-lookup"><span data-stu-id="01ba2-105">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="01ba2-106">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="01ba2-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="01ba2-107">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="01ba2-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="01ba2-108">В Visual Studio [Создайте Blazor приложение сборки](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="01ba2-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="01ba2-109">Настройте **проверку подлинности** для **отдельных учетных записей пользователей** с помощью параметра **сохранить учетные записи пользователей в приложении** .</span><span class="sxs-lookup"><span data-stu-id="01ba2-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="01ba2-110">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="01ba2-110">Authentication package</span></span>

<span data-ttu-id="01ba2-111">Когда приложение создается для использования отдельных учетных записей пользователей, оно автоматически получает ссылку на пакет для пакета [Microsoft. AspNetCore. Components. веб-сборки. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="01ba2-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="01ba2-112">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="01ba2-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="01ba2-113">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="01ba2-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="01ba2-114">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="01ba2-114">Authentication service support</span></span>

<span data-ttu-id="01ba2-115">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> метода расширения, предоставляемого пакетом [Microsoft. AspNetCore. Components. WebService. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="01ba2-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="01ba2-116">Этот метод настраивает службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="01ba2-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="01ba2-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="01ba2-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="01ba2-118">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="01ba2-118">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="01ba2-119">Поддержка проверки подлинности для автономных приложений предлагается с помощью Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="01ba2-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="01ba2-120"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="01ba2-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="01ba2-121">Значения, необходимые для настройки приложения, можно получить из IP-адреса, совместимого с OIDC.</span><span class="sxs-lookup"><span data-stu-id="01ba2-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="01ba2-122">Получите значения при регистрации приложения, которое обычно происходит на веб-портале.</span><span class="sxs-lookup"><span data-stu-id="01ba2-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="01ba2-123">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="01ba2-123">Access token scopes</span></span>

<span data-ttu-id="01ba2-124">BlazorШаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="01ba2-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="01ba2-125">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области токенов по умолчанию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="01ba2-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="01ba2-126">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="01ba2-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="01ba2-127">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="01ba2-127">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="01ba2-128">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="01ba2-128">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="01ba2-129">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="01ba2-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="01ba2-130">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="01ba2-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="01ba2-131">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="01ba2-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="01ba2-132">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="01ba2-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="01ba2-133">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="01ba2-133">LoginDisplay component</span></span>

<span data-ttu-id="01ba2-134">`LoginDisplay`Компонент (*Shared/логиндисплай. Razor*) подготавливается к просмотру в `MainLayout` компоненте (*Shared/маинлайаут. Razor*) и управляет следующими поведениями:</span><span class="sxs-lookup"><span data-stu-id="01ba2-134">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="01ba2-135">Для пользователей, прошедших проверку подлинности:</span><span class="sxs-lookup"><span data-stu-id="01ba2-135">For authenticated users:</span></span>
  * <span data-ttu-id="01ba2-136">Отображает текущее имя пользователя.</span><span class="sxs-lookup"><span data-stu-id="01ba2-136">Displays the current username.</span></span>
  * <span data-ttu-id="01ba2-137">Предлагает кнопку для выхода из приложения.</span><span class="sxs-lookup"><span data-stu-id="01ba2-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="01ba2-138">Для анонимных пользователей предлагает возможность входа в систему.</span><span class="sxs-lookup"><span data-stu-id="01ba2-138">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="01ba2-139">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="01ba2-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="01ba2-140">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="01ba2-140">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="01ba2-141">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="01ba2-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
