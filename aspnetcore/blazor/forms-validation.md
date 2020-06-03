---
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a>Формы и проверка ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

Формы и проверка поддерживаются в Blazor с помощью [заметок к данным](xref:mvc/models/validation).

Следующий тип `ExampleModel` определяет логику проверки с помощью заметок к данным:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Форма определяется с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.EditForm>. В следующей форме показаны типичные элементы, компоненты и код Razor:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

В предшествующем примере:

* Форма проверяет введенные пользователем данные в поле `name`, используя проверку, определенную в типе `ExampleModel`. Модель создается в блоке `@code` компонента и хранится в частном поле (`exampleModel`). Поле назначается атрибуту `Model` элемента `<EditForm>`.
* Элемент `@bind-Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText> привязывает:
  * Свойство модели (`exampleModel.Name`) к свойству `Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>. См. сведения о привязке свойств здесь: <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.
  * Делегат события изменения к свойству `ValueChanged` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>.
* Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным.
* Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> обобщает сообщения проверки.
* `HandleValidSubmit` активируется, когда форма успешно отправляется (проходит проверку).

Для получения и проверки вводимых пользователем данных доступны наборы встроенных компонентов ввода. Входные данные проверяются при их изменении и отправке формы. В приведенной ниже таблице показаны доступные компоненты ввода.

| Компонент ввода | Отображается как &hellip; |
| ---
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-------- | --- название: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

-
title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

Все компоненты ввода, включая <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, поддерживают произвольные атрибуты. В отрисованный HTML-элемент добавляется любой атрибут, который не соответствует параметру компонента.

Входные компоненты предоставляют поведение по умолчанию для проверки редактирования и изменения класса CSS в соответствии с состоянием поля. Некоторые компоненты включают в себя полезную логику синтаксического анализа. Например, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> и <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> обрабатывают не анализируемые значения надлежащим образом, регистрируя их как ошибки проверки. Типы, которые могут принимать значения NULL, также поддерживают допустимость значений NULL для целевого поля (например, `int?`).

Следующий тип `Starship` определяет логику проверки, используя более широкий набор свойств и заметок к данным, чем предыдущий `ExampleModel`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

В предыдущем примере `Description` является необязательным, так как заметки к данным отсутствуют.

