---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью учетных записей Майкрософт
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 7114cf60639c7fdff9fb9d5713dd524f3cf27a96
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103492"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="2c144-102">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="2c144-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="2c144-103">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="2c144-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2c144-104">Чтобы создать изолированное приложение Blazor WebAssembly, использующее [учетные записи Майкрософт с Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) для проверки подлинности, выполните приведенные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="2c144-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="2c144-105">Создайте клиент AAD и веб-приложение</span><span class="sxs-lookup"><span data-stu-id="2c144-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="2c144-106">Зарегистрируйте приложение AAD на портале Azure в разделе **Azure Active Directory** > **Регистрация приложений**.</span><span class="sxs-lookup"><span data-stu-id="2c144-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="2c144-107">Укажите **имя** приложения (например, **Изолированное приложение Blazor с учетными записями Майкрософт в AAD**).</span><span class="sxs-lookup"><span data-stu-id="2c144-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="2c144-108">В разделе **Поддерживаемые типы учетных записей** выберите **Учетные записи в любом каталоге организации**.</span><span class="sxs-lookup"><span data-stu-id="2c144-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="2c144-109">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="2c144-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2c144-110">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="2c144-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2c144-111">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="2c144-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2c144-112">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="2c144-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2c144-113">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2c144-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2c144-114">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2c144-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2c144-115">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="2c144-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="2c144-116">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="2c144-116">Select **Register**.</span></span>

<span data-ttu-id="2c144-117">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="2c144-117">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="2c144-118">В разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2c144-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="2c144-119">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="2c144-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2c144-120">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="2c144-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="2c144-121">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2c144-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2c144-122">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="2c144-122">Select the **Save** button.</span></span>

<span data-ttu-id="2c144-123">Создайте приложение.</span><span class="sxs-lookup"><span data-stu-id="2c144-123">Create the app.</span></span> <span data-ttu-id="2c144-124">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="2c144-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="2c144-125">Чтобы указать расположение выходных данных для создания папки проекта, если она не существует, включите в команду параметр выходных данных с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="2c144-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2c144-126">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="2c144-126">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="2c144-127">На портале Azure в разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** > **URI перенаправления** в качестве порта для приложений, выполняющихся на сервере Kestrel с параметрами по умолчанию, задан порт 5001.</span><span class="sxs-lookup"><span data-stu-id="2c144-127">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="2c144-128">Если приложение выполняется на случайном порту IIS Express, порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="2c144-128">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="2c144-129">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="2c144-129">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="2c144-130">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="2c144-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="2c144-131">выполнять вход в приложение, используя учетную запись Майкрософт;</span><span class="sxs-lookup"><span data-stu-id="2c144-131">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="2c144-132">запрашивать маркеры доступа для API Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="2c144-132">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="2c144-133">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="2c144-133">For more information, see:</span></span>
  * [<span data-ttu-id="2c144-134">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="2c144-134">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="2c144-135">[Краткое руководство. Настройка приложения для предоставления интерфейсов веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="2c144-135">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="2c144-136">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2c144-136">Authentication package</span></span>

<span data-ttu-id="2c144-137">Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="2c144-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="2c144-138">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="2c144-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2c144-139">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="2c144-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="2c144-140">Пакет [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) транзитивно добавляет в приложение пакет [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="2c144-140">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="2c144-141">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2c144-141">Authentication service support</span></span>

<span data-ttu-id="2c144-142">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="2c144-142">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="2c144-143">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="2c144-143">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2c144-144">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2c144-144">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="2c144-145">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="2c144-145">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2c144-146">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="2c144-146">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="2c144-147">Конфигурация предоставляется файлом *wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2c144-147">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="2c144-148">Пример.</span><span class="sxs-lookup"><span data-stu-id="2c144-148">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="2c144-149">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="2c144-149">Access token scopes</span></span>

<span data-ttu-id="2c144-150">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="2c144-150">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="2c144-151">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера доступа по умолчанию <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="2c144-151">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="2c144-152">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="2c144-152">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2c144-153">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="2c144-153">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2c144-154">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="2c144-154">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="2c144-155">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="2c144-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="2c144-156">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="2c144-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="2c144-157">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="2c144-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="2c144-158">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2c144-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="2c144-159">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2c144-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="2c144-160">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2c144-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2c144-161">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2c144-161">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2c144-162">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2c144-162">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="2c144-163">Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="2c144-163">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="2c144-164">Краткое руководство. Настройка приложения для предоставления веб-API</span><span class="sxs-lookup"><span data-stu-id="2c144-164">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
