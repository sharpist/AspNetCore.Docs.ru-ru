---
<span data-ttu-id="4d045-101">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-102">'Blazor'</span></span>
- <span data-ttu-id="4d045-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-103">'Identity'</span></span>
- <span data-ttu-id="4d045-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-105">'Razor'</span></span>
- <span data-ttu-id="4d045-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-106">'SignalR' uid:</span></span> 

---
# <a name="compare-grpc-services-with-http-apis"></a><span data-ttu-id="4d045-107">Сравнение служб gRPC с API-интерфейсами HTTP</span><span class="sxs-lookup"><span data-stu-id="4d045-107">Compare gRPC services with HTTP APIs</span></span>

<span data-ttu-id="4d045-108">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="4d045-108">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4d045-109">В этой статье объясняется, чем [службы gRPC](https://grpc.io/docs/guides/) отличаются от API HTTP с JSON (включая [веб-API](xref:web-api/index) ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="4d045-109">This article explains how [gRPC services](https://grpc.io/docs/guides/) compare to HTTP APIs with JSON (including ASP.NET Core [web APIs](xref:web-api/index)).</span></span> <span data-ttu-id="4d045-110">Важно правильно выбрать технологию для предоставления API для вашего приложения, и gRPC предлагает уникальные преимущества по сравнению с API HTTP.</span><span class="sxs-lookup"><span data-stu-id="4d045-110">The technology used to provide an API for your app is an important choice, and gRPC offers unique benefits compared to HTTP APIs.</span></span> <span data-ttu-id="4d045-111">В этой статье обсуждаются сильные и слабые стороны gRPC и приводятся рекомендации по использованию gRPC вместо других технологий.</span><span class="sxs-lookup"><span data-stu-id="4d045-111">This article discusses the strengths and weaknesses of gRPC and recommends scenarios for using gRPC over other technologies.</span></span>

## <a name="high-level-comparison"></a><span data-ttu-id="4d045-112">Общее сравнение</span><span class="sxs-lookup"><span data-stu-id="4d045-112">High-level comparison</span></span>

<span data-ttu-id="4d045-113">В следующей таблице представлено общее сравнение функций gRPC и API HTTP с JSON.</span><span class="sxs-lookup"><span data-stu-id="4d045-113">The following table offers a high-level comparison of features between gRPC and HTTP APIs with JSON.</span></span>

| <span data-ttu-id="4d045-114">Функция</span><span class="sxs-lookup"><span data-stu-id="4d045-114">Feature</span></span>          | <span data-ttu-id="4d045-115">gRPC</span><span class="sxs-lookup"><span data-stu-id="4d045-115">gRPC</span></span>                                               | <span data-ttu-id="4d045-116">API HTTP с JSON</span><span class="sxs-lookup"><span data-stu-id="4d045-116">HTTP APIs with JSON</span></span>           |
| ---
<span data-ttu-id="4d045-117">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-117">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-118">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-118">'Blazor'</span></span>
- <span data-ttu-id="4d045-119">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-119">'Identity'</span></span>
- <span data-ttu-id="4d045-120">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-120">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-121">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-121">'Razor'</span></span>
- <span data-ttu-id="4d045-122">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-122">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-123">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-123">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-124">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-124">'Blazor'</span></span>
- <span data-ttu-id="4d045-125">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-125">'Identity'</span></span>
- <span data-ttu-id="4d045-126">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-126">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-127">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-127">'Razor'</span></span>
- <span data-ttu-id="4d045-128">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-128">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-129">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-129">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-130">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-130">'Blazor'</span></span>
- <span data-ttu-id="4d045-131">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-131">'Identity'</span></span>
- <span data-ttu-id="4d045-132">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-132">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-133">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-133">'Razor'</span></span>
- <span data-ttu-id="4d045-134">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-134">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-135">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-135">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-136">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-136">'Blazor'</span></span>
- <span data-ttu-id="4d045-137">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-137">'Identity'</span></span>
- <span data-ttu-id="4d045-138">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-138">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-139">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-139">'Razor'</span></span>
- <span data-ttu-id="4d045-140">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-140">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-141">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-141">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-142">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-142">'Blazor'</span></span>
- <span data-ttu-id="4d045-143">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-143">'Identity'</span></span>
- <span data-ttu-id="4d045-144">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-144">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-145">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-145">'Razor'</span></span>
- <span data-ttu-id="4d045-146">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-146">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-147">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-147">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-148">'Blazor'</span></span>
- <span data-ttu-id="4d045-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-149">'Identity'</span></span>
- <span data-ttu-id="4d045-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-151">'Razor'</span></span>
- <span data-ttu-id="4d045-152">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-152">'SignalR' uid:</span></span> 

