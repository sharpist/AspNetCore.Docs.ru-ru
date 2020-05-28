---
<span data-ttu-id="dca20-101">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-102">'Blazor'</span></span>
- <span data-ttu-id="dca20-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-103">'Identity'</span></span>
- <span data-ttu-id="dca20-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-105">'Razor'</span></span>
- <span data-ttu-id="dca20-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-106">'SignalR' uid:</span></span> 

---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="dca20-107">Сохранение дополнительных утверждений и маркеров от внешних поставщиков в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dca20-107">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dca20-108">Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter.</span><span class="sxs-lookup"><span data-stu-id="dca20-108">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="dca20-109">Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.</span><span class="sxs-lookup"><span data-stu-id="dca20-109">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="dca20-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dca20-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dca20-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="dca20-111">Prerequisites</span></span>

<span data-ttu-id="dca20-112">Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении.</span><span class="sxs-lookup"><span data-stu-id="dca20-112">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="dca20-113">Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="dca20-113">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="dca20-114">Для получения дополнительной информации см. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="dca20-114">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="dca20-115">В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="dca20-115">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="dca20-116">Задание идентификатора клиента и секрета клиента</span><span class="sxs-lookup"><span data-stu-id="dca20-116">Set the client ID and client secret</span></span>

<span data-ttu-id="dca20-117">Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="dca20-117">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="dca20-118">Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике.</span><span class="sxs-lookup"><span data-stu-id="dca20-118">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="dca20-119">Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика.</span><span class="sxs-lookup"><span data-stu-id="dca20-119">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="dca20-120">Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.</span><span class="sxs-lookup"><span data-stu-id="dca20-120">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="dca20-121">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="dca20-121">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="dca20-122">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="dca20-122">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="dca20-123">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dca20-123">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="dca20-124">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="dca20-124">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="dca20-125">Другие поставщики проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca20-125">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="dca20-126">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="dca20-126">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="dca20-127">Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:</span><span class="sxs-lookup"><span data-stu-id="dca20-127">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="dca20-128">Определение области проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca20-128">Establish the authentication scope</span></span>

<span data-ttu-id="dca20-129">Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="dca20-129">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="dca20-130">В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="dca20-130">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="dca20-131">Поставщик</span><span class="sxs-lookup"><span data-stu-id="dca20-131">Provider</span></span>  | <span data-ttu-id="dca20-132">Область</span><span class="sxs-lookup"><span data-stu-id="dca20-132">Scope</span></span>                                                            |
| ---
<span data-ttu-id="dca20-133">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-133">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-134">'Blazor'</span></span>
- <span data-ttu-id="dca20-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-135">'Identity'</span></span>
- <span data-ttu-id="dca20-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-137">'Razor'</span></span>
- <span data-ttu-id="dca20-138">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-139">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-139">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-140">'Blazor'</span></span>
- <span data-ttu-id="dca20-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-141">'Identity'</span></span>
- <span data-ttu-id="dca20-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-143">'Razor'</span></span>
- <span data-ttu-id="dca20-144">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-144">'SignalR' uid:</span></span> 

