---
<span data-ttu-id="a10d3-101">Title: ' безопасное ASP.NET Core Blazor размещенное приложение сборки с помощью Azure Active Directory ' author: описание: моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="a10d3-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a10d3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a10d3-102">'Blazor'</span></span>
- <span data-ttu-id="a10d3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a10d3-103">'Identity'</span></span>
- <span data-ttu-id="a10d3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a10d3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="a10d3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a10d3-105">'Razor'</span></span>
- <span data-ttu-id="a10d3-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="a10d3-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a10d3-107">Обеспечение безопасности Blazor размещенного в ASP.NET Core приложения сборки Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a10d3-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a10d3-108">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a10d3-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a10d3-109">В этой статье описывается создание [ Blazor размещенного приложения сборки](xref:blazor/hosting-models#blazor-webassembly) , которое использует [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a10d3-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="a10d3-110">Регистрация приложений в AAD и создание решения</span><span class="sxs-lookup"><span data-stu-id="a10d3-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a10d3-111">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="a10d3-111">Create a tenant</span></span>

<span data-ttu-id="a10d3-112">Следуйте указаниям в [кратком руководстве по настройке клиента](/azure/active-directory/develop/quickstart-create-new-tenant) для создания клиента в AAD.</span><span class="sxs-lookup"><span data-stu-id="a10d3-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a10d3-113">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="a10d3-113">Register a server API app</span></span>

