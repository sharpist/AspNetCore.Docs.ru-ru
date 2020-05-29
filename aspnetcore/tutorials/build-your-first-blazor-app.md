---
<span data-ttu-id="ab23e-101">title: 'Создание первого приложения Blazor' author: description: 'Пошаговое создание приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="ab23e-101">title: 'Build your first Blazor app' author: description: 'Build a Blazor app step-by-step.'</span></span>
<span data-ttu-id="ab23e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="ab23e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ab23e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ab23e-103">'Blazor'</span></span>
- <span data-ttu-id="ab23e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ab23e-104">'Identity'</span></span>
- <span data-ttu-id="ab23e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ab23e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ab23e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ab23e-106">'Razor'</span></span>
- <span data-ttu-id="ab23e-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="ab23e-107">'SignalR' uid:</span></span> 

---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="ab23e-108">Создание первого приложения Blazor</span><span class="sxs-lookup"><span data-stu-id="ab23e-108">Build your first Blazor app</span></span>

<span data-ttu-id="ab23e-109">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ab23e-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ab23e-110">В этом учебнике показано, как создать и изменить приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="ab23e-110">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="ab23e-111">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="ab23e-111">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ab23e-112">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="ab23e-112">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ab23e-113">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="ab23e-113">Modify Razor components</span></span>
> * <span data-ttu-id="ab23e-114">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="ab23e-114">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ab23e-115">использовать внедрение зависимостей и маршрутизацию в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="ab23e-115">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="ab23e-116">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.</span><span class="sxs-lookup"><span data-stu-id="ab23e-116">At the end of this tutorial, you'll have a working chat app.</span></span>

## <a name="build-components"></a><span data-ttu-id="ab23e-117">Сборка компонентов</span><span class="sxs-lookup"><span data-stu-id="ab23e-117">Build components</span></span>

1. <span data-ttu-id="ab23e-118">Следуйте указаниям из статьи <xref:blazor/get-started>, чтобы создать проект Blazor для этого учебника.</span><span class="sxs-lookup"><span data-stu-id="ab23e-118">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="ab23e-119">Назовите проект *ToDoList*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-119">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="ab23e-120">Перейдите на каждую из трех страниц, размещенных в папке *Pages*: домашнюю, счетчика и получения данных.</span><span class="sxs-lookup"><span data-stu-id="ab23e-120">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="ab23e-121">Эти страницы реализованы в виде файлов компонентов Razor *Index.razor*, *Counter.razor* и *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-121">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="ab23e-122">На странице Counter нажмите кнопку **Click me** (Щелкните здесь), чтобы увеличить значение счетчика без обновления страницы.</span><span class="sxs-lookup"><span data-stu-id="ab23e-122">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ab23e-123">Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ab23e-123">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="ab23e-124">Благодаря Blazor можно использовать вместо него C#.</span><span class="sxs-lookup"><span data-stu-id="ab23e-124">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="ab23e-125">Изучите реализацию компонента `Counter` в файле *Counter.razor*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-125">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="ab23e-126">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ab23e-126">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="ab23e-127">Пользовательский интерфейс компонента `Counter` определяется с помощью HTML.</span><span class="sxs-lookup"><span data-stu-id="ab23e-127">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="ab23e-128">Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="ab23e-128">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="ab23e-129">HTML-разметка и логика отображения C# преобразуются в класс компонента во время сборки.</span><span class="sxs-lookup"><span data-stu-id="ab23e-129">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="ab23e-130">Имя создаваемого класса .NET совпадает с именем файла.</span><span class="sxs-lookup"><span data-stu-id="ab23e-130">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="ab23e-131">Элементы класса компонента определяются в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-131">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="ab23e-132">В блоке `@code` указываются состояние (свойства, поля) и методы компонента для обработки событий или определения другой логики компонента.</span><span class="sxs-lookup"><span data-stu-id="ab23e-132">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="ab23e-133">Эти элементы можно позднее включить в логику отображения компонента и обработки событий.</span><span class="sxs-lookup"><span data-stu-id="ab23e-133">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="ab23e-134">При нажатии кнопки **Click me** (Щелкните здесь) выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="ab23e-134">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="ab23e-135">Вызывается обработчик `onclick`, зарегистрированный для компонента `Counter` (метод `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="ab23e-135">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="ab23e-136">Компонент `Counter` повторно создает свое дерево отображения.</span><span class="sxs-lookup"><span data-stu-id="ab23e-136">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="ab23e-137">Выполняется сравнение нового и старого деревьев отображения.</span><span class="sxs-lookup"><span data-stu-id="ab23e-137">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="ab23e-138">Применяются только изменения модели DOM (Document Object Model — объектная модель документа).</span><span class="sxs-lookup"><span data-stu-id="ab23e-138">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="ab23e-139">Отображаемое значение счетчика обновляется.</span><span class="sxs-lookup"><span data-stu-id="ab23e-139">The displayed count is updated.</span></span>

