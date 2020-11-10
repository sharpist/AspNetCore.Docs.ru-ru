---
title: 'Защита размещенного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью Azure Active Directory B2C'
author: guardrex
description: 'Узнайте о том, как защитить размещенное приложение ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью Azure Active Directory B2C.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8727fa52acbcf59549c326bd5106e5dfe23c36be
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234495"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="77234-103">Защита размещенного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="77234-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="77234-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="77234-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77234-105">В этой статье описывается, как создать [размещенное приложение :::no-loc(Blazor WebAssembly):::](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="77234-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="77234-106">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="77234-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="77234-107">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="77234-107">Create a tenant</span></span>

<span data-ttu-id="77234-108">Следуйте инструкциям в разделе [Руководство по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant), чтобы создать клиент AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="77234-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="77234-109">Запишите экземпляр AAD B2C (например, `https://contoso.b2clogin.com/` с косой чертой в конце).</span><span class="sxs-lookup"><span data-stu-id="77234-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="77234-110">Экземпляр — это схема и узел регистрации приложения Azure B2C, которую можно найти в окне **Конечные точки** на странице **Регистрации приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="77234-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="77234-111">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="77234-111">Register a server API app</span></span>

<span data-ttu-id="77234-112">Следуйте инструкциям в разделе [Руководство по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications), чтобы зарегистрировать приложение AAD для *приложения API сервера* , а затем выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="77234-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="77234-113">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="77234-113">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="77234-114">Укажите **имя** приложения (например, **:::no-loc(Blazor Server)::: AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="77234-114">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD B2C** ).</span></span>
1. <span data-ttu-id="77234-115">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом поставщике удостоверений или каталоге организации (для проверки подлинности с помощью потоков пользователей)**</span><span class="sxs-lookup"><span data-stu-id="77234-115">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="77234-116">В этом сценарии *приложению API сервера* не требуется **URI перенаправления** , поэтому в раскрывающемся списке оставьте значение **Интернет** и не вводите URI-перенаправления.</span><span class="sxs-lookup"><span data-stu-id="77234-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="77234-117">Убедитесь, что флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access** установлен.</span><span class="sxs-lookup"><span data-stu-id="77234-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="77234-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="77234-118">Select **Register**.</span></span>

<span data-ttu-id="77234-119">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="77234-119">Record the following information:</span></span>

