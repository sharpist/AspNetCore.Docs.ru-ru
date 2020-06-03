---
<span data-ttu-id="2cb55-101">название: автор: описание: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cb55-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cb55-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cb55-102">'Blazor'</span></span>
- <span data-ttu-id="2cb55-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cb55-103">'Identity'</span></span>
- <span data-ttu-id="2cb55-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cb55-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cb55-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cb55-105">'Razor'</span></span>
- <span data-ttu-id="2cb55-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cb55-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="2cb55-107">Нагрузочное тестирование в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cb55-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="2cb55-108">Нагрузочное тестирование и нагрузочные испытания важны для обеспечения эффективности и масштабируемости веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="2cb55-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="2cb55-109">Их цели различаются, несмотря на то, что они часто используют аналогичные тесты.</span><span class="sxs-lookup"><span data-stu-id="2cb55-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="2cb55-110">**Нагрузочное тестирование.** Проверка того, может ли приложение управлять заданной нагрузкой пользователей для определенного сценария в соответствии с задачей ответа.</span><span class="sxs-lookup"><span data-stu-id="2cb55-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="2cb55-111">Приложение запускается в нормальных условиях.</span><span class="sxs-lookup"><span data-stu-id="2cb55-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="2cb55-112">**Нагрузочные тесты.** Проверка стабильности приложений при выполнении в экстремальных условиях, часто в течение длительного периода времени.</span><span class="sxs-lookup"><span data-stu-id="2cb55-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="2cb55-113">В тестах создается большая нагрузка на пользователей, пиковая или постепенно увеличивающаяся нагрузка на приложение или ограничиваются вычислительные ресурсы приложения.</span><span class="sxs-lookup"><span data-stu-id="2cb55-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="2cb55-114">Нагрузочные испытания определяют, может ли приложение под нагрузкой восстанавливаться после сбоя и корректно возвращаться к ожидаемому поведению.</span><span class="sxs-lookup"><span data-stu-id="2cb55-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="2cb55-115">В условиях нагрузки приложение не запускается в нормальных условиях.</span><span class="sxs-lookup"><span data-stu-id="2cb55-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="2cb55-116">Visual Studio 2019 — это последняя версия Visual Studio, включающая средства нагрузочного тестирования.</span><span class="sxs-lookup"><span data-stu-id="2cb55-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="2cb55-117">Клиентам, которым в будущем потребуются средства нагрузочного тестирования, мы рекомендуем использовать альтернативные средства нагрузочного тестирования, такие как Apache JMeter, Akamai CloudTest или BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="2cb55-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="2cb55-118">Дополнительные сведения см. в [заметках о выпуске Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="2cb55-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="2cb55-119">Инструменты Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cb55-119">Visual Studio tools</span></span>

<span data-ttu-id="2cb55-120">Visual Studio позволяет пользователям создавать, разрабатывать и отлаживать тесты веб-производительности и нагрузочные тесты.</span><span class="sxs-lookup"><span data-stu-id="2cb55-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="2cb55-121">Имеется параметр для создания тестов путем записи действий в веб-браузере.</span><span class="sxs-lookup"><span data-stu-id="2cb55-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="2cb55-122">Сведения о создании, настройке и запуске проектов нагрузочных тестов с помощью Visual Studio 2017 см. в разделе [Быстрое начало. Создание проекта нагрузочного тестирования](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="2cb55-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="2cb55-123">Нагрузочные тесты можно настроить для локального запуска или запуска в облаке с помощью Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="2cb55-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="2cb55-124">Сторонние средства</span><span class="sxs-lookup"><span data-stu-id="2cb55-124">Third-party tools</span></span>

<span data-ttu-id="2cb55-125">В списке ниже представлены сторонние средства тестирования веб-производительности с различными наборами функций:</span><span class="sxs-lookup"><span data-stu-id="2cb55-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="2cb55-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="2cb55-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="2cb55-127">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="2cb55-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="2cb55-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="2cb55-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="2cb55-129">k6</span><span class="sxs-lookup"><span data-stu-id="2cb55-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="2cb55-130">Locust</span><span class="sxs-lookup"><span data-stu-id="2cb55-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="2cb55-131">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="2cb55-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="2cb55-132">Netling</span><span class="sxs-lookup"><span data-stu-id="2cb55-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="2cb55-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="2cb55-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

