---
title: Привязка к данным в ASP.NET Core Blazor
author: guardrex
description: Сведения о функциях привязки данных для компонентов и элементов модели DOM в приложениях Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/data-binding
ms.openlocfilehash: 3b41aedcbd0d2c22b20d8fa3a21b8af97d1fbb2c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628564"
---
# <a name="aspnet-core-no-locblazor-data-binding"></a><span data-ttu-id="c507d-103">Привязка к данным в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c507d-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="c507d-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="c507d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c507d-105">Компоненты Razor реализуют функции привязки данных для поля, свойства или значения выражения Razor с помощью атрибута HTML-элемента [`@bind`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="c507d-105">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="c507d-106">В следующем примере выполняется привязка элемента `<input>` к полю `currentValue`, а элемента `<input>` — к свойству `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="c507d-106">The following example binds an `<input>` element to the `currentValue` field and an `<input>` element to the `CurrentValue` property:</span></span>

```razor
<p>
    <input @bind="currentValue" /> Current value: @currentValue
</p>

<p>
    <input @bind="CurrentValue" /> Current value: @CurrentValue
</p>

@code {
    private string currentValue;

    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c507d-107">Когда один из элементов теряет фокус, привязанное поле или свойство обновляется.</span><span class="sxs-lookup"><span data-stu-id="c507d-107">When one of the elements looses focus, its bound field or property is updated.</span></span>

<span data-ttu-id="c507d-108">Текстовое поле обновляется в пользовательском интерфейсе только при отрисовке компонента, а не в ответ на изменение значения поля или свойства.</span><span class="sxs-lookup"><span data-stu-id="c507d-108">The text box is updated in the UI only when the component is rendered, not in response to changing the field's or property's value.</span></span> <span data-ttu-id="c507d-109">Так как компоненты отрисовываются после выполнения кода обработчика событий, изменения полей и свойств *обычно* отражаются в пользовательском интерфейсе сразу после активации обработчика событий.</span><span class="sxs-lookup"><span data-stu-id="c507d-109">Since components render themselves after event handler code executes, field and property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="c507d-110">Использование [`@bind`](xref:mvc/views/razor#bind) со свойством `CurrentValue` (`<input @bind="CurrentValue" />`) фактически эквивалентно следующему:</span><span class="sxs-lookup"><span data-stu-id="c507d-110">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c507d-111">Когда компонент отрисовывается, значение `value` элемента input берется из свойства `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="c507d-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c507d-112">Когда пользователь вводит данные в текстовом поле, а затем переводит фокус, инициируется событие `onchange`, и свойству `CurrentValue` присваивается измененное значение.</span><span class="sxs-lookup"><span data-stu-id="c507d-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c507d-113">На практике код обычно сложнее этого, так как [`@bind`](xref:mvc/views/razor#bind) применяется в случаях, когда выполняется преобразование типов.</span><span class="sxs-lookup"><span data-stu-id="c507d-113">In reality, the code generation is more complex than that because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="c507d-114">Как правило, [`@bind`](xref:mvc/views/razor#bind) связывает текущее значение выражения с атрибутом `value` и обрабатывает изменения с помощью зарегистрированного обработчика.</span><span class="sxs-lookup"><span data-stu-id="c507d-114">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c507d-115">Чтобы привязать свойство или поле к другим событиям, можно использовать атрибут `@bind:event` с параметром `event`.</span><span class="sxs-lookup"><span data-stu-id="c507d-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="c507d-116">В следующем примере свойство `CurrentValue` привязывается к событию `oninput`:</span><span class="sxs-lookup"><span data-stu-id="c507d-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c507d-117">В отличие от события `onchange`, которое происходит, когда элемент теряет фокус, событие `oninput` происходит при изменении значения в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="c507d-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c507d-118">Для привязки атрибутов элементов, отличных от `value`, используйте `@bind-{ATTRIBUTE}` с синтаксисом `@bind-{ATTRIBUTE}:event`.</span><span class="sxs-lookup"><span data-stu-id="c507d-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="c507d-119">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="c507d-119">In the following example:</span></span>

* <span data-ttu-id="c507d-120">При загрузке компонента у абзаца **красный** стиль (`style="color:red"`).</span><span class="sxs-lookup"><span data-stu-id="c507d-120">The paragraph's style is **red** when the component loads (`style="color:red"`).</span></span>
* <span data-ttu-id="c507d-121">Пользователь изменяет значение текстового поля, чтобы отразить другой цветовой стиль CSS, и изменяет фокус элемента страницы.</span><span class="sxs-lookup"><span data-stu-id="c507d-121">The user changes the value of the text box to reflect a different CSS color style and changes the page's element focus.</span></span> <span data-ttu-id="c507d-122">Например, пользователь изменяет значение текстового поля на `color:blue` и нажимает клавишу <kbd>TAB</kbd> на клавиатуре.</span><span class="sxs-lookup"><span data-stu-id="c507d-122">For example, the user changes the text box value to `color:blue` and presses the <kbd>Tab</kbd> key on the keyboard.</span></span>
* <span data-ttu-id="c507d-123">При изменении фокуса элемента:</span><span class="sxs-lookup"><span data-stu-id="c507d-123">When the element focus changes:</span></span>
  * <span data-ttu-id="c507d-124">Значение `paragraphStyle` присваивается из значения элемента `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c507d-124">The value of `paragraphStyle` is assigned from the `<input>` element's value.</span></span>
  * <span data-ttu-id="c507d-125">Стиль абзаца изменяется для отражения нового стиля в `paragraphStyle`.</span><span class="sxs-lookup"><span data-stu-id="c507d-125">The paragraph style is updated to reflect the new style in `paragraphStyle`.</span></span> <span data-ttu-id="c507d-126">Если стиль изменяется на `color:blue`, цвет текста становится **синим**.</span><span class="sxs-lookup"><span data-stu-id="c507d-126">If the style is updated to `color:blue`, the text color changes to **blue**.</span></span>

```razor
<p>
    <input type="text" @bind="paragraphStyle" />
</p>

<p @bind-style="paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string paragraphStyle = "color:red";
}
```

<span data-ttu-id="c507d-127">Привязка атрибута учитывает регистр:</span><span class="sxs-lookup"><span data-stu-id="c507d-127">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="c507d-128">`@bind` является допустимым;</span><span class="sxs-lookup"><span data-stu-id="c507d-128">`@bind` is valid.</span></span>
* <span data-ttu-id="c507d-129">`@Bind` и `@BIND` является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="c507d-129">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="c507d-130">Значения, не поддающиеся анализу</span><span class="sxs-lookup"><span data-stu-id="c507d-130">Unparsable values</span></span>

<span data-ttu-id="c507d-131">Когда пользователь присваивает не поддающееся анализу значение элементу, привязанному к данным, при активации события привязки автоматически восстанавливается предыдущее значение.</span><span class="sxs-lookup"><span data-stu-id="c507d-131">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="c507d-132">Рассмотрим следующий сценарий.</span><span class="sxs-lookup"><span data-stu-id="c507d-132">Consider the following scenario:</span></span>

* <span data-ttu-id="c507d-133">Элемент `<input>` привязан к типу `int` с начальным значением `123`:</span><span class="sxs-lookup"><span data-stu-id="c507d-133">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="inputValue" />

  @code {
      private int inputValue = 123;
  }
  ```