<span data-ttu-id="a10d3-114">Следуйте указаниям в [кратком руководстве: регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделов Azure AAD, чтобы зарегистрировать приложение AAD для *приложения API сервера*:</span><span class="sxs-lookup"><span data-stu-id="a10d3-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="a10d3-115">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a10d3-116">Укажите **имя** приложения (например, \*\* Blazor сервер AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a10d3-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a10d3-117">Выберите **Поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="a10d3-118">Для этого интерфейса можно выбрать **учетные записи только в этом каталоге Организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="a10d3-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a10d3-119">В этом сценарии *приложению API сервера* не требуется **универсальный код ресурса (URI) перенаправления** , поэтому оставьте в раскрывающемся списке значение **Web** и не вводите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="a10d3-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a10d3-120">Отключите **разрешения**  >  **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a10d3-121">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-121">Select **Register**.</span></span>

<span data-ttu-id="a10d3-122">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="a10d3-122">Record the following information:</span></span>

* <span data-ttu-id="a10d3-123">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="a10d3-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="a10d3-124">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="a10d3-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="a10d3-125">Домен клиента AAD (например, `contoso.onmicrosoft.com` ) домен &ndash; доступен в качестве **домена издателя** в колонке **фирменной символики** портал Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="a10d3-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a10d3-126">В окне **разрешения API**удалите разрешение **Microsoft Graph**  >  **пользователь. чтение** , так как приложению не требуется доступ для входа или профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="a10d3-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="a10d3-127">В **предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="a10d3-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="a10d3-128">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a10d3-129">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a10d3-130">Укажите **имя области** (например, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a10d3-131">Укажите **Отображаемое имя согласия администратора** (например, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a10d3-132">Введите **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a10d3-133">Убедитесь, что для **состояния** задано значение **включено**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a10d3-134">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-134">Select **Add scope**.</span></span>

<span data-ttu-id="a10d3-135">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="a10d3-135">Record the following information:</span></span>

* <span data-ttu-id="a10d3-136">URI идентификатора приложения (например,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` или предоставленное вами пользовательское значение)</span><span class="sxs-lookup"><span data-stu-id="a10d3-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a10d3-137">Область по умолчанию (например, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="a10d3-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a10d3-138">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="a10d3-138">Register a client app</span></span>

<span data-ttu-id="a10d3-139">Следуйте указаниям в [кратком руководстве: регистрация приложения с помощью платформы удостоверений Майкрософт](/azure/active-directory/develop/quickstart-register-app) и последующих разделов Azure AAD для регистрации приложения AAD для *клиентского приложения*:</span><span class="sxs-lookup"><span data-stu-id="a10d3-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="a10d3-140">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a10d3-141">Укажите **имя** приложения (например, \*\* Blazor AAD клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="a10d3-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="a10d3-142">Выберите **Поддерживаемые типы учетных записей**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="a10d3-143">Для этого интерфейса можно выбрать **учетные записи только в этом каталоге Организации** (один клиент).</span><span class="sxs-lookup"><span data-stu-id="a10d3-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a10d3-144">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб**и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="a10d3-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a10d3-145">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="a10d3-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a10d3-146">Для IIS Express порт, созданный случайным образом, можно найти в свойствах серверного приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="a10d3-147">Отключите **разрешения**  >  **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-147">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a10d3-148">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-148">Select **Register**.</span></span>

<span data-ttu-id="a10d3-149">Запишите идентификатор приложения *клиентского приложения* (идентификатор клиента) (например, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-149">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="a10d3-150">В конфигурации платформы **проверки подлинности**  >  **Platform configurations**  >  **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="a10d3-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a10d3-151">Убедитесь, что **URI перенаправления** `https://localhost:{PORT}/authentication/login-callback` имеется.</span><span class="sxs-lookup"><span data-stu-id="a10d3-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a10d3-152">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a10d3-153">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="a10d3-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a10d3-154">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-154">Select the **Save** button.</span></span>

<span data-ttu-id="a10d3-155">В **разрешениях API**:</span><span class="sxs-lookup"><span data-stu-id="a10d3-155">In **API permissions**:</span></span>

1. <span data-ttu-id="a10d3-156">Убедитесь, что приложение имеет **Microsoft Graph**  >  **пользователь. чтение** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a10d3-157">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a10d3-158">Выберите *приложение API сервера* из столбца **имя** (например, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a10d3-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="a10d3-159">Откройте список **API** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-159">Open the **API** list.</span></span>
1. <span data-ttu-id="a10d3-160">Разрешение доступа к API (например, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a10d3-161">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="a10d3-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a10d3-162">Нажмите кнопку **предоставить содержимое администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-162">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="a10d3-163">Нажмите кнопку **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="a10d3-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a10d3-164">Создайте приложение</span><span class="sxs-lookup"><span data-stu-id="a10d3-164">Create the app</span></span>

<span data-ttu-id="a10d3-165">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="a10d3-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="a10d3-166">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a10d3-167">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="a10d3-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a10d3-168">Передайте универсальный код ресурса (URI) идентификатора приложения в `app-id-uri` параметр, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, которое описано в разделе [области действия маркера доступа](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="a10d3-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a10d3-169">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="a10d3-169">Server app configuration</span></span>

<span data-ttu-id="a10d3-170">*Этот раздел относится к **серверному** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="a10d3-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a10d3-171">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a10d3-171">Authentication package</span></span>

<span data-ttu-id="a10d3-172">Поддержка проверки подлинности и авторизации вызовов ASP.NET Core веб-интерфейсов API обеспечивается `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="a10d3-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a10d3-173">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a10d3-173">Authentication service support</span></span>

<span data-ttu-id="a10d3-174">Метод настраивает `AddAuthentication` службы проверки подлинности в приложении и настраивает обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a10d3-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a10d3-175">`AddAzureADBearer`Метод настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, созданных Azure Active Directory:</span><span class="sxs-lookup"><span data-stu-id="a10d3-175">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="a10d3-176">`UseAuthentication`и `UseAuthorization` Убедитесь, что:</span><span class="sxs-lookup"><span data-stu-id="a10d3-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="a10d3-177">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="a10d3-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a10d3-178">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="a10d3-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="a10d3-179">Пользователь. Identity . Безымян</span><span class="sxs-lookup"><span data-stu-id="a10d3-179">User.Identity.Name</span></span>

<span data-ttu-id="a10d3-180">По умолчанию API серверного приложения заполняет `User.Identity.Name` значение значением из `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` типа утверждения (например, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-180">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="a10d3-181">Чтобы настроить приложение для получения значения из `name` типа утверждения, настройте [TokenValidationParameters. намеклаимтипе](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a10d3-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a10d3-182">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="a10d3-182">App settings</span></span>

<span data-ttu-id="a10d3-183">Файл *appSettings. JSON* содержит параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа:</span><span class="sxs-lookup"><span data-stu-id="a10d3-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="a10d3-184">Пример.</span><span class="sxs-lookup"><span data-stu-id="a10d3-184">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="a10d3-185">Контроллер Веасерфорекаст</span><span class="sxs-lookup"><span data-stu-id="a10d3-185">WeatherForecast controller</span></span>

<span data-ttu-id="a10d3-186">Контроллер Веасерфорекаст (*Controllers/веасерфорекастконтроллер. CS*) предоставляет защищенный API с `[Authorize]` атрибутом, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="a10d3-186">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="a10d3-187">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="a10d3-187">It's **important** to understand that:</span></span>

* <span data-ttu-id="a10d3-188">`[Authorize]`Атрибут в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="a10d3-188">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a10d3-189">`[Authorize]`Атрибут, используемый в Blazor приложении сборки, служит указанием для приложения, которое должно быть проверено для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="a10d3-189">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a10d3-190">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="a10d3-190">Client app configuration</span></span>

<span data-ttu-id="a10d3-191">*Этот раздел относится к **клиентскому** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="a10d3-191">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a10d3-192">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a10d3-192">Authentication package</span></span>

<span data-ttu-id="a10d3-193">При создании приложения для использования рабочих или учебных учетных записей `SingleOrg` приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="a10d3-193">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a10d3-194">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="a10d3-194">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a10d3-195">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="a10d3-195">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="a10d3-196">`Microsoft.Authentication.WebAssembly.Msal`Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="a10d3-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a10d3-197">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a10d3-197">Authentication service support</span></span>

<span data-ttu-id="a10d3-198">Добавлена поддержка `HttpClient` экземпляров, включающая маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="a10d3-198">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a10d3-199">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a10d3-199">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a10d3-200">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью `AddMsalAuthentication` метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="a10d3-200">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a10d3-201">Этот метод настраивает службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="a10d3-201">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a10d3-202">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a10d3-202">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a10d3-203">`AddMsalAuthentication`Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="a10d3-203">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a10d3-204">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="a10d3-204">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a10d3-205">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="a10d3-205">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a10d3-206">Пример.</span><span class="sxs-lookup"><span data-stu-id="a10d3-206">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a10d3-207">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="a10d3-207">Access token scopes</span></span>

<span data-ttu-id="a10d3-208">Области токена доступа по умолчанию представляют собой список областей токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="a10d3-208">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a10d3-209">Включается по умолчанию в запросе на вход.</span><span class="sxs-lookup"><span data-stu-id="a10d3-209">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a10d3-210">Используется для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="a10d3-210">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a10d3-211">Все области должны принадлежать одному и тому же приложению для правил Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a10d3-211">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a10d3-212">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="a10d3-212">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="a10d3-213">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="a10d3-213">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a10d3-214">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="a10d3-214">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a10d3-215">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="a10d3-215">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="a10d3-216">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="a10d3-216">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a10d3-217">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="a10d3-217">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a10d3-218">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="a10d3-218">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a10d3-219">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="a10d3-219">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a10d3-220">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="a10d3-220">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a10d3-221">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="a10d3-221">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a10d3-222">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="a10d3-222">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a10d3-223">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="a10d3-223">Run the app</span></span>

<span data-ttu-id="a10d3-224">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="a10d3-224">Run the app from the Server project.</span></span> <span data-ttu-id="a10d3-225">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="a10d3-225">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a10d3-226">Задайте раскрывающийся список **запускаемые проекты** на панели инструментов для *приложения API сервера* и нажмите кнопку **выполнить** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-226">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a10d3-227">Выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="a10d3-227">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a10d3-228">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a10d3-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="a10d3-229">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a10d3-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a10d3-230">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="a10d3-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
