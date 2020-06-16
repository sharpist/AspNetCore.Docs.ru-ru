---
title: Обеспечение безопасности Blazor размещенного в ASP.NET Core приложения сборки Azure Active Directory B2C
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: f7b53c8043b53912bcca67bae868e3b516ad2e0d
ms.sourcegitcommit: b0062f29cba2e5c21b95cf89eaf435ba830d11a3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84776466"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="5ab3d-102">Обеспечение безопасности Blazor размещенного в ASP.NET Core приложения сборки Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5ab3d-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="5ab3d-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ab3d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5ab3d-104">В этой статье описывается, как создать Blazor автономное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="5ab3d-105">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="5ab3d-106">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="5ab3d-106">Create a tenant</span></span>

<span data-ttu-id="5ab3d-107">Следуйте указаниям в [руководстве по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) для создания клиента AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="5ab3d-108">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-108">Record the following information:</span></span>

* <span data-ttu-id="5ab3d-109">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/` , включающий косую черту)</span><span class="sxs-lookup"><span data-stu-id="5ab3d-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="5ab3d-110">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="5ab3d-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="5ab3d-111">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="5ab3d-111">Register a server API app</span></span>

<span data-ttu-id="5ab3d-112">Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , чтобы зарегистрировать приложение AAD для *приложения API сервера* , а затем выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="5ab3d-113">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="5ab3d-114">Укажите **имя** приложения (например, \*\* Blazor AAD B2C сервера\*\*).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="5ab3d-115">Для **поддерживаемых типов учетных записей**выберите параметр несколько клиентов: **учетные записи в любом каталоге организации или в любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="5ab3d-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="5ab3d-116">В этом сценарии *приложению API сервера* не требуется **универсальный код ресурса (URI) перенаправления** , поэтому оставьте в раскрывающемся списке значение **Web** и не вводите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="5ab3d-117">Убедитесь, что **разрешения**  >  **предоставлены администратору для разрешений OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="5ab3d-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-118">Select **Register**.</span></span>

<span data-ttu-id="5ab3d-119">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-119">Record the following information:</span></span>

* <span data-ttu-id="5ab3d-120">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="5ab3d-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="5ab3d-121">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="5ab3d-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="5ab3d-122">Домен клиента AAD (например, `contoso.onmicrosoft.com` ). домен доступен в качестве **домена издателя** в колонке **фирменной символики** портал Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="5ab3d-123">В **предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="5ab3d-124">Нажмите **Добавить группу**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="5ab3d-125">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="5ab3d-126">Укажите **имя области** (например, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5ab3d-127">Укажите **Отображаемое имя согласия администратора** (например, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="5ab3d-128">Введите **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="5ab3d-129">Убедитесь, что для **состояния** задано значение **включено**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="5ab3d-130">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-130">Select **Add scope**.</span></span>

<span data-ttu-id="5ab3d-131">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-131">Record the following information:</span></span>

* <span data-ttu-id="5ab3d-132">URI идентификатора приложения (например,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` или предоставленное вами пользовательское значение)</span><span class="sxs-lookup"><span data-stu-id="5ab3d-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="5ab3d-133">Область по умолчанию (например, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="5ab3d-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="5ab3d-134">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-134">Register a client app</span></span>

