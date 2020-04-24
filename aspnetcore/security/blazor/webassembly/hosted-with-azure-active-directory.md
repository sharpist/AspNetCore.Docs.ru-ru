---
title: Обеспечение безопасности размещенного в ASP.NET Core Blazor приложения сборки Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110932"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="82657-102">Обеспечение безопасности размещенного в ASP.NET Core Blazor приложения сборки Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="82657-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="82657-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="82657-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="82657-104">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="82657-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="82657-105">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="82657-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="82657-106">В этой статье описывается создание [ Blazor размещенного приложения сборки](xref:blazor/hosting-models#blazor-webassembly) , которое использует [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="82657-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="82657-107">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="82657-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="82657-108">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="82657-108">Create a tenant</span></span>

<span data-ttu-id="82657-109">Следуйте указаниям в [кратком руководстве по настройке клиента](/azure/active-directory/develop/quickstart-create-new-tenant) для создания клиента в AAD.</span><span class="sxs-lookup"><span data-stu-id="82657-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="82657-110">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="82657-110">Register a server API app</span></span>

<span data-ttu-id="82657-111">Следуйте указаниям в [кратком руководстве: регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделов Azure AAD, чтобы зарегистрировать приложение AAD для *приложения API сервера* в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="82657-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="82657-112">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="82657-112">Select **New registration**.</span></span>
1. <span data-ttu-id="82657-113">Укажите **имя** приложения (например, \*\* Blazor сервер AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="82657-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="82657-114">Выберите **Поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="82657-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="82657-115">Для этого интерфейса можно выбрать **учетные записи только в этом каталоге Организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="82657-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="82657-116">В этом сценарии *приложению API сервера* не требуется **универсальный код ресурса (URI) перенаправления** , поэтому оставьте в раскрывающемся списке значение **Web** и не вводите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="82657-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="82657-117">Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="82657-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="82657-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="82657-118">Select **Register**.</span></span>

<span data-ttu-id="82657-119">В окне **разрешения API**удалите разрешение **Microsoft Graph** > **пользователь. чтение** , так как приложению не требуется доступ для входа или профиля уер.</span><span class="sxs-lookup"><span data-stu-id="82657-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="82657-120">В **предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="82657-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="82657-121">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="82657-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="82657-122">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="82657-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="82657-123">Укажите **имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="82657-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="82657-124">Укажите **Отображаемое имя согласия администратора** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="82657-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="82657-125">Введите **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="82657-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="82657-126">Убедитесь, что для **состояния** задано значение **включено**.</span><span class="sxs-lookup"><span data-stu-id="82657-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="82657-127">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="82657-127">Select **Add scope**.</span></span>

<span data-ttu-id="82657-128">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="82657-128">Record the following information:</span></span>

* <span data-ttu-id="82657-129">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="82657-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="82657-130">URI идентификатора приложения (например `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, или предоставленное вами пользовательское значение)</span><span class="sxs-lookup"><span data-stu-id="82657-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="82657-131">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="82657-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="82657-132">Домен клиента AAD (например, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="82657-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="82657-133">Область по умолчанию (например `API.Access`,)</span><span class="sxs-lookup"><span data-stu-id="82657-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="82657-134">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="82657-134">Register a client app</span></span>

<span data-ttu-id="82657-135">Следуйте указаниям в [кратком руководстве: регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделов Azure AAD, чтобы зарегистрировать приложение AAD для *клиентского приложения* в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="82657-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="82657-136">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="82657-136">Select **New registration**.</span></span>
1. <span data-ttu-id="82657-137">Укажите **имя** приложения (например, \*\* Blazor AAD клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="82657-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="82657-138">Выберите **Поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="82657-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="82657-139">Для этого интерфейса можно выбрать **учетные записи только в этом каталоге Организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="82657-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="82657-140">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.</span><span class="sxs-lookup"><span data-stu-id="82657-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="82657-141">Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="82657-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="82657-142">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="82657-142">Select **Register**.</span></span>

<span data-ttu-id="82657-143">В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="82657-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="82657-144">Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.</span><span class="sxs-lookup"><span data-stu-id="82657-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="82657-145">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="82657-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="82657-146">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="82657-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="82657-147">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="82657-147">Select the **Save** button.</span></span>

<span data-ttu-id="82657-148">В **разрешениях API**:</span><span class="sxs-lookup"><span data-stu-id="82657-148">In **API permissions**:</span></span>

1. <span data-ttu-id="82657-149">Убедитесь, что приложение имеет **Microsoft Graph** > **пользователь. чтение** .</span><span class="sxs-lookup"><span data-stu-id="82657-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="82657-150">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="82657-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="82657-151">Выберите *приложение API сервера* из столбца **имя** (например, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="82657-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="82657-152">Откройте список **API** .</span><span class="sxs-lookup"><span data-stu-id="82657-152">Open the **API** list.</span></span>
1. <span data-ttu-id="82657-153">Разрешение доступа к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="82657-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="82657-154">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="82657-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="82657-155">Нажмите кнопку **предоставить содержимое администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="82657-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="82657-156">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="82657-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="82657-157">Запишите идентификатор приложения *клиентского приложения* (идентификатор клиента) (например, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="82657-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="82657-158">Создайте приложение</span><span class="sxs-lookup"><span data-stu-id="82657-158">Create the app</span></span>

<span data-ttu-id="82657-159">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="82657-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="82657-160">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="82657-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="82657-161">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="82657-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="82657-162">Передайте универсальный код ресурса (URI `app-id-uri` ) идентификатора приложения в параметр, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, которое описано в разделе [области действия маркера доступа](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="82657-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="82657-163">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="82657-163">Server app configuration</span></span>

<span data-ttu-id="82657-164">*Этот раздел относится к **серверному** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="82657-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="82657-165">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="82657-165">Authentication package</span></span>

<span data-ttu-id="82657-166">Поддержка проверки подлинности и авторизации вызовов ASP.NET Core веб-интерфейсов API обеспечивается `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="82657-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="82657-167">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="82657-167">Authentication service support</span></span>

<span data-ttu-id="82657-168">`AddAuthentication` Метод настраивает службы проверки подлинности в приложении и настраивает обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="82657-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="82657-169">`AddAzureADBearer` Метод настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, созданных Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="82657-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="82657-170">`UseAuthentication`и `UseAuthorization` убедитесь, что:</span><span class="sxs-lookup"><span data-stu-id="82657-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="82657-171">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="82657-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="82657-172">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="82657-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="82657-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="82657-173">User.Identity.Name</span></span>

<span data-ttu-id="82657-174">По умолчанию API серверного приложения заполняет `User.Identity.Name` значение значением из типа `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` утверждения (например, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="82657-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="82657-175">Чтобы настроить приложение для получения значения `name` из типа утверждения, настройте [TokenValidationParameters. намеклаимтипе](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="82657-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="82657-176">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="82657-176">App settings</span></span>

<span data-ttu-id="82657-177">Файл *appSettings. JSON* содержит параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="82657-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="82657-178">Контроллер Веасерфорекаст</span><span class="sxs-lookup"><span data-stu-id="82657-178">WeatherForecast controller</span></span>

<span data-ttu-id="82657-179">Контроллер Веасерфорекаст (*Controllers/веасерфорекастконтроллер. CS*) предоставляет защищенный API с `[Authorize]` атрибутом, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="82657-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="82657-180">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="82657-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="82657-181">`[Authorize]` Атрибут в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="82657-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="82657-182">`[Authorize]` Атрибут, используемый в Blazor приложении сборки, служит указанием для приложения, которое должно быть проверено для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="82657-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="82657-183">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="82657-183">Client app configuration</span></span>

<span data-ttu-id="82657-184">*Этот раздел относится к **клиентскому** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="82657-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="82657-185">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="82657-185">Authentication package</span></span>

<span data-ttu-id="82657-186">При создании приложения для использования рабочих или учебных учетных записей`SingleOrg`приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="82657-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="82657-187">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="82657-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="82657-188">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="82657-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="82657-189">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="82657-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="82657-190">`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="82657-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="82657-191">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="82657-191">Authentication service support</span></span>

<span data-ttu-id="82657-192">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="82657-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="82657-193">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).</span><span class="sxs-lookup"><span data-stu-id="82657-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="82657-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="82657-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="82657-195">`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="82657-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="82657-196">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="82657-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="82657-197">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="82657-197">Access token scopes</span></span>

<span data-ttu-id="82657-198">Области токена доступа по умолчанию представляют собой список областей токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="82657-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="82657-199">Включается по умолчанию в запросе на вход.</span><span class="sxs-lookup"><span data-stu-id="82657-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="82657-200">Используется для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="82657-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="82657-201">Все области должны принадлежать одному и тому же приложению для правил Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="82657-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="82657-202">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="82657-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="82657-203">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="82657-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="82657-204">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="82657-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="82657-205">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="82657-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="82657-206">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="82657-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="82657-207">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="82657-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="82657-208">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="82657-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="82657-209">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="82657-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="82657-210">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="82657-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="82657-211">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="82657-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="82657-212">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="82657-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="82657-213">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="82657-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="82657-214">Запустите приложение</span><span class="sxs-lookup"><span data-stu-id="82657-214">Run the app</span></span>

<span data-ttu-id="82657-215">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="82657-215">Run the app from the Server project.</span></span> <span data-ttu-id="82657-216">При использовании Visual Studio выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="82657-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="82657-217">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="82657-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="82657-218">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="82657-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