* <span data-ttu-id="c507d-134">Пользователь изменяет значение элемента на `123.45` на странице и переводит фокус.</span><span class="sxs-lookup"><span data-stu-id="c507d-134">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="c507d-135">В приведенном выше сценарии восстанавливается значение элемента `123`.</span><span class="sxs-lookup"><span data-stu-id="c507d-135">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="c507d-136">Когда значение `123.45` отклоняется и вместо него используется исходное значение `123`, пользователь понимает, что его значение не было принято.</span><span class="sxs-lookup"><span data-stu-id="c507d-136">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="c507d-137">По умолчанию привязка применяется к событию `onchange` элемента (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="c507d-137">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="c507d-138">Чтобы выполнить привязку к другому событию, используйте `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`.</span><span class="sxs-lookup"><span data-stu-id="c507d-138">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="c507d-139">Для события `oninput` (`@bind:event="oninput"`) исходное значение восстанавливается после любого нажатия клавиши, которое приводит к вводу не поддающегося анализу значения.</span><span class="sxs-lookup"><span data-stu-id="c507d-139">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="c507d-140">При привязке типа `int` к событию `oninput` пользователь не может ввести символ `.`.</span><span class="sxs-lookup"><span data-stu-id="c507d-140">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="c507d-141">Символ `.` немедленно удаляется, так что пользователь сразу понимает, что допустимы только целые числа.</span><span class="sxs-lookup"><span data-stu-id="c507d-141">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="c507d-142">В некоторых ситуациях восстанавливать исходное значение при возникновении события `oninput` нежелательно, например, когда пользователю следует дать возможность самому удалять не поддающиеся анализу значения `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c507d-142">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="c507d-143">Ниже представлены возможные альтернативы.</span><span class="sxs-lookup"><span data-stu-id="c507d-143">Alternatives include:</span></span>

* <span data-ttu-id="c507d-144">Не используйте событие `oninput`.</span><span class="sxs-lookup"><span data-stu-id="c507d-144">Don't use the `oninput` event.</span></span> <span data-ttu-id="c507d-145">Используйте вместо этого событие `onchange` по умолчанию (укажите просто `@bind="{PROPERTY OR FIELD}"`). В этом случае недопустимое значение не будет меняться на исходное, пока элемент не потеряет фокус.</span><span class="sxs-lookup"><span data-stu-id="c507d-145">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="c507d-146">Выполните привязку к типу, допускающему значение NULL, например `int?` или `string`, и предоставьте пользовательскую логику для обработки недопустимых значений.</span><span class="sxs-lookup"><span data-stu-id="c507d-146">Bind to a nullable type, such as `int?` or `string` and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="c507d-147">Используйте [компонент для проверки формы](xref:blazor/forms-validation), например <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> или <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="c507d-147">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="c507d-148">Компоненты для проверки форм имеют встроенную поддержку управления недопустимыми входными данными.</span><span class="sxs-lookup"><span data-stu-id="c507d-148">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="c507d-149">Дополнительные сведения см. в разделе <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="c507d-149">For more information, see <xref:blazor/forms-validation>.</span></span> <span data-ttu-id="c507d-150">Компоненты для проверки форм:</span><span class="sxs-lookup"><span data-stu-id="c507d-150">Form validation components:</span></span>
  * <span data-ttu-id="c507d-151">позволяют пользователю вводить недопустимые данные и получать ошибки проверки в соответствующем контексте <xref:Microsoft.AspNetCore.Components.Forms.EditContext>;</span><span class="sxs-lookup"><span data-stu-id="c507d-151">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="c507d-152">выводят ошибки проверки в пользовательском интерфейсе, не мешая пользователю вводить дополнительные данные в веб-форме.</span><span class="sxs-lookup"><span data-stu-id="c507d-152">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="c507d-153">Строки формата</span><span class="sxs-lookup"><span data-stu-id="c507d-153">Format strings</span></span>

<span data-ttu-id="c507d-154">Привязка данных работает со строками формата <xref:System.DateTime> с использованием `@bind:format`.</span><span class="sxs-lookup"><span data-stu-id="c507d-154">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="c507d-155">Другие выражения форматирования, например денежные или числовые форматы, в настоящее время недоступны.</span><span class="sxs-lookup"><span data-stu-id="c507d-155">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="startDate" @bind:format="yyyy-MM-dd" />

@code {
    private DateTime startDate = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c507d-156">В приведенном выше коде типом поля элемента `<input>` (`type`) по умолчанию является `text`.</span><span class="sxs-lookup"><span data-stu-id="c507d-156">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="c507d-157">`@bind:format` поддерживается для привязки следующих типов .NET:</span><span class="sxs-lookup"><span data-stu-id="c507d-157">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c507d-158"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c507d-158"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c507d-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c507d-159"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c507d-160">Атрибут `@bind:format` указывает формат даты, применяемый к значению `value` элемента `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c507d-160">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c507d-161">Формат также используется для синтаксического анализа значения при возникновении события `onchange`.</span><span class="sxs-lookup"><span data-stu-id="c507d-161">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c507d-162">Указывать формат для типа поля `date` не рекомендуется, так как в Blazor есть встроенная поддержка форматирования дат.</span><span class="sxs-lookup"><span data-stu-id="c507d-162">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="c507d-163">Если все же для типа поля `date` указывается формат, для правильной работы привязки используйте только формат даты `yyyy-MM-dd`.</span><span class="sxs-lookup"><span data-stu-id="c507d-163">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to function correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="startDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="c507d-164">Привязка родительского компонента к дочернему с помощью параметров компонентов</span><span class="sxs-lookup"><span data-stu-id="c507d-164">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="c507d-165">Параметры компонента позволяют привязывать свойства и поля родительского компонента с помощью синтаксиса `@bind-{PROPERTY OR FIELD}`.</span><span class="sxs-lookup"><span data-stu-id="c507d-165">Component parameters permit binding properties and fields of a parent component with `@bind-{PROPERTY OR FIELD}` syntax.</span></span>

<span data-ttu-id="c507d-166">Следующий компонент `Child` (`Child.razor`) имеет параметр компонента `Year` и обратный вызов `YearChanged`.</span><span class="sxs-lookup"><span data-stu-id="c507d-166">The following `Child` component (`Child.razor`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<div class="card bg-light mt-3" style="width:18rem ">
    <div class="card-body">
        <h3 class="card-title">Child Component</h3>
        <p class="card-text">Child <code>Year</code>: @Year</p>
        <p>
            <button @onclick="UpdateYear">
                Update Child <code>Year</code> and call 
                <code>YearChanged.InvokeAsync(Year)</code>
            </button>
        </p>
    </div>
</div>

@code {
    private Random r = new Random();

    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }

    private Task UpdateYear()
    {
        Year = r.Next(10050, 12021);

        return YearChanged.InvokeAsync(Year);
    }
}
```

<span data-ttu-id="c507d-167">Имя обратного вызова (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) должно включать имя параметра компонента, за которым следует суффикс "`Changed`" (`{PARAMETER NAME}Changed`).</span><span class="sxs-lookup"><span data-stu-id="c507d-167">The callback (<xref:Microsoft.AspNetCore.Components.EventCallback%601>) must be named as the component parameter name followed by the "`Changed`" suffix (`{PARAMETER NAME}Changed`).</span></span> <span data-ttu-id="c507d-168">В предыдущем примере обратный вызов назывался `YearChanged`.</span><span class="sxs-lookup"><span data-stu-id="c507d-168">In the preceding example, the callback is named `YearChanged`.</span></span> <span data-ttu-id="c507d-169">Дополнительные сведения о методе <xref:Microsoft.AspNetCore.Components.EventCallback%601> см. в разделе <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="c507d-169">For more information on <xref:Microsoft.AspNetCore.Components.EventCallback%601>, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

<span data-ttu-id="c507d-170">В следующем компоненте `Parent` (`Parent.razor`) поле `year` привязано к параметру `Year` дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="c507d-170">In the following `Parent` component (`Parent.razor`), the `year` field is bound to the `Year` parameter of the child component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<p>Parent <code>year</code>: @year</p>

<button @onclick="UpdateYear">Update Parent <code>year</code></button>

<Child @bind-Year="year" />

@code {
    private Random r = new Random();
    private int year = 1978;

    private void UpdateYear()
    {
        year = r.Next(1950, 2021);
    }
}
```

<span data-ttu-id="c507d-171">Параметр `Year` допускает привязку, так как он имеет сопутствующее событие `YearChanged`, соответствующее типу параметра `Year`.</span><span class="sxs-lookup"><span data-stu-id="c507d-171">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c507d-172">По соглашению свойство можно привязать к соответствующему обработчику событий, включив атрибут `@bind-{PROPERTY}:event`, назначенный обработчику.</span><span class="sxs-lookup"><span data-stu-id="c507d-172">By convention, a property can be bound to a corresponding event handler by including an `@bind-{PROPERTY}:event` attribute assigned to the handler.</span></span> <span data-ttu-id="c507d-173">Запись `<Child @bind-Year="year" />` эквивалентна следующей.</span><span class="sxs-lookup"><span data-stu-id="c507d-173">`<Child @bind-Year="year" />` is equivalent to writing:</span></span>

```razor
<Child @bind-Year="year" @bind-Year:event="YearChanged" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="c507d-174">Привязка дочернего компонента к родительскому посредством цепочки привязки</span><span class="sxs-lookup"><span data-stu-id="c507d-174">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="c507d-175">Распространенным сценарием является связывание привязанного к данным параметра к элементу страницы в выходных данных компонента.</span><span class="sxs-lookup"><span data-stu-id="c507d-175">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="c507d-176">Такой сценарий называется *цепочкой привязки*, так как одновременно имеется несколько уровней привязки.</span><span class="sxs-lookup"><span data-stu-id="c507d-176">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="c507d-177">Цепочку привязки нельзя реализовать с помощью синтаксиса [`@bind`](xref:mvc/views/razor#bind) в дочернем компоненте.</span><span class="sxs-lookup"><span data-stu-id="c507d-177">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the child component.</span></span> <span data-ttu-id="c507d-178">Обработчик событий и значение необходимо указывать отдельно.</span><span class="sxs-lookup"><span data-stu-id="c507d-178">The event handler and value must be specified separately.</span></span> <span data-ttu-id="c507d-179">Но родительский компонент может использовать синтаксис [`@bind`](xref:mvc/views/razor#bind) с параметром дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="c507d-179">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the child component's parameter.</span></span>

<span data-ttu-id="c507d-180">Следующий компонент `PasswordField` (`PasswordField.razor`):</span><span class="sxs-lookup"><span data-stu-id="c507d-180">The following `PasswordField` component (`PasswordField.razor`):</span></span>

* <span data-ttu-id="c507d-181">присваивает элементу `<input>` значение свойства `Password`;</span><span class="sxs-lookup"><span data-stu-id="c507d-181">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="c507d-182">сообщает об изменениях свойства `Password` родительскому компоненту посредством [`EventCallback`](xref:blazor/components/event-handling#eventcallback);</span><span class="sxs-lookup"><span data-stu-id="c507d-182">Exposes changes of the `Password` property to a parent component with an [`EventCallback`](xref:blazor/components/event-handling#eventcallback).</span></span>
* <span data-ttu-id="c507d-183">использует событие `onclick` для активации метода `ToggleShowPassword`.</span><span class="sxs-lookup"><span data-stu-id="c507d-183">Uses the `onclick` event to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="c507d-184">Для получения дополнительной информации см. <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c507d-184">For more information, see <xref:blazor/components/event-handling>.</span></span>

```razor
<h1>Child Component</h1>

Password:

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="c507d-185">Компонент `PasswordField` используется в другом компоненте:</span><span class="sxs-lookup"><span data-stu-id="c507d-185">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/Parent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="c507d-186">Для проверки пароля или перехвата ошибок в нем в предыдущем примере выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="c507d-186">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="c507d-187">Создайте резервное поле для `Password` (`password` в следующем примере кода).</span><span class="sxs-lookup"><span data-stu-id="c507d-187">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="c507d-188">Выполните проверки или перехватите ошибки в методе задания `Password`.</span><span class="sxs-lookup"><span data-stu-id="c507d-188">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="c507d-189">В следующем примере пользователю немедленно сообщается о том, что в пароле есть пробел:</span><span class="sxs-lookup"><span data-stu-id="c507d-189">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
<h1>Child Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="c507d-190">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="c507d-190">Additional resources</span></span>

* [<span data-ttu-id="c507d-191">Привязка к переключателям в форме</span><span class="sxs-lookup"><span data-stu-id="c507d-191">Binding to radio buttons in a form</span></span>](xref:blazor/forms-validation#radio-buttons)
* [<span data-ttu-id="c507d-192">Привязка параметров элемента `<select>` к значению объекта C# `null` в форме</span><span class="sxs-lookup"><span data-stu-id="c507d-192">Binding `<select>` element options to C# object `null` values in a form</span></span>](xref:blazor/forms-validation#binding-select-element-options-to-c-object-null-values)
