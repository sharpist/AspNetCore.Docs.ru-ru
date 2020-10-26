---
title: 'Вызов функций JavaScript из методов .NET в ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: Узнайте, как вызывать функции JavaScript из методов .NET в приложениях :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 3bd881b124e00b91ab0aa9d3eb7531f10ef895f2
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326499"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="173b5-103">Вызов функций JavaScript из методов .NET в ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="173b5-103">Call JavaScript functions from .NET methods in ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="173b5-104">Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="173b5-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="173b5-105">Приложение :::no-loc(Blazor)::: может вызывать функции JavaScript из методов .NET и методы .NET из функций JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-105">A :::no-loc(Blazor)::: app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="173b5-106">Такой подход называется *взаимодействием с JavaScript* ( *JS* ).</span><span class="sxs-lookup"><span data-stu-id="173b5-106">These scenarios are called *JavaScript interoperability* ( *JS interop* ).</span></span>

<span data-ttu-id="173b5-107">В этой статье рассматривается вызов функций JavaScript из .NET.</span><span class="sxs-lookup"><span data-stu-id="173b5-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="173b5-108">Сведения о том, как вызывать методы .NET из JavaScript, см. в статье <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="173b5-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="173b5-109">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="173b5-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="173b5-110">Для вызова JavaScript из .NET используйте абстракцию <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="173b5-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="173b5-111">Чтобы выполнять вызовы взаимодействия с JS, внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент.</span><span class="sxs-lookup"><span data-stu-id="173b5-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="173b5-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> принимает идентификатор функции JavaScript, которую нужно вызвать, вместе с любым числом аргументов, сериализуемых в JSON.</span><span class="sxs-lookup"><span data-stu-id="173b5-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="173b5-113">Идентификатор функции задается относительно глобальной области (`window`).</span><span class="sxs-lookup"><span data-stu-id="173b5-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="173b5-114">Если нужно вызвать функцию `window.someScope.someFunction`, идентификатором будет `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="173b5-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="173b5-115">Регистрировать функцию перед ее вызовом не требуется.</span><span class="sxs-lookup"><span data-stu-id="173b5-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="173b5-116">Тип возвращаемого значения `T` также должен сериализоваться в JSON.</span><span class="sxs-lookup"><span data-stu-id="173b5-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="173b5-117">Тип `T` должен соответствовать типу .NET, который лучше всего соответствует возвращаемому типу JSON.</span><span class="sxs-lookup"><span data-stu-id="173b5-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="173b5-118">Функции JavaScript, возвращающие [обещание](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise), вызываются с помощью <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="173b5-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="173b5-119">`InvokeAsync` распаковывает обещание и возвращает значение, ожидаемое обещанием.</span><span class="sxs-lookup"><span data-stu-id="173b5-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="173b5-120">Для приложений :::no-loc(Blazor Server)::: с включенной предварительной обработкой вызвать JavaScript нельзя во время первоначальной предварительной обработки.</span><span class="sxs-lookup"><span data-stu-id="173b5-120">For :::no-loc(Blazor Server)::: apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="173b5-121">Вызовы взаимодействия с JavaScript должны быть отложены до тех пор, пока не будет установлено соединение с браузером.</span><span class="sxs-lookup"><span data-stu-id="173b5-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="173b5-122">См. раздел [Обнаружение предварительной обработки в приложении :::no-loc(Blazor Server):::](#detect-when-a-blazor-server-app-is-prerendering).</span><span class="sxs-lookup"><span data-stu-id="173b5-122">For more information, see the [Detect when a :::no-loc(Blazor Server)::: app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="173b5-123">Приведенный ниже пример основан на [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), декодере на базе JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="173b5-124">В примере показано, как вызвать функцию JavaScript из метода C#, которая переносит требование из кода разработчика в существующий API JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="173b5-125">Функция JavaScript принимает массив байтов из метода C#, декодирует его и возвращает компоненту текст для отображения.</span><span class="sxs-lookup"><span data-stu-id="173b5-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="173b5-126">Внутри элемента `<head>` файла `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) или `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) предоставьте функцию JavaScript, которая использует `TextDecoder` для декодирования переданного массива и возвращения декодированного значения:</span><span class="sxs-lookup"><span data-stu-id="173b5-126">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="173b5-127">Код JavaScript, например из предыдущего примера, можно также загрузить из файла JavaScript (`.js`) со ссылкой на файл скрипта:</span><span class="sxs-lookup"><span data-stu-id="173b5-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="173b5-128">Приведенный ниже компонент выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="173b5-128">The following component:</span></span>

* <span data-ttu-id="173b5-129">вызывает функцию JavaScript `convertArray` с помощью `JSRuntime` при нажатии кнопки компонента ( **`Convert Array`** );</span><span class="sxs-lookup"><span data-stu-id="173b5-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button ( **`Convert Array`** ) is selected.</span></span>
* <span data-ttu-id="173b5-130">после вызова функции JavaScript переданный массив преобразуется в строку.</span><span class="sxs-lookup"><span data-stu-id="173b5-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="173b5-131">Строка возвращается компоненту для отображения.</span><span class="sxs-lookup"><span data-stu-id="173b5-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="173b5-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="173b5-132">IJSRuntime</span></span>

<span data-ttu-id="173b5-133">Чтобы использовать абстракцию <xref:Microsoft.JSInterop.IJSRuntime>, можно выбрать один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="173b5-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="173b5-134">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в компонент :::no-loc(Razor)::: (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="173b5-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the :::no-loc(Razor)::: component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="173b5-135">В элементе `<head>` `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) или `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="173b5-135">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="173b5-136">Функция вызывается с помощью метода <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> и не возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="173b5-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="173b5-137">Внедрите абстракцию <xref:Microsoft.JSInterop.IJSRuntime> в класс (`.cs`):</span><span class="sxs-lookup"><span data-stu-id="173b5-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="173b5-138">В элементе `<head>` `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) или `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::) укажите функцию JavaScript `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="173b5-138">Inside the `<head>` element of `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="173b5-139">Функция вызывается с помощью метода `JSRuntime.InvokeAsync` и возвращает значение:</span><span class="sxs-lookup"><span data-stu-id="173b5-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="173b5-140">Для динамического создания содержимого с помощью [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic) используйте атрибут `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="173b5-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="173b5-141">В примере клиентского приложения, используемом в этой статье, приложению доступны две функции JavaScript, которые взаимодействуют с моделью DOM для получения вводимых пользователем данных и вывода приветственного сообщения:</span><span class="sxs-lookup"><span data-stu-id="173b5-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="173b5-142">`showPrompt`. Создает запрос на ввод пользователем данных (имени пользователя) и возвращает имя вызвавшему объекту.</span><span class="sxs-lookup"><span data-stu-id="173b5-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="173b5-143">`displayWelcome`. Назначает приветственное сообщение от вызывающего объекта объекту модели DOM со значением `id`, равным `welcome`.</span><span class="sxs-lookup"><span data-stu-id="173b5-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="173b5-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="173b5-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="173b5-145">Поместите тег `<script>` со ссылкой на файл JavaScript в файл `wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::) или `Pages/_Host.cshtml` (:::no-loc(Blazor Server):::).</span><span class="sxs-lookup"><span data-stu-id="173b5-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (:::no-loc(Blazor WebAssembly):::) or `Pages/_Host.cshtml` file (:::no-loc(Blazor Server):::).</span></span>

<span data-ttu-id="173b5-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span><span class="sxs-lookup"><span data-stu-id="173b5-146">`wwwroot/index.html` (:::no-loc(Blazor WebAssembly):::):</span></span>

[!code-html[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="173b5-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span><span class="sxs-lookup"><span data-stu-id="173b5-147">`Pages/_Host.cshtml` (:::no-loc(Blazor Server):::):</span></span>

[!code-cshtml[](./common/samples/3.x/:::no-loc(Blazor):::ServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="173b5-148">Не помещайте тег `<script>` в файл компонента, так как тег `<script>` не может изменяться динамически.</span><span class="sxs-lookup"><span data-stu-id="173b5-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="173b5-149">Методы .NET взаимодействуют с функциями JavaScript в файле `exampleJsInterop.js` путем вызова <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="173b5-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="173b5-150">Абстракция <xref:Microsoft.JSInterop.IJSRuntime> является асинхронной для поддержки сценариев :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="173b5-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for :::no-loc(Blazor Server)::: scenarios.</span></span> <span data-ttu-id="173b5-151">Если вы используете приложение :::no-loc(Blazor WebAssembly)::: и вам нужно вызывать функцию JavaScript синхронно, выполните нисходящее приведение к <xref:Microsoft.JSInterop.IJSInProcessRuntime> и вызовите <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> вместо этого.</span><span class="sxs-lookup"><span data-stu-id="173b5-151">If the app is a :::no-loc(Blazor WebAssembly)::: app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="173b5-152">В большинстве библиотек взаимодействия с JS рекомендуется использовать асинхронные интерфейсы API, чтобы обеспечить доступность библиотек в любых сценариях.</span><span class="sxs-lookup"><span data-stu-id="173b5-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="173b5-153">Сведения о включении изоляции JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) см. в разделе об [изоляции JavaScript :::no-loc(Blazor)::: и ссылках на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="173b5-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [:::no-loc(Blazor)::: JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="173b5-154">Пример приложения включает в себя компонент для демонстрации взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="173b5-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="173b5-155">Он выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="173b5-155">The component:</span></span>

* <span data-ttu-id="173b5-156">принимает вводимые пользователем данные посредством запроса JavaScript;</span><span class="sxs-lookup"><span data-stu-id="173b5-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="173b5-157">возвращает текст компоненту для обработки;</span><span class="sxs-lookup"><span data-stu-id="173b5-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="173b5-158">вызывает еще одну функцию JavaScript, которая взаимодействует с моделью DOM для вывода приветственного сообщения.</span><span class="sxs-lookup"><span data-stu-id="173b5-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="173b5-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="173b5-159">`Pages/JsInterop.razor`:</span></span>

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
                $"Hello {name}! Welcome to :::no-loc(Blazor):::!");
    }
}
```

