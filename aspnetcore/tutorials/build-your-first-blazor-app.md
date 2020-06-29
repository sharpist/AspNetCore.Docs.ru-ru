---
title: Создание первого приложения Blazor
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 892663a533a207df84b0fce9af259a7dc212bc9b
ms.sourcegitcommit: 5e462c3328c70f95969d02adce9c71592049f54c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85292780"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="bb4d4-103">Создание первого приложения Blazor</span><span class="sxs-lookup"><span data-stu-id="bb4d4-103">Build your first Blazor app</span></span>

<span data-ttu-id="bb4d4-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bb4d4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bb4d4-105">В этом учебнике показано, как создать и изменить приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="bb4d4-106">Вы узнаете, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bb4d4-107">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="bb4d4-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="bb4d4-108">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="bb4d4-108">Modify Razor components</span></span>
> * <span data-ttu-id="bb4d4-109">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="bb4d4-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="bb4d4-110">использовать внедрение зависимостей и маршрутизацию в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="bb4d4-111">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="bb4d4-112">Сборка компонентов</span><span class="sxs-lookup"><span data-stu-id="bb4d4-112">Build components</span></span>

1. <span data-ttu-id="bb4d4-113">Следуйте указаниям из статьи <xref:blazor/get-started>, чтобы создать проект Blazor для этого учебника.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="bb4d4-114">Присвойте проекту имя `ToDoList`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-114">Name the project `ToDoList`.</span></span>

1. <span data-ttu-id="bb4d4-115">Перейдите на каждую из трех страниц, размещенных в папке `Pages`: `Home`, `Counter` и `Fetch data`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-115">Browse to each of the app's three pages in the `Pages` folder: `Home`, `Counter`, and `Fetch data`.</span></span> <span data-ttu-id="bb4d4-116">Эти страницы реализуются такими файлами компонентов Razor, как `Index.razor`, `Counter.razor` и `FetchData.razor`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-116">These pages are implemented by the Razor component files `Index.razor`, `Counter.razor`, and `FetchData.razor`.</span></span>

1. <span data-ttu-id="bb4d4-117">На странице `Counter` нажмите кнопку увеличения значения счетчика без обновления страницы.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-117">On the `Counter` page, select the button to increment the counter without a page refresh.</span></span> <span data-ttu-id="bb4d4-118">Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="bb4d4-119">Благодаря Blazor можно использовать вместо него C#.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="bb4d4-120">Изучите реализацию компонента `Counter` в файле `Counter.razor`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-120">Examine the implementation of the `Counter` component in the `Counter.razor` file.</span></span>

   <span data-ttu-id="bb4d4-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-121">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="bb4d4-122">Пользовательский интерфейс компонента `Counter` определяется с помощью HTML.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="bb4d4-123">Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="bb4d4-124">HTML-разметка и логика отображения C# преобразуются в класс компонента во время сборки.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="bb4d4-125">Имя создаваемого класса .NET совпадает с именем файла.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="bb4d4-126">Элементы класса компонента определяются в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="bb4d4-127">В блоке `@code` указываются состояние (свойства, поля) и методы компонента для обработки событий или определения другой логики компонента.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="bb4d4-128">Эти элементы можно позднее включить в логику отображения компонента и обработки событий.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="bb4d4-129">При нажатии кнопки увеличения значения счетчика выполняются следующие действия:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-129">When the counter increment button is selected:</span></span>

   * <span data-ttu-id="bb4d4-130">Вызывается обработчик `onclick`, зарегистрированный для компонента `Counter` (метод `IncrementCount`).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="bb4d4-131">Компонент `Counter` повторно создает свое дерево отображения.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="bb4d4-132">Выполняется сравнение нового и старого деревьев отображения.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="bb4d4-133">Применяются только изменения модели DOM (Document Object Model — объектная модель документа).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="bb4d4-134">Отображаемое значение счетчика обновляется.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="bb4d4-135">Измените логику C# компонента `Counter`, чтобы при нажатии кнопки значение счетчика увеличивалось на две единицы вместо одной.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="bb4d4-136">Скомпилируйте и запустите приложение, чтобы увидеть изменения.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="bb4d4-137">Нажмите кнопку.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-137">Select the button.</span></span> <span data-ttu-id="bb4d4-138">Значение счетчика увеличится на две единицы.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="bb4d4-139">Использование компонентов</span><span class="sxs-lookup"><span data-stu-id="bb4d4-139">Use components</span></span>

