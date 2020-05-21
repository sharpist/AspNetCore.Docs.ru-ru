---
<span data-ttu-id="44b72-101">Title: ' безопасное ASP.NET Core Blazor размещенное приложение сборки с помощью Azure Active Directory B2C ' author: описание: моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="44b72-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="44b72-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="44b72-102">'Blazor'</span></span>
- <span data-ttu-id="44b72-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="44b72-103">'Identity'</span></span>
- <span data-ttu-id="44b72-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="44b72-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="44b72-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="44b72-105">'Razor'</span></span>
- <span data-ttu-id="44b72-106">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="44b72-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="44b72-107">Обеспечение безопасности Blazor размещенного в ASP.NET Core приложения сборки Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="44b72-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="44b72-108">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="44b72-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="44b72-109">В этой статье описывается, как создать Blazor автономное приложение для сборки, использующее [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) для проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44b72-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="44b72-110">Регистрация приложений в AAD B2C и создание решения</span><span class="sxs-lookup"><span data-stu-id="44b72-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="44b72-111">Создание клиента</span><span class="sxs-lookup"><span data-stu-id="44b72-111">Create a tenant</span></span>

<span data-ttu-id="44b72-112">Следуйте указаниям в [руководстве по созданию клиента Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) для создания клиента AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="44b72-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="44b72-113">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="44b72-113">Record the following information:</span></span>

* <span data-ttu-id="44b72-114">AAD B2C экземпляр (например, `https://contoso.b2clogin.com/` , включающий косую черту)</span><span class="sxs-lookup"><span data-stu-id="44b72-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="44b72-115">Домен клиента AAD B2C (например, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="44b72-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="44b72-116">Регистрация приложения API сервера</span><span class="sxs-lookup"><span data-stu-id="44b72-116">Register a server API app</span></span>

<span data-ttu-id="44b72-117">Следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) , чтобы зарегистрировать приложение AAD для *приложения API сервера*.</span><span class="sxs-lookup"><span data-stu-id="44b72-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="44b72-118">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="44b72-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="44b72-119">Укажите **имя** приложения (например, \*\* Blazor AAD B2C сервера\*\*).</span><span class="sxs-lookup"><span data-stu-id="44b72-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="44b72-120">Для **поддерживаемых типов учетных записей**выберите параметр несколько клиентов: **учетные записи в любом каталоге организации или в любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="44b72-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="44b72-121">В этом сценарии *приложению API сервера* не требуется **универсальный код ресурса (URI) перенаправления** , поэтому оставьте в раскрывающемся списке значение **Web** и не вводите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="44b72-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="44b72-122">Убедитесь, что **разрешения**  >  **на предоставление разрешений администратора OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="44b72-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="44b72-123">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="44b72-123">Select **Register**.</span></span>

<span data-ttu-id="44b72-124">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="44b72-124">Record the following information:</span></span>