1. <span data-ttu-id="ab23e-140">Измените логику C# компонента `Counter`, чтобы при нажатии кнопки значение счетчика увеличивалось на две единицы вместо одной.</span><span class="sxs-lookup"><span data-stu-id="ab23e-140">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="ab23e-141">Скомпилируйте и запустите приложение, чтобы увидеть изменения.</span><span class="sxs-lookup"><span data-stu-id="ab23e-141">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="ab23e-142">Нажмите кнопку **Click Me** (Щелкните здесь).</span><span class="sxs-lookup"><span data-stu-id="ab23e-142">Select the **Click me** button.</span></span> <span data-ttu-id="ab23e-143">Значение счетчика увеличится на две единицы.</span><span class="sxs-lookup"><span data-stu-id="ab23e-143">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="ab23e-144">Использование компонентов</span><span class="sxs-lookup"><span data-stu-id="ab23e-144">Use components</span></span>

<span data-ttu-id="ab23e-145">Включите компонент в другой компонент, используя синтаксис HTML.</span><span class="sxs-lookup"><span data-stu-id="ab23e-145">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="ab23e-146">Добавьте компонент `Counter` в компонент `Index` приложения, разместив элемент `<Counter />` внутри компонента `Index` (*Index.razor*).</span><span class="sxs-lookup"><span data-stu-id="ab23e-146">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="ab23e-147">Если вы выполняете эту задачу с помощью WebAssembly Blazor, это значит, что компонент `SurveyPrompt` использует компонент `Index`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-147">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="ab23e-148">Замените элемент `<SurveyPrompt>` элементом `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-148">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="ab23e-149">Если вы используете для этой задачи серверное приложение Blazor, добавьте к компоненту `Index` элемент `<Counter />`:</span><span class="sxs-lookup"><span data-stu-id="ab23e-149">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="ab23e-150">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ab23e-150">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="ab23e-151">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="ab23e-151">Rebuild and run the app.</span></span> <span data-ttu-id="ab23e-152">Компонент `Index` имеет свой собственный счетчик.</span><span class="sxs-lookup"><span data-stu-id="ab23e-152">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="ab23e-153">Параметры компонентов</span><span class="sxs-lookup"><span data-stu-id="ab23e-153">Component parameters</span></span>

