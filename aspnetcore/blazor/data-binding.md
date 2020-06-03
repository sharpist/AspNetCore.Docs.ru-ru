---
<span data-ttu-id="c4087-101">title: 'Привязка данных ASP.NET Core Blazor' author: description: 'Узнайте о функциях привязки данных для компонентов и элементов модели DOM в приложениях Blazor.'</span><span class="sxs-lookup"><span data-stu-id="c4087-101">title: 'ASP.NET Core Blazor data binding' author: description: 'Learn about data binding features for components and DOM elements in Blazor apps.'</span></span>
<span data-ttu-id="c4087-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="c4087-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="c4087-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="c4087-103">'Blazor'</span></span>
- <span data-ttu-id="c4087-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="c4087-104">'Identity'</span></span>
- <span data-ttu-id="c4087-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="c4087-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="c4087-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="c4087-106">'Razor'</span></span>
- <span data-ttu-id="c4087-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="c4087-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-data-binding"></a><span data-ttu-id="c4087-108">Привязка к данным в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c4087-108">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="c4087-109">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="c4087-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="c4087-110">Компоненты Razor реализуют функции привязки данных для поля, свойства или значения выражения Razor с помощью атрибута HTML-элемента [`@bind`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="c4087-110">Razor components provide data binding features via an HTML element attribute named [`@bind`](xref:mvc/views/razor#bind) with a field, property, or Razor expression value.</span></span>

