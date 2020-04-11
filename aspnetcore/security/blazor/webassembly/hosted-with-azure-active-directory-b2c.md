---
title: Защищайте Blazor ASP.NET Core WebAssembly размещает приложение с Помощью Активного каталога Azure B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123471"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e8fa8-102">Защищайте Blazor ASP.NET Core WebAssembly размещает приложение с Помощью Активного каталога Azure B2C</span><span class="sxs-lookup"><span data-stu-id="e8fa8-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e8fa8-103">[Хавьер Кальварро Нельсон](https://github.com/javiercn) и Люк [Лэйтам](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e8fa8-104">В этой статье описывается, как создать автономное приложение Blazor WebAssembly, использующее для проверки подлинности [Azure Active Directory (AAD) B2C.](/azure/active-directory-b2c/overview)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e8fa8-105">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="e8fa8-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e8fa8-106">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="e8fa8-106">Create a tenant</span></span>

<span data-ttu-id="e8fa8-107">Следуйте инструкциям в [учебном центре: Создайте арендатора Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) для создания арендатора AAD B2C и записи следующей информации:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="e8fa8-108">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/`который включает в себя задний слэш)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="e8fa8-109">AAD B2C Арендатор домена `contoso.onmicrosoft.com`(например, )</span><span class="sxs-lookup"><span data-stu-id="e8fa8-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e8fa8-110">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="e8fa8-110">Register a server API app</span></span>

<span data-ttu-id="e8fa8-111">Следуйте инструкциям в [учебном центре: Зарегистрируйте приложение в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) для регистрации приложения AAD для *приложения Server API* в зоне**регистрации приложений** **Azure Active Directory** > на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e8fa8-112">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-112">Select **New registration**.</span></span>
1. <span data-ttu-id="e8fa8-113">Укажите **имя** приложения (например, \*\* Blazor Server AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="e8fa8-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e8fa8-114">Для **типов поддерживаемых учетных записей**выберите **учетные записи в любом каталоге организации или любом поставщике идентификационных данных. Для аутентификации пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e8fa8-115">(мультитенант) для этого опыта.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e8fa8-116">*Приложение Server API* не требует **перенаправления URI** в этом сценарии, поэтому оставьте упаж вниз в **Веб** и не вводите перенаправление URI.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e8fa8-117">Подтвердите, **Permissions** > что разрешение**на админ-концентратор для openid и offline_access разрешений** включено.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e8fa8-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-118">Select **Register**.</span></span>

<span data-ttu-id="e8fa8-119">В **экспозиции API**:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="e8fa8-120">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e8fa8-121">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e8fa8-122">Укажите **имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e8fa8-123">Укажите **имя отображения согласия админа** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e8fa8-124">Укажите **описание согласия админа** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e8fa8-125">Подтвердите, что **государство** настроено на **включено.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e8fa8-126">Выберите **область добавления.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-126">Select **Add scope**.</span></span>

<span data-ttu-id="e8fa8-127">Запишите следующую информацию:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-127">Record the following information:</span></span>

* <span data-ttu-id="e8fa8-128">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e8fa8-129">App ID URI (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`или пользовательское значение, которое вы предоставили)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e8fa8-130">Идентификатор каталога (идентификатор арендатора) (например, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e8fa8-131">*Приложение API сервера* APP ID URI (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`портал Azure может по умолчанию значение для идентификатора клиента)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="e8fa8-132">Сфера действия по `API.Access`умолчанию (например, )</span><span class="sxs-lookup"><span data-stu-id="e8fa8-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e8fa8-133">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="e8fa8-133">Register a client app</span></span>

<span data-ttu-id="e8fa8-134">Следуйте инструкциям в [учебном центре: Зарегистрируйте приложение в Azure Active Directory B2C,](/azure/active-directory-b2c/tutorial-register-applications) чтобы снова зарегистрировать приложение AAD для *приложения Клиента* в зоне**регистрации приложений** **Azure Active Directory** > на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e8fa8-135">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-135">Select **New registration**.</span></span>
1. <span data-ttu-id="e8fa8-136">Укажите **имя** приложения (например, \*\* Blazor Client AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="e8fa8-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="e8fa8-137">Для **типов поддерживаемых учетных записей**выберите **учетные записи в любом каталоге организации или любом поставщике идентификационных данных. Для аутентификации пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e8fa8-138">(мультитенант) для этого опыта.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e8fa8-139">Оставьте **перенаправить URI** упасть набор в **Интернет**, `https://localhost:5001/authentication/login-callback`и обеспечить перенаправление URI .</span><span class="sxs-lookup"><span data-stu-id="e8fa8-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e8fa8-140">Подтвердите, **Permissions** > что разрешение**на админ-концентратор для openid и offline_access разрешений** включено.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e8fa8-141">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-141">Select **Register**.</span></span>

<span data-ttu-id="e8fa8-142">В**настройках платформы** >  **аутентификации** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e8fa8-143">Подтвердите, `https://localhost:5001/authentication/login-callback` что **redirect URI** присутствует.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e8fa8-144">Для **неявного гранта**выберите флажки для **токенов Access** и **токенов ID.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e8fa8-145">Остальные по умолчанию для приложения приемлемы для этого опыта.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e8fa8-146">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-146">Select the **Save** button.</span></span>

<span data-ttu-id="e8fa8-147">В **разрешениях API:**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-147">In **API permissions**:</span></span>

1. <span data-ttu-id="e8fa8-148">Подтвердите, что приложение имеет разрешение **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e8fa8-149">Выберите **Добавить разрешение,** за которым следуют **мои AA.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e8fa8-150">Выберите *приложение Server API* из столбца **«Имя»** (например, \*\* Blazor Server AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="e8fa8-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e8fa8-151">Откройте список **API.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-151">Open the **API** list.</span></span>
1. <span data-ttu-id="e8fa8-152">Включить доступ к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e8fa8-153">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e8fa8-154">Выберите **содержимое администратора Гранта для кнопки «TENANT NAME».**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e8fa8-155">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e8fa8-156">В **домашних** > **Azure AD B2C** > **Потоки пользователей:**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e8fa8-157">Создание потока пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="e8fa8-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e8fa8-158">Как минимум, выберите атрибут **приложения Претензии** > **Display Name** для заполнения `context.User.Identity.Name` `LoginDisplay` компонента *(Общий/LoginDisplay.razor*).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="e8fa8-159">Запишите следующую информацию:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-159">Record the following information:</span></span>

* <span data-ttu-id="e8fa8-160">Запись идентификатора *приложения клиента* (например, `33333333-3333-3333-3333-333333333333`ID клиента).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="e8fa8-161">Запись регистрации и входе в пользовательское имя, созданное `B2C_1_signupsignin`для приложения (например, ).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e8fa8-162">Создайте приложение</span><span class="sxs-lookup"><span data-stu-id="e8fa8-162">Create the app</span></span>

<span data-ttu-id="e8fa8-163">Замените заполнителей в следующей команде информацией, записанной ранее, и выполните команду в командном корпусе:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e8fa8-164">Чтобы указать местона вывода, которое создает папку проекта, если она не существует, включите опцию вывода в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e8fa8-165">Имя папки также становится частью названия проекта.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e8fa8-166">Передайте опцион App `app-id-uri` ID URI, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, описанное в разделе [прицелов доступа.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="e8fa8-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e8fa8-167">Конфигурация приложения сервера</span><span class="sxs-lookup"><span data-stu-id="e8fa8-167">Server app configuration</span></span>

<span data-ttu-id="e8fa8-168">*Этот раздел относится к приложению **Server** решения.*</span><span class="sxs-lookup"><span data-stu-id="e8fa8-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e8fa8-169">Пакет аутентификации</span><span class="sxs-lookup"><span data-stu-id="e8fa8-169">Authentication package</span></span>

<span data-ttu-id="e8fa8-170">Поддержка аутентификации и авторизации вызовов ASP.NET базовых `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`Web AIS обеспечивается:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e8fa8-171">Поддержка службы аутентификации</span><span class="sxs-lookup"><span data-stu-id="e8fa8-171">Authentication service support</span></span>

<span data-ttu-id="e8fa8-172">Метод `AddAuthentication` настраивает службы аутентификации в приложении и настраивает обработчик JWT Bearer как метод аутентификации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e8fa8-173">Метод `AddAzureADB2CBearer` устанавливает определенные параметры в обработчике JWT Bearer, необходимые для проверки токенов, испускаемых B2C Active Directory:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="e8fa8-174">`UseAuthentication`и `UseAuthorization` обеспечить, чтобы:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e8fa8-175">Приложение пытается разобрать и проверить токены на входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e8fa8-176">Любой запрос, пытающихся получить доступ к защищенного ресурсу без надлежащих учетных данных, завершается неудачей.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="e8fa8-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="e8fa8-177">User.Identity.Name</span></span>

<span data-ttu-id="e8fa8-178">По умолчанию, `User.Identity.Name` не заселен.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="e8fa8-179">Чтобы настроить приложение для получения значения `name` из типа претензии, назначь [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="e8fa8-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e8fa8-180">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="e8fa8-180">App settings</span></span>

<span data-ttu-id="e8fa8-181">Файл *appsettings.json* содержит параметры настройки обработчика носителя JWT, используемого для проверки токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="e8fa8-182">Контроллер WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="e8fa8-182">WeatherForecast controller</span></span>

<span data-ttu-id="e8fa8-183">Контроллер WeatherForecast *(Контроллеры/WeatherForecastController.cs*) предоставляет защищенный `[Authorize]` API с атрибутом, применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e8fa8-184">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="e8fa8-185">Атрибут `[Authorize]` в этом контроллере API является единственной вещью, которая защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e8fa8-186">Атрибут, `[Authorize]` используемый Blazor в приложении WebAssembly, служит только намеком на приложение о том, что пользователь должен быть авторизован для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e8fa8-187">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="e8fa8-187">Client app configuration</span></span>

<span data-ttu-id="e8fa8-188">*Этот раздел относится к **клиенту** приложения решения.*</span><span class="sxs-lookup"><span data-stu-id="e8fa8-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e8fa8-189">Пакет аутентификации</span><span class="sxs-lookup"><span data-stu-id="e8fa8-189">Authentication package</span></span>

<span data-ttu-id="e8fa8-190">Когда приложение создается для использования индивидуальной`IndividualB2C`учетной записи B2C ( ), приложение`Microsoft.Authentication.WebAssembly.Msal`автоматически получает ссылку на пакет для [библиотеки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e8fa8-191">Пакет предоставляет набор примитивов, которые помогают приложению аутентифицировать пользователей и получать токены для вызова защищенных AIS.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e8fa8-192">При добавлении аутентификации в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e8fa8-193">Замените `{VERSION}` в предыдущем пакете ссылку на версию `Microsoft.AspNetCore.Blazor.Templates` пакета, показанную <xref:blazor/get-started> в статье.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e8fa8-194">Пакет `Microsoft.Authentication.WebAssembly.Msal` транзитно добавляет `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e8fa8-195">Поддержка службы аутентификации</span><span class="sxs-lookup"><span data-stu-id="e8fa8-195">Authentication service support</span></span>

<span data-ttu-id="e8fa8-196">Поддержка аутентификации пользователей регистрируется `AddMsalAuthentication` в сервисном `Microsoft.Authentication.WebAssembly.Msal` контейнере с методом расширения, предусмотренным пакетом.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e8fa8-197">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком идентификационных данных (IP).</span><span class="sxs-lookup"><span data-stu-id="e8fa8-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e8fa8-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-198">*Program.cs*:</span></span>

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

<span data-ttu-id="e8fa8-199">Метод `AddMsalAuthentication` принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e8fa8-200">Значения, необходимые для настройки приложения, можно получить из конфигурации Azure Portal AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="e8fa8-201">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="e8fa8-201">Access token scopes</span></span>

<span data-ttu-id="e8fa8-202">Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e8fa8-203">Включен по умолчанию в знак в запросе.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e8fa8-204">Используется для предоставления токена доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e8fa8-205">Все области должны принадлежать к одному и тому же приложению в правилах Active Directory Azure.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e8fa8-206">Дополнительные области могут быть добавлены для дополнительных приложений API по мере необходимости:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e8fa8-207">Если портал Azure предоставляет область URI и **приложение выбрасывает необработанное исключение,** когда получает *401 несанкционированный* ответ от API, попробуйте использовать область URI, которая не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e8fa8-208">Например, портал Azure может предоставить один из следующих форматов URI:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e8fa8-209">Поставка области URI без схемы и хозяина:</span><span class="sxs-lookup"><span data-stu-id="e8fa8-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e8fa8-210">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="e8fa8-211">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="e8fa8-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e8fa8-212">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="e8fa8-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e8fa8-213">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="e8fa8-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e8fa8-214">ПеренаправлениеКомпонентToLogin</span><span class="sxs-lookup"><span data-stu-id="e8fa8-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e8fa8-215">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e8fa8-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e8fa8-216">Компонент аутентификации</span><span class="sxs-lookup"><span data-stu-id="e8fa8-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e8fa8-217">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="e8fa8-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e8fa8-218">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="e8fa8-218">Run the app</span></span>

<span data-ttu-id="e8fa8-219">Выполнить приложение из проекта Server.</span><span class="sxs-lookup"><span data-stu-id="e8fa8-219">Run the app from the Server project.</span></span> <span data-ttu-id="e8fa8-220">При использовании Visual Studio выберите проект Server в **Solution Explorer** и выберите кнопку **Run** в панели инструментов или запустите приложение из меню **Debug.**</span><span class="sxs-lookup"><span data-stu-id="e8fa8-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e8fa8-221">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e8fa8-221">Additional resources</span></span>

* [<span data-ttu-id="e8fa8-222">Запрос дополнительных токенов доступа</span><span class="sxs-lookup"><span data-stu-id="e8fa8-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e8fa8-223">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e8fa8-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e8fa8-224">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="e8fa8-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
