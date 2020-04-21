---
title: Защищайте Blazor ASP.NET приложение Core WebAssembly с помощью active-каталога Azure Active
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661832"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="35061-102">Защищайте Blazor ASP.NET приложение Core WebAssembly с помощью active-каталога Azure Active</span><span class="sxs-lookup"><span data-stu-id="35061-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="35061-103">[Хавьер Кальварро Нельсон](https://github.com/javiercn) и Люк [Лэйтам](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="35061-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="35061-104">В этой статье описывается, как создать [ Blazor приложение WebAssembly,](xref:blazor/hosting-models#blazor-webassembly) которое использует [Активный каталог Azure (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="35061-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="35061-105">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="35061-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="35061-106">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="35061-106">Create a tenant</span></span>

<span data-ttu-id="35061-107">Следуйте инструкциям в [компании «Быстрый старт»: навладите арендатора](/azure/active-directory/develop/quickstart-create-new-tenant) для создания арендатора в AAD.</span><span class="sxs-lookup"><span data-stu-id="35061-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="35061-108">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="35061-108">Register a server API app</span></span>

<span data-ttu-id="35061-109">Следуйте инструкциям в [компании «Быстрый старт»: зарегистрируйте приложение с платформой Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующие темы Azure AAD для регистрации приложения AAD для *приложения Server API* в области**регистрации приложений** **Active Directory** > На портале Azure:</span><span class="sxs-lookup"><span data-stu-id="35061-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="35061-110">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="35061-110">Select **New registration**.</span></span>
1. <span data-ttu-id="35061-111">Укажите **имя** приложения (например, \*\* Blazor Server AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="35061-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="35061-112">Выберите **типы учетной записи Поддержки.**</span><span class="sxs-lookup"><span data-stu-id="35061-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="35061-113">Вы можете выбрать **учетные записи только в этом каталоге организации** (один арендатор) для этого опыта.</span><span class="sxs-lookup"><span data-stu-id="35061-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="35061-114">*Приложение Server API* не требует **перенаправления URI** в этом сценарии, поэтому оставьте упаж вниз в **Веб** и не вводите перенаправление URI.</span><span class="sxs-lookup"><span data-stu-id="35061-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="35061-115">Отключите > **админ-концентратор** **Разрешений**Гранта для проверки открытых и offline_access разрешений.</span><span class="sxs-lookup"><span data-stu-id="35061-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="35061-116">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="35061-116">Select **Register**.</span></span>

<span data-ttu-id="35061-117">В **разрешении API**удалите разрешение **Microsoft Graph** > **User.Read,** так как приложение не требует входного доступа к профилю.</span><span class="sxs-lookup"><span data-stu-id="35061-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="35061-118">В **экспозиции API**:</span><span class="sxs-lookup"><span data-stu-id="35061-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="35061-119">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="35061-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="35061-120">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="35061-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="35061-121">Укажите **имя области** (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="35061-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="35061-122">Укажите **имя отображения согласия админа** (например, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="35061-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="35061-123">Укажите **описание согласия админа** (например, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="35061-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="35061-124">Подтвердите, что **государство** настроено на **включено.**</span><span class="sxs-lookup"><span data-stu-id="35061-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="35061-125">Выберите **область добавления.**</span><span class="sxs-lookup"><span data-stu-id="35061-125">Select **Add scope**.</span></span>

<span data-ttu-id="35061-126">Запишите следующую информацию:</span><span class="sxs-lookup"><span data-stu-id="35061-126">Record the following information:</span></span>

* <span data-ttu-id="35061-127">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="35061-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="35061-128">App ID URI (например, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`или пользовательское значение, которое вы предоставили)</span><span class="sxs-lookup"><span data-stu-id="35061-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="35061-129">Идентификатор каталога (идентификатор арендатора) (например, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="35061-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="35061-130">AAD Арендатор домен `contoso.onmicrosoft.com`(например, )</span><span class="sxs-lookup"><span data-stu-id="35061-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="35061-131">Сфера действия по `API.Access`умолчанию (например, )</span><span class="sxs-lookup"><span data-stu-id="35061-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="35061-132">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="35061-132">Register a client app</span></span>

<span data-ttu-id="35061-133">Следуйте инструкциям в [компании «Быстрый старт»: зарегистрируйте приложение с платформой Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующие темы Azure AAD для регистрации приложения AAD для *приложения Клиента* в зоне**регистрации приложений** **Active Directory** > Azure на портале Azure:</span><span class="sxs-lookup"><span data-stu-id="35061-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="35061-134">Выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="35061-134">Select **New registration**.</span></span>
1. <span data-ttu-id="35061-135">Укажите **имя** приложения (например, \*\* Blazor Client AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="35061-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="35061-136">Выберите **типы учетной записи Поддержки.**</span><span class="sxs-lookup"><span data-stu-id="35061-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="35061-137">Вы можете выбрать **учетные записи только в этом каталоге организации** (один арендатор) для этого опыта.</span><span class="sxs-lookup"><span data-stu-id="35061-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="35061-138">Оставьте **перенаправить URI** упасть набор в **Интернет**, `https://localhost:5001/authentication/login-callback`и обеспечить перенаправление URI .</span><span class="sxs-lookup"><span data-stu-id="35061-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="35061-139">Отключите > **админ-концентратор** **Разрешений**Гранта для проверки открытых и offline_access разрешений.</span><span class="sxs-lookup"><span data-stu-id="35061-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="35061-140">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="35061-140">Select **Register**.</span></span>

<span data-ttu-id="35061-141">В**настройках платформы** >  **аутентификации** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="35061-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="35061-142">Подтвердите, `https://localhost:5001/authentication/login-callback` что **redirect URI** присутствует.</span><span class="sxs-lookup"><span data-stu-id="35061-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="35061-143">Для **неявного гранта**выберите флажки для **токенов Access** и **токенов ID.**</span><span class="sxs-lookup"><span data-stu-id="35061-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="35061-144">Остальные по умолчанию для приложения приемлемы для этого опыта.</span><span class="sxs-lookup"><span data-stu-id="35061-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="35061-145">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="35061-145">Select the **Save** button.</span></span>

<span data-ttu-id="35061-146">В **разрешениях API:**</span><span class="sxs-lookup"><span data-stu-id="35061-146">In **API permissions**:</span></span>

1. <span data-ttu-id="35061-147">Подтвердите, что приложение имеет разрешение **Microsoft Graph** > **User.Read.**</span><span class="sxs-lookup"><span data-stu-id="35061-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="35061-148">Выберите **Добавить разрешение,** за которым следуют **мои AA.**</span><span class="sxs-lookup"><span data-stu-id="35061-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="35061-149">Выберите *приложение Server API* из столбца **«Имя»** (например, \*\* Blazor Server AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="35061-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="35061-150">Откройте список **API.**</span><span class="sxs-lookup"><span data-stu-id="35061-150">Open the **API** list.</span></span>
1. <span data-ttu-id="35061-151">Включить доступ к API (например, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="35061-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="35061-152">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="35061-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="35061-153">Выберите **содержимое администратора Гранта для кнопки «TENANT NAME».**</span><span class="sxs-lookup"><span data-stu-id="35061-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="35061-154">Выберите **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="35061-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="35061-155">Запись идентификатора *приложения клиента* (например, `33333333-3333-3333-3333-333333333333`ID клиента).</span><span class="sxs-lookup"><span data-stu-id="35061-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="35061-156">Создайте приложение</span><span class="sxs-lookup"><span data-stu-id="35061-156">Create the app</span></span>

<span data-ttu-id="35061-157">Замените заполнителей в следующей команде информацией, записанной ранее, и выполните команду в командном корпусе:</span><span class="sxs-lookup"><span data-stu-id="35061-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="35061-158">Чтобы указать местона вывода, которое создает папку проекта, если она не существует, включите опцию вывода в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="35061-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="35061-159">Имя папки также становится частью названия проекта.</span><span class="sxs-lookup"><span data-stu-id="35061-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="35061-160">Передайте опцион App `app-id-uri` ID URI, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, описанное в разделе [прицелов доступа.](#access-token-scopes)</span><span class="sxs-lookup"><span data-stu-id="35061-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="35061-161">Конфигурация приложения сервера</span><span class="sxs-lookup"><span data-stu-id="35061-161">Server app configuration</span></span>

<span data-ttu-id="35061-162">*Этот раздел относится к приложению **Server** решения.*</span><span class="sxs-lookup"><span data-stu-id="35061-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="35061-163">Пакет аутентификации</span><span class="sxs-lookup"><span data-stu-id="35061-163">Authentication package</span></span>

<span data-ttu-id="35061-164">Поддержка аутентификации и авторизации вызовов ASP.NET базовых `Microsoft.AspNetCore.Authentication.AzureAD.UI`Web AIS обеспечивается:</span><span class="sxs-lookup"><span data-stu-id="35061-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="35061-165">Поддержка службы аутентификации</span><span class="sxs-lookup"><span data-stu-id="35061-165">Authentication service support</span></span>

<span data-ttu-id="35061-166">Метод `AddAuthentication` настраивает службы аутентификации в приложении и настраивает обработчик JWT Bearer как метод аутентификации по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="35061-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="35061-167">Метод `AddAzureADBearer` устанавливает определенные параметры в обработчике JWT Bearer, необходимые для проверки токенов, испускаемых Active Directory:</span><span class="sxs-lookup"><span data-stu-id="35061-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="35061-168">`UseAuthentication`и `UseAuthorization` обеспечить, чтобы:</span><span class="sxs-lookup"><span data-stu-id="35061-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="35061-169">Приложение пытается разобрать и проверить токены на входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="35061-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="35061-170">Любой запрос, пытающихся получить доступ к защищенного ресурсу без надлежащих учетных данных, завершается неудачей.</span><span class="sxs-lookup"><span data-stu-id="35061-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="35061-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="35061-171">User.Identity.Name</span></span>

<span data-ttu-id="35061-172">По умолчанию API приложения `User.Identity.Name` Server заполняется `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` значением из типа `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`претензии (например, ).</span><span class="sxs-lookup"><span data-stu-id="35061-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="35061-173">Чтобы настроить приложение для получения значения `name` из типа претензии, назначь [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="35061-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="35061-174">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="35061-174">App settings</span></span>

<span data-ttu-id="35061-175">Файл *appsettings.json* содержит параметры настройки обработчика носителя JWT, используемого для проверки токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="35061-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="35061-176">Контроллер WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="35061-176">WeatherForecast controller</span></span>

<span data-ttu-id="35061-177">Контроллер WeatherForecast *(Контроллеры/WeatherForecastController.cs*) предоставляет защищенный `[Authorize]` API с атрибутом, применяемым к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="35061-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="35061-178">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="35061-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="35061-179">Атрибут `[Authorize]` в этом контроллере API является единственной вещью, которая защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="35061-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="35061-180">Атрибут, `[Authorize]` используемый Blazor в приложении WebAssembly, служит только намеком на приложение о том, что пользователь должен быть авторизован для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="35061-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="35061-181">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="35061-181">Client app configuration</span></span>

<span data-ttu-id="35061-182">*Этот раздел относится к **клиенту** приложения решения.*</span><span class="sxs-lookup"><span data-stu-id="35061-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="35061-183">Пакет аутентификации</span><span class="sxs-lookup"><span data-stu-id="35061-183">Authentication package</span></span>

<span data-ttu-id="35061-184">Когда приложение создается для использования рабочих`SingleOrg`или школьных учетных записей ( ),`Microsoft.Authentication.WebAssembly.Msal`приложение автоматически получает ссылку на пакет для [библиотеки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ().</span><span class="sxs-lookup"><span data-stu-id="35061-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="35061-185">Пакет предоставляет набор примитивов, которые помогают приложению аутентифицировать пользователей и получать токены для вызова защищенных AIS.</span><span class="sxs-lookup"><span data-stu-id="35061-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="35061-186">При добавлении аутентификации в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="35061-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="35061-187">Замените `{VERSION}` в предыдущем пакете ссылку на версию `Microsoft.AspNetCore.Blazor.Templates` пакета, показанную <xref:blazor/get-started> в статье.</span><span class="sxs-lookup"><span data-stu-id="35061-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="35061-188">Пакет `Microsoft.Authentication.WebAssembly.Msal` транзитно добавляет `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="35061-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="35061-189">Поддержка службы аутентификации</span><span class="sxs-lookup"><span data-stu-id="35061-189">Authentication service support</span></span>

<span data-ttu-id="35061-190">Поддержка аутентификации пользователей регистрируется `AddMsalAuthentication` в сервисном `Microsoft.Authentication.WebAssembly.Msal` контейнере с методом расширения, предусмотренным пакетом.</span><span class="sxs-lookup"><span data-stu-id="35061-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="35061-191">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком идентификационных данных (IP).</span><span class="sxs-lookup"><span data-stu-id="35061-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="35061-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="35061-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="35061-193">Метод `AddMsalAuthentication` принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="35061-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="35061-194">Значения, необходимые для настройки приложения, можно получить из конфигурации Azure Portal AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="35061-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="35061-195">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="35061-195">Access token scopes</span></span>

<span data-ttu-id="35061-196">Области маркеров доступа по умолчанию представляют собой список областей маркеров доступа, которые:</span><span class="sxs-lookup"><span data-stu-id="35061-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="35061-197">Включен по умолчанию в знак в запросе.</span><span class="sxs-lookup"><span data-stu-id="35061-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="35061-198">Используется для предоставления токена доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="35061-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="35061-199">Все области должны принадлежать к одному и тому же приложению в правилах Active Directory Azure.</span><span class="sxs-lookup"><span data-stu-id="35061-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="35061-200">Дополнительные области могут быть добавлены для дополнительных приложений API по мере необходимости:</span><span class="sxs-lookup"><span data-stu-id="35061-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="35061-201">Если портал Azure предоставляет область URI и **приложение выбрасывает необработанное исключение,** когда получает *401 несанкционированный* ответ от API, попробуйте использовать область URI, которая не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="35061-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="35061-202">Например, портал Azure может предоставить один из следующих форматов URI:</span><span class="sxs-lookup"><span data-stu-id="35061-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="35061-203">Поставка области URI без схемы и хозяина:</span><span class="sxs-lookup"><span data-stu-id="35061-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="35061-204">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="35061-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="35061-205">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="35061-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="35061-206">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="35061-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="35061-207">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="35061-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="35061-208">ПеренаправлениеКомпонентToLogin</span><span class="sxs-lookup"><span data-stu-id="35061-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="35061-209">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="35061-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="35061-210">Компонент аутентификации</span><span class="sxs-lookup"><span data-stu-id="35061-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="35061-211">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="35061-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="35061-212">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="35061-212">Run the app</span></span>

<span data-ttu-id="35061-213">Выполнить приложение из проекта Server.</span><span class="sxs-lookup"><span data-stu-id="35061-213">Run the app from the Server project.</span></span> <span data-ttu-id="35061-214">При использовании Visual Studio выберите проект Server в **Solution Explorer** и выберите кнопку **Run** в панели инструментов или запустите приложение из меню **Debug.**</span><span class="sxs-lookup"><span data-stu-id="35061-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="35061-215">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="35061-215">Additional resources</span></span>

* [<span data-ttu-id="35061-216">Запрос дополнительных токенов доступа</span><span class="sxs-lookup"><span data-stu-id="35061-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="35061-217">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="35061-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
