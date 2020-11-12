---
title: 'Обработка событий :::no-loc(Blazor)::: в ASP.NET Core'
author: guardrex
description: Сведения о функциях обработки событий :::no-loc(Blazor):::, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: blazor/components/event-handling
ms.openlocfilehash: 56306d305a61d549380e847ab2754fa41130e14e
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507815"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="8b40f-103">Обработка событий :::no-loc(Blazor)::: в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b40f-103">ASP.NET Core :::no-loc(Blazor)::: event handling</span></span>

<span data-ttu-id="8b40f-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="8b40f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="8b40f-105">Компоненты :::no-loc(Razor)::: предоставляют функции обработки событий.</span><span class="sxs-lookup"><span data-stu-id="8b40f-105">:::no-loc(Razor)::: components provide event handling features.</span></span> <span data-ttu-id="8b40f-106">Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент :::no-loc(Razor)::: рассматривает значение атрибута как обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="8b40f-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a :::no-loc(Razor)::: component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="8b40f-107">Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:</span><span class="sxs-lookup"><span data-stu-id="8b40f-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="8b40f-108">Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:</span><span class="sxs-lookup"><span data-stu-id="8b40f-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="8b40f-109">Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="8b40f-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="8b40f-110">Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="8b40f-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="8b40f-111">Исключения регистрируются при их возникновении.</span><span class="sxs-lookup"><span data-stu-id="8b40f-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="8b40f-112">В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:</span><span class="sxs-lookup"><span data-stu-id="8b40f-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        await ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="8b40f-113">Типы аргументов событий</span><span class="sxs-lookup"><span data-stu-id="8b40f-113">Event argument types</span></span>

