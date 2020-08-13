---
title: Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как вызывать функции JavaScript из методов .NET в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
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
ms.openlocfilehash: 04876976340ff440bb739100f891d8d3612b3754
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012612"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="82ddb-103">Вызов функций JavaScript из методов .NET в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="82ddb-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="82ddb-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="82ddb-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="82ddb-105">Приложение Blazor может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82ddb-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="82ddb-106">Такой подход называется *взаимодействием с JavaScript* (*JS*).</span><span class="sxs-lookup"><span data-stu-id="82ddb-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="82ddb-107">В этой статье рассматривается вызов функций JavaScript из .NET.</span><span class="sxs-lookup"><span data-stu-id="82ddb-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="82ddb-108">Сведения о том, как вызывать методы .NET из JavaScript, см. в статье <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="82ddb-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="82ddb-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="82ddb-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="82ddb-110">Для вызова JavaScript из .NET используйте абстракцию <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="82ddb-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="82ddb-111">Чтобы выполнять вызовы взаимодействия с JS, внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент.</span><span class="sxs-lookup"><span data-stu-id="82ddb-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="82ddb-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> принимает идентификатор функции JavaScript, которую нужно вызвать, вместе с любым числом аргументов, сериализуемых в JSON.</span><span class="sxs-lookup"><span data-stu-id="82ddb-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="82ddb-113">Идентификатор функции задается относительно глобальной области (`window`).</span><span class="sxs-lookup"><span data-stu-id="82ddb-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="82ddb-114">Если нужно вызвать функцию `window.someScope.someFunction`, идентификатором будет `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="82ddb-115">Регистрировать функцию перед ее вызовом не требуется.</span><span class="sxs-lookup"><span data-stu-id="82ddb-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="82ddb-116">Тип возвращаемого значения `T` также должен сериализоваться в JSON.</span><span class="sxs-lookup"><span data-stu-id="82ddb-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="82ddb-117">Тип `T` должен соответствовать типу .NET, который лучше всего соответствует возвращаемому типу JSON.</span><span class="sxs-lookup"><span data-stu-id="82ddb-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="82ddb-118">Для приложений Blazor Server с включенной предварительной обработкой вызвать JavaScript нельзя во время первоначальной предварительной обработки.</span><span class="sxs-lookup"><span data-stu-id="82ddb-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="82ddb-119">Вызовы взаимодействия с JavaScript должны быть отложены до тех пор, пока не будет установлено соединение с браузером.</span><span class="sxs-lookup"><span data-stu-id="82ddb-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="82ddb-120">См. раздел [Обнаружение предварительной обработки в приложении Blazor Server](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="82ddb-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="82ddb-121">Приведенный ниже пример основан на [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), декодере на базе JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82ddb-121">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="82ddb-122">В примере показано, как вызвать функцию JavaScript из метода C#, которая переносит требование из кода разработчика в существующий API JavaScript.</span><span class="sxs-lookup"><span data-stu-id="82ddb-122">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="82ddb-123">Функция JavaScript принимает массив байтов из метода C#, декодирует его и возвращает компоненту текст для отображения.</span><span class="sxs-lookup"><span data-stu-id="82ddb-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="82ddb-124">Внутри элемента `<head>` файла `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) предоставьте функцию JavaScript, которая использует `TextDecoder` для декодирования переданного массива и возвращения декодированного значения:</span><span class="sxs-lookup"><span data-stu-id="82ddb-124">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="82ddb-125">Код JavaScript, например из предыдущего примера, можно также загрузить из файла JavaScript (`.js`) со ссылкой на файл скрипта:</span><span class="sxs-lookup"><span data-stu-id="82ddb-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="82ddb-126">Приведенный ниже компонент выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="82ddb-126">The following component:</span></span>

* <span data-ttu-id="82ddb-127">вызывает функцию JavaScript `convertArray` с помощью `JSRuntime` при нажатии кнопки компонента ( **`Convert Array`** );</span><span class="sxs-lookup"><span data-stu-id="82ddb-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="82ddb-128">после вызова функции JavaScript переданный массив преобразуется в строку.</span><span class="sxs-lookup"><span data-stu-id="82ddb-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="82ddb-129">Строка возвращается компоненту для отображения.</span><span class="sxs-lookup"><span data-stu-id="82ddb-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="82ddb-130">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="82ddb-130">IJSRuntime</span></span>

