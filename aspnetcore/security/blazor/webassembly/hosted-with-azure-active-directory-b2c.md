---
title: Обеспечение безопасности размещенного в ASP.NET Core Blazor приложения сборки Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110984"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="fff3c-102">Обеспечение безопасности размещенного в ASP.NET Core Blazor приложения сборки Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="fff3c-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="fff3c-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fff3c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="fff3c-104">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="fff3c-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="fff3c-105">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="fff3c-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="fff3c-106">В этой статье описывается, как создать Blazor автономное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="fff3c-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="fff3c-107">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="fff3c-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="fff3c-108">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="fff3c-108">Create a tenant</span></span>

<span data-ttu-id="fff3c-109">Следуйте указаниям в [руководстве по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) для создания клиента AAD B2C и записи следующих сведений.</span><span class="sxs-lookup"><span data-stu-id="fff3c-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="fff3c-110">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/`, включающий косую черту)</span><span class="sxs-lookup"><span data-stu-id="fff3c-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="fff3c-111">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="fff3c-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="fff3c-112">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="fff3c-112">Register a server API app</span></span>

<span data-ttu-id="fff3c-113">Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , чтобы зарегистрировать приложение AAD для *приложения API сервера* в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="fff3c-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fff3c-114">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-114">Select **New registration**.</span></span>
1. <span data-ttu-id="fff3c-115">Укажите **имя** приложения (например, \*\* Blazor AAD B2C сервера\*\*).</span><span class="sxs-lookup"><span data-stu-id="fff3c-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="fff3c-116">Для **поддерживаемых типов учетных записей**выберите **учетные записи в любом организационном каталоге или любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="fff3c-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="fff3c-117">(несколько клиентов) для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="fff3c-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="fff3c-118">В этом сценарии *приложению API сервера* не требуется **универсальный код ресурса (URI) перенаправления** , поэтому оставьте в раскрывающемся списке значение **Web** и не вводите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="fff3c-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="fff3c-119">Убедитесь, что **разрешения** > **на предоставление разрешений администратора OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="fff3c-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="fff3c-120">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-120">Select **Register**.</span></span>

<span data-ttu-id="fff3c-121">В **предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="fff3c-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="fff3c-122">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="fff3c-123">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="fff3c-124">Укажите **имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="fff3c-125">Укажите **Отображаемое имя согласия администратора** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="fff3c-126">Введите **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="fff3c-127">Убедитесь, что для **состояния** задано значение **включено**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="fff3c-128">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-128">Select **Add scope**.</span></span>

<span data-ttu-id="fff3c-129">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="fff3c-129">Record the following information:</span></span>

* <span data-ttu-id="fff3c-130">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="fff3c-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fff3c-131">URI идентификатора приложения (например `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, или предоставленное вами пользовательское значение)</span><span class="sxs-lookup"><span data-stu-id="fff3c-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="fff3c-132">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="fff3c-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="fff3c-133">*Приложение API сервера* URI идентификатора приложения (например `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, портал Azure может по умолчанию присвоить значение идентификатору клиента)</span><span class="sxs-lookup"><span data-stu-id="fff3c-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="fff3c-134">Область по умолчанию (например `API.Access`,)</span><span class="sxs-lookup"><span data-stu-id="fff3c-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="fff3c-135">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="fff3c-135">Register a client app</span></span>

