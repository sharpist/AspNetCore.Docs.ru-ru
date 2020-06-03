---
<span data-ttu-id="25ce6-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-102">'Blazor'</span></span>
- <span data-ttu-id="25ce6-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-103">'Identity'</span></span>
- <span data-ttu-id="25ce6-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-105">'Razor'</span></span>
- <span data-ttu-id="25ce6-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="25ce6-107">ПО промежуточного слоя для переопределения URL-адресов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25ce6-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="25ce6-108">Авторы: [Микаэль Менгисту](https://github.com/mikaelm12) (Mikael Mengistu)</span><span class="sxs-lookup"><span data-stu-id="25ce6-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="25ce6-109">Этот документ содержит вводные сведения о переопределении URL-адресов и инструкции по использованию ПО промежуточного слоя для переопределения URL-адресов в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25ce6-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="25ce6-110">Переопределение URL-адресов представляет собой изменение URL-адресов запросов на основе одного или нескольких предопределенных правил.</span><span class="sxs-lookup"><span data-stu-id="25ce6-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="25ce6-111">Переопределение URL-адресов создает абстракцию между расположениями ресурсов и их адресами, позволяя убрать тесную связь между ними.</span><span class="sxs-lookup"><span data-stu-id="25ce6-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="25ce6-112">Существует несколько сценариев, где может пригодиться переопределение URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="25ce6-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="25ce6-113">Временное или постоянное перемещение или замещение ресурсов сервера с сохранением стабильных указателей для этих ресурсов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="25ce6-114">Разделение обработки запросов между разными приложениями или разными областями одного приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="25ce6-115">Удаление, добавление или переупорядочение сегментов URL-адресов для входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="25ce6-116">Оптимизация общедоступных URL-адресов в целях оптимизации для поисковых систем (SEO).</span><span class="sxs-lookup"><span data-stu-id="25ce6-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="25ce6-117">Разрешение использования понятных общедоступных URL-адресов, чтобы помочь посетителям прогнозировать содержимое, возвращаемое в результате запроса ресурса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="25ce6-118">Перенаправление небезопасных запросов на защищенные конечные точки.</span><span class="sxs-lookup"><span data-stu-id="25ce6-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="25ce6-119">Запрет вставки прямых ссылок, когда внешний сайт использует статический ресурс, размещенный на другом сайте, привязывая ресурс к своему содержимому.</span><span class="sxs-lookup"><span data-stu-id="25ce6-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="25ce6-120">Переопределение URL-адресов может снижать производительность приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="25ce6-121">Следует по возможности ограничить число и сложность правил.</span><span class="sxs-lookup"><span data-stu-id="25ce6-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="25ce6-122">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="25ce6-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="25ce6-123">Перенаправление и переопределение URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="25ce6-124">Разница между *перенаправлением* и *переопределением* URL-адресов может показаться незначительной, но она оказывает существенное влияние на предоставление ресурсов клиентам.</span><span class="sxs-lookup"><span data-stu-id="25ce6-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="25ce6-125">ПО промежуточного слоя для переопределения URL-адресов в ASP.NET Core удовлетворяет потребность в обеих этих функциях.</span><span class="sxs-lookup"><span data-stu-id="25ce6-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="25ce6-126">*Перенаправление URL-адресов* является операцией на стороне клиента, которая предписывает клиенту обратиться к ресурсу по другому адресу, отличному от запрошенного клиентом.</span><span class="sxs-lookup"><span data-stu-id="25ce6-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="25ce6-127">Для этого используется круговое обращение к серверу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-127">This requires a round trip to the server.</span></span> <span data-ttu-id="25ce6-128">URL-адрес перенаправления, возвращаемый клиенту, отображается в адресной строке браузера, когда клиент отправляет новый запрос для данного ресурса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="25ce6-129">Если `/resource`*перенаправляется* на `/different-resource`, сервер отвечает, что клиенту следует получить ресурс в `/different-resource` с кодом состояния, указывающим, что такое перенаправление является временным или постоянным.</span><span class="sxs-lookup"><span data-stu-id="25ce6-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Конечная точка службы WebAPI временно изменена с версии 1 (v1) на версию 2 (v2) на сервере.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="25ce6-135">При перенаправлении запросов на другой URL-адрес можно указать, является ли оно постоянным или временным, предоставив код состояния в ответе:</span><span class="sxs-lookup"><span data-stu-id="25ce6-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="25ce6-136">Код состояния *301 (перемещен окончательно)* используется, когда ресурс имеет новый постоянный URL-адрес и вы хотите сообщить клиенту, что все последующие запросы для этого ресурса должны использовать новый URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="25ce6-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="25ce6-137">*Клиент может кэшировать и повторно использовать отклик при получении кода состояния 301.*</span><span class="sxs-lookup"><span data-stu-id="25ce6-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="25ce6-138">Код состояния *302 (найдено)* используется, когда перенаправление является временным или может меняться.</span><span class="sxs-lookup"><span data-stu-id="25ce6-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="25ce6-139">Код состояния 302 указывает клиенту, что не нужно сохранять URL-адрес и использовать его в будущем.</span><span class="sxs-lookup"><span data-stu-id="25ce6-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="25ce6-140">Дополнительные сведения о кодах состояния см. в документе [RFC 2616: определения кодов состояния](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="25ce6-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="25ce6-141">*Переопределение URL-адреса* является операцией на стороне сервера для предоставления ресурса с другого адреса ресурса, отличного от запрошенного клиентом.</span><span class="sxs-lookup"><span data-stu-id="25ce6-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="25ce6-142">Переопределение URL-адреса не требует кругового обращения к серверу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="25ce6-143">Переопределенный URL-адрес не возвращается клиенту и не отображается в адресной строке браузера.</span><span class="sxs-lookup"><span data-stu-id="25ce6-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="25ce6-144">Когда `/resource`*переопределяется* на `/different-resource`, сервер получает и запрашивает ресурс *внутри* в `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="25ce6-145">Хотя клиент может быть в состоянии получить ресурс по переопределенному URL-адресу, когда клиент отправляет запрос и получает отклик, он не уведомляется о наличии ресурса по переопределенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Конечная точка службы WebAPI была изменена с версии 1 (v1) на версию 2 (v2) на сервере.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="25ce6-150">Пример приложения с переопределением URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-150">URL rewriting sample app</span></span>

<span data-ttu-id="25ce6-151">Вы можете ознакомиться с функциями ПО промежуточного слоя переопределения URL-адресов на [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="25ce6-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="25ce6-152">Это приложение применяет правила перенаправления и переопределения и показывает перенаправленный или переопределенный URL-адрес для нескольких сценариев.</span><span class="sxs-lookup"><span data-stu-id="25ce6-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="25ce6-153">Условия для использования ПО промежуточного слоя для переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="25ce6-154">Используйте ПО промежуточного слоя для переопределения URL-адресов, если невозможно использовать следующие подходы:</span><span class="sxs-lookup"><span data-stu-id="25ce6-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="25ce6-155">Модуль переопределения URL-адресов со службами IIS на Windows Server</span><span class="sxs-lookup"><span data-stu-id="25ce6-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="25ce6-156">Модуль Apache mod_rewrite на сервере Apache Server</span><span class="sxs-lookup"><span data-stu-id="25ce6-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="25ce6-157">Переопределение URL-адресов в Nginx</span><span class="sxs-lookup"><span data-stu-id="25ce6-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="25ce6-158">Кроме того, используйте ПО промежуточного слоя, когда приложение размещается на [сервере HTTP.sys](xref:fundamentals/servers/httpsys) (который раньше назывался WebListener).</span><span class="sxs-lookup"><span data-stu-id="25ce6-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="25ce6-159">Основные причины для использования переопределения URL-адресов на основе сервера в IIS, Apache и Nginx:</span><span class="sxs-lookup"><span data-stu-id="25ce6-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="25ce6-160">ПО промежуточного слоя не поддерживает все функции этих модулей.</span><span class="sxs-lookup"><span data-stu-id="25ce6-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="25ce6-161">Некоторые функции серверных модулей не работают с проектами ASP.NET Core, например ограничения `IsFile` и `IsDirectory` для модуля переопределения IIS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="25ce6-162">В этих случаях следует использовать ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="25ce6-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="25ce6-163">Производительность ПО промежуточного слоя, скорее всего, не соответствует производительности модулей.</span><span class="sxs-lookup"><span data-stu-id="25ce6-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="25ce6-164">Тестирование производительности — это единственный способ узнать точно, какой подход больше всего снижает производительность или является ли такое снижение незначительным.</span><span class="sxs-lookup"><span data-stu-id="25ce6-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="25ce6-165">Пакет</span><span class="sxs-lookup"><span data-stu-id="25ce6-165">Package</span></span>

<span data-ttu-id="25ce6-166">ПО промежуточного слоя перезаписи URL-адресов предоставляется пакетом [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite), который неявно включается в приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25ce6-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="25ce6-167">Расширение и параметры</span><span class="sxs-lookup"><span data-stu-id="25ce6-167">Extension and options</span></span>

<span data-ttu-id="25ce6-168">Задайте правила переопределения и перенаправления URL-адресов, создав экземпляр класса [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) для каждого правила с помощью методов расширения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="25ce6-169">Несколько правил можно объединить в цепочку в том порядке, в котором они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="25ce6-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="25ce6-170">`RewriteOptions` передаются в ПО промежуточного слоя для переопределения URL-адресов по мере его добавления в конвейер запросов с помощью <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="25ce6-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="25ce6-171">Перенаправление не www на www</span><span class="sxs-lookup"><span data-stu-id="25ce6-171">Redirect non-www to www</span></span>

<span data-ttu-id="25ce6-172">Три параметра разрешают приложению перенаправлять запросы, отличные от `www`, на `www`:</span><span class="sxs-lookup"><span data-stu-id="25ce6-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="25ce6-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>. Окончательно перенаправляет запрос в поддомен `www`, если запрос не является `www`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="25ce6-174">Перенаправляет с кодом состояния [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="25ce6-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="25ce6-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>. Перенаправляет запрос в поддомен `www`, если входящий запрос не является `www`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="25ce6-176">Перенаправляет с кодом состояния [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="25ce6-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="25ce6-177">Перегрузка позволяет предоставить код состояния для ответа.</span><span class="sxs-lookup"><span data-stu-id="25ce6-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="25ce6-178">Используйте поле класса <xref:Microsoft.AspNetCore.Http.StatusCodes> для назначения кода состояния.</span><span class="sxs-lookup"><span data-stu-id="25ce6-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="25ce6-179">Перенаправление URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-179">URL redirect</span></span>

<span data-ttu-id="25ce6-180">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> для перенаправления запросов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="25ce6-181">Первый параметр содержит регулярное выражение, соответствующее пути входящего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="25ce6-182">Второй параметр является строкой замены.</span><span class="sxs-lookup"><span data-stu-id="25ce6-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="25ce6-183">Третий параметр (при его наличии) указывает код состояния.</span><span class="sxs-lookup"><span data-stu-id="25ce6-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="25ce6-184">Если код состояния не задан, по умолчанию используется код *302 (найдено)* , указывающий, что ресурс временно перемещен или заменен.</span><span class="sxs-lookup"><span data-stu-id="25ce6-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="25ce6-185">В браузере с включенными средствами разработчика выполните запрос для примера приложения по пути `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="25ce6-186">Регулярное выражение соответствует пути запроса в `redirect-rule/(.*)`, и этот путь заменяется на `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="25ce6-187">URL-адрес перенаправления отправляется обратно клиенту с кодом состояния *302 (найдено)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="25ce6-188">Браузер выполняет новый запрос на URL-адрес перенаправления, отображаемый в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="25ce6-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="25ce6-189">Поскольку в примере приложения нет правил, соответствующих URL-адресу перенаправления:</span><span class="sxs-lookup"><span data-stu-id="25ce6-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="25ce6-190">Второй запрос получает от приложения ответ *200 (ОК)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="25ce6-191">Текст ответа показывает URL-адрес перенаправления.</span><span class="sxs-lookup"><span data-stu-id="25ce6-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="25ce6-192">При *перенаправлении* URL-адреса используется круговое обращение к серверу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="25ce6-193">Соблюдайте осторожность при задании правил перенаправления.</span><span class="sxs-lookup"><span data-stu-id="25ce6-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="25ce6-194">Они оцениваются для каждого запроса, отправляемого в приложение, даже после перенаправления.</span><span class="sxs-lookup"><span data-stu-id="25ce6-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="25ce6-195">Можно легко случайно создать *бесконечный цикл перенаправлений*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="25ce6-196">Исходный запрос: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="25ce6-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="25ce6-198">Часть выражения, заключенная в скобки, называется *группой записи*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="25ce6-199">Точка (`.`) в выражении означает *совпадение с любым символом*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="25ce6-200">Звездочка (`*`) означает *совпадение с предыдущим символом ноль или более раз*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="25ce6-201">Таким образом, два последних сегмента пути URL-адреса, `1234/5678`, перехватываются группой записи `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="25ce6-202">Любое значение, указанное в URL-адресе запроса после `redirect-rule/`, перехватывается этой группой записи.</span><span class="sxs-lookup"><span data-stu-id="25ce6-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="25ce6-203">В строке замены группы записи внедряются с помощью знака доллара (`$`), за которым следует порядковый номер записи.</span><span class="sxs-lookup"><span data-stu-id="25ce6-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="25ce6-204">Первое значение группы записи получается с помощью `$1`, второе — с помощью `$2`, после чего они продолжают по очереди использоваться для групп записи в регулярном выражении.</span><span class="sxs-lookup"><span data-stu-id="25ce6-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="25ce6-205">В регулярном выражении правила перенаправления в примере приложения присутствует всего одна группа записи, поэтому в строку замены внедряется тоже одна строка — `$1`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="25ce6-206">Когда применяется правило, URL-адрес становится `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="25ce6-207">Перенаправление URL-адресов на защищенную конечную точку</span><span class="sxs-lookup"><span data-stu-id="25ce6-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="25ce6-208">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>, чтобы перенаправлять HTTP-запросы на тот же узел и путь с использованием протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="25ce6-209">Если код состояния не указан, ПО промежуточного слоя по умолчанию использует значение *302 (найдено)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="25ce6-210">Если порт не указан:</span><span class="sxs-lookup"><span data-stu-id="25ce6-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="25ce6-211">ПО промежуточного слоя по умолчанию устанавливается на `null`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="25ce6-212">Схема меняется на `https` (протокол HTTPS), и клиент обращается к ресурсу через порт 443.</span><span class="sxs-lookup"><span data-stu-id="25ce6-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="25ce6-213">Этот пример показывает, как задать код состояния *301 (перемещен окончательно)* и изменить порт на 5001.</span><span class="sxs-lookup"><span data-stu-id="25ce6-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="25ce6-214">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>, чтобы перенаправить небезопасные запросы на тот же узел и путь с использованием безопасного протокола HTTPS через порт 443.</span><span class="sxs-lookup"><span data-stu-id="25ce6-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="25ce6-215">ПО промежуточного слоя задает код состояния *301 (перемещен окончательно)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="25ce6-216">При перенаправлении на защищенную конечную точку без дополнительных правил перенаправления рекомендуется использовать ПО промежуточного слоя перенаправления на HTTPS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="25ce6-217">Дополнительные сведения см. в разделе [Обязательное использование HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="25ce6-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="25ce6-218">Пример приложения позволяет продемонстрировать использование `AddRedirectToHttps` или `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="25ce6-219">Добавьте этот метод расширения в `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="25ce6-220">Выполните небезопасный запрос к приложению по любому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="25ce6-221">Закройте предостережение системы безопасности о том, что самозаверяющий сертификат не является доверенным, или создайте исключение, чтобы сделать сертификат доверенным.</span><span class="sxs-lookup"><span data-stu-id="25ce6-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="25ce6-222">Исходный запрос с использованием `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="25ce6-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="25ce6-224">Исходный запрос с использованием `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="25ce6-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="25ce6-226">Переопределение URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-226">URL rewrite</span></span>

<span data-ttu-id="25ce6-227">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>, чтобы создать правило для переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="25ce6-228">Первый параметр содержит регулярное выражение, соответствующее пути входящего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="25ce6-229">Второй параметр является строкой замены.</span><span class="sxs-lookup"><span data-stu-id="25ce6-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="25ce6-230">Третий параметр `skipRemainingRules: {true|false}` сообщает ПО промежуточного слоя, нужно ли пропустить дополнительные правила переопределения, если применяется текущее правило.</span><span class="sxs-lookup"><span data-stu-id="25ce6-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="25ce6-231">Исходный запрос: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="25ce6-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запрос и отклик](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="25ce6-233">Карет (`^`) в начале выражение означает, что сопоставление начинается с начала URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="25ce6-234">В предыдущем примере с правилом перенаправления, `redirect-rule/(.*)`, в начале регулярного выражения нет символа `^`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="25ce6-235">Таким образом, для успешного совпадения до `redirect-rule/` в пути могут стоять любые символы.</span><span class="sxs-lookup"><span data-stu-id="25ce6-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="25ce6-236">Path</span><span class="sxs-lookup"><span data-stu-id="25ce6-236">Path</span></span>                               | <span data-ttu-id="25ce6-237">Соответствие</span><span class="sxs-lookup"><span data-stu-id="25ce6-237">Match</span></span> |
| ---
<span data-ttu-id="25ce6-238">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-239">'Blazor'</span></span>
- <span data-ttu-id="25ce6-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-240">'Identity'</span></span>
- <span data-ttu-id="25ce6-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-242">'Razor'</span></span>
- <span data-ttu-id="25ce6-243">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-244">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-245">'Blazor'</span></span>
- <span data-ttu-id="25ce6-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-246">'Identity'</span></span>
- <span data-ttu-id="25ce6-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-248">'Razor'</span></span>
- <span data-ttu-id="25ce6-249">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-250">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-251">'Blazor'</span></span>
- <span data-ttu-id="25ce6-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-252">'Identity'</span></span>
- <span data-ttu-id="25ce6-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-254">'Razor'</span></span>
- <span data-ttu-id="25ce6-255">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-256">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-257">'Blazor'</span></span>
- <span data-ttu-id="25ce6-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-258">'Identity'</span></span>
- <span data-ttu-id="25ce6-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-260">'Razor'</span></span>
- <span data-ttu-id="25ce6-261">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-262">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-263">'Blazor'</span></span>
- <span data-ttu-id="25ce6-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-264">'Identity'</span></span>
- <span data-ttu-id="25ce6-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-266">'Razor'</span></span>
- <span data-ttu-id="25ce6-267">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-268">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-269">'Blazor'</span></span>
- <span data-ttu-id="25ce6-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-270">'Identity'</span></span>
- <span data-ttu-id="25ce6-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-272">'Razor'</span></span>
- <span data-ttu-id="25ce6-273">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-274">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-275">'Blazor'</span></span>
- <span data-ttu-id="25ce6-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-276">'Identity'</span></span>
- <span data-ttu-id="25ce6-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-278">'Razor'</span></span>
- <span data-ttu-id="25ce6-279">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-280">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-281">'Blazor'</span></span>
- <span data-ttu-id="25ce6-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-282">'Identity'</span></span>
- <span data-ttu-id="25ce6-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-284">'Razor'</span></span>
- <span data-ttu-id="25ce6-285">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-286">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-287">'Blazor'</span></span>
- <span data-ttu-id="25ce6-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-288">'Identity'</span></span>
- <span data-ttu-id="25ce6-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-290">'Razor'</span></span>
- <span data-ttu-id="25ce6-291">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-292">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-293">'Blazor'</span></span>
- <span data-ttu-id="25ce6-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-294">'Identity'</span></span>
- <span data-ttu-id="25ce6-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-296">'Razor'</span></span>
- <span data-ttu-id="25ce6-297">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-298">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-299">'Blazor'</span></span>
- <span data-ttu-id="25ce6-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-300">'Identity'</span></span>
- <span data-ttu-id="25ce6-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-302">'Razor'</span></span>
- <span data-ttu-id="25ce6-303">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-304">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-305">'Blazor'</span></span>
- <span data-ttu-id="25ce6-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-306">'Identity'</span></span>
- <span data-ttu-id="25ce6-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-308">'Razor'</span></span>
- <span data-ttu-id="25ce6-309">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-310">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-311">'Blazor'</span></span>
- <span data-ttu-id="25ce6-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-312">'Identity'</span></span>
- <span data-ttu-id="25ce6-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-314">'Razor'</span></span>
- <span data-ttu-id="25ce6-315">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-316">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-317">'Blazor'</span></span>
- <span data-ttu-id="25ce6-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-318">'Identity'</span></span>
- <span data-ttu-id="25ce6-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-320">'Razor'</span></span>
- <span data-ttu-id="25ce6-321">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-322">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-323">'Blazor'</span></span>
- <span data-ttu-id="25ce6-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-324">'Identity'</span></span>
- <span data-ttu-id="25ce6-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-326">'Razor'</span></span>
- <span data-ttu-id="25ce6-327">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-327">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Да   | | `/my-cool-redirect-rule/1234/5678` | Да   | | `/anotherredirect-rule/1234/5678`  | Да   |</span><span class="sxs-lookup"><span data-stu-id="25ce6-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="25ce6-329">Правило переопределения `^rewrite-rule/(\d+)/(\d+)` соответствует путям, только если они начинаются с `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="25ce6-330">В следующей таблице обратите внимание на разницу в сопоставлении.</span><span class="sxs-lookup"><span data-stu-id="25ce6-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="25ce6-331">Path</span><span class="sxs-lookup"><span data-stu-id="25ce6-331">Path</span></span>                              | <span data-ttu-id="25ce6-332">Соответствие</span><span class="sxs-lookup"><span data-stu-id="25ce6-332">Match</span></span> |
| ---
<span data-ttu-id="25ce6-333">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-334">'Blazor'</span></span>
- <span data-ttu-id="25ce6-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-335">'Identity'</span></span>
- <span data-ttu-id="25ce6-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-337">'Razor'</span></span>
- <span data-ttu-id="25ce6-338">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-339">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-340">'Blazor'</span></span>
- <span data-ttu-id="25ce6-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-341">'Identity'</span></span>
- <span data-ttu-id="25ce6-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-343">'Razor'</span></span>
- <span data-ttu-id="25ce6-344">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-345">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-346">'Blazor'</span></span>
- <span data-ttu-id="25ce6-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-347">'Identity'</span></span>
- <span data-ttu-id="25ce6-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-349">'Razor'</span></span>
- <span data-ttu-id="25ce6-350">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-351">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-352">'Blazor'</span></span>
- <span data-ttu-id="25ce6-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-353">'Identity'</span></span>
- <span data-ttu-id="25ce6-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-355">'Razor'</span></span>
- <span data-ttu-id="25ce6-356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-357">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-358">'Blazor'</span></span>
- <span data-ttu-id="25ce6-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-359">'Identity'</span></span>
- <span data-ttu-id="25ce6-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-361">'Razor'</span></span>
- <span data-ttu-id="25ce6-362">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-363">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-364">'Blazor'</span></span>
- <span data-ttu-id="25ce6-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-365">'Identity'</span></span>
- <span data-ttu-id="25ce6-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-367">'Razor'</span></span>
- <span data-ttu-id="25ce6-368">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-369">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-370">'Blazor'</span></span>
- <span data-ttu-id="25ce6-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-371">'Identity'</span></span>
- <span data-ttu-id="25ce6-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-373">'Razor'</span></span>
- <span data-ttu-id="25ce6-374">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-375">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-376">'Blazor'</span></span>
- <span data-ttu-id="25ce6-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-377">'Identity'</span></span>
- <span data-ttu-id="25ce6-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-379">'Razor'</span></span>
- <span data-ttu-id="25ce6-380">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-381">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-382">'Blazor'</span></span>
- <span data-ttu-id="25ce6-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-383">'Identity'</span></span>
- <span data-ttu-id="25ce6-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-385">'Razor'</span></span>
- <span data-ttu-id="25ce6-386">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-387">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-388">'Blazor'</span></span>
- <span data-ttu-id="25ce6-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-389">'Identity'</span></span>
- <span data-ttu-id="25ce6-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-391">'Razor'</span></span>
- <span data-ttu-id="25ce6-392">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-393">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-394">'Blazor'</span></span>
- <span data-ttu-id="25ce6-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-395">'Identity'</span></span>
- <span data-ttu-id="25ce6-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-397">'Razor'</span></span>
- <span data-ttu-id="25ce6-398">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-399">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-400">'Blazor'</span></span>
- <span data-ttu-id="25ce6-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-401">'Identity'</span></span>
- <span data-ttu-id="25ce6-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-403">'Razor'</span></span>
- <span data-ttu-id="25ce6-404">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-405">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-406">'Blazor'</span></span>
- <span data-ttu-id="25ce6-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-407">'Identity'</span></span>
- <span data-ttu-id="25ce6-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-409">'Razor'</span></span>
- <span data-ttu-id="25ce6-410">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-411">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-412">'Blazor'</span></span>
- <span data-ttu-id="25ce6-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-413">'Identity'</span></span>
- <span data-ttu-id="25ce6-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-415">'Razor'</span></span>
- <span data-ttu-id="25ce6-416">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-416">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Да   | | `/my-cool-rewrite-rule/1234/5678` | Нет    | | `/anotherrewrite-rule/1234/5678`  | Нет    |</span><span class="sxs-lookup"><span data-stu-id="25ce6-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="25ce6-418">После части `^rewrite-rule/` выражения стоят две группы записи `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="25ce6-419">`\d` означает *соответствие цифре (числу)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="25ce6-420">Знак "плюс" (`+`) означает *соответствие одному или нескольким предшествующим символам*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="25ce6-421">Таким образом, URL-адрес должен содержать число, за которым идет прямая косая черта, за которой идет другое число.</span><span class="sxs-lookup"><span data-stu-id="25ce6-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="25ce6-422">Эти группы записи внедряются в переопределенный URL-адрес как `$1` и `$2`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="25ce6-423">Строка замены для правила переопределения помещает группы записи в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="25ce6-424">Запрошенный путь `/rewrite-rule/1234/5678` переопределяется, чтобы ресурс можно было получить по адресу `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="25ce6-425">Если в исходном запросе присутствует строка запроса, она сохраняется при переопределении URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="25ce6-426">Круговое обращение к серверу для получения ресурса не выполняется.</span><span class="sxs-lookup"><span data-stu-id="25ce6-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="25ce6-427">Если ресурс существует, он извлекается и возвращается клиенту с кодом состояния *200 (ОК)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="25ce6-428">Так как клиент не перенаправляется, URL-адрес в адресной строке браузера не изменяется.</span><span class="sxs-lookup"><span data-stu-id="25ce6-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="25ce6-429">Клиенты не могут узнать, что на сервере произошла операция переопределения URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="25ce6-430">Используйте `skipRemainingRules: true` при любой возможности, так как правила сопоставления потребляют много ресурсов и ухудшают время отклика приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="25ce6-431">Чтобы максимально ускорить отклик приложения:</span><span class="sxs-lookup"><span data-stu-id="25ce6-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="25ce6-432">расположите правила переопределения в порядке от наиболее к наименее часто используемому;</span><span class="sxs-lookup"><span data-stu-id="25ce6-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="25ce6-433">пропускайте обработку оставшихся правил, когда совпадение найдено и обработка дополнительных правил не требуется.</span><span class="sxs-lookup"><span data-stu-id="25ce6-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="25ce6-434">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="25ce6-434">Apache mod_rewrite</span></span>

<span data-ttu-id="25ce6-435">Для применения правил mod_rewrite Apache можно использовать <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="25ce6-436">Убедитесь, что файл правил развертывается вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="25ce6-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="25ce6-437">Дополнительные сведения и примеры правил mod_rewrite см. в статье о [правилах mod_rewrite Apache](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="25ce6-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="25ce6-438"><xref:System.IO.StreamReader> используется для чтения правил из файла *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="25ce6-439">Пример приложения перенаправляет запросы из `/apache-mod-rules-redirect/(.\*)` в `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="25ce6-440">Отклик имеет код состояния *302 (найдено)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="25ce6-441">Исходный запрос: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="25ce6-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="25ce6-443">ПО промежуточного слоя поддерживает следующие переменные сервера в mod_rewrite Apache:</span><span class="sxs-lookup"><span data-stu-id="25ce6-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="25ce6-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="25ce6-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="25ce6-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="25ce6-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="25ce6-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="25ce6-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="25ce6-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="25ce6-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="25ce6-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="25ce6-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="25ce6-449">HTTP_HOST</span></span>
* <span data-ttu-id="25ce6-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="25ce6-450">HTTP_REFERER</span></span>
* <span data-ttu-id="25ce6-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="25ce6-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="25ce6-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="25ce6-452">HTTPS</span></span>
* <span data-ttu-id="25ce6-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="25ce6-453">IPV6</span></span>
* <span data-ttu-id="25ce6-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="25ce6-454">QUERY_STRING</span></span>
* <span data-ttu-id="25ce6-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="25ce6-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="25ce6-456">REMOTE_PORT</span></span>
* <span data-ttu-id="25ce6-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="25ce6-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="25ce6-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="25ce6-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="25ce6-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="25ce6-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="25ce6-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="25ce6-460">REQUEST_URI</span></span>
* <span data-ttu-id="25ce6-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="25ce6-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="25ce6-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-462">SERVER_ADDR</span></span>
* <span data-ttu-id="25ce6-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="25ce6-463">SERVER_PORT</span></span>
* <span data-ttu-id="25ce6-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="25ce6-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="25ce6-465">TIME</span><span class="sxs-lookup"><span data-stu-id="25ce6-465">TIME</span></span>
* <span data-ttu-id="25ce6-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="25ce6-466">TIME_DAY</span></span>
* <span data-ttu-id="25ce6-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="25ce6-467">TIME_HOUR</span></span>
* <span data-ttu-id="25ce6-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="25ce6-468">TIME_MIN</span></span>
* <span data-ttu-id="25ce6-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="25ce6-469">TIME_MON</span></span>
* <span data-ttu-id="25ce6-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="25ce6-470">TIME_SEC</span></span>
* <span data-ttu-id="25ce6-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="25ce6-471">TIME_WDAY</span></span>
* <span data-ttu-id="25ce6-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="25ce6-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="25ce6-473">Правила модуля переопределения URL-адресов для IIS</span><span class="sxs-lookup"><span data-stu-id="25ce6-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="25ce6-474">Чтобы использовать тот же набор правил, который применяется к модулю переопределения URL-адресов для IIS, используйте <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="25ce6-475">Убедитесь, что файл правил развертывается вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="25ce6-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="25ce6-476">Не указывайте ПО промежуточного слоя использовать файл приложения *web.config* при работе в службах IIS Windows Server.</span><span class="sxs-lookup"><span data-stu-id="25ce6-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="25ce6-477">В IIS эти правила нужно хранить за пределами файла приложения *web.config*, чтобы предотвратить конфликты с модулем переопределения для IIS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="25ce6-478">Дополнительные сведения и примеры правил модуля переопределения URL-адресов для IIS см. в разделах [Использование модуля переопределения URL-адресов 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) и [Справочник по конфигурации модуля переопределения URL-адресов](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="25ce6-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="25ce6-479"><xref:System.IO.StreamReader> используется для чтения правил из файла *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="25ce6-480">Пример приложения переопределяет запросы с `/iis-rules-rewrite/(.*)` на `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="25ce6-481">Клиенту отправляется отклик с кодом состояния *200 (ОК)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="25ce6-482">Исходный запрос: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="25ce6-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запрос и отклик](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="25ce6-484">Если имеется активный модуль переопределения для IIS, где настроены правила брандмауэра уровня сервера, способные негативно повлиять на ваше приложение, можно отключить этот модуль для приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="25ce6-485">Дополнительные сведения см. в разделе [Отключение модулей IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="25ce6-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="25ce6-486">Неподдерживаемые функции</span><span class="sxs-lookup"><span data-stu-id="25ce6-486">Unsupported features</span></span>

<span data-ttu-id="25ce6-487">ПО промежуточного слоя, выпущенное вместе с ASP.NET Core 2.x, не поддерживает следующие функции в модуле переопределения URL-адресов для IIS:</span><span class="sxs-lookup"><span data-stu-id="25ce6-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="25ce6-488">Правила для исходящих подключений</span><span class="sxs-lookup"><span data-stu-id="25ce6-488">Outbound Rules</span></span>
* <span data-ttu-id="25ce6-489">Пользовательские переменные сервера</span><span class="sxs-lookup"><span data-stu-id="25ce6-489">Custom Server Variables</span></span>
* <span data-ttu-id="25ce6-490">Знаки подстановки</span><span class="sxs-lookup"><span data-stu-id="25ce6-490">Wildcards</span></span>
* <span data-ttu-id="25ce6-491">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="25ce6-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="25ce6-492">Поддерживаемые переменные сервера</span><span class="sxs-lookup"><span data-stu-id="25ce6-492">Supported server variables</span></span>

<span data-ttu-id="25ce6-493">ПО промежуточного слоя поддерживает следующие переменные сервера в модуле переопределения URL-адресов для IIS:</span><span class="sxs-lookup"><span data-stu-id="25ce6-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="25ce6-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="25ce6-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="25ce6-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="25ce6-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="25ce6-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="25ce6-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="25ce6-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="25ce6-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="25ce6-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="25ce6-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="25ce6-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="25ce6-499">HTTP_HOST</span></span>
* <span data-ttu-id="25ce6-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="25ce6-500">HTTP_REFERER</span></span>
* <span data-ttu-id="25ce6-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="25ce6-501">HTTP_URL</span></span>
* <span data-ttu-id="25ce6-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="25ce6-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="25ce6-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="25ce6-503">HTTPS</span></span>
* <span data-ttu-id="25ce6-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="25ce6-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="25ce6-505">QUERY_STRING</span></span>
* <span data-ttu-id="25ce6-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="25ce6-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="25ce6-507">REMOTE_PORT</span></span>
* <span data-ttu-id="25ce6-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="25ce6-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="25ce6-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="25ce6-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="25ce6-510">Можно также получить <xref:Microsoft.Extensions.FileProviders.IFileProvider> через <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="25ce6-511">Такой подход позволяет более гибко задавать расположение для файлов с правилами переопределения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="25ce6-512">Убедитесь, что эти файлы развертываются на сервере по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="25ce6-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="25ce6-513">Правило, основанное на методе</span><span class="sxs-lookup"><span data-stu-id="25ce6-513">Method-based rule</span></span>

<span data-ttu-id="25ce6-514">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, чтобы реализовать собственную логику правил в методе.</span><span class="sxs-lookup"><span data-stu-id="25ce6-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="25ce6-515">`Add` предоставляет <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, который предоставляет <xref:Microsoft.AspNetCore.Http.HttpContext> для использования в методе.</span><span class="sxs-lookup"><span data-stu-id="25ce6-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="25ce6-516">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) определяет, как осуществляется дополнительная обработка в конвейере.</span><span class="sxs-lookup"><span data-stu-id="25ce6-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="25ce6-517">Установите значение одного из полей <xref:Microsoft.AspNetCore.Rewrite.RuleResult> из следующей таблицы.</span><span class="sxs-lookup"><span data-stu-id="25ce6-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="25ce6-518">Действие</span><span class="sxs-lookup"><span data-stu-id="25ce6-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="25ce6-519">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-520">'Blazor'</span></span>
- <span data-ttu-id="25ce6-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-521">'Identity'</span></span>
- <span data-ttu-id="25ce6-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-523">'Razor'</span></span>
- <span data-ttu-id="25ce6-524">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-525">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-526">'Blazor'</span></span>
- <span data-ttu-id="25ce6-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-527">'Identity'</span></span>
- <span data-ttu-id="25ce6-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-529">'Razor'</span></span>
- <span data-ttu-id="25ce6-530">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-531">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-532">'Blazor'</span></span>
- <span data-ttu-id="25ce6-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-533">'Identity'</span></span>
- <span data-ttu-id="25ce6-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-535">'Razor'</span></span>
- <span data-ttu-id="25ce6-536">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-537">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-538">'Blazor'</span></span>
- <span data-ttu-id="25ce6-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-539">'Identity'</span></span>
- <span data-ttu-id="25ce6-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-541">'Razor'</span></span>
- <span data-ttu-id="25ce6-542">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-543">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-544">'Blazor'</span></span>
- <span data-ttu-id="25ce6-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-545">'Identity'</span></span>
- <span data-ttu-id="25ce6-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-547">'Razor'</span></span>
- <span data-ttu-id="25ce6-548">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-549">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-550">'Blazor'</span></span>
- <span data-ttu-id="25ce6-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-551">'Identity'</span></span>
- <span data-ttu-id="25ce6-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-553">'Razor'</span></span>
- <span data-ttu-id="25ce6-554">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-555">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-556">'Blazor'</span></span>
- <span data-ttu-id="25ce6-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-557">'Identity'</span></span>
- <span data-ttu-id="25ce6-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-559">'Razor'</span></span>
- <span data-ttu-id="25ce6-560">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-561">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-562">'Blazor'</span></span>
- <span data-ttu-id="25ce6-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-563">'Identity'</span></span>
- <span data-ttu-id="25ce6-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-565">'Razor'</span></span>
- <span data-ttu-id="25ce6-566">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-567">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-568">'Blazor'</span></span>
- <span data-ttu-id="25ce6-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-569">'Identity'</span></span>
- <span data-ttu-id="25ce6-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-571">'Razor'</span></span>
- <span data-ttu-id="25ce6-572">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-573">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-574">'Blazor'</span></span>
- <span data-ttu-id="25ce6-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-575">'Identity'</span></span>
- <span data-ttu-id="25ce6-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-577">'Razor'</span></span>
- <span data-ttu-id="25ce6-578">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-579">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-580">'Blazor'</span></span>
- <span data-ttu-id="25ce6-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-581">'Identity'</span></span>
- <span data-ttu-id="25ce6-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-583">'Razor'</span></span>
- <span data-ttu-id="25ce6-584">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-585">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-586">'Blazor'</span></span>
- <span data-ttu-id="25ce6-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-587">'Identity'</span></span>
- <span data-ttu-id="25ce6-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-589">'Razor'</span></span>
- <span data-ttu-id="25ce6-590">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-591">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-592">'Blazor'</span></span>
- <span data-ttu-id="25ce6-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-593">'Identity'</span></span>
- <span data-ttu-id="25ce6-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-595">'Razor'</span></span>
- <span data-ttu-id="25ce6-596">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-597">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-598">'Blazor'</span></span>
- <span data-ttu-id="25ce6-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-599">'Identity'</span></span>
- <span data-ttu-id="25ce6-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-601">'Razor'</span></span>
- <span data-ttu-id="25ce6-602">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-603">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-604">'Blazor'</span></span>
- <span data-ttu-id="25ce6-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-605">'Identity'</span></span>
- <span data-ttu-id="25ce6-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-607">'Razor'</span></span>
- <span data-ttu-id="25ce6-608">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-609">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-610">'Blazor'</span></span>
- <span data-ttu-id="25ce6-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-611">'Identity'</span></span>
- <span data-ttu-id="25ce6-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-613">'Razor'</span></span>
- <span data-ttu-id="25ce6-614">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-614">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-616">'Blazor'</span></span>
- <span data-ttu-id="25ce6-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-617">'Identity'</span></span>
- <span data-ttu-id="25ce6-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-619">'Razor'</span></span>
- <span data-ttu-id="25ce6-620">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-621">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-622">'Blazor'</span></span>
- <span data-ttu-id="25ce6-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-623">'Identity'</span></span>
- <span data-ttu-id="25ce6-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-625">'Razor'</span></span>
- <span data-ttu-id="25ce6-626">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-627">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-628">'Blazor'</span></span>
- <span data-ttu-id="25ce6-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-629">'Identity'</span></span>
- <span data-ttu-id="25ce6-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-631">'Razor'</span></span>
- <span data-ttu-id="25ce6-632">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-633">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-634">'Blazor'</span></span>
- <span data-ttu-id="25ce6-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-635">'Identity'</span></span>
- <span data-ttu-id="25ce6-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-637">'Razor'</span></span>
- <span data-ttu-id="25ce6-638">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-639">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-640">'Blazor'</span></span>
- <span data-ttu-id="25ce6-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-641">'Identity'</span></span>
- <span data-ttu-id="25ce6-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-643">'Razor'</span></span>
- <span data-ttu-id="25ce6-644">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-645">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-646">'Blazor'</span></span>
- <span data-ttu-id="25ce6-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-647">'Identity'</span></span>
- <span data-ttu-id="25ce6-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-649">'Razor'</span></span>
- <span data-ttu-id="25ce6-650">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-651">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-652">'Blazor'</span></span>
- <span data-ttu-id="25ce6-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-653">'Identity'</span></span>
- <span data-ttu-id="25ce6-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-655">'Razor'</span></span>
- <span data-ttu-id="25ce6-656">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-657">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-658">'Blazor'</span></span>
- <span data-ttu-id="25ce6-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-659">'Identity'</span></span>
- <span data-ttu-id="25ce6-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-661">'Razor'</span></span>
- <span data-ttu-id="25ce6-662">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-663">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-664">'Blazor'</span></span>
- <span data-ttu-id="25ce6-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-665">'Identity'</span></span>
- <span data-ttu-id="25ce6-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-667">'Razor'</span></span>
- <span data-ttu-id="25ce6-668">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-669">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-670">'Blazor'</span></span>
- <span data-ttu-id="25ce6-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-671">'Identity'</span></span>
- <span data-ttu-id="25ce6-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-673">'Razor'</span></span>
- <span data-ttu-id="25ce6-674">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-675">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-676">'Blazor'</span></span>
- <span data-ttu-id="25ce6-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-677">'Identity'</span></span>
- <span data-ttu-id="25ce6-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-679">'Razor'</span></span>
- <span data-ttu-id="25ce6-680">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-681">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-682">'Blazor'</span></span>
- <span data-ttu-id="25ce6-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-683">'Identity'</span></span>
- <span data-ttu-id="25ce6-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-685">'Razor'</span></span>
- <span data-ttu-id="25ce6-686">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-687">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-688">'Blazor'</span></span>
- <span data-ttu-id="25ce6-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-689">'Identity'</span></span>
- <span data-ttu-id="25ce6-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-691">'Razor'</span></span>
- <span data-ttu-id="25ce6-692">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-693">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-694">'Blazor'</span></span>
- <span data-ttu-id="25ce6-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-695">'Identity'</span></span>
- <span data-ttu-id="25ce6-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-697">'Razor'</span></span>
- <span data-ttu-id="25ce6-698">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-699">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-700">'Blazor'</span></span>
- <span data-ttu-id="25ce6-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-701">'Identity'</span></span>
- <span data-ttu-id="25ce6-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-703">'Razor'</span></span>
- <span data-ttu-id="25ce6-704">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-705">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-706">'Blazor'</span></span>
- <span data-ttu-id="25ce6-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-707">'Identity'</span></span>
- <span data-ttu-id="25ce6-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-709">'Razor'</span></span>
- <span data-ttu-id="25ce6-710">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-711">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-712">'Blazor'</span></span>
- <span data-ttu-id="25ce6-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-713">'Identity'</span></span>
- <span data-ttu-id="25ce6-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-715">'Razor'</span></span>
- <span data-ttu-id="25ce6-716">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-717">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-718">'Blazor'</span></span>
- <span data-ttu-id="25ce6-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-719">'Identity'</span></span>
- <span data-ttu-id="25ce6-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-721">'Razor'</span></span>
- <span data-ttu-id="25ce6-722">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-723">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-724">'Blazor'</span></span>
- <span data-ttu-id="25ce6-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-725">'Identity'</span></span>
- <span data-ttu-id="25ce6-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-727">'Razor'</span></span>
- <span data-ttu-id="25ce6-728">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-729">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-730">'Blazor'</span></span>
- <span data-ttu-id="25ce6-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-731">'Identity'</span></span>
- <span data-ttu-id="25ce6-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-733">'Razor'</span></span>
- <span data-ttu-id="25ce6-734">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-735">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-736">'Blazor'</span></span>
- <span data-ttu-id="25ce6-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-737">'Identity'</span></span>
- <span data-ttu-id="25ce6-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-739">'Razor'</span></span>
- <span data-ttu-id="25ce6-740">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-741">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-742">'Blazor'</span></span>
- <span data-ttu-id="25ce6-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-743">'Identity'</span></span>
- <span data-ttu-id="25ce6-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-745">'Razor'</span></span>
- <span data-ttu-id="25ce6-746">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-747">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-748">'Blazor'</span></span>
- <span data-ttu-id="25ce6-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-749">'Identity'</span></span>
- <span data-ttu-id="25ce6-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-751">'Razor'</span></span>
- <span data-ttu-id="25ce6-752">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-753">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-754">'Blazor'</span></span>
- <span data-ttu-id="25ce6-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-755">'Identity'</span></span>
- <span data-ttu-id="25ce6-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-757">'Razor'</span></span>
- <span data-ttu-id="25ce6-758">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-759">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-760">'Blazor'</span></span>
- <span data-ttu-id="25ce6-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-761">'Identity'</span></span>
- <span data-ttu-id="25ce6-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-763">'Razor'</span></span>
- <span data-ttu-id="25ce6-764">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-765">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-766">'Blazor'</span></span>
- <span data-ttu-id="25ce6-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-767">'Identity'</span></span>
- <span data-ttu-id="25ce6-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-769">'Razor'</span></span>
- <span data-ttu-id="25ce6-770">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-771">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-772">'Blazor'</span></span>
- <span data-ttu-id="25ce6-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-773">'Identity'</span></span>
- <span data-ttu-id="25ce6-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-775">'Razor'</span></span>
- <span data-ttu-id="25ce6-776">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-777">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-778">'Blazor'</span></span>
- <span data-ttu-id="25ce6-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-779">'Identity'</span></span>
- <span data-ttu-id="25ce6-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-781">'Razor'</span></span>
- <span data-ttu-id="25ce6-782">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-783">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-784">'Blazor'</span></span>
- <span data-ttu-id="25ce6-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-785">'Identity'</span></span>
- <span data-ttu-id="25ce6-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-787">'Razor'</span></span>
- <span data-ttu-id="25ce6-788">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-789">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-790">'Blazor'</span></span>
- <span data-ttu-id="25ce6-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-791">'Identity'</span></span>
- <span data-ttu-id="25ce6-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-793">'Razor'</span></span>
- <span data-ttu-id="25ce6-794">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-794">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-795">-------------------------------- | | `RuleResult.ContinueRules` (по умолчанию) | Продолжение применения правил.</span><span class="sxs-lookup"><span data-stu-id="25ce6-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="25ce6-796">| | `RuleResult.EndResponse`             | Остановка применения правил и отправка ответа.</span><span class="sxs-lookup"><span data-stu-id="25ce6-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="25ce6-797">| | `RuleResult.SkipRemainingRules`      | Остановка применения правил и отправка контекста в следующий компонент ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="25ce6-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="25ce6-798">Пример приложения демонстрирует метод, который перенаправляет запросы для путей, заканчивающихся на *.xml*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="25ce6-799">Если запрос выполняется для `/file.xml`, запрос перенаправляется к `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="25ce6-800">Для кода состояния задается значение *301 (перемещен окончательно)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="25ce6-801">Когда браузер отправляет новый запрос на */xmlfiles/file.xml*, ПО промежуточного слоя статических файлов предоставляет файл клиенту из папки *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="25ce6-802">Для перенаправления необходимо явно указать код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="25ce6-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="25ce6-803">В противном случае возвращается код состояния *200 (ОК)* и перенаправление на стороне клиента не происходит.</span><span class="sxs-lookup"><span data-stu-id="25ce6-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="25ce6-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="25ce6-805">Этот подход также переопределяет запросы.</span><span class="sxs-lookup"><span data-stu-id="25ce6-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="25ce6-806">В примере приложения показано переопределение пути для любого запроса текстового файла, чтобы отправить текстовый файл *file.txt* из папки *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="25ce6-807">ПО промежуточного слоя статических файлов предоставляет файл по обновленному пути запроса:</span><span class="sxs-lookup"><span data-stu-id="25ce6-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="25ce6-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="25ce6-809">Правило, основанное на IRule</span><span class="sxs-lookup"><span data-stu-id="25ce6-809">IRule-based rule</span></span>

<span data-ttu-id="25ce6-810">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, чтобы использовать собственную логику правил в классе, реализующем интерфейс <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="25ce6-811">`IRule` позволяет более гибко применять правила, основанные на методах.</span><span class="sxs-lookup"><span data-stu-id="25ce6-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="25ce6-812">Класс реализации может включать конструктор, который позволяет передавать параметры для метода <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="25ce6-813">Значения параметров в примере приложения для `extension` и `newPath` проверяются на соответствие нескольким условиям.</span><span class="sxs-lookup"><span data-stu-id="25ce6-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="25ce6-814">`extension` должен содержать одно из значений: *.png*, *.jpg* или *.gif*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="25ce6-815">Если `newPath` не является допустимым, возникает исключение <xref:System.ArgumentException>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="25ce6-816">Если запрос выполняется для *image.png*, запрос перенаправляется к `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="25ce6-817">Если запрос выполняется для *image.jpg*, запрос перенаправляется к `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="25ce6-818">Для кода состояния устанавливается значение *301 (перемещен окончательно)* , а также задается `context.Result`, чтобы остановить обработку правил и отправить отклик.</span><span class="sxs-lookup"><span data-stu-id="25ce6-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="25ce6-819">Исходный запрос: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="25ce6-819">Original Request: `/image.png`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики для image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="25ce6-821">Исходный запрос: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="25ce6-821">Original Request: `/image.jpg`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики для image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="25ce6-823">Примеры регулярных выражений</span><span class="sxs-lookup"><span data-stu-id="25ce6-823">Regex examples</span></span>

| <span data-ttu-id="25ce6-824">Goal</span><span class="sxs-lookup"><span data-stu-id="25ce6-824">Goal</span></span> | <span data-ttu-id="25ce6-825">Пример строки регулярного выражения</span><span class="sxs-lookup"><span data-stu-id="25ce6-825">Regex String &</span></span><br><span data-ttu-id="25ce6-826">и совпадения</span><span class="sxs-lookup"><span data-stu-id="25ce6-826">Match Example</span></span> | <span data-ttu-id="25ce6-827">Пример строки замены и</span><span class="sxs-lookup"><span data-stu-id="25ce6-827">Replacement String &</span></span><br><span data-ttu-id="25ce6-828">выходных данных</span><span class="sxs-lookup"><span data-stu-id="25ce6-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="25ce6-829">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-830">'Blazor'</span></span>
- <span data-ttu-id="25ce6-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-831">'Identity'</span></span>
- <span data-ttu-id="25ce6-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-833">'Razor'</span></span>
- <span data-ttu-id="25ce6-834">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-835">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-836">'Blazor'</span></span>
- <span data-ttu-id="25ce6-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-837">'Identity'</span></span>
- <span data-ttu-id="25ce6-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-839">'Razor'</span></span>
- <span data-ttu-id="25ce6-840">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-841">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-842">'Blazor'</span></span>
- <span data-ttu-id="25ce6-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-843">'Identity'</span></span>
- <span data-ttu-id="25ce6-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-845">'Razor'</span></span>
- <span data-ttu-id="25ce6-846">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-847">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-848">'Blazor'</span></span>
- <span data-ttu-id="25ce6-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-849">'Identity'</span></span>
- <span data-ttu-id="25ce6-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-851">'Razor'</span></span>
- <span data-ttu-id="25ce6-852">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-853">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-854">'Blazor'</span></span>
- <span data-ttu-id="25ce6-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-855">'Identity'</span></span>
- <span data-ttu-id="25ce6-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-857">'Razor'</span></span>
- <span data-ttu-id="25ce6-858">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-859">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-860">'Blazor'</span></span>
- <span data-ttu-id="25ce6-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-861">'Identity'</span></span>
- <span data-ttu-id="25ce6-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-863">'Razor'</span></span>
- <span data-ttu-id="25ce6-864">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-865">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-866">'Blazor'</span></span>
- <span data-ttu-id="25ce6-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-867">'Identity'</span></span>
- <span data-ttu-id="25ce6-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-869">'Razor'</span></span>
- <span data-ttu-id="25ce6-870">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-871">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-872">'Blazor'</span></span>
- <span data-ttu-id="25ce6-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-873">'Identity'</span></span>
- <span data-ttu-id="25ce6-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-875">'Razor'</span></span>
- <span data-ttu-id="25ce6-876">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-877">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-878">'Blazor'</span></span>
- <span data-ttu-id="25ce6-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-879">'Identity'</span></span>
- <span data-ttu-id="25ce6-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-881">'Razor'</span></span>
- <span data-ttu-id="25ce6-882">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-883">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-884">'Blazor'</span></span>
- <span data-ttu-id="25ce6-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-885">'Identity'</span></span>
- <span data-ttu-id="25ce6-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-887">'Razor'</span></span>
- <span data-ttu-id="25ce6-888">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-889">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-890">'Blazor'</span></span>
- <span data-ttu-id="25ce6-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-891">'Identity'</span></span>
- <span data-ttu-id="25ce6-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-893">'Razor'</span></span>
- <span data-ttu-id="25ce6-894">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-895">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-896">'Blazor'</span></span>
- <span data-ttu-id="25ce6-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-897">'Identity'</span></span>
- <span data-ttu-id="25ce6-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-899">'Razor'</span></span>
- <span data-ttu-id="25ce6-900">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-901">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-902">'Blazor'</span></span>
- <span data-ttu-id="25ce6-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-903">'Identity'</span></span>
- <span data-ttu-id="25ce6-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-905">'Razor'</span></span>
- <span data-ttu-id="25ce6-906">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-906">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-908">'Blazor'</span></span>
- <span data-ttu-id="25ce6-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-909">'Identity'</span></span>
- <span data-ttu-id="25ce6-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-911">'Razor'</span></span>
- <span data-ttu-id="25ce6-912">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-913">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-914">'Blazor'</span></span>
- <span data-ttu-id="25ce6-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-915">'Identity'</span></span>
- <span data-ttu-id="25ce6-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-917">'Razor'</span></span>
- <span data-ttu-id="25ce6-918">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-919">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-920">'Blazor'</span></span>
- <span data-ttu-id="25ce6-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-921">'Identity'</span></span>
- <span data-ttu-id="25ce6-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-923">'Razor'</span></span>
- <span data-ttu-id="25ce6-924">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-925">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-926">'Blazor'</span></span>
- <span data-ttu-id="25ce6-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-927">'Identity'</span></span>
- <span data-ttu-id="25ce6-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-929">'Razor'</span></span>
- <span data-ttu-id="25ce6-930">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-931">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-932">'Blazor'</span></span>
- <span data-ttu-id="25ce6-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-933">'Identity'</span></span>
- <span data-ttu-id="25ce6-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-935">'Razor'</span></span>
- <span data-ttu-id="25ce6-936">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-937">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-938">'Blazor'</span></span>
- <span data-ttu-id="25ce6-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-939">'Identity'</span></span>
- <span data-ttu-id="25ce6-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-941">'Razor'</span></span>
- <span data-ttu-id="25ce6-942">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-943">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-944">'Blazor'</span></span>
- <span data-ttu-id="25ce6-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-945">'Identity'</span></span>
- <span data-ttu-id="25ce6-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-947">'Razor'</span></span>
- <span data-ttu-id="25ce6-948">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-949">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-950">'Blazor'</span></span>
- <span data-ttu-id="25ce6-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-951">'Identity'</span></span>
- <span data-ttu-id="25ce6-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-953">'Razor'</span></span>
- <span data-ttu-id="25ce6-954">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-955">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-956">'Blazor'</span></span>
- <span data-ttu-id="25ce6-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-957">'Identity'</span></span>
- <span data-ttu-id="25ce6-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-959">'Razor'</span></span>
- <span data-ttu-id="25ce6-960">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-961">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-962">'Blazor'</span></span>
- <span data-ttu-id="25ce6-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-963">'Identity'</span></span>
- <span data-ttu-id="25ce6-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-965">'Razor'</span></span>
- <span data-ttu-id="25ce6-966">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-967">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-968">'Blazor'</span></span>
- <span data-ttu-id="25ce6-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-969">'Identity'</span></span>
- <span data-ttu-id="25ce6-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-971">'Razor'</span></span>
- <span data-ttu-id="25ce6-972">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-973">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-974">'Blazor'</span></span>
- <span data-ttu-id="25ce6-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-975">'Identity'</span></span>
- <span data-ttu-id="25ce6-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-977">'Razor'</span></span>
- <span data-ttu-id="25ce6-978">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-979">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-980">'Blazor'</span></span>
- <span data-ttu-id="25ce6-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-981">'Identity'</span></span>
- <span data-ttu-id="25ce6-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-983">'Razor'</span></span>
- <span data-ttu-id="25ce6-984">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-985">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-986">'Blazor'</span></span>
- <span data-ttu-id="25ce6-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-987">'Identity'</span></span>
- <span data-ttu-id="25ce6-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-989">'Razor'</span></span>
- <span data-ttu-id="25ce6-990">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-991">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-992">'Blazor'</span></span>
- <span data-ttu-id="25ce6-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-993">'Identity'</span></span>
- <span data-ttu-id="25ce6-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-995">'Razor'</span></span>
- <span data-ttu-id="25ce6-996">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-997">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-998">'Blazor'</span></span>
- <span data-ttu-id="25ce6-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-999">'Identity'</span></span>
- <span data-ttu-id="25ce6-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1001">'Razor'</span></span>
- <span data-ttu-id="25ce6-1002">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1003">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1004">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1005">'Identity'</span></span>
- <span data-ttu-id="25ce6-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1007">'Razor'</span></span>
- <span data-ttu-id="25ce6-1008">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1009">------------------- | | Переопределение пути в строке запроса | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="25ce6-1010">`/path?var1=abc&var2=123` | | Удаление косой черты в конце | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="25ce6-1011">`/path` | | Добавление косой черты в конце | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="25ce6-1012">`/path/` | | Запрет переопределения отдельных запросов | `^(.*)(?<!\.axd)$` или `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="25ce6-1013">Да: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="25ce6-1014">Нет: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="25ce6-1015">`/resource.axd` | | Переупорядочение сегментов URL-адреса | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="25ce6-1016">`path/3/2/1` | | Замена сегмента URL-адреса | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="25ce6-1017">Этот документ содержит вводные сведения о переопределении URL-адресов и инструкции по использованию ПО промежуточного слоя для переопределения URL-адресов в приложениях ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="25ce6-1018">Переопределение URL-адресов представляет собой изменение URL-адресов запросов на основе одного или нескольких предопределенных правил.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="25ce6-1019">Переопределение URL-адресов создает абстракцию между расположениями ресурсов и их адресами, позволяя убрать тесную связь между ними.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="25ce6-1020">Существует несколько сценариев, где может пригодиться переопределение URL-адресов:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="25ce6-1021">Временное или постоянное перемещение или замещение ресурсов сервера с сохранением стабильных указателей для этих ресурсов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="25ce6-1022">Разделение обработки запросов между разными приложениями или разными областями одного приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="25ce6-1023">Удаление, добавление или переупорядочение сегментов URL-адресов для входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="25ce6-1024">Оптимизация общедоступных URL-адресов в целях оптимизации для поисковых систем (SEO).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="25ce6-1025">Разрешение использования понятных общедоступных URL-адресов, чтобы помочь посетителям прогнозировать содержимое, возвращаемое в результате запроса ресурса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="25ce6-1026">Перенаправление небезопасных запросов на защищенные конечные точки.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="25ce6-1027">Запрет вставки прямых ссылок, когда внешний сайт использует статический ресурс, размещенный на другом сайте, привязывая ресурс к своему содержимому.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="25ce6-1028">Переопределение URL-адресов может снижать производительность приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="25ce6-1029">Следует по возможности ограничить число и сложность правил.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="25ce6-1030">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="25ce6-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="25ce6-1031">Перенаправление и переопределение URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="25ce6-1032">Разница между *перенаправлением* и *переопределением* URL-адресов может показаться незначительной, но она оказывает существенное влияние на предоставление ресурсов клиентам.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="25ce6-1033">ПО промежуточного слоя для переопределения URL-адресов в ASP.NET Core удовлетворяет потребность в обеих этих функциях.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="25ce6-1034">*Перенаправление URL-адресов* является операцией на стороне клиента, которая предписывает клиенту обратиться к ресурсу по другому адресу, отличному от запрошенного клиентом.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="25ce6-1035">Для этого используется круговое обращение к серверу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="25ce6-1036">URL-адрес перенаправления, возвращаемый клиенту, отображается в адресной строке браузера, когда клиент отправляет новый запрос для данного ресурса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="25ce6-1037">Если `/resource`*перенаправляется* на `/different-resource`, сервер отвечает, что клиенту следует получить ресурс в `/different-resource` с кодом состояния, указывающим, что такое перенаправление является временным или постоянным.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Конечная точка службы WebAPI временно изменена с версии 1 (v1) на версию 2 (v2) на сервере.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="25ce6-1043">При перенаправлении запросов на другой URL-адрес можно указать, является ли оно постоянным или временным, предоставив код состояния в ответе:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="25ce6-1044">Код состояния *301 (перемещен окончательно)* используется, когда ресурс имеет новый постоянный URL-адрес и вы хотите сообщить клиенту, что все последующие запросы для этого ресурса должны использовать новый URL-адрес.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="25ce6-1045">*Клиент может кэшировать и повторно использовать отклик при получении кода состояния 301.*</span><span class="sxs-lookup"><span data-stu-id="25ce6-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="25ce6-1046">Код состояния *302 (найдено)* используется, когда перенаправление является временным или может меняться.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="25ce6-1047">Код состояния 302 указывает клиенту, что не нужно сохранять URL-адрес и использовать его в будущем.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="25ce6-1048">Дополнительные сведения о кодах состояния см. в документе [RFC 2616: определения кодов состояния](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="25ce6-1049">*Переопределение URL-адреса* является операцией на стороне сервера для предоставления ресурса с другого адреса ресурса, отличного от запрошенного клиентом.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="25ce6-1050">Переопределение URL-адреса не требует кругового обращения к серверу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="25ce6-1051">Переопределенный URL-адрес не возвращается клиенту и не отображается в адресной строке браузера.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="25ce6-1052">Когда `/resource`*переопределяется* на `/different-resource`, сервер получает и запрашивает ресурс *внутри* в `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="25ce6-1053">Хотя клиент может быть в состоянии получить ресурс по переопределенному URL-адресу, когда клиент отправляет запрос и получает отклик, он не уведомляется о наличии ресурса по переопределенному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Конечная точка службы WebAPI была изменена с версии 1 (v1) на версию 2 (v2) на сервере.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="25ce6-1058">Пример приложения с переопределением URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1058">URL rewriting sample app</span></span>

<span data-ttu-id="25ce6-1059">Вы можете ознакомиться с функциями ПО промежуточного слоя переопределения URL-адресов на [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="25ce6-1060">Это приложение применяет правила перенаправления и переопределения и показывает перенаправленный или переопределенный URL-адрес для нескольких сценариев.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="25ce6-1061">Условия для использования ПО промежуточного слоя для переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="25ce6-1062">Используйте ПО промежуточного слоя для переопределения URL-адресов, если невозможно использовать следующие подходы:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="25ce6-1063">Модуль переопределения URL-адресов со службами IIS на Windows Server</span><span class="sxs-lookup"><span data-stu-id="25ce6-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="25ce6-1064">Модуль Apache mod_rewrite на сервере Apache Server</span><span class="sxs-lookup"><span data-stu-id="25ce6-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="25ce6-1065">Переопределение URL-адресов в Nginx</span><span class="sxs-lookup"><span data-stu-id="25ce6-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="25ce6-1066">Кроме того, используйте ПО промежуточного слоя, когда приложение размещается на [сервере HTTP.sys](xref:fundamentals/servers/httpsys) (который раньше назывался WebListener).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="25ce6-1067">Основные причины для использования переопределения URL-адресов на основе сервера в IIS, Apache и Nginx:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="25ce6-1068">ПО промежуточного слоя не поддерживает все функции этих модулей.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="25ce6-1069">Некоторые функции серверных модулей не работают с проектами ASP.NET Core, например ограничения `IsFile` и `IsDirectory` для модуля переопределения IIS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="25ce6-1070">В этих случаях следует использовать ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="25ce6-1071">Производительность ПО промежуточного слоя, скорее всего, не соответствует производительности модулей.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="25ce6-1072">Тестирование производительности — это единственный способ узнать точно, какой подход больше всего снижает производительность или является ли такое снижение незначительным.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="25ce6-1073">Пакет</span><span class="sxs-lookup"><span data-stu-id="25ce6-1073">Package</span></span>

<span data-ttu-id="25ce6-1074">Чтобы включить ПО промежуточного слоя в проект, добавьте ссылки на пакет в [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) в файле проекта, который содержит пакет [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="25ce6-1075">Если метапакет `Microsoft.AspNetCore.App` не используется, добавьте ссылку на проект в пакет `Microsoft.AspNetCore.Rewrite`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="25ce6-1076">Расширение и параметры</span><span class="sxs-lookup"><span data-stu-id="25ce6-1076">Extension and options</span></span>

<span data-ttu-id="25ce6-1077">Задайте правила переопределения и перенаправления URL-адресов, создав экземпляр класса [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) для каждого правила с помощью методов расширения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="25ce6-1078">Несколько правил можно объединить в цепочку в том порядке, в котором они должны обрабатываться.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="25ce6-1079">`RewriteOptions` передаются в ПО промежуточного слоя для переопределения URL-адресов по мере его добавления в конвейер запросов с помощью <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="25ce6-1080">Перенаправление не www на www</span><span class="sxs-lookup"><span data-stu-id="25ce6-1080">Redirect non-www to www</span></span>

<span data-ttu-id="25ce6-1081">Три параметра разрешают приложению перенаправлять запросы, отличные от `www`, на `www`:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="25ce6-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>. Окончательно перенаправляет запрос в поддомен `www`, если запрос не является `www`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="25ce6-1083">Перенаправляет с кодом состояния [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="25ce6-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>. Перенаправляет запрос в поддомен `www`, если входящий запрос не является `www`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="25ce6-1085">Перенаправляет с кодом состояния [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="25ce6-1086">Перегрузка позволяет предоставить код состояния для ответа.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="25ce6-1087">Используйте поле класса <xref:Microsoft.AspNetCore.Http.StatusCodes> для назначения кода состояния.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="25ce6-1088">Перенаправление URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1088">URL redirect</span></span>

<span data-ttu-id="25ce6-1089">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> для перенаправления запросов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="25ce6-1090">Первый параметр содержит регулярное выражение, соответствующее пути входящего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="25ce6-1091">Второй параметр является строкой замены.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="25ce6-1092">Третий параметр (при его наличии) указывает код состояния.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="25ce6-1093">Если код состояния не задан, по умолчанию используется код *302 (найдено)* , указывающий, что ресурс временно перемещен или заменен.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="25ce6-1094">В браузере с включенными средствами разработчика выполните запрос для примера приложения по пути `/redirect-rule/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="25ce6-1095">Регулярное выражение соответствует пути запроса в `redirect-rule/(.*)`, и этот путь заменяется на `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="25ce6-1096">URL-адрес перенаправления отправляется обратно клиенту с кодом состояния *302 (найдено)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="25ce6-1097">Браузер выполняет новый запрос на URL-адрес перенаправления, отображаемый в адресной строке.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="25ce6-1098">Поскольку в примере приложения нет правил, соответствующих URL-адресу перенаправления:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="25ce6-1099">Второй запрос получает от приложения ответ *200 (ОК)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="25ce6-1100">Текст ответа показывает URL-адрес перенаправления.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="25ce6-1101">При *перенаправлении* URL-адреса используется круговое обращение к серверу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="25ce6-1102">Соблюдайте осторожность при задании правил перенаправления.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="25ce6-1103">Они оцениваются для каждого запроса, отправляемого в приложение, даже после перенаправления.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="25ce6-1104">Можно легко случайно создать *бесконечный цикл перенаправлений*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="25ce6-1105">Исходный запрос: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="25ce6-1107">Часть выражения, заключенная в скобки, называется *группой записи*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="25ce6-1108">Точка (`.`) в выражении означает *совпадение с любым символом*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="25ce6-1109">Звездочка (`*`) означает *совпадение с предыдущим символом ноль или более раз*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="25ce6-1110">Таким образом, два последних сегмента пути URL-адреса, `1234/5678`, перехватываются группой записи `(.*)`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="25ce6-1111">Любое значение, указанное в URL-адресе запроса после `redirect-rule/`, перехватывается этой группой записи.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="25ce6-1112">В строке замены группы записи внедряются с помощью знака доллара (`$`), за которым следует порядковый номер записи.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="25ce6-1113">Первое значение группы записи получается с помощью `$1`, второе — с помощью `$2`, после чего они продолжают по очереди использоваться для групп записи в регулярном выражении.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="25ce6-1114">В регулярном выражении правила перенаправления в примере приложения присутствует всего одна группа записи, поэтому в строку замены внедряется тоже одна строка — `$1`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="25ce6-1115">Когда применяется правило, URL-адрес становится `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="25ce6-1116">Перенаправление URL-адресов на защищенную конечную точку</span><span class="sxs-lookup"><span data-stu-id="25ce6-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="25ce6-1117">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>, чтобы перенаправлять HTTP-запросы на тот же узел и путь с использованием протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="25ce6-1118">Если код состояния не указан, ПО промежуточного слоя по умолчанию использует значение *302 (найдено)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="25ce6-1119">Если порт не указан:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="25ce6-1120">ПО промежуточного слоя по умолчанию устанавливается на `null`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="25ce6-1121">Схема меняется на `https` (протокол HTTPS), и клиент обращается к ресурсу через порт 443.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="25ce6-1122">Этот пример показывает, как задать код состояния *301 (перемещен окончательно)* и изменить порт на 5001.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="25ce6-1123">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>, чтобы перенаправить небезопасные запросы на тот же узел и путь с использованием безопасного протокола HTTPS через порт 443.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="25ce6-1124">ПО промежуточного слоя задает код состояния *301 (перемещен окончательно)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="25ce6-1125">При перенаправлении на защищенную конечную точку без дополнительных правил перенаправления рекомендуется использовать ПО промежуточного слоя перенаправления на HTTPS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="25ce6-1126">Дополнительные сведения см. в разделе [Обязательное использование HTTPS](xref:security/enforcing-ssl#require-https).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="25ce6-1127">Пример приложения позволяет продемонстрировать использование `AddRedirectToHttps` или `AddRedirectToHttpsPermanent`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="25ce6-1128">Добавьте этот метод расширения в `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="25ce6-1129">Выполните небезопасный запрос к приложению по любому URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="25ce6-1130">Закройте предостережение системы безопасности о том, что самозаверяющий сертификат не является доверенным, или создайте исключение, чтобы сделать сертификат доверенным.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="25ce6-1131">Исходный запрос с использованием `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="25ce6-1133">Исходный запрос с использованием `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="25ce6-1135">Переопределение URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1135">URL rewrite</span></span>

<span data-ttu-id="25ce6-1136">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>, чтобы создать правило для переопределения URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="25ce6-1137">Первый параметр содержит регулярное выражение, соответствующее пути входящего URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="25ce6-1138">Второй параметр является строкой замены.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="25ce6-1139">Третий параметр `skipRemainingRules: {true|false}` сообщает ПО промежуточного слоя, нужно ли пропустить дополнительные правила переопределения, если применяется текущее правило.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="25ce6-1140">Исходный запрос: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запрос и отклик](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="25ce6-1142">Карет (`^`) в начале выражение означает, что сопоставление начинается с начала URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="25ce6-1143">В предыдущем примере с правилом перенаправления, `redirect-rule/(.*)`, в начале регулярного выражения нет символа `^`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="25ce6-1144">Таким образом, для успешного совпадения до `redirect-rule/` в пути могут стоять любые символы.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="25ce6-1145">Path</span><span class="sxs-lookup"><span data-stu-id="25ce6-1145">Path</span></span>                               | <span data-ttu-id="25ce6-1146">Соответствие</span><span class="sxs-lookup"><span data-stu-id="25ce6-1146">Match</span></span> |
| ---
<span data-ttu-id="25ce6-1147">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1148">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1149">'Identity'</span></span>
- <span data-ttu-id="25ce6-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1151">'Razor'</span></span>
- <span data-ttu-id="25ce6-1152">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1153">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1154">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1155">'Identity'</span></span>
- <span data-ttu-id="25ce6-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1157">'Razor'</span></span>
- <span data-ttu-id="25ce6-1158">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1159">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1160">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1161">'Identity'</span></span>
- <span data-ttu-id="25ce6-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1163">'Razor'</span></span>
- <span data-ttu-id="25ce6-1164">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1165">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1166">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1167">'Identity'</span></span>
- <span data-ttu-id="25ce6-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1169">'Razor'</span></span>
- <span data-ttu-id="25ce6-1170">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1171">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1172">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1173">'Identity'</span></span>
- <span data-ttu-id="25ce6-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1175">'Razor'</span></span>
- <span data-ttu-id="25ce6-1176">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1177">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1178">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1179">'Identity'</span></span>
- <span data-ttu-id="25ce6-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1181">'Razor'</span></span>
- <span data-ttu-id="25ce6-1182">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1183">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1184">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1185">'Identity'</span></span>
- <span data-ttu-id="25ce6-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1187">'Razor'</span></span>
- <span data-ttu-id="25ce6-1188">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1189">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1190">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1191">'Identity'</span></span>
- <span data-ttu-id="25ce6-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1193">'Razor'</span></span>
- <span data-ttu-id="25ce6-1194">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1195">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1196">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1197">'Identity'</span></span>
- <span data-ttu-id="25ce6-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1199">'Razor'</span></span>
- <span data-ttu-id="25ce6-1200">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1201">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1202">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1203">'Identity'</span></span>
- <span data-ttu-id="25ce6-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1205">'Razor'</span></span>
- <span data-ttu-id="25ce6-1206">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1207">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1208">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1209">'Identity'</span></span>
- <span data-ttu-id="25ce6-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1211">'Razor'</span></span>
- <span data-ttu-id="25ce6-1212">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1213">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1214">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1215">'Identity'</span></span>
- <span data-ttu-id="25ce6-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1217">'Razor'</span></span>
- <span data-ttu-id="25ce6-1218">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1219">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1220">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1221">'Identity'</span></span>
- <span data-ttu-id="25ce6-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1223">'Razor'</span></span>
- <span data-ttu-id="25ce6-1224">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1225">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1226">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1227">'Identity'</span></span>
- <span data-ttu-id="25ce6-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1229">'Razor'</span></span>
- <span data-ttu-id="25ce6-1230">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1231">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1232">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1233">'Identity'</span></span>
- <span data-ttu-id="25ce6-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1235">'Razor'</span></span>
- <span data-ttu-id="25ce6-1236">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Да   | | `/my-cool-redirect-rule/1234/5678` | Да   | | `/anotherredirect-rule/1234/5678`  | Да   |</span><span class="sxs-lookup"><span data-stu-id="25ce6-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="25ce6-1238">Правило переопределения `^rewrite-rule/(\d+)/(\d+)` соответствует путям, только если они начинаются с `rewrite-rule/`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="25ce6-1239">В следующей таблице обратите внимание на разницу в сопоставлении.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="25ce6-1240">Path</span><span class="sxs-lookup"><span data-stu-id="25ce6-1240">Path</span></span>                              | <span data-ttu-id="25ce6-1241">Соответствие</span><span class="sxs-lookup"><span data-stu-id="25ce6-1241">Match</span></span> |
| ---
<span data-ttu-id="25ce6-1242">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1243">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1244">'Identity'</span></span>
- <span data-ttu-id="25ce6-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1246">'Razor'</span></span>
- <span data-ttu-id="25ce6-1247">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1248">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1249">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1250">'Identity'</span></span>
- <span data-ttu-id="25ce6-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1252">'Razor'</span></span>
- <span data-ttu-id="25ce6-1253">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1254">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1255">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1256">'Identity'</span></span>
- <span data-ttu-id="25ce6-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1258">'Razor'</span></span>
- <span data-ttu-id="25ce6-1259">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1260">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1261">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1262">'Identity'</span></span>
- <span data-ttu-id="25ce6-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1264">'Razor'</span></span>
- <span data-ttu-id="25ce6-1265">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1266">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1267">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1268">'Identity'</span></span>
- <span data-ttu-id="25ce6-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1270">'Razor'</span></span>
- <span data-ttu-id="25ce6-1271">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1272">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1273">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1274">'Identity'</span></span>
- <span data-ttu-id="25ce6-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1276">'Razor'</span></span>
- <span data-ttu-id="25ce6-1277">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1278">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1279">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1280">'Identity'</span></span>
- <span data-ttu-id="25ce6-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1282">'Razor'</span></span>
- <span data-ttu-id="25ce6-1283">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1284">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1285">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1286">'Identity'</span></span>
- <span data-ttu-id="25ce6-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1288">'Razor'</span></span>
- <span data-ttu-id="25ce6-1289">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1290">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1291">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1292">'Identity'</span></span>
- <span data-ttu-id="25ce6-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1294">'Razor'</span></span>
- <span data-ttu-id="25ce6-1295">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1296">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1297">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1298">'Identity'</span></span>
- <span data-ttu-id="25ce6-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1300">'Razor'</span></span>
- <span data-ttu-id="25ce6-1301">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1302">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1303">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1304">'Identity'</span></span>
- <span data-ttu-id="25ce6-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1306">'Razor'</span></span>
- <span data-ttu-id="25ce6-1307">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1308">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1309">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1310">'Identity'</span></span>
- <span data-ttu-id="25ce6-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1312">'Razor'</span></span>
- <span data-ttu-id="25ce6-1313">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1314">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1315">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1316">'Identity'</span></span>
- <span data-ttu-id="25ce6-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1318">'Razor'</span></span>
- <span data-ttu-id="25ce6-1319">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1320">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1321">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1322">'Identity'</span></span>
- <span data-ttu-id="25ce6-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1324">'Razor'</span></span>
- <span data-ttu-id="25ce6-1325">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Да   | | `/my-cool-rewrite-rule/1234/5678` | Нет    | | `/anotherrewrite-rule/1234/5678`  | Нет    |</span><span class="sxs-lookup"><span data-stu-id="25ce6-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="25ce6-1327">После части `^rewrite-rule/` выражения стоят две группы записи `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="25ce6-1328">`\d` означает *соответствие цифре (числу)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="25ce6-1329">Знак "плюс" (`+`) означает *соответствие одному или нескольким предшествующим символам*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="25ce6-1330">Таким образом, URL-адрес должен содержать число, за которым идет прямая косая черта, за которой идет другое число.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="25ce6-1331">Эти группы записи внедряются в переопределенный URL-адрес как `$1` и `$2`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="25ce6-1332">Строка замены для правила переопределения помещает группы записи в строку запроса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="25ce6-1333">Запрошенный путь `/rewrite-rule/1234/5678` переопределяется, чтобы ресурс можно было получить по адресу `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="25ce6-1334">Если в исходном запросе присутствует строка запроса, она сохраняется при переопределении URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="25ce6-1335">Круговое обращение к серверу для получения ресурса не выполняется.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="25ce6-1336">Если ресурс существует, он извлекается и возвращается клиенту с кодом состояния *200 (ОК)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="25ce6-1337">Так как клиент не перенаправляется, URL-адрес в адресной строке браузера не изменяется.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="25ce6-1338">Клиенты не могут узнать, что на сервере произошла операция переопределения URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="25ce6-1339">Используйте `skipRemainingRules: true` при любой возможности, так как правила сопоставления потребляют много ресурсов и ухудшают время отклика приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="25ce6-1340">Чтобы максимально ускорить отклик приложения:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="25ce6-1341">расположите правила переопределения в порядке от наиболее к наименее часто используемому;</span><span class="sxs-lookup"><span data-stu-id="25ce6-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="25ce6-1342">пропускайте обработку оставшихся правил, когда совпадение найдено и обработка дополнительных правил не требуется.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="25ce6-1343">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="25ce6-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="25ce6-1344">Для применения правил mod_rewrite Apache можно использовать <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="25ce6-1345">Убедитесь, что файл правил развертывается вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="25ce6-1346">Дополнительные сведения и примеры правил mod_rewrite см. в статье о [правилах mod_rewrite Apache](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="25ce6-1347"><xref:System.IO.StreamReader> используется для чтения правил из файла *ApacheModRewrite.txt*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="25ce6-1348">Пример приложения перенаправляет запросы из `/apache-mod-rules-redirect/(.\*)` в `/redirected?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="25ce6-1349">Отклик имеет код состояния *302 (найдено)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="25ce6-1350">Исходный запрос: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="25ce6-1352">ПО промежуточного слоя поддерживает следующие переменные сервера в mod_rewrite Apache:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="25ce6-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="25ce6-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="25ce6-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="25ce6-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="25ce6-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="25ce6-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="25ce6-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="25ce6-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="25ce6-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="25ce6-1358">HTTP_HOST</span></span>
* <span data-ttu-id="25ce6-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="25ce6-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="25ce6-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="25ce6-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="25ce6-1361">HTTPS</span></span>
* <span data-ttu-id="25ce6-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="25ce6-1362">IPV6</span></span>
* <span data-ttu-id="25ce6-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="25ce6-1363">QUERY_STRING</span></span>
* <span data-ttu-id="25ce6-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="25ce6-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="25ce6-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="25ce6-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="25ce6-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="25ce6-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="25ce6-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="25ce6-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="25ce6-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="25ce6-1369">REQUEST_URI</span></span>
* <span data-ttu-id="25ce6-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="25ce6-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="25ce6-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="25ce6-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1372">SERVER_PORT</span></span>
* <span data-ttu-id="25ce6-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="25ce6-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="25ce6-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="25ce6-1374">TIME</span></span>
* <span data-ttu-id="25ce6-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="25ce6-1375">TIME_DAY</span></span>
* <span data-ttu-id="25ce6-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1376">TIME_HOUR</span></span>
* <span data-ttu-id="25ce6-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="25ce6-1377">TIME_MIN</span></span>
* <span data-ttu-id="25ce6-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="25ce6-1378">TIME_MON</span></span>
* <span data-ttu-id="25ce6-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="25ce6-1379">TIME_SEC</span></span>
* <span data-ttu-id="25ce6-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="25ce6-1380">TIME_WDAY</span></span>
* <span data-ttu-id="25ce6-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="25ce6-1382">Правила модуля переопределения URL-адресов для IIS</span><span class="sxs-lookup"><span data-stu-id="25ce6-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="25ce6-1383">Чтобы использовать тот же набор правил, который применяется к модулю переопределения URL-адресов для IIS, используйте <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="25ce6-1384">Убедитесь, что файл правил развертывается вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="25ce6-1385">Не указывайте ПО промежуточного слоя использовать файл приложения *web.config* при работе в службах IIS Windows Server.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="25ce6-1386">В IIS эти правила нужно хранить за пределами файла приложения *web.config*, чтобы предотвратить конфликты с модулем переопределения для IIS.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="25ce6-1387">Дополнительные сведения и примеры правил модуля переопределения URL-адресов для IIS см. в разделах [Использование модуля переопределения URL-адресов 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) и [Справочник по конфигурации модуля переопределения URL-адресов](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="25ce6-1388"><xref:System.IO.StreamReader> используется для чтения правил из файла *IISUrlRewrite.xml*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="25ce6-1389">Пример приложения переопределяет запросы с `/iis-rules-rewrite/(.*)` на `/rewritten?id=$1`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="25ce6-1390">Клиенту отправляется отклик с кодом состояния *200 (ОК)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="25ce6-1391">Исходный запрос: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запрос и отклик](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="25ce6-1393">Если имеется активный модуль переопределения для IIS, где настроены правила брандмауэра уровня сервера, способные негативно повлиять на ваше приложение, можно отключить этот модуль для приложения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="25ce6-1394">Дополнительные сведения см. в разделе [Отключение модулей IIS](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="25ce6-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="25ce6-1395">Неподдерживаемые функции</span><span class="sxs-lookup"><span data-stu-id="25ce6-1395">Unsupported features</span></span>

<span data-ttu-id="25ce6-1396">ПО промежуточного слоя, выпущенное вместе с ASP.NET Core 2.x, не поддерживает следующие функции в модуле переопределения URL-адресов для IIS:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="25ce6-1397">Правила для исходящих подключений</span><span class="sxs-lookup"><span data-stu-id="25ce6-1397">Outbound Rules</span></span>
* <span data-ttu-id="25ce6-1398">Пользовательские переменные сервера</span><span class="sxs-lookup"><span data-stu-id="25ce6-1398">Custom Server Variables</span></span>
* <span data-ttu-id="25ce6-1399">Знаки подстановки</span><span class="sxs-lookup"><span data-stu-id="25ce6-1399">Wildcards</span></span>
* <span data-ttu-id="25ce6-1400">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="25ce6-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="25ce6-1401">Поддерживаемые переменные сервера</span><span class="sxs-lookup"><span data-stu-id="25ce6-1401">Supported server variables</span></span>

<span data-ttu-id="25ce6-1402">ПО промежуточного слоя поддерживает следующие переменные сервера в модуле переопределения URL-адресов для IIS:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="25ce6-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="25ce6-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="25ce6-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="25ce6-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="25ce6-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="25ce6-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="25ce6-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="25ce6-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="25ce6-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="25ce6-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="25ce6-1408">HTTP_HOST</span></span>
* <span data-ttu-id="25ce6-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="25ce6-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="25ce6-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="25ce6-1410">HTTP_URL</span></span>
* <span data-ttu-id="25ce6-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="25ce6-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="25ce6-1412">HTTPS</span></span>
* <span data-ttu-id="25ce6-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="25ce6-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="25ce6-1414">QUERY_STRING</span></span>
* <span data-ttu-id="25ce6-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="25ce6-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="25ce6-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="25ce6-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="25ce6-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="25ce6-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="25ce6-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="25ce6-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="25ce6-1419">Можно также получить <xref:Microsoft.Extensions.FileProviders.IFileProvider> через <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="25ce6-1420">Такой подход позволяет более гибко задавать расположение для файлов с правилами переопределения.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="25ce6-1421">Убедитесь, что эти файлы развертываются на сервере по указанному пути.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="25ce6-1422">Правило, основанное на методе</span><span class="sxs-lookup"><span data-stu-id="25ce6-1422">Method-based rule</span></span>

<span data-ttu-id="25ce6-1423">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, чтобы реализовать собственную логику правил в методе.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="25ce6-1424">`Add` предоставляет <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, который предоставляет <xref:Microsoft.AspNetCore.Http.HttpContext> для использования в методе.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="25ce6-1425">[RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) определяет, как осуществляется дополнительная обработка в конвейере.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="25ce6-1426">Установите значение одного из полей <xref:Microsoft.AspNetCore.Rewrite.RuleResult> из следующей таблицы.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="25ce6-1427">Действие</span><span class="sxs-lookup"><span data-stu-id="25ce6-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="25ce6-1428">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1429">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1430">'Identity'</span></span>
- <span data-ttu-id="25ce6-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1432">'Razor'</span></span>
- <span data-ttu-id="25ce6-1433">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1434">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1435">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1436">'Identity'</span></span>
- <span data-ttu-id="25ce6-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1438">'Razor'</span></span>
- <span data-ttu-id="25ce6-1439">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1440">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1441">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1442">'Identity'</span></span>
- <span data-ttu-id="25ce6-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1444">'Razor'</span></span>
- <span data-ttu-id="25ce6-1445">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1446">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1447">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1448">'Identity'</span></span>
- <span data-ttu-id="25ce6-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1450">'Razor'</span></span>
- <span data-ttu-id="25ce6-1451">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1452">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1453">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1454">'Identity'</span></span>
- <span data-ttu-id="25ce6-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1456">'Razor'</span></span>
- <span data-ttu-id="25ce6-1457">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1458">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1459">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1460">'Identity'</span></span>
- <span data-ttu-id="25ce6-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1462">'Razor'</span></span>
- <span data-ttu-id="25ce6-1463">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1464">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1465">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1466">'Identity'</span></span>
- <span data-ttu-id="25ce6-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1468">'Razor'</span></span>
- <span data-ttu-id="25ce6-1469">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1470">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1471">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1472">'Identity'</span></span>
- <span data-ttu-id="25ce6-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1474">'Razor'</span></span>
- <span data-ttu-id="25ce6-1475">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1476">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1477">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1478">'Identity'</span></span>
- <span data-ttu-id="25ce6-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1480">'Razor'</span></span>
- <span data-ttu-id="25ce6-1481">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1482">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1483">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1484">'Identity'</span></span>
- <span data-ttu-id="25ce6-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1486">'Razor'</span></span>
- <span data-ttu-id="25ce6-1487">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1488">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1489">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1490">'Identity'</span></span>
- <span data-ttu-id="25ce6-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1492">'Razor'</span></span>
- <span data-ttu-id="25ce6-1493">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1494">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1495">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1496">'Identity'</span></span>
- <span data-ttu-id="25ce6-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1498">'Razor'</span></span>
- <span data-ttu-id="25ce6-1499">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1500">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1501">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1502">'Identity'</span></span>
- <span data-ttu-id="25ce6-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1504">'Razor'</span></span>
- <span data-ttu-id="25ce6-1505">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1506">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1507">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1508">'Identity'</span></span>
- <span data-ttu-id="25ce6-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1510">'Razor'</span></span>
- <span data-ttu-id="25ce6-1511">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1512">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1513">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1514">'Identity'</span></span>
- <span data-ttu-id="25ce6-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1516">'Razor'</span></span>
- <span data-ttu-id="25ce6-1517">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1518">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1519">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1520">'Identity'</span></span>
- <span data-ttu-id="25ce6-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1522">'Razor'</span></span>
- <span data-ttu-id="25ce6-1523">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1525">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1526">'Identity'</span></span>
- <span data-ttu-id="25ce6-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1528">'Razor'</span></span>
- <span data-ttu-id="25ce6-1529">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1530">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1531">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1532">'Identity'</span></span>
- <span data-ttu-id="25ce6-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1534">'Razor'</span></span>
- <span data-ttu-id="25ce6-1535">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1536">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1537">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1538">'Identity'</span></span>
- <span data-ttu-id="25ce6-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1540">'Razor'</span></span>
- <span data-ttu-id="25ce6-1541">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1542">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1543">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1544">'Identity'</span></span>
- <span data-ttu-id="25ce6-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1546">'Razor'</span></span>
- <span data-ttu-id="25ce6-1547">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1548">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1549">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1550">'Identity'</span></span>
- <span data-ttu-id="25ce6-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1552">'Razor'</span></span>
- <span data-ttu-id="25ce6-1553">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1554">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1555">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1556">'Identity'</span></span>
- <span data-ttu-id="25ce6-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1558">'Razor'</span></span>
- <span data-ttu-id="25ce6-1559">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1560">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1561">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1562">'Identity'</span></span>
- <span data-ttu-id="25ce6-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1564">'Razor'</span></span>
- <span data-ttu-id="25ce6-1565">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1566">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1567">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1568">'Identity'</span></span>
- <span data-ttu-id="25ce6-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1570">'Razor'</span></span>
- <span data-ttu-id="25ce6-1571">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1572">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1573">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1574">'Identity'</span></span>
- <span data-ttu-id="25ce6-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1576">'Razor'</span></span>
- <span data-ttu-id="25ce6-1577">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1578">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1579">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1580">'Identity'</span></span>
- <span data-ttu-id="25ce6-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1582">'Razor'</span></span>
- <span data-ttu-id="25ce6-1583">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1584">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1585">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1586">'Identity'</span></span>
- <span data-ttu-id="25ce6-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1588">'Razor'</span></span>
- <span data-ttu-id="25ce6-1589">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1590">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1591">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1592">'Identity'</span></span>
- <span data-ttu-id="25ce6-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1594">'Razor'</span></span>
- <span data-ttu-id="25ce6-1595">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1596">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1597">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1598">'Identity'</span></span>
- <span data-ttu-id="25ce6-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1600">'Razor'</span></span>
- <span data-ttu-id="25ce6-1601">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1602">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1603">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1604">'Identity'</span></span>
- <span data-ttu-id="25ce6-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1606">'Razor'</span></span>
- <span data-ttu-id="25ce6-1607">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1608">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1609">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1610">'Identity'</span></span>
- <span data-ttu-id="25ce6-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1612">'Razor'</span></span>
- <span data-ttu-id="25ce6-1613">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1614">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1615">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1616">'Identity'</span></span>
- <span data-ttu-id="25ce6-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1618">'Razor'</span></span>
- <span data-ttu-id="25ce6-1619">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1620">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1621">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1622">'Identity'</span></span>
- <span data-ttu-id="25ce6-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1624">'Razor'</span></span>
- <span data-ttu-id="25ce6-1625">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1626">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1627">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1628">'Identity'</span></span>
- <span data-ttu-id="25ce6-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1630">'Razor'</span></span>
- <span data-ttu-id="25ce6-1631">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1632">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1633">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1634">'Identity'</span></span>
- <span data-ttu-id="25ce6-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1636">'Razor'</span></span>
- <span data-ttu-id="25ce6-1637">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1638">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1639">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1640">'Identity'</span></span>
- <span data-ttu-id="25ce6-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1642">'Razor'</span></span>
- <span data-ttu-id="25ce6-1643">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1644">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1645">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1646">'Identity'</span></span>
- <span data-ttu-id="25ce6-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1648">'Razor'</span></span>
- <span data-ttu-id="25ce6-1649">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1650">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1651">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1652">'Identity'</span></span>
- <span data-ttu-id="25ce6-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1654">'Razor'</span></span>
- <span data-ttu-id="25ce6-1655">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1656">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1657">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1658">'Identity'</span></span>
- <span data-ttu-id="25ce6-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1660">'Razor'</span></span>
- <span data-ttu-id="25ce6-1661">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1662">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1663">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1664">'Identity'</span></span>
- <span data-ttu-id="25ce6-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1666">'Razor'</span></span>
- <span data-ttu-id="25ce6-1667">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1668">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1669">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1670">'Identity'</span></span>
- <span data-ttu-id="25ce6-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1672">'Razor'</span></span>
- <span data-ttu-id="25ce6-1673">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1674">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1675">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1676">'Identity'</span></span>
- <span data-ttu-id="25ce6-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1678">'Razor'</span></span>
- <span data-ttu-id="25ce6-1679">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1680">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1681">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1682">'Identity'</span></span>
- <span data-ttu-id="25ce6-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1684">'Razor'</span></span>
- <span data-ttu-id="25ce6-1685">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1686">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1687">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1688">'Identity'</span></span>
- <span data-ttu-id="25ce6-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1690">'Razor'</span></span>
- <span data-ttu-id="25ce6-1691">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1692">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1693">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1694">'Identity'</span></span>
- <span data-ttu-id="25ce6-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1696">'Razor'</span></span>
- <span data-ttu-id="25ce6-1697">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1698">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1699">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1700">'Identity'</span></span>
- <span data-ttu-id="25ce6-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1702">'Razor'</span></span>
- <span data-ttu-id="25ce6-1703">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1704">-------------------------------- | | `RuleResult.ContinueRules` (по умолчанию) | Продолжение применения правил.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="25ce6-1705">| | `RuleResult.EndResponse`             | Остановка применения правил и отправка ответа.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="25ce6-1706">| | `RuleResult.SkipRemainingRules`      | Остановка применения правил и отправка контекста в следующий компонент ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="25ce6-1707">Пример приложения демонстрирует метод, который перенаправляет запросы для путей, заканчивающихся на *.xml*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="25ce6-1708">Если запрос выполняется для `/file.xml`, запрос перенаправляется к `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="25ce6-1709">Для кода состояния задается значение *301 (перемещен окончательно)* .</span><span class="sxs-lookup"><span data-stu-id="25ce6-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="25ce6-1710">Когда браузер отправляет новый запрос на */xmlfiles/file.xml*, ПО промежуточного слоя статических файлов предоставляет файл клиенту из папки *wwwroot/xmlfiles*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="25ce6-1711">Для перенаправления необходимо явно указать код состояния ответа.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="25ce6-1712">В противном случае возвращается код состояния *200 (ОК)* и перенаправление на стороне клиента не происходит.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="25ce6-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="25ce6-1714">Этот подход также переопределяет запросы.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="25ce6-1715">В примере приложения показано переопределение пути для любого запроса текстового файла, чтобы отправить текстовый файл *file.txt* из папки *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="25ce6-1716">ПО промежуточного слоя статических файлов предоставляет файл по обновленному пути запроса:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="25ce6-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="25ce6-1718">Правило, основанное на IRule</span><span class="sxs-lookup"><span data-stu-id="25ce6-1718">IRule-based rule</span></span>

<span data-ttu-id="25ce6-1719">Используйте <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, чтобы использовать собственную логику правил в классе, реализующем интерфейс <xref:Microsoft.AspNetCore.Rewrite.IRule>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="25ce6-1720">`IRule` позволяет более гибко применять правила, основанные на методах.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="25ce6-1721">Класс реализации может включать конструктор, который позволяет передавать параметры для метода <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="25ce6-1722">Значения параметров в примере приложения для `extension` и `newPath` проверяются на соответствие нескольким условиям.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="25ce6-1723">`extension` должен содержать одно из значений: *.png*, *.jpg* или *.gif*.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="25ce6-1724">Если `newPath` не является допустимым, возникает исключение <xref:System.ArgumentException>.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="25ce6-1725">Если запрос выполняется для *image.png*, запрос перенаправляется к `/png-images/image.png`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="25ce6-1726">Если запрос выполняется для *image.jpg*, запрос перенаправляется к `/jpg-images/image.jpg`.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="25ce6-1727">Для кода состояния устанавливается значение *301 (перемещен окончательно)* , а также задается `context.Result`, чтобы остановить обработку правил и отправить отклик.</span><span class="sxs-lookup"><span data-stu-id="25ce6-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="25ce6-1728">Исходный запрос: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1728">Original Request: `/image.png`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики для image.png](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="25ce6-1730">Исходный запрос: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1730">Original Request: `/image.jpg`</span></span>

![Окно браузера, в котором средства разработчика отслеживают запросы и отклики для image.jpg](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="25ce6-1732">Примеры регулярных выражений</span><span class="sxs-lookup"><span data-stu-id="25ce6-1732">Regex examples</span></span>

| <span data-ttu-id="25ce6-1733">Goal</span><span class="sxs-lookup"><span data-stu-id="25ce6-1733">Goal</span></span> | <span data-ttu-id="25ce6-1734">Пример строки регулярного выражения</span><span class="sxs-lookup"><span data-stu-id="25ce6-1734">Regex String &</span></span><br><span data-ttu-id="25ce6-1735">и совпадения</span><span class="sxs-lookup"><span data-stu-id="25ce6-1735">Match Example</span></span> | <span data-ttu-id="25ce6-1736">Пример строки замены и</span><span class="sxs-lookup"><span data-stu-id="25ce6-1736">Replacement String &</span></span><br><span data-ttu-id="25ce6-1737">выходных данных</span><span class="sxs-lookup"><span data-stu-id="25ce6-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="25ce6-1738">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1739">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1740">'Identity'</span></span>
- <span data-ttu-id="25ce6-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1742">'Razor'</span></span>
- <span data-ttu-id="25ce6-1743">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1744">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1745">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1746">'Identity'</span></span>
- <span data-ttu-id="25ce6-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1748">'Razor'</span></span>
- <span data-ttu-id="25ce6-1749">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1750">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1751">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1752">'Identity'</span></span>
- <span data-ttu-id="25ce6-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1754">'Razor'</span></span>
- <span data-ttu-id="25ce6-1755">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1756">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1757">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1758">'Identity'</span></span>
- <span data-ttu-id="25ce6-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1760">'Razor'</span></span>
- <span data-ttu-id="25ce6-1761">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1762">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1763">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1764">'Identity'</span></span>
- <span data-ttu-id="25ce6-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1766">'Razor'</span></span>
- <span data-ttu-id="25ce6-1767">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1768">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1769">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1770">'Identity'</span></span>
- <span data-ttu-id="25ce6-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1772">'Razor'</span></span>
- <span data-ttu-id="25ce6-1773">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1774">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1775">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1776">'Identity'</span></span>
- <span data-ttu-id="25ce6-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1778">'Razor'</span></span>
- <span data-ttu-id="25ce6-1779">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1780">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1781">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1782">'Identity'</span></span>
- <span data-ttu-id="25ce6-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1784">'Razor'</span></span>
- <span data-ttu-id="25ce6-1785">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1786">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1787">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1788">'Identity'</span></span>
- <span data-ttu-id="25ce6-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1790">'Razor'</span></span>
- <span data-ttu-id="25ce6-1791">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1792">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1793">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1794">'Identity'</span></span>
- <span data-ttu-id="25ce6-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1796">'Razor'</span></span>
- <span data-ttu-id="25ce6-1797">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1798">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1799">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1800">'Identity'</span></span>
- <span data-ttu-id="25ce6-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1802">'Razor'</span></span>
- <span data-ttu-id="25ce6-1803">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1804">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1805">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1806">'Identity'</span></span>
- <span data-ttu-id="25ce6-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1808">'Razor'</span></span>
- <span data-ttu-id="25ce6-1809">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1810">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1811">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1812">'Identity'</span></span>
- <span data-ttu-id="25ce6-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1814">'Razor'</span></span>
- <span data-ttu-id="25ce6-1815">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1817">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1818">'Identity'</span></span>
- <span data-ttu-id="25ce6-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1820">'Razor'</span></span>
- <span data-ttu-id="25ce6-1821">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1822">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1823">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1824">'Identity'</span></span>
- <span data-ttu-id="25ce6-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1826">'Razor'</span></span>
- <span data-ttu-id="25ce6-1827">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1828">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1829">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1830">'Identity'</span></span>
- <span data-ttu-id="25ce6-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1832">'Razor'</span></span>
- <span data-ttu-id="25ce6-1833">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1834">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1835">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1836">'Identity'</span></span>
- <span data-ttu-id="25ce6-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1838">'Razor'</span></span>
- <span data-ttu-id="25ce6-1839">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1840">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1841">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1842">'Identity'</span></span>
- <span data-ttu-id="25ce6-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1844">'Razor'</span></span>
- <span data-ttu-id="25ce6-1845">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1846">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1847">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1848">'Identity'</span></span>
- <span data-ttu-id="25ce6-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1850">'Razor'</span></span>
- <span data-ttu-id="25ce6-1851">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1852">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1853">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1854">'Identity'</span></span>
- <span data-ttu-id="25ce6-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1856">'Razor'</span></span>
- <span data-ttu-id="25ce6-1857">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1858">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1859">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1860">'Identity'</span></span>
- <span data-ttu-id="25ce6-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1862">'Razor'</span></span>
- <span data-ttu-id="25ce6-1863">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1864">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1865">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1866">'Identity'</span></span>
- <span data-ttu-id="25ce6-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1868">'Razor'</span></span>
- <span data-ttu-id="25ce6-1869">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1870">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1871">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1872">'Identity'</span></span>
- <span data-ttu-id="25ce6-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1874">'Razor'</span></span>
- <span data-ttu-id="25ce6-1875">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1876">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1877">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1878">'Identity'</span></span>
- <span data-ttu-id="25ce6-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1880">'Razor'</span></span>
- <span data-ttu-id="25ce6-1881">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1882">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1883">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1884">'Identity'</span></span>
- <span data-ttu-id="25ce6-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1886">'Razor'</span></span>
- <span data-ttu-id="25ce6-1887">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1888">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1889">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1890">'Identity'</span></span>
- <span data-ttu-id="25ce6-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1892">'Razor'</span></span>
- <span data-ttu-id="25ce6-1893">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1894">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1895">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1896">'Identity'</span></span>
- <span data-ttu-id="25ce6-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1898">'Razor'</span></span>
- <span data-ttu-id="25ce6-1899">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1900">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1901">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1902">'Identity'</span></span>
- <span data-ttu-id="25ce6-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1904">'Razor'</span></span>
- <span data-ttu-id="25ce6-1905">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1906">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1907">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1908">'Identity'</span></span>
- <span data-ttu-id="25ce6-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1910">'Razor'</span></span>
- <span data-ttu-id="25ce6-1911">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="25ce6-1912">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="25ce6-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="25ce6-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1913">'Blazor'</span></span>
- <span data-ttu-id="25ce6-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1914">'Identity'</span></span>
- <span data-ttu-id="25ce6-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="25ce6-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="25ce6-1916">'Razor'</span></span>
- <span data-ttu-id="25ce6-1917">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="25ce6-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="25ce6-1918">------------------- | | Переопределение пути в строке запроса | `^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="25ce6-1919">`/path?var1=abc&var2=123` | | Удаление косой черты в конце | `(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="25ce6-1920">`/path` | | Добавление косой черты в конце | `(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="25ce6-1921">`/path/` | | Запрет переопределения отдельных запросов | `^(.*)(?<!\.axd)$` или `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="25ce6-1922">Да: `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="25ce6-1923">Нет: `/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="25ce6-1924">`/resource.axd` | | Переупорядочение сегментов URL-адреса | `path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="25ce6-1925">`path/3/2/1` | | Замена сегмента URL-адреса | `^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="25ce6-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="25ce6-1926">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="25ce6-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="25ce6-1927">Регулярные выражения в .NET</span><span class="sxs-lookup"><span data-stu-id="25ce6-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="25ce6-1928">Элементы языка регулярных выражений — краткий справочник</span><span class="sxs-lookup"><span data-stu-id="25ce6-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="25ce6-1929">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="25ce6-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="25ce6-1930">Использование модуля переопределения URL-адресов 2.0 (для IIS)</span><span class="sxs-lookup"><span data-stu-id="25ce6-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="25ce6-1931">Справочник по конфигурации модуля переопределения URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="25ce6-1932">Форум по модулю переопределения URL-адресов для IIS</span><span class="sxs-lookup"><span data-stu-id="25ce6-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="25ce6-1933">Сохранение простой структуры URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="25ce6-1934">10 советов по переопределению URL-адресов</span><span class="sxs-lookup"><span data-stu-id="25ce6-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="25ce6-1935">Аспекты использования косой черты</span><span class="sxs-lookup"><span data-stu-id="25ce6-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
