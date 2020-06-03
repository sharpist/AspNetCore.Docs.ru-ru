---
<span data-ttu-id="2cd95-101">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-102">'Blazor'</span></span>
- <span data-ttu-id="2cd95-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-103">'Identity'</span></span>
- <span data-ttu-id="2cd95-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-105">'Razor'</span></span>
- <span data-ttu-id="2cd95-106">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="2cd95-107">Интеграционные тесты на платформе ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cd95-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="2cd95-108">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Стив Смит](https://ardalis.com/) (Steve Smith) и [Йос ван дер Тиль](https://jvandertil.nl) (Jos van der Til)</span><span class="sxs-lookup"><span data-stu-id="2cd95-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2cd95-109">С помощью интеграционных тестов можно проверить работу компонентов приложения на уровне, который включает инфраструктуру, поддерживающую приложение, такую как база данных, файловая система и сеть.</span><span class="sxs-lookup"><span data-stu-id="2cd95-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="2cd95-110">ASP.NET Core поддерживает интеграционные тесты с помощью платформы модульного тестирования с тестовым веб-узлом и сервером тестирования в памяти.</span><span class="sxs-lookup"><span data-stu-id="2cd95-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="2cd95-111">В этом разделе предполагается базовое понимание модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="2cd95-112">Если вы не знакомы с концепциями тестирования, ознакомьтесь с разделом [Модульное тестирование в .NET Core и .NET Standard](/dotnet/core/testing/) и связанным с ним содержимым.</span><span class="sxs-lookup"><span data-stu-id="2cd95-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="2cd95-113">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cd95-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2cd95-114">В качестве примера используется приложение Razor Pages (предполагается базовое понимание Razor Pages).</span><span class="sxs-lookup"><span data-stu-id="2cd95-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="2cd95-115">Если вы не знакомы с Razor Pages, см. следующие разделы:</span><span class="sxs-lookup"><span data-stu-id="2cd95-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="2cd95-116">[Введение в Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="2cd95-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="2cd95-117">[Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="2cd95-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="2cd95-118">[Модульные тесты Razor Pages](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="2cd95-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-119">Для тестирования одностраничных приложений мы рекомендуем инструмент типа [Selenium](https://www.seleniumhq.org/), с помощью которого можно автоматизировать браузер.</span><span class="sxs-lookup"><span data-stu-id="2cd95-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="2cd95-120">Общие сведения об интеграционных тестах</span><span class="sxs-lookup"><span data-stu-id="2cd95-120">Introduction to integration tests</span></span>

<span data-ttu-id="2cd95-121">Интеграционные тесты служат для оценки компонентов приложения на более широком уровне, чем [модульные тесты](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="2cd95-122">Модульные тесты используются для тестирования изолированных программных компонентов, таких как отдельные методы класса.</span><span class="sxs-lookup"><span data-stu-id="2cd95-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="2cd95-123">Интеграционные тесты позволяют убедиться, что два или несколько компонентов приложения работают совместно для получения ожидаемого результата, включая, возможно, все компоненты, необходимые для полной обработки запроса.</span><span class="sxs-lookup"><span data-stu-id="2cd95-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="2cd95-124">Эти более широкие тесты используются для тестирования инфраструктуры приложения и всей платформы, зачастую включая следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="2cd95-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="2cd95-125">База данных</span><span class="sxs-lookup"><span data-stu-id="2cd95-125">Database</span></span>
* <span data-ttu-id="2cd95-126">Файловая система</span><span class="sxs-lookup"><span data-stu-id="2cd95-126">File system</span></span>
* <span data-ttu-id="2cd95-127">Сетевые устройства</span><span class="sxs-lookup"><span data-stu-id="2cd95-127">Network appliances</span></span>
* <span data-ttu-id="2cd95-128">Конвейер "запрос-ответ"</span><span class="sxs-lookup"><span data-stu-id="2cd95-128">Request-response pipeline</span></span>

<span data-ttu-id="2cd95-129">В модульных тестах вместо компонентов инфраструктуры используются структурные компоненты, известные как *имитации* или *макеты объектов*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="2cd95-130">В отличие от модульных тестов, интеграционные тесты:</span><span class="sxs-lookup"><span data-stu-id="2cd95-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="2cd95-131">Используют фактические компоненты, которые приложение использует в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2cd95-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="2cd95-132">Требуют больше кода и обработки данных.</span><span class="sxs-lookup"><span data-stu-id="2cd95-132">Require more code and data processing.</span></span>
* <span data-ttu-id="2cd95-133">Выполняются дольше.</span><span class="sxs-lookup"><span data-stu-id="2cd95-133">Take longer to run.</span></span>

<span data-ttu-id="2cd95-134">Поэтому используйте интеграционные тесты только для наиболее важных сценариев инфраструктуры.</span><span class="sxs-lookup"><span data-stu-id="2cd95-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="2cd95-135">Если поведение можно проверить с помощью модульного теста или интеграционного теста, выбирайте модульный тест.</span><span class="sxs-lookup"><span data-stu-id="2cd95-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="2cd95-136">Не создавайте интеграционные тесты для каждого возможного варианта доступа к базам данных и файловым системам.</span><span class="sxs-lookup"><span data-stu-id="2cd95-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="2cd95-137">В скольких бы местах приложение ни взаимодействовало с ними, фокусный набор интеграционных тестов на чтение, запись, обновление и удаление обычно способен адекватно протестировать их компоненты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="2cd95-138">Используйте модульные тесты для стандартных тестов логики методов, взаимодействующих с этими компонентами.</span><span class="sxs-lookup"><span data-stu-id="2cd95-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="2cd95-139">В модульных тестах использование инфраструктурных имитаций приводит к более быстрому выполнению теста.</span><span class="sxs-lookup"><span data-stu-id="2cd95-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-140">При обсуждении интеграционных тестов тестируемый проект часто называется *тестируемой системой* или "ТС".</span><span class="sxs-lookup"><span data-stu-id="2cd95-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="2cd95-141">*В этом разделе используется "ТС" для ссылки на проверенное приложение ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="2cd95-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="2cd95-142">Интеграционные тесты ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cd95-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="2cd95-143">Для интеграционных тестов в ASP.NET Core требуется следующее:</span><span class="sxs-lookup"><span data-stu-id="2cd95-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="2cd95-144">Тестовый проект, который используется для хранения и выполнения тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="2cd95-145">Тестовый проект содержит ссылку на ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="2cd95-146">Тестовый проект создает тестовый веб-узел для ТС и использует клиент тестового сервера для обработки запросов и ответов с помощью ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="2cd95-147">Средство запуска тестов используется для выполнения тестов и передачи результатов тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="2cd95-148">Интеграционные тесты придерживаются последовательности событий из обычных шагов теста *Подготовка*, *Выполнение* и *Проверка*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="2cd95-149">Настраивается веб-узел ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="2cd95-150">Создается клиент тестового сервера для отправки запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="2cd95-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="2cd95-151">Выполняется шаг теста *Подготовка*: тестовое приложение готовит запрос.</span><span class="sxs-lookup"><span data-stu-id="2cd95-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="2cd95-152">Выполняется шаг теста *Выполнение*: клиент отправляет запрос и получает ответ.</span><span class="sxs-lookup"><span data-stu-id="2cd95-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="2cd95-153">Выполняется шаг теста *Проверка*: *фактический* ответ определяется как *правильный* или *ошибочный* на основе *ожидаемого* ответа.</span><span class="sxs-lookup"><span data-stu-id="2cd95-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="2cd95-154">Процесс продолжается до тех пор, пока не будут выполнены все тесты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="2cd95-155">Выводятся результаты теста.</span><span class="sxs-lookup"><span data-stu-id="2cd95-155">The test results are reported.</span></span>

<span data-ttu-id="2cd95-156">Как правило, тестовый веб-узел настраивается отлично от обычного веб-узла приложения для тестовых запусков.</span><span class="sxs-lookup"><span data-stu-id="2cd95-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="2cd95-157">Например, для тестов может использоваться другая база данных или другие параметры приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="2cd95-158">Компоненты инфраструктуры, такие как тестовый веб-узел и сервер тестирования в памяти ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), предоставляются или управляются пакетом [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).</span><span class="sxs-lookup"><span data-stu-id="2cd95-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="2cd95-159">Использование этого пакета упрощает создание и выполнение тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="2cd95-160">Пакет `Microsoft.AspNetCore.Mvc.Testing` выполняет следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="2cd95-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="2cd95-161">Копирует файл зависимостей (*DEPS*) из ТС в папку *bin* тестового проекта.</span><span class="sxs-lookup"><span data-stu-id="2cd95-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="2cd95-162">Задает [корневой каталог](xref:fundamentals/index#content-root) содержимого в корне проекта ТС, чтобы при выполнении тестов были найдены статические файлы и страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="2cd95-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="2cd95-163">Предоставляет класс [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) для упрощения начальной загрузки ТС с `TestServer`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="2cd95-164">В документации [модульные тесты](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) описано, как настроить тестовый проект и средство запуска тестов, а также представлены подробные инструкции по выполнению тестов и рекомендаций по именованию тестов и тестовых классов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-165">При создании тестового проекта для приложения отделяйте модульные тесты от интеграционных, помещая их в разные проекты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="2cd95-166">Это гарантирует, что компоненты тестирования инфраструктуры не будут случайно добавлены в модульные тесты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="2cd95-167">Разделение модульных и интеграционных тестов также позволяет контролировать, какой набор тестов выполняется.</span><span class="sxs-lookup"><span data-stu-id="2cd95-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="2cd95-168">В сущности, между конфигурацией для тестов приложений Razor Pages и приложений MVC нет никаких отличий.</span><span class="sxs-lookup"><span data-stu-id="2cd95-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="2cd95-169">Единственное отличие заключается в том, как именуются тесты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="2cd95-170">В приложении Razor Pages тесты конечных точек страницы обычно именуются по классу модели страницы (например, `IndexPageTests` для тестирования интеграции компонентов на странице индекса).</span><span class="sxs-lookup"><span data-stu-id="2cd95-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="2cd95-171">В приложении MVC тесты обычно организованы по классам контроллеров и именуются по контроллеру, который они проверяют (например, `HomeControllerTests` для тестирования интеграции компонентов на контроллере Home).</span><span class="sxs-lookup"><span data-stu-id="2cd95-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="2cd95-172">Проверка необходимых требований к приложению</span><span class="sxs-lookup"><span data-stu-id="2cd95-172">Test app prerequisites</span></span>

<span data-ttu-id="2cd95-173">Тестовый проект должен выполнять следующие требования.</span><span class="sxs-lookup"><span data-stu-id="2cd95-173">The test project must:</span></span>

* <span data-ttu-id="2cd95-174">Ссылаться на пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).</span><span class="sxs-lookup"><span data-stu-id="2cd95-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="2cd95-175">Указывать веб-пакет SDK в файле проекта (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span><span class="sxs-lookup"><span data-stu-id="2cd95-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="2cd95-176">Выполнение необходимых требований можно посмотреть в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="2cd95-177">Изучите файл *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="2cd95-178">В примере приложения используются платформа тестирования [xUnit](https://xunit.github.io/) и библиотека средства синтаксического анализа [AngleSharp](https://anglesharp.github.io/), поэтому он также ссылается на:</span><span class="sxs-lookup"><span data-stu-id="2cd95-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="2cd95-179">xunit</span><span class="sxs-lookup"><span data-stu-id="2cd95-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="2cd95-180">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="2cd95-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="2cd95-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="2cd95-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="2cd95-182">В тестах также используется Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="2cd95-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="2cd95-183">Ссылки на приложение:</span><span class="sxs-lookup"><span data-stu-id="2cd95-183">The app references:</span></span>

* [<span data-ttu-id="2cd95-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2cd95-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="2cd95-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="2cd95-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="2cd95-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="2cd95-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="2cd95-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="2cd95-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="2cd95-188">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="2cd95-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="2cd95-189">Среда ТС</span><span class="sxs-lookup"><span data-stu-id="2cd95-189">SUT environment</span></span>

<span data-ttu-id="2cd95-190">Если [среда](xref:fundamentals/environments) ТС не задана, то по умолчанию среда имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="2cd95-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="2cd95-191">Базовые тесты со стандартной WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="2cd95-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="2cd95-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) используется для создания [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) для интеграционных тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="2cd95-193">`TEntryPoint` — это класс точки входа ТС, обычно класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="2cd95-194">Тестовые классы реализуют интерфейс *средства тестирования* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), чтобы указать, что класс содержит тесты, и предоставить экземпляры общего объекта между тестами в классе.</span><span class="sxs-lookup"><span data-stu-id="2cd95-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="2cd95-195">Следующий тестовый класс, `BasicTests`, использует `WebApplicationFactory` для начальной загрузки ТС и предоставляет [HttpClient](/dotnet/api/system.net.http.httpclient) тестовому методу `Get_EndpointsReturnSuccessAndCorrectContentType`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="2cd95-196">Метод проверяет, что код состояния ответа свидетельствует о выполнении (коды состояния в диапазоне от 200 до 299) и что заголовок `Content-Type` имеет значение `text/html; charset=utf-8` для нескольких страниц приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="2cd95-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) создает экземпляр класса `HttpClient`, который автоматически следует за перенаправлениями и обрабатывает файлы cookie.</span><span class="sxs-lookup"><span data-stu-id="2cd95-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="2cd95-198">По умолчанию некритические файлы cookie не сохраняются в запросах, если включена [политика согласия GDPR](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2cd95-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="2cd95-199">Чтобы сохранить ненужные файлы cookie, например те, которые используются поставщиком TempData, пометьте их как основные в тестах.</span><span class="sxs-lookup"><span data-stu-id="2cd95-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="2cd95-200">Инструкции по маркировке файла cookie в качестве основы см. в разделе [Основные файлы cookie](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="2cd95-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="2cd95-201">Настройка WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="2cd95-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="2cd95-202">Конфигурацию веб-узла можно создать независимо от тестовых классов путем наследования от `WebApplicationFactory` для создания одной или нескольких пользовательских фабрик:</span><span class="sxs-lookup"><span data-stu-id="2cd95-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="2cd95-203">Выполните наследование от `WebApplicationFactory` и переопределите [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="2cd95-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="2cd95-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) позволяет настраивать коллекцию служб с помощью [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="2cd95-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="2cd95-205">Заполнение базы данных в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) выполняется методом `InitializeDbForTests`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="2cd95-206">Этот метод описан в разделе [Пример интеграционных тестов: организация приложения для тестирования](#test-app-organization).</span><span class="sxs-lookup"><span data-stu-id="2cd95-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="2cd95-207">Контекст базы данных ТС регистрируется в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2cd95-208">Обратный вызов `builder.ConfigureServices` тестового приложения выполняется *после* выполнения кода `Startup.ConfigureServices` приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="2cd95-209">Порядок выполнения является критическим изменением для [универсального узла](xref:fundamentals/host/generic-host) с выпуском ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="2cd95-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2cd95-210">Чтобы использовать для тестов базу данных, отличную от базы данных приложения, необходимо заменить контекст базы данных приложения в `builder.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="2cd95-211">В тестируемых системах, которые по-прежнему используют [веб-узел](xref:fundamentals/host/web-host), обратный вызов `builder.ConfigureServices` тестового приложения выполняется *до* выполнения кода `Startup.ConfigureServices` тестируемой системы.</span><span class="sxs-lookup"><span data-stu-id="2cd95-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="2cd95-212">Обратный вызов `builder.ConfigureTestServices` тестового приложения выполняется *позже*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="2cd95-213">Пример приложения находит дескриптор службы для контекста базы данных и использует дескриптор для удаления регистрации службы.</span><span class="sxs-lookup"><span data-stu-id="2cd95-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="2cd95-214">Затем фабрика добавляет новый `ApplicationDbContext`, который использует базу данных в памяти для тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="2cd95-215">Чтобы подключиться к базе данных, отличной от базы данных в памяти, измените вызов `UseInMemoryDatabase`, чтобы подключить контекст к другой базе данных.</span><span class="sxs-lookup"><span data-stu-id="2cd95-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="2cd95-216">Чтобы использовать тестовую базу данных SQL Server, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2cd95-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="2cd95-217">Сошлитесь на пакет NuGet [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="2cd95-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="2cd95-218">Вызовите `UseSqlServer` со строкой подключения к базе данных.</span><span class="sxs-lookup"><span data-stu-id="2cd95-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="2cd95-219">Используйте настраиваемый `CustomWebApplicationFactory` в тестовых классах.</span><span class="sxs-lookup"><span data-stu-id="2cd95-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="2cd95-220">В следующем примере используется фабрика в классе `IndexPageTests`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="2cd95-221">Клиент примера приложения настроен так, чтобы не допустить выполнение клиентов `HttpClient` следующих перенаправлений.</span><span class="sxs-lookup"><span data-stu-id="2cd95-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="2cd95-222">Как описано далее в разделе [Имитация проверки подлинности](#mock-authentication), это позволяет тестам проверять результат первого ответа приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="2cd95-223">Первый ответ — это перенаправление во многих из этих тестов с заголовком `Location`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="2cd95-224">Обычный тест использует `HttpClient` и вспомогательные методы для обработки запроса и ответа:</span><span class="sxs-lookup"><span data-stu-id="2cd95-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2cd95-225">Любой запрос POST к ТС должен соответствовать проверке защиты от подделки, которая автоматически вносится в [систему защиты данных от подделки](xref:security/data-protection/introduction) приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2cd95-226">Чтобы упорядочить запрос POST теста, тестовое приложение должно:</span><span class="sxs-lookup"><span data-stu-id="2cd95-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="2cd95-227">Выполнить запрос к странице.</span><span class="sxs-lookup"><span data-stu-id="2cd95-227">Make a request for the page.</span></span>
1. <span data-ttu-id="2cd95-228">Проанализировать файл cookie для защиты от подделки и запросить маркер проверки из ответа.</span><span class="sxs-lookup"><span data-stu-id="2cd95-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="2cd95-229">Выполните запрос POST с файлом cookie для защиты от подделки и запросом маркера проверки на месте.</span><span class="sxs-lookup"><span data-stu-id="2cd95-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="2cd95-230">Вспомогательные методы расширения `SendAsync` (*Helpers/HttpClientExtensions.cs*) и вспомогательный метод `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) используют средство синтаксического анализа [AngleSharp](https://anglesharp.github.io/) для обработки защиты от подделки с помощью следующих методов:</span><span class="sxs-lookup"><span data-stu-id="2cd95-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="2cd95-231">`GetDocumentAsync`. Получает [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) и возвращает `IHtmlDocument`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-231">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="2cd95-232">`GetDocumentAsync` использует фабрику, которая подготавливает *виртуальный ответ* на основе исходного `HttpResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="2cd95-233">Дополнительные сведения см. в [документации по AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="2cd95-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="2cd95-234">Методы расширения `SendAsync` для `HttpClient` составляют [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) и вызывают [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) для отправки запросов к ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="2cd95-235">Перегрузки для `SendAsync` принимают HTML-форму (`IHtmlFormElement`) и следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="2cd95-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="2cd95-236">Кнопка "Отправить" в форме (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="2cd95-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="2cd95-237">Коллекция значений формы (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2cd95-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="2cd95-238">Кнопка "Отправить" (`IHtmlElement`) и значения формы (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2cd95-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-239">[AngleSharp](https://anglesharp.github.io/) — это сторонняя библиотека анализа, используемая для демонстрационных целей в этом разделе и в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="2cd95-240">AngleSharp не поддерживается интеграционным тестированием приложением ASP.NET Core и не требуется для этого.</span><span class="sxs-lookup"><span data-stu-id="2cd95-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="2cd95-241">Можно использовать и другие средства синтаксического анализа, такие как [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="2cd95-242">Другой подход заключается в написании кода для непосредственной работы с маркером проверки запроса системы защиты от подделки и файлом cookie защиты от подделки.</span><span class="sxs-lookup"><span data-stu-id="2cd95-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="2cd95-243">Настройка клиента с помощью WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="2cd95-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="2cd95-244">Если в методе теста требуется дополнительная настройка, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) создает новую `WebApplicationFactory` с [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), которая дополнительно настроена в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2cd95-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="2cd95-245">Метод теста `Post_DeleteMessageHandler_ReturnsRedirectToRoot` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) демонстрирует использование `WithWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="2cd95-246">Этот тест выполняет удаление записи из базы данных, активируя отправку формы в ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="2cd95-247">Поскольку другой тест в классе `IndexPageTests` выполняет операцию, которая удаляет все записи из базы данных и может выполняться до метода `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, база данных повторно заполняется в этом методе теста, чтобы обеспечить наличие записи для ее удаления ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="2cd95-248">Выбор первой кнопки удаления в форме `messages` в ТС имитируется в запросе к ТС:</span><span class="sxs-lookup"><span data-stu-id="2cd95-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="2cd95-249">Параметры клиента</span><span class="sxs-lookup"><span data-stu-id="2cd95-249">Client options</span></span>

<span data-ttu-id="2cd95-250">В следующей таблице показаны [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) по умолчанию, доступные при создании экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="2cd95-251">Параметр</span><span class="sxs-lookup"><span data-stu-id="2cd95-251">Option</span></span> | <span data-ttu-id="2cd95-252">Описание</span><span class="sxs-lookup"><span data-stu-id="2cd95-252">Description</span></span> | <span data-ttu-id="2cd95-253">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2cd95-253">Default</span></span> |
| ---
<span data-ttu-id="2cd95-254">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-255">'Blazor'</span></span>
- <span data-ttu-id="2cd95-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-256">'Identity'</span></span>
- <span data-ttu-id="2cd95-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-258">'Razor'</span></span>
- <span data-ttu-id="2cd95-259">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-259">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-260">--- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-261">'Blazor'</span></span>
- <span data-ttu-id="2cd95-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-262">'Identity'</span></span>
- <span data-ttu-id="2cd95-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-264">'Razor'</span></span>
- <span data-ttu-id="2cd95-265">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-266">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-267">'Blazor'</span></span>
- <span data-ttu-id="2cd95-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-268">'Identity'</span></span>
- <span data-ttu-id="2cd95-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-270">'Razor'</span></span>
- <span data-ttu-id="2cd95-271">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-272">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-273">'Blazor'</span></span>
- <span data-ttu-id="2cd95-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-274">'Identity'</span></span>
- <span data-ttu-id="2cd95-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-276">'Razor'</span></span>
- <span data-ttu-id="2cd95-277">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-277">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-278">------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-279">'Blazor'</span></span>
- <span data-ttu-id="2cd95-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-280">'Identity'</span></span>
- <span data-ttu-id="2cd95-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-282">'Razor'</span></span>
- <span data-ttu-id="2cd95-283">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-283">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Возвращает или задает значение, указывающее, должны ли экземпляры `HttpClient` автоматически следовать ответам перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2cd95-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="2cd95-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Возвращает или задает базовый адрес экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="2cd95-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Возвращает или задает значение, указывающее, должны ли экземпляры `HttpClient` обрабатывать файлы cookie.</span><span class="sxs-lookup"><span data-stu-id="2cd95-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="2cd95-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Возвращает или задает максимальное число ответов на перенаправление, которым должны следовать экземпляры `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="2cd95-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="2cd95-288">| 7 |</span></span>

<span data-ttu-id="2cd95-289">Создайте класс `WebApplicationFactoryClientOptions` и передайте его в метод [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (значения по умолчанию показаны в примере кода):</span><span class="sxs-lookup"><span data-stu-id="2cd95-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="2cd95-290">Вставка служб имитации</span><span class="sxs-lookup"><span data-stu-id="2cd95-290">Inject mock services</span></span>

<span data-ttu-id="2cd95-291">Службы можно переопределить в тесте с помощью вызова [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) в построителе узлов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="2cd95-292">**Чтобы внедрить службы имитации, в ТС должен иметься класс `Startup` с методом `Startup.ConfigureServices`.**</span><span class="sxs-lookup"><span data-stu-id="2cd95-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="2cd95-293">Пример ТС включает службу с заданной областью, которая возвращает цитату.</span><span class="sxs-lookup"><span data-stu-id="2cd95-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="2cd95-294">Цитата внедряется в скрытое поле на странице индекса при запросе страницы индекса.</span><span class="sxs-lookup"><span data-stu-id="2cd95-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="2cd95-295">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="2cd95-296">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="2cd95-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="2cd95-298">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="2cd95-299">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="2cd95-300">При запуске приложения ТС создается следующая разметка:</span><span class="sxs-lookup"><span data-stu-id="2cd95-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="2cd95-301">Чтобы протестировать службу и внедрение цитат в интеграционный тест, служба имитации будет внедрена в ТС тестом.</span><span class="sxs-lookup"><span data-stu-id="2cd95-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="2cd95-302">Служба имитации заменяет `QuoteService` приложения службой, предоставляемой тестовым приложением, с именем `TestQuoteService`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="2cd95-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="2cd95-304">Вызывается `ConfigureTestServices` и регистрируется служба с заданной областью:</span><span class="sxs-lookup"><span data-stu-id="2cd95-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="2cd95-305">Разметка, созданная во время выполнения теста, отражает текст цитаты, предоставленный `TestQuoteService`, поэтому утверждение передается следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2cd95-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="2cd95-306">Имитация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2cd95-306">Mock authentication</span></span>

<span data-ttu-id="2cd95-307">Тесты в классе `AuthTests` проверяют, что безопасная конечная точка:</span><span class="sxs-lookup"><span data-stu-id="2cd95-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="2cd95-308">перенаправляет пользователя, не прошедшего проверку подлинности, на страницу входа;</span><span class="sxs-lookup"><span data-stu-id="2cd95-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="2cd95-309">возвращает содержимое для пользователя, прошедшего проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="2cd95-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="2cd95-310">В ТС на странице `/SecurePage` используется соглашение [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) для применения [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) на странице.</span><span class="sxs-lookup"><span data-stu-id="2cd95-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="2cd95-311">Дополнительные сведения см. в разделе [Соглашения проверки подлинности RazorPages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="2cd95-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="2cd95-312">В тесте `Get_SecurePageRedirectsAnUnauthenticatedUser` параметр [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) настроен на запрет перенаправления путем установки для параметра [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) значения `false`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="2cd95-313">Если запретить клиенту следовать перенаправлению, можно выполнить следующие проверки:</span><span class="sxs-lookup"><span data-stu-id="2cd95-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="2cd95-314">Код состояния, возвращаемый ТС, можно проверить по ожидаемому результату [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), а не по окончательному коду состояния после перенаправления на страницу входа, который будет [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="2cd95-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="2cd95-315">Значение заголовка `Location` в заголовках ответа проверяется, чтобы подтвердить, что он начинается с `http://localhost/Identity/Account/Login`, а не с последнего ответа страницы входа, где отсутствует заголовок `Location`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="2cd95-316">Тестовое приложение может имитировать макет <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> в [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) для тестирования аспектов проверки подлинности и авторизации.</span><span class="sxs-lookup"><span data-stu-id="2cd95-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="2cd95-317">В минимальном сценарии возвращается [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="2cd95-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="2cd95-318">Для проверки подлинности пользователя вызывается `TestAuthHandler`, если для схемы проверки подлинности задано `Test`, где `AddAuthentication` зарегистрировано для `ConfigureTestServices`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="2cd95-319">Дополнительные сведения о `WebApplicationFactoryClientOptions` см. в разделе [Параметры клиента](#client-options).</span><span class="sxs-lookup"><span data-stu-id="2cd95-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2cd95-320">Указание среды</span><span class="sxs-lookup"><span data-stu-id="2cd95-320">Set the environment</span></span>

<span data-ttu-id="2cd95-321">По умолчанию узел и среда приложений ТС настроены для использования среды Development.</span><span class="sxs-lookup"><span data-stu-id="2cd95-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="2cd95-322">Чтобы переопределить среду ТС при использовании `IHostBuilder`, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="2cd95-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="2cd95-323">Задайте переменную среды `ASPNETCORE_ENVIRONMENT` (например, `Staging`, `Production` или другое настраиваемое значение, например `Testing`).</span><span class="sxs-lookup"><span data-stu-id="2cd95-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="2cd95-324">Переопределите `CreateHostBuilder` в тестовом приложении, чтобы считать переменные среды с префиксом `ASPNETCORE`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="2cd95-325">Если ТС использует веб-узел (`IWebHostBuilder`), переопределите `CreateWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="2cd95-326">Определение тестовой инфраструктурой пути к корневому каталогу содержимого приложения</span><span class="sxs-lookup"><span data-stu-id="2cd95-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="2cd95-327">Конструктор `WebApplicationFactory` выводит путь к [корневому каталогу содержимого приложения](xref:fundamentals/index#content-root), выполняя поиск [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) в сборке, содержащей интеграционные тесты с ключом, равным сборке `TEntryPoint` `System.Reflection.Assembly.FullName`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="2cd95-328">Если атрибут с правильным ключом не найден, `WebApplicationFactory` возвращается к поиску файла решения (*SLN*) и добавляет имя сборки `TEntryPoint` в каталог решения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="2cd95-329">Корневой каталог приложения (корневой путь к содержимому) используется для обнаружения представлений и файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="2cd95-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="2cd95-330">Отключение теневого копирования</span><span class="sxs-lookup"><span data-stu-id="2cd95-330">Disable shadow copying</span></span>

<span data-ttu-id="2cd95-331">Теневое копирование приводит к тому, что тесты выполняются в каталоге, отличном от выходного каталога.</span><span class="sxs-lookup"><span data-stu-id="2cd95-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="2cd95-332">Для правильной работы тестов необходимо отключить теневое копирование.</span><span class="sxs-lookup"><span data-stu-id="2cd95-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="2cd95-333">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) использует xUnit и отключает теневое копирование для xUnit, включая файл *xunit.runner.json* с правильным параметром конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2cd95-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="2cd95-334">Дополнительные сведения см. в разделе [Настройка xUnit с помощью JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="2cd95-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="2cd95-335">Добавьте файл *xunit.runner.json* в корень тестового проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="2cd95-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="2cd95-336">Удаление объектов</span><span class="sxs-lookup"><span data-stu-id="2cd95-336">Disposal of objects</span></span>

<span data-ttu-id="2cd95-337">После выполнения тестов реализации `IClassFixture` [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) и [HttpClient](/dotnet/api/system.net.http.httpclient) удаляются, когда xUnit удаляет [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="2cd95-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="2cd95-338">Если объекты, создаваемые разработчиком, требуется удалить, удалите их в реализации `IClassFixture`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="2cd95-339">Дополнительные сведения см. в разделе [Реализация метода Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="2cd95-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="2cd95-340">Пример интеграционных тестов</span><span class="sxs-lookup"><span data-stu-id="2cd95-340">Integration tests sample</span></span>

<span data-ttu-id="2cd95-341">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) состоит из двух приложений:</span><span class="sxs-lookup"><span data-stu-id="2cd95-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="2cd95-342">Приложение</span><span class="sxs-lookup"><span data-stu-id="2cd95-342">App</span></span> | <span data-ttu-id="2cd95-343">каталог проекта;</span><span class="sxs-lookup"><span data-stu-id="2cd95-343">Project directory</span></span> | <span data-ttu-id="2cd95-344">Описание</span><span class="sxs-lookup"><span data-stu-id="2cd95-344">Description</span></span> |
| --- | ---
<span data-ttu-id="2cd95-345">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-346">'Blazor'</span></span>
- <span data-ttu-id="2cd95-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-347">'Identity'</span></span>
- <span data-ttu-id="2cd95-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-349">'Razor'</span></span>
- <span data-ttu-id="2cd95-350">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-351">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-352">'Blazor'</span></span>
- <span data-ttu-id="2cd95-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-353">'Identity'</span></span>
- <span data-ttu-id="2cd95-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-355">'Razor'</span></span>
- <span data-ttu-id="2cd95-356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-357">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-358">'Blazor'</span></span>
- <span data-ttu-id="2cd95-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-359">'Identity'</span></span>
- <span data-ttu-id="2cd95-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-361">'Razor'</span></span>
- <span data-ttu-id="2cd95-362">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-363">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-364">'Blazor'</span></span>
- <span data-ttu-id="2cd95-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-365">'Identity'</span></span>
- <span data-ttu-id="2cd95-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-367">'Razor'</span></span>
- <span data-ttu-id="2cd95-368">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-369">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-370">'Blazor'</span></span>
- <span data-ttu-id="2cd95-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-371">'Identity'</span></span>
- <span data-ttu-id="2cd95-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-373">'Razor'</span></span>
- <span data-ttu-id="2cd95-374">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-375">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-376">'Blazor'</span></span>
- <span data-ttu-id="2cd95-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-377">'Identity'</span></span>
- <span data-ttu-id="2cd95-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-379">'Razor'</span></span>
- <span data-ttu-id="2cd95-380">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-380">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-381">--------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-382">'Blazor'</span></span>
- <span data-ttu-id="2cd95-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-383">'Identity'</span></span>
- <span data-ttu-id="2cd95-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-385">'Razor'</span></span>
- <span data-ttu-id="2cd95-386">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-387">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-388">'Blazor'</span></span>
- <span data-ttu-id="2cd95-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-389">'Identity'</span></span>
- <span data-ttu-id="2cd95-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-391">'Razor'</span></span>
- <span data-ttu-id="2cd95-392">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-393">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-394">'Blazor'</span></span>
- <span data-ttu-id="2cd95-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-395">'Identity'</span></span>
- <span data-ttu-id="2cd95-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-397">'Razor'</span></span>
- <span data-ttu-id="2cd95-398">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-398">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-399">------ | | Приложение для сообщений (ТС) | *src/RazorPagesProject* | Позволяет пользователю добавлять, удалять сообщения (по одному или все) и анализировать их.</span><span class="sxs-lookup"><span data-stu-id="2cd95-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="2cd95-400">| | Тестовое приложение | *tests/RazorPagesProject.Tests* | Используется для тестирования интеграции ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="2cd95-401">Тесты можно выполнять с помощью встроенных функций тестирования интегрированной среды разработки, таких как [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="2cd95-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="2cd95-402">При использовании [Visual Studio Code](https://code.visualstudio.com/) или командной строки выполните следующую команду в командной строке в каталоге *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="2cd95-403">Организация приложения для сообщений (ТС)</span><span class="sxs-lookup"><span data-stu-id="2cd95-403">Message app (SUT) organization</span></span>

<span data-ttu-id="2cd95-404">Тестируемая система (ТС) — это система сообщений Razor Pages со следующими характеристиками:</span><span class="sxs-lookup"><span data-stu-id="2cd95-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2cd95-405">Страница индекса приложения (*Pages/Index.cshtml* и *Pages/Index.cshtml.cs*) предоставляет методы пользовательского интерфейса и модели страницы для управления добавлением, удалением и анализом сообщений (поиск среднего числа слов на сообщение).</span><span class="sxs-lookup"><span data-stu-id="2cd95-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="2cd95-406">Сообщение описывается классом `Message` (*Data/Message.cs*) с двумя свойствами: `Id` (ключ) и `Text` (сообщение).</span><span class="sxs-lookup"><span data-stu-id="2cd95-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2cd95-407">Свойство `Text` является обязательным и ограничено 200 символами.</span><span class="sxs-lookup"><span data-stu-id="2cd95-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2cd95-408">Сообщения хранятся с помощью [базы данных Entity Framework в памяти](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="2cd95-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2cd95-409">Приложение содержит слой доступа к данным DAL в своем классе контекста базы данных — `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="2cd95-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="2cd95-410">Если база данных пуста при запуске приложения, то хранилище сообщений инициализируется тремя сообщениями.</span><span class="sxs-lookup"><span data-stu-id="2cd95-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="2cd95-411">Приложение включает `/SecurePage`, доступ к которому может получить только пользователь, прошедший проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="2cd95-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="2cd95-412">&#8224;В разделе документации о EF [Тестирование с помощью InMemory](/ef/core/miscellaneous/testing/in-memory) объясняется, как использовать базу данных в памяти для тестов с помощью MSTest.</span><span class="sxs-lookup"><span data-stu-id="2cd95-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2cd95-413">В этом разделе используется платформа тестирования [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2cd95-414">Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.</span><span class="sxs-lookup"><span data-stu-id="2cd95-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2cd95-415">Несмотря на то, что приложение не использует шаблон репозитория и не является эффективным примером [шаблона "единица работы" (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages поддерживает такие шаблоны разработки.</span><span class="sxs-lookup"><span data-stu-id="2cd95-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2cd95-416">Дополнительные сведения см. в разделах [Проектирование уровня сохраняемости инфраструктуры](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) и [Логика контроллера тестирования](/aspnet/core/mvc/controllers/testing) (пример реализует шаблон репозитория).</span><span class="sxs-lookup"><span data-stu-id="2cd95-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="2cd95-417">Организация приложения для тестирования</span><span class="sxs-lookup"><span data-stu-id="2cd95-417">Test app organization</span></span>

<span data-ttu-id="2cd95-418">Тестовое приложение — это консольное приложение в папке *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="2cd95-419">Каталог тестового приложения</span><span class="sxs-lookup"><span data-stu-id="2cd95-419">Test app directory</span></span> | <span data-ttu-id="2cd95-420">Описание</span><span class="sxs-lookup"><span data-stu-id="2cd95-420">Description</span></span> |
| ---
<span data-ttu-id="2cd95-421">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-422">'Blazor'</span></span>
- <span data-ttu-id="2cd95-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-423">'Identity'</span></span>
- <span data-ttu-id="2cd95-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-425">'Razor'</span></span>
- <span data-ttu-id="2cd95-426">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-427">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-428">'Blazor'</span></span>
- <span data-ttu-id="2cd95-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-429">'Identity'</span></span>
- <span data-ttu-id="2cd95-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-431">'Razor'</span></span>
- <span data-ttu-id="2cd95-432">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-433">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-434">'Blazor'</span></span>
- <span data-ttu-id="2cd95-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-435">'Identity'</span></span>
- <span data-ttu-id="2cd95-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-437">'Razor'</span></span>
- <span data-ttu-id="2cd95-438">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-439">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-440">'Blazor'</span></span>
- <span data-ttu-id="2cd95-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-441">'Identity'</span></span>
- <span data-ttu-id="2cd95-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-443">'Razor'</span></span>
- <span data-ttu-id="2cd95-444">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-445">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-446">'Blazor'</span></span>
- <span data-ttu-id="2cd95-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-447">'Identity'</span></span>
- <span data-ttu-id="2cd95-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-449">'Razor'</span></span>
- <span data-ttu-id="2cd95-450">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-451">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-452">'Blazor'</span></span>
- <span data-ttu-id="2cd95-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-453">'Identity'</span></span>
- <span data-ttu-id="2cd95-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-455">'Razor'</span></span>
- <span data-ttu-id="2cd95-456">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-457">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-458">'Blazor'</span></span>
- <span data-ttu-id="2cd95-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-459">'Identity'</span></span>
- <span data-ttu-id="2cd95-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-461">'Razor'</span></span>
- <span data-ttu-id="2cd95-462">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-462">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-463">--------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-464">'Blazor'</span></span>
- <span data-ttu-id="2cd95-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-465">'Identity'</span></span>
- <span data-ttu-id="2cd95-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-467">'Razor'</span></span>
- <span data-ttu-id="2cd95-468">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-469">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-470">'Blazor'</span></span>
- <span data-ttu-id="2cd95-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-471">'Identity'</span></span>
- <span data-ttu-id="2cd95-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-473">'Razor'</span></span>
- <span data-ttu-id="2cd95-474">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-475">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-476">'Blazor'</span></span>
- <span data-ttu-id="2cd95-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-477">'Identity'</span></span>
- <span data-ttu-id="2cd95-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-479">'Razor'</span></span>
- <span data-ttu-id="2cd95-480">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-480">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-481">------ | | *AuthTests* | Содержит методы теста для:</span><span class="sxs-lookup"><span data-stu-id="2cd95-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="2cd95-482">доступа к защищенной странице пользователя, не прошедшего проверку подлинности;</span><span class="sxs-lookup"><span data-stu-id="2cd95-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="2cd95-483">доступа к защищенной странице пользователя, прошедшего проверку подлинности с помощью макета <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>;</span><span class="sxs-lookup"><span data-stu-id="2cd95-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="2cd95-484">получения профиля пользователя GitHub и проверки имени входа пользователя профиля.</span><span class="sxs-lookup"><span data-stu-id="2cd95-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="2cd95-485">| | *BasicTests* | Содержит метод теста для маршрутизации и типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="2cd95-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="2cd95-486">| | *IntegrationTests* | Содержит интеграционные тесты для страницы индекса с использованием настраиваемого класса `WebApplicationFactory`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="2cd95-487">*Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="2cd95-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="2cd95-488">*Utilities.cs* содержит метод `InitializeDbForTests`, используемый для заполнения базы данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="2cd95-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="2cd95-489">*HtmlHelpers.cs* предоставляет метод, возвращающий `IHtmlDocument` AngleSharp для использования методами теста.</span><span class="sxs-lookup"><span data-stu-id="2cd95-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="2cd95-490">*HttpClientExtensions.cs* предоставляет перегрузки для `SendAsync` для отправки запросов к ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="2cd95-491">Используемая платформа тестирования — [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2cd95-492">Интеграционные тесты выполняются с помощью [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), который включает [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="2cd95-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="2cd95-493">Поскольку пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) используется для настройки узла тестирования и тестового сервера, для пакетов `TestHost` и `TestServer` не требуются прямые ссылки на пакеты в файле проекта тестового приложения или конфигурации разработчика в тестовом приложении.</span><span class="sxs-lookup"><span data-stu-id="2cd95-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="2cd95-494">**Заполнение базы данных для тестирования**</span><span class="sxs-lookup"><span data-stu-id="2cd95-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="2cd95-495">Перед выполнением интеграционных тестов обычно требуется небольшой набор данных в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2cd95-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="2cd95-496">Например, при удалении теста вызывается удаление записей базы данных, поэтому в базе данных должна иметься по крайней мере одна запись, чтобы запрос на удаление был выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="2cd95-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="2cd95-497">Пример приложения заполняет базу данных тремя сообщениями в *Utilities.cs*, которые могут использоваться тестами при выполнении:</span><span class="sxs-lookup"><span data-stu-id="2cd95-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="2cd95-498">Контекст базы данных ТС регистрируется в методе `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2cd95-499">Обратный вызов `builder.ConfigureServices` тестового приложения выполняется *после* выполнения кода `Startup.ConfigureServices` приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="2cd95-500">Чтобы использовать для тестов другую базу данных, необходимо заменить контекст базы данных приложения в `builder.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="2cd95-501">Дополнительные сведения см. в разделе [Настройка WebApplicationFactory](#customize-webapplicationfactory).</span><span class="sxs-lookup"><span data-stu-id="2cd95-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="2cd95-502">В тестируемых системах, которые по-прежнему используют [веб-узел](xref:fundamentals/host/web-host), обратный вызов `builder.ConfigureServices` тестового приложения выполняется *до* выполнения кода `Startup.ConfigureServices` тестируемой системы.</span><span class="sxs-lookup"><span data-stu-id="2cd95-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="2cd95-503">Обратный вызов `builder.ConfigureTestServices` тестового приложения выполняется *позже*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2cd95-504">С помощью интеграционных тестов можно проверить работу компонентов приложения на уровне, который включает инфраструктуру, поддерживающую приложение, такую как база данных, файловая система и сеть.</span><span class="sxs-lookup"><span data-stu-id="2cd95-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="2cd95-505">ASP.NET Core поддерживает интеграционные тесты с помощью платформы модульного тестирования с тестовым веб-узлом и сервером тестирования в памяти.</span><span class="sxs-lookup"><span data-stu-id="2cd95-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="2cd95-506">В этом разделе предполагается базовое понимание модульных тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="2cd95-507">Если вы не знакомы с концепциями тестирования, ознакомьтесь с разделом [Модульное тестирование в .NET Core и .NET Standard](/dotnet/core/testing/) и связанным с ним содержимым.</span><span class="sxs-lookup"><span data-stu-id="2cd95-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="2cd95-508">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cd95-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2cd95-509">В качестве примера используется приложение Razor Pages (предполагается базовое понимание Razor Pages).</span><span class="sxs-lookup"><span data-stu-id="2cd95-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="2cd95-510">Если вы не знакомы с Razor Pages, см. следующие разделы:</span><span class="sxs-lookup"><span data-stu-id="2cd95-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="2cd95-511">[Введение в Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="2cd95-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="2cd95-512">[Начало работы с Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="2cd95-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="2cd95-513">[Модульные тесты Razor Pages](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="2cd95-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-514">Для тестирования одностраничных приложений мы рекомендуем инструмент типа [Selenium](https://www.seleniumhq.org/), с помощью которого можно автоматизировать браузер.</span><span class="sxs-lookup"><span data-stu-id="2cd95-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="2cd95-515">Общие сведения об интеграционных тестах</span><span class="sxs-lookup"><span data-stu-id="2cd95-515">Introduction to integration tests</span></span>

<span data-ttu-id="2cd95-516">Интеграционные тесты служат для оценки компонентов приложения на более широком уровне, чем [модульные тесты](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="2cd95-517">Модульные тесты используются для тестирования изолированных программных компонентов, таких как отдельные методы класса.</span><span class="sxs-lookup"><span data-stu-id="2cd95-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="2cd95-518">Интеграционные тесты позволяют убедиться, что два или несколько компонентов приложения работают совместно для получения ожидаемого результата, включая, возможно, все компоненты, необходимые для полной обработки запроса.</span><span class="sxs-lookup"><span data-stu-id="2cd95-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="2cd95-519">Эти более широкие тесты используются для тестирования инфраструктуры приложения и всей платформы, зачастую включая следующие компоненты:</span><span class="sxs-lookup"><span data-stu-id="2cd95-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="2cd95-520">База данных</span><span class="sxs-lookup"><span data-stu-id="2cd95-520">Database</span></span>
* <span data-ttu-id="2cd95-521">Файловая система</span><span class="sxs-lookup"><span data-stu-id="2cd95-521">File system</span></span>
* <span data-ttu-id="2cd95-522">Сетевые устройства</span><span class="sxs-lookup"><span data-stu-id="2cd95-522">Network appliances</span></span>
* <span data-ttu-id="2cd95-523">Конвейер "запрос-ответ"</span><span class="sxs-lookup"><span data-stu-id="2cd95-523">Request-response pipeline</span></span>

<span data-ttu-id="2cd95-524">В модульных тестах вместо компонентов инфраструктуры используются структурные компоненты, известные как *имитации* или *макеты объектов*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="2cd95-525">В отличие от модульных тестов, интеграционные тесты:</span><span class="sxs-lookup"><span data-stu-id="2cd95-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="2cd95-526">Используют фактические компоненты, которые приложение использует в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="2cd95-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="2cd95-527">Требуют больше кода и обработки данных.</span><span class="sxs-lookup"><span data-stu-id="2cd95-527">Require more code and data processing.</span></span>
* <span data-ttu-id="2cd95-528">Выполняются дольше.</span><span class="sxs-lookup"><span data-stu-id="2cd95-528">Take longer to run.</span></span>

<span data-ttu-id="2cd95-529">Поэтому используйте интеграционные тесты только для наиболее важных сценариев инфраструктуры.</span><span class="sxs-lookup"><span data-stu-id="2cd95-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="2cd95-530">Если поведение можно проверить с помощью модульного теста или интеграционного теста, выбирайте модульный тест.</span><span class="sxs-lookup"><span data-stu-id="2cd95-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="2cd95-531">Не создавайте интеграционные тесты для каждого возможного варианта доступа к базам данных и файловым системам.</span><span class="sxs-lookup"><span data-stu-id="2cd95-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="2cd95-532">В скольких бы местах приложение ни взаимодействовало с ними, фокусный набор интеграционных тестов на чтение, запись, обновление и удаление обычно способен адекватно протестировать их компоненты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="2cd95-533">Используйте модульные тесты для стандартных тестов логики методов, взаимодействующих с этими компонентами.</span><span class="sxs-lookup"><span data-stu-id="2cd95-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="2cd95-534">В модульных тестах использование инфраструктурных имитаций приводит к более быстрому выполнению теста.</span><span class="sxs-lookup"><span data-stu-id="2cd95-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-535">При обсуждении интеграционных тестов тестируемый проект часто называется *тестируемой системой* или "ТС".</span><span class="sxs-lookup"><span data-stu-id="2cd95-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="2cd95-536">*В этом разделе используется "ТС" для ссылки на проверенное приложение ASP.NET Core.*</span><span class="sxs-lookup"><span data-stu-id="2cd95-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="2cd95-537">Интеграционные тесты ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2cd95-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="2cd95-538">Для интеграционных тестов в ASP.NET Core требуется следующее:</span><span class="sxs-lookup"><span data-stu-id="2cd95-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="2cd95-539">Тестовый проект, который используется для хранения и выполнения тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="2cd95-540">Тестовый проект содержит ссылку на ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="2cd95-541">Тестовый проект создает тестовый веб-узел для ТС и использует клиент тестового сервера для обработки запросов и ответов с помощью ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="2cd95-542">Средство запуска тестов используется для выполнения тестов и передачи результатов тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="2cd95-543">Интеграционные тесты придерживаются последовательности событий из обычных шагов теста *Подготовка*, *Выполнение* и *Проверка*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="2cd95-544">Настраивается веб-узел ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="2cd95-545">Создается клиент тестового сервера для отправки запросов к приложению.</span><span class="sxs-lookup"><span data-stu-id="2cd95-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="2cd95-546">Выполняется шаг теста *Подготовка*: тестовое приложение готовит запрос.</span><span class="sxs-lookup"><span data-stu-id="2cd95-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="2cd95-547">Выполняется шаг теста *Выполнение*: клиент отправляет запрос и получает ответ.</span><span class="sxs-lookup"><span data-stu-id="2cd95-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="2cd95-548">Выполняется шаг теста *Проверка*: *фактический* ответ определяется как *правильный* или *ошибочный* на основе *ожидаемого* ответа.</span><span class="sxs-lookup"><span data-stu-id="2cd95-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="2cd95-549">Процесс продолжается до тех пор, пока не будут выполнены все тесты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="2cd95-550">Выводятся результаты теста.</span><span class="sxs-lookup"><span data-stu-id="2cd95-550">The test results are reported.</span></span>

<span data-ttu-id="2cd95-551">Как правило, тестовый веб-узел настраивается отлично от обычного веб-узла приложения для тестовых запусков.</span><span class="sxs-lookup"><span data-stu-id="2cd95-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="2cd95-552">Например, для тестов может использоваться другая база данных или другие параметры приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="2cd95-553">Компоненты инфраструктуры, такие как тестовый веб-узел и сервер тестирования в памяти ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), предоставляются или управляются пакетом [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing).</span><span class="sxs-lookup"><span data-stu-id="2cd95-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="2cd95-554">Использование этого пакета упрощает создание и выполнение тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="2cd95-555">Пакет `Microsoft.AspNetCore.Mvc.Testing` выполняет следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="2cd95-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="2cd95-556">Копирует файл зависимостей (*DEPS*) из ТС в папку *bin* тестового проекта.</span><span class="sxs-lookup"><span data-stu-id="2cd95-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="2cd95-557">Задает [корневой каталог](xref:fundamentals/index#content-root) содержимого в корне проекта ТС, чтобы при выполнении тестов были найдены статические файлы и страницы или представления.</span><span class="sxs-lookup"><span data-stu-id="2cd95-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="2cd95-558">Предоставляет класс [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) для упрощения начальной загрузки ТС с `TestServer`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="2cd95-559">В документации [модульные тесты](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) описано, как настроить тестовый проект и средство запуска тестов, а также представлены подробные инструкции по выполнению тестов и рекомендаций по именованию тестов и тестовых классов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-560">При создании тестового проекта для приложения отделяйте модульные тесты от интеграционных, помещая их в разные проекты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="2cd95-561">Это гарантирует, что компоненты тестирования инфраструктуры не будут случайно добавлены в модульные тесты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="2cd95-562">Разделение модульных и интеграционных тестов также позволяет контролировать, какой набор тестов выполняется.</span><span class="sxs-lookup"><span data-stu-id="2cd95-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="2cd95-563">В сущности, между конфигурацией для тестов приложений Razor Pages и приложений MVC нет никаких отличий.</span><span class="sxs-lookup"><span data-stu-id="2cd95-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="2cd95-564">Единственное отличие заключается в том, как именуются тесты.</span><span class="sxs-lookup"><span data-stu-id="2cd95-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="2cd95-565">В приложении Razor Pages тесты конечных точек страницы обычно именуются по классу модели страницы (например, `IndexPageTests` для тестирования интеграции компонентов на странице индекса).</span><span class="sxs-lookup"><span data-stu-id="2cd95-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="2cd95-566">В приложении MVC тесты обычно организованы по классам контроллеров и именуются по контроллеру, который они проверяют (например, `HomeControllerTests` для тестирования интеграции компонентов на контроллере Home).</span><span class="sxs-lookup"><span data-stu-id="2cd95-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="2cd95-567">Проверка необходимых требований к приложению</span><span class="sxs-lookup"><span data-stu-id="2cd95-567">Test app prerequisites</span></span>

<span data-ttu-id="2cd95-568">Тестовый проект должен выполнять следующие требования.</span><span class="sxs-lookup"><span data-stu-id="2cd95-568">The test project must:</span></span>

* <span data-ttu-id="2cd95-569">Ссылаться на следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="2cd95-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="2cd95-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2cd95-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="2cd95-571">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="2cd95-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="2cd95-572">Указывать веб-пакет SDK в файле проекта (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span><span class="sxs-lookup"><span data-stu-id="2cd95-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="2cd95-573">Веб-пакет SDK требуется при ссылке на [метапакет Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2cd95-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2cd95-574">Выполнение необходимых требований можно посмотреть в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="2cd95-575">Изучите файл *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="2cd95-576">В примере приложения используются платформа тестирования [xUnit](https://xunit.github.io/) и библиотека средства синтаксического анализа [AngleSharp](https://anglesharp.github.io/), поэтому он также ссылается на:</span><span class="sxs-lookup"><span data-stu-id="2cd95-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="2cd95-577">xunit</span><span class="sxs-lookup"><span data-stu-id="2cd95-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="2cd95-578">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="2cd95-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="2cd95-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="2cd95-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="2cd95-580">Среда ТС</span><span class="sxs-lookup"><span data-stu-id="2cd95-580">SUT environment</span></span>

<span data-ttu-id="2cd95-581">Если [среда](xref:fundamentals/environments) ТС не задана, то по умолчанию среда имеет значение Development.</span><span class="sxs-lookup"><span data-stu-id="2cd95-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="2cd95-582">Базовые тесты со стандартной WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="2cd95-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="2cd95-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) используется для создания [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) для интеграционных тестов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="2cd95-584">`TEntryPoint` — это класс точки входа ТС, обычно класс `Startup`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="2cd95-585">Тестовые классы реализуют интерфейс *средства тестирования* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), чтобы указать, что класс содержит тесты, и предоставить экземпляры общего объекта между тестами в классе.</span><span class="sxs-lookup"><span data-stu-id="2cd95-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="2cd95-586">Следующий тестовый класс, `BasicTests`, использует `WebApplicationFactory` для начальной загрузки ТС и предоставляет [HttpClient](/dotnet/api/system.net.http.httpclient) тестовому методу `Get_EndpointsReturnSuccessAndCorrectContentType`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="2cd95-587">Метод проверяет, что код состояния ответа свидетельствует о выполнении (коды состояния в диапазоне от 200 до 299) и что заголовок `Content-Type` имеет значение `text/html; charset=utf-8` для нескольких страниц приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="2cd95-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) создает экземпляр класса `HttpClient`, который автоматически следует за перенаправлениями и обрабатывает файлы cookie.</span><span class="sxs-lookup"><span data-stu-id="2cd95-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="2cd95-589">По умолчанию некритические файлы cookie не сохраняются в запросах, если включена [политика согласия GDPR](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="2cd95-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="2cd95-590">Чтобы сохранить ненужные файлы cookie, например те, которые используются поставщиком TempData, пометьте их как основные в тестах.</span><span class="sxs-lookup"><span data-stu-id="2cd95-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="2cd95-591">Инструкции по маркировке файла cookie в качестве основы см. в разделе [Основные файлы cookie](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="2cd95-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="2cd95-592">Настройка WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="2cd95-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="2cd95-593">Конфигурацию веб-узла можно создать независимо от тестовых классов путем наследования от `WebApplicationFactory` для создания одной или нескольких пользовательских фабрик:</span><span class="sxs-lookup"><span data-stu-id="2cd95-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="2cd95-594">Выполните наследование от `WebApplicationFactory` и переопределите [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="2cd95-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="2cd95-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) позволяет настраивать коллекцию служб с помощью команды [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), которая выполняется перед командой `Startup.ConfigureServices` приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2cd95-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) позволяет переопределять и изменять зарегистрированные службы в коллекции служб с помощью команды [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices).</span><span class="sxs-lookup"><span data-stu-id="2cd95-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="2cd95-597">Заполнение базы данных в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) выполняется методом `InitializeDbForTests`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="2cd95-598">Этот метод описан в разделе [Пример интеграционных тестов: организация приложения для тестирования](#test-app-organization).</span><span class="sxs-lookup"><span data-stu-id="2cd95-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="2cd95-599">Используйте настраиваемый `CustomWebApplicationFactory` в тестовых классах.</span><span class="sxs-lookup"><span data-stu-id="2cd95-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="2cd95-600">В следующем примере используется фабрика в классе `IndexPageTests`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="2cd95-601">Клиент примера приложения настроен так, чтобы не допустить выполнение клиентов `HttpClient` следующих перенаправлений.</span><span class="sxs-lookup"><span data-stu-id="2cd95-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="2cd95-602">Как описано далее в разделе [Имитация проверки подлинности](#mock-authentication), это позволяет тестам проверять результат первого ответа приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="2cd95-603">Первый ответ — это перенаправление во многих из этих тестов с заголовком `Location`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="2cd95-604">Обычный тест использует `HttpClient` и вспомогательные методы для обработки запроса и ответа:</span><span class="sxs-lookup"><span data-stu-id="2cd95-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2cd95-605">Любой запрос POST к ТС должен соответствовать проверке защиты от подделки, которая автоматически вносится в [систему защиты данных от подделки](xref:security/data-protection/introduction) приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2cd95-606">Чтобы упорядочить запрос POST теста, тестовое приложение должно:</span><span class="sxs-lookup"><span data-stu-id="2cd95-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="2cd95-607">Выполнить запрос к странице.</span><span class="sxs-lookup"><span data-stu-id="2cd95-607">Make a request for the page.</span></span>
1. <span data-ttu-id="2cd95-608">Проанализировать файл cookie для защиты от подделки и запросить маркер проверки из ответа.</span><span class="sxs-lookup"><span data-stu-id="2cd95-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="2cd95-609">Выполните запрос POST с файлом cookie для защиты от подделки и запросом маркера проверки на месте.</span><span class="sxs-lookup"><span data-stu-id="2cd95-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="2cd95-610">Вспомогательные методы расширения `SendAsync` (*Helpers/HttpClientExtensions.cs*) и вспомогательный метод `GetDocumentAsync` (*Helpers/HtmlHelpers.cs*) в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) используют средство синтаксического анализа [AngleSharp](https://anglesharp.github.io/) для обработки защиты от подделки с помощью следующих методов:</span><span class="sxs-lookup"><span data-stu-id="2cd95-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="2cd95-611">`GetDocumentAsync`. Получает [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) и возвращает `IHtmlDocument`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-611">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="2cd95-612">`GetDocumentAsync` использует фабрику, которая подготавливает *виртуальный ответ* на основе исходного `HttpResponseMessage`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="2cd95-613">Дополнительные сведения см. в [документации по AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="2cd95-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="2cd95-614">Методы расширения `SendAsync` для `HttpClient` составляют [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) и вызывают [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) для отправки запросов к ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="2cd95-615">Перегрузки для `SendAsync` принимают HTML-форму (`IHtmlFormElement`) и следующие элементы:</span><span class="sxs-lookup"><span data-stu-id="2cd95-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="2cd95-616">Кнопка "Отправить" в форме (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="2cd95-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="2cd95-617">Коллекция значений формы (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2cd95-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="2cd95-618">Кнопка "Отправить" (`IHtmlElement`) и значения формы (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2cd95-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="2cd95-619">[AngleSharp](https://anglesharp.github.io/) — это сторонняя библиотека анализа, используемая для демонстрационных целей в этом разделе и в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="2cd95-620">AngleSharp не поддерживается интеграционным тестированием приложением ASP.NET Core и не требуется для этого.</span><span class="sxs-lookup"><span data-stu-id="2cd95-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="2cd95-621">Можно использовать и другие средства синтаксического анализа, такие как [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="2cd95-622">Другой подход заключается в написании кода для непосредственной работы с маркером проверки запроса системы защиты от подделки и файлом cookie защиты от подделки.</span><span class="sxs-lookup"><span data-stu-id="2cd95-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="2cd95-623">Настройка клиента с помощью WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="2cd95-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="2cd95-624">Если в методе теста требуется дополнительная настройка, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) создает новую `WebApplicationFactory` с [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), которая дополнительно настроена в конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2cd95-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="2cd95-625">Метод теста `Post_DeleteMessageHandler_ReturnsRedirectToRoot` в [примере приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) демонстрирует использование `WithWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="2cd95-626">Этот тест выполняет удаление записи из базы данных, активируя отправку формы в ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="2cd95-627">Поскольку другой тест в классе `IndexPageTests` выполняет операцию, которая удаляет все записи из базы данных и может выполняться до метода `Post_DeleteMessageHandler_ReturnsRedirectToRoot`, база данных повторно заполняется в этом методе теста, чтобы обеспечить наличие записи для ее удаления ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="2cd95-628">Выбор первой кнопки удаления в форме `messages` в ТС имитируется в запросе к ТС:</span><span class="sxs-lookup"><span data-stu-id="2cd95-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="2cd95-629">Параметры клиента</span><span class="sxs-lookup"><span data-stu-id="2cd95-629">Client options</span></span>

<span data-ttu-id="2cd95-630">В следующей таблице показаны [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) по умолчанию, доступные при создании экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="2cd95-631">Параметр</span><span class="sxs-lookup"><span data-stu-id="2cd95-631">Option</span></span> | <span data-ttu-id="2cd95-632">Описание</span><span class="sxs-lookup"><span data-stu-id="2cd95-632">Description</span></span> | <span data-ttu-id="2cd95-633">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="2cd95-633">Default</span></span> |
| ---
<span data-ttu-id="2cd95-634">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-635">'Blazor'</span></span>
- <span data-ttu-id="2cd95-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-636">'Identity'</span></span>
- <span data-ttu-id="2cd95-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-638">'Razor'</span></span>
- <span data-ttu-id="2cd95-639">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-639">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-640">--- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-641">'Blazor'</span></span>
- <span data-ttu-id="2cd95-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-642">'Identity'</span></span>
- <span data-ttu-id="2cd95-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-644">'Razor'</span></span>
- <span data-ttu-id="2cd95-645">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-646">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-647">'Blazor'</span></span>
- <span data-ttu-id="2cd95-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-648">'Identity'</span></span>
- <span data-ttu-id="2cd95-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-650">'Razor'</span></span>
- <span data-ttu-id="2cd95-651">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-652">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-653">'Blazor'</span></span>
- <span data-ttu-id="2cd95-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-654">'Identity'</span></span>
- <span data-ttu-id="2cd95-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-656">'Razor'</span></span>
- <span data-ttu-id="2cd95-657">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-657">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-658">------ | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-659">'Blazor'</span></span>
- <span data-ttu-id="2cd95-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-660">'Identity'</span></span>
- <span data-ttu-id="2cd95-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-662">'Razor'</span></span>
- <span data-ttu-id="2cd95-663">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-663">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Возвращает или задает значение, указывающее, должны ли экземпляры `HttpClient` автоматически следовать ответам перенаправления.</span><span class="sxs-lookup"><span data-stu-id="2cd95-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="2cd95-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Возвращает или задает базовый адрес экземпляров `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="2cd95-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Возвращает или задает значение, указывающее, должны ли экземпляры `HttpClient` обрабатывать файлы cookie.</span><span class="sxs-lookup"><span data-stu-id="2cd95-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="2cd95-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Возвращает или задает максимальное число ответов на перенаправление, которым должны следовать экземпляры `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="2cd95-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="2cd95-668">| 7 |</span></span>

<span data-ttu-id="2cd95-669">Создайте класс `WebApplicationFactoryClientOptions` и передайте его в метод [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (значения по умолчанию показаны в примере кода):</span><span class="sxs-lookup"><span data-stu-id="2cd95-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="2cd95-670">Вставка служб имитации</span><span class="sxs-lookup"><span data-stu-id="2cd95-670">Inject mock services</span></span>

<span data-ttu-id="2cd95-671">Службы можно переопределить в тесте с помощью вызова [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) в построителе узлов.</span><span class="sxs-lookup"><span data-stu-id="2cd95-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="2cd95-672">**Чтобы внедрить службы имитации, в ТС должен иметься класс `Startup` с методом `Startup.ConfigureServices`.**</span><span class="sxs-lookup"><span data-stu-id="2cd95-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="2cd95-673">Пример ТС включает службу с заданной областью, которая возвращает цитату.</span><span class="sxs-lookup"><span data-stu-id="2cd95-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="2cd95-674">Цитата внедряется в скрытое поле на странице индекса при запросе страницы индекса.</span><span class="sxs-lookup"><span data-stu-id="2cd95-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="2cd95-675">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="2cd95-676">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="2cd95-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="2cd95-678">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="2cd95-679">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="2cd95-680">При запуске приложения ТС создается следующая разметка:</span><span class="sxs-lookup"><span data-stu-id="2cd95-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="2cd95-681">Чтобы протестировать службу и внедрение цитат в интеграционный тест, служба имитации будет внедрена в ТС тестом.</span><span class="sxs-lookup"><span data-stu-id="2cd95-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="2cd95-682">Служба имитации заменяет `QuoteService` приложения службой, предоставляемой тестовым приложением, с именем `TestQuoteService`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="2cd95-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="2cd95-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="2cd95-684">Вызывается `ConfigureTestServices` и регистрируется служба с заданной областью:</span><span class="sxs-lookup"><span data-stu-id="2cd95-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="2cd95-685">Разметка, созданная во время выполнения теста, отражает текст цитаты, предоставленный `TestQuoteService`, поэтому утверждение передается следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2cd95-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="2cd95-686">Имитация проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="2cd95-686">Mock authentication</span></span>

<span data-ttu-id="2cd95-687">Тесты в классе `AuthTests` проверяют, что безопасная конечная точка:</span><span class="sxs-lookup"><span data-stu-id="2cd95-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="2cd95-688">перенаправляет пользователя, не прошедшего проверку подлинности, на страницу входа;</span><span class="sxs-lookup"><span data-stu-id="2cd95-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="2cd95-689">возвращает содержимое для пользователя, прошедшего проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="2cd95-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="2cd95-690">В ТС на странице `/SecurePage` используется соглашение [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) для применения [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) на странице.</span><span class="sxs-lookup"><span data-stu-id="2cd95-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="2cd95-691">Дополнительные сведения см. в разделе [Соглашения проверки подлинности RazorPages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="2cd95-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="2cd95-692">В тесте `Get_SecurePageRedirectsAnUnauthenticatedUser` параметр [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) настроен на запрет перенаправления путем установки для параметра [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) значения `false`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="2cd95-693">Если запретить клиенту следовать перенаправлению, можно выполнить следующие проверки:</span><span class="sxs-lookup"><span data-stu-id="2cd95-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="2cd95-694">Код состояния, возвращаемый ТС, можно проверить по ожидаемому результату [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode), а не по окончательному коду состояния после перенаправления на страницу входа, который будет [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="2cd95-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="2cd95-695">Значение заголовка `Location` в заголовках ответа проверяется, чтобы подтвердить, что он начинается с `http://localhost/Identity/Account/Login`, а не с последнего ответа страницы входа, где отсутствует заголовок `Location`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="2cd95-696">Тестовое приложение может имитировать макет <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> в [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) для тестирования аспектов проверки подлинности и авторизации.</span><span class="sxs-lookup"><span data-stu-id="2cd95-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="2cd95-697">В минимальном сценарии возвращается [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span><span class="sxs-lookup"><span data-stu-id="2cd95-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="2cd95-698">Для проверки подлинности пользователя вызывается `TestAuthHandler`, если для схемы проверки подлинности задано `Test`, где `AddAuthentication` зарегистрировано для `ConfigureTestServices`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="2cd95-699">Дополнительные сведения о `WebApplicationFactoryClientOptions` см. в разделе [Параметры клиента](#client-options).</span><span class="sxs-lookup"><span data-stu-id="2cd95-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2cd95-700">Указание среды</span><span class="sxs-lookup"><span data-stu-id="2cd95-700">Set the environment</span></span>

<span data-ttu-id="2cd95-701">По умолчанию узел и среда приложений ТС настроены для использования среды Development.</span><span class="sxs-lookup"><span data-stu-id="2cd95-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="2cd95-702">Чтобы переопределить среду ТС, выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="2cd95-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="2cd95-703">Задайте переменную среды `ASPNETCORE_ENVIRONMENT` (например, `Staging`, `Production` или другое настраиваемое значение, например `Testing`).</span><span class="sxs-lookup"><span data-stu-id="2cd95-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="2cd95-704">Переопределите `CreateWebHostBuilder` в тестовом приложении, чтобы считать переменные среды `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="2cd95-705">Среду также можно задать непосредственно в построителе узлов в пользовательском классе <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>.</span><span class="sxs-lookup"><span data-stu-id="2cd95-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="2cd95-706">Определение тестовой инфраструктурой пути к корневому каталогу содержимого приложения</span><span class="sxs-lookup"><span data-stu-id="2cd95-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="2cd95-707">Конструктор `WebApplicationFactory` выводит путь к [корневому каталогу содержимого приложения](xref:fundamentals/index#content-root), выполняя поиск [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) в сборке, содержащей интеграционные тесты с ключом, равным сборке `TEntryPoint` `System.Reflection.Assembly.FullName`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="2cd95-708">Если атрибут с правильным ключом не найден, `WebApplicationFactory` возвращается к поиску файла решения (*SLN*) и добавляет имя сборки `TEntryPoint` в каталог решения.</span><span class="sxs-lookup"><span data-stu-id="2cd95-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="2cd95-709">Корневой каталог приложения (корневой путь к содержимому) используется для обнаружения представлений и файлов содержимого.</span><span class="sxs-lookup"><span data-stu-id="2cd95-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="2cd95-710">Отключение теневого копирования</span><span class="sxs-lookup"><span data-stu-id="2cd95-710">Disable shadow copying</span></span>

<span data-ttu-id="2cd95-711">Теневое копирование приводит к тому, что тесты выполняются в каталоге, отличном от выходного каталога.</span><span class="sxs-lookup"><span data-stu-id="2cd95-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="2cd95-712">Для правильной работы тестов необходимо отключить теневое копирование.</span><span class="sxs-lookup"><span data-stu-id="2cd95-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="2cd95-713">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) использует xUnit и отключает теневое копирование для xUnit, включая файл *xunit.runner.json* с правильным параметром конфигурации.</span><span class="sxs-lookup"><span data-stu-id="2cd95-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="2cd95-714">Дополнительные сведения см. в разделе [Настройка xUnit с помощью JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="2cd95-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="2cd95-715">Добавьте файл *xunit.runner.json* в корень тестового проекта со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="2cd95-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="2cd95-716">Если используется Microsoft Visual Studio, установите для свойства **Копировать в выходной каталог** значение **Всегда копировать**.</span><span class="sxs-lookup"><span data-stu-id="2cd95-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="2cd95-717">Если вы не используете Microsoft Visual Studio, добавьте в файл проекта тестового приложения целевой объект `Content`:</span><span class="sxs-lookup"><span data-stu-id="2cd95-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="2cd95-718">Удаление объектов</span><span class="sxs-lookup"><span data-stu-id="2cd95-718">Disposal of objects</span></span>

<span data-ttu-id="2cd95-719">После выполнения тестов реализации `IClassFixture` [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) и [HttpClient](/dotnet/api/system.net.http.httpclient) удаляются, когда xUnit удаляет [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="2cd95-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="2cd95-720">Если объекты, создаваемые разработчиком, требуется удалить, удалите их в реализации `IClassFixture`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="2cd95-721">Дополнительные сведения см. в разделе [Реализация метода Dispose](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="2cd95-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="2cd95-722">Пример интеграционных тестов</span><span class="sxs-lookup"><span data-stu-id="2cd95-722">Integration tests sample</span></span>

<span data-ttu-id="2cd95-723">[Пример приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) состоит из двух приложений:</span><span class="sxs-lookup"><span data-stu-id="2cd95-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="2cd95-724">Приложение</span><span class="sxs-lookup"><span data-stu-id="2cd95-724">App</span></span> | <span data-ttu-id="2cd95-725">каталог проекта;</span><span class="sxs-lookup"><span data-stu-id="2cd95-725">Project directory</span></span> | <span data-ttu-id="2cd95-726">Описание</span><span class="sxs-lookup"><span data-stu-id="2cd95-726">Description</span></span> |
| --- | ---
<span data-ttu-id="2cd95-727">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-728">'Blazor'</span></span>
- <span data-ttu-id="2cd95-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-729">'Identity'</span></span>
- <span data-ttu-id="2cd95-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-731">'Razor'</span></span>
- <span data-ttu-id="2cd95-732">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-733">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-734">'Blazor'</span></span>
- <span data-ttu-id="2cd95-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-735">'Identity'</span></span>
- <span data-ttu-id="2cd95-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-737">'Razor'</span></span>
- <span data-ttu-id="2cd95-738">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-739">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-740">'Blazor'</span></span>
- <span data-ttu-id="2cd95-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-741">'Identity'</span></span>
- <span data-ttu-id="2cd95-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-743">'Razor'</span></span>
- <span data-ttu-id="2cd95-744">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-745">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-746">'Blazor'</span></span>
- <span data-ttu-id="2cd95-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-747">'Identity'</span></span>
- <span data-ttu-id="2cd95-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-749">'Razor'</span></span>
- <span data-ttu-id="2cd95-750">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-751">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-752">'Blazor'</span></span>
- <span data-ttu-id="2cd95-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-753">'Identity'</span></span>
- <span data-ttu-id="2cd95-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-755">'Razor'</span></span>
- <span data-ttu-id="2cd95-756">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-757">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-758">'Blazor'</span></span>
- <span data-ttu-id="2cd95-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-759">'Identity'</span></span>
- <span data-ttu-id="2cd95-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-761">'Razor'</span></span>
- <span data-ttu-id="2cd95-762">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-762">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-763">--------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-764">'Blazor'</span></span>
- <span data-ttu-id="2cd95-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-765">'Identity'</span></span>
- <span data-ttu-id="2cd95-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-767">'Razor'</span></span>
- <span data-ttu-id="2cd95-768">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-769">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-770">'Blazor'</span></span>
- <span data-ttu-id="2cd95-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-771">'Identity'</span></span>
- <span data-ttu-id="2cd95-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-773">'Razor'</span></span>
- <span data-ttu-id="2cd95-774">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-775">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-776">'Blazor'</span></span>
- <span data-ttu-id="2cd95-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-777">'Identity'</span></span>
- <span data-ttu-id="2cd95-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-779">'Razor'</span></span>
- <span data-ttu-id="2cd95-780">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-780">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-781">------ | | Приложение для сообщений (ТС) | *src/RazorPagesProject* | Позволяет пользователю добавлять, удалять сообщения (по одному или все) и анализировать их.</span><span class="sxs-lookup"><span data-stu-id="2cd95-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="2cd95-782">| | Тестовое приложение | *tests/RazorPagesProject.Tests* | Используется для тестирования интеграции ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="2cd95-783">Тесты можно выполнять с помощью встроенных функций тестирования интегрированной среды разработки, таких как [Visual Studio](https://visualstudio.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="2cd95-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="2cd95-784">При использовании [Visual Studio Code](https://code.visualstudio.com/) или командной строки выполните следующую команду в командной строке в каталоге *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="2cd95-785">Организация приложения для сообщений (ТС)</span><span class="sxs-lookup"><span data-stu-id="2cd95-785">Message app (SUT) organization</span></span>

<span data-ttu-id="2cd95-786">Тестируемая система (ТС) — это система сообщений Razor Pages со следующими характеристиками:</span><span class="sxs-lookup"><span data-stu-id="2cd95-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2cd95-787">Страница индекса приложения (*Pages/Index.cshtml* и *Pages/Index.cshtml.cs*) предоставляет методы пользовательского интерфейса и модели страницы для управления добавлением, удалением и анализом сообщений (поиск среднего числа слов на сообщение).</span><span class="sxs-lookup"><span data-stu-id="2cd95-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="2cd95-788">Сообщение описывается классом `Message` (*Data/Message.cs*) с двумя свойствами: `Id` (ключ) и `Text` (сообщение).</span><span class="sxs-lookup"><span data-stu-id="2cd95-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2cd95-789">Свойство `Text` является обязательным и ограничено 200 символами.</span><span class="sxs-lookup"><span data-stu-id="2cd95-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2cd95-790">Сообщения хранятся с помощью [базы данных Entity Framework в памяти](/ef/core/providers/in-memory/)&#8224;.</span><span class="sxs-lookup"><span data-stu-id="2cd95-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2cd95-791">Приложение содержит слой доступа к данным DAL в своем классе контекста базы данных — `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="2cd95-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="2cd95-792">Если база данных пуста при запуске приложения, то хранилище сообщений инициализируется тремя сообщениями.</span><span class="sxs-lookup"><span data-stu-id="2cd95-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="2cd95-793">Приложение включает `/SecurePage`, доступ к которому может получить только пользователь, прошедший проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="2cd95-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="2cd95-794">&#8224;В разделе документации о EF [Тестирование с помощью InMemory](/ef/core/miscellaneous/testing/in-memory) объясняется, как использовать базу данных в памяти для тестов с помощью MSTest.</span><span class="sxs-lookup"><span data-stu-id="2cd95-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2cd95-795">В этом разделе используется платформа тестирования [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2cd95-796">Концепции тестирования и реализации тестов в разных платформах тестирования похожи, но не идентичны.</span><span class="sxs-lookup"><span data-stu-id="2cd95-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2cd95-797">Несмотря на то, что приложение не использует шаблон репозитория и не является эффективным примером [шаблона "единица работы" (UoW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages поддерживает такие шаблоны разработки.</span><span class="sxs-lookup"><span data-stu-id="2cd95-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2cd95-798">Дополнительные сведения см. в разделах [Проектирование уровня сохраняемости инфраструктуры](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) и [Логика контроллера тестирования](/aspnet/core/mvc/controllers/testing) (пример реализует шаблон репозитория).</span><span class="sxs-lookup"><span data-stu-id="2cd95-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="2cd95-799">Организация приложения для тестирования</span><span class="sxs-lookup"><span data-stu-id="2cd95-799">Test app organization</span></span>

<span data-ttu-id="2cd95-800">Тестовое приложение — это консольное приложение в папке *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="2cd95-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="2cd95-801">Каталог тестового приложения</span><span class="sxs-lookup"><span data-stu-id="2cd95-801">Test app directory</span></span> | <span data-ttu-id="2cd95-802">Описание</span><span class="sxs-lookup"><span data-stu-id="2cd95-802">Description</span></span> |
| ---
<span data-ttu-id="2cd95-803">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-804">'Blazor'</span></span>
- <span data-ttu-id="2cd95-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-805">'Identity'</span></span>
- <span data-ttu-id="2cd95-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-807">'Razor'</span></span>
- <span data-ttu-id="2cd95-808">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-809">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-810">'Blazor'</span></span>
- <span data-ttu-id="2cd95-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-811">'Identity'</span></span>
- <span data-ttu-id="2cd95-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-813">'Razor'</span></span>
- <span data-ttu-id="2cd95-814">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-815">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-816">'Blazor'</span></span>
- <span data-ttu-id="2cd95-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-817">'Identity'</span></span>
- <span data-ttu-id="2cd95-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-819">'Razor'</span></span>
- <span data-ttu-id="2cd95-820">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-821">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-822">'Blazor'</span></span>
- <span data-ttu-id="2cd95-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-823">'Identity'</span></span>
- <span data-ttu-id="2cd95-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-825">'Razor'</span></span>
- <span data-ttu-id="2cd95-826">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-827">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-828">'Blazor'</span></span>
- <span data-ttu-id="2cd95-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-829">'Identity'</span></span>
- <span data-ttu-id="2cd95-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-831">'Razor'</span></span>
- <span data-ttu-id="2cd95-832">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-833">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-834">'Blazor'</span></span>
- <span data-ttu-id="2cd95-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-835">'Identity'</span></span>
- <span data-ttu-id="2cd95-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-837">'Razor'</span></span>
- <span data-ttu-id="2cd95-838">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-839">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-840">'Blazor'</span></span>
- <span data-ttu-id="2cd95-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-841">'Identity'</span></span>
- <span data-ttu-id="2cd95-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-843">'Razor'</span></span>
- <span data-ttu-id="2cd95-844">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-844">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-845">--------- | --- название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-846">'Blazor'</span></span>
- <span data-ttu-id="2cd95-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-847">'Identity'</span></span>
- <span data-ttu-id="2cd95-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-849">'Razor'</span></span>
- <span data-ttu-id="2cd95-850">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-851">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-852">'Blazor'</span></span>
- <span data-ttu-id="2cd95-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-853">'Identity'</span></span>
- <span data-ttu-id="2cd95-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-855">'Razor'</span></span>
- <span data-ttu-id="2cd95-856">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2cd95-857">название: автор: описание: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="2cd95-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2cd95-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-858">'Blazor'</span></span>
- <span data-ttu-id="2cd95-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2cd95-859">'Identity'</span></span>
- <span data-ttu-id="2cd95-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2cd95-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="2cd95-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2cd95-861">'Razor'</span></span>
- <span data-ttu-id="2cd95-862">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="2cd95-862">'SignalR' uid:</span></span> 

<span data-ttu-id="2cd95-863">------ | | *AuthTests* | Содержит методы теста для:</span><span class="sxs-lookup"><span data-stu-id="2cd95-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="2cd95-864">доступа к защищенной странице пользователя, не прошедшего проверку подлинности;</span><span class="sxs-lookup"><span data-stu-id="2cd95-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="2cd95-865">доступа к защищенной странице пользователя, прошедшего проверку подлинности с помощью макета <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>;</span><span class="sxs-lookup"><span data-stu-id="2cd95-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="2cd95-866">получения профиля пользователя GitHub и проверки имени входа пользователя профиля.</span><span class="sxs-lookup"><span data-stu-id="2cd95-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="2cd95-867">| | *BasicTests* | Содержит метод теста для маршрутизации и типа содержимого.</span><span class="sxs-lookup"><span data-stu-id="2cd95-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="2cd95-868">| | *IntegrationTests* | Содержит интеграционные тесты для страницы индекса с использованием настраиваемого класса `WebApplicationFactory`.</span><span class="sxs-lookup"><span data-stu-id="2cd95-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="2cd95-869">*Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="2cd95-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="2cd95-870">*Utilities.cs* содержит метод `InitializeDbForTests`, используемый для заполнения базы данных тестовыми данными.</span><span class="sxs-lookup"><span data-stu-id="2cd95-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="2cd95-871">*HtmlHelpers.cs* предоставляет метод, возвращающий `IHtmlDocument` AngleSharp для использования методами теста.</span><span class="sxs-lookup"><span data-stu-id="2cd95-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="2cd95-872">*HttpClientExtensions.cs* предоставляет перегрузки для `SendAsync` для отправки запросов к ТС.</span><span class="sxs-lookup"><span data-stu-id="2cd95-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="2cd95-873">Используемая платформа тестирования — [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2cd95-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2cd95-874">Интеграционные тесты выполняются с помощью [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), который включает [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span><span class="sxs-lookup"><span data-stu-id="2cd95-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="2cd95-875">Поскольку пакет [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) используется для настройки узла тестирования и тестового сервера, для пакетов `TestHost` и `TestServer` не требуются прямые ссылки на пакеты в файле проекта тестового приложения или конфигурации разработчика в тестовом приложении.</span><span class="sxs-lookup"><span data-stu-id="2cd95-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="2cd95-876">**Заполнение базы данных для тестирования**</span><span class="sxs-lookup"><span data-stu-id="2cd95-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="2cd95-877">Перед выполнением интеграционных тестов обычно требуется небольшой набор данных в базе данных.</span><span class="sxs-lookup"><span data-stu-id="2cd95-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="2cd95-878">Например, при удалении теста вызывается удаление записей базы данных, поэтому в базе данных должна иметься по крайней мере одна запись, чтобы запрос на удаление был выполнен успешно.</span><span class="sxs-lookup"><span data-stu-id="2cd95-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="2cd95-879">Пример приложения заполняет базу данных тремя сообщениями в *Utilities.cs*, которые могут использоваться тестами при выполнении:</span><span class="sxs-lookup"><span data-stu-id="2cd95-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2cd95-880">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="2cd95-880">Additional resources</span></span>

* [<span data-ttu-id="2cd95-881">Модульные тесты</span><span class="sxs-lookup"><span data-stu-id="2cd95-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
