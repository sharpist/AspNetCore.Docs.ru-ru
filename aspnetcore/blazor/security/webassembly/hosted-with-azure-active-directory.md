---
title: 'Защита размещенного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью Azure Active Directory'
author: guardrex
description: 'Узнайте о том, как защитить размещенное приложение ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 17f96be762ece8c59577445eb2ae630a8ee3b3dd
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234482"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a7b88-103">Защита размещенного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a7b88-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a7b88-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="a7b88-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a7b88-105">В этой статье описывается, как создать [размещенное приложение :::no-loc(Blazor WebAssembly):::](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a7b88-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a7b88-106">Для приложений :::no-loc(Blazor WebAssembly):::, созданных в Visual Studio, которые настроены для поддержки учетных записей в каталоге AAD организации, Visual Studio неправильно настраивает приложение при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="a7b88-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="a7b88-107">Эта проблема будет устранена в следующем выпуске Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a7b88-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="a7b88-108">В этой статье показано, как создать приложение с помощью команды `dotnet new` в .NET Core CLI.</span><span class="sxs-lookup"><span data-stu-id="a7b88-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="a7b88-109">Если вы предпочитаете создать приложение в среде Visual Studio до ее обновления до последних шаблонов :::no-loc(Blazor)::: в ASP.NET Core 5.0, ознакомьтесь с каждым из разделов этой статьи и подтвердите или обновите конфигурацию приложения после того, как Visual Studio создаст приложение.</span><span class="sxs-lookup"><span data-stu-id="a7b88-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="a7b88-110">Регистрация приложений в AAD и создание решения</span><span class="sxs-lookup"><span data-stu-id="a7b88-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a7b88-111">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="a7b88-111">Create a tenant</span></span>

<span data-ttu-id="a7b88-112">Следуйте инструкциям в разделе [Краткое руководство. Настройка клиента](/azure/active-directory/develop/quickstart-create-new-tenant), чтобы создать клиент в AAD.</span><span class="sxs-lookup"><span data-stu-id="a7b88-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a7b88-113">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="a7b88-113">Register a server API app</span></span>

<span data-ttu-id="a7b88-114">Следуйте инструкциям в разделе [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделах, посвященных Azure AAD, чтобы зарегистрировать приложение AAD для *приложения API сервера* , а затем выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="a7b88-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="a7b88-115">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-115">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="a7b88-116">Укажите **имя** приложения (например, **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a7b88-116">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="a7b88-117">Выберите **поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="a7b88-118">Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="a7b88-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a7b88-119">В этом сценарии *приложению API сервера* не требуется **URI перенаправления** , поэтому в раскрывающемся списке оставьте значение **Интернет** и не вводите URI-перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a7b88-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a7b88-120">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a7b88-121">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-121">Select **Register**.</span></span>

<span data-ttu-id="a7b88-122">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="a7b88-122">Record the following information:</span></span>

* <span data-ttu-id="a7b88-123">Идентификатор *приложения API сервера* (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a7b88-124">Идентификатор каталога (клиента) (например, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="a7b88-125">Основной домен AAD/домен издателя/домен клиента (например, `contoso.onmicrosoft.com`). домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a7b88-126">В разделе **Разрешения API** удалите разрешение **Microsoft Graph** > **User.Read** , поскольку приложению не требуется вход или доступ к профилю пользователя.</span><span class="sxs-lookup"><span data-stu-id="a7b88-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="a7b88-127">В разделе **Предоставление API** :</span><span class="sxs-lookup"><span data-stu-id="a7b88-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="a7b88-128">Нажмите **Добавить группу**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a7b88-129">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a7b88-130">Укажите значение в поле **Имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a7b88-131">Укажите значение в поле **Отображаемое имя согласия администратора** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a7b88-132">Укажите значение в поле **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a7b88-133">Убедитесь в том, что параметру **Состояние** присвоено значение **Включено**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a7b88-134">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-134">Select **Add scope**.</span></span>

<span data-ttu-id="a7b88-135">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="a7b88-135">Record the following information:</span></span>

* <span data-ttu-id="a7b88-136">URI идентификатора приложения (например, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` или предоставленное вами значение);</span><span class="sxs-lookup"><span data-stu-id="a7b88-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="a7b88-137">Имя области (например, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="a7b88-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a7b88-138">зарегистрируете клиентское приложение;</span><span class="sxs-lookup"><span data-stu-id="a7b88-138">Register a client app</span></span>

<span data-ttu-id="a7b88-139">Следуйте инструкциям в разделе [Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделах, посвященных Azure AAD, чтобы зарегистрировать приложение AAD для приложения *`Client`* , а затем выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="a7b88-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a7b88-140">В разделе **Azure Active Directory**  > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-140">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="a7b88-141">Укажите **имя** приложения (например, **:::no-loc(Blazor):::Клиентский AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a7b88-141">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="a7b88-142">Выберите **поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="a7b88-143">Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="a7b88-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a7b88-144">Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a7b88-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a7b88-145">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="a7b88-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a7b88-146">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a7b88-147">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения *`Server`* на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a7b88-148">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a7b88-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a7b88-149">В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a7b88-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a7b88-150">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a7b88-151">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-151">Select **Register**.</span></span>

<span data-ttu-id="a7b88-152">Запишите идентификатор приложения *`Client`* (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a7b88-153">В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="a7b88-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="a7b88-154">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a7b88-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a7b88-155">В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.</span><span class="sxs-lookup"><span data-stu-id="a7b88-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a7b88-156">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a7b88-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a7b88-157">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a7b88-158">В разделе **Azure Active Directory**  > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-158">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="a7b88-159">Укажите **имя** приложения (например, **:::no-loc(Blazor):::Клиентский AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a7b88-159">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="a7b88-160">Выберите **поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="a7b88-161">Можно выбрать **Учетные записи только в этом каталоге организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="a7b88-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a7b88-162">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a7b88-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a7b88-163">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="a7b88-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a7b88-164">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a7b88-165">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения *`Server`* на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a7b88-166">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a7b88-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a7b88-167">В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a7b88-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a7b88-168">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a7b88-169">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-169">Select **Register**.</span></span>

<span data-ttu-id="a7b88-170">Запишите идентификатор приложения *`Client`* (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a7b88-171">В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="a7b88-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="a7b88-172">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a7b88-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a7b88-173">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a7b88-174">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a7b88-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a7b88-175">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a7b88-176">В разделе **Разрешения API** выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="a7b88-176">In **API permissions** :</span></span>

1. <span data-ttu-id="a7b88-177">Убедитесь, что у приложения имеется разрешение **Microsoft Graph** > **User.Read**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a7b88-178">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a7b88-179">В столбце *Имя* выберите **Приложение API сервера** (например, **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a7b88-179">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="a7b88-180">Откройте список **API**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-180">Open the **API** list.</span></span>
1. <span data-ttu-id="a7b88-181">Разрешите доступ к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a7b88-182">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a7b88-183">Нажмите кнопку **Предоставить согласие администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="a7b88-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="a7b88-184">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a7b88-185">Создание приложения</span><span class="sxs-lookup"><span data-stu-id="a7b88-185">Create the app</span></span>

<span data-ttu-id="a7b88-186">В пустой папке замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="a7b88-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="a7b88-187">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="a7b88-187">Placeholder</span></span>                  | <span data-ttu-id="a7b88-188">Название на портале Azure</span><span class="sxs-lookup"><span data-stu-id="a7b88-188">Azure portal name</span></span>                                     | <span data-ttu-id="a7b88-189">Пример</span><span class="sxs-lookup"><span data-stu-id="a7b88-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `:::no-loc(Blazor):::Sample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="a7b88-190">Идентификатор приложения (клиента) для приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="a7b88-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="a7b88-191">Имя области</span><span class="sxs-lookup"><span data-stu-id="a7b88-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="a7b88-192">Идентификатор приложения (клиента) для *приложения API сервера*</span><span class="sxs-lookup"><span data-stu-id="a7b88-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="a7b88-193">универсальный код ресурса (URI) идентификатора приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="a7b88-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="a7b88-194">Основной домен/домен издателя/домен клиента</span><span class="sxs-lookup"><span data-stu-id="a7b88-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="a7b88-195">Идентификатор каталога (клиента)</span><span class="sxs-lookup"><span data-stu-id="a7b88-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="a7b88-196">&dagger;Шаблон :::no-loc(Blazor WebAssembly)::: автоматически добавляет схему `api://` к аргументу URI идентификатора приложения, переданному в команде `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="a7b88-196">&dagger;The :::no-loc(Blazor WebAssembly)::: template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="a7b88-197">Если при предоставлении URI идентификатора приложения для заполнителя `{SERVER API APP ID URI}` используется схема `api://`, удалите эту схему (`api://`) из аргумента, как показано в примере значения в предыдущей таблице.</span><span class="sxs-lookup"><span data-stu-id="a7b88-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="a7b88-198">Если URI идентификатора приложения является пользовательским значением или имеет какую-то другую схему (например, `https://` для домена недоверенного издателя, подобного `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), необходимо вручную обновить URI области по умолчанию и удалить схему `api://` после того, как приложение *`Client`* будет создано на основе шаблона.</span><span class="sxs-lookup"><span data-stu-id="a7b88-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="a7b88-199">Дополнительные сведения см. в примечании в разделе [Области маркеров доступа](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="a7b88-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="a7b88-200">Возможно, шаблон :::no-loc(Blazor WebAssembly)::: будет изменен в следующем выпуске ASP.NET Core с учетом таких сценариев.</span><span class="sxs-lookup"><span data-stu-id="a7b88-200">The :::no-loc(Blazor WebAssembly)::: template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="a7b88-201">Дополнительные сведения см. в статье [Двойная схема URI идентификатора приложения с шаблоном WASM :::no-loc(Blazor)::: (размещенное решение для одной организации) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="a7b88-201">For more information, see [Double scheme for App ID URI with :::no-loc(Blazor)::: WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="a7b88-202">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a7b88-203">Может потребоваться изменить конфигурацию при использовании клиента Azure с непроверенным доменом издателя, как описано в разделе [Параметры приложения](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="a7b88-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a7b88-204">Может потребоваться изменить конфигурацию при использовании клиента Azure с непроверенным доменом издателя, как описано в разделе [Области маркеров доступа](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="a7b88-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a7b88-205">На портале Azure **URI перенаправления** в конфигурации платформы приложения *`Client`* настроен для использования порта 5001, предназначенного для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a7b88-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a7b88-206">Если приложение *`Client`* выполняется на случайном порту IIS Express, порт для приложения можно просмотреть в свойствах *приложения API Server* в области **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a7b88-207">Если порт не был настроен ранее с использованием известного порта приложения *`Client`* , вернитесь к регистрации приложения *`Client`* на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="a7b88-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a7b88-208">Конфигурация приложения *`Server`*</span><span class="sxs-lookup"><span data-stu-id="a7b88-208">*`Server`* app configuration</span></span>

<span data-ttu-id="a7b88-209">*Этот раздел относится к приложению **`Server`** решения.*</span><span class="sxs-lookup"><span data-stu-id="a7b88-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a7b88-210">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a7b88-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7b88-211">Поддержка аутентификации и авторизации для вызовов веб-API ASP.NET Core на платформе :::no-loc(Identity)::: Майкрософт предоставляется следующими пакетами:</span><span class="sxs-lookup"><span data-stu-id="a7b88-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft :::no-loc(Identity)::: Platform is provided by the following packages:</span></span>

* [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web)
* [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)

```xml
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="a7b88-212">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="a7b88-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a7b88-213">Поддержка проверки подлинности и авторизации вызовов веб-API ASP.NET Core предоставляется пакетом [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="a7b88-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="a7b88-214">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="a7b88-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="a7b88-215">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a7b88-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7b88-216">Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a7b88-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a7b88-217">Метод <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> настраивает службы для защиты веб-API с помощью платформы :::no-loc(Identity)::: Майкрософт версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="a7b88-217">The <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> method configures services to protect the web API with Microsoft :::no-loc(Identity)::: Platform v2.0.</span></span> <span data-ttu-id="a7b88-218">Этот метод ожидает получить раздел `AzureAd` в конфигурации приложения, где настроены параметры для инициализации методов аутентификации.</span><span class="sxs-lookup"><span data-stu-id="a7b88-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a7b88-219">Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a7b88-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a7b88-220">Метод <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, создаваемых Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="a7b88-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="a7b88-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> позволяют обеспечить, что:</span><span class="sxs-lookup"><span data-stu-id="a7b88-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="a7b88-222">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="a7b88-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a7b88-223">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="a7b88-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="a7b88-224">User.:::no-loc(Identity):::.Name</span><span class="sxs-lookup"><span data-stu-id="a7b88-224">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="a7b88-225">По умолчанию API приложения *`Server`* заполняет `User.:::no-loc(Identity):::.Name` значением из типа утверждения `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (например, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-225">By default, the *`Server`* app API populates `User.:::no-loc(Identity):::.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="a7b88-226">Чтобы настроить приложение на получение значения из типа утверждения `name`, настройте <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a7b88-226">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a7b88-227">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="a7b88-227">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a7b88-228">В файле `:::no-loc(appsettings.json):::` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="a7b88-228">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="a7b88-229">Пример.</span><span class="sxs-lookup"><span data-stu-id="a7b88-229">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a7b88-230">В файле `:::no-loc(appsettings.json):::` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="a7b88-230">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="a7b88-231">Пример.</span><span class="sxs-lookup"><span data-stu-id="a7b88-231">Example:</span></span>

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

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="a7b88-232">Контроллер WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="a7b88-232">WeatherForecast controller</span></span>

<span data-ttu-id="a7b88-233">Контроллер WeatherForecast ( *Controllers/WeatherForecastController.cs* ) предоставляет защищенный API с атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="a7b88-233">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="a7b88-234">**Важно** понять следующее:</span><span class="sxs-lookup"><span data-stu-id="a7b88-234">It's **important** to understand that:</span></span>

* <span data-ttu-id="a7b88-235">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="a7b88-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a7b88-236">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), используемый в приложении :::no-loc(Blazor WebAssembly):::, служит подсказкой для приложения о том, что пользователь должен пройти авторизацию, чтобы приложение работало правильно.</span><span class="sxs-lookup"><span data-stu-id="a7b88-236">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a7b88-237">Конфигурация приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="a7b88-237">*`Client`* app configuration</span></span>

<span data-ttu-id="a7b88-238">*Этот раздел относится к приложению **`Client`** решения.*</span><span class="sxs-lookup"><span data-stu-id="a7b88-238">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a7b88-239">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a7b88-239">Authentication package</span></span>

<span data-ttu-id="a7b88-240">Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="a7b88-240">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a7b88-241">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="a7b88-241">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a7b88-242">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="a7b88-242">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a7b88-243">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="a7b88-243">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a7b88-244">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.</span><span class="sxs-lookup"><span data-stu-id="a7b88-244">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a7b88-245">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a7b88-245">Authentication service support</span></span>

<span data-ttu-id="a7b88-246">Добавлена поддержка экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="a7b88-246">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a7b88-247">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7b88-247">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a7b88-248">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `:::no-loc(Blazor):::Sample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-248">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="a7b88-249">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="a7b88-249">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a7b88-250">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком :::no-loc(Identity)::: (IP).</span><span class="sxs-lookup"><span data-stu-id="a7b88-250">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="a7b88-251">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a7b88-251">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a7b88-252">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-252">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a7b88-253">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-253">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a7b88-254">Конфигурация предоставлена в файле `wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="a7b88-254">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a7b88-255">Пример.</span><span class="sxs-lookup"><span data-stu-id="a7b88-255">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a7b88-256">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="a7b88-256">Access token scopes</span></span>

<span data-ttu-id="a7b88-257">Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:</span><span class="sxs-lookup"><span data-stu-id="a7b88-257">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a7b88-258">включены по умолчанию в запросе на вход;</span><span class="sxs-lookup"><span data-stu-id="a7b88-258">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a7b88-259">используются для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a7b88-259">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a7b88-260">Все области должны входить в одно и то же приложение для каждого правила Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a7b88-260">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a7b88-261">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="a7b88-261">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a7b88-262">Шаблон :::no-loc(Blazor WebAssembly)::: автоматически добавляет схему `api://` к аргументу URI идентификатора приложения, переданному в команде `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="a7b88-262">The :::no-loc(Blazor WebAssembly)::: template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="a7b88-263">При создании приложения на основе шаблона проекта :::no-loc(Blazor)::: убедитесь, что для области маркера доступа по умолчанию указано правильное значение URI идентификатора пользовательского приложения, указанное на портале Azure, или значение в **одном** из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="a7b88-263">When generating an app from the :::no-loc(Blazor)::: project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="a7b88-264">Если домен издателя каталога является **доверенным** , то для области маркера доступа по умолчанию, как правило, задано значение, аналогичное приведенному в следующем примере, где `API.Access` — это имя области по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="a7b88-264">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="a7b88-265">Проверьте значение двойной схемы (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-265">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="a7b88-266">При наличии двойной схемы удалите первую схему `api://` из значения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-266">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="a7b88-267">Если домен издателя каталога является **недоверенным** , то для области маркера доступа по умолчанию, как правило, задано значение, аналогичное приведенному в следующем примере, где `API.Access` — это имя области по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="a7b88-267">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="a7b88-268">Проверьте значение на наличие дополнительной схемы `api://` (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="a7b88-268">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="a7b88-269">При наличии дополнительной схемы `api://` удалите схему `api://` из значения.</span><span class="sxs-lookup"><span data-stu-id="a7b88-269">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="a7b88-270">Возможно, шаблон :::no-loc(Blazor WebAssembly)::: будет изменен в следующем выпуске ASP.NET Core с учетом таких сценариев.</span><span class="sxs-lookup"><span data-stu-id="a7b88-270">The :::no-loc(Blazor WebAssembly)::: template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="a7b88-271">Дополнительные сведения см. в статье [Двойная схема URI идентификатора приложения с шаблоном WASM :::no-loc(Blazor)::: (размещенное решение для одной организации) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="a7b88-271">For more information, see [Double scheme for App ID URI with :::no-loc(Blazor)::: WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="a7b88-272">Укажите дополнительные области с помощью `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="a7b88-272">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="a7b88-273">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="a7b88-273">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a7b88-274">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="a7b88-274">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a7b88-275">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="a7b88-275">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="a7b88-276">Режим входа</span><span class="sxs-lookup"><span data-stu-id="a7b88-276">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="a7b88-277">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="a7b88-277">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a7b88-278">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="a7b88-278">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a7b88-279">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="a7b88-279">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a7b88-280">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a7b88-280">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a7b88-281">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a7b88-281">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a7b88-282">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a7b88-282">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a7b88-283">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="a7b88-283">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a7b88-284">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="a7b88-284">Run the app</span></span>

<span data-ttu-id="a7b88-285">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="a7b88-285">Run the app from the Server project.</span></span> <span data-ttu-id="a7b88-286">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="a7b88-286">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a7b88-287">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-287">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a7b88-288">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="a7b88-288">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a7b88-289">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a7b88-289">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a7b88-290">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a7b88-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a7b88-291">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="a7b88-291">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
