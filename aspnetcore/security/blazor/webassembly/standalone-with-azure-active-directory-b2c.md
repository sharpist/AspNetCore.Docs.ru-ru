---
title: Защита Blazor автономного приложения ASP.NET Coreной сборки с помощью Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 059947888653c05a062ec5e5849d8087cd01f475
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153600"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="1f783-102">Защита Blazor автономного приложения ASP.NET Coreной сборки с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="1f783-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="1f783-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1f783-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1f783-104">Чтобы создать Blazor изолированное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="1f783-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="1f783-105">Следуйте указаниям в следующих разделах, чтобы создать клиент и зарегистрировать веб-приложение на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="1f783-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="1f783-106">[Создание клиента AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="1f783-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="1f783-107">1\.</span><span class="sxs-lookup"><span data-stu-id="1f783-107">1\.</span></span> <span data-ttu-id="1f783-108">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/` , включающий косую черту)</span><span class="sxs-lookup"><span data-stu-id="1f783-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="1f783-109">2\.</span><span class="sxs-lookup"><span data-stu-id="1f783-109">2\.</span></span> <span data-ttu-id="1f783-110">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="1f783-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="1f783-111">[Регистрация веб-приложения](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Во время регистрации приложения сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="1f783-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="1f783-112">1\.</span><span class="sxs-lookup"><span data-stu-id="1f783-112">1\.</span></span> <span data-ttu-id="1f783-113">Задайте для параметра **веб-приложение или веб-API** значение **Да**.</span><span class="sxs-lookup"><span data-stu-id="1f783-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="1f783-114">2\.</span><span class="sxs-lookup"><span data-stu-id="1f783-114">2\.</span></span> <span data-ttu-id="1f783-115">Установите для параметра **Разрешить неявный поток** значение **Да**.</span><span class="sxs-lookup"><span data-stu-id="1f783-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="1f783-116">3 \.</span><span class="sxs-lookup"><span data-stu-id="1f783-116">3\.</span></span> <span data-ttu-id="1f783-117">Добавьте **URL-адрес ответа** `https://localhost:5001/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="1f783-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="1f783-118">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="1f783-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="1f783-119">[Создание потоков пользователя](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Создайте пользовательский поток регистрации и входа.</span><span class="sxs-lookup"><span data-stu-id="1f783-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="1f783-120">**Application claims**  >  Для заполнения**Display Name** `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя "отображаемое имя утверждения приложения".</span><span class="sxs-lookup"><span data-stu-id="1f783-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="1f783-121">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="1f783-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="1f783-122">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="1f783-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="1f783-123">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="1f783-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1f783-124">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="1f783-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="1f783-125">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="1f783-125">Authentication package</span></span>

<span data-ttu-id="1f783-126">При создании приложения для использования отдельной учетной записи B2C ( `IndividualB2C` ) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="1f783-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1f783-127">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="1f783-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1f783-128">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="1f783-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1f783-129">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="1f783-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1f783-130">`Microsoft.Authentication.WebAssembly.Msal`Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="1f783-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="1f783-131">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="1f783-131">Authentication service support</span></span>

<span data-ttu-id="1f783-132">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью `AddMsalAuthentication` метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="1f783-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1f783-133">Этот метод настраивает все службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="1f783-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1f783-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1f783-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="1f783-135">`AddMsalAuthentication`Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="1f783-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1f783-136">Значения, необходимые для настройки приложения, можно получить из конфигурации учетных записей Майкрософт при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="1f783-136">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="1f783-137">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="1f783-137">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="1f783-138">Пример</span><span class="sxs-lookup"><span data-stu-id="1f783-138">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="1f783-139">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="1f783-139">Access token scopes</span></span>

<span data-ttu-id="1f783-140">BlazorШаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="1f783-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="1f783-141">Чтобы настроить маркер доступа как часть потока входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="1f783-141">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1f783-142">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="1f783-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1f783-143">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="1f783-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1f783-144">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="1f783-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1f783-145">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="1f783-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="1f783-146">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="1f783-146">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="1f783-147">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="1f783-147">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="1f783-148">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="1f783-148">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="1f783-149">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="1f783-149">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="1f783-150">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="1f783-150">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="1f783-151">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="1f783-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="1f783-152">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="1f783-152">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="1f783-153">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="1f783-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1f783-154">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="1f783-154">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="1f783-155">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="1f783-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="1f783-156">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="1f783-156">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="1f783-157">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="1f783-157">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