<span data-ttu-id="ab23e-154">Компоненты также могут принимать параметры.</span><span class="sxs-lookup"><span data-stu-id="ab23e-154">Components can also have parameters.</span></span> <span data-ttu-id="ab23e-155">Параметры для компонентов определяются с помощью открытых свойств в классе компонента с атрибутом `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-155">Component parameters are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="ab23e-156">Используйте атрибуты, чтобы указать аргументы для компонента в разметке.</span><span class="sxs-lookup"><span data-stu-id="ab23e-156">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="ab23e-157">Обновите код C# для `@code` в компоненте следующим образом:</span><span class="sxs-lookup"><span data-stu-id="ab23e-157">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="ab23e-158">Добавьте открытое свойство `IncrementAmount` с атрибутом `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-158">Add a public `IncrementAmount` property with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="ab23e-159">Изменение метод `IncrementCount`, чтобы он использовал свойство `IncrementAmount` при увеличении значения `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-159">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="ab23e-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ab23e-160">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="ab23e-161">Укажите параметр `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.</span><span class="sxs-lookup"><span data-stu-id="ab23e-161">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="ab23e-162">Установите приращение счетчика на десять.</span><span class="sxs-lookup"><span data-stu-id="ab23e-162">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="ab23e-163">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ab23e-163">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="ab23e-164">Перезагрузите компонент `Index`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-164">Reload the `Index` component.</span></span> <span data-ttu-id="ab23e-165">Теперь значение счетчика на домашней странице будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь).</span><span class="sxs-lookup"><span data-stu-id="ab23e-165">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ab23e-166">Счетчик в компоненте `Counter` продолжает увеличиваться на единицу.</span><span class="sxs-lookup"><span data-stu-id="ab23e-166">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="ab23e-167">Маршрутизация к компонентам</span><span class="sxs-lookup"><span data-stu-id="ab23e-167">Route to components</span></span>

<span data-ttu-id="ab23e-168">Директива `@page` в верхней части файла *Counter.razor* указывает, что компонент `Counter` является конечной точкой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="ab23e-168">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="ab23e-169">Компонент `Counter` обрабатывает запросы, направляемые к `/counter`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-169">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="ab23e-170">Без директивы `@page` этот компонент не будет обрабатывать перенаправленные запросы, но останется доступным для использования другими компонентами.</span><span class="sxs-lookup"><span data-stu-id="ab23e-170">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="ab23e-171">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="ab23e-171">Dependency injection</span></span>

### <a name="blazor-server-experience"></a><span data-ttu-id="ab23e-172">Взаимодействие с сервером Blazor</span><span class="sxs-lookup"><span data-stu-id="ab23e-172">Blazor Server experience</span></span>

<span data-ttu-id="ab23e-173">Если вы используете серверное приложение Blazor, служба `WeatherForecastService` зарегистрирована как [отдельная](xref:fundamentals/dependency-injection#service-lifetimes) в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-173">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ab23e-174">Экземпляр этой службы предоставляется для всего приложения посредством [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="ab23e-174">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="ab23e-175">Директива `@inject` используется для внедрения экземпляра службы `WeatherForecastService` в компонент `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-175">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="ab23e-176">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="ab23e-176">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="ab23e-177">Компонент `FetchData` использует внедренную службу как `ForecastService`, чтобы получить массив объектов `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-177">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a><span data-ttu-id="ab23e-178">Взаимодействие с WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="ab23e-178">Blazor WebAssembly experience</span></span>

<span data-ttu-id="ab23e-179">Если вы работаете с приложением WebAssembly Blazor, внедряется `HttpClient` для получения данных прогноза погоды из файла *weather.json*, расположенного в папке *wwwroot/sample-data*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-179">If working with a Blazor WebAssembly app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="ab23e-180">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="ab23e-180">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="ab23e-181">Цикл [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) используется для отображения каждого экземпляра прогноза в отдельной строке в таблице погоды.</span><span class="sxs-lookup"><span data-stu-id="ab23e-181">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="ab23e-182">Создание списка дел</span><span class="sxs-lookup"><span data-stu-id="ab23e-182">Build a todo list</span></span>

