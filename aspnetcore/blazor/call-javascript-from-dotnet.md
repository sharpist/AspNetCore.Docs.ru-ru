---
title: Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать функции JavaScript из методов .NET в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: da4ce8a2610fc07d22153f66831d693ae66e0fe5
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424156"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="65d2e-103">Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="65d2e-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="65d2e-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="65d2e-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="65d2e-105">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65d2e-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="65d2e-106">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="65d2e-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="65d2e-107">В этой статье рассматривается вызов функций JavaScript из .NET.</span><span class="sxs-lookup"><span data-stu-id="65d2e-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="65d2e-108">Сведения о том, как вызывать методы .NET из JavaScript, см. в статье <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="65d2e-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65d2e-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="65d2e-110">Для вызова JavaScript из .NET используйте абстракцию <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="65d2e-111">Чтобы выполнять вызовы взаимодействия с JS, внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент.</span><span class="sxs-lookup"><span data-stu-id="65d2e-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="65d2e-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> принимает идентификатор функции JavaScript, которую нужно вызвать, вместе с любым числом аргументов, сериализуемых в JSON.</span><span class="sxs-lookup"><span data-stu-id="65d2e-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="65d2e-113">Идентификатор функции задается относительно глобальной области (`window`).</span><span class="sxs-lookup"><span data-stu-id="65d2e-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="65d2e-114">Если нужно вызвать функцию `window.someScope.someFunction`, идентификатором будет `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="65d2e-115">Регистрировать функцию перед ее вызовом не требуется.</span><span class="sxs-lookup"><span data-stu-id="65d2e-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="65d2e-116">Тип возвращаемого значения `T` также должен сериализоваться в JSON.</span><span class="sxs-lookup"><span data-stu-id="65d2e-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="65d2e-117">Тип `T` должен соответствовать типу .NET, который лучше всего соответствует возвращаемому типу JSON.</span><span class="sxs-lookup"><span data-stu-id="65d2e-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="65d2e-118">Функции JavaScript, возвращающие [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), вызываются с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="65d2e-119">`InvokeAsync` распаковывает обещание и возвращает значение, ожидаемое обещанием.</span><span class="sxs-lookup"><span data-stu-id="65d2e-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="65d2e-120">Для приложений Blazor Server с включенной предварительной обработкой вызвать JavaScript нельзя во время первоначальной предварительной обработки.</span><span class="sxs-lookup"><span data-stu-id="65d2e-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="65d2e-121">Вызовы взаимодействия с JavaScript должны быть отложены до тех пор, пока не будет установлено соединение с браузером.</span><span class="sxs-lookup"><span data-stu-id="65d2e-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="65d2e-122">См. раздел [Обнаружение предварительной обработки в приложении Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="65d2e-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="65d2e-123">Приведенный ниже пример основан на [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), декодере на базе JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65d2e-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="65d2e-124">В примере показано, как вызвать функцию JavaScript из метода C#, которая переносит требование из кода разработчика в существующий API JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65d2e-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="65d2e-125">Функция JavaScript принимает массив байтов из метода C#, декодирует его и возвращает компоненту текст для отображения.</span><span class="sxs-lookup"><span data-stu-id="65d2e-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="65d2e-126">Внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) предоставьте функцию JavaScript, которая использует `TextDecoder` для декодирования переданного массива и возвращения декодированного значения:</span><span class="sxs-lookup"><span data-stu-id="65d2e-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="65d2e-127">Код JavaScript, например из предыдущего примера, можно также загрузить из файла JavaScript (`.js`) со ссылкой на файл скрипта:</span><span class="sxs-lookup"><span data-stu-id="65d2e-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="65d2e-128">Приведенный ниже компонент выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="65d2e-128">The following component:</span></span>