<span data-ttu-id="4d045-153">-------- | --- название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-153">-------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-154">'Blazor'</span></span>
- <span data-ttu-id="4d045-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-155">'Identity'</span></span>
- <span data-ttu-id="4d045-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-157">'Razor'</span></span>
- <span data-ttu-id="4d045-158">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-159">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-159">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-160">'Blazor'</span></span>
- <span data-ttu-id="4d045-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-161">'Identity'</span></span>
- <span data-ttu-id="4d045-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-163">'Razor'</span></span>
- <span data-ttu-id="4d045-164">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-165">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-165">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-166">'Blazor'</span></span>
- <span data-ttu-id="4d045-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-167">'Identity'</span></span>
- <span data-ttu-id="4d045-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-169">'Razor'</span></span>
- <span data-ttu-id="4d045-170">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-171">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-171">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-172">'Blazor'</span></span>
- <span data-ttu-id="4d045-173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-173">'Identity'</span></span>
- <span data-ttu-id="4d045-174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-174">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-175">'Razor'</span></span>
- <span data-ttu-id="4d045-176">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-177">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-177">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-178">'Blazor'</span></span>
- <span data-ttu-id="4d045-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-179">'Identity'</span></span>
- <span data-ttu-id="4d045-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-181">'Razor'</span></span>
- <span data-ttu-id="4d045-182">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-183">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-183">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-184">'Blazor'</span></span>
- <span data-ttu-id="4d045-185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-185">'Identity'</span></span>
- <span data-ttu-id="4d045-186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-186">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-187">'Razor'</span></span>
- <span data-ttu-id="4d045-188">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-189">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-189">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-190">'Blazor'</span></span>
- <span data-ttu-id="4d045-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-191">'Identity'</span></span>
- <span data-ttu-id="4d045-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-193">'Razor'</span></span>
- <span data-ttu-id="4d045-194">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-195">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-195">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-196">'Blazor'</span></span>
- <span data-ttu-id="4d045-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-197">'Identity'</span></span>
- <span data-ttu-id="4d045-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-199">'Razor'</span></span>
- <span data-ttu-id="4d045-200">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-201">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-201">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-202">'Blazor'</span></span>
- <span data-ttu-id="4d045-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-203">'Identity'</span></span>
- <span data-ttu-id="4d045-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-205">'Razor'</span></span>
- <span data-ttu-id="4d045-206">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-207">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-207">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-208">'Blazor'</span></span>
- <span data-ttu-id="4d045-209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-209">'Identity'</span></span>
- <span data-ttu-id="4d045-210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-210">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-211">'Razor'</span></span>
- <span data-ttu-id="4d045-212">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-213">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-213">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-214">'Blazor'</span></span>
- <span data-ttu-id="4d045-215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-215">'Identity'</span></span>
- <span data-ttu-id="4d045-216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-216">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-217">'Razor'</span></span>
- <span data-ttu-id="4d045-218">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-219">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-219">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-220">'Blazor'</span></span>
- <span data-ttu-id="4d045-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-221">'Identity'</span></span>
- <span data-ttu-id="4d045-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-223">'Razor'</span></span>
- <span data-ttu-id="4d045-224">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-225">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-225">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-226">'Blazor'</span></span>
- <span data-ttu-id="4d045-227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-227">'Identity'</span></span>
- <span data-ttu-id="4d045-228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-228">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-229">'Razor'</span></span>
- <span data-ttu-id="4d045-230">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-231">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-231">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-232">'Blazor'</span></span>
- <span data-ttu-id="4d045-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-233">'Identity'</span></span>
- <span data-ttu-id="4d045-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-235">'Razor'</span></span>
- <span data-ttu-id="4d045-236">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-237">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-237">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-238">'Blazor'</span></span>
- <span data-ttu-id="4d045-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-239">'Identity'</span></span>
- <span data-ttu-id="4d045-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-241">'Razor'</span></span>
- <span data-ttu-id="4d045-242">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-243">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-243">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-244">'Blazor'</span></span>
- <span data-ttu-id="4d045-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-245">'Identity'</span></span>
- <span data-ttu-id="4d045-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-247">'Razor'</span></span>
- <span data-ttu-id="4d045-248">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-249">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-249">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-250">'Blazor'</span></span>
- <span data-ttu-id="4d045-251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-251">'Identity'</span></span>
- <span data-ttu-id="4d045-252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-252">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-253">'Razor'</span></span>
- <span data-ttu-id="4d045-254">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-255">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-255">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-256">'Blazor'</span></span>
- <span data-ttu-id="4d045-257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-257">'Identity'</span></span>
- <span data-ttu-id="4d045-258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-258">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-259">'Razor'</span></span>
- <span data-ttu-id="4d045-260">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-261">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-261">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-262">'Blazor'</span></span>
- <span data-ttu-id="4d045-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-263">'Identity'</span></span>
- <span data-ttu-id="4d045-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-265">'Razor'</span></span>
- <span data-ttu-id="4d045-266">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-267">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-267">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-268">'Blazor'</span></span>
- <span data-ttu-id="4d045-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-269">'Identity'</span></span>
- <span data-ttu-id="4d045-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-271">'Razor'</span></span>
- <span data-ttu-id="4d045-272">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-273">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-273">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-274">'Blazor'</span></span>
- <span data-ttu-id="4d045-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-275">'Identity'</span></span>
- <span data-ttu-id="4d045-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-277">'Razor'</span></span>
- <span data-ttu-id="4d045-278">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-279">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-279">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-280">'Blazor'</span></span>
- <span data-ttu-id="4d045-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-281">'Identity'</span></span>
- <span data-ttu-id="4d045-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-283">'Razor'</span></span>
- <span data-ttu-id="4d045-284">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-285">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-285">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-286">'Blazor'</span></span>
- <span data-ttu-id="4d045-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-287">'Identity'</span></span>
- <span data-ttu-id="4d045-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-289">'Razor'</span></span>
- <span data-ttu-id="4d045-290">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-290">'SignalR' uid:</span></span> 

