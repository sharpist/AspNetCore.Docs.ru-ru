---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111192"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="83b0b-102">Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="83b0b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="83b0b-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="83b0b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="83b0b-104">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="83b0b-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="83b0b-105">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="83b0b-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="83b0b-106">Чтобы создать изолированное приложение для веб- Blazor сборки, использующее [учетные записи Майкрософт с Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="83b0b-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="83b0b-107">Создание клиента AAD и веб-приложения</span><span class="sxs-lookup"><span data-stu-id="83b0b-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="83b0b-108">Зарегистрируйте приложение AAD в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="83b0b-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="83b0b-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-109">1\.</span></span> <span data-ttu-id="83b0b-110">Укажите **имя** приложения (например, \*\* Blazor AAD клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="83b0b-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="83b0b-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-111">2\.</span></span> <span data-ttu-id="83b0b-112">В списке **Поддерживаемые типы учетных записей**выберите **учетные записи в любом организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="83b0b-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="83b0b-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-113">3\.</span></span> <span data-ttu-id="83b0b-114">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.</span><span class="sxs-lookup"><span data-stu-id="83b0b-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="83b0b-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-115">4\.</span></span> <span data-ttu-id="83b0b-116">Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="83b0b-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="83b0b-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-117">5\.</span></span> <span data-ttu-id="83b0b-118">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="83b0b-118">Select **Register**.</span></span>

   <span data-ttu-id="83b0b-119">В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="83b0b-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="83b0b-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-120">1\.</span></span> <span data-ttu-id="83b0b-121">Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.</span><span class="sxs-lookup"><span data-stu-id="83b0b-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="83b0b-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-122">2\.</span></span> <span data-ttu-id="83b0b-123">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="83b0b-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="83b0b-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-124">3\.</span></span> <span data-ttu-id="83b0b-125">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="83b0b-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="83b0b-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="83b0b-126">4\.</span></span> <span data-ttu-id="83b0b-127">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="83b0b-127">Select the **Save** button.</span></span>

   <span data-ttu-id="83b0b-128">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="83b0b-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="83b0b-129">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="83b0b-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="83b0b-130">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="83b0b-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="83b0b-131">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="83b0b-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="83b0b-132">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="83b0b-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="83b0b-133">Войдите в приложение, используя учетную запись Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="83b0b-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="83b0b-134">Запросите маркеры доступа для API-интерфейсов Майкрософт, используя Blazor тот же подход, что и для автономных приложений, при условии, что приложение настроено правильно.</span><span class="sxs-lookup"><span data-stu-id="83b0b-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="83b0b-135">Дополнительные сведения см. [в разделе Краткое руководство. Настройка приложения для предоставления веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="83b0b-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="83b0b-136">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="83b0b-136">Authentication package</span></span>

<span data-ttu-id="83b0b-137">При создании приложения для использования рабочих или учебных учетных записей`SingleOrg`приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="83b0b-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="83b0b-138">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="83b0b-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="83b0b-139">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="83b0b-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="83b0b-140">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="83b0b-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="83b0b-141">`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="83b0b-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="83b0b-142">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="83b0b-142">Authentication service support</span></span>

<span data-ttu-id="83b0b-143">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="83b0b-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="83b0b-144">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).</span><span class="sxs-lookup"><span data-stu-id="83b0b-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="83b0b-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="83b0b-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="83b0b-146">`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="83b0b-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="83b0b-147">Значения, необходимые для настройки приложения, можно получить из конфигурации учетных записей Майкрософт при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="83b0b-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="83b0b-148">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="83b0b-148">Access token scopes</span></span>

<span data-ttu-id="83b0b-149">Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="83b0b-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="83b0b-150">Чтобы подготавливать маркер в процессе входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="83b0b-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="83b0b-151">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="83b0b-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="83b0b-152">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="83b0b-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="83b0b-153">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="83b0b-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="83b0b-154">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="83b0b-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="83b0b-155">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="83b0b-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="83b0b-156">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="83b0b-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="83b0b-157">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="83b0b-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="83b0b-158">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="83b0b-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="83b0b-159">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="83b0b-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="83b0b-160">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="83b0b-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="83b0b-161">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="83b0b-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="83b0b-162">Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="83b0b-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="83b0b-163">Краткое руководство. Настройка приложения для предоставления веб-API</span><span class="sxs-lookup"><span data-stu-id="83b0b-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