<span data-ttu-id="8b40f-114">Для некоторых событий разрешены типы аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="8b40f-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="8b40f-115">Указание параметра события в определении метода события является необязательным и требуется только в том случае, если тип события используется в методе.</span><span class="sxs-lookup"><span data-stu-id="8b40f-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="8b40f-116">В следующем примере аргумент события `MouseEventArgs` используется в методе `ShowMessage` для задания текста сообщения:</span><span class="sxs-lookup"><span data-stu-id="8b40f-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="8b40f-117">Поддерживаемые параметры <xref:System.EventArgs> приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="8b40f-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="8b40f-118">Событие</span><span class="sxs-lookup"><span data-stu-id="8b40f-118">Event</span></span>            | <span data-ttu-id="8b40f-119">Class</span><span class="sxs-lookup"><span data-stu-id="8b40f-119">Class</span></span>  | <span data-ttu-id="8b40f-120">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="8b40f-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="8b40f-121">Буфер обмена</span><span class="sxs-lookup"><span data-stu-id="8b40f-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="8b40f-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="8b40f-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="8b40f-123">Перетаскивание</span><span class="sxs-lookup"><span data-stu-id="8b40f-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="8b40f-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="8b40f-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="8b40f-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="8b40f-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="8b40f-126">Реализуйте перетаскивание в приложениях :::no-loc(Blazor)::: с помощью [взаимодействия JS](xref:blazor/call-javascript-from-dotnet) с [API перетаскивания для HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="8b40f-126">Implement drag and drop in :::no-loc(Blazor)::: apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="8b40f-127">Error</span><span class="sxs-lookup"><span data-stu-id="8b40f-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="8b40f-128">Событие</span><span class="sxs-lookup"><span data-stu-id="8b40f-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="8b40f-129">*Общие сведения*</span><span class="sxs-lookup"><span data-stu-id="8b40f-129">*General*</span></span><br><span data-ttu-id="8b40f-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="8b40f-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="8b40f-131">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="8b40f-131">*Clipboard*</span></span><br><span data-ttu-id="8b40f-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="8b40f-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="8b40f-133">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="8b40f-133">*Input*</span></span><br><span data-ttu-id="8b40f-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="8b40f-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="8b40f-135">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="8b40f-135">*Media*</span></span><br><span data-ttu-id="8b40f-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="8b40f-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="8b40f-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="8b40f-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="8b40f-138">Фокус</span><span class="sxs-lookup"><span data-stu-id="8b40f-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="8b40f-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="8b40f-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="8b40f-140">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="8b40f-141">Входные данные</span><span class="sxs-lookup"><span data-stu-id="8b40f-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="8b40f-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="8b40f-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="8b40f-143">Клавиатура</span><span class="sxs-lookup"><span data-stu-id="8b40f-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="8b40f-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="8b40f-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="8b40f-145">Мышь</span><span class="sxs-lookup"><span data-stu-id="8b40f-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="8b40f-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="8b40f-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="8b40f-147">Указатель мыши</span><span class="sxs-lookup"><span data-stu-id="8b40f-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="8b40f-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="8b40f-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="8b40f-149">Колесико мыши</span><span class="sxs-lookup"><span data-stu-id="8b40f-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="8b40f-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="8b40f-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="8b40f-151">Ход выполнения</span><span class="sxs-lookup"><span data-stu-id="8b40f-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="8b40f-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="8b40f-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="8b40f-153">Сенсорные технологии</span><span class="sxs-lookup"><span data-stu-id="8b40f-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="8b40f-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="8b40f-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="8b40f-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="8b40f-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="8b40f-156">Событие</span><span class="sxs-lookup"><span data-stu-id="8b40f-156">Event</span></span>            | <span data-ttu-id="8b40f-157">Class</span><span class="sxs-lookup"><span data-stu-id="8b40f-157">Class</span></span> | <span data-ttu-id="8b40f-158">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="8b40f-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="8b40f-159">Буфер обмена</span><span class="sxs-lookup"><span data-stu-id="8b40f-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="8b40f-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="8b40f-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="8b40f-161">Перетаскивание</span><span class="sxs-lookup"><span data-stu-id="8b40f-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="8b40f-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="8b40f-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="8b40f-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="8b40f-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="8b40f-164">Реализуйте перетаскивание в приложениях :::no-loc(Blazor)::: с помощью [взаимодействия JS](xref:blazor/call-javascript-from-dotnet) с [API перетаскивания для HTML](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span><span class="sxs-lookup"><span data-stu-id="8b40f-164">Implement drag and drop in :::no-loc(Blazor)::: apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="8b40f-165">Error</span><span class="sxs-lookup"><span data-stu-id="8b40f-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="8b40f-166">Событие</span><span class="sxs-lookup"><span data-stu-id="8b40f-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="8b40f-167">*Общие сведения*</span><span class="sxs-lookup"><span data-stu-id="8b40f-167">*General*</span></span><br><span data-ttu-id="8b40f-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="8b40f-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="8b40f-169">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="8b40f-169">*Clipboard*</span></span><br><span data-ttu-id="8b40f-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="8b40f-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="8b40f-171">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="8b40f-171">*Input*</span></span><br><span data-ttu-id="8b40f-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="8b40f-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="8b40f-173">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="8b40f-173">*Media*</span></span><br><span data-ttu-id="8b40f-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="8b40f-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="8b40f-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="8b40f-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="8b40f-176">Фокус</span><span class="sxs-lookup"><span data-stu-id="8b40f-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="8b40f-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="8b40f-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="8b40f-178">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="8b40f-179">Входные данные</span><span class="sxs-lookup"><span data-stu-id="8b40f-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="8b40f-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="8b40f-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="8b40f-181">Клавиатура</span><span class="sxs-lookup"><span data-stu-id="8b40f-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="8b40f-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="8b40f-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="8b40f-183">Мышь</span><span class="sxs-lookup"><span data-stu-id="8b40f-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="8b40f-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="8b40f-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="8b40f-185">Указатель мыши</span><span class="sxs-lookup"><span data-stu-id="8b40f-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="8b40f-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="8b40f-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="8b40f-187">Колесико мыши</span><span class="sxs-lookup"><span data-stu-id="8b40f-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="8b40f-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="8b40f-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="8b40f-189">Ход выполнения</span><span class="sxs-lookup"><span data-stu-id="8b40f-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="8b40f-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="8b40f-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="8b40f-191">Сенсорные технологии</span><span class="sxs-lookup"><span data-stu-id="8b40f-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="8b40f-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="8b40f-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="8b40f-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="8b40f-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="8b40f-194">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="8b40f-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="8b40f-195">[Классы `EventArgs` в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore `master`)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="8b40f-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="8b40f-196">Ветвь `master` представляет разрабатываемый API для *следующего* выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8b40f-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="8b40f-197">В текущем выпуске выберите соответствующую ветвь репозитория GitHub (например, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="8b40f-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="8b40f-198">[Веб-документы MDN: GlobalEventHandlers.](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) Содержит сведения о том, какие элементы HTML поддерживают каждое из событий DOM.</span><span class="sxs-lookup"><span data-stu-id="8b40f-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="8b40f-199">Лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="8b40f-199">Lambda expressions</span></span>

<span data-ttu-id="8b40f-200">Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="8b40f-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="8b40f-201">Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов.</span><span class="sxs-lookup"><span data-stu-id="8b40f-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="8b40f-202">В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:</span><span class="sxs-lookup"><span data-stu-id="8b40f-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="8b40f-203">**Не** используйте переменную цикла непосредственно в лямбда-выражении, например `i` в предыдущем примере цикла `for`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="8b40f-204">В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение будет одинаковым во всех лямбда-выражениях.</span><span class="sxs-lookup"><span data-stu-id="8b40f-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="8b40f-205">Всегда записывайте значение переменной в локальную переменную, а затем используйте ее.</span><span class="sxs-lookup"><span data-stu-id="8b40f-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="8b40f-206">В предыдущем примере переменная цикла `i` назначается `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="8b40f-207">EventCallback</span><span class="sxs-lookup"><span data-stu-id="8b40f-207">EventCallback</span></span>

<span data-ttu-id="8b40f-208">Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента,</span><span class="sxs-lookup"><span data-stu-id="8b40f-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="8b40f-209">например, когда в дочернем элементе возникает событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="8b40f-210">Чтобы обеспечить доступ к событиям в компонентах, используйте <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="8b40f-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="8b40f-211">Родительский компонент может назначить метод обратного вызова <xref:Microsoft.AspNetCore.Components.EventCallback> дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="8b40f-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="8b40f-212">В `ChildComponent` в примере приложения (`Components/ChildComponent.razor`) показано, как обработчик `onclick` кнопки настроен на получение делегата <xref:Microsoft.AspNetCore.Components.EventCallback> из `ParentComponent` образца.</span><span class="sxs-lookup"><span data-stu-id="8b40f-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="8b40f-213"><xref:Microsoft.AspNetCore.Components.EventCallback> вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:</span><span class="sxs-lookup"><span data-stu-id="8b40f-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/5.x/:::no-loc(Blazor):::WebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="8b40f-214">`ParentComponent` задает для <xref:Microsoft.AspNetCore.Components.EventCallback%601> дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="8b40f-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="8b40f-215">`Pages/ParentComponent.razor`:</span></span>

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
        messageText = $"Blaze a new trail with :::no-loc(Blazor):::! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="8b40f-216">При выборе кнопки в `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="8b40f-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="8b40f-217">Вызывается метод `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="8b40f-218">`messageText` обновляется и отображается в `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="8b40f-219">Вызов [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="8b40f-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="8b40f-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе.</span><span class="sxs-lookup"><span data-stu-id="8b40f-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="8b40f-221"><xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> разрешают выполнять асинхронные делегаты.</span><span class="sxs-lookup"><span data-stu-id="8b40f-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="8b40f-222">Структура <xref:Microsoft.AspNetCore.Components.EventCallback> слабо типизирована и позволяет передавать любой аргумент типа в `InvokeAsync(Object)`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="8b40f-223">Структура <xref:Microsoft.AspNetCore.Components.EventCallback%601> сильно типизирована и требует передачи аргумента `T` в `InvokeAsync(T)`, который можно назначить `TValue`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="8b40f-224">Вызов <xref:Microsoft.AspNetCore.Components.EventCallback> или <xref:Microsoft.AspNetCore.Components.EventCallback%601> с <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> и ожидание <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="8b40f-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="8b40f-225">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> для обработки событий и параметров компонента привязки.</span><span class="sxs-lookup"><span data-stu-id="8b40f-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="8b40f-226">Предпочтительнее использовать строго типизированный <xref:Microsoft.AspNetCore.Components.EventCallback%601> вместо <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="8b40f-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="8b40f-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> обеспечивает более эффективное реагирование на ошибки для пользователей компонента.</span><span class="sxs-lookup"><span data-stu-id="8b40f-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="8b40f-228">Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным.</span><span class="sxs-lookup"><span data-stu-id="8b40f-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="8b40f-229">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback>, если в обратный вызов не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="8b40f-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="8b40f-230">Запрет действий по умолчанию</span><span class="sxs-lookup"><span data-stu-id="8b40f-230">Prevent default actions</span></span>

<span data-ttu-id="8b40f-231">Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="8b40f-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="8b40f-232">Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа.</span><span class="sxs-lookup"><span data-stu-id="8b40f-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="8b40f-233">В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="8b40f-234">Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:</span><span class="sxs-lookup"><span data-stu-id="8b40f-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="8b40f-235">Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="8b40f-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="8b40f-236">Значение атрибута также может быть выражением.</span><span class="sxs-lookup"><span data-stu-id="8b40f-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="8b40f-237">В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:</span><span class="sxs-lookup"><span data-stu-id="8b40f-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="8b40f-238">Остановка распространения событий</span><span class="sxs-lookup"><span data-stu-id="8b40f-238">Stop event propagation</span></span>

<span data-ttu-id="8b40f-239">Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.</span><span class="sxs-lookup"><span data-stu-id="8b40f-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="8b40f-240">В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:</span><span class="sxs-lookup"><span data-stu-id="8b40f-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

## <a name="focus-an-element"></a><span data-ttu-id="8b40f-241">Установка фокуса на элемент</span><span class="sxs-lookup"><span data-stu-id="8b40f-241">Focus an element</span></span>

<span data-ttu-id="8b40f-242">Вызовите `FocusAsync` на [ссылке на элемент](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), чтобы установить фокус на элементе в коде:</span><span class="sxs-lookup"><span data-stu-id="8b40f-242">Call `FocusAsync` on an [element reference](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements) to focus an element in code:</span></span>

```razor
<input @ref="exampleInput" />

<button @onclick="ChangeFocus">Focus the Input Element</button>

@code {
    private ElementReference exampleInput;
    
    private async Task ChangeFocus()
    {
        await exampleInput.FocusAsync();
    }
}
```

::: moniker-end