<span data-ttu-id="173b5-160">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="173b5-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

1. <span data-ttu-id="173b5-161">Если функция `TriggerJsPrompt` выполняется при нажатии кнопки **`Trigger JavaScript Prompt`** , то вызывается функция JavaScript `showPrompt`, предоставленная в файле `wwwroot/exampleJsInterop.js`.</span><span class="sxs-lookup"><span data-stu-id="173b5-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="173b5-162">Функция `showPrompt` принимает введенные пользователем данные (имя пользователя), которые кодируются в HTML и возвращаются компоненту.</span><span class="sxs-lookup"><span data-stu-id="173b5-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="173b5-163">Компонент сохраняет имя пользователя в локальной переменной `name`.</span><span class="sxs-lookup"><span data-stu-id="173b5-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="173b5-164">Строка, хранящаяся в `name`, включается в приветственное сообщение, которое передается в функцию JavaScript `displayWelcome`, визуализирующую приветственное сообщение в теге заголовка.</span><span class="sxs-lookup"><span data-stu-id="173b5-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="173b5-165">Вызов функции void JavaScript</span><span class="sxs-lookup"><span data-stu-id="173b5-165">Call a void JavaScript function</span></span>

<span data-ttu-id="173b5-166">Используйте <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> в следующих случаях:</span><span class="sxs-lookup"><span data-stu-id="173b5-166">Use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> for the following:</span></span>

