---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6f884cbfc9ac50f38c415af522d3d09a3ef38712
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976848"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="dfd16-102">Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dfd16-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="dfd16-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dfd16-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="dfd16-104">Чтобы создать изолированное приложение для веб- Blazor сборки, использующее [учетные записи Майкрософт с Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="dfd16-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="dfd16-105">Создание клиента AAD и веб-приложения</span><span class="sxs-lookup"><span data-stu-id="dfd16-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="dfd16-106">Зарегистрируйте приложение AAD в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="dfd16-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="dfd16-107">1\.</span><span class="sxs-lookup"><span data-stu-id="dfd16-107">1\.</span></span> <span data-ttu-id="dfd16-108">Укажите **имя** приложения (например, \*\* Blazor AAD клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="dfd16-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="dfd16-109">2\.</span><span class="sxs-lookup"><span data-stu-id="dfd16-109">2\.</span></span> <span data-ttu-id="dfd16-110">В списке **Поддерживаемые типы учетных записей**выберите **учетные записи в любом организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="dfd16-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="dfd16-111">3 \.</span><span class="sxs-lookup"><span data-stu-id="dfd16-111">3\.</span></span> <span data-ttu-id="dfd16-112">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.</span><span class="sxs-lookup"><span data-stu-id="dfd16-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="dfd16-113">4 \.</span><span class="sxs-lookup"><span data-stu-id="dfd16-113">4\.</span></span> <span data-ttu-id="dfd16-114">Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="dfd16-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="dfd16-115">5 \.</span><span class="sxs-lookup"><span data-stu-id="dfd16-115">5\.</span></span> <span data-ttu-id="dfd16-116">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="dfd16-116">Select **Register**.</span></span>

   <span data-ttu-id="dfd16-117">В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="dfd16-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="dfd16-118">1\.</span><span class="sxs-lookup"><span data-stu-id="dfd16-118">1\.</span></span> <span data-ttu-id="dfd16-119">Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.</span><span class="sxs-lookup"><span data-stu-id="dfd16-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="dfd16-120">2\.</span><span class="sxs-lookup"><span data-stu-id="dfd16-120">2\.</span></span> <span data-ttu-id="dfd16-121">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="dfd16-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="dfd16-122">3 \.</span><span class="sxs-lookup"><span data-stu-id="dfd16-122">3\.</span></span> <span data-ttu-id="dfd16-123">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="dfd16-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="dfd16-124">4 \.</span><span class="sxs-lookup"><span data-stu-id="dfd16-124">4\.</span></span> <span data-ttu-id="dfd16-125">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="dfd16-125">Select the **Save** button.</span></span>

   <span data-ttu-id="dfd16-126">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="dfd16-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="dfd16-127">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="dfd16-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="dfd16-128">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="dfd16-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="dfd16-129">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="dfd16-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="dfd16-130">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="dfd16-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="dfd16-131">Войдите в приложение, используя учетную запись Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="dfd16-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="dfd16-132">Запросите маркеры доступа для API-интерфейсов Майкрософт, используя Blazor тот же подход, что и для автономных приложений, при условии, что приложение настроено правильно.</span><span class="sxs-lookup"><span data-stu-id="dfd16-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="dfd16-133">Дополнительные сведения см. [в разделе Краткое руководство. Настройка приложения для предоставления веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="dfd16-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="dfd16-134">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dfd16-134">Authentication package</span></span>

<span data-ttu-id="dfd16-135">При создании приложения для использования рабочих или учебных учетных записей`SingleOrg`приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="dfd16-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="dfd16-136">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="dfd16-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="dfd16-137">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="dfd16-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="dfd16-138">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="dfd16-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="dfd16-139">`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="dfd16-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="dfd16-140">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dfd16-140">Authentication service support</span></span>

<span data-ttu-id="dfd16-141">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="dfd16-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="dfd16-142">Этот метод настраивает все службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="dfd16-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="dfd16-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="dfd16-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="dfd16-144">`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="dfd16-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="dfd16-145">Значения, необходимые для настройки приложения, можно получить из конфигурации учетных записей Майкрософт при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="dfd16-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="dfd16-146">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="dfd16-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="dfd16-147">Пример</span><span class="sxs-lookup"><span data-stu-id="dfd16-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="dfd16-148">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="dfd16-148">Access token scopes</span></span>

<span data-ttu-id="dfd16-149">Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="dfd16-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="dfd16-150">Чтобы настроить маркер доступа как часть потока входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="dfd16-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="dfd16-151">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="dfd16-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="dfd16-152">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="dfd16-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="dfd16-153">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="dfd16-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="dfd16-154">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="dfd16-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="dfd16-155">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="dfd16-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="dfd16-156">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="dfd16-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="dfd16-157">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="dfd16-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="dfd16-158">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="dfd16-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="dfd16-159">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="dfd16-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="dfd16-160">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="dfd16-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="dfd16-161">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="dfd16-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="dfd16-162">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dfd16-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="dfd16-163">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dfd16-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="dfd16-164">Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dfd16-164">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="dfd16-165">Краткое руководство. Настройка приложения для предоставления веб-API</span><span class="sxs-lookup"><span data-stu-id="dfd16-165">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
