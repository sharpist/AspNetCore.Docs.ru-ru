---
<span data-ttu-id="7b2ad-101">Title: ' Защитите Blazor изолированное приложение ASP.NET Coreной сборки с помощью Azure Active Directory B2C "author: описание: моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="7b2ad-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7b2ad-102">'Blazor'</span></span>
- <span data-ttu-id="7b2ad-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7b2ad-103">'Identity'</span></span>
- <span data-ttu-id="7b2ad-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7b2ad-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7b2ad-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7b2ad-105">'Razor'</span></span>
- <span data-ttu-id="7b2ad-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="7b2ad-107">Защита Blazor автономного приложения ASP.NET Coreной сборки с помощью Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="7b2ad-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="7b2ad-108">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7b2ad-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7b2ad-109">Чтобы создать Blazor изолированное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="7b2ad-110">Следуйте указаниям в следующих разделах, чтобы создать клиент и зарегистрировать веб-приложение на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="7b2ad-111">Создание клиента AAD B2C</span><span class="sxs-lookup"><span data-stu-id="7b2ad-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="7b2ad-112">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-112">Record the following information:</span></span>

* <span data-ttu-id="7b2ad-113">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/` , включающий косую черту).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="7b2ad-114">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="7b2ad-115">Чтобы зарегистрировать приложение AAD для *клиентского приложения*, следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) .</span><span class="sxs-lookup"><span data-stu-id="7b2ad-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="7b2ad-116">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="7b2ad-117">Укажите **имя** приложения (например, \*\* Blazor автономный AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="7b2ad-118">Для **поддерживаемых типов учетных записей**выберите параметр несколько клиентов: **учетные записи в любом каталоге организации или в любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="7b2ad-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="7b2ad-119">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб-сайт** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7b2ad-119">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7b2ad-120">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7b2ad-121">Если приложение выполняется на другом порту Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-121">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7b2ad-122">Для IIS Express созданный случайным образом порт для приложения можно найти в свойствах приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="7b2ad-122">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7b2ad-123">Так как на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-123">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7b2ad-124">В этом разделе появится замечание, напоминающее IIS Express пользователям обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-124">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7b2ad-125">Убедитесь, что **разрешения**  >  **на предоставление разрешений администратора OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-125">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="7b2ad-126">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-126">Select **Register**.</span></span>

<span data-ttu-id="7b2ad-127">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-127">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="7b2ad-128">В конфигурации платформы **проверки подлинности**  >  **Platform configurations**  >  **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-128">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="7b2ad-129">Убедитесь, что **URI перенаправления** `https://localhost:{PORT}/authentication/login-callback` имеется.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-129">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7b2ad-130">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-130">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7b2ad-131">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-131">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7b2ad-132">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-132">Select the **Save** button.</span></span>

<span data-ttu-id="7b2ad-133">В **домашних**  >  **Azure AD B2C**  >  **пользовательских потоках**:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-133">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="7b2ad-134">Создание потока пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="7b2ad-134">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="7b2ad-135">**Application claims**  >  Для заполнения**Display Name** `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя "отображаемое имя утверждения приложения".</span><span class="sxs-lookup"><span data-stu-id="7b2ad-135">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="7b2ad-136">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-136">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="7b2ad-137">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-137">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="7b2ad-138">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-138">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7b2ad-139">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-139">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7b2ad-140">В портал Azure Конфигурация платформы **проверки подлинности**приложения  >  **Platform configurations**  >  **Web**  >  **URI перенаправления** настроена для порта 5001 для приложений, работающих на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-140">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7b2ad-141">Если приложение выполняется на случайном IIS Express порту, порт для приложения можно найти в свойствах приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="7b2ad-141">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7b2ad-142">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения в портал Azure и обновите URI перенаправления с правильным портом.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-142">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="7b2ad-143">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-143">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="7b2ad-144">Войдите в приложение, используя учетную запись пользователя AAD.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-144">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="7b2ad-145">Запрос маркеров доступа для API-интерфейсов Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-145">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="7b2ad-146">Дополнительные сведения можно найти в разделе</span><span class="sxs-lookup"><span data-stu-id="7b2ad-146">For more information, see:</span></span>
  * [<span data-ttu-id="7b2ad-147">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="7b2ad-147">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="7b2ad-148">[Краткое руководство. Настройка приложения для предоставления веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-148">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7b2ad-149">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7b2ad-149">Authentication package</span></span>

<span data-ttu-id="7b2ad-150">При создании приложения для использования отдельной учетной записи B2C ( `IndividualB2C` ) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-150">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="7b2ad-151">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-151">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7b2ad-152">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-152">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="7b2ad-153">`Microsoft.Authentication.WebAssembly.Msal`Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-153">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7b2ad-154">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7b2ad-154">Authentication service support</span></span>

<span data-ttu-id="7b2ad-155">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью `AddMsalAuthentication` метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-155">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="7b2ad-156">Этот метод настраивает все службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="7b2ad-156">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7b2ad-157">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b2ad-157">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="7b2ad-158">`AddMsalAuthentication`Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-158">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7b2ad-159">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-159">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="7b2ad-160">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="7b2ad-160">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="7b2ad-161">Пример.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-161">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="7b2ad-162">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="7b2ad-162">Access token scopes</span></span>

<span data-ttu-id="7b2ad-163">BlazorШаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="7b2ad-163">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7b2ad-164">Чтобы настроить маркер доступа как часть потока входа, добавьте область в область маркера доступа по умолчанию для `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="7b2ad-164">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="7b2ad-165">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="7b2ad-165">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7b2ad-166">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="7b2ad-166">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7b2ad-167">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="7b2ad-167">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="7b2ad-168">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="7b2ad-168">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7b2ad-169">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="7b2ad-169">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="7b2ad-170">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="7b2ad-170">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7b2ad-171">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="7b2ad-171">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7b2ad-172">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="7b2ad-172">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7b2ad-173">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="7b2ad-173">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7b2ad-174">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7b2ad-174">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="7b2ad-175">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7b2ad-175">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="7b2ad-176">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="7b2ad-176">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="7b2ad-177">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="7b2ad-177">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