* <span data-ttu-id="65d2e-129">вызывает функцию JavaScript `convertArray` с помощью `JSRuntime` при нажатии кнопки компонента ( **`Convert Array`** );</span><span class="sxs-lookup"><span data-stu-id="65d2e-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="65d2e-130">после вызова функции JavaScript переданный массив преобразуется в строку.</span><span class="sxs-lookup"><span data-stu-id="65d2e-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="65d2e-131">Строка возвращается компоненту для отображения.</span><span class="sxs-lookup"><span data-stu-id="65d2e-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="65d2e-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="65d2e-132">IJSRuntime</span></span>

<span data-ttu-id="65d2e-133">Чтобы использовать абстракцию <xref:Microsoft.JSInterop.IJSRuntime>, можно выбрать один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="65d2e-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="65d2e-134">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="65d2e-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="65d2e-135">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="65d2e-136">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="65d2e-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="65d2e-137">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в класс (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="65d2e-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="65d2e-138">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="65d2e-139">Функция вызывается с помощью метода `JSRuntime.InvokeAsync` и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="65d2e-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="65d2e-140">Для динамического создания содержимого с помощью [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) используйте атрибут `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="65d2e-141">В примере клиентского приложения, используемом в этой статье, приложению доступны две функции JavaScript, которые взаимодействуют с моделью DOM для получения вводимых пользователем данных и вывода приветственного сообщения:</span><span class="sxs-lookup"><span data-stu-id="65d2e-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="65d2e-142">`showPrompt`. Создает запрос на ввод пользователем данных (имени пользователя) и возвращает имя вызвавшему объекту.</span><span class="sxs-lookup"><span data-stu-id="65d2e-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="65d2e-143">`displayWelcome`. Назначает приветственное сообщение от вызывающего объекта объекту модели DOM со значением `id`, равным `welcome`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="65d2e-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="65d2e-145">Поместите тег `<script>` со ссылкой на файл JavaScript в файл `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="65d2e-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="65d2e-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="65d2e-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="65d2e-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="65d2e-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="65d2e-148">Не помещайте тег `<script>` в файл компонента, так как тег `<script>` не может изменяться динамически.</span><span class="sxs-lookup"><span data-stu-id="65d2e-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="65d2e-149">Методы .NET взаимодействуют с функциями JavaScript в файле `exampleJsInterop.js` путем вызова <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="65d2e-150">Абстракция <xref:Microsoft.JSInterop.IJSRuntime> является асинхронной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="65d2e-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="65d2e-151">Если вы используете приложение Blazor WebAssembly и вам нужно вызывать функцию JavaScript синхронно, выполните нисходящее приведение к <xref:Microsoft.JSInterop.IJSInProcessRuntime> и вызовите <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> вместо этого.</span><span class="sxs-lookup"><span data-stu-id="65d2e-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="65d2e-152">В большинстве библиотек взаимодействия с JS рекомендуется использовать асинхронные интерфейсы API, чтобы обеспечить доступность библиотек в любых сценариях.</span><span class="sxs-lookup"><span data-stu-id="65d2e-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="65d2e-153">Сведения о включении изоляции JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) см. в разделе об [изоляции JavaScript Blazor и ссылках на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="65d2e-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="65d2e-154">Пример приложения включает в себя компонент для демонстрации взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="65d2e-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="65d2e-155">Он выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="65d2e-155">The component:</span></span>

* <span data-ttu-id="65d2e-156">принимает вводимые пользователем данные посредством запроса JavaScript;</span><span class="sxs-lookup"><span data-stu-id="65d2e-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="65d2e-157">возвращает текст компоненту для обработки;</span><span class="sxs-lookup"><span data-stu-id="65d2e-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="65d2e-158">вызывает еще одну функцию JavaScript, которая взаимодействует с моделью DOM для вывода приветственного сообщения.</span><span class="sxs-lookup"><span data-stu-id="65d2e-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="65d2e-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

<span data-ttu-id="65d2e-160">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="65d2e-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="65d2e-161">Если функция `TriggerJsPrompt` выполняется при нажатии кнопки **`Trigger JavaScript Prompt`** , то вызывается функция JavaScript `showPrompt`, предоставленная в файле `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="65d2e-162">Функция `showPrompt` принимает введенные пользователем данные (имя пользователя), которые кодируются в HTML и возвращаются компоненту.</span><span class="sxs-lookup"><span data-stu-id="65d2e-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="65d2e-163">Компонент сохраняет имя пользователя в локальной переменной `name`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="65d2e-164">Строка, хранящаяся в `name`, включается в приветственное сообщение, которое передается в функцию JavaScript `displayWelcome`, визуализирующую приветственное сообщение в теге заголовка.</span><span class="sxs-lookup"><span data-stu-id="65d2e-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="65d2e-165">Вызов функции void JavaScript</span><span class="sxs-lookup"><span data-stu-id="65d2e-165">Call a void JavaScript function</span></span>