<span data-ttu-id="82ddb-131">Чтобы использовать абстракцию <xref:Microsoft.JSInterop.IJSRuntime>, можно выбрать один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="82ddb-131">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="82ddb-132">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент Razor (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="82ddb-132">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="82ddb-133">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-133">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="82ddb-134">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="82ddb-134">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="82ddb-135">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в класс (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="82ddb-135">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="82ddb-136">В элементе `<head>` `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-136">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="82ddb-137">Функция вызывается с помощью метода `JSRuntime.InvokeAsync` и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="82ddb-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="82ddb-138">Для динамического создания содержимого с помощью [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) используйте атрибут `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="82ddb-139">В примере клиентского приложения, используемом в этой статье, приложению доступны две функции JavaScript, которые взаимодействуют с моделью DOM для получения вводимых пользователем данных и вывода приветственного сообщения:</span><span class="sxs-lookup"><span data-stu-id="82ddb-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="82ddb-140">`showPrompt`. Создает запрос на ввод пользователем данных (имени пользователя) и возвращает имя вызвавшему объекту.</span><span class="sxs-lookup"><span data-stu-id="82ddb-140">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="82ddb-141">`displayWelcome`. Назначает приветственное сообщение от вызывающего объекта объекту модели DOM со значением `id`, равным `welcome`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-141">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="82ddb-142">`wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="82ddb-143">Поместите тег `<script>` со ссылкой на файл JavaScript в файл `wwwroot/index.html` (Blazor WebAssembly) или `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="82ddb-143">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="82ddb-144">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="82ddb-144">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="82ddb-145">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="82ddb-145">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="82ddb-146">Не помещайте тег `<script>` в файл компонента, так как тег `<script>` не может изменяться динамически.</span><span class="sxs-lookup"><span data-stu-id="82ddb-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="82ddb-147">Методы .NET взаимодействуют с функциями JavaScript в файле `exampleJsInterop.js` путем вызова <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="82ddb-147">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="82ddb-148">Абстракция <xref:Microsoft.JSInterop.IJSRuntime> является асинхронной для поддержки сценариев Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="82ddb-148">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="82ddb-149">Если вы используете приложение Blazor WebAssembly и вам нужно вызывать функцию JavaScript синхронно, выполните нисходящее приведение к <xref:Microsoft.JSInterop.IJSInProcessRuntime> и вызовите <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> вместо этого.</span><span class="sxs-lookup"><span data-stu-id="82ddb-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="82ddb-150">В большинстве библиотек взаимодействия с JS рекомендуется использовать асинхронные интерфейсы API, чтобы обеспечить доступность библиотек в любых сценариях.</span><span class="sxs-lookup"><span data-stu-id="82ddb-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="82ddb-151">Пример приложения включает в себя компонент для демонстрации взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="82ddb-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="82ddb-152">Он выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="82ddb-152">The component:</span></span>

* <span data-ttu-id="82ddb-153">принимает вводимые пользователем данные посредством запроса JavaScript;</span><span class="sxs-lookup"><span data-stu-id="82ddb-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="82ddb-154">возвращает текст компоненту для обработки;</span><span class="sxs-lookup"><span data-stu-id="82ddb-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="82ddb-155">вызывает еще одну функцию JavaScript, которая взаимодействует с моделью DOM для вывода приветственного сообщения.</span><span class="sxs-lookup"><span data-stu-id="82ddb-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="82ddb-156">`Pages/JsInterop.razor`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-156">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="82ddb-157">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="82ddb-157">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="82ddb-158">Если функция `TriggerJsPrompt` выполняется при нажатии кнопки **`Trigger JavaScript Prompt`** , то вызывается функция JavaScript `showPrompt`, предоставленная в файле `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-158">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="82ddb-159">Функция `showPrompt` принимает введенные пользователем данные (имя пользователя), которые кодируются в HTML и возвращаются компоненту.</span><span class="sxs-lookup"><span data-stu-id="82ddb-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="82ddb-160">Компонент сохраняет имя пользователя в локальной переменной `name`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="82ddb-161">Строка, хранящаяся в `name`, включается в приветственное сообщение, которое передается в функцию JavaScript `displayWelcome`, визуализирующую приветственное сообщение в теге заголовка.</span><span class="sxs-lookup"><span data-stu-id="82ddb-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="82ddb-162">Вызов функции void JavaScript</span><span class="sxs-lookup"><span data-stu-id="82ddb-162">Call a void JavaScript function</span></span>

<span data-ttu-id="82ddb-163">Функции JavaScript, возвращающие значение [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) или [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined), вызываются с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="82ddb-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="82ddb-164">Обнаружение предварительной отрисовки в приложении Blazor Server</span><span class="sxs-lookup"><span data-stu-id="82ddb-164">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="82ddb-165">Получение ссылок на элементы</span><span class="sxs-lookup"><span data-stu-id="82ddb-165">Capture references to elements</span></span>

<span data-ttu-id="82ddb-166">В некоторых сценариях взаимодействия с JS требуются ссылки на элементы HTML.</span><span class="sxs-lookup"><span data-stu-id="82ddb-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="82ddb-167">Например, ссылка на элемент может требоваться библиотеке пользовательского интерфейса для инициализации либо необходимо вызывать командные интерфейсы API для элемента, такого как `focus` или `play`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="82ddb-168">Для получения ссылок на элементы HTML в компоненте используйте описанный ниже подход.</span><span class="sxs-lookup"><span data-stu-id="82ddb-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="82ddb-169">Добавьте атрибут `@ref` к элементу HTML.</span><span class="sxs-lookup"><span data-stu-id="82ddb-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="82ddb-170">Определите поле типа <xref:Microsoft.AspNetCore.Components.ElementReference>, имя которого совпадает со значением атрибута `@ref`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-170">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="82ddb-171">В следующем примере показано получение ссылки на элемент `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="82ddb-172">Ссылку на элемент следует использовать только для изменения содержимого пустого элемента, который не взаимодействует с Blazor.</span><span class="sxs-lookup"><span data-stu-id="82ddb-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="82ddb-173">Этот сценарий полезен, если сторонний интерфейс API предоставляет содержимое элементу.</span><span class="sxs-lookup"><span data-stu-id="82ddb-173">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="82ddb-174">Так как Blazor не взаимодействует с элементом, риск конфликта между представлением Blazor элемента и моделью DOM отсутствует.</span><span class="sxs-lookup"><span data-stu-id="82ddb-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="82ddb-175">В следующем примере изменять содержимое неупорядоченного списка (`ul`) *опасно*, так как Blazor взаимодействует с моделью DOM для заполнения элементов этого списка (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="82ddb-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="82ddb-176">Если при взаимодействии с JS содержимое элемента `MyList` изменяется и Blazor пытается применить изменения к элементу, эти изменения не будут соответствовать модели DOM.</span><span class="sxs-lookup"><span data-stu-id="82ddb-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="82ddb-177">В случае с кодом .NET <xref:Microsoft.AspNetCore.Components.ElementReference> является непрозрачным дескриптором.</span><span class="sxs-lookup"><span data-stu-id="82ddb-177">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="82ddb-178">*Единственное*, что можно сделать с <xref:Microsoft.AspNetCore.Components.ElementReference>, — передать в код JavaScript посредством взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="82ddb-178">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="82ddb-179">При этом код на стороне JavaScript получает экземпляр `HTMLElement`, который может использоваться с обычными интерфейсами API DOM.</span><span class="sxs-lookup"><span data-stu-id="82ddb-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="82ddb-180">Например, в приведенном ниже коде определяется метод расширения .NET, который позволяет установить фокус на элемент.</span><span class="sxs-lookup"><span data-stu-id="82ddb-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="82ddb-181">`exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-181">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="82ddb-182">Для вызова функции JavaScript, которая не возвращает значение, используйте метод <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="82ddb-182">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="82ddb-183">Следующий код устанавливает фокус на элементе для ввода имени пользователя, вызывая предыдущую функцию JavaScript с полученной ссылкой <xref:Microsoft.AspNetCore.Components.ElementReference>:</span><span class="sxs-lookup"><span data-stu-id="82ddb-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="82ddb-184">Чтобы использовать метод расширения, создайте статический метод расширения, который принимает экземпляр <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="82ddb-184">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="82ddb-185">Метод `Focus` вызывается для объекта напрямую.</span><span class="sxs-lookup"><span data-stu-id="82ddb-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="82ddb-186">В следующем примере предполагается, что метод `Focus` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="82ddb-187">Переменная `username` заполняется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="82ddb-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="82ddb-188">Если в код JavaScript передается пустая ссылка <xref:Microsoft.AspNetCore.Components.ElementReference>, он получает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-188">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="82ddb-189">Для управления ссылками на элементы после завершения отрисовки компонента (для установки начального фокуса на элемент) используйте [метод жизненного цикла компонента `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="82ddb-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="82ddb-190">При работе с универсальными типами и возврате значения используйте <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="82ddb-190">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="82ddb-191">Метод `GenericMethod` вызывается для объекта с типом напрямую.</span><span class="sxs-lookup"><span data-stu-id="82ddb-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="82ddb-192">В следующем примере предполагается, что метод `GenericMethod` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="82ddb-193">Ссылки на элементы между компонентами</span><span class="sxs-lookup"><span data-stu-id="82ddb-193">Reference elements across components</span></span>

<span data-ttu-id="82ddb-194">Ссылка <xref:Microsoft.AspNetCore.Components.ElementReference> гарантированно действует только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> компонента (причем ссылка на элемент является `struct`), поэтому ссылка на элемент не может передаваться между компонентами.</span><span class="sxs-lookup"><span data-stu-id="82ddb-194">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="82ddb-195">Чтобы сделать ссылку на элемент доступной для других компонентов, родительский компонент может:</span><span class="sxs-lookup"><span data-stu-id="82ddb-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="82ddb-196">разрешить дочерним компонентам регистрировать обратные вызовы;</span><span class="sxs-lookup"><span data-stu-id="82ddb-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="82ddb-197">вызывать зарегистрированные обратные вызовы во время события <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> с помощью переданной ссылки на элемент.</span><span class="sxs-lookup"><span data-stu-id="82ddb-197">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="82ddb-198">Такой подход позволяет дочерним компонентам взаимодействовать со ссылкой на элемент родительского компонента косвенным образом.</span><span class="sxs-lookup"><span data-stu-id="82ddb-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="82ddb-199">Этот подход показан в следующем примере Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="82ddb-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="82ddb-200">В `<head>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-200">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="82ddb-201">В `<body>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-201">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="82ddb-202">`Pages/Index.razor` (родительский компонент):</span><span class="sxs-lookup"><span data-stu-id="82ddb-202">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="82ddb-203">`Pages/Index.razor.cs`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-203">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="82ddb-204">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="82ddb-204">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="82ddb-205">`Shared/SurveyPrompt.razor` (дочерний компонент):</span><span class="sxs-lookup"><span data-stu-id="82ddb-205">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="82ddb-206">`Shared/SurveyPrompt.razor.cs`.</span><span class="sxs-lookup"><span data-stu-id="82ddb-206">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="82ddb-207">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="82ddb-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="82ddb-208">Повышение надежности вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="82ddb-208">Harden JS interop calls</span></span>

<span data-ttu-id="82ddb-209">Взаимодействие с JS может завершаться сбоем из-за ошибок сети и в этом случае должно считаться ненадежным.</span><span class="sxs-lookup"><span data-stu-id="82ddb-209">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="82ddb-210">По умолчанию в приложениях Blazor Server время ожидания вызовов взаимодействия с JS на сервере равно одной минуте.</span><span class="sxs-lookup"><span data-stu-id="82ddb-210">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="82ddb-211">Если для приложения допустимо более короткое время ожидания, установите его одним из указанных ниже способов.</span><span class="sxs-lookup"><span data-stu-id="82ddb-211">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="82ddb-212">Глобально в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="82ddb-212">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="82ddb-213">Время ожидания можно указать для отдельного вызова в коде компонента:</span><span class="sxs-lookup"><span data-stu-id="82ddb-213">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="82ddb-214">Дополнительные сведения о нехватке ресурсов см. в статье <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="82ddb-214">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="82ddb-215">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="82ddb-215">Avoid circular object references</span></span>

<span data-ttu-id="82ddb-216">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="82ddb-216">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="82ddb-217">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="82ddb-217">.NET method calls.</span></span>
* <span data-ttu-id="82ddb-218">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="82ddb-218">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="82ddb-219">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="82ddb-219">For more information, see the following issues:</span></span>

* [<span data-ttu-id="82ddb-220">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="82ddb-220">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="82ddb-221">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="82ddb-221">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="82ddb-222">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="82ddb-222">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="82ddb-223">Пример InteropComponent.razor (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="82ddb-223">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="82ddb-224">Выполнение передачи больших объемов данных в приложениях Blazor Server</span><span class="sxs-lookup"><span data-stu-id="82ddb-224">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