<span data-ttu-id="c4087-111">В следующем примере показана привязка свойства `CurrentValue` к значению текстового поля:</span><span class="sxs-lookup"><span data-stu-id="c4087-111">The following example binds the `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c4087-112">Когда текстовое поле теряет фокус, значение свойства обновляется.</span><span class="sxs-lookup"><span data-stu-id="c4087-112">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="c4087-113">Текстовое поле обновляется в пользовательском интерфейсе только при отрисовке компонента, а не в ответ на изменение значения свойства.</span><span class="sxs-lookup"><span data-stu-id="c4087-113">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="c4087-114">Так как компоненты отрисовываются после выполнения кода обработчика событий, изменения свойств *обычно* отражаются в пользовательском интерфейсе сразу после активации обработчика событий.</span><span class="sxs-lookup"><span data-stu-id="c4087-114">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="c4087-115">Использование [`@bind`](xref:mvc/views/razor#bind) со свойством `CurrentValue` (`<input @bind="CurrentValue" />`) фактически эквивалентно следующему:</span><span class="sxs-lookup"><span data-stu-id="c4087-115">Using [`@bind`](xref:mvc/views/razor#bind) with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c4087-116">Когда компонент отрисовывается, значение `value` элемента input берется из свойства `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="c4087-116">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="c4087-117">Когда пользователь вводит данные в текстовом поле, а затем переводит фокус, инициируется событие `onchange`, и свойству `CurrentValue` присваивается измененное значение.</span><span class="sxs-lookup"><span data-stu-id="c4087-117">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="c4087-118">На практике код обычно сложнее, так как [`@bind`](xref:mvc/views/razor#bind) применяется в случаях, когда выполняется преобразование типов.</span><span class="sxs-lookup"><span data-stu-id="c4087-118">In reality, the code generation is more complex because [`@bind`](xref:mvc/views/razor#bind) handles cases where type conversions are performed.</span></span> <span data-ttu-id="c4087-119">Как правило, [`@bind`](xref:mvc/views/razor#bind) связывает текущее значение выражения с атрибутом `value` и обрабатывает изменения с помощью зарегистрированного обработчика.</span><span class="sxs-lookup"><span data-stu-id="c4087-119">In principle, [`@bind`](xref:mvc/views/razor#bind) associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="c4087-120">Чтобы привязать свойство или поле к другим событиям, можно использовать атрибут `@bind:event` с параметром `event`.</span><span class="sxs-lookup"><span data-stu-id="c4087-120">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="c4087-121">В следующем примере свойство `CurrentValue` привязывается к событию `oninput`:</span><span class="sxs-lookup"><span data-stu-id="c4087-121">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="c4087-122">В отличие от события `onchange`, которое происходит, когда элемент теряет фокус, событие `oninput` происходит при изменении значения в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="c4087-122">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="c4087-123">Для привязки атрибутов элементов, отличных от `value`, используйте `@bind-{ATTRIBUTE}` с синтаксисом `@bind-{ATTRIBUTE}:event`.</span><span class="sxs-lookup"><span data-stu-id="c4087-123">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="c4087-124">В следующем примере стиль абзаца обновляется при изменении значения `paragraphStyle`:</span><span class="sxs-lookup"><span data-stu-id="c4087-124">In the following example, the paragraph's style is updated when the `paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

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

<span data-ttu-id="c4087-125">Привязка атрибута учитывает регистр:</span><span class="sxs-lookup"><span data-stu-id="c4087-125">Attribute binding is case sensitive:</span></span>

* <span data-ttu-id="c4087-126">`@bind` является допустимым;</span><span class="sxs-lookup"><span data-stu-id="c4087-126">`@bind` is valid.</span></span>
* <span data-ttu-id="c4087-127">`@Bind` и `@BIND` является недопустимым.</span><span class="sxs-lookup"><span data-stu-id="c4087-127">`@Bind` and `@BIND` are invalid.</span></span>

## <a name="unparsable-values"></a><span data-ttu-id="c4087-128">Значения, не поддающиеся анализу</span><span class="sxs-lookup"><span data-stu-id="c4087-128">Unparsable values</span></span>

<span data-ttu-id="c4087-129">Когда пользователь присваивает не поддающееся анализу значение элементу, привязанному к данным, при активации события привязки автоматически восстанавливается предыдущее значение.</span><span class="sxs-lookup"><span data-stu-id="c4087-129">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="c4087-130">Рассмотрим следующий сценарий.</span><span class="sxs-lookup"><span data-stu-id="c4087-130">Consider the following scenario:</span></span>

* <span data-ttu-id="c4087-131">Элемент `<input>` привязан к типу `int` с начальным значением `123`:</span><span class="sxs-lookup"><span data-stu-id="c4087-131">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="c4087-132">Пользователь изменяет значение элемента на `123.45` на странице и переводит фокус.</span><span class="sxs-lookup"><span data-stu-id="c4087-132">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="c4087-133">В приведенном выше сценарии восстанавливается значение элемента `123`.</span><span class="sxs-lookup"><span data-stu-id="c4087-133">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="c4087-134">Когда значение `123.45` отклоняется и вместо него используется исходное значение `123`, пользователь понимает, что его значение не было принято.</span><span class="sxs-lookup"><span data-stu-id="c4087-134">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="c4087-135">По умолчанию привязка применяется к событию `onchange` элемента (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="c4087-135">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="c4087-136">Чтобы выполнить привязку к другому событию, используйте `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}`.</span><span class="sxs-lookup"><span data-stu-id="c4087-136">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="c4087-137">Для события `oninput` (`@bind:event="oninput"`) исходное значение восстанавливается после любого нажатия клавиши, которое приводит к вводу не поддающегося анализу значения.</span><span class="sxs-lookup"><span data-stu-id="c4087-137">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="c4087-138">При привязке типа `int` к событию `oninput` пользователь не может ввести символ `.`.</span><span class="sxs-lookup"><span data-stu-id="c4087-138">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="c4087-139">Символ `.` немедленно удаляется, так что пользователь сразу понимает, что допустимы только целые числа.</span><span class="sxs-lookup"><span data-stu-id="c4087-139">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="c4087-140">В некоторых ситуациях восстанавливать исходное значение при возникновении события `oninput` нежелательно, например, когда пользователю следует дать возможность самому удалять не поддающиеся анализу значения `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c4087-140">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="c4087-141">Ниже представлены возможные альтернативы.</span><span class="sxs-lookup"><span data-stu-id="c4087-141">Alternatives include:</span></span>

* <span data-ttu-id="c4087-142">Не используйте событие `oninput`.</span><span class="sxs-lookup"><span data-stu-id="c4087-142">Don't use the `oninput` event.</span></span> <span data-ttu-id="c4087-143">Используйте вместо этого событие `onchange` по умолчанию (укажите просто `@bind="{PROPERTY OR FIELD}"`). В этом случае недопустимое значение не будет меняться на исходное, пока элемент не потеряет фокус.</span><span class="sxs-lookup"><span data-stu-id="c4087-143">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="c4087-144">Выполните привязку к типу, допускающему значение NULL, например `int?` или `string`, и предоставьте пользовательскую логику для обработки недопустимых значений.</span><span class="sxs-lookup"><span data-stu-id="c4087-144">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="c4087-145">Используйте [компонент для проверки формы](xref:blazor/forms-validation), например <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> или <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span><span class="sxs-lookup"><span data-stu-id="c4087-145">Use a [form validation component](xref:blazor/forms-validation), such as <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> or <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>.</span></span> <span data-ttu-id="c4087-146">Компоненты для проверки форм имеют встроенную поддержку управления недопустимыми входными данными.</span><span class="sxs-lookup"><span data-stu-id="c4087-146">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="c4087-147">Компоненты для проверки форм:</span><span class="sxs-lookup"><span data-stu-id="c4087-147">Form validation components:</span></span>
  * <span data-ttu-id="c4087-148">позволяют пользователю вводить недопустимые данные и получать ошибки проверки в соответствующем контексте <xref:Microsoft.AspNetCore.Components.Forms.EditContext>;</span><span class="sxs-lookup"><span data-stu-id="c4087-148">Permit the user to provide invalid input and receive validation errors on the associated <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
  * <span data-ttu-id="c4087-149">выводят ошибки проверки в пользовательском интерфейсе, не мешая пользователю вводить дополнительные данные в веб-форме.</span><span class="sxs-lookup"><span data-stu-id="c4087-149">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="c4087-150">Строки формата</span><span class="sxs-lookup"><span data-stu-id="c4087-150">Format strings</span></span>

<span data-ttu-id="c4087-151">Привязка данных работает со строками формата <xref:System.DateTime> с использованием `@bind:format`.</span><span class="sxs-lookup"><span data-stu-id="c4087-151">Data binding works with <xref:System.DateTime> format strings using `@bind:format`.</span></span> <span data-ttu-id="c4087-152">Другие выражения форматирования, например денежные или числовые форматы, в настоящее время недоступны.</span><span class="sxs-lookup"><span data-stu-id="c4087-152">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="c4087-153">В приведенном выше коде типом поля элемента `<input>` (`type`) по умолчанию является `text`.</span><span class="sxs-lookup"><span data-stu-id="c4087-153">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="c4087-154">`@bind:format` поддерживается для привязки следующих типов .NET:</span><span class="sxs-lookup"><span data-stu-id="c4087-154">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="c4087-155"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c4087-155"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="c4087-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="c4087-156"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="c4087-157">Атрибут `@bind:format` указывает формат даты, применяемый к значению `value` элемента `<input>`.</span><span class="sxs-lookup"><span data-stu-id="c4087-157">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="c4087-158">Формат также используется для синтаксического анализа значения при возникновении события `onchange`.</span><span class="sxs-lookup"><span data-stu-id="c4087-158">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="c4087-159">Указывать формат для типа поля `date` не рекомендуется, так как в Blazor есть встроенная поддержка форматирования дат.</span><span class="sxs-lookup"><span data-stu-id="c4087-159">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="c4087-160">Если все же для типа поля `date` указывается формат, для правильной работы привязки используйте только формат даты `yyyy-MM-dd`.</span><span class="sxs-lookup"><span data-stu-id="c4087-160">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="c4087-161">Привязка родительского компонента к дочернему с помощью параметров компонентов</span><span class="sxs-lookup"><span data-stu-id="c4087-161">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="c4087-162">Привязка поддерживает параметры компонентов: с помощью атрибута `@bind-{PROPERTY}` можно выполнить привязку значения свойства от родительского компонента к дочернему.</span><span class="sxs-lookup"><span data-stu-id="c4087-162">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="c4087-163">Привязка дочернего компонента к родительскому рассматривается в разделе [Привязка дочернего компонента к родительскому посредством цепочки привязки](#child-to-parent-binding-with-chained-bind).</span><span class="sxs-lookup"><span data-stu-id="c4087-163">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="c4087-164">Следующий дочерний компонент (`ChildComponent`) имеет параметр компонента `Year` и обратный вызов `YearChanged`:</span><span class="sxs-lookup"><span data-stu-id="c4087-164">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="c4087-165">Назначение структуры <xref:Microsoft.AspNetCore.Components.EventCallback%601> описывается в разделе <xref:blazor/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="c4087-165"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="c4087-166">Рассмотрим представленный ниже родительский компонент.</span><span class="sxs-lookup"><span data-stu-id="c4087-166">The following parent component uses:</span></span>

* <span data-ttu-id="c4087-167">Он использует `ChildComponent`, а его параметр `ParentYear` привязывается к параметру `Year` дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="c4087-167">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="c4087-168">Событие `onclick` используется для активации метода `ChangeTheYear`.</span><span class="sxs-lookup"><span data-stu-id="c4087-168">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="c4087-169">Для получения дополнительной информации см. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c4087-169">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="c4087-170">При загрузке `ParentComponent` создается следующая разметка:</span><span class="sxs-lookup"><span data-stu-id="c4087-170">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="c4087-171">Если значение свойства `ParentYear` изменяется в результате нажатия кнопки в `ParentComponent`, свойство `Year` компонента `ChildComponent` обновляется.</span><span class="sxs-lookup"><span data-stu-id="c4087-171">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="c4087-172">Новое значение свойства `Year` отображается в пользовательском интерфейсе при повторной отрисовке компонента `ParentComponent`:</span><span class="sxs-lookup"><span data-stu-id="c4087-172">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="c4087-173">Параметр `Year` допускает привязку, так как он имеет сопутствующее событие `YearChanged`, соответствующее типу параметра `Year`.</span><span class="sxs-lookup"><span data-stu-id="c4087-173">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="c4087-174">В соответствии с соглашением `<ChildComponent @bind-Year="ParentYear" />` фактически эквивалентно следующему коду:</span><span class="sxs-lookup"><span data-stu-id="c4087-174">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="c4087-175">Как правило, свойство можно привязать к соответствующему обработчику событий, включив атрибут `@bind-{PROPRETY}:event`.</span><span class="sxs-lookup"><span data-stu-id="c4087-175">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="c4087-176">Например, свойство `MyProp` можно привязать к `MyEventHandler` с помощью следующих двух атрибутов:</span><span class="sxs-lookup"><span data-stu-id="c4087-176">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="c4087-177">Привязка дочернего компонента к родительскому посредством цепочки привязки</span><span class="sxs-lookup"><span data-stu-id="c4087-177">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="c4087-178">Распространенным сценарием является связывание привязанного к данным параметра к элементу страницы в выходных данных компонента.</span><span class="sxs-lookup"><span data-stu-id="c4087-178">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="c4087-179">Такой сценарий называется *цепочкой привязки*, так как одновременно имеется несколько уровней привязки.</span><span class="sxs-lookup"><span data-stu-id="c4087-179">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="c4087-180">Цепочку привязки нельзя реализовать с помощью синтаксиса [`@bind`](xref:mvc/views/razor#bind) в элементе страницы.</span><span class="sxs-lookup"><span data-stu-id="c4087-180">A chained bind can't be implemented with [`@bind`](xref:mvc/views/razor#bind) syntax in the page's element.</span></span> <span data-ttu-id="c4087-181">Обработчик событий и значение необходимо указывать отдельно.</span><span class="sxs-lookup"><span data-stu-id="c4087-181">The event handler and value must be specified separately.</span></span> <span data-ttu-id="c4087-182">Но родительский компонент может использовать синтаксис [`@bind`](xref:mvc/views/razor#bind) с параметром компонента.</span><span class="sxs-lookup"><span data-stu-id="c4087-182">A parent component, however, can use [`@bind`](xref:mvc/views/razor#bind) syntax with the component's parameter.</span></span>

<span data-ttu-id="c4087-183">Приведенный ниже компонент `PasswordField` (*PasswordField.razor*) делает следующее:</span><span class="sxs-lookup"><span data-stu-id="c4087-183">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="c4087-184">присваивает элементу `<input>` значение свойства `Password`;</span><span class="sxs-lookup"><span data-stu-id="c4087-184">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="c4087-185">сообщает об изменениях свойства `Password` родительскому компоненту посредством [EventCallback](xref:blazor/event-handling#eventcallback);</span><span class="sxs-lookup"><span data-stu-id="c4087-185">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="c4087-186">использует событие `onclick` для активации метода `ToggleShowPassword`.</span><span class="sxs-lookup"><span data-stu-id="c4087-186">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="c4087-187">Для получения дополнительной информации см. <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="c4087-187">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="c4087-188">Компонент `PasswordField` используется в другом компоненте:</span><span class="sxs-lookup"><span data-stu-id="c4087-188">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="c4087-189">Для проверки пароля или перехвата ошибок в нем в предыдущем примере выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="c4087-189">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="c4087-190">Создайте резервное поле для `Password` (`password` в следующем примере кода).</span><span class="sxs-lookup"><span data-stu-id="c4087-190">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="c4087-191">Выполните проверки или перехватите ошибки в методе задания `Password`.</span><span class="sxs-lookup"><span data-stu-id="c4087-191">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="c4087-192">В следующем примере пользователю немедленно сообщается о том, что в пароле есть пробел:</span><span class="sxs-lookup"><span data-stu-id="c4087-192">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="radio-buttons"></a><span data-ttu-id="c4087-193">Переключатели</span><span class="sxs-lookup"><span data-stu-id="c4087-193">Radio buttons</span></span>

<span data-ttu-id="c4087-194">Сведения о привязке к переключателям в форме см. в разделе <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="c4087-194">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
