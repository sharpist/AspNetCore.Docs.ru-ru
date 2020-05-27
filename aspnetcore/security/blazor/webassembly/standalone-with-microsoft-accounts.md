---
<span data-ttu-id="b684b-101">Title: ' безопасное ASP.NET Core Blazor изолированное приложение веб-сборки с учетными записями Майкрософт: описание: моникерранже: MS. author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="b684b-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="b684b-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b684b-102">'Blazor'</span></span>
- <span data-ttu-id="b684b-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b684b-103">'Identity'</span></span>
- <span data-ttu-id="b684b-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b684b-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="b684b-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b684b-105">'Razor'</span></span>
- <span data-ttu-id="b684b-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="b684b-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="b684b-107">Защита Blazor автономного приложения ASP.NET Coreной сборки с помощью учетных записей Майкрософт</span><span class="sxs-lookup"><span data-stu-id="b684b-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="b684b-108">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b684b-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b684b-109">Чтобы создать Blazor изолированное приложение для веб-сборки, использующее [учетные записи майкрософт с Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) для проверки подлинности:</span><span class="sxs-lookup"><span data-stu-id="b684b-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="b684b-110">Создание клиента AAD и веб-приложения</span><span class="sxs-lookup"><span data-stu-id="b684b-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="b684b-111">Зарегистрируйте приложение AAD в области **Azure Active Directory**  >  **Регистрация приложений** портал Azure:</span><span class="sxs-lookup"><span data-stu-id="b684b-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="b684b-112">Укажите **имя** приложения (например, \*\* Blazor автономные учетные записи Microsoft AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="b684b-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="b684b-113">В списке **Поддерживаемые типы учетных записей**выберите **учетные записи в любом организационном каталоге**.</span><span class="sxs-lookup"><span data-stu-id="b684b-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="b684b-114">Оставьте в раскрывающемся списке **URI перенаправления** значение **веб-сайт** и укажите следующий URI перенаправления: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="b684b-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="b684b-115">Порт по умолчанию для приложения, работающего на Kestrel, — 5001.</span><span class="sxs-lookup"><span data-stu-id="b684b-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="b684b-116">Если приложение выполняется на другом порту Kestrel, используйте порт приложения.</span><span class="sxs-lookup"><span data-stu-id="b684b-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="b684b-117">Для IIS Express созданный случайным образом порт для приложения можно найти в свойствах приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="b684b-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="b684b-118">Так как на этом этапе приложение не существует и порт IIS Express неизвестен, вернитесь к этому шагу после создания приложения и обновите URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b684b-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="b684b-119">В этом разделе появится замечание, напоминающее IIS Express пользователям обновить URI перенаправления.</span><span class="sxs-lookup"><span data-stu-id="b684b-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="b684b-120">Отключите **разрешения**  >  **предоставление прав администратора для OpenID Connect и offline_access** .</span><span class="sxs-lookup"><span data-stu-id="b684b-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="b684b-121">Выберите **Зарегистрировать**.</span><span class="sxs-lookup"><span data-stu-id="b684b-121">Select **Register**.</span></span>

