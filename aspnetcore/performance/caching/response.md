---
<span data-ttu-id="af4da-101">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-102">'Blazor'</span></span>
- <span data-ttu-id="af4da-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-103">'Identity'</span></span>
- <span data-ttu-id="af4da-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-105">'Razor'</span></span>
- <span data-ttu-id="af4da-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="af4da-107">Кэширование ответов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af4da-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="af4da-108">[Джон Луо](https://github.com/JunTaoLuo), [Рик Андерсон (](https://twitter.com/RickAndMSFT)и [Виктор Смит](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="af4da-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="af4da-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="af4da-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="af4da-110">Кэширование ответов сокращает количество запросов к веб-серверу, которые клиент или прокси выполняет.</span><span class="sxs-lookup"><span data-stu-id="af4da-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="af4da-111">Кэширование ответов также сокращает объем работы, выполняемой веб-сервером для создания ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="af4da-112">Кэширование ответов управляется заголовками, которые определяют, как клиент, прокси-сервер и промежуточное по должны кэшировать ответы.</span><span class="sxs-lookup"><span data-stu-id="af4da-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="af4da-113">[Атрибут респонсекаче](#responsecache-attribute) участвует в установках заголовков кэширования ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="af4da-114">Клиенты и промежуточные прокси-серверы должны учитывать заголовки для кэширования ответов в [спецификации кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="af4da-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="af4da-115">Для кэширования на стороне сервера, которое следует за спецификацией кэширования HTTP 1,1, используйте по [промежуточного слоя кэширование ответа](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="af4da-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="af4da-116">По промежуточного слоя может использовать <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> свойства, чтобы повлиять на поведение кэширования на стороне сервера.</span><span class="sxs-lookup"><span data-stu-id="af4da-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="af4da-117">Кэширование ответов на основе HTTP</span><span class="sxs-lookup"><span data-stu-id="af4da-117">HTTP-based response caching</span></span>

<span data-ttu-id="af4da-118">[Спецификация кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234) описывает, как должны вести себя Интернет-кэши.</span><span class="sxs-lookup"><span data-stu-id="af4da-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="af4da-119">Основной заголовок HTTP, используемый для кэширования, — это [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), который используется для указания *директив*кэша.</span><span class="sxs-lookup"><span data-stu-id="af4da-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="af4da-120">Директивы управляют поведением кэширования в качестве запросов от клиентов к серверам, а также по мере того, как отклики от серверов возвращаются к клиентам.</span><span class="sxs-lookup"><span data-stu-id="af4da-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="af4da-121">Запросы и ответы перемещаются через прокси-серверы, а прокси-серверы также должны соответствовать спецификации кэширования HTTP 1,1.</span><span class="sxs-lookup"><span data-stu-id="af4da-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="af4da-122">Общие `Cache-Control` директивы показаны в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="af4da-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="af4da-123">Директива</span><span class="sxs-lookup"><span data-stu-id="af4da-123">Directive</span></span>                                                       | <span data-ttu-id="af4da-124">Действие</span><span class="sxs-lookup"><span data-stu-id="af4da-124">Action</span></span> |
| ---
<span data-ttu-id="af4da-125">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-126">'Blazor'</span></span>
- <span data-ttu-id="af4da-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-127">'Identity'</span></span>
- <span data-ttu-id="af4da-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-129">'Razor'</span></span>
- <span data-ttu-id="af4da-130">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-131">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-132">'Blazor'</span></span>
- <span data-ttu-id="af4da-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-133">'Identity'</span></span>
- <span data-ttu-id="af4da-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-135">'Razor'</span></span>
- <span data-ttu-id="af4da-136">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-137">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-138">'Blazor'</span></span>
- <span data-ttu-id="af4da-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-139">'Identity'</span></span>
- <span data-ttu-id="af4da-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-141">'Razor'</span></span>
- <span data-ttu-id="af4da-142">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-143">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-144">'Blazor'</span></span>
- <span data-ttu-id="af4da-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-145">'Identity'</span></span>
- <span data-ttu-id="af4da-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-147">'Razor'</span></span>
- <span data-ttu-id="af4da-148">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-149">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-150">'Blazor'</span></span>
- <span data-ttu-id="af4da-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-151">'Identity'</span></span>
- <span data-ttu-id="af4da-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-153">'Razor'</span></span>
- <span data-ttu-id="af4da-154">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-155">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-156">'Blazor'</span></span>
- <span data-ttu-id="af4da-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-157">'Identity'</span></span>
- <span data-ttu-id="af4da-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-159">'Razor'</span></span>
- <span data-ttu-id="af4da-160">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-161">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-162">'Blazor'</span></span>
- <span data-ttu-id="af4da-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-163">'Identity'</span></span>
- <span data-ttu-id="af4da-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-165">'Razor'</span></span>
- <span data-ttu-id="af4da-166">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-167">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-168">'Blazor'</span></span>
- <span data-ttu-id="af4da-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-169">'Identity'</span></span>
- <span data-ttu-id="af4da-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-171">'Razor'</span></span>
- <span data-ttu-id="af4da-172">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-173">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-174">'Blazor'</span></span>
- <span data-ttu-id="af4da-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-175">'Identity'</span></span>
- <span data-ttu-id="af4da-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-177">'Razor'</span></span>
- <span data-ttu-id="af4da-178">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-179">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-180">'Blazor'</span></span>
- <span data-ttu-id="af4da-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-181">'Identity'</span></span>
- <span data-ttu-id="af4da-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-183">'Razor'</span></span>
- <span data-ttu-id="af4da-184">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-185">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-186">'Blazor'</span></span>
- <span data-ttu-id="af4da-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-187">'Identity'</span></span>
- <span data-ttu-id="af4da-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-189">'Razor'</span></span>
- <span data-ttu-id="af4da-190">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-191">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-192">'Blazor'</span></span>
- <span data-ttu-id="af4da-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-193">'Identity'</span></span>
- <span data-ttu-id="af4da-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-195">'Razor'</span></span>
- <span data-ttu-id="af4da-196">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-197">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-198">'Blazor'</span></span>
- <span data-ttu-id="af4da-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-199">'Identity'</span></span>
- <span data-ttu-id="af4da-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-201">'Razor'</span></span>
- <span data-ttu-id="af4da-202">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-203">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-204">'Blazor'</span></span>
- <span data-ttu-id="af4da-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-205">'Identity'</span></span>
- <span data-ttu-id="af4da-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-207">'Razor'</span></span>
- <span data-ttu-id="af4da-208">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-209">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-210">'Blazor'</span></span>
- <span data-ttu-id="af4da-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-211">'Identity'</span></span>
- <span data-ttu-id="af4da-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-213">'Razor'</span></span>
- <span data-ttu-id="af4da-214">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-215">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-216">'Blazor'</span></span>
- <span data-ttu-id="af4da-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-217">'Identity'</span></span>
- <span data-ttu-id="af4da-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-219">'Razor'</span></span>
- <span data-ttu-id="af4da-220">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-221">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-222">'Blazor'</span></span>
- <span data-ttu-id="af4da-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-223">'Identity'</span></span>
- <span data-ttu-id="af4da-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-225">'Razor'</span></span>
- <span data-ttu-id="af4da-226">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-227">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-228">'Blazor'</span></span>
- <span data-ttu-id="af4da-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-229">'Identity'</span></span>
- <span data-ttu-id="af4da-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-231">'Razor'</span></span>
- <span data-ttu-id="af4da-232">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-233">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-234">'Blazor'</span></span>
- <span data-ttu-id="af4da-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-235">'Identity'</span></span>
- <span data-ttu-id="af4da-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-237">'Razor'</span></span>
- <span data-ttu-id="af4da-238">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-239">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-240">'Blazor'</span></span>
- <span data-ttu-id="af4da-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-241">'Identity'</span></span>
- <span data-ttu-id="af4da-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-243">'Razor'</span></span>
- <span data-ttu-id="af4da-244">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-245">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-246">'Blazor'</span></span>
- <span data-ttu-id="af4da-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-247">'Identity'</span></span>
- <span data-ttu-id="af4da-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-249">'Razor'</span></span>
- <span data-ttu-id="af4da-250">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-251">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-252">'Blazor'</span></span>
- <span data-ttu-id="af4da-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-253">'Identity'</span></span>
- <span data-ttu-id="af4da-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-255">'Razor'</span></span>
- <span data-ttu-id="af4da-256">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-257">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-258">'Blazor'</span></span>
- <span data-ttu-id="af4da-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-259">'Identity'</span></span>
- <span data-ttu-id="af4da-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-261">'Razor'</span></span>
- <span data-ttu-id="af4da-262">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-263">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-264">'Blazor'</span></span>
- <span data-ttu-id="af4da-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-265">'Identity'</span></span>
- <span data-ttu-id="af4da-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-267">'Razor'</span></span>
- <span data-ttu-id="af4da-268">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-269">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-270">'Blazor'</span></span>
- <span data-ttu-id="af4da-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-271">'Identity'</span></span>
- <span data-ttu-id="af4da-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-273">'Razor'</span></span>
- <span data-ttu-id="af4da-274">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-275">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-276">'Blazor'</span></span>
- <span data-ttu-id="af4da-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-277">'Identity'</span></span>
- <span data-ttu-id="af4da-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-279">'Razor'</span></span>
- <span data-ttu-id="af4da-280">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-281">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-282">'Blazor'</span></span>
- <span data-ttu-id="af4da-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-283">'Identity'</span></span>
- <span data-ttu-id="af4da-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-285">'Razor'</span></span>
- <span data-ttu-id="af4da-286">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-287">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-288">'Blazor'</span></span>
- <span data-ttu-id="af4da-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-289">'Identity'</span></span>
- <span data-ttu-id="af4da-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-291">'Razor'</span></span>
- <span data-ttu-id="af4da-292">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-293">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-294">'Blazor'</span></span>
- <span data-ttu-id="af4da-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-295">'Identity'</span></span>
- <span data-ttu-id="af4da-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-297">'Razor'</span></span>
- <span data-ttu-id="af4da-298">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-298">'SignalR' uid:</span></span> 

<span data-ttu-id="af4da-299">-------------------------------- | Заголовок---: Автор: описание: Моникерранже: MS. author: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-300">'Blazor'</span></span>
- <span data-ttu-id="af4da-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-301">'Identity'</span></span>
- <span data-ttu-id="af4da-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-303">'Razor'</span></span>
- <span data-ttu-id="af4da-304">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-304">'SignalR' uid:</span></span> 

<span data-ttu-id="af4da-305">--- | | [общедоступная](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Ответ может храниться в кэше.</span><span class="sxs-lookup"><span data-stu-id="af4da-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="af4da-306">| | [частный](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | Ответ не должен храниться в общем кэше.</span><span class="sxs-lookup"><span data-stu-id="af4da-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="af4da-307">Частный кэш может хранить и повторно использовать ответ.</span><span class="sxs-lookup"><span data-stu-id="af4da-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="af4da-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | Клиент не принимает ответ, возраст которого превышает указанное число секунд.</span><span class="sxs-lookup"><span data-stu-id="af4da-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="af4da-309">Примеры: `max-age=60` (60 секунд), `max-age=2592000` (1 месяц) | | [без кэша](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **В запросах**: кэш не должен использовать сохраненный ответ для удовлетворения запроса.</span><span class="sxs-lookup"><span data-stu-id="af4da-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="af4da-310">Сервер источника повторно создает ответ для клиента, и по промежуточного слоя обновляет сохраненный ответ в своем кэше.</span><span class="sxs-lookup"><span data-stu-id="af4da-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="af4da-311">**В ответах**: ответ не должен использоваться для последующего запроса без проверки на сервере-источнике.</span><span class="sxs-lookup"><span data-stu-id="af4da-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="af4da-312">| | [без магазина](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **В запросах**: кэш не должен сохранять запрос.</span><span class="sxs-lookup"><span data-stu-id="af4da-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="af4da-313">**В**ответах: кэш не должен хранить какую-либо часть ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="af4da-314">Другие заголовки кэша, которые играют роль в кэшировании, показаны в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="af4da-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="af4da-315">Заголовок</span><span class="sxs-lookup"><span data-stu-id="af4da-315">Header</span></span>                                                     | <span data-ttu-id="af4da-316">Функция</span><span class="sxs-lookup"><span data-stu-id="af4da-316">Function</span></span> |
| ---
<span data-ttu-id="af4da-317">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-318">'Blazor'</span></span>
- <span data-ttu-id="af4da-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-319">'Identity'</span></span>
- <span data-ttu-id="af4da-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-321">'Razor'</span></span>
- <span data-ttu-id="af4da-322">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-323">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-324">'Blazor'</span></span>
- <span data-ttu-id="af4da-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-325">'Identity'</span></span>
- <span data-ttu-id="af4da-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-327">'Razor'</span></span>
- <span data-ttu-id="af4da-328">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-329">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-330">'Blazor'</span></span>
- <span data-ttu-id="af4da-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-331">'Identity'</span></span>
- <span data-ttu-id="af4da-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-333">'Razor'</span></span>
- <span data-ttu-id="af4da-334">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-335">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-336">'Blazor'</span></span>
- <span data-ttu-id="af4da-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-337">'Identity'</span></span>
- <span data-ttu-id="af4da-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-339">'Razor'</span></span>
- <span data-ttu-id="af4da-340">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-341">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-342">'Blazor'</span></span>
- <span data-ttu-id="af4da-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-343">'Identity'</span></span>
- <span data-ttu-id="af4da-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-345">'Razor'</span></span>
- <span data-ttu-id="af4da-346">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-347">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-348">'Blazor'</span></span>
- <span data-ttu-id="af4da-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-349">'Identity'</span></span>
- <span data-ttu-id="af4da-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-351">'Razor'</span></span>
- <span data-ttu-id="af4da-352">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-353">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-354">'Blazor'</span></span>
- <span data-ttu-id="af4da-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-355">'Identity'</span></span>
- <span data-ttu-id="af4da-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-357">'Razor'</span></span>
- <span data-ttu-id="af4da-358">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-359">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-360">'Blazor'</span></span>
- <span data-ttu-id="af4da-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-361">'Identity'</span></span>
- <span data-ttu-id="af4da-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-363">'Razor'</span></span>
- <span data-ttu-id="af4da-364">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-365">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-366">'Blazor'</span></span>
- <span data-ttu-id="af4da-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-367">'Identity'</span></span>
- <span data-ttu-id="af4da-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-369">'Razor'</span></span>
- <span data-ttu-id="af4da-370">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-371">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-372">'Blazor'</span></span>
- <span data-ttu-id="af4da-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-373">'Identity'</span></span>
- <span data-ttu-id="af4da-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-375">'Razor'</span></span>
- <span data-ttu-id="af4da-376">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-377">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-378">'Blazor'</span></span>
- <span data-ttu-id="af4da-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-379">'Identity'</span></span>
- <span data-ttu-id="af4da-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-381">'Razor'</span></span>
- <span data-ttu-id="af4da-382">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-383">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-384">'Blazor'</span></span>
- <span data-ttu-id="af4da-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-385">'Identity'</span></span>
- <span data-ttu-id="af4da-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-387">'Razor'</span></span>
- <span data-ttu-id="af4da-388">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-389">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-390">'Blazor'</span></span>
- <span data-ttu-id="af4da-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-391">'Identity'</span></span>
- <span data-ttu-id="af4da-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-393">'Razor'</span></span>
- <span data-ttu-id="af4da-394">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-395">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-396">'Blazor'</span></span>
- <span data-ttu-id="af4da-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-397">'Identity'</span></span>
- <span data-ttu-id="af4da-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-399">'Razor'</span></span>
- <span data-ttu-id="af4da-400">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-401">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-402">'Blazor'</span></span>
- <span data-ttu-id="af4da-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-403">'Identity'</span></span>
- <span data-ttu-id="af4da-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-405">'Razor'</span></span>
- <span data-ttu-id="af4da-406">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-407">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-408">'Blazor'</span></span>
- <span data-ttu-id="af4da-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-409">'Identity'</span></span>
- <span data-ttu-id="af4da-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-411">'Razor'</span></span>
- <span data-ttu-id="af4da-412">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-413">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-414">'Blazor'</span></span>
- <span data-ttu-id="af4da-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-415">'Identity'</span></span>
- <span data-ttu-id="af4da-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-417">'Razor'</span></span>
- <span data-ttu-id="af4da-418">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-419">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-420">'Blazor'</span></span>
- <span data-ttu-id="af4da-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-421">'Identity'</span></span>
- <span data-ttu-id="af4da-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-423">'Razor'</span></span>
- <span data-ttu-id="af4da-424">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-425">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-426">'Blazor'</span></span>
- <span data-ttu-id="af4da-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-427">'Identity'</span></span>
- <span data-ttu-id="af4da-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-429">'Razor'</span></span>
- <span data-ttu-id="af4da-430">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-431">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-432">'Blazor'</span></span>
- <span data-ttu-id="af4da-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-433">'Identity'</span></span>
- <span data-ttu-id="af4da-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-435">'Razor'</span></span>
- <span data-ttu-id="af4da-436">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-437">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-438">'Blazor'</span></span>
- <span data-ttu-id="af4da-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-439">'Identity'</span></span>
- <span data-ttu-id="af4da-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-441">'Razor'</span></span>
- <span data-ttu-id="af4da-442">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-443">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-444">'Blazor'</span></span>
- <span data-ttu-id="af4da-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-445">'Identity'</span></span>
- <span data-ttu-id="af4da-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-447">'Razor'</span></span>
- <span data-ttu-id="af4da-448">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-449">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-450">'Blazor'</span></span>
- <span data-ttu-id="af4da-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-451">'Identity'</span></span>
- <span data-ttu-id="af4da-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-453">'Razor'</span></span>
- <span data-ttu-id="af4da-454">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-455">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-456">'Blazor'</span></span>
- <span data-ttu-id="af4da-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-457">'Identity'</span></span>
- <span data-ttu-id="af4da-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-459">'Razor'</span></span>
- <span data-ttu-id="af4da-460">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-461">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-462">'Blazor'</span></span>
- <span data-ttu-id="af4da-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-463">'Identity'</span></span>
- <span data-ttu-id="af4da-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-465">'Razor'</span></span>
- <span data-ttu-id="af4da-466">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-467">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-468">'Blazor'</span></span>
- <span data-ttu-id="af4da-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-469">'Identity'</span></span>
- <span data-ttu-id="af4da-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-471">'Razor'</span></span>
- <span data-ttu-id="af4da-472">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-473">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-474">'Blazor'</span></span>
- <span data-ttu-id="af4da-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-475">'Identity'</span></span>
- <span data-ttu-id="af4da-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-477">'Razor'</span></span>
- <span data-ttu-id="af4da-478">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-478">'SignalR' uid:</span></span> 

<span data-ttu-id="af4da-479">----------------------------- | Заголовок---: Автор: описание: Моникерранже: MS. author: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-480">'Blazor'</span></span>
- <span data-ttu-id="af4da-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-481">'Identity'</span></span>
- <span data-ttu-id="af4da-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-483">'Razor'</span></span>
- <span data-ttu-id="af4da-484">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-485">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-486">'Blazor'</span></span>
- <span data-ttu-id="af4da-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-487">'Identity'</span></span>
- <span data-ttu-id="af4da-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-489">'Razor'</span></span>
- <span data-ttu-id="af4da-490">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-490">'SignalR' uid:</span></span> 

<span data-ttu-id="af4da-491">---- | | [Возраст](https://tools.ietf.org/html/rfc7234#section-5.1) | Оценка количества времени в секундах с момента создания или успешной проверки ответа на сервере источника.</span><span class="sxs-lookup"><span data-stu-id="af4da-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="af4da-492">| | [Срок действия истекает](https://tools.ietf.org/html/rfc7234#section-5.3) | Время, после которого ответ считается устаревшим.</span><span class="sxs-lookup"><span data-stu-id="af4da-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="af4da-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Существует для обеспечения обратной совместимости с кэшами HTTP/1.0 для настройки `no-cache` поведения.</span><span class="sxs-lookup"><span data-stu-id="af4da-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="af4da-494">Если `Cache-Control` заголовок имеется, `Pragma` заголовок игнорируется.</span><span class="sxs-lookup"><span data-stu-id="af4da-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="af4da-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Указывает, что кэшированный ответ не должен отправляться, если все `Vary` поля заголовка не совпадают как в исходном запросе, так и в новом запросе кэшированного ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="af4da-496">Директивы управления кэшем запросов для кэширования на основе HTTP</span><span class="sxs-lookup"><span data-stu-id="af4da-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="af4da-497">[Спецификация кэширования HTTP 1,1 для заголовка Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) требует, чтобы кэш учитывал допустимый `Cache-Control` заголовок, отправленный клиентом.</span><span class="sxs-lookup"><span data-stu-id="af4da-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="af4da-498">Клиент может выполнять запросы со `no-cache` значением заголовка и принудительно выдавать серверу запрос на создание нового ответа для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="af4da-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="af4da-499">Всегда учитывать `Cache-Control` заголовки запросов клиентов имеет смысл, если рассматривать цель кэширования HTTP.</span><span class="sxs-lookup"><span data-stu-id="af4da-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="af4da-500">В официальной спецификации кэширование предназначено для уменьшения задержки и нагрузки на сети для удовлетворения запросов в сети клиентов, прокси и серверов.</span><span class="sxs-lookup"><span data-stu-id="af4da-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="af4da-501">Это не обязательно способ управления нагрузкой на сервере-источнике.</span><span class="sxs-lookup"><span data-stu-id="af4da-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="af4da-502">При использовании по [промежуточного слоя для кэширования ответа](xref:performance/caching/middleware) разработчик не управляет этим поведением кэширования, поскольку по промежуточного слоя соответствует официальной спецификации кэширования.</span><span class="sxs-lookup"><span data-stu-id="af4da-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="af4da-503">[Запланированные улучшения по промежуточного слоя](https://github.com/dotnet/AspNetCore/issues/2612) позволяют настроить по промежуточного слоя на игнорирование заголовка запроса `Cache-Control` при принятии решения о выполнении кэшированного ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="af4da-504">Запланированные улучшения дают возможность лучше управлять нагрузкой сервера.</span><span class="sxs-lookup"><span data-stu-id="af4da-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="af4da-505">Другая технология кэширования в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af4da-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="af4da-506">Кэширование в памяти</span><span class="sxs-lookup"><span data-stu-id="af4da-506">In-memory caching</span></span>

<span data-ttu-id="af4da-507">В процессе кэширования в памяти используется память сервера для хранения кэшированных данных.</span><span class="sxs-lookup"><span data-stu-id="af4da-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="af4da-508">Этот тип кэширования подходит для одного сервера или нескольких серверов, использующих *закрепленные сеансы*.</span><span class="sxs-lookup"><span data-stu-id="af4da-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="af4da-509">Прикрепленные сеансы означает, что запросы от клиента всегда направляются на один и тот же сервер для обработки.</span><span class="sxs-lookup"><span data-stu-id="af4da-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="af4da-510">Для получения дополнительной информации см. <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="af4da-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="af4da-511">Распределенный кэш</span><span class="sxs-lookup"><span data-stu-id="af4da-511">Distributed Cache</span></span>

<span data-ttu-id="af4da-512">Используйте распределенный кэш для хранения данных в памяти, когда приложение размещается в облаке или ферме серверов.</span><span class="sxs-lookup"><span data-stu-id="af4da-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="af4da-513">Кэш является общим для серверов, обрабатывающих запросы.</span><span class="sxs-lookup"><span data-stu-id="af4da-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="af4da-514">Клиент может отправить запрос, который обрабатывается любым сервером в группе, если доступны кэшированные данные для клиента.</span><span class="sxs-lookup"><span data-stu-id="af4da-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="af4da-515">ASP.NET Core работает с распределенными кэшами SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)и [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="af4da-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="af4da-516">Для получения дополнительной информации см. <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="af4da-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="af4da-517">Вспомогательная функция тега кэша</span><span class="sxs-lookup"><span data-stu-id="af4da-517">Cache Tag Helper</span></span>

<span data-ttu-id="af4da-518">Кэширование содержимого из представления MVC или Razor страницы с вспомогательной функцией тега кэша.</span><span class="sxs-lookup"><span data-stu-id="af4da-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="af4da-519">Вспомогательная функция тега кэша использует кэширование в памяти для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="af4da-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="af4da-520">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="af4da-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="af4da-521">Вспомогательная функция тега распределенного кэша</span><span class="sxs-lookup"><span data-stu-id="af4da-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="af4da-522">Кэширование содержимого из представления MVC или Razor страницы в сценариях распределенного облака или веб-фермы с помощью вспомогательной функции тега распределенного кэша.</span><span class="sxs-lookup"><span data-stu-id="af4da-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="af4da-523">Вспомогательная функция тега распределенного кэша использует SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)или [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) для хранения данных.</span><span class="sxs-lookup"><span data-stu-id="af4da-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="af4da-524">Для получения дополнительной информации см. <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="af4da-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="af4da-525">Атрибут Респонсекаче</span><span class="sxs-lookup"><span data-stu-id="af4da-525">ResponseCache attribute</span></span>

<span data-ttu-id="af4da-526"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Указывает параметры, необходимые для настройки соответствующих заголовков в кэшировании ответов.</span><span class="sxs-lookup"><span data-stu-id="af4da-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="af4da-527">Отключите кэширование для содержимого, содержащего сведения для клиентов, прошедших проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="af4da-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="af4da-528">Кэширование следует включать только для содержимого, которое не изменяется в зависимости от удостоверения пользователя или от того, вошел ли пользователь в систему.</span><span class="sxs-lookup"><span data-stu-id="af4da-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="af4da-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>изменяет сохраненный ответ на значения заданного списка ключей запроса.</span><span class="sxs-lookup"><span data-stu-id="af4da-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="af4da-530">Если указано одно значение `*` , по промежуточного слоя изменяет ответы всеми параметрами строки запроса.</span><span class="sxs-lookup"><span data-stu-id="af4da-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="af4da-531">Для установки свойства необходимо включить по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware) <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="af4da-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="af4da-532">В противном случае выдается исключение времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="af4da-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="af4da-533">Отсутствует соответствующий заголовок HTTP для <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Свойства.</span><span class="sxs-lookup"><span data-stu-id="af4da-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="af4da-534">Свойство является компонентом HTTP, обрабатываемым по промежуточного слоя кэширования ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="af4da-535">Чтобы по промежуточного слоя обслуживать кэшированный ответ, строка запроса и значение строки запроса должны соответствовать предыдущему запросу.</span><span class="sxs-lookup"><span data-stu-id="af4da-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="af4da-536">Например, рассмотрим последовательность запросов и результатов, показанных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="af4da-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="af4da-537">Запрос</span><span class="sxs-lookup"><span data-stu-id="af4da-537">Request</span></span>                          | <span data-ttu-id="af4da-538">Результат</span><span class="sxs-lookup"><span data-stu-id="af4da-538">Result</span></span>                    |
| ---
<span data-ttu-id="af4da-539">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-540">'Blazor'</span></span>
- <span data-ttu-id="af4da-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-541">'Identity'</span></span>
- <span data-ttu-id="af4da-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-543">'Razor'</span></span>
- <span data-ttu-id="af4da-544">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-545">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-546">'Blazor'</span></span>
- <span data-ttu-id="af4da-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-547">'Identity'</span></span>
- <span data-ttu-id="af4da-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-549">'Razor'</span></span>
- <span data-ttu-id="af4da-550">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-551">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-552">'Blazor'</span></span>
- <span data-ttu-id="af4da-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-553">'Identity'</span></span>
- <span data-ttu-id="af4da-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-555">'Razor'</span></span>
- <span data-ttu-id="af4da-556">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-557">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-558">'Blazor'</span></span>
- <span data-ttu-id="af4da-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-559">'Identity'</span></span>
- <span data-ttu-id="af4da-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-561">'Razor'</span></span>
- <span data-ttu-id="af4da-562">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-563">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-564">'Blazor'</span></span>
- <span data-ttu-id="af4da-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-565">'Identity'</span></span>
- <span data-ttu-id="af4da-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-567">'Razor'</span></span>
- <span data-ttu-id="af4da-568">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-569">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-570">'Blazor'</span></span>
- <span data-ttu-id="af4da-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-571">'Identity'</span></span>
- <span data-ttu-id="af4da-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-573">'Razor'</span></span>
- <span data-ttu-id="af4da-574">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-575">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-576">'Blazor'</span></span>
- <span data-ttu-id="af4da-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-577">'Identity'</span></span>
- <span data-ttu-id="af4da-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-579">'Razor'</span></span>
- <span data-ttu-id="af4da-580">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-581">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-582">'Blazor'</span></span>
- <span data-ttu-id="af4da-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-583">'Identity'</span></span>
- <span data-ttu-id="af4da-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-585">'Razor'</span></span>
- <span data-ttu-id="af4da-586">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-587">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-588">'Blazor'</span></span>
- <span data-ttu-id="af4da-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-589">'Identity'</span></span>
- <span data-ttu-id="af4da-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-591">'Razor'</span></span>
- <span data-ttu-id="af4da-592">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-593">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-594">'Blazor'</span></span>
- <span data-ttu-id="af4da-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-595">'Identity'</span></span>
- <span data-ttu-id="af4da-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-597">'Razor'</span></span>
- <span data-ttu-id="af4da-598">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-599">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-600">'Blazor'</span></span>
- <span data-ttu-id="af4da-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-601">'Identity'</span></span>
- <span data-ttu-id="af4da-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-603">'Razor'</span></span>
- <span data-ttu-id="af4da-604">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-605">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-606">'Blazor'</span></span>
- <span data-ttu-id="af4da-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-607">'Identity'</span></span>
- <span data-ttu-id="af4da-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-609">'Razor'</span></span>
- <span data-ttu-id="af4da-610">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-611">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-612">'Blazor'</span></span>
- <span data-ttu-id="af4da-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-613">'Identity'</span></span>
- <span data-ttu-id="af4da-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-615">'Razor'</span></span>
- <span data-ttu-id="af4da-616">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-617">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-618">'Blazor'</span></span>
- <span data-ttu-id="af4da-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-619">'Identity'</span></span>
- <span data-ttu-id="af4da-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-621">'Razor'</span></span>
- <span data-ttu-id="af4da-622">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-622">'SignalR' uid:</span></span> 

<span data-ttu-id="af4da-623">---------------- | Заголовок---: Автор: описание: Моникерранже: MS. author: MS. Дата: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-624">'Blazor'</span></span>
- <span data-ttu-id="af4da-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-625">'Identity'</span></span>
- <span data-ttu-id="af4da-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-627">'Razor'</span></span>
- <span data-ttu-id="af4da-628">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-629">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-630">'Blazor'</span></span>
- <span data-ttu-id="af4da-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-631">'Identity'</span></span>
- <span data-ttu-id="af4da-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-633">'Razor'</span></span>
- <span data-ttu-id="af4da-634">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-635">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-636">'Blazor'</span></span>
- <span data-ttu-id="af4da-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-637">'Identity'</span></span>
- <span data-ttu-id="af4da-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-639">'Razor'</span></span>
- <span data-ttu-id="af4da-640">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-641">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-642">'Blazor'</span></span>
- <span data-ttu-id="af4da-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-643">'Identity'</span></span>
- <span data-ttu-id="af4da-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-645">'Razor'</span></span>
- <span data-ttu-id="af4da-646">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-647">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-648">'Blazor'</span></span>
- <span data-ttu-id="af4da-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-649">'Identity'</span></span>
- <span data-ttu-id="af4da-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-651">'Razor'</span></span>
- <span data-ttu-id="af4da-652">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-653">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-654">'Blazor'</span></span>
- <span data-ttu-id="af4da-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-655">'Identity'</span></span>
- <span data-ttu-id="af4da-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-657">'Razor'</span></span>
- <span data-ttu-id="af4da-658">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-659">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-660">'Blazor'</span></span>
- <span data-ttu-id="af4da-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-661">'Identity'</span></span>
- <span data-ttu-id="af4da-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-663">'Razor'</span></span>
- <span data-ttu-id="af4da-664">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-665">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-666">'Blazor'</span></span>
- <span data-ttu-id="af4da-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-667">'Identity'</span></span>
- <span data-ttu-id="af4da-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-669">'Razor'</span></span>
- <span data-ttu-id="af4da-670">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-671">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-672">'Blazor'</span></span>
- <span data-ttu-id="af4da-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-673">'Identity'</span></span>
- <span data-ttu-id="af4da-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-675">'Razor'</span></span>
- <span data-ttu-id="af4da-676">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="af4da-677">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="af4da-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="af4da-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="af4da-678">'Blazor'</span></span>
- <span data-ttu-id="af4da-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="af4da-679">'Identity'</span></span>
- <span data-ttu-id="af4da-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="af4da-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="af4da-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="af4da-681">'Razor'</span></span>
- <span data-ttu-id="af4da-682">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="af4da-682">'SignalR' uid:</span></span> 

<span data-ttu-id="af4da-683">------------- | | `http://example.com?key1=value1` | Возвращается с сервера.</span><span class="sxs-lookup"><span data-stu-id="af4da-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="af4da-684">| | `http://example.com?key1=value1` | Возвращается из промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="af4da-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="af4da-685">| | `http://example.com?key1=value2` | Возвращается с сервера.</span><span class="sxs-lookup"><span data-stu-id="af4da-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="af4da-686">Первый запрос возвращается сервером и кэшируется по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="af4da-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="af4da-687">Второй запрос возвращается по промежуточного слоя, так как строка запроса совпадает с предыдущим запросом.</span><span class="sxs-lookup"><span data-stu-id="af4da-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="af4da-688">Третий запрос не находится в кэше по промежуточного слоя, так как значение строки запроса не соответствует предыдущему запросу.</span><span class="sxs-lookup"><span data-stu-id="af4da-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="af4da-689"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Используется для настройки и создания (через <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> ) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="af4da-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="af4da-690">`ResponseCacheFilter`Компонент выполняет работу по обновлению соответствующих HTTP-заголовков и функций ответа.</span><span class="sxs-lookup"><span data-stu-id="af4da-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="af4da-691">Фильтр:</span><span class="sxs-lookup"><span data-stu-id="af4da-691">The filter:</span></span>

* <span data-ttu-id="af4da-692">Удаляет все существующие заголовки для `Vary` , `Cache-Control` и `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="af4da-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="af4da-693">Записывает соответствующие заголовки на основе свойств, заданных в <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="af4da-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="af4da-694">Обновляет компонент кэширования ответов HTTP, если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> задано значение.</span><span class="sxs-lookup"><span data-stu-id="af4da-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="af4da-695">Меняющие</span><span class="sxs-lookup"><span data-stu-id="af4da-695">Vary</span></span>

<span data-ttu-id="af4da-696">Этот заголовок записывается только в том случае, если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> задано свойство.</span><span class="sxs-lookup"><span data-stu-id="af4da-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="af4da-697">Свойство, для которого задано `Vary` значение свойства.</span><span class="sxs-lookup"><span data-stu-id="af4da-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="af4da-698">В следующем примере используется <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> свойство:</span><span class="sxs-lookup"><span data-stu-id="af4da-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="af4da-699">С помощью примера приложения просмотрите заголовки ответа с помощью сетевых средств браузера.</span><span class="sxs-lookup"><span data-stu-id="af4da-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="af4da-700">Следующие заголовки ответа отправляются с ответом страницы Cache1:</span><span class="sxs-lookup"><span data-stu-id="af4da-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="af4da-701">\ Store и Location. None</span><span class="sxs-lookup"><span data-stu-id="af4da-701">NoStore and Location.None</span></span>

<span data-ttu-id="af4da-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>переопределяет большинство других свойств.</span><span class="sxs-lookup"><span data-stu-id="af4da-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="af4da-703">Если для этого свойства задано значение `true` , `Cache-Control` заголовок устанавливается в значение `no-store` .</span><span class="sxs-lookup"><span data-stu-id="af4da-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="af4da-704">Если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> параметр имеет значение `None` :</span><span class="sxs-lookup"><span data-stu-id="af4da-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="af4da-705">`Cache-Control` задан как `no-store,no-cache`.</span><span class="sxs-lookup"><span data-stu-id="af4da-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="af4da-706">`Pragma` задан как `no-cache`.</span><span class="sxs-lookup"><span data-stu-id="af4da-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="af4da-707">Если <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> параметр имеет значение `false` <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> , а имеет значение, и, то `None` `Cache-Control` `Pragma` для свойства задаются значения `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="af4da-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="af4da-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>обычно имеет значение `true` для страниц ошибок.</span><span class="sxs-lookup"><span data-stu-id="af4da-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="af4da-709">Страница Cache2 в примере приложения создает заголовки ответа, указывающие клиенту не сохранять ответ.</span><span class="sxs-lookup"><span data-stu-id="af4da-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="af4da-710">Пример приложения возвращает страницу Cache2 со следующими заголовками:</span><span class="sxs-lookup"><span data-stu-id="af4da-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="af4da-711">Расположение и длительность</span><span class="sxs-lookup"><span data-stu-id="af4da-711">Location and Duration</span></span>

<span data-ttu-id="af4da-712">Чтобы включить кэширование, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> необходимо задать положительное значение, которое <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> должно быть либо `Any` (по умолчанию), либо `Client` .</span><span class="sxs-lookup"><span data-stu-id="af4da-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="af4da-713">Платформа задает `Cache-Control` для заголовка значение Location, за которым следует `max-age` ответ.</span><span class="sxs-lookup"><span data-stu-id="af4da-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="af4da-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>параметры `Any` и `Client` транслируются в `Cache-Control` значения заголовков `public` и `private` соответственно.</span><span class="sxs-lookup"><span data-stu-id="af4da-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="af4da-715">Как указано в разделе "не [Store" и "Location. None](#nostore-and-locationnone) ", параметр <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None` задает для обоих `Cache-Control` `Pragma` заголовков и значение `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="af4da-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="af4da-716">`Location.Any`( `Cache-Control` значение `public` ) указывает, что *клиент или любой промежуточный прокси-сервер* может кэшировать значение, включая по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="af4da-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="af4da-717">`Location.Client`( `Cache-Control` значение `private` ) указывает, что *только клиент* может кэшировать значение.</span><span class="sxs-lookup"><span data-stu-id="af4da-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="af4da-718">Ни один промежуточный кэш не должен кэшировать значение, включая по [промежуточного слоя кэширования ответа](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="af4da-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="af4da-719">Заголовки управления кэшем просто предоставляют рекомендации клиентам и промежуточным прокси-серверам, когда и как кэшировать ответы.</span><span class="sxs-lookup"><span data-stu-id="af4da-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="af4da-720">Нет никакой гарантии, что клиенты и прокси-серверы будут учитывать [спецификацию кэширования HTTP 1,1](https://tools.ietf.org/html/rfc7234).</span><span class="sxs-lookup"><span data-stu-id="af4da-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="af4da-721">[Промежуточное расположение кэширования ответов](xref:performance/caching/middleware) всегда соответствует правилам кэширования, указанным в спецификации.</span><span class="sxs-lookup"><span data-stu-id="af4da-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="af4da-722">В следующем примере показана модель страницы Cache3 из примера приложения и заголовки, созданные с помощью параметра <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> и значения по умолчанию <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> :</span><span class="sxs-lookup"><span data-stu-id="af4da-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="af4da-723">Пример приложения возвращает страницу Cache3 со следующим заголовком:</span><span class="sxs-lookup"><span data-stu-id="af4da-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="af4da-724">Профили кэша</span><span class="sxs-lookup"><span data-stu-id="af4da-724">Cache profiles</span></span>

<span data-ttu-id="af4da-725">Вместо дублирования параметров кэша ответов во многих атрибутах действия контроллера, профили кэша можно настроить в качестве параметров при настройке MVC и Razor страниц в `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="af4da-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="af4da-726">Значения, найденные в указанном профиле кэша, используются по умолчанию <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> и переопределяются любыми свойствами, заданными в атрибуте.</span><span class="sxs-lookup"><span data-stu-id="af4da-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="af4da-727">Настройка профиля кэша.</span><span class="sxs-lookup"><span data-stu-id="af4da-727">Set up a cache profile.</span></span> <span data-ttu-id="af4da-728">В следующем примере показан 30-секундный профиль кэша в примере приложения `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="af4da-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="af4da-729">Модель страницы Cache4 в примере приложения ссылается на `Default30` профиль кэша:</span><span class="sxs-lookup"><span data-stu-id="af4da-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="af4da-730"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Можно применить к:</span><span class="sxs-lookup"><span data-stu-id="af4da-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="af4da-731">Обработчики страниц (классы): атрибуты не могут применяться к методам обработчика.</span><span class="sxs-lookup"><span data-stu-id="af4da-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="af4da-732">Контроллеры MVC (классы).</span><span class="sxs-lookup"><span data-stu-id="af4da-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="af4da-733">Действия MVC (методы): атрибуты уровня метода переопределяют параметры, указанные в атрибутах уровня класса.</span><span class="sxs-lookup"><span data-stu-id="af4da-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="af4da-734">Результирующий заголовок, примененный к ответу страницы Cache4 в `Default30` профиле кэша:</span><span class="sxs-lookup"><span data-stu-id="af4da-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="af4da-735">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="af4da-735">Additional resources</span></span>

* [<span data-ttu-id="af4da-736">Хранение ответов в кэшах</span><span class="sxs-lookup"><span data-stu-id="af4da-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="af4da-737">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="af4da-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