<span data-ttu-id="4d045-291">------------------------- | --- название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-291">------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-292">'Blazor'</span></span>
- <span data-ttu-id="4d045-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-293">'Identity'</span></span>
- <span data-ttu-id="4d045-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-295">'Razor'</span></span>
- <span data-ttu-id="4d045-296">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-297">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-297">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-298">'Blazor'</span></span>
- <span data-ttu-id="4d045-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-299">'Identity'</span></span>
- <span data-ttu-id="4d045-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-301">'Razor'</span></span>
- <span data-ttu-id="4d045-302">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-303">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-303">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-304">'Blazor'</span></span>
- <span data-ttu-id="4d045-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-305">'Identity'</span></span>
- <span data-ttu-id="4d045-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-307">'Razor'</span></span>
- <span data-ttu-id="4d045-308">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-309">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-309">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-310">'Blazor'</span></span>
- <span data-ttu-id="4d045-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-311">'Identity'</span></span>
- <span data-ttu-id="4d045-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-313">'Razor'</span></span>
- <span data-ttu-id="4d045-314">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-315">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-315">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-316">'Blazor'</span></span>
- <span data-ttu-id="4d045-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-317">'Identity'</span></span>
- <span data-ttu-id="4d045-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-319">'Razor'</span></span>
- <span data-ttu-id="4d045-320">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-321">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-321">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-322">'Blazor'</span></span>
- <span data-ttu-id="4d045-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-323">'Identity'</span></span>
- <span data-ttu-id="4d045-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-325">'Razor'</span></span>
- <span data-ttu-id="4d045-326">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-327">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-327">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-328">'Blazor'</span></span>
- <span data-ttu-id="4d045-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-329">'Identity'</span></span>
- <span data-ttu-id="4d045-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-331">'Razor'</span></span>
- <span data-ttu-id="4d045-332">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-333">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-333">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-334">'Blazor'</span></span>
- <span data-ttu-id="4d045-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-335">'Identity'</span></span>
- <span data-ttu-id="4d045-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-337">'Razor'</span></span>
- <span data-ttu-id="4d045-338">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-339">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-339">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-340">'Blazor'</span></span>
- <span data-ttu-id="4d045-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-341">'Identity'</span></span>
- <span data-ttu-id="4d045-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-343">'Razor'</span></span>
- <span data-ttu-id="4d045-344">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-345">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-345">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-346">'Blazor'</span></span>
- <span data-ttu-id="4d045-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-347">'Identity'</span></span>
- <span data-ttu-id="4d045-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-349">'Razor'</span></span>
- <span data-ttu-id="4d045-350">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-351">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-351">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-352">'Blazor'</span></span>
- <span data-ttu-id="4d045-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-353">'Identity'</span></span>
- <span data-ttu-id="4d045-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-355">'Razor'</span></span>
- <span data-ttu-id="4d045-356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="4d045-357">название: автор: описание: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="4d045-357">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="4d045-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="4d045-358">'Blazor'</span></span>
- <span data-ttu-id="4d045-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="4d045-359">'Identity'</span></span>
- <span data-ttu-id="4d045-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="4d045-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="4d045-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="4d045-361">'Razor'</span></span>
- <span data-ttu-id="4d045-362">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="4d045-362">'SignalR' uid:</span></span> 