<span data-ttu-id="dca20-145">----- | Заголовок---: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-145">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-146">'Blazor'</span></span>
- <span data-ttu-id="dca20-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-147">'Identity'</span></span>
- <span data-ttu-id="dca20-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-149">'Razor'</span></span>
- <span data-ttu-id="dca20-150">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-151">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-151">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-152">'Blazor'</span></span>
- <span data-ttu-id="dca20-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-153">'Identity'</span></span>
- <span data-ttu-id="dca20-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-155">'Razor'</span></span>
- <span data-ttu-id="dca20-156">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-157">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-157">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-158">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-158">'Blazor'</span></span>
- <span data-ttu-id="dca20-159">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-159">'Identity'</span></span>
- <span data-ttu-id="dca20-160">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-160">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-161">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-161">'Razor'</span></span>
- <span data-ttu-id="dca20-162">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-162">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-163">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-163">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-164">'Blazor'</span></span>
- <span data-ttu-id="dca20-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-165">'Identity'</span></span>
- <span data-ttu-id="dca20-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-167">'Razor'</span></span>
- <span data-ttu-id="dca20-168">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-169">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-169">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-170">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-170">'Blazor'</span></span>
- <span data-ttu-id="dca20-171">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-171">'Identity'</span></span>
- <span data-ttu-id="dca20-172">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-172">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-173">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-173">'Razor'</span></span>
- <span data-ttu-id="dca20-174">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-174">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-175">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-175">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-176">'Blazor'</span></span>
- <span data-ttu-id="dca20-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-177">'Identity'</span></span>
- <span data-ttu-id="dca20-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-179">'Razor'</span></span>
- <span data-ttu-id="dca20-180">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-181">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-181">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-182">'Blazor'</span></span>
- <span data-ttu-id="dca20-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-183">'Identity'</span></span>
- <span data-ttu-id="dca20-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-185">'Razor'</span></span>
- <span data-ttu-id="dca20-186">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-187">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-187">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-188">'Blazor'</span></span>
- <span data-ttu-id="dca20-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-189">'Identity'</span></span>
- <span data-ttu-id="dca20-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-191">'Razor'</span></span>
- <span data-ttu-id="dca20-192">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-193">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-193">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-194">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-194">'Blazor'</span></span>
- <span data-ttu-id="dca20-195">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-195">'Identity'</span></span>
- <span data-ttu-id="dca20-196">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-196">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-197">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-197">'Razor'</span></span>
- <span data-ttu-id="dca20-198">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-198">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-199">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-199">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-200">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-200">'Blazor'</span></span>
- <span data-ttu-id="dca20-201">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-201">'Identity'</span></span>
- <span data-ttu-id="dca20-202">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-202">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-203">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-203">'Razor'</span></span>
- <span data-ttu-id="dca20-204">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-204">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-205">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-205">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-206">'Blazor'</span></span>
- <span data-ttu-id="dca20-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-207">'Identity'</span></span>
- <span data-ttu-id="dca20-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-209">'Razor'</span></span>
- <span data-ttu-id="dca20-210">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-211">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-211">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-212">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-212">'Blazor'</span></span>
- <span data-ttu-id="dca20-213">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-213">'Identity'</span></span>
- <span data-ttu-id="dca20-214">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-214">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-215">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-215">'Razor'</span></span>
- <span data-ttu-id="dca20-216">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-216">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-217">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-217">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-218">'Blazor'</span></span>
- <span data-ttu-id="dca20-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-219">'Identity'</span></span>
- <span data-ttu-id="dca20-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-221">'Razor'</span></span>
- <span data-ttu-id="dca20-222">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-222">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-223">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-223">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-224">'Blazor'</span></span>
- <span data-ttu-id="dca20-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-225">'Identity'</span></span>
- <span data-ttu-id="dca20-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-227">'Razor'</span></span>
- <span data-ttu-id="dca20-228">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-229">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-229">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-230">'Blazor'</span></span>
- <span data-ttu-id="dca20-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-231">'Identity'</span></span>
- <span data-ttu-id="dca20-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-233">'Razor'</span></span>
- <span data-ttu-id="dca20-234">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-235">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-235">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-236">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-236">'Blazor'</span></span>
- <span data-ttu-id="dca20-237">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-237">'Identity'</span></span>
- <span data-ttu-id="dca20-238">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-238">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-239">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-239">'Razor'</span></span>
- <span data-ttu-id="dca20-240">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-240">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-241">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-241">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-242">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-242">'Blazor'</span></span>
- <span data-ttu-id="dca20-243">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-243">'Identity'</span></span>
- <span data-ttu-id="dca20-244">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-244">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-245">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-245">'Razor'</span></span>
- <span data-ttu-id="dca20-246">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-246">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-247">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-247">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-248">'Blazor'</span></span>
- <span data-ttu-id="dca20-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-249">'Identity'</span></span>
- <span data-ttu-id="dca20-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-251">'Razor'</span></span>
- <span data-ttu-id="dca20-252">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-253">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-253">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-254">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-254">'Blazor'</span></span>
- <span data-ttu-id="dca20-255">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-255">'Identity'</span></span>
- <span data-ttu-id="dca20-256">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-256">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-257">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-257">'Razor'</span></span>
- <span data-ttu-id="dca20-258">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-258">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-259">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-259">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-260">'Blazor'</span></span>
- <span data-ttu-id="dca20-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-261">'Identity'</span></span>
- <span data-ttu-id="dca20-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-263">'Razor'</span></span>
- <span data-ttu-id="dca20-264">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-265">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-265">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-266">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-266">'Blazor'</span></span>
- <span data-ttu-id="dca20-267">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-267">'Identity'</span></span>
- <span data-ttu-id="dca20-268">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-268">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-269">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-269">'Razor'</span></span>
- <span data-ttu-id="dca20-270">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-270">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-271">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-271">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-272">'Blazor'</span></span>
- <span data-ttu-id="dca20-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-273">'Identity'</span></span>
- <span data-ttu-id="dca20-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-275">'Razor'</span></span>
- <span data-ttu-id="dca20-276">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-277">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-277">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-278">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-278">'Blazor'</span></span>
- <span data-ttu-id="dca20-279">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-279">'Identity'</span></span>
- <span data-ttu-id="dca20-280">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-280">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-281">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-281">'Razor'</span></span>
- <span data-ttu-id="dca20-282">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-282">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-283">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-283">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-284">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-284">'Blazor'</span></span>
- <span data-ttu-id="dca20-285">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-285">'Identity'</span></span>
- <span data-ttu-id="dca20-286">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-286">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-287">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-287">'Razor'</span></span>
- <span data-ttu-id="dca20-288">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-288">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-289">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-289">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-290">'Blazor'</span></span>
- <span data-ttu-id="dca20-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-291">'Identity'</span></span>
- <span data-ttu-id="dca20-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-293">'Razor'</span></span>
- <span data-ttu-id="dca20-294">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-295">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-295">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-296">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-296">'Blazor'</span></span>
- <span data-ttu-id="dca20-297">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-297">'Identity'</span></span>
- <span data-ttu-id="dca20-298">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-298">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-299">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-299">'Razor'</span></span>
- <span data-ttu-id="dca20-300">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-300">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-301">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-301">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-302">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-302">'Blazor'</span></span>
- <span data-ttu-id="dca20-303">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-303">'Identity'</span></span>
- <span data-ttu-id="dca20-304">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-304">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-305">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-305">'Razor'</span></span>
- <span data-ttu-id="dca20-306">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-306">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-307">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-307">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-308">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-308">'Blazor'</span></span>
- <span data-ttu-id="dca20-309">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-309">'Identity'</span></span>
- <span data-ttu-id="dca20-310">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-310">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-311">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-311">'Razor'</span></span>
- <span data-ttu-id="dca20-312">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-312">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-313">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-313">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-314">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-314">'Blazor'</span></span>
- <span data-ttu-id="dca20-315">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-315">'Identity'</span></span>
- <span data-ttu-id="dca20-316">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-316">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-317">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-317">'Razor'</span></span>
- <span data-ttu-id="dca20-318">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-318">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-319">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-319">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-320">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-320">'Blazor'</span></span>
- <span data-ttu-id="dca20-321">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-321">'Identity'</span></span>
- <span data-ttu-id="dca20-322">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-322">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-323">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-323">'Razor'</span></span>
- <span data-ttu-id="dca20-324">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-324">'SignalR' uid:</span></span> 

