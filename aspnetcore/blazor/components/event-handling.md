---
title: Обработка событий Blazor в ASP.NET Core
author: guardrex
description: Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 4ac7b82d734f078cf50901d02e7d0c4eb8bb45bb
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242424"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="a5179-103">Обработка событий Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5179-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="a5179-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="a5179-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a5179-105">Компоненты Razor предоставляют функции обработки событий.</span><span class="sxs-lookup"><span data-stu-id="a5179-105">Razor components provide event handling features.</span></span> <span data-ttu-id="a5179-106">Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент Razor рассматривает значение атрибута как обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="a5179-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="a5179-107">Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:</span><span class="sxs-lookup"><span data-stu-id="a5179-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="a5179-108">Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:</span><span class="sxs-lookup"><span data-stu-id="a5179-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="a5179-109">Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="a5179-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="a5179-110">Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="a5179-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="a5179-111">Исключения регистрируются при их возникновении.</span><span class="sxs-lookup"><span data-stu-id="a5179-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="a5179-112">В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:</span><span class="sxs-lookup"><span data-stu-id="a5179-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="a5179-113">Типы аргументов событий</span><span class="sxs-lookup"><span data-stu-id="a5179-113">Event argument types</span></span>

<span data-ttu-id="a5179-114">Для некоторых событий разрешены типы аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="a5179-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="a5179-115">Указание параметра события в определении метода события является необязательным и требуется только в том случае, если тип события используется в методе.</span><span class="sxs-lookup"><span data-stu-id="a5179-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="a5179-116">В следующем примере аргумент события `MouseEventArgs` используется в методе `ShowMessage` для задания текста сообщения:</span><span class="sxs-lookup"><span data-stu-id="a5179-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="a5179-117">Поддерживаемые параметры <xref:System.EventArgs> приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="a5179-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="a5179-118">Событие</span><span class="sxs-lookup"><span data-stu-id="a5179-118">Event</span></span>            | <span data-ttu-id="a5179-119">Class</span><span class="sxs-lookup"><span data-stu-id="a5179-119">Class</span></span>                | <span data-ttu-id="a5179-120">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="a5179-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="a5179-121">Буфер обмена</span><span class="sxs-lookup"><span data-stu-id="a5179-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="a5179-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="a5179-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="a5179-123">Перетаскивание</span><span class="sxs-lookup"><span data-stu-id="a5179-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="a5179-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="a5179-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="a5179-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="a5179-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="a5179-126">Error</span><span class="sxs-lookup"><span data-stu-id="a5179-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="a5179-127">Событие</span><span class="sxs-lookup"><span data-stu-id="a5179-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="a5179-128">*Общие сведения*</span><span class="sxs-lookup"><span data-stu-id="a5179-128">*General*</span></span><br><span data-ttu-id="a5179-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="a5179-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="a5179-130">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="a5179-130">*Clipboard*</span></span><br><span data-ttu-id="a5179-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="a5179-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="a5179-132">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="a5179-132">*Input*</span></span><br><span data-ttu-id="a5179-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="a5179-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="a5179-134">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="a5179-134">*Media*</span></span><br><span data-ttu-id="a5179-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="a5179-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="a5179-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="a5179-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="a5179-137">Focus</span><span class="sxs-lookup"><span data-stu-id="a5179-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="a5179-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="a5179-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="a5179-139">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="a5179-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="a5179-140">Входные данные</span><span class="sxs-lookup"><span data-stu-id="a5179-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="a5179-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="a5179-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="a5179-142">Клавиатура</span><span class="sxs-lookup"><span data-stu-id="a5179-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="a5179-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="a5179-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="a5179-144">Мышь</span><span class="sxs-lookup"><span data-stu-id="a5179-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="a5179-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="a5179-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="a5179-146">Указатель мыши</span><span class="sxs-lookup"><span data-stu-id="a5179-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="a5179-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="a5179-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="a5179-148">Колесико мыши</span><span class="sxs-lookup"><span data-stu-id="a5179-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="a5179-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="a5179-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="a5179-150">Ход выполнения</span><span class="sxs-lookup"><span data-stu-id="a5179-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="a5179-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="a5179-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="a5179-152">Сенсорные технологии</span><span class="sxs-lookup"><span data-stu-id="a5179-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="a5179-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="a5179-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="a5179-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="a5179-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="a5179-155">Для получения дополнительных сведений см. следующие ресурсы:</span><span class="sxs-lookup"><span data-stu-id="a5179-155">For more information, see the following resources:</span></span>

