---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111166"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="f382d-102">Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="f382d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="f382d-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f382d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="f382d-104">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="f382d-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="f382d-105">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="f382d-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="f382d-106">Чтобы создать Blazor изолированное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="f382d-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="f382d-107">Следуйте указаниям в следующих разделах, чтобы создать клиент и зарегистрировать веб-приложение на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="f382d-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="f382d-108">[Создайте клиент](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; AAD B2C запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="f382d-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="f382d-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="f382d-109">1\.</span></span> <span data-ttu-id="f382d-110">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/`, включающий косую черту)</span><span class="sxs-lookup"><span data-stu-id="f382d-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="f382d-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="f382d-111">2\.</span></span> <span data-ttu-id="f382d-112">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="f382d-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="f382d-113">[Регистрация веб-приложения](/azure/active-directory-b2c/tutorial-register-applications) &ndash; во время регистрации приложения делает следующее:</span><span class="sxs-lookup"><span data-stu-id="f382d-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="f382d-114">1 \.</span><span class="sxs-lookup"><span data-stu-id="f382d-114">1\.</span></span> <span data-ttu-id="f382d-115">Задайте для параметра **веб-приложение или веб-API** значение **Да**.</span><span class="sxs-lookup"><span data-stu-id="f382d-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="f382d-116">2 \.</span><span class="sxs-lookup"><span data-stu-id="f382d-116">2\.</span></span> <span data-ttu-id="f382d-117">Установите для параметра **Разрешить неявный поток** значение **Да**.</span><span class="sxs-lookup"><span data-stu-id="f382d-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="f382d-118">3 \.</span><span class="sxs-lookup"><span data-stu-id="f382d-118">3\.</span></span> <span data-ttu-id="f382d-119">Добавьте **URL-адрес ответа** `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="f382d-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="f382d-120">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="f382d-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="f382d-121">[Создание потоков](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; пользователя создание потока входа и входа пользователя в систему.</span><span class="sxs-lookup"><span data-stu-id="f382d-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="f382d-122">Для заполнения `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя " > **Отображаемое имя** **утверждения приложения**".</span><span class="sxs-lookup"><span data-stu-id="f382d-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="f382d-123">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="f382d-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="f382d-124">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="f382d-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="f382d-125">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="f382d-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f382d-126">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="f382d-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f382d-127">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="f382d-127">Authentication package</span></span>

<span data-ttu-id="f382d-128">При создании приложения для использования отдельной учетной записи B2C (`IndividualB2C`) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="f382d-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f382d-129">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="f382d-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f382d-130">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="f382d-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="f382d-131">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="f382d-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="f382d-132">`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="f382d-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f382d-133">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="f382d-133">Authentication service support</span></span>

<span data-ttu-id="f382d-134">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="f382d-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f382d-135">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).</span><span class="sxs-lookup"><span data-stu-id="f382d-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f382d-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f382d-136">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="f382d-137">`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="f382d-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f382d-138">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="f382d-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="f382d-139">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="f382d-139">Access token scopes</span></span>

<span data-ttu-id="f382d-140">Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="f382d-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f382d-141">Чтобы подготавливать маркер в процессе входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="f382d-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="f382d-142">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="f382d-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="f382d-143">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="f382d-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="f382d-144">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="f382d-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="f382d-145">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="f382d-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="f382d-146">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="f382d-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f382d-147">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="f382d-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="f382d-148">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="f382d-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f382d-149">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="f382d-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f382d-150">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="f382d-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f382d-151">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="f382d-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f382d-152">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="f382d-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="f382d-153">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="f382d-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="f382d-154">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="f382d-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
