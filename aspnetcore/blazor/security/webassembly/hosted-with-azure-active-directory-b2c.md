---
title: Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 22e9af6ed0a6215e881ed733aa0ba3ad8c6cc78e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103512"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="2f9ef-102">Защита размещенного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="2f9ef-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="2f9ef-103">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2f9ef-104">В этой статье описывается, как создать изолированное приложение Blazor WebAssembly, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="2f9ef-105">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="2f9ef-106">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="2f9ef-106">Create a tenant</span></span>

<span data-ttu-id="2f9ef-107">Следуйте инструкциям в разделе [Руководство по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant), чтобы создать клиент AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="2f9ef-108">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-108">Record the following information:</span></span>

* <span data-ttu-id="2f9ef-109">Экземпляр AAD B2C (например, `https://contoso.b2clogin.com/`, который включает косую черту в конце)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="2f9ef-110">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="2f9ef-111">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="2f9ef-111">Register a server API app</span></span>

<span data-ttu-id="2f9ef-112">Следуйте инструкциям в разделе [Руководство по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications), чтобы зарегистрировать приложение AAD для *приложения API сервера*, а затем выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="2f9ef-113">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="2f9ef-114">Укажите **имя** приложения (например, **BlazorСерверный AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="2f9ef-115">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом каталоге организации или поставщике удостоверений. Выберите этот вариант для проверки подлинности в Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="2f9ef-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="2f9ef-116">В этом сценарии *приложению API сервера* не требуется **URI перенаправления**, поэтому в раскрывающемся списке оставьте значение **Интернет** и не вводите URI-перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="2f9ef-117">Убедитесь, что флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access** установлен.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="2f9ef-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-118">Select **Register**.</span></span>

<span data-ttu-id="2f9ef-119">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-119">Record the following information:</span></span>

* <span data-ttu-id="2f9ef-120">Идентификатор *приложения API сервера* (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="2f9ef-121">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="2f9ef-122">Домен клиента AAD (например, `contoso.onmicrosoft.com`): домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="2f9ef-123">В разделе **Предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="2f9ef-124">Нажмите **Добавить группу**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="2f9ef-125">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="2f9ef-126">Укажите значение в поле **Имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="2f9ef-127">Укажите значение в поле **Отображаемое имя согласия администратора** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="2f9ef-128">Укажите значение в поле **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="2f9ef-129">Убедитесь в том, что параметру **Состояние** присвоено значение **Включено**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="2f9ef-130">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-130">Select **Add scope**.</span></span>

<span data-ttu-id="2f9ef-131">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-131">Record the following information:</span></span>

* <span data-ttu-id="2f9ef-132">URI идентификатора приложения (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111` или настраиваемое значение)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="2f9ef-133">Область по умолчанию (например, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="2f9ef-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="2f9ef-134">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-134">Register a client app</span></span>

<span data-ttu-id="2f9ef-135">Следуйте инструкциям в разделе [Руководство по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications), чтобы зарегистрировать приложение AAD для *клиентского приложения*, а затем выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="2f9ef-136">В разделе **Azure Active Directory** > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="2f9ef-137">Укажите **имя** приложения (например, **BlazorКлиентский AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="2f9ef-138">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом каталоге организации или поставщике удостоверений. Выберите этот вариант для проверки подлинности в Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="2f9ef-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="2f9ef-139">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2f9ef-140">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2f9ef-141">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2f9ef-142">Для IIS Express созданный случайным образом порт для приложения указан в свойствах серверного приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2f9ef-143">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2f9ef-144">В разделе [Создание приложения](#create-the-app) появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2f9ef-145">Убедитесь, что флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access** установлен.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="2f9ef-146">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-146">Select **Register**.</span></span>

<span data-ttu-id="2f9ef-147">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="2f9ef-148">В разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="2f9ef-149">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2f9ef-150">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="2f9ef-151">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2f9ef-152">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-152">Select the **Save** button.</span></span>

<span data-ttu-id="2f9ef-153">В разделе **Разрешения API** выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-153">In **API permissions**:</span></span>

1. <span data-ttu-id="2f9ef-154">Выберите **Добавить разрешение**, а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="2f9ef-155">В столбце *Имя* выберите **Приложение API сервера** (например, **Blazor Серверный AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="2f9ef-156">Откройте список **API**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-156">Open the **API** list.</span></span>
1. <span data-ttu-id="2f9ef-157">Разрешите доступ к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="2f9ef-158">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="2f9ef-159">Нажмите кнопку **Предоставить согласие администратора для {ИМЯ КЛИЕНТА}** .</span><span class="sxs-lookup"><span data-stu-id="2f9ef-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="2f9ef-160">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="2f9ef-161">В разделе **Главная** > **Azure AD B2C** > **Потоки пользователя** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="2f9ef-162">Создайте поток пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="2f9ef-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="2f9ef-163">Чтобы заполнить `context.User.Identity.Name` в компоненте `LoginDisplay` (*Shared/LoginDisplay.razor*), выберите хотя бы атрибут пользователя **Утверждения приложения** > **Отображаемое имя**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="2f9ef-164">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="2f9ef-165">Создание приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-165">Create the app</span></span>

<span data-ttu-id="2f9ef-166">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="2f9ef-167">Чтобы указать расположение выходных данных для создания папки проекта, если она не существует, включите в команду параметр выходных данных с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="2f9ef-168">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="2f9ef-169">Передайте URI идентификатора приложения в параметр `app-id-uri`, но обратите внимание, что в клиентском приложении может потребоваться изменить конфигурацию, как описано в разделе [Области маркеров доступа](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="2f9ef-170">Кроме того, область, настроенная размещенным шаблоном Blazor, может содержать повторяющийся узел URI идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="2f9ef-171">Убедитесь, что область, настроенная для коллекции `DefaultAccessTokenScopes`, указана правильно в `Program.Main` (*Program.cs*) *клиентского приложения*.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="2f9ef-172">На портале Azure в разделе **Проверка подлинности** > **Конфигурации платформы** > **Интернет** > **URI перенаправления** в качестве порта для приложений, выполняющихся на сервере Kestrel с параметрами по умолчанию, для *клиентского приложения* задан порт 5001.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="2f9ef-173">Если *клиентское приложение* выполняется на случайном порту IIS Express, порт для приложения указан в свойствах *серверного приложения* на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="2f9ef-174">Если порт не был настроен ранее с помощью известного порта *клиентского приложения*, вернитесь к регистрации *клиентского приложения* на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="2f9ef-175">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-175">Server app configuration</span></span>

<span data-ttu-id="2f9ef-176">*Этот раздел относится к **серверному** приложению.*</span><span class="sxs-lookup"><span data-stu-id="2f9ef-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2f9ef-177">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2f9ef-177">Authentication package</span></span>

<span data-ttu-id="2f9ef-178">Поддержка проверки подлинности и авторизации вызовов веб-API ASP.NET Core предоставляется пакетом [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="2f9ef-179">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2f9ef-179">Authentication service support</span></span>

<span data-ttu-id="2f9ef-180">Метод `AddAuthentication` настраивает службы проверки подлинности в приложении и обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="2f9ef-181">Метод <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, создаваемых Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="2f9ef-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> позволяют обеспечить, что:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="2f9ef-183">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="2f9ef-184">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="2f9ef-185">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="2f9ef-185">User.Identity.Name</span></span>

<span data-ttu-id="2f9ef-186">По умолчанию параметр `User.Identity.Name` не заполняется.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="2f9ef-187">Чтобы настроить приложение на получение значения из типа утверждения`name`, настройте [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) для <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="2f9ef-188">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-188">App settings</span></span>

<span data-ttu-id="2f9ef-189">В файле *appsettings.json* содержатся параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="2f9ef-190">Пример.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-190">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="2f9ef-191">Контроллер WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="2f9ef-191">WeatherForecast controller</span></span>

<span data-ttu-id="2f9ef-192">Контроллер WeatherForecast (*Controllers/WeatherForecastController.cs*) предоставляет защищенный API с атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="2f9ef-193">**Важно** понять следующее:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="2f9ef-194">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="2f9ef-195">Атрибут [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute), используемый в приложении Blazor WebAssembly, служит подсказкой для приложения, которую должен авторизовать пользователь, чтобы приложение работало правильно.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="2f9ef-196">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-196">Client app configuration</span></span>

<span data-ttu-id="2f9ef-197">*Этот раздел относится к **клиентскому** приложению.*</span><span class="sxs-lookup"><span data-stu-id="2f9ef-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2f9ef-198">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2f9ef-198">Authentication package</span></span>

<span data-ttu-id="2f9ef-199">Когда приложение создается для использования отдельной учетной записи B2C (`IndividualB2C`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="2f9ef-200">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2f9ef-201">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="2f9ef-202">Пакет [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) транзитивно добавляет в приложение пакет [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="2f9ef-203">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2f9ef-203">Authentication service support</span></span>

<span data-ttu-id="2f9ef-204">Добавлена поддержка экземпляров <xref:System.Net.Http.HttpClient>, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="2f9ef-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="2f9ef-206">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="2f9ef-207">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="2f9ef-208">Этот метод настраивает службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2f9ef-209">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-209">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="2f9ef-210">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2f9ef-211">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="2f9ef-212">Конфигурация предоставляется файлом *wwwroot/appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-212">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="2f9ef-213">Пример.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="2f9ef-214">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="2f9ef-214">Access token scopes</span></span>

<span data-ttu-id="2f9ef-215">Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="2f9ef-216">включены по умолчанию в запросе на вход;</span><span class="sxs-lookup"><span data-stu-id="2f9ef-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="2f9ef-217">используются для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="2f9ef-218">Все области должны входить в одно и то же приложение для каждого правила Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="2f9ef-219">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="2f9ef-220">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии*:</span><span class="sxs-lookup"><span data-stu-id="2f9ef-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2f9ef-221">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="2f9ef-221">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2f9ef-222">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="2f9ef-222">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="2f9ef-223">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="2f9ef-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="2f9ef-224">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="2f9ef-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="2f9ef-225">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="2f9ef-226">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="2f9ef-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="2f9ef-227">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="2f9ef-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="2f9ef-228">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2f9ef-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="2f9ef-229">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="2f9ef-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="2f9ef-230">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="2f9ef-230">Run the app</span></span>

<span data-ttu-id="2f9ef-231">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-231">Run the app from the Server project.</span></span> <span data-ttu-id="2f9ef-232">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="2f9ef-233">На панели инструментов в раскрывающемся списке **Запускаемые проекты** выберите *Приложение API сервера* и нажмите клавишу **Запустить**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="2f9ef-234">В **обозревателе решений** выберите серверный проект и нажмите кнопку **Запустить** на панели инструментов или запустите приложение из меню **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="2f9ef-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2f9ef-235">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2f9ef-235">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2f9ef-236">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2f9ef-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* <span data-ttu-id="2f9ef-237">[Учебник. Создание клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="2f9ef-237">[Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)</span></span>
* [<span data-ttu-id="2f9ef-238">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="2f9ef-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