* <span data-ttu-id="a5179-156">[Классы `EventArgs` в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="a5179-156">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="a5179-157">[Веб-документы MDN: GlobalEventHandlers.](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) Содержит сведения о том, какие элементы HTML поддерживают каждое из событий DOM.</span><span class="sxs-lookup"><span data-stu-id="a5179-157">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="a5179-158">Лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="a5179-158">Lambda expressions</span></span>

<span data-ttu-id="a5179-159">Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="a5179-159">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="a5179-160">Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов.</span><span class="sxs-lookup"><span data-stu-id="a5179-160">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="a5179-161">В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:</span><span class="sxs-lookup"><span data-stu-id="a5179-161">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="a5179-162">**Не** используйте переменную цикла непосредственно в лямбда-выражении, например `i` в предыдущем примере цикла `for`, или ссылочную переменную в цикле `foreach`.</span><span class="sxs-lookup"><span data-stu-id="a5179-162">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="a5179-163">В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение будет одинаковым во всех лямбда-выражениях.</span><span class="sxs-lookup"><span data-stu-id="a5179-163">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="a5179-164">Всегда записывайте значение переменной в локальную переменную, а затем используйте ее.</span><span class="sxs-lookup"><span data-stu-id="a5179-164">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="a5179-165">В предыдущем примере переменная цикла `i` назначается `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="a5179-165">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="a5179-166">EventCallback</span><span class="sxs-lookup"><span data-stu-id="a5179-166">EventCallback</span></span>

<span data-ttu-id="a5179-167">Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента,</span><span class="sxs-lookup"><span data-stu-id="a5179-167">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="a5179-168">например, когда в дочернем элементе возникает событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="a5179-168">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="a5179-169">Чтобы обеспечить доступ к событиям в компонентах, используйте <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="a5179-169">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="a5179-170">Родительский компонент может назначить метод обратного вызова <xref:Microsoft.AspNetCore.Components.EventCallback> дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="a5179-170">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="a5179-171">В `ChildComponent` в примере приложения (`Components/ChildComponent.razor`) показано, как обработчик `onclick` кнопки настроен на получение делегата <xref:Microsoft.AspNetCore.Components.EventCallback> из `ParentComponent` образца.</span><span class="sxs-lookup"><span data-stu-id="a5179-171">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="a5179-172"><xref:Microsoft.AspNetCore.Components.EventCallback> вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:</span><span class="sxs-lookup"><span data-stu-id="a5179-172">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="a5179-173">`ParentComponent` задает для <xref:Microsoft.AspNetCore.Components.EventCallback%601> дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="a5179-173">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="a5179-174">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="a5179-174">`Pages/ParentComponent.razor`:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="a5179-175">При выборе кнопки в `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="a5179-175">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="a5179-176">Вызывается метод `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="a5179-176">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="a5179-177">`messageText` обновляется и отображается в `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="a5179-177">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="a5179-178">Вызов [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="a5179-178">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="a5179-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе.</span><span class="sxs-lookup"><span data-stu-id="a5179-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="a5179-180"><xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> разрешают выполнять асинхронные делегаты.</span><span class="sxs-lookup"><span data-stu-id="a5179-180"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="a5179-181"><xref:Microsoft.AspNetCore.Components.EventCallback%601> является строго типизированным и требует определенного типа аргумента.</span><span class="sxs-lookup"><span data-stu-id="a5179-181"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="a5179-182"><xref:Microsoft.AspNetCore.Components.EventCallback> слабо типизирован и допускает любой тип аргумента.</span><span class="sxs-lookup"><span data-stu-id="a5179-182"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="a5179-183">Вызов <xref:Microsoft.AspNetCore.Components.EventCallback> или <xref:Microsoft.AspNetCore.Components.EventCallback%601> с <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> и ожидание <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="a5179-183">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="a5179-184">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> для обработки событий и параметров компонента привязки.</span><span class="sxs-lookup"><span data-stu-id="a5179-184">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="a5179-185">Предпочтительнее использовать строго типизированный <xref:Microsoft.AspNetCore.Components.EventCallback%601> вместо <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="a5179-185">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="a5179-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> обеспечивает более эффективное реагирование на ошибки для пользователей компонента.</span><span class="sxs-lookup"><span data-stu-id="a5179-186"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="a5179-187">Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным.</span><span class="sxs-lookup"><span data-stu-id="a5179-187">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="a5179-188">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback>, если в обратный вызов не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="a5179-188">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="a5179-189">Запрет действий по умолчанию</span><span class="sxs-lookup"><span data-stu-id="a5179-189">Prevent default actions</span></span>

<span data-ttu-id="a5179-190">Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="a5179-190">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="a5179-191">Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа.</span><span class="sxs-lookup"><span data-stu-id="a5179-191">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="a5179-192">В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="a5179-192">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="a5179-193">Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:</span><span class="sxs-lookup"><span data-stu-id="a5179-193">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="a5179-194">Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="a5179-194">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="a5179-195">Значение атрибута также может быть выражением.</span><span class="sxs-lookup"><span data-stu-id="a5179-195">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="a5179-196">В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:</span><span class="sxs-lookup"><span data-stu-id="a5179-196">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="a5179-197">Для запрета действий по умолчанию не требуется обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="a5179-197">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="a5179-198">Обработчик событий и сценарий запрета действий по умолчанию можно использовать независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="a5179-198">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="a5179-199">Остановка распространения событий</span><span class="sxs-lookup"><span data-stu-id="a5179-199">Stop event propagation</span></span>

<span data-ttu-id="a5179-200">Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.</span><span class="sxs-lookup"><span data-stu-id="a5179-200">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="a5179-201">В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:</span><span class="sxs-lookup"><span data-stu-id="a5179-201">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