<span data-ttu-id="65d2e-166">Функции JavaScript, возвращающие значение [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) или [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined), вызываются с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-166">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="65d2e-167">Обнаружение предварительной отрисовки в приложении Blazor Server</span><span class="sxs-lookup"><span data-stu-id="65d2e-167">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="65d2e-168">Получение ссылок на элементы</span><span class="sxs-lookup"><span data-stu-id="65d2e-168">Capture references to elements</span></span>

<span data-ttu-id="65d2e-169">В некоторых сценариях взаимодействия с JS требуются ссылки на элементы HTML.</span><span class="sxs-lookup"><span data-stu-id="65d2e-169">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="65d2e-170">Например, ссылка на элемент может требоваться библиотеке пользовательского интерфейса для инициализации либо необходимо вызывать командные интерфейсы API для элемента, такого как `focus` или `play`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-170">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="65d2e-171">Для получения ссылок на элементы HTML в компоненте используйте описанный ниже подход.</span><span class="sxs-lookup"><span data-stu-id="65d2e-171">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="65d2e-172">Добавьте атрибут `@ref` к элементу HTML.</span><span class="sxs-lookup"><span data-stu-id="65d2e-172">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="65d2e-173">Определите поле типа <xref:Microsoft.AspNetCore.Components.ElementReference>, имя которого совпадает со значением атрибута `@ref`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-173">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="65d2e-174">В следующем примере показано получение ссылки на элемент `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-174">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="65d2e-175">Ссылку на элемент следует использовать только для изменения содержимого пустого элемента, который не взаимодействует с Blazor.</span><span class="sxs-lookup"><span data-stu-id="65d2e-175">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="65d2e-176">Этот сценарий полезен, если сторонний интерфейс API предоставляет содержимое элементу.</span><span class="sxs-lookup"><span data-stu-id="65d2e-176">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="65d2e-177">Так как Blazor не взаимодействует с элементом, риск конфликта между представлением Blazor элемента и моделью DOM отсутствует.</span><span class="sxs-lookup"><span data-stu-id="65d2e-177">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="65d2e-178">В следующем примере изменять содержимое неупорядоченного списка (`ul`) *опасно*, так как Blazor взаимодействует с моделью DOM для заполнения элементов этого списка (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="65d2e-178">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="65d2e-179">Если при взаимодействии с JS содержимое элемента `MyList` изменяется и Blazor пытается применить изменения к элементу, эти изменения не будут соответствовать модели DOM.</span><span class="sxs-lookup"><span data-stu-id="65d2e-179">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="65d2e-180">В случае с кодом .NET <xref:Microsoft.AspNetCore.Components.ElementReference> является непрозрачным дескриптором.</span><span class="sxs-lookup"><span data-stu-id="65d2e-180">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="65d2e-181">*Единственное*, что можно сделать с <xref:Microsoft.AspNetCore.Components.ElementReference>, — передать в код JavaScript посредством взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="65d2e-181">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="65d2e-182">При этом код на стороне JavaScript получает экземпляр `HTMLElement`, который может использоваться с обычными интерфейсами API DOM.</span><span class="sxs-lookup"><span data-stu-id="65d2e-182">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="65d2e-183">Например, в приведенном ниже коде определяется метод расширения .NET, который позволяет установить фокус на элемент.</span><span class="sxs-lookup"><span data-stu-id="65d2e-183">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="65d2e-184">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-184">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="65d2e-185">Для вызова функции JavaScript, которая не возвращает значение, используйте метод <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-185">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="65d2e-186">Следующий код устанавливает фокус на элементе для ввода имени пользователя, вызывая предыдущую функцию JavaScript с полученной ссылкой <xref:Microsoft.AspNetCore.Components.ElementReference>:</span><span class="sxs-lookup"><span data-stu-id="65d2e-186">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="65d2e-187">Чтобы использовать метод расширения, создайте статический метод расширения, который принимает экземпляр <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="65d2e-187">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="65d2e-188">Метод `Focus` вызывается для объекта напрямую.</span><span class="sxs-lookup"><span data-stu-id="65d2e-188">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="65d2e-189">В следующем примере предполагается, что метод `Focus` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-189">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="65d2e-190">Переменная `username` заполняется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="65d2e-190">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="65d2e-191">Если в код JavaScript передается пустая ссылка <xref:Microsoft.AspNetCore.Components.ElementReference>, он получает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-191">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="65d2e-192">Для управления ссылками на элементы после завершения отрисовки компонента (для установки начального фокуса на элемент) используйте [метод жизненного цикла компонента `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="65d2e-192">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="65d2e-193">При работе с универсальными типами и возврате значения используйте <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="65d2e-193">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="65d2e-194">Метод `GenericMethod` вызывается для объекта с типом напрямую.</span><span class="sxs-lookup"><span data-stu-id="65d2e-194">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="65d2e-195">В следующем примере предполагается, что метод `GenericMethod` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-195">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="65d2e-196">Ссылки на элементы между компонентами</span><span class="sxs-lookup"><span data-stu-id="65d2e-196">Reference elements across components</span></span>