* <span data-ttu-id="173b5-167">для функций JavaScript, возвращающих значение [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) или [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined);</span><span class="sxs-lookup"><span data-stu-id="173b5-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined).</span></span>
* <span data-ttu-id="173b5-168">если .NET не нужно считывать результат вызова JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-168">If .NET isn't required to read the result of a JavaScript call.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="173b5-169">Обнаружение предварительной отрисовки в приложении :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="173b5-169">Detect when a :::no-loc(Blazor Server)::: app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="173b5-170">Получение ссылок на элементы</span><span class="sxs-lookup"><span data-stu-id="173b5-170">Capture references to elements</span></span>

<span data-ttu-id="173b5-171">В некоторых сценариях взаимодействия с JS требуются ссылки на элементы HTML.</span><span class="sxs-lookup"><span data-stu-id="173b5-171">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="173b5-172">Например, ссылка на элемент может требоваться библиотеке пользовательского интерфейса для инициализации либо необходимо вызывать командные интерфейсы API для элемента, такого как `focus` или `play`.</span><span class="sxs-lookup"><span data-stu-id="173b5-172">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="173b5-173">Для получения ссылок на элементы HTML в компоненте используйте описанный ниже подход.</span><span class="sxs-lookup"><span data-stu-id="173b5-173">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="173b5-174">Добавьте атрибут `@ref` к элементу HTML.</span><span class="sxs-lookup"><span data-stu-id="173b5-174">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="173b5-175">Определите поле типа <xref:Microsoft.AspNetCore.Components.ElementReference>, имя которого совпадает со значением атрибута `@ref`.</span><span class="sxs-lookup"><span data-stu-id="173b5-175">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="173b5-176">В следующем примере показано получение ссылки на элемент `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="173b5-176">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="173b5-177">Ссылку на элемент следует использовать только для изменения содержимого пустого элемента, который не взаимодействует с :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="173b5-177">Only use an element reference to mutate the contents of an empty element that doesn't interact with :::no-loc(Blazor):::.</span></span> <span data-ttu-id="173b5-178">Этот сценарий полезен, если сторонний интерфейс API предоставляет содержимое элементу.</span><span class="sxs-lookup"><span data-stu-id="173b5-178">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="173b5-179">Так как :::no-loc(Blazor)::: не взаимодействует с элементом, риск конфликта между представлением :::no-loc(Blazor)::: элемента и моделью DOM отсутствует.</span><span class="sxs-lookup"><span data-stu-id="173b5-179">Because :::no-loc(Blazor)::: doesn't interact with the element, there's no possibility of a conflict between :::no-loc(Blazor):::'s representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="173b5-180">В следующем примере изменять содержимое неупорядоченного списка (`ul`) *опасно* , так как :::no-loc(Blazor)::: взаимодействует с моделью DOM для заполнения элементов этого списка (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="173b5-180">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because :::no-loc(Blazor)::: interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="173b5-181">Если при взаимодействии с JS содержимое элемента `MyList` изменяется и :::no-loc(Blazor)::: пытается применить изменения к элементу, эти изменения не будут соответствовать модели DOM.</span><span class="sxs-lookup"><span data-stu-id="173b5-181">If JS interop mutates the contents of element `MyList` and :::no-loc(Blazor)::: attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="173b5-182">В случае с кодом .NET <xref:Microsoft.AspNetCore.Components.ElementReference> является непрозрачным дескриптором.</span><span class="sxs-lookup"><span data-stu-id="173b5-182">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="173b5-183">*Единственное* , что можно сделать с <xref:Microsoft.AspNetCore.Components.ElementReference>, — передать в код JavaScript посредством взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="173b5-183">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="173b5-184">При этом код на стороне JavaScript получает экземпляр `HTMLElement`, который может использоваться с обычными интерфейсами API DOM.</span><span class="sxs-lookup"><span data-stu-id="173b5-184">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="173b5-185">Например, в приведенном ниже коде определяется метод расширения .NET, который позволяет установить фокус на элемент.</span><span class="sxs-lookup"><span data-stu-id="173b5-185">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="173b5-186">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="173b5-186">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="173b5-187">Для вызова функции JavaScript, которая не возвращает значение, используйте метод <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="173b5-187">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="173b5-188">Следующий код устанавливает фокус на элементе для ввода имени пользователя, вызывая предыдущую функцию JavaScript с полученной ссылкой <xref:Microsoft.AspNetCore.Components.ElementReference>:</span><span class="sxs-lookup"><span data-stu-id="173b5-188">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="173b5-189">Чтобы использовать метод расширения, создайте статический метод расширения, который принимает экземпляр <xref:Microsoft.JSInterop.IJSRuntime>:</span><span class="sxs-lookup"><span data-stu-id="173b5-189">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="173b5-190">Метод `Focus` вызывается для объекта напрямую.</span><span class="sxs-lookup"><span data-stu-id="173b5-190">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="173b5-191">В следующем примере предполагается, что метод `Focus` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="173b5-191">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="173b5-192">Переменная `username` заполняется только после отрисовки компонента.</span><span class="sxs-lookup"><span data-stu-id="173b5-192">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="173b5-193">Если в код JavaScript передается пустая ссылка <xref:Microsoft.AspNetCore.Components.ElementReference>, он получает значение `null`.</span><span class="sxs-lookup"><span data-stu-id="173b5-193">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="173b5-194">Для управления ссылками на элементы после завершения отрисовки компонента (для установки начального фокуса на элемент) используйте [метод жизненного цикла компонента `OnAfterRenderAsync` или `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="173b5-194">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="173b5-195">При работе с универсальными типами и возврате значения используйте <xref:System.Threading.Tasks.ValueTask%601>:</span><span class="sxs-lookup"><span data-stu-id="173b5-195">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="173b5-196">Метод `GenericMethod` вызывается для объекта с типом напрямую.</span><span class="sxs-lookup"><span data-stu-id="173b5-196">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="173b5-197">В следующем примере предполагается, что метод `GenericMethod` доступен из пространства имен `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="173b5-197">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="173b5-198">Ссылки на элементы между компонентами</span><span class="sxs-lookup"><span data-stu-id="173b5-198">Reference elements across components</span></span>

<span data-ttu-id="173b5-199">Экземпляр <xref:Microsoft.AspNetCore.Components.ElementReference> является гарантированно допустимым только в методе <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> компонента (причем ссылка на элемент имеет тип `struct`), поэтому ссылка на элемент не может передаваться между компонентами.</span><span class="sxs-lookup"><span data-stu-id="173b5-199">An <xref:Microsoft.AspNetCore.Components.ElementReference> instance is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span> <span data-ttu-id="173b5-200">Чтобы сделать ссылку на элемент доступной для других компонентов, родительский компонент может:</span><span class="sxs-lookup"><span data-stu-id="173b5-200">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="173b5-201">разрешить дочерним компонентам регистрировать обратные вызовы;</span><span class="sxs-lookup"><span data-stu-id="173b5-201">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="173b5-202">вызывать зарегистрированные обратные вызовы во время события <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> с помощью переданной ссылки на элемент.</span><span class="sxs-lookup"><span data-stu-id="173b5-202">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="173b5-203">Такой подход позволяет дочерним компонентам взаимодействовать со ссылкой на элемент родительского компонента косвенным образом.</span><span class="sxs-lookup"><span data-stu-id="173b5-203">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="173b5-204">Этот подход показан в следующем примере :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="173b5-204">The following :::no-loc(Blazor WebAssembly)::: example illustrates the approach.</span></span>

<span data-ttu-id="173b5-205">В `<head>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="173b5-205">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="173b5-206">В `<body>` `wwwroot/index.html`:</span><span class="sxs-lookup"><span data-stu-id="173b5-206">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="173b5-207">`Pages/Index.razor` (родительский компонент):</span><span class="sxs-lookup"><span data-stu-id="173b5-207">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is :::no-loc(Blazor)::: working for you?" />
```

<span data-ttu-id="173b5-208">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="173b5-208">`Pages/Index.razor.cs`:</span></span>

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

<span data-ttu-id="173b5-209">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="173b5-209">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

<span data-ttu-id="173b5-210">`Shared/SurveyPrompt.razor` (дочерний компонент):</span><span class="sxs-lookup"><span data-stu-id="173b5-210">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="173b5-211">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="173b5-211">`Shared/SurveyPrompt.razor.cs`:</span></span>

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

<span data-ttu-id="173b5-212">Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `:::no-loc(Blazor):::Sample`).</span><span class="sxs-lookup"><span data-stu-id="173b5-212">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `:::no-loc(Blazor):::Sample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="173b5-213">Повышение надежности вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="173b5-213">Harden JS interop calls</span></span>