Следующая форма проверяет введенные пользователем данные, используя проверку, определенную в модели `Starship`:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> создает <xref:Microsoft.AspNetCore.Components.Forms.EditContext> в виде [каскадного значения](xref:blazor/components#cascading-values-and-parameters), которое отслеживает метаданные процесса редактирования, включая измененные поля и текущие сообщения проверки. <xref:Microsoft.AspNetCore.Components.Forms.EditForm> также предоставляет удобные события для допустимых и недопустимых отправок (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>). Кроме того, можно использовать <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>, чтобы активировать проверку полей значений с помощью пользовательского кода проверки.

В следующем примере:

* Метод `HandleSubmit` выполняется при нажатии кнопки **Отправить**.
* Форма проверяется с помощью <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы.
* Затем форма проверяется с помощью передачи <xref:Microsoft.AspNetCore.Components.Forms.EditContext> методу `ServerValidate`, который вызывает конечную точку веб-API на сервере (*не отображается*).
* Дополнительный код выполняется в зависимости от результатов проверки на стороне клиента и сервера путем проверки `isValid`.

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate() && 
            await ServerValidate(editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a>InputText на основе события ввода

Используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.InputText>, чтобы создать пользовательский компонент, использующий событие `input`, а не событие `change`.

Создайте компонент со следующей разметкой и используйте компонент так же, как используется <xref:Microsoft.AspNetCore.Components.Forms.InputText>:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Работа с переключателями

При работе с переключателями в форме привязка данных обрабатывается иначе, чем другие элементы, так как переключатели оцениваются как группа. Значение каждого переключателя является фиксированным, но значение группы переключателей является значением выбранного переключателя. В приведенном ниже примере показано, как выполнить следующие задачи.

* Обработка привязки данных для группы переключателей.
* Поддержка проверки с помощью пользовательского компонента `InputRadio`.

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

Следующий <xref:Microsoft.AspNetCore.Components.Forms.EditForm> использует предыдущий компонент `InputRadio` для получения и проверки оценки пользователя:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a>Поддержка проверки

Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным к каскадному <xref:Microsoft.AspNetCore.Components.Forms.EditContext>. Чтобы включить поддержку проверки с помощью заметок к данным, требуется это явное действие. Чтобы использовать другую систему проверки, а не заметки к данным, замените <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пользовательской реализацией. Реализация ASP.NET Core доступна для проверки в эталонном источнике: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor выполняет два типа проверки данных:

* *Проверка поля* выполняется, когда пользователь выходит за пределы поля. Во время проверки поля компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> связывает все результаты проверки с полем.
* *Проверка модели* выполняется, когда пользователь отправляет форму. Во время проверки модели компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пытается определить поле на основе имени члена из результатов проверки. Результаты проверки, не связанные с отдельным элементом, связаны с моделью, а не с полем.

### <a name="validation-summary-and-validation-message-components"></a>Сводка проверки и компоненты сообщений о проверке

Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> содержит обзор всех сообщений проверки аналогично [вспомогательному методу тега сводки проверки](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Выходные сообщения проверки для конкретной модели с параметром `Model`:
  
```razor
<ValidationSummary Model="@starship" />
```

Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> отображает сообщения проверки для определенного поля, которые похожи на [вспомогательный метод тега сообщения проверки](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Укажите поле для проверки с помощью атрибута `For` и лямбда-выражения с именем свойства модели:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Компоненты <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> и <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> поддерживают произвольные атрибуты. В созданный элемент `<div>` или `<ul>` добавляется любой атрибут, который не соответствует параметру компонента.

### <a name="custom-validation-attributes"></a>Пользовательские атрибуты проверки

Чтобы убедиться, что результат проверки правильно связан с полем при использовании [настраиваемого атрибута проверки](xref:mvc/models/validation#custom-attributes), передайте <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> контекста проверки при создании <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

### <a name="blazor-data-annotations-validation-package"></a>Пакет проверки заметок к данным в Blazor

[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) — это пакет, который заполняет пропуски проверки с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. В настоящее время пакет является *экспериментальным*.

### <a name="compareproperty-attribute"></a>Атрибут [CompareProperty]

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> плохо работает с компонентом <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>, так как он не связывает результат проверки с конкретным элементом. Это может привести к несогласованному поведению при проверке на уровне полей и при проверке всей модели при отправке. *Экспериментальный пакет* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) содержит дополнительный атрибут проверки `ComparePropertyAttribute`, который обходит эти ограничения. В приложении Blazor объект `[CompareProperty]` является непосредственной заменой атрибута [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).

### <a name="nested-models-collection-types-and-complex-types"></a>Вложенные модели, типы коллекций и сложные типы

Blazor обеспечивает поддержку проверки входных данных формы с помощью заметок к данным со встроенным <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. Однако <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> проверяет только свойства верхнего уровня модели, привязанной к форме, которые не являются свойствами типа коллекции или сложного типа.

Чтобы проверить весь граф объектов привязанной модели, включая свойства типа коллекции и сложного типа, используйте `ObjectGraphDataAnnotationsValidator`, предоставляемый *экспериментальным* пакетом [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Делайте заметки для свойств модели с помощью `[ValidateComplexType]`. В следующих классах модели класс `ShipDescription` содержит дополнительные заметки к данным для проверки, когда модель привязана к форме:

*Starship.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

*ShipDescription.cs*:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a>Предоставление кнопки "Отправить" на основе проверки формы

Чтобы включить или отключить кнопку "Отправить" на основе проверки формы, выполните следующие действия:

* Используйте <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы, чтобы назначить модель при инициализации компонента.
* Проверьте форму в обратном вызове <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> контекста, чтобы включить и отключить кнопку "Отправить".
* Отсоедините обработчик событий в методе `Dispose`. Для получения дополнительной информации см. <xref:blazor/lifecycle#component-disposal-with-idisposable>.

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship();
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

В примерах выше установите для параметра `formInvalid` значение `false`, если:

* Форма предварительно загружена с допустимыми значениями по умолчанию.
* Вы хотите, чтобы кнопка отправки была включена при загрузке формы.

Побочным эффектом предыдущего подхода является то, что компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> заполняется недопустимыми полями после того, как пользователь взаимодействует с одним полем. Этот сценарий можно решить одним из следующих способов:

* Не используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> в форме.
* Сделайте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> видимым при нажатии кнопки "Отправить" (например, в методе `HandleValidSubmit`).

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a>Устранение неполадок

> InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both (Для EditForm требуется указать параметр Model или EditContext, но не оба).

Убедитесь, что <xref:Microsoft.AspNetCore.Components.Forms.EditForm> назначен параметр <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> или <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.

При назначении <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> форме убедитесь, что создается экземпляр типа модели, как показано в следующем примере:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
