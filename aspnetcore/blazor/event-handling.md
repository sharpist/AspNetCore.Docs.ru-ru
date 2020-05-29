---
title: Обработка событий Blazor в ASP.NET Core
author: guardrex
description: Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 610cb9124f59ed07f1fe6193f92052b4513450c8
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424250"
---
# <a name="aspnet-core-blazor-event-handling"></a>Обработка событий Blazor в ASP.NET Core

Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)

Компоненты Razor предоставляют функции обработки событий. Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент Razor рассматривает значение атрибута как обработчик событий.

Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:

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

Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>. Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/lifecycle#state-changes). Исключения регистрируются при их возникновении.

В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:

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

## <a name="event-argument-types"></a>Типы аргументов событий

Для некоторых событий разрешены типы аргументов событий. Указание типа события в вызове метода требуется только в том случае, если тип события используется в методе.

Поддерживаемые параметры `EventArgs` приведены в следующей таблице.

| событие            | Класс                | События DOM и примечания |
| ---------------- | -------------------- | -------------------- |
| буфер обмена        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Перетаскивание             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` и `DataTransferItem` содержат данные перетаскиваемого элемента. |
| Ошибка            | `ErrorEventArgs`     | `onerror` |
| событие            | `EventArgs`          | *Общие сведения*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Буфер обмена*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Ввод*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Носитель*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Фокус            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Не включает поддержку `relatedTarget`. |
| Входные данные            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Клавиатура         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Мышь            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Указатель мыши    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Колесико мыши      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Ход выполнения         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Сенсорные технологии            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` представляет одну точку касания на устройстве с сенсорным вводом. |

Дополнительные сведения см. в следующих ресурсах:

* [Классы EventArgs в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [Веб-документы MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; – содержит сведения о том, какие элементы HTML поддерживают каждое событие DOM.

## <a name="lambda-expressions"></a>Лямбда-выражения

Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов. В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (`MouseEventArgs`) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:

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
> **Не** используйте переменную цикла (`i`) в цикле `for` непосредственно в лямбда-выражении. В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение `i` будет одинаковым во всех лямбда-выражениях. Всегда записывайте значение в локальную переменную (`buttonNumber` в предыдущем примере), а затем используйте ее.

## <a name="eventcallback"></a>EventCallback

Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента, например, когда в дочернем элементе возникает событие `onclick`. Чтобы обеспечить доступ к событиям в компонентах, используйте `EventCallback`. Родительский компонент может назначить метод обратного вызова `EventCallback` дочернего компонента.

В `ChildComponent` в примере приложения (*Components/ChildComponent.razor*) показано, как обработчик `onclick` кнопки настроен на получение делегата `EventCallback` из `ParentComponent` образца. `EventCallback` вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` задает для `EventCallback<T>` дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.

*Pages/ParentComponent.razor*:

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

При выборе кнопки в `ChildComponent`:

* Вызывается метод `ShowMessage` `ParentComponent`. `messageText` обновляется и отображается в `ParentComponent`.
* Вызов [StateHasChanged](xref:blazor/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`). `StateHasChanged` вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе.

`EventCallback` и `EventCallback<T>` разрешают выполнять асинхронные делегаты. `EventCallback<T>` является строго типизированным и требует определенного типа аргумента. `EventCallback` слабо типизирован и допускает любой тип аргумента.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Вызов `EventCallback` или `EventCallback<T>` с `InvokeAsync` и ожидание <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Используйте `EventCallback` и `EventCallback<T>` для обработки событий и параметров компонента привязки.

Предпочтительнее использовать строго типизированный `EventCallback<T>` вместо `EventCallback`. `EventCallback<T>` обеспечивает более эффективное реагирование на ошибки для пользователей компонента. Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным. Используйте `EventCallback`, если в обратный вызов не было передано значение.

## <a name="prevent-default-actions"></a>Запрет действий по умолчанию

Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.

Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа. В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`. Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:

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

Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.

Значение атрибута также может быть выражением. В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

Для запрета действий по умолчанию не требуется обработчик событий. Обработчик событий и сценарий запрета действий по умолчанию можно использовать независимо друг от друга.

## <a name="stop-event-propagation"></a>Остановка распространения событий

Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.

В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:

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
