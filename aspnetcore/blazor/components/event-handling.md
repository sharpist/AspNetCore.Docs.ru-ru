---
title: Обработка событий Blazor в ASP.NET Core
author: guardrex
description: Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию.
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
uid: blazor/components/event-handling
ms.openlocfilehash: cb7009946f56b3c8f1e5eeabb09b81075749c720
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722453"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="91a08-103">Обработка событий Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91a08-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="91a08-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="91a08-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="91a08-105">Компоненты Razor предоставляют функции обработки событий.</span><span class="sxs-lookup"><span data-stu-id="91a08-105">Razor components provide event handling features.</span></span> <span data-ttu-id="91a08-106">Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент Razor рассматривает значение атрибута как обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="91a08-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="91a08-107">Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:</span><span class="sxs-lookup"><span data-stu-id="91a08-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="91a08-108">Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:</span><span class="sxs-lookup"><span data-stu-id="91a08-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="91a08-109">Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="91a08-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="91a08-110">Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="91a08-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="91a08-111">Исключения регистрируются при их возникновении.</span><span class="sxs-lookup"><span data-stu-id="91a08-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="91a08-112">В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:</span><span class="sxs-lookup"><span data-stu-id="91a08-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="91a08-113">Типы аргументов событий</span><span class="sxs-lookup"><span data-stu-id="91a08-113">Event argument types</span></span>

<span data-ttu-id="91a08-114">Для некоторых событий разрешены типы аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="91a08-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="91a08-115">Указание параметра события в определении метода события является необязательным и требуется только в том случае, если тип события используется в методе.</span><span class="sxs-lookup"><span data-stu-id="91a08-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="91a08-116">В следующем примере аргумент события `MouseEventArgs` используется в методе `ShowMessage` для задания текста сообщения:</span><span class="sxs-lookup"><span data-stu-id="91a08-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="91a08-117">Поддерживаемые параметры <xref:System.EventArgs> приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="91a08-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="91a08-118">событие</span><span class="sxs-lookup"><span data-stu-id="91a08-118">Event</span></span>            | <span data-ttu-id="91a08-119">Класс</span><span class="sxs-lookup"><span data-stu-id="91a08-119">Class</span></span>  | <span data-ttu-id="91a08-120">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="91a08-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="91a08-121">буфер обмена</span><span class="sxs-lookup"><span data-stu-id="91a08-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="91a08-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="91a08-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="91a08-123">Перетаскивание</span><span class="sxs-lookup"><span data-stu-id="91a08-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="91a08-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="91a08-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="91a08-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="91a08-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="91a08-126">Ошибка</span><span class="sxs-lookup"><span data-stu-id="91a08-126">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="91a08-127">событие</span><span class="sxs-lookup"><span data-stu-id="91a08-127">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="91a08-128">*Общие сведения*</span><span class="sxs-lookup"><span data-stu-id="91a08-128">*General*</span></span><br><span data-ttu-id="91a08-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="91a08-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="91a08-130">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="91a08-130">*Clipboard*</span></span><br><span data-ttu-id="91a08-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="91a08-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="91a08-132">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="91a08-132">*Input*</span></span><br><span data-ttu-id="91a08-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="91a08-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="91a08-134">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="91a08-134">*Media*</span></span><br><span data-ttu-id="91a08-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="91a08-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="91a08-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="91a08-136"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="91a08-137">Фокус</span><span class="sxs-lookup"><span data-stu-id="91a08-137">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="91a08-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="91a08-138">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="91a08-139">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="91a08-139">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="91a08-140">Входные данные</span><span class="sxs-lookup"><span data-stu-id="91a08-140">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="91a08-141">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="91a08-141">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="91a08-142">Клавиатура</span><span class="sxs-lookup"><span data-stu-id="91a08-142">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="91a08-143">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="91a08-143">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="91a08-144">Мышь</span><span class="sxs-lookup"><span data-stu-id="91a08-144">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="91a08-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="91a08-145">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="91a08-146">Указатель мыши</span><span class="sxs-lookup"><span data-stu-id="91a08-146">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="91a08-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="91a08-147">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="91a08-148">Колесико мыши</span><span class="sxs-lookup"><span data-stu-id="91a08-148">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="91a08-149">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="91a08-149">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="91a08-150">Ход выполнения</span><span class="sxs-lookup"><span data-stu-id="91a08-150">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="91a08-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="91a08-151">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="91a08-152">Сенсорные технологии</span><span class="sxs-lookup"><span data-stu-id="91a08-152">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="91a08-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="91a08-153">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="91a08-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="91a08-154"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="91a08-155">Событие</span><span class="sxs-lookup"><span data-stu-id="91a08-155">Event</span></span>            | <span data-ttu-id="91a08-156">Class</span><span class="sxs-lookup"><span data-stu-id="91a08-156">Class</span></span> | <span data-ttu-id="91a08-157">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="91a08-157">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="91a08-158">буфер обмена</span><span class="sxs-lookup"><span data-stu-id="91a08-158">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="91a08-159">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="91a08-159">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="91a08-160">Перетаскивание</span><span class="sxs-lookup"><span data-stu-id="91a08-160">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="91a08-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="91a08-161">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="91a08-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="91a08-162"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="91a08-163">Error</span><span class="sxs-lookup"><span data-stu-id="91a08-163">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="91a08-164">Событие</span><span class="sxs-lookup"><span data-stu-id="91a08-164">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="91a08-165">*Общие сведения*</span><span class="sxs-lookup"><span data-stu-id="91a08-165">*General*</span></span><br><span data-ttu-id="91a08-166">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="91a08-166">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="91a08-167">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="91a08-167">*Clipboard*</span></span><br><span data-ttu-id="91a08-168">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="91a08-168">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="91a08-169">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="91a08-169">*Input*</span></span><br><span data-ttu-id="91a08-170">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="91a08-170">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="91a08-171">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="91a08-171">*Media*</span></span><br><span data-ttu-id="91a08-172">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="91a08-172">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="91a08-173"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="91a08-173"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="91a08-174">Фокус</span><span class="sxs-lookup"><span data-stu-id="91a08-174">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="91a08-175">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="91a08-175">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="91a08-176">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="91a08-176">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="91a08-177">Входные данные</span><span class="sxs-lookup"><span data-stu-id="91a08-177">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="91a08-178">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="91a08-178">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="91a08-179">Клавиатура</span><span class="sxs-lookup"><span data-stu-id="91a08-179">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="91a08-180">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="91a08-180">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="91a08-181">Мышь</span><span class="sxs-lookup"><span data-stu-id="91a08-181">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="91a08-182">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="91a08-182">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="91a08-183">Указатель мыши</span><span class="sxs-lookup"><span data-stu-id="91a08-183">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="91a08-184">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="91a08-184">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="91a08-185">Колесико мыши</span><span class="sxs-lookup"><span data-stu-id="91a08-185">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="91a08-186">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="91a08-186">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="91a08-187">Ход выполнения</span><span class="sxs-lookup"><span data-stu-id="91a08-187">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="91a08-188">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="91a08-188">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="91a08-189">Сенсорные технологии</span><span class="sxs-lookup"><span data-stu-id="91a08-189">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="91a08-190">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="91a08-190">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="91a08-191"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="91a08-191"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="91a08-192">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="91a08-192">For more information, see the following resources:</span></span>