<span data-ttu-id="dca20-325">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="dca20-325">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="dca20-326">В примере приложения `userinfo.profile` область Google автоматически добавляется платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dca20-326">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="dca20-327">Если приложению требуются дополнительные области, добавьте их в параметры.</span><span class="sxs-lookup"><span data-stu-id="dca20-327">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="dca20-328">В следующем примере `https://www.googleapis.com/auth/user.birthday.read` область Google добавляется для получения дня рождения пользователя:</span><span class="sxs-lookup"><span data-stu-id="dca20-328">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="dca20-329">Сопоставьте ключи данных пользователя и создайте утверждения</span><span class="sxs-lookup"><span data-stu-id="dca20-329">Map user data keys and create claims</span></span>

<span data-ttu-id="dca20-330">В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе.</span><span class="sxs-lookup"><span data-stu-id="dca20-330">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="dca20-331">Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="dca20-331">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="dca20-332">Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:</span><span class="sxs-lookup"><span data-stu-id="dca20-332">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="dca20-333">В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="dca20-333">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="dca20-334">Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="dca20-334">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="dca20-335">В примере приложения `OnPostConfirmationAsync` (*Account/екстерналлогин. cshtml. CS*) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="dca20-335">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="dca20-336">По умолчанию утверждения пользователя хранятся в файле cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca20-336">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="dca20-337">Если файл cookie для проверки подлинности слишком большой, это может привести к сбою приложения по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="dca20-337">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="dca20-338">Браузер обнаруживает, что заголовок файла cookie слишком длинный.</span><span class="sxs-lookup"><span data-stu-id="dca20-338">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="dca20-339">Общий размер запроса слишком велик.</span><span class="sxs-lookup"><span data-stu-id="dca20-339">The overall size of the request is too large.</span></span>