<span data-ttu-id="65d2e-197">Ссылка <xref:Microsoft.AspNetCore.Components.ElementReference> гарантированно действует только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> компонента (причем ссылка на элемент является `struct`), поэтому ссылка на элемент не может передаваться между компонентами.</span><span class="sxs-lookup"><span data-stu-id="65d2e-197">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="65d2e-198">Чтобы сделать ссылку на элемент доступной для других компонентов, родительский компонент может:</span><span class="sxs-lookup"><span data-stu-id="65d2e-198">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="65d2e-199">разрешить дочерним компонентам регистрировать обратные вызовы;</span><span class="sxs-lookup"><span data-stu-id="65d2e-199">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="65d2e-200">вызывать зарегистрированные обратные вызовы во время события <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> с помощью переданной ссылки на элемент.</span><span class="sxs-lookup"><span data-stu-id="65d2e-200">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="65d2e-201">Такой подход позволяет дочерним компонентам взаимодействовать со ссылкой на элемент родительского компонента косвенным образом.</span><span class="sxs-lookup"><span data-stu-id="65d2e-201">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="65d2e-202">Этот подход показан в следующем примере Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="65d2e-202">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="65d2e-203">В `<head>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-203">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="65d2e-204">В `<body>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-204">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="65d2e-205">`Pages/Index.razor` (родительский компонент):</span><span class="sxs-lookup"><span data-stu-id="65d2e-205">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="65d2e-206">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-206">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="65d2e-207">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="65d2e-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="65d2e-208">`Shared/SurveyPrompt.razor` (дочерний компонент):</span><span class="sxs-lookup"><span data-stu-id="65d2e-208">`Shared/SurveyPrompt.razor` (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="65d2e-209">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-209">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="65d2e-210">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="65d2e-210">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="65d2e-211">Повышение надежности вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="65d2e-211">Harden JS interop calls</span></span>