<span data-ttu-id="bb4d4-140">Включите компонент в другой компонент, используя синтаксис HTML.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="bb4d4-141">Добавьте компонент `Counter` в компонент `Index` приложения, разместив элемент `<Counter />` внутри компонента `Index` (`Index.razor`).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (`Index.razor`).</span></span>

   <span data-ttu-id="bb4d4-142">Если вы выполняете эту задачу с помощью WebAssembly Blazor, это значит, что компонент `SurveyPrompt` использует компонент `Index`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="bb4d4-143">Замените элемент `<SurveyPrompt>` элементом `<Counter />`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="bb4d4-144">Если вы используете для этой задачи серверное приложение Blazor, добавьте к компоненту `Index` элемент `<Counter />`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="bb4d4-145">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-145">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="bb4d4-146">Повторно выполните сборку и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-146">Rebuild and run the app.</span></span> <span data-ttu-id="bb4d4-147">Компонент `Index` имеет свой собственный счетчик.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="bb4d4-148">Параметры компонентов</span><span class="sxs-lookup"><span data-stu-id="bb4d4-148">Component parameters</span></span>

<span data-ttu-id="bb4d4-149">Компоненты также могут принимать параметры.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-149">Components can also have parameters.</span></span> <span data-ttu-id="bb4d4-150">Параметры для компонентов определяются с помощью открытых свойств в классе компонента с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="bb4d4-151">Используйте атрибуты, чтобы указать аргументы для компонента в разметке.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="bb4d4-152">Обновите код C# для `@code` в компоненте следующим образом:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="bb4d4-153">Добавьте открытое свойство `IncrementAmount` с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="bb4d4-154">Изменение метод `IncrementCount`, чтобы он использовал свойство `IncrementAmount` при увеличении значения `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="bb4d4-155">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-155">`Pages/Counter.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="bb4d4-156">Укажите параметр `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="bb4d4-157">Установите приращение счетчика на десять.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="bb4d4-158">`Pages/Index.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-158">`Pages/Index.razor`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="bb4d4-159">Перезагрузите компонент `Index`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-159">Reload the `Index` component.</span></span> <span data-ttu-id="bb4d4-160">Теперь значение счетчика будет увеличиваться на десять при каждом нажатии кнопки.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-160">The counter increments by ten each time the button is selected.</span></span> <span data-ttu-id="bb4d4-161">Счетчик в компоненте `Counter` продолжает увеличиваться на единицу.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="bb4d4-162">Маршрутизация к компонентам</span><span class="sxs-lookup"><span data-stu-id="bb4d4-162">Route to components</span></span>

<span data-ttu-id="bb4d4-163">Директива `@page` в верхней части файла `Counter.razor` указывает, что компонент `Counter` является конечной точкой маршрутизации.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-163">The `@page` directive at the top of the `Counter.razor` file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="bb4d4-164">Компонент `Counter` обрабатывает запросы, направляемые к `/counter`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="bb4d4-165">Без директивы `@page` этот компонент не будет обрабатывать перенаправленные запросы, но останется доступным для использования другими компонентами.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="bb4d4-166">Внедрение зависимостей</span><span class="sxs-lookup"><span data-stu-id="bb4d4-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a><span data-ttu-id="bb4d4-167">Взаимодействие с сервером Blazor</span><span class="sxs-lookup"><span data-stu-id="bb4d4-167">Blazor Server experience</span></span>

<span data-ttu-id="bb4d4-168">Если вы используете серверное приложение Blazor, служба `WeatherForecastService` зарегистрирована как [отдельная](xref:fundamentals/dependency-injection#service-lifetimes) в `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bb4d4-169">Экземпляр этой службы предоставляется для всего приложения посредством [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="bb4d4-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="bb4d4-170">Директива [`@inject`](xref:mvc/views/razor#inject) используется для внедрения экземпляра службы `WeatherForecastService` в компонент `FetchData`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="bb4d4-171">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-171">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="bb4d4-172">Компонент `FetchData` использует внедренную службу как `ForecastService`, чтобы получить массив объектов `WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a><span data-ttu-id="bb4d4-173">Взаимодействие с WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="bb4d4-173">Blazor WebAssembly experience</span></span>

<span data-ttu-id="bb4d4-174">Если вы работаете с приложением Blazor WebAssembly, внедряется <xref:System.Net.Http.HttpClient> для получения данных прогноза погоды из файла `weather.json`, расположенного в папке `wwwroot/sample-data`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the `weather.json` file in the `wwwroot/sample-data` folder.</span></span>

<span data-ttu-id="bb4d4-175">`Pages/FetchData.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-175">`Pages/FetchData.razor`:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="bb4d4-176">Цикл [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) используется для отображения каждого экземпляра прогноза в отдельной строке в таблице погоды.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="bb4d4-177">Создание списка дел</span><span class="sxs-lookup"><span data-stu-id="bb4d4-177">Build a todo list</span></span>

<span data-ttu-id="bb4d4-178">Добавьте в приложение новый компонент, представляющий собой простой список дел.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="bb4d4-179">Добавьте в приложение новый компонент `Todo` Razor в папке `Pages`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-179">Add a new `Todo` Razor component to the app in the `Pages` folder.</span></span> <span data-ttu-id="bb4d4-180">Если вы используете Visual Studio, щелкните правой кнопкой мыши папку `Pages` и выберите **Добавить** > **Создать элемент** > **Компонент Razor** .</span><span class="sxs-lookup"><span data-stu-id="bb4d4-180">If you're using Visual Studio, right-click the `Pages` folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="bb4d4-181">Присвойте файлу компонента имя `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-181">Name the component's file `Todo.razor`.</span></span> <span data-ttu-id="bb4d4-182">В других средах разработки необходимо добавить в папку `Pages` пустой файл с именем `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-182">In other development environments, add a blank file to the `Pages` folder named `Todo.razor`.</span></span> <span data-ttu-id="bb4d4-183">Имена файлов компонентов Razor должны начинаться с прописной буквы, поэтому убедитесь в том, что имя файла компонента `Todo` начинается с прописной буквы `T`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-183">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="bb4d4-184">Задайте начальную разметку компонента.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-184">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="bb4d4-185">Добавьте компонент `Todo` на панель навигации.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-185">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="bb4d4-186">Компонент `NavMenu` (`Shared/NavMenu.razor`) используется в макете этого приложения.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-186">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="bb4d4-187">Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-187">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="bb4d4-188">Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файле `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-188">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="bb4d4-189">Повторно выполните сборку и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-189">Rebuild and run the app.</span></span> <span data-ttu-id="bb4d4-190">Перейдите на новую страницу Todo, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-190">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="bb4d4-191">Добавьте в корень проекта файл `TodoItem.cs`, который будет содержать класс для элемента списка дел.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-191">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="bb4d4-192">Используйте следующий код C# для класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-192">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="bb4d4-193">Снова вернитесь к компоненту `Todo` (`Pages/Todo.razor`).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-193">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="bb4d4-194">Добавьте поле для элементов списка дел в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-194">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="bb4d4-195">Компонент `Todo` использует это поле для сохранения состояния списка дел.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-195">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="bb4d4-196">Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-196">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="bb4d4-197">Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-197">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="bb4d4-198">Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-198">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="bb4d4-199">Повторно выполните сборку и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-199">Rebuild and run the app.</span></span> <span data-ttu-id="bb4d4-200">При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не привязан обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-200">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="bb4d4-201">Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-201">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="bb4d4-202">Теперь при нажатии кнопки вызывается метод C# `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-202">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="bb4d4-203">Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-203">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="bb4d4-204">Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-204">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="bb4d4-205">Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-205">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="bb4d4-206">Повторно выполните сборку и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-206">Rebuild and run the app.</span></span> <span data-ttu-id="bb4d4-207">Добавьте несколько задач в список дел, чтобы проверить работу нового кода.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-207">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="bb4d4-208">Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-208">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="bb4d4-209">Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-209">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="bb4d4-210">Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-210">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="bb4d4-211">Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).</span><span class="sxs-lookup"><span data-stu-id="bb4d4-211">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="bb4d4-212">Готовый компонент `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="bb4d4-212">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="bb4d4-213">Повторно выполните сборку и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-213">Rebuild and run the app.</span></span> <span data-ttu-id="bb4d4-214">Добавьте элементы в список дел, чтобы протестировать новый код.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-214">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bb4d4-215">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="bb4d4-215">Next steps</span></span>

<span data-ttu-id="bb4d4-216">В этом руководстве вы узнали, как выполнять следующие задачи:</span><span class="sxs-lookup"><span data-stu-id="bb4d4-216">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="bb4d4-217">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="bb4d4-217">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="bb4d4-218">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="bb4d4-218">Modify Razor components</span></span>
> * <span data-ttu-id="bb4d4-219">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="bb4d4-219">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="bb4d4-220">использовать внедрение зависимостей и маршрутизацию в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-220">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="bb4d4-221">Узнайте, как создавать и использовать компоненты.</span><span class="sxs-lookup"><span data-stu-id="bb4d4-221">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components/index>