* <span data-ttu-id="77234-120">Идентификатор *приложения API сервера* (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="77234-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="77234-121">Основной домен AAD/домен издателя/домен клиента (например, `contoso.onmicrosoft.com`). домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="77234-122">В разделе **Предоставление API** :</span><span class="sxs-lookup"><span data-stu-id="77234-122">In **Expose an API** :</span></span>

1. <span data-ttu-id="77234-123">Нажмите **Добавить группу**.</span><span class="sxs-lookup"><span data-stu-id="77234-123">Select **Add a scope**.</span></span>
1. <span data-ttu-id="77234-124">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="77234-124">Select **Save and continue**.</span></span>
1. <span data-ttu-id="77234-125">Укажите значение в поле **Имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="77234-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="77234-126">Укажите значение в поле **Отображаемое имя согласия администратора** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="77234-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="77234-127">Укажите значение в поле **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="77234-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="77234-128">Убедитесь в том, что параметру **Состояние** присвоено значение **Включено**.</span><span class="sxs-lookup"><span data-stu-id="77234-128">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="77234-129">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="77234-129">Select **Add scope**.</span></span>

<span data-ttu-id="77234-130">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="77234-130">Record the following information:</span></span>

* <span data-ttu-id="77234-131">URI идентификатора приложения (например, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` или настраиваемое значение)</span><span class="sxs-lookup"><span data-stu-id="77234-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="77234-132">Имя области (например, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="77234-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="77234-133">зарегистрируете клиентское приложение;</span><span class="sxs-lookup"><span data-stu-id="77234-133">Register a client app</span></span>

<span data-ttu-id="77234-134">Следуйте инструкциям в разделе [Руководство по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications), чтобы зарегистрировать приложение AAD для приложения *`Client`* , а затем выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="77234-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="77234-135">В разделе **Azure Active Directory**  > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="77234-135">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="77234-136">Укажите **имя** приложения (например, **:::no-loc(Blazor):::Клиентский AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="77234-136">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD B2C** ).</span></span>
1. <span data-ttu-id="77234-137">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом поставщике удостоверений или каталоге организации (для проверки подлинности с помощью потоков пользователей)**</span><span class="sxs-lookup"><span data-stu-id="77234-137">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="77234-138">Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="77234-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="77234-139">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="77234-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="77234-140">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="77234-141">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения *`Server`* на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="77234-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="77234-142">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="77234-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="77234-143">В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="77234-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="77234-144">Убедитесь, что в разделе **Разрешения** установлен флажок **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="77234-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="77234-145">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="77234-145">Select **Register**.</span></span>

1. <span data-ttu-id="77234-146">Запишите идентификатор приложения (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="77234-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="77234-147">В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="77234-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="77234-148">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="77234-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="77234-149">В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.</span><span class="sxs-lookup"><span data-stu-id="77234-149">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="77234-150">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="77234-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="77234-151">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="77234-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="77234-152">В разделе **Azure Active Directory**  > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="77234-152">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="77234-153">Укажите **имя** приложения (например, **:::no-loc(Blazor):::Клиентский AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="77234-153">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD B2C** ).</span></span>
1. <span data-ttu-id="77234-154">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом поставщике удостоверений или каталоге организации (для проверки подлинности с помощью потоков пользователей)**</span><span class="sxs-lookup"><span data-stu-id="77234-154">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="77234-155">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="77234-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="77234-156">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="77234-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="77234-157">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="77234-158">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения *`Server`* на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="77234-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="77234-159">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="77234-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="77234-160">В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="77234-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="77234-161">Убедитесь, что в разделе **Разрешения** установлен флажок **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="77234-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="77234-162">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="77234-162">Select **Register**.</span></span>

<span data-ttu-id="77234-163">Запишите идентификатор приложения (клиента) (например, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="77234-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="77234-164">В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="77234-164">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="77234-165">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="77234-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="77234-166">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="77234-166">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="77234-167">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="77234-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="77234-168">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="77234-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="77234-169">В разделе **Разрешения API** выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="77234-169">In **API permissions** :</span></span>

1. <span data-ttu-id="77234-170">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="77234-170">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="77234-171">В столбце *Имя* выберите **Приложение API сервера** (например, **:::no-loc(Blazor Server)::: AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="77234-171">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD B2C** ).</span></span>
1. <span data-ttu-id="77234-172">Откройте список **API**.</span><span class="sxs-lookup"><span data-stu-id="77234-172">Open the **API** list.</span></span>
1. <span data-ttu-id="77234-173">Разрешите доступ к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="77234-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="77234-174">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="77234-174">Select **Add permissions**.</span></span>
1. <span data-ttu-id="77234-175">Нажмите кнопку **Предоставить согласие администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="77234-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="77234-176">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="77234-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="77234-177">В разделе **Главная** > **Azure AD B2C** > **Потоки пользователя** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="77234-177">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="77234-178">Создайте поток пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="77234-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="77234-179">Чтобы заполнить `context.User.:::no-loc(Identity):::.Name` в компоненте `LoginDisplay` (`Shared/LoginDisplay.razor`), выберите хотя бы атрибут пользователя **Утверждения приложения** > **Отображаемое имя**.</span><span class="sxs-lookup"><span data-stu-id="77234-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.:::no-loc(Identity):::.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="77234-180">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="77234-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="77234-181">Создание приложения</span><span class="sxs-lookup"><span data-stu-id="77234-181">Create the app</span></span>

<span data-ttu-id="77234-182">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="77234-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="77234-183">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="77234-183">Placeholder</span></span>                   | <span data-ttu-id="77234-184">Название на портале Azure</span><span class="sxs-lookup"><span data-stu-id="77234-184">Azure portal name</span></span>                                     | <span data-ttu-id="77234-185">Пример</span><span class="sxs-lookup"><span data-stu-id="77234-185">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="77234-186">Экземпляр</span><span class="sxs-lookup"><span data-stu-id="77234-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `:::no-loc(Blazor):::Sample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="77234-187">Идентификатор приложения (клиента) для приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="77234-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="77234-188">Имя области</span><span class="sxs-lookup"><span data-stu-id="77234-188">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="77234-189">Идентификатор приложения (клиента) для *приложения API сервера*</span><span class="sxs-lookup"><span data-stu-id="77234-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="77234-190">универсальный код ресурса (URI) идентификатора приложения.&dagger;</span><span class="sxs-lookup"><span data-stu-id="77234-190">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="77234-191">Поток регистрации/входа пользователей</span><span class="sxs-lookup"><span data-stu-id="77234-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="77234-192">Основной домен/домен издателя/домен клиента</span><span class="sxs-lookup"><span data-stu-id="77234-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="77234-193">&dagger;Шаблон :::no-loc(Blazor WebAssembly)::: автоматически добавляет схему `api://` к аргументу URI идентификатора приложения, переданному в команде `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="77234-193">&dagger;The :::no-loc(Blazor WebAssembly)::: template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="77234-194">Если при предоставлении URI идентификатора приложения для заполнителя `{SERVER API APP ID URI}` используется схема `api://`, удалите эту схему (`api://`) из аргумента, как показано в примере значения в предыдущей таблице.</span><span class="sxs-lookup"><span data-stu-id="77234-194">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="77234-195">Если URI идентификатора приложения является пользовательским значением или имеет какую-то другую схему (например, `https://` для домена недоверенного издателя, подобного `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), необходимо вручную обновить URI области по умолчанию и удалить схему `api://` после того, как приложение *`Client`* будет создано на основе шаблона.</span><span class="sxs-lookup"><span data-stu-id="77234-195">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="77234-196">Дополнительные сведения см. в примечании в разделе [Области маркеров доступа](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="77234-196">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="77234-197">Возможно, шаблон :::no-loc(Blazor WebAssembly)::: будет изменен в следующем выпуске ASP.NET Core с учетом таких сценариев.</span><span class="sxs-lookup"><span data-stu-id="77234-197">The :::no-loc(Blazor WebAssembly)::: template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="77234-198">Дополнительные сведения см. в статье [Двойная схема URI идентификатора приложения с шаблоном WASM :::no-loc(Blazor)::: (размещенное решение для одной организации) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="77234-198">For more information, see [Double scheme for App ID URI with :::no-loc(Blazor)::: WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="77234-199">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-199">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="77234-200">Область, настроенная размещенным шаблоном :::no-loc(Blazor):::, может содержать повторяющийся узел URI идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-200">The scope set up by the Hosted :::no-loc(Blazor)::: template might have the App ID URI host repeated.</span></span> <span data-ttu-id="77234-201">Убедитесь, что область, настроенная для коллекции `DefaultAccessTokenScopes`, указана правильно в `Program.Main` (`Program.cs`) приложения *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="77234-201">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="77234-202">На портале Azure **URI перенаправления** в конфигурации платформы приложения *`Client`* настроен для использования порта 5001, предназначенного для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="77234-202">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="77234-203">Если приложение *`Client`* выполняется на случайном порту IIS Express, порт для приложения можно просмотреть в свойствах *приложения API Server* в области **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="77234-203">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="77234-204">Если порт не был настроен ранее с использованием известного порта приложения *`Client`* , вернитесь к регистрации приложения *`Client`* на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="77234-204">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="77234-205">Конфигурация приложения *`Server`*</span><span class="sxs-lookup"><span data-stu-id="77234-205">*`Server`* app configuration</span></span>

<span data-ttu-id="77234-206">*Этот раздел относится к приложению **`Server`** решения.*</span><span class="sxs-lookup"><span data-stu-id="77234-206">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="77234-207">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="77234-207">Authentication package</span></span>

<span data-ttu-id="77234-208">Поддержка проверки подлинности и авторизации вызовов веб-API ASP.NET Core предоставляется пакетом [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI).</span><span class="sxs-lookup"><span data-stu-id="77234-208">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="77234-209">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="77234-209">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="77234-210">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="77234-210">Authentication service support</span></span>

<span data-ttu-id="77234-211">Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="77234-211">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="77234-212">Метод <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, создаваемых Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="77234-212">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="77234-213"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> позволяют обеспечить, что:</span><span class="sxs-lookup"><span data-stu-id="77234-213"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="77234-214">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="77234-214">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="77234-215">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="77234-215">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="77234-216">User.:::no-loc(Identity):::.Name</span><span class="sxs-lookup"><span data-stu-id="77234-216">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="77234-217">По умолчанию параметр `User.:::no-loc(Identity):::.Name` не заполняется.</span><span class="sxs-lookup"><span data-stu-id="77234-217">By default, the `User.:::no-loc(Identity):::.Name` isn't populated.</span></span>

<span data-ttu-id="77234-218">Чтобы настроить приложение на получение значения из типа утверждения `name`, настройте <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="77234-218">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="77234-219">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="77234-219">App settings</span></span>

<span data-ttu-id="77234-220">В файле `:::no-loc(appsettings.json):::` содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="77234-220">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="77234-221">Пример.</span><span class="sxs-lookup"><span data-stu-id="77234-221">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="77234-222">Контроллер WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="77234-222">WeatherForecast controller</span></span>

<span data-ttu-id="77234-223">Контроллер WeatherForecast ( *Controllers/WeatherForecastController.cs* ) предоставляет защищенный API с атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="77234-223">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="77234-224">**Важно** понять следующее:</span><span class="sxs-lookup"><span data-stu-id="77234-224">It's **important** to understand that:</span></span>

* <span data-ttu-id="77234-225">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="77234-225">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="77234-226">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), используемый в приложении :::no-loc(Blazor WebAssembly):::, служит подсказкой для приложения о том, что пользователь должен пройти авторизацию, чтобы приложение работало правильно.</span><span class="sxs-lookup"><span data-stu-id="77234-226">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="77234-227">Конфигурация приложения *`Client`*</span><span class="sxs-lookup"><span data-stu-id="77234-227">*`Client`* app configuration</span></span>

<span data-ttu-id="77234-228">*Этот раздел относится к приложению **`Client`** решения.*</span><span class="sxs-lookup"><span data-stu-id="77234-228">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="77234-229">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="77234-229">Authentication package</span></span>

<span data-ttu-id="77234-230">Когда приложение создается для использования отдельной учетной записи B2C (`IndividualB2C`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="77234-230">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="77234-231">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="77234-231">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="77234-232">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="77234-232">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="77234-233">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="77234-233">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="77234-234">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.</span><span class="sxs-lookup"><span data-stu-id="77234-234">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="77234-235">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="77234-235">Authentication service support</span></span>

<span data-ttu-id="77234-236">Добавлена поддержка экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="77234-236">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="77234-237">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="77234-237">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="77234-238">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `:::no-loc(Blazor):::Sample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="77234-238">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="77234-239">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="77234-239">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="77234-240">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком :::no-loc(Identity)::: (IP).</span><span class="sxs-lookup"><span data-stu-id="77234-240">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="77234-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="77234-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="77234-242">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-242">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="77234-243">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="77234-243">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="77234-244">Конфигурация предоставлена в файле `wwwroot/:::no-loc(appsettings.json):::`:</span><span class="sxs-lookup"><span data-stu-id="77234-244">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="77234-245">Пример.</span><span class="sxs-lookup"><span data-stu-id="77234-245">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="77234-246">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="77234-246">Access token scopes</span></span>

<span data-ttu-id="77234-247">Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:</span><span class="sxs-lookup"><span data-stu-id="77234-247">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="77234-248">включены по умолчанию в запросе на вход;</span><span class="sxs-lookup"><span data-stu-id="77234-248">Included by default in the sign in request.</span></span>
* <span data-ttu-id="77234-249">используются для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="77234-249">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="77234-250">Все области должны входить в одно и то же приложение для каждого правила Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="77234-250">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="77234-251">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="77234-251">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="77234-252">Шаблон :::no-loc(Blazor WebAssembly)::: автоматически добавляет схему `api://` к аргументу URI идентификатора приложения, переданному в команде `dotnet new`.</span><span class="sxs-lookup"><span data-stu-id="77234-252">The :::no-loc(Blazor WebAssembly)::: template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="77234-253">При создании приложения на основе шаблона проекта :::no-loc(Blazor)::: убедитесь, что для области маркера доступа по умолчанию указано правильное значение URI идентификатора пользовательского приложения, указанное на портале Azure, или значение в **одном** из следующих форматов:</span><span class="sxs-lookup"><span data-stu-id="77234-253">When generating an app from the :::no-loc(Blazor)::: project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="77234-254">Если домен издателя каталога является **доверенным** , то для области маркера доступа по умолчанию, как правило, задано значение, аналогичное приведенному в следующем примере, где `API.Access` — это имя области по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="77234-254">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="77234-255">Проверьте значение двойной схемы (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="77234-255">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="77234-256">При наличии двойной схемы удалите первую схему `api://` из значения.</span><span class="sxs-lookup"><span data-stu-id="77234-256">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="77234-257">Если домен издателя каталога является **недоверенным** , то для области маркера доступа по умолчанию, как правило, задано значение, аналогичное приведенному в следующем примере, где `API.Access` — это имя области по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="77234-257">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="77234-258">Проверьте значение на наличие дополнительной схемы `api://` (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="77234-258">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="77234-259">При наличии дополнительной схемы `api://` удалите схему `api://` из значения.</span><span class="sxs-lookup"><span data-stu-id="77234-259">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="77234-260">Возможно, шаблон :::no-loc(Blazor WebAssembly)::: будет изменен в следующем выпуске ASP.NET Core с учетом таких сценариев.</span><span class="sxs-lookup"><span data-stu-id="77234-260">The :::no-loc(Blazor WebAssembly)::: template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="77234-261">Дополнительные сведения см. в статье [Двойная схема URI идентификатора приложения с шаблоном WASM :::no-loc(Blazor)::: (размещенное решение для одной организации) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="77234-261">For more information, see [Double scheme for App ID URI with :::no-loc(Blazor)::: WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="77234-262">Укажите дополнительные области с помощью `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="77234-262">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="77234-263">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="77234-263">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="77234-264">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="77234-264">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="77234-265">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="77234-265">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="77234-266">Режим входа</span><span class="sxs-lookup"><span data-stu-id="77234-266">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="77234-267">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="77234-267">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="77234-268">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="77234-268">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="77234-269">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="77234-269">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="77234-270">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="77234-270">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="77234-271">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="77234-271">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="77234-272">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="77234-272">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="77234-273">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="77234-273">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="77234-274">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="77234-274">Run the app</span></span>

<span data-ttu-id="77234-275">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="77234-275">Run the app from the Server project.</span></span> <span data-ttu-id="77234-276">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="77234-276">When using Visual Studio, either:</span></span>

* <span data-ttu-id="77234-277">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="77234-277">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="77234-278">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="77234-278">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="77234-279">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="77234-279">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="77234-280">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="77234-280">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* <span data-ttu-id="77234-281">[Учебник. Создание клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="77234-281">[Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)</span></span>
* [<span data-ttu-id="77234-282">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="77234-282">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