* <span data-ttu-id="91a08-193">[Классы `EventArgs` в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore `master`)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="91a08-193">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="91a08-194">Ветвь `master` представляет разрабатываемый API для *следующего* выпуска ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91a08-194">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="91a08-195">В текущем выпуске выберите соответствующую ветвь репозитория GitHub (например, `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="91a08-195">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="91a08-196">[Веб-документы MDN: GlobalEventHandlers.](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) Содержит сведения о том, какие элементы HTML поддерживают каждое из событий DOM.</span><span class="sxs-lookup"><span data-stu-id="91a08-196">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="91a08-197">Лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="91a08-197">Lambda expressions</span></span>

<span data-ttu-id="91a08-198">Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="91a08-198">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="91a08-199">Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов.</span><span class="sxs-lookup"><span data-stu-id="91a08-199">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="91a08-200">В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:</span><span class="sxs-lookup"><span data-stu-id="91a08-200">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="91a08-201">**Не** используйте переменную цикла непосредственно в лямбда-выражении, например `i` в предыдущем примере цикла `for`.</span><span class="sxs-lookup"><span data-stu-id="91a08-201">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="91a08-202">В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение будет одинаковым во всех лямбда-выражениях.</span><span class="sxs-lookup"><span data-stu-id="91a08-202">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="91a08-203">Всегда записывайте значение переменной в локальную переменную, а затем используйте ее.</span><span class="sxs-lookup"><span data-stu-id="91a08-203">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="91a08-204">В предыдущем примере переменная цикла `i` назначается `buttonNumber`.</span><span class="sxs-lookup"><span data-stu-id="91a08-204">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="91a08-205">EventCallback</span><span class="sxs-lookup"><span data-stu-id="91a08-205">EventCallback</span></span>