<span data-ttu-id="5ab3d-135">Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) еще раз, чтобы зарегистрировать приложение AAD для *клиентского приложения* , а затем выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="5ab3d-136">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="5ab3d-137">Укажите **имя** приложения (например, \*\* Blazor AAD B2C клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="5ab3d-138">Для **поддерживаемых типов учетных записей**выберите параметр несколько клиентов: **учетные записи в любом каталоге организации или в любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="5ab3d-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="5ab3d-139">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб-сайт** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="5ab3d-140">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="5ab3d-141">Если приложение выполняется на другом порту Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="5ab3d-142">Для IIS Express созданный случайным образом порт для приложения можно найти в свойствах серверного приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="5ab3d-143">Так как на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="5ab3d-144">Замечание появится в разделе [Создание приложения](#create-the-app) , чтобы напомнить IIS Express пользователям об обновлении URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="5ab3d-145">Убедитесь, что **разрешения**  >  **предоставлены администратору для разрешений OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="5ab3d-146">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-146">Select **Register**.</span></span>

<span data-ttu-id="5ab3d-147">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="5ab3d-148">В конфигурации платформы **проверки подлинности**  >  **Platform configurations**  >  **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="5ab3d-149">Убедитесь, что **URI перенаправления** `https://localhost:{PORT}/authentication/login-callback` имеется.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="5ab3d-150">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="5ab3d-151">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="5ab3d-152">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-152">Select the **Save** button.</span></span>

<span data-ttu-id="5ab3d-153">В **разрешениях API**:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-153">In **API permissions**:</span></span>

1. <span data-ttu-id="5ab3d-154">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="5ab3d-155">Выберите *приложение API сервера* из столбца **имя** (например, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="5ab3d-156">Откройте список **API** .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-156">Open the **API** list.</span></span>
1. <span data-ttu-id="5ab3d-157">Разрешение доступа к API (например, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="5ab3d-158">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="5ab3d-159">Нажмите кнопку **предоставить содержимое администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="5ab3d-160">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="5ab3d-161">В **домашних**  >  **Azure AD B2C**  >  **пользовательских потоках**:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="5ab3d-162">Создание потока пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="5ab3d-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="5ab3d-163">**Application claims**  >  Для заполнения**Display Name** `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя "отображаемое имя утверждения приложения".</span><span class="sxs-lookup"><span data-stu-id="5ab3d-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="5ab3d-164">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="5ab3d-165">Создание приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-165">Create the app</span></span>

<span data-ttu-id="5ab3d-166">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="5ab3d-167">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5ab3d-168">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="5ab3d-169">Передайте универсальный код ресурса (URI) идентификатора приложения в `app-id-uri` параметр, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, которое описано в разделе [области действия маркера доступа](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="5ab3d-170">Кроме того, в области, настроенной размещенным Blazor шаблоном, может быть повторяющийся узел URI идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="5ab3d-171">Убедитесь, что область, настроенная для `DefaultAccessTokenScopes` коллекции, верна в `Program.Main` (*Program.CS*) *клиентского приложения*.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="5ab3d-172">В портал Azure параметры платформы **проверки подлинности**для веб- *клиента клиентского приложения*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** настроены для порта 5001 для приложений, работающих на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="5ab3d-173">Если *клиентское приложение* выполняется на случайном IIS Express порту, порт для приложения можно найти в свойствах *серверного приложения* на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="5ab3d-174">Если порт не был настроен ранее с помощью известного порта *клиентского приложения* , вернитесь к регистрации *клиентского приложения* в портал Azure и обновите URI перенаправления с правильным портом.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="5ab3d-175">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-175">Server app configuration</span></span>

<span data-ttu-id="5ab3d-176">*Этот раздел относится к **серверному** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="5ab3d-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5ab3d-177">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5ab3d-177">Authentication package</span></span>

<span data-ttu-id="5ab3d-178">Поддержка проверки подлинности и авторизации вызовов ASP.NET Core веб-API предоставляется пакетом [Microsoft. AspNetCore. Authentication. AzureADB2C. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) :</span><span class="sxs-lookup"><span data-stu-id="5ab3d-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="5ab3d-179">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5ab3d-179">Authentication service support</span></span>

<span data-ttu-id="5ab3d-180">Метод настраивает `AddAuthentication` службы проверки подлинности в приложении и настраивает обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="5ab3d-181"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Метод настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, созданных Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="5ab3d-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>и <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Убедитесь, что:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="5ab3d-183">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="5ab3d-184">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="5ab3d-185">Пользователь. Identity . Безымян</span><span class="sxs-lookup"><span data-stu-id="5ab3d-185">User.Identity.Name</span></span>

<span data-ttu-id="5ab3d-186">По умолчанию значение `User.Identity.Name` не заполняется.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="5ab3d-187">Чтобы настроить приложение для получения значения из `name` типа утверждения, настройте [TokenValidationParameters. намеклаимтипе](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5ab3d-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="5ab3d-188">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-188">App settings</span></span>

<span data-ttu-id="5ab3d-189">*appsettings.jsв* файле содержит параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="5ab3d-190">Пример</span><span class="sxs-lookup"><span data-stu-id="5ab3d-190">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="5ab3d-191">Контроллер Веасерфорекаст</span><span class="sxs-lookup"><span data-stu-id="5ab3d-191">WeatherForecast controller</span></span>

<span data-ttu-id="5ab3d-192">Контроллер Веасерфорекаст (*Controllers/веасерфорекастконтроллер. CS*) предоставляет защищенный API с [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибутом, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="5ab3d-193">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="5ab3d-194">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Атрибут в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="5ab3d-195">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Атрибут, используемый в Blazor приложении сборки, служит указанием для приложения, которое должно быть проверено для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="5ab3d-196">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-196">Client app configuration</span></span>

<span data-ttu-id="5ab3d-197">*Этот раздел относится к **клиентскому** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="5ab3d-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5ab3d-198">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5ab3d-198">Authentication package</span></span>

<span data-ttu-id="5ab3d-199">При создании приложения для использования отдельной учетной записи B2C ( `IndividualB2C` ) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="5ab3d-200">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5ab3d-201">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="5ab3d-202">Пакет [Microsoft. Authentication. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) . в качестве транзитного пакета добавляет пакет [Microsoft. AspNetCore. Components. Assembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в приложение.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="5ab3d-203">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5ab3d-203">Authentication service support</span></span>

<span data-ttu-id="5ab3d-204">Добавлена поддержка <xref:System.Net.Http.HttpClient> экземпляров, включающая маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="5ab3d-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="5ab3d-206">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="5ab3d-207">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> метода расширения, предоставленного пакетом [Microsoft. Authentication. WebService. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="5ab3d-208">Этот метод настраивает службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="5ab3d-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5ab3d-209">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-209">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="5ab3d-210"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5ab3d-211">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="5ab3d-212">Конфигурация предоставляется файлом *wwwroot/appsettings.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-212">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="5ab3d-213">Пример</span><span class="sxs-lookup"><span data-stu-id="5ab3d-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="5ab3d-214">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="5ab3d-214">Access token scopes</span></span>

<span data-ttu-id="5ab3d-215">Области токена доступа по умолчанию представляют собой список областей токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="5ab3d-216">Включается по умолчанию в запросе на вход.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="5ab3d-217">Используется для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="5ab3d-218">Все области должны принадлежать одному и тому же приложению для правил Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="5ab3d-219">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="5ab3d-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="5ab3d-220">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="5ab3d-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="5ab3d-221">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="5ab3d-221">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5ab3d-222">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="5ab3d-222">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="5ab3d-223">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="5ab3d-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5ab3d-224">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="5ab3d-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="5ab3d-225">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5ab3d-226">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="5ab3d-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5ab3d-227">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="5ab3d-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="5ab3d-228">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="5ab3d-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5ab3d-229">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="5ab3d-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5ab3d-230">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="5ab3d-230">Run the app</span></span>

<span data-ttu-id="5ab3d-231">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-231">Run the app from the Server project.</span></span> <span data-ttu-id="5ab3d-232">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="5ab3d-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="5ab3d-233">Задайте раскрывающийся список **запускаемые проекты** на панели инструментов для *приложения API сервера* и нажмите кнопку **выполнить** .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="5ab3d-234">Выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="5ab3d-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5ab3d-235">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5ab3d-235">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="5ab3d-236">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="5ab3d-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="5ab3d-237">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="5ab3d-237">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="5ab3d-238">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="5ab3d-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