<span data-ttu-id="4d045-363">--------------- | | Контракт         | Обязательный (*PROTO*)                                | Необязательный (OpenAPI)            | | Протокол         | HTTP/2                                             | HTTP                          | | Полезные данные          | [Protobuf (малый, двоичный)](#performance)           | JSON (большой, понятный для человека)  | | Нормативность | [Строгая спецификация](#strict-specification)      | Слабая.</span><span class="sxs-lookup"><span data-stu-id="4d045-363">--------------- | | Contract         | Required (*.proto*)                                | Optional (OpenAPI)            | | Protocol         | HTTP/2                                             | HTTP                          | | Payload          | [Protobuf (small, binary)](#performance)           | JSON (large, human readable)  | | Prescriptiveness | [Strict specification](#strict-specification)      | Loose.</span></span> <span data-ttu-id="4d045-364">Допустимы все HTTP-протоколы.</span><span class="sxs-lookup"><span data-stu-id="4d045-364">Any HTTP is valid.</span></span>     <span data-ttu-id="4d045-365">| | Потоковая передача        | [Клиент, сервер, двунаправленная](#streaming)       | Клиент, Сервер                | | Поддержка браузера  | [Нет (требуется grpc-web)](#limited-browser-support) | Да                           | | Безопасность         | Транспортный уровень (TLS)                                    | Транспортный уровень (TLS)               | | Формирование клиентского кода | [Да](#code-generation)                      | OpenAPI + сторонние инструменты |</span><span class="sxs-lookup"><span data-stu-id="4d045-365">| | Streaming        | [Client, server, bi-directional](#streaming)       | Client, server                | | Browser support  | [No (requires grpc-web)](#limited-browser-support) | Yes                           | | Security         | Transport (TLS)                                    | Transport (TLS)               | | Client code-generation | [Yes](#code-generation)                      | OpenAPI + third-party tooling |</span></span>

## <a name="grpc-strengths"></a><span data-ttu-id="4d045-366">Сильные стороны gRPC</span><span class="sxs-lookup"><span data-stu-id="4d045-366">gRPC strengths</span></span>

### <a name="performance"></a><span data-ttu-id="4d045-367">Производительность</span><span class="sxs-lookup"><span data-stu-id="4d045-367">Performance</span></span>

<span data-ttu-id="4d045-368">Сообщения gRPC сериализуются с помощью [Protobuf](https://developers.google.com/protocol-buffers/docs/overview) — эффективного двоичного формата сообщений.</span><span class="sxs-lookup"><span data-stu-id="4d045-368">gRPC messages are serialized using [Protobuf](https://developers.google.com/protocol-buffers/docs/overview), an efficient binary message format.</span></span> <span data-ttu-id="4d045-369">Protobuf очень быстро выполняет сериализацию на сервере и клиенте.</span><span class="sxs-lookup"><span data-stu-id="4d045-369">Protobuf serializes very quickly on the server and client.</span></span> <span data-ttu-id="4d045-370">Сериализация Protobuf приводит к небольшому объему полезных данных сообщения, а это важно в сценариях с ограниченной пропускной способностью, например для мобильных приложений.</span><span class="sxs-lookup"><span data-stu-id="4d045-370">Protobuf serialization results in small message payloads, important in limited bandwidth scenarios like mobile apps.</span></span>

<span data-ttu-id="4d045-371">gRPC предназначен для протокола HTTP/2, основной версии HTTP, которая обеспечивает значительное повышение производительности по сравнению с HTTP 1.x:</span><span class="sxs-lookup"><span data-stu-id="4d045-371">gRPC is designed for HTTP/2, a major revision of HTTP that provides significant performance benefits over HTTP 1.x:</span></span>

* <span data-ttu-id="4d045-372">Двоичное кадрирование и сжатие.</span><span class="sxs-lookup"><span data-stu-id="4d045-372">Binary framing and compression.</span></span> <span data-ttu-id="4d045-373">Протокол HTTP/2 является компактным и эффективным при отправке и получении.</span><span class="sxs-lookup"><span data-stu-id="4d045-373">HTTP/2 protocol is compact and efficient both in sending and receiving.</span></span>
* <span data-ttu-id="4d045-374">Мультиплексирование нескольких вызовов HTTP/2 через одно TCP-соединение.</span><span class="sxs-lookup"><span data-stu-id="4d045-374">Multiplexing of multiple HTTP/2 calls over a single TCP connection.</span></span> <span data-ttu-id="4d045-375">Мультиплексирование устраняет [блокировки очереди](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span><span class="sxs-lookup"><span data-stu-id="4d045-375">Multiplexing eliminates [head-of-line blocking](https://en.wikipedia.org/wiki/Head-of-line_blocking).</span></span>

<span data-ttu-id="4d045-376">HTTP/2 поддерживается не только gRPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-376">HTTP/2 is not exclusive to gRPC.</span></span> <span data-ttu-id="4d045-377">Многие типы запросов, включая API HTTP с JSON, могут использовать HTTP/2, чтобы повысить производительность.</span><span class="sxs-lookup"><span data-stu-id="4d045-377">Many request types, including HTTP APIs with JSON, can use HTTP/2 and benefit from its performance improvements.</span></span>

### <a name="code-generation"></a><span data-ttu-id="4d045-378">Создание кода</span><span class="sxs-lookup"><span data-stu-id="4d045-378">Code generation</span></span>

<span data-ttu-id="4d045-379">Все платформы gRPC предоставляют поддержку первого класса для создания кода.</span><span class="sxs-lookup"><span data-stu-id="4d045-379">All gRPC frameworks provide first-class support for code generation.</span></span> <span data-ttu-id="4d045-380">Базовым файлом для разработки gRPC является [PROTO-файл](https://developers.google.com/protocol-buffers/docs/proto3), который определяет контракт служб и сообщений gRPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-380">A core file to gRPC development is the [.proto file](https://developers.google.com/protocol-buffers/docs/proto3), which defines the contract of gRPC services and messages.</span></span> <span data-ttu-id="4d045-381">Из этого файла платформы gRPC будут создавать базовый класс службы, сообщения и полный клиент.</span><span class="sxs-lookup"><span data-stu-id="4d045-381">From this file gRPC frameworks will code generate a service base class, messages, and a complete client.</span></span>

<span data-ttu-id="4d045-382">Благодаря совместному использованию *PROTO-файла* между сервером и клиентом сообщения и клиентский код могут быть созданы от начала до конца.</span><span class="sxs-lookup"><span data-stu-id="4d045-382">By sharing the *.proto* file between the server and client, messages and client code can be generated from end to end.</span></span> <span data-ttu-id="4d045-383">Создание кода клиента устраняет дублирование сообщений на клиенте и сервере, а также создает клиент со строгой типизацией.</span><span class="sxs-lookup"><span data-stu-id="4d045-383">Code generation of the client eliminates duplication of messages on the client and server, and creates a strongly-typed client for you.</span></span> <span data-ttu-id="4d045-384">Отсутствие необходимости писать клиент экономит много времени при разработке в приложениях со множеством служб.</span><span class="sxs-lookup"><span data-stu-id="4d045-384">Not having to write a client saves significant development time in applications with many services.</span></span>

### <a name="strict-specification"></a><span data-ttu-id="4d045-385">Строгая спецификация</span><span class="sxs-lookup"><span data-stu-id="4d045-385">Strict specification</span></span>

<span data-ttu-id="4d045-386">Формальная спецификация для API HTTP с JSON не существует.</span><span class="sxs-lookup"><span data-stu-id="4d045-386">A formal specification for HTTP API with JSON doesn't exist.</span></span> <span data-ttu-id="4d045-387">Разработчики спорят о лучшем формате URL-адресов, HTTP-команд и кодов ответов.</span><span class="sxs-lookup"><span data-stu-id="4d045-387">Developers debate the best format of URLs, HTTP verbs, and response codes.</span></span>

<span data-ttu-id="4d045-388">[Спецификация gRPC](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) содержит строгие указания о формате, которому должна соответствовать служба gRPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-388">The [gRPC specification](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) is prescriptive about the format a gRPC service must follow.</span></span> <span data-ttu-id="4d045-389">gRPC исключает споры и экономит время разработчика, поскольку службы gRPC согласованы между платформами и реализациями.</span><span class="sxs-lookup"><span data-stu-id="4d045-389">gRPC eliminates debate and saves developer time because gRPC is consistent across platforms and implementations.</span></span>

### <a name="streaming"></a><span data-ttu-id="4d045-390">Потоковые операторы</span><span class="sxs-lookup"><span data-stu-id="4d045-390">Streaming</span></span>

<span data-ttu-id="4d045-391">HTTP/2 предоставляет основу для долгосрочных потоков связи в режиме реального времени.</span><span class="sxs-lookup"><span data-stu-id="4d045-391">HTTP/2 provides a foundation for long-lived, real-time communication streams.</span></span> <span data-ttu-id="4d045-392">gRPC предоставляет поддержку первого класса для потоковой передачи через HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4d045-392">gRPC provides first-class support for streaming through HTTP/2.</span></span>

<span data-ttu-id="4d045-393">Служба gRPC поддерживает все сочетания потоков:</span><span class="sxs-lookup"><span data-stu-id="4d045-393">A gRPC service supports all streaming combinations:</span></span>

* <span data-ttu-id="4d045-394">Унарный (нет потоковой передачи)</span><span class="sxs-lookup"><span data-stu-id="4d045-394">Unary (no streaming)</span></span>
* <span data-ttu-id="4d045-395">Потоковая передача от сервера к клиенту</span><span class="sxs-lookup"><span data-stu-id="4d045-395">Server to client streaming</span></span>
* <span data-ttu-id="4d045-396">Потоковая передача от клиента к серверу</span><span class="sxs-lookup"><span data-stu-id="4d045-396">Client to server streaming</span></span>
* <span data-ttu-id="4d045-397">Двунаправленная потоковая передача</span><span class="sxs-lookup"><span data-stu-id="4d045-397">Bi-directional streaming</span></span>

### <a name="deadlinetimeouts-and-cancellation"></a><span data-ttu-id="4d045-398">Крайний срок, время ожидания и отмена</span><span class="sxs-lookup"><span data-stu-id="4d045-398">Deadline/timeouts and cancellation</span></span>

<span data-ttu-id="4d045-399">gRPC позволяет клиентам указать, как долго они хотят ожидать завершения RPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-399">gRPC allows clients to specify how long they are willing to wait for an RPC to complete.</span></span> <span data-ttu-id="4d045-400">[Крайний срок](https://grpc.io/blog/deadlines) отправляется на сервер, и сервер может решить, какое действие следует предпринять, когда крайний срок превышен.</span><span class="sxs-lookup"><span data-stu-id="4d045-400">The [deadline](https://grpc.io/blog/deadlines) is sent to the server, and the server can decide what action to take if it exceeds the deadline.</span></span> <span data-ttu-id="4d045-401">Например, сервер может отменять выполняющиеся запросы gRPC/HTTP/базы данных по истечении времени ожидания.</span><span class="sxs-lookup"><span data-stu-id="4d045-401">For example, the server might cancel in-progress gRPC/HTTP/database requests on timeout.</span></span>

<span data-ttu-id="4d045-402">Распространение крайнего срока и отмены через дочерние вызовы gRPC помогает применять ограничения использования ресурсов.</span><span class="sxs-lookup"><span data-stu-id="4d045-402">Propagating the deadline and cancellation through child gRPC calls helps enforce resource usage limits.</span></span>

## <a name="grpc-recommended-scenarios"></a><span data-ttu-id="4d045-403">Рекомендуемые сценарии gRPC</span><span class="sxs-lookup"><span data-stu-id="4d045-403">gRPC recommended scenarios</span></span>

<span data-ttu-id="4d045-404">gRPC хорошо подходит для следующих сценариев:</span><span class="sxs-lookup"><span data-stu-id="4d045-404">gRPC is well suited to the following scenarios:</span></span>

* <span data-ttu-id="4d045-405">**Микрослужбы** — gRPC обеспечивает малое время задержки и высокую пропускную способность.</span><span class="sxs-lookup"><span data-stu-id="4d045-405">**Microservices**: gRPC is designed for low latency and high throughput communication.</span></span> <span data-ttu-id="4d045-406">gRPC отлично подходит для упрощенных микрослужб, где важна эффективность.</span><span class="sxs-lookup"><span data-stu-id="4d045-406">gRPC is great for lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="4d045-407">**Взаимодействие между точками в реальном времени** — gRPC полноценно поддерживает двунаправленную потоковую передачу.</span><span class="sxs-lookup"><span data-stu-id="4d045-407">**Point-to-point real-time communication**: gRPC has excellent support for bi-directional streaming.</span></span> <span data-ttu-id="4d045-408">Службы gRPC могут отправлять сообщения в режиме реального времени без опроса.</span><span class="sxs-lookup"><span data-stu-id="4d045-408">gRPC services can push messages in real-time without polling.</span></span>
* <span data-ttu-id="4d045-409">**Среды с разными языками** — средства gRPC поддерживают все популярные языки разработки, поэтому gRPC является хорошим выбором для многоязыковых сред.</span><span class="sxs-lookup"><span data-stu-id="4d045-409">**Polyglot environments**: gRPC tooling supports all popular development languages, making gRPC a good choice for multi-language environments.</span></span>
* <span data-ttu-id="4d045-410">**Среды с ограниченными ресурсами сети** — сообщения gRPC сериализуются с помощью Protobuf и имеют упрощенный формат.</span><span class="sxs-lookup"><span data-stu-id="4d045-410">**Network constrained environments**: gRPC messages are serialized with Protobuf, a lightweight message format.</span></span> <span data-ttu-id="4d045-411">Сообщение gRPC всегда меньше, чем эквивалентное сообщение JSON.</span><span class="sxs-lookup"><span data-stu-id="4d045-411">A gRPC message is always smaller than an equivalent JSON message.</span></span>

## <a name="grpc-weaknesses"></a><span data-ttu-id="4d045-412">Слабые стороны gRPC</span><span class="sxs-lookup"><span data-stu-id="4d045-412">gRPC weaknesses</span></span>

### <a name="limited-browser-support"></a><span data-ttu-id="4d045-413">Ограниченная поддержка веб-браузера</span><span class="sxs-lookup"><span data-stu-id="4d045-413">Limited browser support</span></span>

<span data-ttu-id="4d045-414">Сейчас невозможно напрямую вызвать службу gRPC из браузера.</span><span class="sxs-lookup"><span data-stu-id="4d045-414">It's impossible to directly call a gRPC service from a browser today.</span></span> <span data-ttu-id="4d045-415">gRPC активно использует функции HTTP/2, и ни один браузер не предоставляет необходимый уровень контроля над веб-запросами для поддержки клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-415">gRPC heavily uses HTTP/2 features and no browser provides the level of control required over web requests to support a gRPC client.</span></span> <span data-ttu-id="4d045-416">Например, браузеры не позволяют вызывающему объекту требовать использования HTTP/2 или предоставить доступ к базовым кадрам HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4d045-416">For example, browsers do not allow a caller to require that HTTP/2 be used, or provide access to underlying HTTP/2 frames.</span></span>

<span data-ttu-id="4d045-417">[gRPC-Web-](https://grpc.io/docs/tutorials/basic/web.html) — это дополнительная технология от команды gRPC, которая предоставляет ограниченную поддержку gRPC в браузере.</span><span class="sxs-lookup"><span data-stu-id="4d045-417">[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) is an additional technology from the gRPC team that provides limited gRPC support in the browser.</span></span> <span data-ttu-id="4d045-418">gRPC-Web состоит из двух частей: клиент JavaScript, поддерживающий все современные браузеры, и прокси gRPC-Web на сервере.</span><span class="sxs-lookup"><span data-stu-id="4d045-418">gRPC-Web consists of two parts: a JavaScript client that supports all modern browsers, and a gRPC-Web proxy on the server.</span></span> <span data-ttu-id="4d045-419">Клиент gRPC-Web вызывает прокси-сервер, и прокси-сервер пересылает запросы gRPC на сервер gRPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-419">The gRPC-Web client calls the proxy and the proxy will forward on the gRPC requests to the gRPC server.</span></span>

<span data-ttu-id="4d045-420">gRPC-Web поддерживает не все возможности gRPC.</span><span class="sxs-lookup"><span data-stu-id="4d045-420">Not all of gRPC's features are supported by gRPC-Web.</span></span> <span data-ttu-id="4d045-421">Клиентская и двунаправленная потоковая передача не поддерживается, и существует ограниченная поддержка потоковой передачи сервера.</span><span class="sxs-lookup"><span data-stu-id="4d045-421">Client and bi-directional streaming isn't supported, and there is limited support for server streaming.</span></span>

> [!TIP]
> <span data-ttu-id="4d045-422">.NET Core имеет экспериментальную поддержку для gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4d045-422">.NET Core has experimental support for gRPC-Web.</span></span> <span data-ttu-id="4d045-423">Дополнительные сведения см. по адресу <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="4d045-423">Visit <xref:grpc/browser> for more information.</span></span>

### <a name="not-human-readable"></a><span data-ttu-id="4d045-424">Недоступно для чтения человеком</span><span class="sxs-lookup"><span data-stu-id="4d045-424">Not human readable</span></span>

<span data-ttu-id="4d045-425">Запросы API HTTP отправляются в виде текста и могут быть прочитаны и созданы людьми.</span><span class="sxs-lookup"><span data-stu-id="4d045-425">HTTP API requests are sent as text and can be read and created by humans.</span></span>

<span data-ttu-id="4d045-426">По умолчанию сообщения gRPC кодируются с помощью Protobuf.</span><span class="sxs-lookup"><span data-stu-id="4d045-426">gRPC messages are encoded with Protobuf by default.</span></span> <span data-ttu-id="4d045-427">Хотя Protobuf является эффективным методом отправки и получения, его двоичный формат не читается человеком.</span><span class="sxs-lookup"><span data-stu-id="4d045-427">While Protobuf is efficient to send and receive, its binary format isn't human readable.</span></span> <span data-ttu-id="4d045-428">Для Protobuf требуется описание интерфейса сообщения, указанное в файле *PROTO* для правильной десериализации.</span><span class="sxs-lookup"><span data-stu-id="4d045-428">Protobuf requires the message's interface description specified in the *.proto* file to properly deserialize.</span></span> <span data-ttu-id="4d045-429">Для анализа полезных данных Protobuf на канале передачи и создания запросов вручную требуются дополнительные средства.</span><span class="sxs-lookup"><span data-stu-id="4d045-429">Additional tooling is required to analyze Protobuf payloads on the wire and to compose requests by hand.</span></span>

<span data-ttu-id="4d045-430">Такие функции, как [отражение сервера](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) и [средство командной строки gRPC](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) помогают работать с двоичными сообщениями Protobuf.</span><span class="sxs-lookup"><span data-stu-id="4d045-430">Features such as [server reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) and the [gRPC command line tool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md) exist to assist with binary Protobuf messages.</span></span> <span data-ttu-id="4d045-431">Кроме того, сообщения Protobuf поддерживают [преобразование в JSON и из JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span><span class="sxs-lookup"><span data-stu-id="4d045-431">Also, Protobuf messages support [conversion to and from JSON](https://developers.google.com/protocol-buffers/docs/proto3#json).</span></span> <span data-ttu-id="4d045-432">Встроенное преобразование JSON обеспечивает эффективный способ преобразования сообщений Protobuf в удобочитаемую форму при отладке.</span><span class="sxs-lookup"><span data-stu-id="4d045-432">The built-in JSON conversion provides an efficient way to convert Protobuf messages to and from human readable form when debugging.</span></span>

## <a name="alternative-framework-scenarios"></a><span data-ttu-id="4d045-433">Альтернативные сценарии платформы</span><span class="sxs-lookup"><span data-stu-id="4d045-433">Alternative framework scenarios</span></span>

<span data-ttu-id="4d045-434">Рекомендуется использовать другие платформы вместо gRPC в следующих сценариях:</span><span class="sxs-lookup"><span data-stu-id="4d045-434">Other frameworks are recommended over gRPC in the following scenarios:</span></span>

* <span data-ttu-id="4d045-435">**API, доступные в браузере** — gRPC не полностью поддерживаются в браузере.</span><span class="sxs-lookup"><span data-stu-id="4d045-435">**Browser accessible APIs**: gRPC isn't fully supported in the browser.</span></span> <span data-ttu-id="4d045-436">gRPC-Web может предлагать поддержку браузеров, но имеет ограничения и вводит серверный прокси.</span><span class="sxs-lookup"><span data-stu-id="4d045-436">gRPC-Web can offer browser support, but it has limitations and introduces a server proxy.</span></span>
* <span data-ttu-id="4d045-437">**Широковещательная передача в реальном времени** — gRPC поддерживает обмен данными в режиме реального времени через потоковую передачу, но концепция широковещательной рассылки сообщения в зарегистрированные подключения не существует.</span><span class="sxs-lookup"><span data-stu-id="4d045-437">**Broadcast real-time communication**: gRPC supports real-time communication via streaming, but the concept of broadcasting a message out to registered connections doesn't exist.</span></span> <span data-ttu-id="4d045-438">Например, в сценарии комнаты чатов, где новые сообщения разговора должны отправляться всем клиентам в комнате разговора, каждый вызов gRPC должен отдельно передавать новые сообщения чата клиенту.</span><span class="sxs-lookup"><span data-stu-id="4d045-438">For example in a chat room scenario where new chat messages should be sent to all clients in the chat room, each gRPC call is required to individually stream new chat messages to the client.</span></span> <span data-ttu-id="4d045-439">Для этого сценария хорошо подойдет [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="4d045-439">[SignalR](xref:signalr/introduction) is a useful framework for this scenario.</span></span> <span data-ttu-id="4d045-440">В SignalR есть концепция постоянных подключений и встроенная поддержка широковещательных сообщений.</span><span class="sxs-lookup"><span data-stu-id="4d045-440">SignalR has the concept of persistent connections and built-in support for broadcasting messages.</span></span>
* <span data-ttu-id="4d045-441">**Межпроцессное взаимодействие** — для приема входящих вызовов gRPC процесс должен содержать сервер HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4d045-441">**Inter-process communication**: A process must host an HTTP/2 server to accept incoming gRPC calls.</span></span> <span data-ttu-id="4d045-442">Для Windows [каналы](/dotnet/standard/io/pipe-operations) внутрипроцессного взаимодействия являются быстрым и простым методом обмена данными.</span><span class="sxs-lookup"><span data-stu-id="4d045-442">For Windows, inter-process communication [pipes](/dotnet/standard/io/pipe-operations) is a fast, lightweight method of communication.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d045-443">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="4d045-443">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