<span data-ttu-id="fff3c-136">Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) еще раз, чтобы зарегистрировать приложение AAD для *клиентского приложения* в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="fff3c-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fff3c-137">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-137">Select **New registration**.</span></span>
1. <span data-ttu-id="fff3c-138">Укажите **имя** приложения (например, \*\* Blazor AAD B2C клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="fff3c-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="fff3c-139">Для **поддерживаемых типов учетных записей**выберите **учетные записи в любом организационном каталоге или любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="fff3c-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="fff3c-140">(несколько клиентов) для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="fff3c-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="fff3c-141">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.</span><span class="sxs-lookup"><span data-stu-id="fff3c-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="fff3c-142">Убедитесь, что **разрешения** > **на предоставление разрешений администратора OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="fff3c-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="fff3c-143">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-143">Select **Register**.</span></span>

<span data-ttu-id="fff3c-144">В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="fff3c-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fff3c-145">Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.</span><span class="sxs-lookup"><span data-stu-id="fff3c-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fff3c-146">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fff3c-147">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="fff3c-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fff3c-148">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-148">Select the **Save** button.</span></span>

<span data-ttu-id="fff3c-149">В **разрешениях API**:</span><span class="sxs-lookup"><span data-stu-id="fff3c-149">In **API permissions**:</span></span>

1. <span data-ttu-id="fff3c-150">Убедитесь, что приложение имеет **Microsoft Graph** > **пользователь. чтение** .</span><span class="sxs-lookup"><span data-stu-id="fff3c-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="fff3c-151">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="fff3c-152">Выберите *приложение API сервера* из столбца **имя** (например, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="fff3c-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="fff3c-153">Откройте список **API** .</span><span class="sxs-lookup"><span data-stu-id="fff3c-153">Open the **API** list.</span></span>
1. <span data-ttu-id="fff3c-154">Разрешение доступа к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="fff3c-155">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="fff3c-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="fff3c-156">Нажмите кнопку **предоставить содержимое администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="fff3c-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="fff3c-157">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="fff3c-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="fff3c-158">В **домашних** > **Azure AD B2C** > **пользовательских потоках**:</span><span class="sxs-lookup"><span data-stu-id="fff3c-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="fff3c-159">Создание потока пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="fff3c-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="fff3c-160">Для заполнения `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя " > **Отображаемое имя** **утверждения приложения**".</span><span class="sxs-lookup"><span data-stu-id="fff3c-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="fff3c-161">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="fff3c-161">Record the following information:</span></span>

* <span data-ttu-id="fff3c-162">Запишите идентификатор приложения *клиентского приложения* (идентификатор клиента) (например, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="fff3c-163">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="fff3c-164">Создайте приложение</span><span class="sxs-lookup"><span data-stu-id="fff3c-164">Create the app</span></span>

<span data-ttu-id="fff3c-165">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="fff3c-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fff3c-166">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fff3c-167">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="fff3c-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="fff3c-168">Передайте универсальный код ресурса (URI `app-id-uri` ) идентификатора приложения в параметр, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, которое описано в разделе [области действия маркера доступа](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="fff3c-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="fff3c-169">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="fff3c-169">Server app configuration</span></span>

<span data-ttu-id="fff3c-170">*Этот раздел относится к **серверному** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="fff3c-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="fff3c-171">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fff3c-171">Authentication package</span></span>

<span data-ttu-id="fff3c-172">Поддержка проверки подлинности и авторизации вызовов ASP.NET Core веб-интерфейсов API обеспечивается `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="fff3c-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="fff3c-173">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fff3c-173">Authentication service support</span></span>

<span data-ttu-id="fff3c-174">`AddAuthentication` Метод настраивает службы проверки подлинности в приложении и настраивает обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="fff3c-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="fff3c-175">`AddAzureADB2CBearer` Метод настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, созданных Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="fff3c-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="fff3c-176">`UseAuthentication`и `UseAuthorization` убедитесь, что:</span><span class="sxs-lookup"><span data-stu-id="fff3c-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="fff3c-177">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="fff3c-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="fff3c-178">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="fff3c-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="fff3c-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="fff3c-179">User.Identity.Name</span></span>

<span data-ttu-id="fff3c-180">По умолчанию значение `User.Identity.Name` не заполняется.</span><span class="sxs-lookup"><span data-stu-id="fff3c-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="fff3c-181">Чтобы настроить приложение для получения значения `name` из типа утверждения, настройте [TokenValidationParameters. намеклаимтипе](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="fff3c-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="fff3c-182">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="fff3c-182">App settings</span></span>

<span data-ttu-id="fff3c-183">Файл *appSettings. JSON* содержит параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="fff3c-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="fff3c-184">Контроллер Веасерфорекаст</span><span class="sxs-lookup"><span data-stu-id="fff3c-184">WeatherForecast controller</span></span>

<span data-ttu-id="fff3c-185">Контроллер Веасерфорекаст (*Controllers/веасерфорекастконтроллер. CS*) предоставляет защищенный API с `[Authorize]` атрибутом, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="fff3c-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="fff3c-186">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="fff3c-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="fff3c-187">`[Authorize]` Атрибут в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="fff3c-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="fff3c-188">`[Authorize]` Атрибут, используемый в Blazor приложении сборки, служит указанием для приложения, которое должно быть проверено для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="fff3c-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="fff3c-189">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="fff3c-189">Client app configuration</span></span>

<span data-ttu-id="fff3c-190">*Этот раздел относится к **клиентскому** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="fff3c-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="fff3c-191">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fff3c-191">Authentication package</span></span>

<span data-ttu-id="fff3c-192">При создании приложения для использования отдельной учетной записи B2C (`IndividualB2C`) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="fff3c-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fff3c-193">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="fff3c-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fff3c-194">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="fff3c-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fff3c-195">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="fff3c-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fff3c-196">`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="fff3c-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="fff3c-197">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fff3c-197">Authentication service support</span></span>

<span data-ttu-id="fff3c-198">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="fff3c-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fff3c-199">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).</span><span class="sxs-lookup"><span data-stu-id="fff3c-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fff3c-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fff3c-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="fff3c-201">`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="fff3c-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fff3c-202">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="fff3c-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="fff3c-203">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="fff3c-203">Access token scopes</span></span>

<span data-ttu-id="fff3c-204">Области токена доступа по умолчанию представляют собой список областей токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="fff3c-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="fff3c-205">Включается по умолчанию в запросе на вход.</span><span class="sxs-lookup"><span data-stu-id="fff3c-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="fff3c-206">Используется для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="fff3c-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="fff3c-207">Все области должны принадлежать одному и тому же приложению для правил Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="fff3c-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="fff3c-208">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="fff3c-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="fff3c-209">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="fff3c-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="fff3c-210">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="fff3c-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="fff3c-211">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="fff3c-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="fff3c-212">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="fff3c-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="fff3c-213">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="fff3c-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="fff3c-214">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="fff3c-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="fff3c-215">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="fff3c-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="fff3c-216">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="fff3c-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="fff3c-217">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="fff3c-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="fff3c-218">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="fff3c-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="fff3c-219">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="fff3c-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="fff3c-220">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="fff3c-220">Run the app</span></span>

<span data-ttu-id="fff3c-221">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="fff3c-221">Run the app from the Server project.</span></span> <span data-ttu-id="fff3c-222">При использовании Visual Studio выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="fff3c-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fff3c-223">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fff3c-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="fff3c-224">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="fff3c-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="fff3c-225">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="fff3c-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
