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
uid: blazor/event-handling
ms.openlocfilehash: 2d806413eb66d4c1301df4a0db950e8b919c09a4
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84419753"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="7d5e4-103">Обработка событий Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d5e4-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="7d5e4-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="7d5e4-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7d5e4-105">Компоненты Razor предоставляют функции обработки событий.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-105">Razor components provide event handling features.</span></span> <span data-ttu-id="7d5e4-106">Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент Razor рассматривает значение атрибута как обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="7d5e4-107">Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="7d5e4-108">Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="7d5e4-109">Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="7d5e4-110">Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="7d5e4-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="7d5e4-111">Исключения регистрируются при их возникновении.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="7d5e4-112">В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="7d5e4-113">Типы аргументов событий</span><span class="sxs-lookup"><span data-stu-id="7d5e4-113">Event argument types</span></span>

<span data-ttu-id="7d5e4-114">Для некоторых событий разрешены типы аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="7d5e4-115">Указание типа события в вызове метода требуется только в том случае, если тип события используется в методе.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="7d5e4-116">Поддерживаемые параметры <xref:System.EventArgs> приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="7d5e4-117">событие</span><span class="sxs-lookup"><span data-stu-id="7d5e4-117">Event</span></span>            | <span data-ttu-id="7d5e4-118">Класс</span><span class="sxs-lookup"><span data-stu-id="7d5e4-118">Class</span></span>                | <span data-ttu-id="7d5e4-119">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="7d5e4-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="7d5e4-120">буфер обмена</span><span class="sxs-lookup"><span data-stu-id="7d5e4-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="7d5e4-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="7d5e4-122">Перетаскивание</span><span class="sxs-lookup"><span data-stu-id="7d5e4-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="7d5e4-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="7d5e4-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="7d5e4-125">Ошибка</span><span class="sxs-lookup"><span data-stu-id="7d5e4-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="7d5e4-126">событие</span><span class="sxs-lookup"><span data-stu-id="7d5e4-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="7d5e4-127">*Общие сведения*</span><span class="sxs-lookup"><span data-stu-id="7d5e4-127">*General*</span></span><br><span data-ttu-id="7d5e4-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="7d5e4-129">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="7d5e4-129">*Clipboard*</span></span><br><span data-ttu-id="7d5e4-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="7d5e4-131">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="7d5e4-131">*Input*</span></span><br><span data-ttu-id="7d5e4-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="7d5e4-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="7d5e4-133">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="7d5e4-133">*Media*</span></span><br><span data-ttu-id="7d5e4-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="7d5e4-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="7d5e4-136">Фокус</span><span class="sxs-lookup"><span data-stu-id="7d5e4-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="7d5e4-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="7d5e4-138">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="7d5e4-139">Входные данные</span><span class="sxs-lookup"><span data-stu-id="7d5e4-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="7d5e4-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="7d5e4-141">Клавиатура</span><span class="sxs-lookup"><span data-stu-id="7d5e4-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="7d5e4-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="7d5e4-143">Мышь</span><span class="sxs-lookup"><span data-stu-id="7d5e4-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="7d5e4-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="7d5e4-145">Указатель мыши</span><span class="sxs-lookup"><span data-stu-id="7d5e4-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="7d5e4-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="7d5e4-147">Колесико мыши</span><span class="sxs-lookup"><span data-stu-id="7d5e4-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="7d5e4-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="7d5e4-149">Ход выполнения</span><span class="sxs-lookup"><span data-stu-id="7d5e4-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="7d5e4-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="7d5e4-151">Сенсорные технологии</span><span class="sxs-lookup"><span data-stu-id="7d5e4-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="7d5e4-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="7d5e4-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="7d5e4-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="7d5e4-154">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="7d5e4-155">[Классы EventArgs в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="7d5e4-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="7d5e4-156">[Веб-документы MDN: GlobalEventHandlers.](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) Содержит сведения о том, какие элементы HTML поддерживают каждое из событий DOM.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="7d5e4-157">Лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="7d5e4-157">Lambda expressions</span></span>

<span data-ttu-id="7d5e4-158">Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="7d5e4-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="7d5e4-159">Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="7d5e4-160">В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="7d5e4-161">**Не** используйте переменную цикла непосредственно в лямбда-выражении, например `i` в предыдущем примере цикла `for`, или ссылочную переменную в цикле `foreach`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="7d5e4-162">В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение будет одинаковым во всех лямбда-выражениях.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="7d5e4-163">Всегда записывайте значение переменной в локальную переменную, а затем используйте ее.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="7d5e4-164">В предыдущем примере переменная цикла `i` назначается `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="7d5e4-165">EventCallback</span><span class="sxs-lookup"><span data-stu-id="7d5e4-165">EventCallback</span></span>

<span data-ttu-id="7d5e4-166">Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента,</span><span class="sxs-lookup"><span data-stu-id="7d5e4-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="7d5e4-167">например, когда в дочернем элементе возникает событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="7d5e4-168">Чтобы обеспечить доступ к событиям в компонентах, используйте <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="7d5e4-169">Родительский компонент может назначить метод обратного вызова <xref:Microsoft.AspNetCore.Components.EventCallback> дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="7d5e4-170">В `ChildComponent` в примере приложения (*Components/ChildComponent.razor*) показано, как обработчик `onclick` кнопки настроен на получение делегата <xref:Microsoft.AspNetCore.Components.EventCallback> из `ParentComponent` образца.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="7d5e4-171"><xref:Microsoft.AspNetCore.Components.EventCallback> вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="7d5e4-172">`ParentComponent` задает для <xref:Microsoft.AspNetCore.Components.EventCallback%601> дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="7d5e4-173">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="7d5e4-174">При выборе кнопки в `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="7d5e4-175">Вызывается метод `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="7d5e4-176">`messageText` обновляется и отображается в `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="7d5e4-177">Вызов [StateHasChanged](xref:blazor/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="7d5e4-177">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="7d5e4-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="7d5e4-179"><xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> разрешают выполнять асинхронные делегаты.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="7d5e4-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> является строго типизированным и требует определенного типа аргумента.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="7d5e4-181"><xref:Microsoft.AspNetCore.Components.EventCallback> слабо типизирован и допускает любой тип аргумента.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="7d5e4-182">Вызов <xref:Microsoft.AspNetCore.Components.EventCallback> или <xref:Microsoft.AspNetCore.Components.EventCallback%601> с <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> и ожидание <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="7d5e4-183">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> для обработки событий и параметров компонента привязки.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="7d5e4-184">Предпочтительнее использовать строго типизированный <xref:Microsoft.AspNetCore.Components.EventCallback%601> вместо <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="7d5e4-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> обеспечивает более эффективное реагирование на ошибки для пользователей компонента.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="7d5e4-186">Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="7d5e4-187">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback>, если в обратный вызов не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="7d5e4-188">Запрет действий по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7d5e4-188">Prevent default actions</span></span>

<span data-ttu-id="7d5e4-189">Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="7d5e4-190">Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="7d5e4-191">В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="7d5e4-192">Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="7d5e4-193">Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="7d5e4-194">Значение атрибута также может быть выражением.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="7d5e4-195">В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="7d5e4-196">Для запрета действий по умолчанию не требуется обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="7d5e4-197">Обработчик событий и сценарий запрета действий по умолчанию можно использовать независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="7d5e4-198">Остановка распространения событий</span><span class="sxs-lookup"><span data-stu-id="7d5e4-198">Stop event propagation</span></span>

<span data-ttu-id="7d5e4-199">Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.</span><span class="sxs-lookup"><span data-stu-id="7d5e4-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="7d5e4-200">В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:</span><span class="sxs-lookup"><span data-stu-id="7d5e4-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
