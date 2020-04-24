---
title: Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111205"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="13e02-102">Защита автономного Blazor приложения ASP.NET Coreной сборки с помощью Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="13e02-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="13e02-103">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13e02-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="13e02-104">Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2.</span><span class="sxs-lookup"><span data-stu-id="13e02-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="13e02-105">Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.</span><span class="sxs-lookup"><span data-stu-id="13e02-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="13e02-106">Чтобы создать Blazor изолированное приложение для сборки, использующее [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="13e02-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="13e02-107">[Создание клиента AAD и веб-приложения](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="13e02-107">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="13e02-108">Зарегистрируйте приложение AAD в области **Azure Active Directory** > **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="13e02-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="13e02-109">Укажите **имя** приложения (например, \*\* Blazor AAD клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="13e02-109">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="13e02-110">Выберите **Поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="13e02-110">Choose a **Supported account types**.</span></span> <span data-ttu-id="13e02-111">Вы можете выбрать **учетные записи в этом каталоге организации только** для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="13e02-111">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="13e02-112">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите универсальный код ресурса ( `https://localhost:5001/authentication/login-callback`URI) перенаправления для.</span><span class="sxs-lookup"><span data-stu-id="13e02-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="13e02-113">Отключите **разрешения** > **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="13e02-113">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="13e02-114">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="13e02-114">Select **Register**.</span></span>

<span data-ttu-id="13e02-115">В > **конфигурации платформы** **проверки подлинности** > **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="13e02-115">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="13e02-116">Убедитесь, что `https://localhost:5001/authentication/login-callback` **URI перенаправления** имеется.</span><span class="sxs-lookup"><span data-stu-id="13e02-116">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="13e02-117">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="13e02-117">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="13e02-118">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="13e02-118">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="13e02-119">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="13e02-119">Select the **Save** button.</span></span>

<span data-ttu-id="13e02-120">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="13e02-120">Record the following information:</span></span>

* <span data-ttu-id="13e02-121">Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="13e02-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="13e02-122">Идентификатор каталога (идентификатор клиента) (например, `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="13e02-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="13e02-123">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="13e02-123">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="13e02-124">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="13e02-124">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="13e02-125">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="13e02-125">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="13e02-126">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="13e02-126">Authentication package</span></span>

<span data-ttu-id="13e02-127">При создании приложения для использования рабочих или учебных учетных записей`SingleOrg`приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="13e02-127">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="13e02-128">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="13e02-128">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="13e02-129">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="13e02-129">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="13e02-130">Замените `{VERSION}` в предыдущей ссылке на пакет версией `Microsoft.AspNetCore.Blazor.Templates` пакета, показанного в этой <xref:blazor/get-started> статье.</span><span class="sxs-lookup"><span data-stu-id="13e02-130">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="13e02-131">`Microsoft.Authentication.WebAssembly.Msal` Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="13e02-131">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="13e02-132">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="13e02-132">Authentication service support</span></span>

<span data-ttu-id="13e02-133">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с `AddMsalAuthentication` помощью метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="13e02-133">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="13e02-134">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком удостоверений (IP).</span><span class="sxs-lookup"><span data-stu-id="13e02-134">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="13e02-135">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="13e02-135">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="13e02-136">`AddMsalAuthentication` Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="13e02-136">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="13e02-137">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="13e02-137">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="13e02-138">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="13e02-138">Access token scopes</span></span>

<span data-ttu-id="13e02-139">Blazor Шаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="13e02-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="13e02-140">Чтобы подготавливать маркер в процессе входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="13e02-140">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="13e02-141">Если портал Azure предоставляет URI области, а **приложение создает необработанное исключение** при 401 получении от API *неавторизованного* ответа, попробуйте использовать URI области, который не включает схему и узел.</span><span class="sxs-lookup"><span data-stu-id="13e02-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="13e02-142">Например, портал Azure может предоставить один из следующих форматов URI области:</span><span class="sxs-lookup"><span data-stu-id="13e02-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="13e02-143">Укажите URI области без схемы и узла:</span><span class="sxs-lookup"><span data-stu-id="13e02-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="13e02-144">Для получения дополнительной информации см. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="13e02-144">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="13e02-145">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="13e02-145">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="13e02-146">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="13e02-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="13e02-147">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="13e02-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="13e02-148">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="13e02-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="13e02-149">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="13e02-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="13e02-150">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="13e02-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="13e02-151">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="13e02-151">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="13e02-152">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="13e02-152">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
