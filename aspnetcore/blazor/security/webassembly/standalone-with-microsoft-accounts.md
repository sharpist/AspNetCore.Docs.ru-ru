---
title: 'Защита изолированного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью учетных записей Майкрософт'
author: guardrex
description: 'Сведения о защите изолированного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью учетных записей Майкрософт.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6defd8c1ab7b99f69efe6e9ef3ba4da4e0e8d8fb
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690382"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="dfbe2-103">Защита изолированного приложения ASP.NET Core :::no-loc(Blazor WebAssembly)::: с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dfbe2-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="dfbe2-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="dfbe2-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dfbe2-105">Чтобы создать [изолированное приложение :::no-loc(Blazor WebAssembly):::](xref:blazor/hosting-models#blazor-webassembly), использующее [учетные записи Майкрософт с Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) для проверки подлинности, выполните приведенные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-105">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="dfbe2-106">Создайте клиент AAD и веб-приложение</span><span class="sxs-lookup"><span data-stu-id="dfbe2-106">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="dfbe2-107">Зарегистрируйте приложение AAD на портале Azure в разделе **Azure Active Directory** > **Регистрация приложений** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-107">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="dfbe2-108">Укажите **имя** приложения (например, **Изолированное приложение :::no-loc(Blazor)::: с учетными записями Майкрософт в AAD** ).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-108">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD Microsoft Accounts** ).</span></span>
1. <span data-ttu-id="dfbe2-109">В разделе **Поддерживаемые типы учетных записей** выберите **Учетные записи в любом каталоге организации** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-109">In **Supported account types** , select **Accounts in any organizational directory** .</span></span>
1. <span data-ttu-id="dfbe2-110">Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-110">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="dfbe2-111">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="dfbe2-112">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="dfbe2-113">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="dfbe2-114">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="dfbe2-115">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="dfbe2-116">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-116">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="dfbe2-117">Выберите **Зарегистрировать** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-117">Select **Register** .</span></span>

<span data-ttu-id="dfbe2-118">Запишите идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-118">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="dfbe2-119">В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-119">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="dfbe2-120">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-120">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="dfbe2-121">В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-121">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="dfbe2-122">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-122">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="dfbe2-123">Нажмите кнопку **Сохранить** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-123">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="dfbe2-124">Укажите **имя** приложения (например, **Изолированное приложение :::no-loc(Blazor)::: с учетными записями Майкрософт в AAD** ).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-124">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD Microsoft Accounts** ).</span></span>
1. <span data-ttu-id="dfbe2-125">В разделе **Поддерживаемые типы учетных записей** выберите **Учетные записи в любом каталоге организации** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-125">In **Supported account types** , select **Accounts in any organizational directory** .</span></span>
1. <span data-ttu-id="dfbe2-126">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-126">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="dfbe2-127">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-127">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="dfbe2-128">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-128">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="dfbe2-129">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-129">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="dfbe2-130">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-130">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="dfbe2-131">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-131">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="dfbe2-132">Снимите флажок **Разрешения** > **Предоставьте согласие администратора для разрешений openid и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-132">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="dfbe2-133">Выберите **Зарегистрировать** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-133">Select **Register** .</span></span>

<span data-ttu-id="dfbe2-134">Запишите идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-134">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="dfbe2-135">В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-135">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="dfbe2-136">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-136">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="dfbe2-137">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-137">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="dfbe2-138">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-138">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="dfbe2-139">Нажмите кнопку **Сохранить** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-139">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="dfbe2-140">Создайте приложение.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-140">Create the app.</span></span> <span data-ttu-id="dfbe2-141">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-141">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="dfbe2-142">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="dfbe2-142">Placeholder</span></span>   | <span data-ttu-id="dfbe2-143">Название на портале Azure</span><span class="sxs-lookup"><span data-stu-id="dfbe2-143">Azure portal name</span></span>       | <span data-ttu-id="dfbe2-144">Пример</span><span class="sxs-lookup"><span data-stu-id="dfbe2-144">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `:::no-loc(Blazor):::Sample`                         |
| `{CLIENT ID}` | <span data-ttu-id="dfbe2-145">Идентификатор приложения (клиента)</span><span class="sxs-lookup"><span data-stu-id="dfbe2-145">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="dfbe2-146">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-146">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="dfbe2-147">На портале Azure **URI перенаправления** в конфигурации платформы приложений настроен для использования порта 5001 для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-147">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="dfbe2-148">Если приложение выполняется на случайном порту IIS Express, порт для приложения указан в свойствах приложения на панели **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="dfbe2-148">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="dfbe2-149">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-149">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="dfbe2-150">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-150">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="dfbe2-151">выполнять вход в приложение, используя учетную запись Майкрософт;</span><span class="sxs-lookup"><span data-stu-id="dfbe2-151">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="dfbe2-152">запрашивать маркеры доступа для API Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-152">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="dfbe2-153">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="dfbe2-153">For more information, see:</span></span>
  * [<span data-ttu-id="dfbe2-154">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="dfbe2-154">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="dfbe2-155">[Краткое руководство. Настройка приложения для предоставления интерфейсов веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-155">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="dfbe2-156">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dfbe2-156">Authentication package</span></span>

<span data-ttu-id="dfbe2-157">Когда приложение создается для использования рабочих или учебных учетных записей (`SingleOrg`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-157">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="dfbe2-158">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-158">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="dfbe2-159">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-159">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="dfbe2-160">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-160">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="dfbe2-161">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-161">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="dfbe2-162">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dfbe2-162">Authentication service support</span></span>

<span data-ttu-id="dfbe2-163">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-163">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="dfbe2-164">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком :::no-loc(Identity)::: (IP).</span><span class="sxs-lookup"><span data-stu-id="dfbe2-164">This method sets up all of the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="dfbe2-165">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-165">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="dfbe2-166">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-166">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="dfbe2-167">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-167">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="dfbe2-168">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-168">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="dfbe2-169">Пример.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-169">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="dfbe2-170">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="dfbe2-170">Access token scopes</span></span>

<span data-ttu-id="dfbe2-171">Шаблон :::no-loc(Blazor WebAssembly)::: не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="dfbe2-171">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="dfbe2-172">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера доступа по умолчанию <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-172">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="dfbe2-173">Укажите дополнительные области с помощью `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="dfbe2-173">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="dfbe2-174">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="dfbe2-174">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="dfbe2-175">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="dfbe2-175">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="dfbe2-176">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="dfbe2-176">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="dfbe2-177">Режим входа</span><span class="sxs-lookup"><span data-stu-id="dfbe2-177">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="dfbe2-178">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="dfbe2-178">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="dfbe2-179">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="dfbe2-179">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="dfbe2-180">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="dfbe2-180">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="dfbe2-181">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="dfbe2-181">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="dfbe2-182">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="dfbe2-182">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="dfbe2-183">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dfbe2-183">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="dfbe2-184">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dfbe2-184">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="dfbe2-185">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dfbe2-185">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="dfbe2-186">Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dfbe2-186">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="dfbe2-187">Краткое руководство. Настройка приложения для предоставления веб-API</span><span class="sxs-lookup"><span data-stu-id="dfbe2-187">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