<span data-ttu-id="dca20-340">Если для обработки запросов пользователей требуется большой объем пользовательских данных:</span><span class="sxs-lookup"><span data-stu-id="dca20-340">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="dca20-341">Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="dca20-341">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="dca20-342">Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по промежуточного слоя для проверки подлинности файлов cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах.</span><span class="sxs-lookup"><span data-stu-id="dca20-342">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="dca20-343">Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.</span><span class="sxs-lookup"><span data-stu-id="dca20-343">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="dca20-344">Сохранение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="dca20-344">Save the access token</span></span>

<span data-ttu-id="dca20-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="dca20-345"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="dca20-346">`SaveTokens`по `false` умолчанию имеет значение, чтобы уменьшить размер файла cookie окончательной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca20-346">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="dca20-347">Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="dca20-347">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="dca20-348">Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="dca20-348">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="dca20-349">Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="dca20-349">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="dca20-350">Добавление дополнительных настраиваемых токенов</span><span class="sxs-lookup"><span data-stu-id="dca20-350">How to add additional custom tokens</span></span>

<span data-ttu-id="dca20-351">Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="dca20-351">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="dca20-352">Создание и добавление утверждений</span><span class="sxs-lookup"><span data-stu-id="dca20-352">Creating and adding claims</span></span>

<span data-ttu-id="dca20-353">Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию.</span><span class="sxs-lookup"><span data-stu-id="dca20-353">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="dca20-354">Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="dca20-354">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="dca20-355">Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.</span><span class="sxs-lookup"><span data-stu-id="dca20-355">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="dca20-356">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="dca20-356">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="dca20-357">Удаление действий утверждений и утверждений</span><span class="sxs-lookup"><span data-stu-id="dca20-357">Removal of claim actions and claims</span></span>

