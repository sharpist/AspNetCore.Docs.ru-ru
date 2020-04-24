---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью библиотеки проверки подлинности
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110957"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="ad8c5-102">Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью библиотеки проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ad8c5-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="ad8c5-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ad8c5-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="ad8c5-104">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="ad8c5-105">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="ad8c5-106">*Для Azure Active Directory (AAD) и Azure Active Directory B2C (AAD B2C) не следуйте указаниям в этом разделе. См. разделы, посвященные AAD и AAD B2C, в этом узле оглавления.*</span><span class="sxs-lookup"><span data-stu-id="ad8c5-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="ad8c5-107">Чтобы создать Blazor изолированное приложение для сборки, использующее `Microsoft.AspNetCore.Components.WebAssembly.Authentication` библиотеку, выполните в командной оболочке следующую команду:</span><span class="sxs-lookup"><span data-stu-id="ad8c5-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="ad8c5-108">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="ad8c5-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ad8c5-109">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="ad8c5-110">В Visual Studio [ Blazor Создайте приложение сборки](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="ad8c5-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="ad8c5-111">Настройте **проверку подлинности** для **отдельных учетных записей пользователей** с помощью параметра **сохранить учетные записи пользователей в приложении** .</span><span class="sxs-lookup"><span data-stu-id="ad8c5-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="ad8c5-112">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ad8c5-112">Authentication package</span></span>

<span data-ttu-id="ad8c5-113">Когда приложение создается для использования отдельных учетных записей пользователей, приложение автоматически получает ссылку `Microsoft.AspNetCore.Components.WebAssembly.Authentication` на пакет в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="ad8c5-114">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ad8c5-115">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="ad8c5-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="ad8c5-116">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ad8c5-117">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ad8c5-117">Authentication service support</span></span>

<span data-ttu-id="ad8c5-118">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddOidcAuthentication` помощью метода расширения, предоставленного `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакетом.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="ad8c5-119">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).</span><span class="sxs-lookup"><span data-stu-id="ad8c5-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ad8c5-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ad8c5-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="ad8c5-121">Поддержка проверки подлинности для автономных приложений предлагается с помощью Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="ad8c5-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="ad8c5-122">`AddOidcAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения с помощью OIDC.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="ad8c5-123">Значения, необходимые для настройки приложения, можно получить из IP-адреса, совместимого с OIDC.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="ad8c5-124">Получите значения при регистрации приложения, которое обычно происходит на веб-портале.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="ad8c5-125">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="ad8c5-125">Access token scopes</span></span>

<span data-ttu-id="ad8c5-126">Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ad8c5-127">Чтобы создать маркер в рамках потока входа, добавьте область в области токенов по умолчанию `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="ad8c5-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="ad8c5-128">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="ad8c5-129">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="ad8c5-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="ad8c5-130">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="ad8c5-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="ad8c5-131">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="ad8c5-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="ad8c5-132">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="ad8c5-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ad8c5-133">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="ad8c5-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="ad8c5-134">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="ad8c5-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ad8c5-135">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="ad8c5-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ad8c5-136">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="ad8c5-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="ad8c5-137">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="ad8c5-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ad8c5-138">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ad8c5-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 