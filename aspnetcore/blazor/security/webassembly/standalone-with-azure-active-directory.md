---
title: Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory
author: guardrex
description: Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 50aed0e0bc9058e54518512b9570ca0fdcde9b4e
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712432"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="00c19-103">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="00c19-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="00c19-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="00c19-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="00c19-105">Чтобы создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности, выполните приведенные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="00c19-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="00c19-106">[Создайте клиент AAD и веб-приложение](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="00c19-106">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="00c19-107">Зарегистрируйте приложение AAD на портале Azure в разделе \*\*\*\*Azure Active Directory\*\* > Регистрация приложений\*\*.</span><span class="sxs-lookup"><span data-stu-id="00c19-107">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="00c19-108">Укажите **имя** приложения (например, **BlazorИзолированный AAD**).</span><span class="sxs-lookup"><span data-stu-id="00c19-108">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="00c19-109">Выберите **поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="00c19-109">Choose a **Supported account types**.</span></span> <span data-ttu-id="00c19-110">Можно выбрать **Учетные записи только в этом каталоге организации**.</span><span class="sxs-lookup"><span data-stu-id="00c19-110">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="00c19-111">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="00c19-111">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="00c19-112">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="00c19-112">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="00c19-113">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="00c19-113">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="00c19-114">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="00c19-114">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="00c19-115">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="00c19-115">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="00c19-116">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="00c19-116">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="00c19-117">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="00c19-117">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="00c19-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="00c19-118">Select **Register**.</span></span>

<span data-ttu-id="00c19-119">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="00c19-119">Record the following information:</span></span>

* <span data-ttu-id="00c19-120">Идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="00c19-120">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="00c19-121">Идентификатор каталога (клиента) (например, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`).</span><span class="sxs-lookup"><span data-stu-id="00c19-121">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="00c19-122">В разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="00c19-122">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="00c19-123">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="00c19-123">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="00c19-124">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="00c19-124">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="00c19-125">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="00c19-125">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="00c19-126">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="00c19-126">Select the **Save** button.</span></span>

<span data-ttu-id="00c19-127">Создайте приложение в пустой папке.</span><span class="sxs-lookup"><span data-stu-id="00c19-127">Create the app in an empty folder.</span></span> <span data-ttu-id="00c19-128">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="00c19-128">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="00c19-129">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="00c19-129">Placeholder</span></span>   | <span data-ttu-id="00c19-130">Название на портале Azure</span><span class="sxs-lookup"><span data-stu-id="00c19-130">Azure portal name</span></span>       | <span data-ttu-id="00c19-131">Пример</span><span class="sxs-lookup"><span data-stu-id="00c19-131">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="00c19-132">Идентификатор приложения (клиента)</span><span class="sxs-lookup"><span data-stu-id="00c19-132">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="00c19-133">Идентификатор каталога (клиента)</span><span class="sxs-lookup"><span data-stu-id="00c19-133">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="00c19-134">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="00c19-134">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="00c19-135">На портале Azure в разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** > **URI перенаправления** в качестве порта для приложений, выполняющихся на сервере Kestrel с параметрами по умолчанию, задан порт 5001.</span><span class="sxs-lookup"><span data-stu-id="00c19-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="00c19-136">Если приложение выполняется на случайном порту IIS Express, порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="00c19-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="00c19-137">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="00c19-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="00c19-138">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="00c19-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="00c19-139">Входить в приложение, используя учетную запись пользователя AAD.</span><span class="sxs-lookup"><span data-stu-id="00c19-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="00c19-140">Запрашивать маркеры доступа для API Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="00c19-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="00c19-141">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="00c19-141">For more information, see:</span></span>
  * [<span data-ttu-id="00c19-142">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="00c19-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="00c19-143">[Краткое руководство. Настройка приложения для предоставления интерфейсов веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="00c19-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="00c19-144">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="00c19-144">Authentication package</span></span>

<span data-ttu-id="00c19-145">Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="00c19-145">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="00c19-146">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="00c19-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="00c19-147">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="00c19-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="00c19-148">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="00c19-148">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="00c19-149">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.</span><span class="sxs-lookup"><span data-stu-id="00c19-149">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="00c19-150">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="00c19-150">Authentication service support</span></span>

<span data-ttu-id="00c19-151">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="00c19-151">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="00c19-152">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="00c19-152">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="00c19-153">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="00c19-153">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="00c19-154">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="00c19-154">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="00c19-155">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="00c19-155">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="00c19-156">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="00c19-156">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="00c19-157">Пример.</span><span class="sxs-lookup"><span data-stu-id="00c19-157">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="00c19-158">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="00c19-158">Access token scopes</span></span>

<span data-ttu-id="00c19-159">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="00c19-159">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="00c19-160">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера доступа по умолчанию <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="00c19-160">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="00c19-161">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="00c19-161">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="00c19-162">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="00c19-162">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="00c19-163">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="00c19-163">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="00c19-164">Режим входа</span><span class="sxs-lookup"><span data-stu-id="00c19-164">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="00c19-165">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="00c19-165">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="00c19-166">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="00c19-166">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="00c19-167">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="00c19-167">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="00c19-168">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="00c19-168">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="00c19-169">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="00c19-169">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="00c19-170">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="00c19-170">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="00c19-171">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="00c19-171">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="00c19-172">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="00c19-172">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="00c19-173">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="00c19-173">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