<span data-ttu-id="173b5-214">Взаимодействие с JS может завершаться сбоем из-за ошибок сети и в этом случае должно считаться ненадежным.</span><span class="sxs-lookup"><span data-stu-id="173b5-214">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="173b5-215">По умолчанию в приложениях :::no-loc(Blazor Server)::: время ожидания вызовов взаимодействия с JS на сервере равно одной минуте.</span><span class="sxs-lookup"><span data-stu-id="173b5-215">By default, a :::no-loc(Blazor Server)::: app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="173b5-216">Если для приложения допустимо более короткое время ожидания, установите его одним из указанных ниже способов.</span><span class="sxs-lookup"><span data-stu-id="173b5-216">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="173b5-217">Глобально в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="173b5-217">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSide:::no-loc(Blazor):::(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="173b5-218">Время ожидания можно указать для отдельного вызова в коде компонента:</span><span class="sxs-lookup"><span data-stu-id="173b5-218">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="173b5-219">Дополнительные сведения о нехватке ресурсов см. в статье <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="173b5-219">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="173b5-220">Исключение циклических ссылок на объекты</span><span class="sxs-lookup"><span data-stu-id="173b5-220">Avoid circular object references</span></span>

<span data-ttu-id="173b5-221">Объекты, содержащие циклические ссылки, не могут быть сериализованы на клиенте для:</span><span class="sxs-lookup"><span data-stu-id="173b5-221">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="173b5-222">вызовов метода .NET.</span><span class="sxs-lookup"><span data-stu-id="173b5-222">.NET method calls.</span></span>
* <span data-ttu-id="173b5-223">Вызов метода JavaScript из C#, если тип возвращаемого значения имеет циклические ссылки.</span><span class="sxs-lookup"><span data-stu-id="173b5-223">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="173b5-224">Дополнительные сведения см. на следующих страницах:</span><span class="sxs-lookup"><span data-stu-id="173b5-224">For more information, see the following issues:</span></span>

* [<span data-ttu-id="173b5-225">Циклические ссылки не поддерживаются, вторая серия (DotNet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="173b5-225">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="173b5-226">Предложение. Добавить механизм для обработки циклических ссылок при сериализации (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="173b5-226">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="173b5-227">Изоляция :::no-loc(Blazor)::: JavaScript и ссылки на объекты</span><span class="sxs-lookup"><span data-stu-id="173b5-227">:::no-loc(Blazor)::: JavaScript isolation and object references</span></span>

<span data-ttu-id="173b5-228">:::no-loc(Blazor)::: реализует изоляцию JavaScript в стандартных [модулях JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="173b5-228">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="173b5-229">Изоляция JavaScript обеспечивает следующие преимущества:</span><span class="sxs-lookup"><span data-stu-id="173b5-229">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="173b5-230">Импортированный JavaScript больше не засоряет глобальное пространство имен.</span><span class="sxs-lookup"><span data-stu-id="173b5-230">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="173b5-231">Пользователям библиотеки и компонентов не требуется импортировать связанный код JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-231">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="173b5-232">Например, следующий модуль JavaScript экспортирует функцию JavaScript для отображения запроса браузера:</span><span class="sxs-lookup"><span data-stu-id="173b5-232">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="173b5-233">Добавьте предыдущий модуль JavaScript в библиотеку .NET в виде статического веб-ресурса (`wwwroot/exampleJsInterop.js`), а затем импортируйте модуль в код .NET с помощью службы <xref:Microsoft.JSInterop.IJSRuntime>.</span><span class="sxs-lookup"><span data-stu-id="173b5-233">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="173b5-234">Служба внедряется как `jsRuntime` (не показано) в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="173b5-234">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="173b5-235">Идентификатором `import` в предыдущем примере является специальный идентификатор, используемый специально для импорта модуля JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-235">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="173b5-236">Укажите модуль, используя путь к статическому стабильному веб-ресурсу: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span><span class="sxs-lookup"><span data-stu-id="173b5-236">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="173b5-237">Заполнитель `{LIBRARY NAME}` — это имя библиотеки.</span><span class="sxs-lookup"><span data-stu-id="173b5-237">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="173b5-238">Заполнитель `{PATH UNDER WWWROOT}` — это путь к скрипту в разделе `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="173b5-238">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="173b5-239"><xref:Microsoft.JSInterop.IJSRuntime> импортирует модуль как `IJSObjectReference`, который представляет ссылку на объект JavaScript из кода .NET.</span><span class="sxs-lookup"><span data-stu-id="173b5-239"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="173b5-240">Используйте `IJSObjectReference` для вызова экспортированных функций JavaScript из модуля:</span><span class="sxs-lookup"><span data-stu-id="173b5-240">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="173b5-241">`IJSInProcessObjectReference` представляет ссылку на объект JavaScript, функции которого могут вызываться синхронно.</span><span class="sxs-lookup"><span data-stu-id="173b5-241">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="173b5-242">`IJSUnmarshalledObjectReference` представляет ссылку на объект JavaScript, функции которого могут вызываться без дополнительных затрат, связанных с сериализацией данных .NET.</span><span class="sxs-lookup"><span data-stu-id="173b5-242">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="173b5-243">Можно использовать в :::no-loc(Blazor WebAssembly):::, когда важна производительность.</span><span class="sxs-lookup"><span data-stu-id="173b5-243">This can be used in :::no-loc(Blazor WebAssembly)::: when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = :::no-loc(Blazor):::.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="173b5-244">Использование библиотек JavaScript, отображающих пользовательский интерфейс (элементы DOM)</span><span class="sxs-lookup"><span data-stu-id="173b5-244">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="173b5-245">Иногда может потребоваться использовать библиотеки JavaScript, которые создают видимые элементы пользовательского интерфейса в модели DOM браузера.</span><span class="sxs-lookup"><span data-stu-id="173b5-245">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="173b5-246">На первый взгляд, это может показаться затруднительным, так как система сравнения :::no-loc(Blazor)::: предполагает наличие контроля над деревом элементов DOM и в ней возникают ошибки, если какой-либо внешний код изменяет дерево DOM и механизм применения различий становится недействительным.</span><span class="sxs-lookup"><span data-stu-id="173b5-246">At first glance, this might seem difficult because :::no-loc(Blazor):::'s diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="173b5-247">Это ограничение не относится лишь к :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="173b5-247">This isn't a :::no-loc(Blazor):::-specific limitation.</span></span> <span data-ttu-id="173b5-248">Такая же проблема возникает при использовании любой платформы пользовательского интерфейса на основе сравнения.</span><span class="sxs-lookup"><span data-stu-id="173b5-248">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="173b5-249">К счастью, в пользовательский интерфейс компонента :::no-loc(Blazor)::: можно легко внедрить внешний пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="173b5-249">Fortunately, it's straightforward to embed externally-generated UI within a :::no-loc(Blazor)::: component UI reliably.</span></span> <span data-ttu-id="173b5-250">Рекомендуемым методом является создание пустого элемента кодом компонента (в файле `.razor`).</span><span class="sxs-lookup"><span data-stu-id="173b5-250">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="173b5-251">С точки зрения системы сравнения :::no-loc(Blazor)::: элемент всегда пуст, поэтому отрисовщик не выполняет рекурсию в него и не обрабатывает его содержимое.</span><span class="sxs-lookup"><span data-stu-id="173b5-251">As far as :::no-loc(Blazor):::'s diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="173b5-252">Это позволяет спокойно заполнить элемент произвольным содержимым, управляемым извне.</span><span class="sxs-lookup"><span data-stu-id="173b5-252">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="173b5-253">Данный принцип демонстрируется в приведенном ниже примере.</span><span class="sxs-lookup"><span data-stu-id="173b5-253">The following example demonstrates the concept.</span></span> <span data-ttu-id="173b5-254">В операторе `if`, когда `firstRender` имеет значение `true`, сделайте что-нибудь с `myElement`.</span><span class="sxs-lookup"><span data-stu-id="173b5-254">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="173b5-255">Например, вызовите внешнюю библиотеку JavaScript для заполнения элемента.</span><span class="sxs-lookup"><span data-stu-id="173b5-255">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="173b5-256">:::no-loc(Blazor)::: оставляет содержимое элемента без изменений, пока сам компонент не будет удален.</span><span class="sxs-lookup"><span data-stu-id="173b5-256">:::no-loc(Blazor)::: leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="173b5-257">При удалении компонента удаляется и все его поддерево DOM.</span><span class="sxs-lookup"><span data-stu-id="173b5-257">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a :::no-loc(Blazor)::: component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="173b5-258">В качестве более подробного примера рассмотрим следующий компонент, который отрисовывает интерактивную карту с помощью [интерфейсов API Mapbox с открытым кодом](https://www.mapbox.com/).</span><span class="sxs-lookup"><span data-stu-id="173b5-258">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="173b5-259">Соответствующий модуль JavaScript, который следует поместить в `wwwroot/mapComponent.js`, выглядит следующим образом.</span><span class="sxs-lookup"><span data-stu-id="173b5-259">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="173b5-260">В предыдущем примере замените строку `{ACCESS TOKEN}` допустимым маркером доступа, который можно получить из https://account.mapbox.com.</span><span class="sxs-lookup"><span data-stu-id="173b5-260">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="173b5-261">Чтобы обеспечить правильный стиль, добавьте следующий тег таблицы стилей на страницу HTML узла (`index.html` или `_Host.cshtml`).</span><span class="sxs-lookup"><span data-stu-id="173b5-261">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="173b5-262">В предыдущем примере создается интерактивный пользовательский интерфейс карты, в котором пользователь может выполнять следующие действия:</span><span class="sxs-lookup"><span data-stu-id="173b5-262">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="173b5-263">прокручивать карту или изменять масштаб перетаскиванием;</span><span class="sxs-lookup"><span data-stu-id="173b5-263">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="173b5-264">переходить к заданным расположениям нажатием кнопок.</span><span class="sxs-lookup"><span data-stu-id="173b5-264">Click buttons to jump to predefined locations.</span></span>

![Карта улиц Mapbox для Токио (Япония) с кнопками для выбора Бристоля (Великобритания) и Токио (Япония)](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="173b5-266">Важном понимать следующие основные моменты.</span><span class="sxs-lookup"><span data-stu-id="173b5-266">The key points to understand are:</span></span>

 * <span data-ttu-id="173b5-267">В случае с :::no-loc(Blazor)::: `<div>` с `@ref="mapElement"` остается пустым.</span><span class="sxs-lookup"><span data-stu-id="173b5-267">The `<div>` with `@ref="mapElement"` is left empty as far as :::no-loc(Blazor)::: is concerned.</span></span> <span data-ttu-id="173b5-268">Поэтому `mapbox-gl.js` может спокойно заполнять его и затем изменять его содержимое.</span><span class="sxs-lookup"><span data-stu-id="173b5-268">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="173b5-269">Этот метод можно использовать с любой библиотекой JavaScript, которая отрисовывает пользовательский интерфейс.</span><span class="sxs-lookup"><span data-stu-id="173b5-269">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="173b5-270">В компоненты :::no-loc(Blazor)::: можно даже внедрять компоненты из сторонней платформы одностраничных приложений JavaScript, если они не пытаются изменить другие части страницы.</span><span class="sxs-lookup"><span data-stu-id="173b5-270">You could even embed components from a third-party JavaScript SPA framework inside :::no-loc(Blazor)::: components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="173b5-271">Ситуация, когда внешний код JavaScript изменяет элементы, которые :::no-loc(Blazor)::: не считает пустыми, *небезопасна* .</span><span class="sxs-lookup"><span data-stu-id="173b5-271">It is *not* safe for external JavaScript code to modify elements that :::no-loc(Blazor)::: does not regard as empty.</span></span>
 * <span data-ttu-id="173b5-272">При использовании этого подхода необходимо учитывать то, как :::no-loc(Blazor)::: удерживает или уничтожает элементы DOM.</span><span class="sxs-lookup"><span data-stu-id="173b5-272">When using this approach, bear in mind the rules about how :::no-loc(Blazor)::: retains or destroys DOM elements.</span></span> <span data-ttu-id="173b5-273">В предыдущем примере компонент безопасно обрабатывает события нажатия кнопок и обновляет существующий экземпляр карты, так как по умолчанию элементы DOM по возможности сохраняются без изменений.</span><span class="sxs-lookup"><span data-stu-id="173b5-273">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="173b5-274">При отрисовке списка элементов карты из цикла `@foreach` необходимо использовать `@key`, чтобы гарантировать сохранность экземпляров компонента.</span><span class="sxs-lookup"><span data-stu-id="173b5-274">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="173b5-275">В противном случае изменения в данных списка могут приводить к нежелательному сохранению состояния предыдущих экземпляров компонента.</span><span class="sxs-lookup"><span data-stu-id="173b5-275">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="173b5-276">Дополнительные сведения см. в разделе об [использовании @key для сохранения элементов и компонентов](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="173b5-276">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="173b5-277">Кроме того, в предыдущем примере показано, как можно инкапсулировать логику и зависимости JavaScript в модуле ES6 и динамически загружать его с помощью идентификатора `import`.</span><span class="sxs-lookup"><span data-stu-id="173b5-277">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="173b5-278">Дополнительные сведения см.в статье [Изоляция JavaScript и ссылки на объекты](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="173b5-278">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="size-limits-on-js-interop-calls"></a><span data-ttu-id="173b5-279">Ограничения размера для вызовов взаимодействия с JS</span><span class="sxs-lookup"><span data-stu-id="173b5-279">Size limits on JS interop calls</span></span>

<span data-ttu-id="173b5-280">При использовании :::no-loc(Blazor WebAssembly)::: платформа не накладывает ограничений на размер входных и выходных данных в вызовах взаимодействия с JS.</span><span class="sxs-lookup"><span data-stu-id="173b5-280">In :::no-loc(Blazor WebAssembly):::, the framework doesn't impose limits on the size of inputs and outputs of JS interop calls.</span></span>

<span data-ttu-id="173b5-281">При использовании :::no-loc(Blazor Server)::: результат вызова взаимодействия с JS ограничен максимальным размером полезных данных, который настраивается в :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>) или имеет значение по умолчанию 32 КБ.</span><span class="sxs-lookup"><span data-stu-id="173b5-281">In :::no-loc(Blazor Server):::, the result of a JS interop call is limited by the maximum payload size enforced by :::no-loc(SignalR)::: (<xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>), which defaults to 32 KB.</span></span> <span data-ttu-id="173b5-282">Если приложение попытается ответить на вызов взаимодействия с JS, в котором размер полезных данных превышает значение <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>, возникает ошибка.</span><span class="sxs-lookup"><span data-stu-id="173b5-282">Applications that attempt to respond to a JS interop call with a payload larger than <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize> throw an error.</span></span> <span data-ttu-id="173b5-283">Можно увеличить предельный размер, изменив значение <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span><span class="sxs-lookup"><span data-stu-id="173b5-283">A larger limit can be configured by modifying <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubOptions.MaximumReceiveMessageSize>.</span></span> <span data-ttu-id="173b5-284">В следующем примере для размера получаемого сообщения устанавливается максимальный размер 64 КБ (64×1024×1024):</span><span class="sxs-lookup"><span data-stu-id="173b5-284">The following example sets the maximum receive message size to 64 KB (64 \* 1024 \* 1024):</span></span>

```csharp
services.AddServerSide:::no-loc(Blazor):::()
   .AddHubOptions(options => options.MaximumReceiveMessageSize = 64 * 1024 * 1024);
```

<span data-ttu-id="173b5-285">Увеличение предельного размера :::no-loc(SignalR)::: потребует больше серверных ресурсов для обработки, а также повысит уязвимость сервера к злонамеренным действиям пользователей.</span><span class="sxs-lookup"><span data-stu-id="173b5-285">Increasing the :::no-loc(SignalR)::: limit comes at the cost of requiring the use of more server resources, and it exposes the server to increased risks from a malicious user.</span></span> <span data-ttu-id="173b5-286">Кроме того, считывание большого объема содержимого в память в виде строк или массивов байтов может привести к неудачным выделениям памяти, которые плохо обрабатываются сборщиком мусора, что дополнительно снизит производительность.</span><span class="sxs-lookup"><span data-stu-id="173b5-286">Additionally, reading a large amount of content in to memory as strings or byte arrays can also result in allocations that work poorly with the garbage collector, resulting in additional performance penalties.</span></span> <span data-ttu-id="173b5-287">Чтобы считывать полезные данные большого размера, можно отправлять такое содержимое меньшими фрагментами и обрабатывать их как <xref:System.IO.Stream>.</span><span class="sxs-lookup"><span data-stu-id="173b5-287">One option for reading large payloads is to consider sending the content in smaller chunks and processing the payload as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="173b5-288">Это удобно для чтения полезных данных большого объема в формате JSON или необработанных байтов из JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-288">This can be used when reading large JSON payloads or if data is available in JavaScript as raw bytes.</span></span> <span data-ttu-id="173b5-289">Вы можете изучить отправку больших двоичных данных в :::no-loc(Blazor Server)::: с помощью методов, эквивалентных работе компонента `InputFile`, в [примере приложения с отправкой двоичных данных](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span><span class="sxs-lookup"><span data-stu-id="173b5-289">For an example that demonstrates sending large binary payloads in :::no-loc(Blazor Server)::: that uses techniques similar to the `InputFile` component, see the [Binary Submit sample app](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/BinarySubmit).</span></span>

<span data-ttu-id="173b5-290">При разработке кода, который передает данные большого объема между JavaScript и :::no-loc(Blazor):::, учитывайте следующие рекомендации:</span><span class="sxs-lookup"><span data-stu-id="173b5-290">Consider the following guidance when developing code that transfers a large amount of data between JavaScript and :::no-loc(Blazor)::::</span></span>

* <span data-ttu-id="173b5-291">Разделите данные на небольшие части и отправляйте сегменты данных последовательно, пока все данные не будут получены сервером.</span><span class="sxs-lookup"><span data-stu-id="173b5-291">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="173b5-292">Не выделяйте большие объекты в коде C# и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="173b5-292">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="173b5-293">Не блокируйте основной поток пользовательского интерфейса на длительные периоды при отправке или получении данных.</span><span class="sxs-lookup"><span data-stu-id="173b5-293">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="173b5-294">Освободите занятую память при завершении или отмене процесса.</span><span class="sxs-lookup"><span data-stu-id="173b5-294">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="173b5-295">Применяйте следующие дополнительные требования в целях безопасности:</span><span class="sxs-lookup"><span data-stu-id="173b5-295">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="173b5-296">Объявите максимальный размер файла или данных, который может быть передан.</span><span class="sxs-lookup"><span data-stu-id="173b5-296">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="173b5-297">Объявите минимальную скорость передачи от клиента к серверу.</span><span class="sxs-lookup"><span data-stu-id="173b5-297">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="173b5-298">После получения данных сервером данные могут быть:</span><span class="sxs-lookup"><span data-stu-id="173b5-298">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="173b5-299">Временно сохранены в буфере памяти до тех пор, пока не будут собраны все сегменты.</span><span class="sxs-lookup"><span data-stu-id="173b5-299">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="173b5-300">Использованы немедленно.</span><span class="sxs-lookup"><span data-stu-id="173b5-300">Consumed immediately.</span></span> <span data-ttu-id="173b5-301">Например, данные могут храниться сразу в базе данных или записываться на диск по мере получения каждого сегмента.</span><span class="sxs-lookup"><span data-stu-id="173b5-301">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="173b5-302">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="173b5-302">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="173b5-303">Пример InteropComponent.razor (репозиторий GitHub dotnet/AspNetCore, ветвь выпуска 3.1)</span><span class="sxs-lookup"><span data-stu-id="173b5-303">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