<span data-ttu-id="dca20-358">[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции.</span><span class="sxs-lookup"><span data-stu-id="dca20-358">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="dca20-359">[Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения.</span><span class="sxs-lookup"><span data-stu-id="dca20-359">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="dca20-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.</span><span class="sxs-lookup"><span data-stu-id="dca20-360"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="dca20-361">Выходные данные примера приложения</span><span class="sxs-lookup"><span data-stu-id="dca20-361">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dca20-362">Приложение ASP.NET Core может устанавливать дополнительные утверждения и маркеры от внешних поставщиков проверки подлинности, таких как Facebook, Google, Microsoft и Twitter.</span><span class="sxs-lookup"><span data-stu-id="dca20-362">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="dca20-363">Каждый поставщик раскрывает разные сведения о пользователях на своей платформе, но шаблон для получения и преобразования пользовательских данных в дополнительные утверждения одинаковы.</span><span class="sxs-lookup"><span data-stu-id="dca20-363">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="dca20-364">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dca20-364">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dca20-365">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="dca20-365">Prerequisites</span></span>

<span data-ttu-id="dca20-366">Решите, какие внешние поставщики проверки подлинности поддерживаются в приложении.</span><span class="sxs-lookup"><span data-stu-id="dca20-366">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="dca20-367">Для каждого поставщика Зарегистрируйте приложение и получите идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="dca20-367">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="dca20-368">Для получения дополнительной информации см. <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="dca20-368">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="dca20-369">В примере приложения используется [поставщик проверки подлинности Google](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="dca20-369">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="dca20-370">Задание идентификатора клиента и секрета клиента</span><span class="sxs-lookup"><span data-stu-id="dca20-370">Set the client ID and client secret</span></span>

<span data-ttu-id="dca20-371">Поставщик проверки подлинности OAuth устанавливает отношение доверия с приложением, используя идентификатор клиента и секрет клиента.</span><span class="sxs-lookup"><span data-stu-id="dca20-371">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="dca20-372">Идентификатор клиента и значения секрета клиента создаются для приложения внешним поставщиком проверки подлинности при регистрации приложения в поставщике.</span><span class="sxs-lookup"><span data-stu-id="dca20-372">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="dca20-373">Каждый внешний поставщик, используемый приложением, должен быть настроен независимо с ИДЕНТИФИКАТОРом клиента и секретом клиента поставщика.</span><span class="sxs-lookup"><span data-stu-id="dca20-373">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="dca20-374">Дополнительные сведения см. в разделах, посвященных внешнему поставщику проверки подлинности, которые относятся к вашему сценарию.</span><span class="sxs-lookup"><span data-stu-id="dca20-374">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="dca20-375">Проверка подлинности Facebook</span><span class="sxs-lookup"><span data-stu-id="dca20-375">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="dca20-376">Проверка подлинности Google</span><span class="sxs-lookup"><span data-stu-id="dca20-376">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="dca20-377">Проверка подлинности Майкрософт</span><span class="sxs-lookup"><span data-stu-id="dca20-377">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="dca20-378">Проверка подлинности Twitter</span><span class="sxs-lookup"><span data-stu-id="dca20-378">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="dca20-379">Другие поставщики проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca20-379">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="dca20-380">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="dca20-380">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="dca20-381">Пример приложения настраивает поставщик проверки подлинности Google с ИДЕНТИФИКАТОРом клиента и секретом клиента, предоставленным Google:</span><span class="sxs-lookup"><span data-stu-id="dca20-381">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="dca20-382">Определение области проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dca20-382">Establish the authentication scope</span></span>

<span data-ttu-id="dca20-383">Укажите список разрешений для получения от поставщика, указав <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="dca20-383">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="dca20-384">В следующей таблице приведены области проверки подлинности для общих внешних поставщиков.</span><span class="sxs-lookup"><span data-stu-id="dca20-384">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="dca20-385">Поставщик</span><span class="sxs-lookup"><span data-stu-id="dca20-385">Provider</span></span>  | <span data-ttu-id="dca20-386">Область</span><span class="sxs-lookup"><span data-stu-id="dca20-386">Scope</span></span>                                                            |
| ---
<span data-ttu-id="dca20-387">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-388">'Blazor'</span></span>
- <span data-ttu-id="dca20-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-389">'Identity'</span></span>
- <span data-ttu-id="dca20-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-391">'Razor'</span></span>
- <span data-ttu-id="dca20-392">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-393">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-394">'Blazor'</span></span>
- <span data-ttu-id="dca20-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-395">'Identity'</span></span>
- <span data-ttu-id="dca20-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-397">'Razor'</span></span>
- <span data-ttu-id="dca20-398">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-398">'SignalR' uid:</span></span> 

<span data-ttu-id="dca20-399">----- | Заголовок---: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-399">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-400">'Blazor'</span></span>
- <span data-ttu-id="dca20-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-401">'Identity'</span></span>
- <span data-ttu-id="dca20-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-403">'Razor'</span></span>
- <span data-ttu-id="dca20-404">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-405">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-406">'Blazor'</span></span>
- <span data-ttu-id="dca20-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-407">'Identity'</span></span>
- <span data-ttu-id="dca20-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-409">'Razor'</span></span>
- <span data-ttu-id="dca20-410">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-411">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-412">'Blazor'</span></span>
- <span data-ttu-id="dca20-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-413">'Identity'</span></span>
- <span data-ttu-id="dca20-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-415">'Razor'</span></span>
- <span data-ttu-id="dca20-416">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-417">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-418">'Blazor'</span></span>
- <span data-ttu-id="dca20-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-419">'Identity'</span></span>
- <span data-ttu-id="dca20-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-421">'Razor'</span></span>
- <span data-ttu-id="dca20-422">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-423">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-424">'Blazor'</span></span>
- <span data-ttu-id="dca20-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-425">'Identity'</span></span>
- <span data-ttu-id="dca20-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-427">'Razor'</span></span>
- <span data-ttu-id="dca20-428">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-429">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-430">'Blazor'</span></span>
- <span data-ttu-id="dca20-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-431">'Identity'</span></span>
- <span data-ttu-id="dca20-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-433">'Razor'</span></span>
- <span data-ttu-id="dca20-434">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-435">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-436">'Blazor'</span></span>
- <span data-ttu-id="dca20-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-437">'Identity'</span></span>
- <span data-ttu-id="dca20-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-439">'Razor'</span></span>
- <span data-ttu-id="dca20-440">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-441">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-441">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-442">'Blazor'</span></span>
- <span data-ttu-id="dca20-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-443">'Identity'</span></span>
- <span data-ttu-id="dca20-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-445">'Razor'</span></span>
- <span data-ttu-id="dca20-446">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-447">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-448">'Blazor'</span></span>
- <span data-ttu-id="dca20-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-449">'Identity'</span></span>
- <span data-ttu-id="dca20-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-451">'Razor'</span></span>
- <span data-ttu-id="dca20-452">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-453">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-454">'Blazor'</span></span>
- <span data-ttu-id="dca20-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-455">'Identity'</span></span>
- <span data-ttu-id="dca20-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-457">'Razor'</span></span>
- <span data-ttu-id="dca20-458">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-459">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-460">'Blazor'</span></span>
- <span data-ttu-id="dca20-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-461">'Identity'</span></span>
- <span data-ttu-id="dca20-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-463">'Razor'</span></span>
- <span data-ttu-id="dca20-464">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-465">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-466">'Blazor'</span></span>
- <span data-ttu-id="dca20-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-467">'Identity'</span></span>
- <span data-ttu-id="dca20-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-469">'Razor'</span></span>
- <span data-ttu-id="dca20-470">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-471">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-472">'Blazor'</span></span>
- <span data-ttu-id="dca20-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-473">'Identity'</span></span>
- <span data-ttu-id="dca20-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-475">'Razor'</span></span>
- <span data-ttu-id="dca20-476">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-477">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-478">'Blazor'</span></span>
- <span data-ttu-id="dca20-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-479">'Identity'</span></span>
- <span data-ttu-id="dca20-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-481">'Razor'</span></span>
- <span data-ttu-id="dca20-482">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-483">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-484">'Blazor'</span></span>
- <span data-ttu-id="dca20-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-485">'Identity'</span></span>
- <span data-ttu-id="dca20-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-487">'Razor'</span></span>
- <span data-ttu-id="dca20-488">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-489">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-490">'Blazor'</span></span>
- <span data-ttu-id="dca20-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-491">'Identity'</span></span>
- <span data-ttu-id="dca20-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-493">'Razor'</span></span>
- <span data-ttu-id="dca20-494">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-495">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-496">'Blazor'</span></span>
- <span data-ttu-id="dca20-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-497">'Identity'</span></span>
- <span data-ttu-id="dca20-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-499">'Razor'</span></span>
- <span data-ttu-id="dca20-500">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-501">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-502">'Blazor'</span></span>
- <span data-ttu-id="dca20-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-503">'Identity'</span></span>
- <span data-ttu-id="dca20-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-505">'Razor'</span></span>
- <span data-ttu-id="dca20-506">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-507">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-508">'Blazor'</span></span>
- <span data-ttu-id="dca20-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-509">'Identity'</span></span>
- <span data-ttu-id="dca20-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-511">'Razor'</span></span>
- <span data-ttu-id="dca20-512">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-513">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-514">'Blazor'</span></span>
- <span data-ttu-id="dca20-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-515">'Identity'</span></span>
- <span data-ttu-id="dca20-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-517">'Razor'</span></span>
- <span data-ttu-id="dca20-518">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-518">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-519">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-520">'Blazor'</span></span>
- <span data-ttu-id="dca20-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-521">'Identity'</span></span>
- <span data-ttu-id="dca20-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-523">'Razor'</span></span>
- <span data-ttu-id="dca20-524">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-525">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-526">'Blazor'</span></span>
- <span data-ttu-id="dca20-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-527">'Identity'</span></span>
- <span data-ttu-id="dca20-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-529">'Razor'</span></span>
- <span data-ttu-id="dca20-530">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-531">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-532">'Blazor'</span></span>
- <span data-ttu-id="dca20-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-533">'Identity'</span></span>
- <span data-ttu-id="dca20-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-535">'Razor'</span></span>
- <span data-ttu-id="dca20-536">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-537">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-538">'Blazor'</span></span>
- <span data-ttu-id="dca20-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-539">'Identity'</span></span>
- <span data-ttu-id="dca20-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-541">'Razor'</span></span>
- <span data-ttu-id="dca20-542">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-543">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-544">'Blazor'</span></span>
- <span data-ttu-id="dca20-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-545">'Identity'</span></span>
- <span data-ttu-id="dca20-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-547">'Razor'</span></span>
- <span data-ttu-id="dca20-548">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-549">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-550">'Blazor'</span></span>
- <span data-ttu-id="dca20-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-551">'Identity'</span></span>
- <span data-ttu-id="dca20-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-553">'Razor'</span></span>
- <span data-ttu-id="dca20-554">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-555">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-556">'Blazor'</span></span>
- <span data-ttu-id="dca20-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-557">'Identity'</span></span>
- <span data-ttu-id="dca20-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-559">'Razor'</span></span>
- <span data-ttu-id="dca20-560">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-561">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-562">'Blazor'</span></span>
- <span data-ttu-id="dca20-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-563">'Identity'</span></span>
- <span data-ttu-id="dca20-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-565">'Razor'</span></span>
- <span data-ttu-id="dca20-566">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-567">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-568">'Blazor'</span></span>
- <span data-ttu-id="dca20-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-569">'Identity'</span></span>
- <span data-ttu-id="dca20-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-571">'Razor'</span></span>
- <span data-ttu-id="dca20-572">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dca20-573">Заголовок: Автор: описание: Моникерранже: MS. author: MS. Custom: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="dca20-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dca20-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dca20-574">'Blazor'</span></span>
- <span data-ttu-id="dca20-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dca20-575">'Identity'</span></span>
- <span data-ttu-id="dca20-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dca20-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="dca20-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dca20-577">'Razor'</span></span>
- <span data-ttu-id="dca20-578">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dca20-578">'SignalR' uid:</span></span> 

<span data-ttu-id="dca20-579">-------------------------------- | | Facebook | `https://www.facebook.com/dialog/oauth`                          |
| Google | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter |`https://api.twitter.com/oauth/authenticate`                     |</span><span class="sxs-lookup"><span data-stu-id="dca20-579">-------------------------------- | | Facebook  | `https://www.facebook.com/dialog/oauth`                          |
| Google    | `https://www.googleapis.com/auth/userinfo.profile`               |
| Microsoft | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| Twitter   | `https://api.twitter.com/oauth/authenticate`                     |</span></span>

<span data-ttu-id="dca20-580">В примере приложения `userinfo.profile` область Google автоматически добавляется платформой при <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> вызове в <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="dca20-580">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="dca20-581">Если приложению требуются дополнительные области, добавьте их в параметры.</span><span class="sxs-lookup"><span data-stu-id="dca20-581">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="dca20-582">В следующем примере `https://www.googleapis.com/auth/user.birthday.read` область Google добавляется для получения дня рождения пользователя:</span><span class="sxs-lookup"><span data-stu-id="dca20-582">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="dca20-583">Сопоставьте ключи данных пользователя и создайте утверждения</span><span class="sxs-lookup"><span data-stu-id="dca20-583">Map user data keys and create claims</span></span>

<span data-ttu-id="dca20-584">В параметрах поставщика укажите <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> или для каждого ключа или подключа <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> в данных пользователя JSON внешнего поставщика, чтобы удостоверение приложения было прочитано при входе.</span><span class="sxs-lookup"><span data-stu-id="dca20-584">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="dca20-585">Дополнительные сведения о типах утверждений см. в разделе <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="dca20-585">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="dca20-586">Пример приложения создает утверждения locale ( `urn:google:locale` ) и Picture ( `urn:google:picture` ) из `locale` `picture` ключей и в данных пользователя Google:</span><span class="sxs-lookup"><span data-stu-id="dca20-586">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="dca20-587">В службах `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` <xref:Microsoft.AspNetCore.Identity.IdentityUser> ( `ApplicationUser` ) выполняется вход в приложение с помощью <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="dca20-587">In `Microsoft.AspNetCore.Identity.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.Identity.IdentityUser> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.Identity.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="dca20-588">Во время входа в систему <xref:Microsoft.AspNetCore.Identity.UserManager%601> можно хранить `ApplicationUser` утверждения для пользовательских данных, доступных в <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="dca20-588">During the sign in process, the <xref:Microsoft.AspNetCore.Identity.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="dca20-589">В примере приложения `OnPostConfirmationAsync` (*Account/екстерналлогин. cshtml. CS*) устанавливает утверждения языкового стандарта ( `urn:google:locale` ) и изображения ( `urn:google:picture` ) для входа `ApplicationUser` , включая утверждение для <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="dca20-589">In the sample app, `OnPostConfirmationAsync` (*Account/ExternalLogin.cshtml.cs*) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="dca20-590">По умолчанию утверждения пользователя хранятся в файле cookie проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca20-590">By default, a user's claims are stored in the authentication cookie.</span></span> <span data-ttu-id="dca20-591">Если файл cookie для проверки подлинности слишком большой, это может привести к сбою приложения по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="dca20-591">If the authentication cookie is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="dca20-592">Браузер обнаруживает, что заголовок файла cookie слишком длинный.</span><span class="sxs-lookup"><span data-stu-id="dca20-592">The browser detects that the cookie header is too long.</span></span>
* <span data-ttu-id="dca20-593">Общий размер запроса слишком велик.</span><span class="sxs-lookup"><span data-stu-id="dca20-593">The overall size of the request is too large.</span></span>

<span data-ttu-id="dca20-594">Если для обработки запросов пользователей требуется большой объем пользовательских данных:</span><span class="sxs-lookup"><span data-stu-id="dca20-594">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="dca20-595">Ограничьте количество и размер утверждений пользователей для обработки запроса только тем, что требуется приложению.</span><span class="sxs-lookup"><span data-stu-id="dca20-595">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="dca20-596">Используйте пользовательский <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> по промежуточного слоя для проверки подлинности файлов cookie <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> для хранения удостоверений в запросах.</span><span class="sxs-lookup"><span data-stu-id="dca20-596">Use a custom <xref:Microsoft.AspNetCore.Authentication.Cookies.ITicketStore> for the Cookie Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="dca20-597">Сохраняйте большие объемы информации об удостоверениях на сервере, при этом клиенту отправляется только небольшой ключ идентификатора сеанса.</span><span class="sxs-lookup"><span data-stu-id="dca20-597">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="dca20-598">Сохранение маркера доступа</span><span class="sxs-lookup"><span data-stu-id="dca20-598">Save the access token</span></span>

<span data-ttu-id="dca20-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*>Определяет, должны ли маркеры доступа и обновления храниться в <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> после успешной авторизации.</span><span class="sxs-lookup"><span data-stu-id="dca20-599"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="dca20-600">`SaveTokens`по `false` умолчанию имеет значение, чтобы уменьшить размер файла cookie окончательной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dca20-600">`SaveTokens` is set to `false` by default to reduce the size of the final authentication cookie.</span></span>

<span data-ttu-id="dca20-601">Пример приложения задает для параметра значение `SaveTokens` `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="dca20-601">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="dca20-602">Когда `OnPostConfirmationAsync` выполняется, сохраните маркер доступа ([Екстерналлогининфо. аусентикатионтокенс](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) из внешнего поставщика в `ApplicationUser` `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="dca20-602">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.Identity.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="dca20-603">Пример приложения сохраняет маркер доступа в `OnPostConfirmationAsync` (регистрация нового пользователя) и `OnGetCallbackAsync` (ранее зарегистрированный пользователь) в *Account/екстерналлогин. cshtml. CS*:</span><span class="sxs-lookup"><span data-stu-id="dca20-603">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs*:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/Identity/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="dca20-604">Добавление дополнительных настраиваемых токенов</span><span class="sxs-lookup"><span data-stu-id="dca20-604">How to add additional custom tokens</span></span>

<span data-ttu-id="dca20-605">Чтобы продемонстрировать, как добавить пользовательский маркер, который хранится в составе `SaveTokens` , пример приложения добавляет объект <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> с текущим <xref:System.DateTime> для [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) из `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="dca20-605">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="dca20-606">Создание и добавление утверждений</span><span class="sxs-lookup"><span data-stu-id="dca20-606">Creating and adding claims</span></span>

<span data-ttu-id="dca20-607">Платформа предоставляет общие действия и методы расширения для создания и добавления заявок в коллекцию.</span><span class="sxs-lookup"><span data-stu-id="dca20-607">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="dca20-608">Дополнительные сведения см. в разделах <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> и <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="dca20-608">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="dca20-609">Пользователи могут определять пользовательские действия, производя от <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> и реализуя абстрактный <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> метод.</span><span class="sxs-lookup"><span data-stu-id="dca20-609">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="dca20-610">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="dca20-610">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="dca20-611">Удаление действий утверждений и утверждений</span><span class="sxs-lookup"><span data-stu-id="dca20-611">Removal of claim actions and claims</span></span>

<span data-ttu-id="dca20-612">[Клаимактионколлектион. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) удаляет все действия с утверждениями для заданного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> из коллекции.</span><span class="sxs-lookup"><span data-stu-id="dca20-612">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="dca20-613">[Клаимактионколлектионмапекстенсионс. делетеклаим (клаимактионколлектион, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) удаляет утверждение для данного <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> удостоверения.</span><span class="sxs-lookup"><span data-stu-id="dca20-613">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="dca20-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*>в основном используется с [OpenID Connect Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) для удаления сформированных протоколом утверждений.</span><span class="sxs-lookup"><span data-stu-id="dca20-614"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="dca20-615">Выходные данные примера приложения</span><span class="sxs-lookup"><span data-stu-id="dca20-615">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.Identity.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="dca20-616">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="dca20-616">Additional resources</span></span>

* <span data-ttu-id="dca20-617">[соЦиалсампле приложение DotNet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): связанный пример приложения находится в технологической ветви [DotNet/AspNetCore в репозитории GitHub](https://github.com/dotnet/AspNetCore) `master` .</span><span class="sxs-lookup"><span data-stu-id="dca20-617">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="dca20-618">`master`Ветвь содержит код в разделе активная разработка для следующего выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dca20-618">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="dca20-619">Чтобы просмотреть версию примера приложения для выпущенной версии ASP.NET Core, используйте раскрывающийся список **ветвь** для выбора ветви выпуска (например, `release/{X.Y}` ).</span><span class="sxs-lookup"><span data-stu-id="dca20-619">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