<span data-ttu-id="b684b-122">Запишите идентификатор приложения (идентификатор клиента) (например, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="b684b-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="b684b-123">В конфигурации платформы **проверки подлинности**  >  **Platform configurations**  >  **веб-сайт**:</span><span class="sxs-lookup"><span data-stu-id="b684b-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="b684b-124">Убедитесь, что **URI перенаправления** `https://localhost:{PORT}/authentication/login-callback` имеется.</span><span class="sxs-lookup"><span data-stu-id="b684b-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="b684b-125">Для **неявного предоставления**установите флажки для **маркеров доступа** и **маркеров идентификации**.</span><span class="sxs-lookup"><span data-stu-id="b684b-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="b684b-126">Остальные значения по умолчанию для приложения приемлемы для этого интерфейса.</span><span class="sxs-lookup"><span data-stu-id="b684b-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="b684b-127">Нажмите кнопку **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="b684b-127">Select the **Save** button.</span></span>

<span data-ttu-id="b684b-128">Создайте приложение.</span><span class="sxs-lookup"><span data-stu-id="b684b-128">Create the app.</span></span> <span data-ttu-id="b684b-129">Замените заполнители в следующей команде на записанные ранее сведения и выполните в командной оболочке следующую команду:</span><span class="sxs-lookup"><span data-stu-id="b684b-129">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="b684b-130">Чтобы указать расположение выходных данных, которое создает папку проекта, если она не существует, включите параметр OUTPUT в команду с путем (например, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="b684b-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="b684b-131">Имя папки также станет частью имени проекта.</span><span class="sxs-lookup"><span data-stu-id="b684b-131">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="b684b-132">В портал Azure Конфигурация платформы **проверки подлинности**приложения  >  **Platform configurations**  >  **Web**  >  **URI перенаправления** настроена для порта 5001 для приложений, работающих на сервере Kestrel с параметрами по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="b684b-132">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="b684b-133">Если приложение выполняется на случайном IIS Express порту, порт для приложения можно найти в свойствах приложения на панели **отладки** .</span><span class="sxs-lookup"><span data-stu-id="b684b-133">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="b684b-134">Если порт не был настроен ранее с помощью известного порта приложения, вернитесь к регистрации приложения в портал Azure и обновите URI перенаправления с правильным портом.</span><span class="sxs-lookup"><span data-stu-id="b684b-134">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="b684b-135">После создания приложения вы сможете:</span><span class="sxs-lookup"><span data-stu-id="b684b-135">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="b684b-136">Войдите в приложение, используя учетная запись Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="b684b-136">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="b684b-137">Запрос маркеров доступа для API-интерфейсов Майкрософт.</span><span class="sxs-lookup"><span data-stu-id="b684b-137">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="b684b-138">Дополнительные сведения см. в разделе:</span><span class="sxs-lookup"><span data-stu-id="b684b-138">For more information, see:</span></span>
  * [<span data-ttu-id="b684b-139">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="b684b-139">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="b684b-140">[Краткое руководство. Настройка приложения для предоставления веб-API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="b684b-140">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="b684b-141">Пакет проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b684b-141">Authentication package</span></span>

<span data-ttu-id="b684b-142">При создании приложения для использования рабочих или учебных учетных записей `SingleOrg` приложение автоматически получает ссылку на пакет для [библиотеки проверки подлинности Майкрософт](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="b684b-142">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="b684b-143">Пакет предоставляет набор примитивов, которые помогают приложению проверять подлинность пользователей и получать маркеры для вызова защищенных интерфейсов API.</span><span class="sxs-lookup"><span data-stu-id="b684b-143">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="b684b-144">При добавлении проверки подлинности в приложение вручную добавьте пакет в файл проекта приложения:</span><span class="sxs-lookup"><span data-stu-id="b684b-144">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="b684b-145">Пакет [Microsoft. Authentication. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) . в качестве транзитного пакета добавляет пакет [Microsoft. AspNetCore. Components. Assembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) в приложение.</span><span class="sxs-lookup"><span data-stu-id="b684b-145">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="b684b-146">Поддержка службы проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b684b-146">Authentication service support</span></span>

<span data-ttu-id="b684b-147">Поддержка проверки подлинности пользователей регистрируется в контейнере службы с помощью <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> метода расширения, предоставленного пакетом [Microsoft. Authentication. WebService. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="b684b-147">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="b684b-148">Этот метод настраивает все службы, необходимые для взаимодействия приложения с Identity поставщиком (IP).</span><span class="sxs-lookup"><span data-stu-id="b684b-148">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="b684b-149">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b684b-149">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="b684b-150"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Метод принимает обратный вызов для настройки параметров, необходимых для проверки подлинности приложения.</span><span class="sxs-lookup"><span data-stu-id="b684b-150">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="b684b-151">Значения, необходимые для настройки приложения, можно получить из конфигурации AAD при регистрации приложения.</span><span class="sxs-lookup"><span data-stu-id="b684b-151">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="b684b-152">Конфигурация предоставляется файлом *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b684b-152">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="b684b-153">Пример</span><span class="sxs-lookup"><span data-stu-id="b684b-153">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="b684b-154">Области токенов доступа</span><span class="sxs-lookup"><span data-stu-id="b684b-154">Access token scopes</span></span>

<span data-ttu-id="b684b-155">BlazorШаблон сборки не автоматически настраивает приложение для запроса маркера доступа для безопасного API.</span><span class="sxs-lookup"><span data-stu-id="b684b-155">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="b684b-156">Чтобы настроить маркер доступа как часть потока входа, добавьте область в область маркера доступа по умолчанию для <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="b684b-156">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="b684b-157">Дополнительные сведения см. в следующих разделах статьи *Дополнительные сценарии* :</span><span class="sxs-lookup"><span data-stu-id="b684b-157">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="b684b-158">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="b684b-158">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="b684b-159">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="b684b-159">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="b684b-160">Файл импорта</span><span class="sxs-lookup"><span data-stu-id="b684b-160">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="b684b-161">Страница индексации</span><span class="sxs-lookup"><span data-stu-id="b684b-161">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="b684b-162">Компонент приложения</span><span class="sxs-lookup"><span data-stu-id="b684b-162">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="b684b-163">Компонент Редиректтологин</span><span class="sxs-lookup"><span data-stu-id="b684b-163">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="b684b-164">Компонент Логиндисплай</span><span class="sxs-lookup"><span data-stu-id="b684b-164">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="b684b-165">Компонент проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="b684b-165">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="b684b-166">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="b684b-166">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="b684b-167">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="b684b-167">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="b684b-168">Краткое руководство. Регистрация приложения с помощью платформы удостоверений Майкрософт</span><span class="sxs-lookup"><span data-stu-id="b684b-168">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="b684b-169">Краткое руководство. Настройка приложения для предоставления веб-API</span><span class="sxs-lookup"><span data-stu-id="b684b-169">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