<span data-ttu-id="65d2e-212">Взаимодействие с JS может завершаться сбоем из-за ошибок сети и в этом случае должно считаться ненадежным.</span><span class="sxs-lookup"><span data-stu-id="65d2e-212">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="65d2e-213">По умолчанию в приложениях Blazor Server время ожидания вызовов взаимодействия с JS на сервере равно одной минуте.</span><span class="sxs-lookup"><span data-stu-id="65d2e-213">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="65d2e-214">Если для приложения допустимо более короткое время ожидания, установите его одним из указанных ниже способов.</span><span class="sxs-lookup"><span data-stu-id="65d2e-214">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="65d2e-215">Глобально в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="65d2e-215">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="65d2e-216">Время ожидания можно указать для отдельного вызова в коде компонента:</span><span class="sxs-lookup"><span data-stu-id="65d2e-216">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="65d2e-217">Дополнительные сведения о нехватке ресурсов см. в статье <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-217">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="65d2e-218">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="65d2e-218">Avoid circular object references</span></span>

<span data-ttu-id="65d2e-219">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="65d2e-219">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="65d2e-220">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="65d2e-220">.NET method calls.</span></span>
* <span data-ttu-id="65d2e-221">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="65d2e-221">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="65d2e-222">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="65d2e-222">For more information, see the following issues:</span></span>

* [<span data-ttu-id="65d2e-223">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="65d2e-223">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="65d2e-224">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="65d2e-224">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="65d2e-225">Изоляция Blazor JavaScript и ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="65d2e-225">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="65d2e-226">Blazor реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="65d2e-226">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="65d2e-227">Изоляция JavaScript обеспечивает следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="65d2e-227">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="65d2e-228">Импортированный JavaScript больше не засоряет глобальное пространство имен.</span><span class="sxs-lookup"><span data-stu-id="65d2e-228">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="65d2e-229">Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65d2e-229">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="65d2e-230">Например, следующий модуль JavaScript экспортирует функцию JavaScript для отображения запроса браузера:</span><span class="sxs-lookup"><span data-stu-id="65d2e-230">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="65d2e-231">Добавьте предыдущий модуль JavaScript в библиотеку .NET в виде статического веб-ресурса (`wwwroot/exampleJsInterop.js`), а затем импортируйте модуль в код .NET с помощью службы <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="65d2e-231">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="65d2e-232">Служба внедряется как `jsRuntime` (не показано) в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="65d2e-232">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<JSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="65d2e-233">Идентификатором `import` в предыдущем примере является специальный идентификатор, используемый специально для импорта модуля JavaScript.</span><span class="sxs-lookup"><span data-stu-id="65d2e-233">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="65d2e-234">Укажите модуль, используя путь к статическому стабильному веб-ресурсу: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-234">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="65d2e-235">Заполнитель `{LIBRARY NAME}` — это имя библиотеки.</span><span class="sxs-lookup"><span data-stu-id="65d2e-235">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="65d2e-236">Заполнитель `{PATH UNDER WWWROOT}` — это путь к скрипту в разделе `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="65d2e-236">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="65d2e-237"><xref:Microsoft.JSInterop.IJSRuntime> импортирует модуль как `JSObjectReference`, который представляет ссылку на объект JavaScript из кода .NET.</span><span class="sxs-lookup"><span data-stu-id="65d2e-237"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `JSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="65d2e-238">Используйте `JSObjectReference` для вызова экспортированных функций JavaScript из модуля:</span><span class="sxs-lookup"><span data-stu-id="65d2e-238">Use the `JSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="65d2e-239">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="65d2e-239">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="65d2e-240">Пример InteropComponent.razor (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="65d2e-240">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="65d2e-241">Выполнение передачи больших объемов данных в приложениях Blazor Server</span><span class="sxs-lookup"><span data-stu-id="65d2e-241">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
