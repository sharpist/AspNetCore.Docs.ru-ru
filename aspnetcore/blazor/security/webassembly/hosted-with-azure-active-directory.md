---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 3a541df51a040291f390559842ecd05ba09cee8c
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243633"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="7ebf7-102">Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7ebf7-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="7ebf7-103">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="7ebf7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7ebf7-104">В этой статье описывается, как создать [размещенное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="7ebf7-105">Регистрация приложений в AAD и создание решения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-105">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="7ebf7-106">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="7ebf7-106">Create a tenant</span></span>

<span data-ttu-id="7ebf7-107">Следуйте инструкциям в разделе [Краткое руководство. Настройка клиента](/azure/active-directory/develop/quickstart-create-new-tenant), чтобы создать клиент в AAD.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="7ebf7-108">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="7ebf7-108">Register a server API app</span></span>

<span data-ttu-id="7ebf7-109">Следуйте инструкциям в разделе [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделах, посвященных Azure AAD, чтобы зарегистрировать приложение AAD для *приложения API сервера*, а затем выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="7ebf7-110">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-110">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7ebf7-111">Укажите **имя** приложения (например, **BlazorСерверный AAD**).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="7ebf7-112">Выберите **поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="7ebf7-113">Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="7ebf7-114">В этом сценарии *приложению API сервера* не требуется **URI перенаправления**, поэтому в раскрывающемся списке оставьте значение **Интернет** и не вводите URI-перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="7ebf7-115">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-115">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7ebf7-116">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-116">Select **Register**.</span></span>

<span data-ttu-id="7ebf7-117">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-117">Record the following information:</span></span>

* <span data-ttu-id="7ebf7-118">Идентификатор *приложения API сервера* (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="7ebf7-118">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="7ebf7-119">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="7ebf7-119">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="7ebf7-120">Домен клиента AAD (например, `contoso.onmicrosoft.com`): домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-120">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="7ebf7-121">В разделе **Разрешения API** удалите разрешение **Microsoft Graph** > **User.Read**, поскольку приложению не требуется вход или доступ к профилю пользователя.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-121">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="7ebf7-122">В разделе **Предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-122">In **Expose an API**:</span></span>

1. <span data-ttu-id="7ebf7-123">Нажмите **Добавить группу**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="7ebf7-124">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="7ebf7-125">Укажите значение в поле **Имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7ebf7-126">Укажите значение в поле **Отображаемое имя согласия администратора** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="7ebf7-127">Укажите значение в поле **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="7ebf7-128">Убедитесь в том, что параметру **Состояние** присвоено значение **Включено**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="7ebf7-129">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-129">Select **Add scope**.</span></span>

<span data-ttu-id="7ebf7-130">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-130">Record the following information:</span></span>

* <span data-ttu-id="7ebf7-131">URI идентификатора приложения (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111` или настраиваемое значение)</span><span class="sxs-lookup"><span data-stu-id="7ebf7-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="7ebf7-132">Область по умолчанию (например, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="7ebf7-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="7ebf7-133">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-133">Register a client app</span></span>

<span data-ttu-id="7ebf7-134">Следуйте инструкциям в разделе [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделах, посвященных Azure AAD, чтобы зарегистрировать приложение AAD для *клиентского приложения*, а затем выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-134">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="7ebf7-135">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-135">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7ebf7-136">Укажите **имя** приложения (например, **BlazorКлиентский AAD**).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-136">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="7ebf7-137">Выберите **поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-137">Choose a **Supported account types**.</span></span> <span data-ttu-id="7ebf7-138">Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-138">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="7ebf7-139">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7ebf7-140">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7ebf7-141">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7ebf7-142">Для IIS Express созданный случайным образом порт для приложения указан в свойствах серверного приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7ebf7-143">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7ebf7-144">В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7ebf7-145">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-145">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7ebf7-146">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-146">Select **Register**.</span></span>

<span data-ttu-id="7ebf7-147">Запишите идентификатор *клиентского приложения* (идентификатор клиента) (например, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-147">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="7ebf7-148">В разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7ebf7-149">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7ebf7-150">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7ebf7-151">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7ebf7-152">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-152">Select the **Save** button.</span></span>

<span data-ttu-id="7ebf7-153">В разделе **Разрешения API** выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-153">In **API permissions**:</span></span>

1. <span data-ttu-id="7ebf7-154">Убедитесь, что у приложения имеется разрешение **Microsoft Graph** > **User.Read**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-154">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="7ebf7-155">Выберите **Добавить разрешение**, а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-155">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="7ebf7-156">В столбце *Имя* выберите **Приложение API сервера** (например, **Blazor Серверный AAD**).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-156">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="7ebf7-157">Откройте список **API**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-157">Open the **API** list.</span></span>
1. <span data-ttu-id="7ebf7-158">Разрешите доступ к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-158">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="7ebf7-159">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-159">Select **Add permissions**.</span></span>
1. <span data-ttu-id="7ebf7-160">Нажмите кнопку **Предоставить согласие администратора для {ИМЯ КЛИЕНТА}** .</span><span class="sxs-lookup"><span data-stu-id="7ebf7-160">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="7ebf7-161">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-161">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="7ebf7-162">Создание приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-162">Create the app</span></span>

<span data-ttu-id="7ebf7-163">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="7ebf7-164">Чтобы указать расположение выходных данных для создания папки проекта, если она не существует, включите в команду параметр выходных данных с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7ebf7-165">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7ebf7-166">Передайте URI идентификатора приложения в параметр `app-id-uri`, но обратите внимание, что в клиентском приложении может потребоваться изменить конфигурацию, как описано в разделе [Области маркеров доступа](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

> [!NOTE]
> <span data-ttu-id="7ebf7-167">На портале Azure в разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** > **URI перенаправления** в качестве порта для приложений, выполняющихся на сервере Kestrel с параметрами по умолчанию, для *клиентского приложения* задан порт 5001.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-167">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7ebf7-168">Если *клиентское приложение* выполняется на случайном порту IIS Express, порт для приложения указан в свойствах *серверного приложения* на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-168">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7ebf7-169">Если порт не был настроен ранее с помощью известного порта *клиентского приложения*, вернитесь к регистрации *клиентского приложения* на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-169">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="7ebf7-170">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-170">Server app configuration</span></span>

<span data-ttu-id="7ebf7-171">*Этот раздел относится к приложению **`Server`** решения.*</span><span class="sxs-lookup"><span data-stu-id="7ebf7-171">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7ebf7-172">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7ebf7-172">Authentication package</span></span>

<span data-ttu-id="7ebf7-173">Поддержка проверки подлинности и авторизации вызовов веб-API ASP.NET Core предоставляется пакетом [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-173">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="7ebf7-174">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7ebf7-174">Authentication service support</span></span>

<span data-ttu-id="7ebf7-175">Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-175">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="7ebf7-176">Метод <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, создаваемых Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-176">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="7ebf7-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> позволяют обеспечить, что:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-177"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="7ebf7-178">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-178">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="7ebf7-179">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-179">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="7ebf7-180">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="7ebf7-180">User.Identity.Name</span></span>

<span data-ttu-id="7ebf7-181">По умолчанию API серверного приложения заполняет `User.Identity.Name` значением из типа утверждения `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (например, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-181">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="7ebf7-182">Чтобы настроить приложение на получение значения из типа утверждения `name`, настройте <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-182">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="7ebf7-183">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-183">App settings</span></span>

<span data-ttu-id="7ebf7-184">В файле `appsettings.json` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-184">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="7ebf7-185">Пример.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-185">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="7ebf7-186">Контроллер WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="7ebf7-186">WeatherForecast controller</span></span>

<span data-ttu-id="7ebf7-187">Контроллер WeatherForecast (*Controllers/WeatherForecastController.cs*) предоставляет защищенный API с атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-187">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="7ebf7-188">**Важно** понять следующее:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-188">It's **important** to understand that:</span></span>

* <span data-ttu-id="7ebf7-189">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-189">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="7ebf7-190">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), используемый в приложении Blazor WebAssembly, служит подсказкой для приложения, которую должен авторизовать пользователь, чтобы приложение работало правильно.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-190">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="7ebf7-191">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-191">Client app configuration</span></span>

<span data-ttu-id="7ebf7-192">*Этот раздел относится к приложению **`Client`** решения.*</span><span class="sxs-lookup"><span data-stu-id="7ebf7-192">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="7ebf7-193">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7ebf7-193">Authentication package</span></span>

<span data-ttu-id="7ebf7-194">Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-194">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="7ebf7-195">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-195">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7ebf7-196">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-196">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="7ebf7-197">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в приложение.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-197">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="7ebf7-198">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7ebf7-198">Authentication service support</span></span>

<span data-ttu-id="7ebf7-199">Добавлена поддержка экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-199">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="7ebf7-200">`Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-200">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="7ebf7-201">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-201">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="7ebf7-202">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-202">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="7ebf7-203">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="7ebf7-203">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7ebf7-204">`Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-204">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="7ebf7-205">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-205">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7ebf7-206">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-206">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="7ebf7-207">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-207">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="7ebf7-208">Пример.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-208">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="7ebf7-209">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="7ebf7-209">Access token scopes</span></span>

<span data-ttu-id="7ebf7-210">Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-210">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="7ebf7-211">включены по умолчанию в запросе на вход;</span><span class="sxs-lookup"><span data-stu-id="7ebf7-211">Included by default in the sign in request.</span></span>
* <span data-ttu-id="7ebf7-212">используются для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-212">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="7ebf7-213">Все области должны входить в одно и то же приложение для каждого правила Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-213">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="7ebf7-214">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-214">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="7ebf7-215">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="7ebf7-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7ebf7-216">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="7ebf7-216">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7ebf7-217">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="7ebf7-217">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="7ebf7-218">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="7ebf7-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="7ebf7-219">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="7ebf7-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="7ebf7-220">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="7ebf7-221">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7ebf7-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="7ebf7-222">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7ebf7-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="7ebf7-223">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7ebf7-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="7ebf7-224">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="7ebf7-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="7ebf7-225">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="7ebf7-225">Run the app</span></span>

<span data-ttu-id="7ebf7-226">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-226">Run the app from the Server project.</span></span> <span data-ttu-id="7ebf7-227">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-227">When using Visual Studio, either:</span></span>

* <span data-ttu-id="7ebf7-228">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-228">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="7ebf7-229">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="7ebf7-229">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7ebf7-230">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7ebf7-230">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7ebf7-231">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7ebf7-231">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="7ebf7-232">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="7ebf7-232">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