<span data-ttu-id="91a08-206">Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента,</span><span class="sxs-lookup"><span data-stu-id="91a08-206">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="91a08-207">например, когда в дочернем элементе возникает событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="91a08-207">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="91a08-208">Чтобы обеспечить доступ к событиям в компонентах, используйте <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="91a08-208">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="91a08-209">Родительский компонент может назначить метод обратного вызова <xref:Microsoft.AspNetCore.Components.EventCallback> дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="91a08-209">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="91a08-210">В `ChildComponent` в примере приложения (`Components/ChildComponent.razor`) показано, как обработчик `onclick` кнопки настроен на получение делегата <xref:Microsoft.AspNetCore.Components.EventCallback> из `ParentComponent` образца.</span><span class="sxs-lookup"><span data-stu-id="91a08-210">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="91a08-211"><xref:Microsoft.AspNetCore.Components.EventCallback> вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:</span><span class="sxs-lookup"><span data-stu-id="91a08-211">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="91a08-212">`ParentComponent` задает для <xref:Microsoft.AspNetCore.Components.EventCallback%601> дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="91a08-212">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="91a08-213">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="91a08-213">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="91a08-214">При выборе кнопки в `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="91a08-214">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="91a08-215">Вызывается метод `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="91a08-215">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="91a08-216">`messageText` обновляется и отображается в `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="91a08-216">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="91a08-217">Вызов [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="91a08-217">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="91a08-218"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе.</span><span class="sxs-lookup"><span data-stu-id="91a08-218"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="91a08-219"><xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> разрешают выполнять асинхронные делегаты.</span><span class="sxs-lookup"><span data-stu-id="91a08-219"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="91a08-220">Структура <xref:Microsoft.AspNetCore.Components.EventCallback> слабо типизирована и позволяет передавать любой аргумент типа в `InvokeAsync(Object)`.</span><span class="sxs-lookup"><span data-stu-id="91a08-220"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="91a08-221">Структура <xref:Microsoft.AspNetCore.Components.EventCallback%601> сильно типизирована и требует передачи аргумента `T` в `InvokeAsync(T)`, который можно назначить `TValue`.</span><span class="sxs-lookup"><span data-stu-id="91a08-221"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="91a08-222">Вызов <xref:Microsoft.AspNetCore.Components.EventCallback> или <xref:Microsoft.AspNetCore.Components.EventCallback%601> с <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> и ожидание <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="91a08-222">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="91a08-223">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> для обработки событий и параметров компонента привязки.</span><span class="sxs-lookup"><span data-stu-id="91a08-223">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="91a08-224">Предпочтительнее использовать строго типизированный <xref:Microsoft.AspNetCore.Components.EventCallback%601> вместо <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="91a08-224">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="91a08-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> обеспечивает более эффективное реагирование на ошибки для пользователей компонента.</span><span class="sxs-lookup"><span data-stu-id="91a08-225"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="91a08-226">Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным.</span><span class="sxs-lookup"><span data-stu-id="91a08-226">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="91a08-227">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback>, если в обратный вызов не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="91a08-227">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="91a08-228">Запрет действий по умолчанию</span><span class="sxs-lookup"><span data-stu-id="91a08-228">Prevent default actions</span></span>

<span data-ttu-id="91a08-229">Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="91a08-229">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="91a08-230">Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа.</span><span class="sxs-lookup"><span data-stu-id="91a08-230">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="91a08-231">В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="91a08-231">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="91a08-232">Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:</span><span class="sxs-lookup"><span data-stu-id="91a08-232">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="91a08-233">Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="91a08-233">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="91a08-234">Значение атрибута также может быть выражением.</span><span class="sxs-lookup"><span data-stu-id="91a08-234">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="91a08-235">В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:</span><span class="sxs-lookup"><span data-stu-id="91a08-235">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="91a08-236">Остановка распространения событий</span><span class="sxs-lookup"><span data-stu-id="91a08-236">Stop event propagation</span></span>

<span data-ttu-id="91a08-237">Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.</span><span class="sxs-lookup"><span data-stu-id="91a08-237">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="91a08-238">В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:</span><span class="sxs-lookup"><span data-stu-id="91a08-238">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