<span data-ttu-id="ab23e-183">Добавьте в приложение новый компонент, представляющий собой простой список дел.</span><span class="sxs-lookup"><span data-stu-id="ab23e-183">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="ab23e-184">Добавьте в приложение новый компонент `Todo` Razor в папке *Pages*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-184">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="ab23e-185">Щелкните в Visual Studio правой кнопкой мыши папку **Pages** и выберите пункт **Добавить** > **Создать элемент** > **Компонент Razor** .</span><span class="sxs-lookup"><span data-stu-id="ab23e-185">In Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="ab23e-186">Присвойте файлу компонента имя *Todo.Razor*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-186">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="ab23e-187">В других средах разработки необходимо добавить в папку **Pages** пустой файл с именем *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-187">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="ab23e-188">Задайте начальную разметку компонента.</span><span class="sxs-lookup"><span data-stu-id="ab23e-188">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="ab23e-189">Добавьте компонент `Todo` на панель навигации.</span><span class="sxs-lookup"><span data-stu-id="ab23e-189">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="ab23e-190">Компонент `NavMenu` (*Shared/NavMenu.razor*) используется в макете этого приложения.</span><span class="sxs-lookup"><span data-stu-id="ab23e-190">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="ab23e-191">Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.</span><span class="sxs-lookup"><span data-stu-id="ab23e-191">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="ab23e-192">Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файл *Shared/NavMenu.razor*.</span><span class="sxs-lookup"><span data-stu-id="ab23e-192">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="ab23e-193">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="ab23e-193">Rebuild and run the app.</span></span> <span data-ttu-id="ab23e-194">Перейдите на новую страницу Todo, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.</span><span class="sxs-lookup"><span data-stu-id="ab23e-194">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="ab23e-195">Добавьте в корень проекта файл *TodoItem.cs*, который будет размещать класс для элемента списка дел.</span><span class="sxs-lookup"><span data-stu-id="ab23e-195">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="ab23e-196">Используйте следующий код C# для класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-196">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="ab23e-197">Снова вернитесь к компоненту `Todo` (*Pages/Todo.razor*).</span><span class="sxs-lookup"><span data-stu-id="ab23e-197">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="ab23e-198">Добавьте поле для элементов списка дел в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-198">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="ab23e-199">Компонент `Todo` использует это поле для сохранения состояния списка дел.</span><span class="sxs-lookup"><span data-stu-id="ab23e-199">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="ab23e-200">Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="ab23e-200">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="ab23e-201">Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.</span><span class="sxs-lookup"><span data-stu-id="ab23e-201">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="ab23e-202">Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="ab23e-202">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="ab23e-203">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="ab23e-203">Rebuild and run the app.</span></span> <span data-ttu-id="ab23e-204">При нажатии кнопки **Add todo** (Добавить задачу) ничего не происходит, так как к кнопке не привязан обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="ab23e-204">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="ab23e-205">Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-205">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="ab23e-206">Теперь при нажатии кнопки вызывается метод C# `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="ab23e-206">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="ab23e-207">Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-207">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="ab23e-208">Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список.</span><span class="sxs-lookup"><span data-stu-id="ab23e-208">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="ab23e-209">Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-209">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="ab23e-210">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="ab23e-210">Rebuild and run the app.</span></span> <span data-ttu-id="ab23e-211">Добавьте несколько задач в список дел, чтобы проверить работу нового кода.</span><span class="sxs-lookup"><span data-stu-id="ab23e-211">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="ab23e-212">Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач.</span><span class="sxs-lookup"><span data-stu-id="ab23e-212">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="ab23e-213">Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-213">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="ab23e-214">Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.</span><span class="sxs-lookup"><span data-stu-id="ab23e-214">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="ab23e-215">Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).</span><span class="sxs-lookup"><span data-stu-id="ab23e-215">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="ab23e-216">Готовый компонент `Todo` (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="ab23e-216">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="ab23e-217">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="ab23e-217">Rebuild and run the app.</span></span> <span data-ttu-id="ab23e-218">Добавьте элементы в список дел, чтобы протестировать новый код.</span><span class="sxs-lookup"><span data-stu-id="ab23e-218">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ab23e-219">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="ab23e-219">Next steps</span></span>

<span data-ttu-id="ab23e-220">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="ab23e-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ab23e-221">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="ab23e-221">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ab23e-222">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="ab23e-222">Modify Razor components</span></span>
> * <span data-ttu-id="ab23e-223">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="ab23e-223">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ab23e-224">использовать внедрение зависимостей и маршрутизацию в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="ab23e-224">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="ab23e-225">Узнайте, как создавать и использовать компоненты.</span><span class="sxs-lookup"><span data-stu-id="ab23e-225">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