* <span data-ttu-id="44b72-125">*Приложение API сервера* Идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="44b72-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="44b72-126">Идентификатор каталога (идентификатор клиента) (например, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="44b72-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="44b72-127">Домен клиента AAD (например, `contoso.onmicrosoft.com` ) домен &ndash; доступен в качестве **домена издателя** в колонке **фирменной символики** портал Azure для зарегистрированного приложения.</span><span class="sxs-lookup"><span data-stu-id="44b72-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="44b72-128">В **предоставление API**:</span><span class="sxs-lookup"><span data-stu-id="44b72-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="44b72-129">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="44b72-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="44b72-130">Выберите **Сохранить и продолжить**.</span><span class="sxs-lookup"><span data-stu-id="44b72-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="44b72-131">Укажите **имя области** (например, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="44b72-132">Укажите **Отображаемое имя согласия администратора** (например, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="44b72-133">Введите **Описание согласия администратора** (например, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="44b72-134">Убедитесь, что для **состояния** задано значение **включено**.</span><span class="sxs-lookup"><span data-stu-id="44b72-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="44b72-135">Выберите **Добавить область**.</span><span class="sxs-lookup"><span data-stu-id="44b72-135">Select **Add scope**.</span></span>

<span data-ttu-id="44b72-136">Запишите следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="44b72-136">Record the following information:</span></span>

* <span data-ttu-id="44b72-137">URI идентификатора приложения (например,, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` или предоставленное вами пользовательское значение)</span><span class="sxs-lookup"><span data-stu-id="44b72-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="44b72-138">Область по умолчанию (например, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="44b72-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="44b72-139">Регистрация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="44b72-139">Register a client app</span></span>

<span data-ttu-id="44b72-140">Чтобы зарегистрировать приложение AAD для *клиентского приложения*, следуйте указаниям в [руководстве по регистрации приложения в Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) .</span><span class="sxs-lookup"><span data-stu-id="44b72-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="44b72-141">В **Azure Active Directory**  >  **Регистрация приложений**выберите пункт **Новая регистрация**.</span><span class="sxs-lookup"><span data-stu-id="44b72-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="44b72-142">Укажите **имя** приложения (например, \*\* Blazor AAD B2C клиента\*\*).</span><span class="sxs-lookup"><span data-stu-id="44b72-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="44b72-143">Для **поддерживаемых типов учетных записей**выберите параметр несколько клиентов: **учетные записи в любом каталоге организации или в любом поставщике удостоверений. Для проверки подлинности пользователей с помощью Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="44b72-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="44b72-144">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб-сайт** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="44b72-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="44b72-145">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="44b72-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="44b72-146">Если приложение выполняется на другом порту Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="44b72-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="44b72-147">Для IIS Express созданный случайным образом порт для приложения можно найти в свойствах серверного приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="44b72-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="44b72-148">Так как на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="44b72-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="44b72-149">Замечание появится в разделе [Создание приложения](#create-the-app) , чтобы напомнить IIS Express пользователям об обновлении URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="44b72-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="44b72-150">Убедитесь, что **разрешения**  >  **на предоставление разрешений администратора OpenID Connect и offline_access** включены.</span><span class="sxs-lookup"><span data-stu-id="44b72-150">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="44b72-151">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="44b72-151">Select **Register**.</span></span>

<span data-ttu-id="44b72-152">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-152">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="44b72-153">В конфигурации платформы **проверки подлинности**  >  **Platform configurations**  >  **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="44b72-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="44b72-154">Убедитесь, что **URI перенаправления** `https://localhost:{PORT}/authentication/login-callback` имеется.</span><span class="sxs-lookup"><span data-stu-id="44b72-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="44b72-155">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="44b72-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="44b72-156">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="44b72-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="44b72-157">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="44b72-157">Select the **Save** button.</span></span>

<span data-ttu-id="44b72-158">В **разрешениях API**:</span><span class="sxs-lookup"><span data-stu-id="44b72-158">In **API permissions**:</span></span>

1. <span data-ttu-id="44b72-159">Выберите **Добавить разрешение** , а затем — **Мои API**.</span><span class="sxs-lookup"><span data-stu-id="44b72-159">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="44b72-160">Выберите *приложение API сервера* из столбца **имя** (например, \*\* Blazor Server AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="44b72-160">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="44b72-161">Откройте список **API** .</span><span class="sxs-lookup"><span data-stu-id="44b72-161">Open the **API** list.</span></span>
1. <span data-ttu-id="44b72-162">Разрешение доступа к API (например, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-162">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="44b72-163">Выберите **Добавить разрешения**.</span><span class="sxs-lookup"><span data-stu-id="44b72-163">Select **Add permissions**.</span></span>
1. <span data-ttu-id="44b72-164">Нажмите кнопку **предоставить содержимое администратора для {имя клиента}** .</span><span class="sxs-lookup"><span data-stu-id="44b72-164">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="44b72-165">Нажмите кнопку **Да** для подтверждения.</span><span class="sxs-lookup"><span data-stu-id="44b72-165">Select **Yes** to confirm.</span></span>

<span data-ttu-id="44b72-166">В **домашних**  >  **Azure AD B2C**  >  **пользовательских потоках**:</span><span class="sxs-lookup"><span data-stu-id="44b72-166">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="44b72-167">Создание потока пользователя для регистрации и входа в систему</span><span class="sxs-lookup"><span data-stu-id="44b72-167">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="44b72-168">**Application claims**  >  Для заполнения**Display Name** `context.User.Identity.Name` в `LoginDisplay` компоненте (*Shared/логиндисплай. Razor*) выберите по меньшей мере атрибут пользователя "отображаемое имя утверждения приложения".</span><span class="sxs-lookup"><span data-stu-id="44b72-168">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="44b72-169">Запишите имя потока пользователя для регистрации и входа, созданное для приложения (например, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-169">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="44b72-170">Создайте приложение</span><span class="sxs-lookup"><span data-stu-id="44b72-170">Create the app</span></span>

<span data-ttu-id="44b72-171">Замените заполнители в следующей команде на записанные ранее сведения и выполните команду в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="44b72-171">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="44b72-172">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-172">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="44b72-173">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="44b72-173">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="44b72-174">Передайте универсальный код ресурса (URI) идентификатора приложения в `app-id-uri` параметр, но обратите внимание, что в клиентском приложении может потребоваться изменение конфигурации, которое описано в разделе [области действия маркера доступа](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="44b72-174">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="44b72-175">Кроме того, в области, настроенной размещенным Blazor шаблоном, может быть повторяющийся узел URI идентификатора приложения.</span><span class="sxs-lookup"><span data-stu-id="44b72-175">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="44b72-176">Убедитесь, что область, настроенная для `DefaultAccessTokenScopes` коллекции, верна в `Program.Main` (*Program.CS*) *клиентского приложения*.</span><span class="sxs-lookup"><span data-stu-id="44b72-176">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="44b72-177">В портал Azure параметры платформы **проверки подлинности**для веб- *клиента клиентского приложения*  >  **Platform configurations**  >  **Web**  >  **Redirect URI** настроены для порта 5001 для приложений, работающих на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="44b72-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="44b72-178">Если *клиентское приложение* выполняется на случайном IIS Express порту, порт для приложения можно найти в свойствах *серверного приложения* на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="44b72-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="44b72-179">Если порт не был настроен ранее с помощью известного порта *клиентского приложения* , вернитесь к регистрации *клиентского приложения* в портал Azure и обновите URI перенаправления с правильным портом.</span><span class="sxs-lookup"><span data-stu-id="44b72-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="44b72-180">Конфигурация серверного приложения</span><span class="sxs-lookup"><span data-stu-id="44b72-180">Server app configuration</span></span>

<span data-ttu-id="44b72-181">*Этот раздел относится к **серверному** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="44b72-181">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="44b72-182">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="44b72-182">Authentication package</span></span>

<span data-ttu-id="44b72-183">Поддержка проверки подлинности и авторизации вызовов ASP.NET Core веб-интерфейсов API обеспечивается `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :</span><span class="sxs-lookup"><span data-stu-id="44b72-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="44b72-184">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="44b72-184">Authentication service support</span></span>

<span data-ttu-id="44b72-185">Метод настраивает `AddAuthentication` службы проверки подлинности в приложении и настраивает обработчик носителя JWT в качестве метода проверки подлинности по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="44b72-185">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="44b72-186">`AddAzureADB2CBearer`Метод настраивает определенные параметры в обработчике носителя JWT, который требуется для проверки маркеров, созданных Azure Active Directory B2C:</span><span class="sxs-lookup"><span data-stu-id="44b72-186">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="44b72-187">`UseAuthentication`и `UseAuthorization` Убедитесь, что:</span><span class="sxs-lookup"><span data-stu-id="44b72-187">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="44b72-188">Приложение пытается проанализировать и проверить маркеры в входящих запросах.</span><span class="sxs-lookup"><span data-stu-id="44b72-188">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="44b72-189">Любой запрос, пытающийся получить доступ к защищенному ресурсу без соответствующих учетных данных, завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="44b72-189">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="44b72-190">Пользователь. Identity . Безымян</span><span class="sxs-lookup"><span data-stu-id="44b72-190">User.Identity.Name</span></span>

<span data-ttu-id="44b72-191">По умолчанию значение `User.Identity.Name` не заполняется.</span><span class="sxs-lookup"><span data-stu-id="44b72-191">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="44b72-192">Чтобы настроить приложение для получения значения из `name` типа утверждения, настройте [TokenValidationParameters. намеклаимтипе](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44b72-192">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="44b72-193">Параметры приложения</span><span class="sxs-lookup"><span data-stu-id="44b72-193">App settings</span></span>

<span data-ttu-id="44b72-194">Файл *appSettings. JSON* содержит параметры для настройки обработчика носителя JWT, используемого для проверки маркеров доступа.</span><span class="sxs-lookup"><span data-stu-id="44b72-194">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="44b72-195">Пример.</span><span class="sxs-lookup"><span data-stu-id="44b72-195">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="44b72-196">Контроллер Веасерфорекаст</span><span class="sxs-lookup"><span data-stu-id="44b72-196">WeatherForecast controller</span></span>

<span data-ttu-id="44b72-197">Контроллер Веасерфорекаст (*Controllers/веасерфорекастконтроллер. CS*) предоставляет защищенный API с `[Authorize]` атрибутом, примененным к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="44b72-197">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="44b72-198">**Важно** понимать, что:</span><span class="sxs-lookup"><span data-stu-id="44b72-198">It's **important** to understand that:</span></span>

* <span data-ttu-id="44b72-199">`[Authorize]`Атрибут в этом контроллере API является единственным, который защищает этот API от несанкционированного доступа.</span><span class="sxs-lookup"><span data-stu-id="44b72-199">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="44b72-200">`[Authorize]`Атрибут, используемый в Blazor приложении сборки, служит указанием для приложения, которое должно быть проверено для правильной работы приложения.</span><span class="sxs-lookup"><span data-stu-id="44b72-200">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="44b72-201">Конфигурация клиентского приложения</span><span class="sxs-lookup"><span data-stu-id="44b72-201">Client app configuration</span></span>

<span data-ttu-id="44b72-202">*Этот раздел относится к **клиентскому** приложению решения.*</span><span class="sxs-lookup"><span data-stu-id="44b72-202">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="44b72-203">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="44b72-203">Authentication package</span></span>

<span data-ttu-id="44b72-204">При создании приложения для использования отдельной учетной записи B2C ( `IndividualB2C` ) приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ( `Microsoft.Authentication.WebAssembly.Msal` ).</span><span class="sxs-lookup"><span data-stu-id="44b72-204">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="44b72-205">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="44b72-205">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="44b72-206">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="44b72-206">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="44b72-207">`Microsoft.Authentication.WebAssembly.Msal`Пакет будет транзитно добавлять `Microsoft.AspNetCore.Components.WebAssembly.Authentication` пакет в приложение.</span><span class="sxs-lookup"><span data-stu-id="44b72-207">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="44b72-208">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="44b72-208">Authentication service support</span></span>

<span data-ttu-id="44b72-209">Добавлена поддержка `HttpClient` экземпляров, включающая маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="44b72-209">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="44b72-210">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="44b72-210">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="44b72-211">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью `AddMsalAuthentication` метода расширения, предоставленного `Microsoft.Authentication.WebAssembly.Msal` пакетом.</span><span class="sxs-lookup"><span data-stu-id="44b72-211">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="44b72-212">Этот метод настраивает службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="44b72-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="44b72-213">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="44b72-213">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="44b72-214">`AddMsalAuthentication`Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="44b72-214">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="44b72-215">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD на портале Azure при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="44b72-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="44b72-216">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="44b72-216">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="44b72-217">Пример.</span><span class="sxs-lookup"><span data-stu-id="44b72-217">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="44b72-218">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="44b72-218">Access token scopes</span></span>

<span data-ttu-id="44b72-219">Области токена доступа по умолчанию представляют собой список областей токенов доступа.</span><span class="sxs-lookup"><span data-stu-id="44b72-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="44b72-220">Включается по умолчанию в запросе на вход.</span><span class="sxs-lookup"><span data-stu-id="44b72-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="44b72-221">Используется для предоставления маркера доступа сразу после проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="44b72-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="44b72-222">Все области должны принадлежать одному и тому же приложению для правил Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="44b72-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="44b72-223">При необходимости можно добавить дополнительные области для дополнительных приложений API:</span><span class="sxs-lookup"><span data-stu-id="44b72-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="44b72-224">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="44b72-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="44b72-225">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="44b72-225">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="44b72-226">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="44b72-226">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="44b72-227">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="44b72-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="44b72-228">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="44b72-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="44b72-229">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="44b72-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="44b72-230">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="44b72-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="44b72-231">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="44b72-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="44b72-232">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="44b72-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="44b72-233">Компонент FetchData</span><span class="sxs-lookup"><span data-stu-id="44b72-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="44b72-234">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="44b72-234">Run the app</span></span>

<span data-ttu-id="44b72-235">Запустите приложение из серверного проекта.</span><span class="sxs-lookup"><span data-stu-id="44b72-235">Run the app from the Server project.</span></span> <span data-ttu-id="44b72-236">При использовании Visual Studio выполните одно из следующих действий.</span><span class="sxs-lookup"><span data-stu-id="44b72-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="44b72-237">Задайте раскрывающийся список **запускаемые проекты** на панели инструментов для *приложения API сервера* и нажмите кнопку **выполнить** .</span><span class="sxs-lookup"><span data-stu-id="44b72-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="44b72-238">Выберите серверный проект в **Обозреватель решений** и нажмите кнопку **выполнить** на панели инструментов или запустите приложение из меню **Отладка** .</span><span class="sxs-lookup"><span data-stu-id="44b72-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="44b72-239">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="44b72-239">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="44b72-240">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="44b72-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="44b72-241">Руководство по созданию клиента Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="44b72-241">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="44b72-242">Документация по платформе удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="44b72-242">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
