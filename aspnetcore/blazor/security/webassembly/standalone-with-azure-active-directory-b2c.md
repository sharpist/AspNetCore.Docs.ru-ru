---
title: 'Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C'
author: guardrex
description: 'Узнайте, как защитить изолированное приложение ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 14eda03419e22538e17b7b4d6fa697d61cb384c8
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343715"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e532d-103">Защита изолированного приложения ASP.NET Core Blazor WebAssembly с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="e532d-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e532d-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="e532d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e532d-105">В этой статье описывается, как создать [изолированное приложение Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="e532d-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

<span data-ttu-id="e532d-106">Создайте арендатор или укажите существующий арендатор B2C для приложения на портале Azure, выполнив инструкции из статьи [Создание арендатора AAD B2C (документация Azure)](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="e532d-106">Create a tenant or identify an existing B2C tenant for the app to use in the Azure portal by following the guidance in the [Create an AAD B2C tenant (Azure documentation)](/azure/active-directory-b2c/tutorial-create-tenant) article.</span></span>

<span data-ttu-id="e532d-107">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="e532d-107">Record the following information:</span></span>

* <span data-ttu-id="e532d-108">Экземпляр AAD B2C (например, `https://contoso.b2clogin.com/`, который включает косую черту в конце): Экземпляр — это схема и узел регистрации приложения Azure B2C, которую можно найти в окне **Конечные точки** на странице **Регистрации приложений** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="e532d-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="e532d-109">Основной домен AAD B2C/домен издателя/домен клиента (например, `contoso.onmicrosoft.com`): домен доступен в качестве **домена издателя** в колонке **Фирменная символика** на портале Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="e532d-109">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="e532d-110">Зарегистрируйте приложение AAD B2C (соответствующие инструкции см. в документации Azure: [Учебник. Регистрация приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)):</span><span class="sxs-lookup"><span data-stu-id="e532d-110">Register an AAD B2C app (related guidance in the Azure documentation: [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)):</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="e532d-111">В разделе **Azure Active Directory**  > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="e532d-111">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="e532d-112">Укажите **имя** приложения (например, **BlazorИзолированный AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="e532d-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="e532d-113">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом каталоге организации или поставщике удостоверений. Выберите этот вариант для проверки подлинности в Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e532d-113">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="e532d-114">Выберите в раскрывающемся списке **URI перенаправления** значение **Одностраничное приложение (SPA)** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="e532d-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e532d-115">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="e532d-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e532d-116">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="e532d-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e532d-117">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="e532d-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e532d-118">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="e532d-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e532d-119">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="e532d-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e532d-120">Убедитесь, что в разделе **Разрешения** установлен флажок **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="e532d-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="e532d-121">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="e532d-121">Select **Register**.</span></span>

<span data-ttu-id="e532d-122">Запишите идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="e532d-122">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="e532d-123">В разделе **Аутентификация** > **Конфигурации платформ** > **Одностраничное приложение (SPA)** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="e532d-123">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="e532d-124">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="e532d-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e532d-125">В разделе **Неявное предоставление разрешения** убедитесь, что флажки **Маркеры доступа** и **Токены идентификаторов** **не** установлены.</span><span class="sxs-lookup"><span data-stu-id="e532d-125">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="e532d-126">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e532d-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e532d-127">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="e532d-127">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="e532d-128">В разделе **Azure Active Directory**  > **Регистрация приложений** выберите **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="e532d-128">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="e532d-129">Укажите **имя** приложения (например, **BlazorИзолированный AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="e532d-129">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="e532d-130">Для параметра **Поддерживаемые типы учетных записей** выберите вариант с использованием нескольких клиентов: **Учетные записи в любом каталоге организации или поставщике удостоверений. Выберите этот вариант для проверки подлинности в Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="e532d-130">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="e532d-131">В раскрывающемся списке **URI перенаправления** оставьте значение **Интернет** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="e532d-131">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e532d-132">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="e532d-132">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e532d-133">Если приложение выполняется на другом порте Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="e532d-133">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e532d-134">Для IIS Express созданный случайным образом порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="e532d-134">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e532d-135">Поскольку на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="e532d-135">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e532d-136">В этом разделе появится замечание с напоминанием для пользователей IIS Express о необходимости обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="e532d-136">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e532d-137">Убедитесь, что в разделе **Разрешения** установлен флажок **Предоставьте согласие администратора для разрешений openid и offline_access**.</span><span class="sxs-lookup"><span data-stu-id="e532d-137">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="e532d-138">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="e532d-138">Select **Register**.</span></span>

<span data-ttu-id="e532d-139">Запишите идентификатор приложения (клиента) (например, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="e532d-139">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="e532d-140">В разделе **Аутентификация** > **Конфигурации платформ** > **Интернет** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="e532d-140">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="e532d-141">Убедитесь в том, что параметру **URI-перенаправления** присвоено значение `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="e532d-141">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e532d-142">В разделе **Неявное предоставление разрешения** установите флажки **Маркеры доступа** и **Токен идентификатора**.</span><span class="sxs-lookup"><span data-stu-id="e532d-142">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e532d-143">Для остальных параметров можно оставить значения по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e532d-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e532d-144">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="e532d-144">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="e532d-145">В разделе **Главная** > **Azure AD B2C** > **Потоки пользователя** выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="e532d-145">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="e532d-146">Создайте поток пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="e532d-146">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e532d-147">Чтобы заполнить `context.User.Identity.Name` в компоненте `LoginDisplay` (`Shared/LoginDisplay.razor`), выберите хотя бы атрибут пользователя **Утверждения приложения** > **Отображаемое имя**.</span><span class="sxs-lookup"><span data-stu-id="e532d-147">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="e532d-148">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="e532d-148">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="e532d-149">В пустой папке замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="e532d-149">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="e532d-150">Заполнитель</span><span class="sxs-lookup"><span data-stu-id="e532d-150">Placeholder</span></span>                   | <span data-ttu-id="e532d-151">Название на портале Azure</span><span class="sxs-lookup"><span data-stu-id="e532d-151">Azure portal name</span></span>               | <span data-ttu-id="e532d-152">Пример</span><span class="sxs-lookup"><span data-stu-id="e532d-152">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="e532d-153">Экземпляр</span><span class="sxs-lookup"><span data-stu-id="e532d-153">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="e532d-154">Идентификатор приложения (клиента)</span><span class="sxs-lookup"><span data-stu-id="e532d-154">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="e532d-155">Поток регистрации/входа пользователей</span><span class="sxs-lookup"><span data-stu-id="e532d-155">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="e532d-156">Основной домен/домен издателя/домен клиента</span><span class="sxs-lookup"><span data-stu-id="e532d-156">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="e532d-157">Расположение выходных данных, указанное в параметре `-o|--output`, создает папку проекта, если она не существует, и внедряется в имя приложения.</span><span class="sxs-lookup"><span data-stu-id="e532d-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e532d-158">На портале Azure **URI перенаправления** в конфигурации платформы приложений настроен для использования порта 5001 для приложений, которые выполняются на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="e532d-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e532d-159">Если приложение выполняется на случайном порту IIS Express, порт для приложения указан в свойствах приложения на панели **Отладка**.</span><span class="sxs-lookup"><span data-stu-id="e532d-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e532d-160">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения на портале Azure и обновите URI перенаправления, указав правильный порт.</span><span class="sxs-lookup"><span data-stu-id="e532d-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="e532d-161">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="e532d-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e532d-162">Входить в приложение, используя учетную запись пользователя AAD.</span><span class="sxs-lookup"><span data-stu-id="e532d-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="e532d-163">Запрашивать маркеры доступа для API Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="e532d-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="e532d-164">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="e532d-164">For more information, see:</span></span>
  * [<span data-ttu-id="e532d-165">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="e532d-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="e532d-166">[Краткое руководство. Настройка приложения для предоставления интерфейсов веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="e532d-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e532d-167">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="e532d-167">Authentication package</span></span>

<span data-ttu-id="e532d-168">Когда приложение создается для использования отдельной учетной записи B2C (`IndividualB2C`), оно автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="e532d-168">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="e532d-169">В пакете содержится набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных API.</span><span class="sxs-lookup"><span data-stu-id="e532d-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e532d-170">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="e532d-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="e532d-171">Информацию для заполнителя `{VERSION}`, последнюю стабильную версию пакета, соответствующую версии общей платформы приложения, можно найти в **журнале версий** пакета на сайте [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="e532d-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="e532d-172">Пакет [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) транзитивно добавляет пакет [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) в приложение.</span><span class="sxs-lookup"><span data-stu-id="e532d-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e532d-173">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="e532d-173">Authentication service support</span></span>

<span data-ttu-id="e532d-174">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью метода расширения <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>, предоставляемого в пакете [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="e532d-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="e532d-175">Этот метод настраивает все службы, необходимые для взаимодействия приложения с поставщиком Identity (IP).</span><span class="sxs-lookup"><span data-stu-id="e532d-175">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e532d-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="e532d-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="e532d-177">Метод <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="e532d-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e532d-178">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="e532d-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="e532d-179">Конфигурация предоставлена в файле `wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="e532d-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="e532d-180">Пример.</span><span class="sxs-lookup"><span data-stu-id="e532d-180">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="e532d-181">Области маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="e532d-181">Access token scopes</span></span>

<span data-ttu-id="e532d-182">Шаблон Blazor WebAssembly не обеспечивает автоматическую настройку приложения для запроса маркера доступа для защищенного API.</span><span class="sxs-lookup"><span data-stu-id="e532d-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e532d-183">Чтобы настроить маркер доступа как часть потока входа, добавьте область в области маркера доступа по умолчанию <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="e532d-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e532d-184">Укажите дополнительные области с помощью `AdditionalScopesToConsent`:</span><span class="sxs-lookup"><span data-stu-id="e532d-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="e532d-185">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="e532d-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e532d-186">Запрашивание дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="e532d-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e532d-187">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="e532d-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="e532d-188">Режим входа</span><span class="sxs-lookup"><span data-stu-id="e532d-188">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="e532d-189">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="e532d-189">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e532d-190">Страница индексов</span><span class="sxs-lookup"><span data-stu-id="e532d-190">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e532d-191">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="e532d-191">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e532d-192">Компонент RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="e532d-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e532d-193">Компонент LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="e532d-193">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e532d-194">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="e532d-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e532d-195">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e532d-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="e532d-196">Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="e532d-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* <span data-ttu-id="e532d-197">[Учебник. Создание клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="e532d-197">[Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant)</span></span>
* [<span data-ttu-id="e532d-198">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="e532d-198">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
