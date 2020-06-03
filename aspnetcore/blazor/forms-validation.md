---
<span data-ttu-id="31e1c-101">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-101">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-103">'Blazor'</span></span>
- <span data-ttu-id="31e1c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-104">'Identity'</span></span>
- <span data-ttu-id="31e1c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-106">'Razor'</span></span>
- <span data-ttu-id="31e1c-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="31e1c-108">Формы и проверка ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="31e1c-108">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="31e1c-109">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="31e1c-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="31e1c-110">Формы и проверка поддерживаются в Blazor с помощью [заметок к данным](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="31e1c-110">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="31e1c-111">Следующий тип `ExampleModel` определяет логику проверки с помощью заметок к данным:</span><span class="sxs-lookup"><span data-stu-id="31e1c-111">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="31e1c-112">Форма определяется с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-112">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="31e1c-113">В следующей форме показаны типичные элементы, компоненты и код Razor:</span><span class="sxs-lookup"><span data-stu-id="31e1c-113">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

<span data-ttu-id="31e1c-114">В предшествующем примере:</span><span class="sxs-lookup"><span data-stu-id="31e1c-114">In the preceding example:</span></span>

* <span data-ttu-id="31e1c-115">Форма проверяет введенные пользователем данные в поле `name`, используя проверку, определенную в типе `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-115">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="31e1c-116">Модель создается в блоке `@code` компонента и хранится в частном поле (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="31e1c-116">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="31e1c-117">Поле назначается атрибуту `Model` элемента `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-117">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="31e1c-118">Элемент `@bind-Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText> привязывает:</span><span class="sxs-lookup"><span data-stu-id="31e1c-118">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="31e1c-119">Свойство модели (`exampleModel.Name`) к свойству `Value` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-119">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="31e1c-120">См. сведения о привязке свойств здесь: <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-120">For more information on property binding, see <xref:blazor/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="31e1c-121">Делегат события изменения к свойству `ValueChanged` компонента <xref:Microsoft.AspNetCore.Components.Forms.InputText>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-121">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="31e1c-122">Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным.</span><span class="sxs-lookup"><span data-stu-id="31e1c-122">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="31e1c-123">Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> обобщает сообщения проверки.</span><span class="sxs-lookup"><span data-stu-id="31e1c-123">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="31e1c-124">`HandleValidSubmit` активируется, когда форма успешно отправляется (проходит проверку).</span><span class="sxs-lookup"><span data-stu-id="31e1c-124">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="31e1c-125">Для получения и проверки вводимых пользователем данных доступны наборы встроенных компонентов ввода.</span><span class="sxs-lookup"><span data-stu-id="31e1c-125">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="31e1c-126">Входные данные проверяются при их изменении и отправке формы.</span><span class="sxs-lookup"><span data-stu-id="31e1c-126">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="31e1c-127">В приведенной ниже таблице показаны доступные компоненты ввода.</span><span class="sxs-lookup"><span data-stu-id="31e1c-127">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="31e1c-128">Компонент ввода</span><span class="sxs-lookup"><span data-stu-id="31e1c-128">Input component</span></span> | <span data-ttu-id="31e1c-129">Отображается как &hellip;</span><span class="sxs-lookup"><span data-stu-id="31e1c-129">Rendered as&hellip;</span></span> |
| ---
<span data-ttu-id="31e1c-130">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-130">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-131">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-132">'Blazor'</span></span>
- <span data-ttu-id="31e1c-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-133">'Identity'</span></span>
- <span data-ttu-id="31e1c-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-135">'Razor'</span></span>
- <span data-ttu-id="31e1c-136">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-137">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-137">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-138">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-139">'Blazor'</span></span>
- <span data-ttu-id="31e1c-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-140">'Identity'</span></span>
- <span data-ttu-id="31e1c-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-142">'Razor'</span></span>
- <span data-ttu-id="31e1c-143">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-144">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-144">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-145">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-146">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-146">'Blazor'</span></span>
- <span data-ttu-id="31e1c-147">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-147">'Identity'</span></span>
- <span data-ttu-id="31e1c-148">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-148">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-149">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-149">'Razor'</span></span>
- <span data-ttu-id="31e1c-150">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-150">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-151">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-151">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-152">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-153">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-153">'Blazor'</span></span>
- <span data-ttu-id="31e1c-154">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-154">'Identity'</span></span>
- <span data-ttu-id="31e1c-155">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-155">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-156">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-156">'Razor'</span></span>
- <span data-ttu-id="31e1c-157">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-157">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-158">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-158">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-159">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-160">'Blazor'</span></span>
- <span data-ttu-id="31e1c-161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-161">'Identity'</span></span>
- <span data-ttu-id="31e1c-162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-162">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-163">'Razor'</span></span>
- <span data-ttu-id="31e1c-164">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-164">'SignalR' uid:</span></span> 

<span data-ttu-id="31e1c-165">-------- | --- название: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-165">-------- | --- title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-166">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-167">'Blazor'</span></span>
- <span data-ttu-id="31e1c-168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-168">'Identity'</span></span>
- <span data-ttu-id="31e1c-169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-169">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-170">'Razor'</span></span>
- <span data-ttu-id="31e1c-171">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-172">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-172">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-173">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-174">'Blazor'</span></span>
- <span data-ttu-id="31e1c-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-175">'Identity'</span></span>
- <span data-ttu-id="31e1c-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-177">'Razor'</span></span>
- <span data-ttu-id="31e1c-178">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-179">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-179">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-180">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-181">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-181">'Blazor'</span></span>
- <span data-ttu-id="31e1c-182">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-182">'Identity'</span></span>
- <span data-ttu-id="31e1c-183">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-183">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-184">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-184">'Razor'</span></span>
- <span data-ttu-id="31e1c-185">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-185">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-186">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-186">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-187">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-188">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-188">'Blazor'</span></span>
- <span data-ttu-id="31e1c-189">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-189">'Identity'</span></span>
- <span data-ttu-id="31e1c-190">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-190">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-191">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-191">'Razor'</span></span>
- <span data-ttu-id="31e1c-192">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-192">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-193">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-193">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-194">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-195">'Blazor'</span></span>
- <span data-ttu-id="31e1c-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-196">'Identity'</span></span>
- <span data-ttu-id="31e1c-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-198">'Razor'</span></span>
- <span data-ttu-id="31e1c-199">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-199">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-200">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-200">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-201">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-202">'Blazor'</span></span>
- <span data-ttu-id="31e1c-203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-203">'Identity'</span></span>
- <span data-ttu-id="31e1c-204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-204">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-205">'Razor'</span></span>
- <span data-ttu-id="31e1c-206">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="31e1c-207">title: Формы и проверка ASP.NET Core Blazor автор: описание: Узнайте, как использовать формы и сценарии проверки полей в Blazor.</span><span class="sxs-lookup"><span data-stu-id="31e1c-207">title: 'ASP.NET Core Blazor forms and validation' author: description: 'Learn how to use forms and field validation scenarios in Blazor.'</span></span>
<span data-ttu-id="31e1c-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="31e1c-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="31e1c-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-209">'Blazor'</span></span>
- <span data-ttu-id="31e1c-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="31e1c-210">'Identity'</span></span>
- <span data-ttu-id="31e1c-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="31e1c-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="31e1c-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="31e1c-212">'Razor'</span></span>
- <span data-ttu-id="31e1c-213">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="31e1c-213">'SignalR' uid:</span></span> 

<span data-ttu-id="31e1c-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span><span class="sxs-lookup"><span data-stu-id="31e1c-214">---------- | | <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` | | <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` | | <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |</span></span>

<span data-ttu-id="31e1c-215">Все компоненты ввода, включая <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, поддерживают произвольные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="31e1c-215">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="31e1c-216">В отрисованный HTML-элемент добавляется любой атрибут, который не соответствует параметру компонента.</span><span class="sxs-lookup"><span data-stu-id="31e1c-216">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="31e1c-217">Входные компоненты предоставляют поведение по умолчанию для проверки редактирования и изменения класса CSS в соответствии с состоянием поля.</span><span class="sxs-lookup"><span data-stu-id="31e1c-217">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="31e1c-218">Некоторые компоненты включают в себя полезную логику синтаксического анализа.</span><span class="sxs-lookup"><span data-stu-id="31e1c-218">Some components include useful parsing logic.</span></span> <span data-ttu-id="31e1c-219">Например, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> и <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> обрабатывают не анализируемые значения надлежащим образом, регистрируя их как ошибки проверки.</span><span class="sxs-lookup"><span data-stu-id="31e1c-219">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="31e1c-220">Типы, которые могут принимать значения NULL, также поддерживают допустимость значений NULL для целевого поля (например, `int?`).</span><span class="sxs-lookup"><span data-stu-id="31e1c-220">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="31e1c-221">Следующий тип `Starship` определяет логику проверки, используя более широкий набор свойств и заметок к данным, чем предыдущий `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="31e1c-221">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="31e1c-222">В предыдущем примере `Description` является необязательным, так как заметки к данным отсутствуют.</span><span class="sxs-lookup"><span data-stu-id="31e1c-222">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="31e1c-223">Следующая форма проверяет введенные пользователем данные, используя проверку, определенную в модели `Starship`:</span><span class="sxs-lookup"><span data-stu-id="31e1c-223">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="31e1c-224"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> создает <xref:Microsoft.AspNetCore.Components.Forms.EditContext> в виде [каскадного значения](xref:blazor/components#cascading-values-and-parameters), которое отслеживает метаданные процесса редактирования, включая измененные поля и текущие сообщения проверки.</span><span class="sxs-lookup"><span data-stu-id="31e1c-224">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="31e1c-225"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> также предоставляет удобные события для допустимых и недопустимых отправок (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span><span class="sxs-lookup"><span data-stu-id="31e1c-225">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> also provides convenient events for valid and invalid submits (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>).</span></span> <span data-ttu-id="31e1c-226">Кроме того, можно использовать <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>, чтобы активировать проверку полей значений с помощью пользовательского кода проверки.</span><span class="sxs-lookup"><span data-stu-id="31e1c-226">Alternatively, use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="31e1c-227">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="31e1c-227">In the following example:</span></span>

* <span data-ttu-id="31e1c-228">Метод `HandleSubmit` выполняется при нажатии кнопки **Отправить**.</span><span class="sxs-lookup"><span data-stu-id="31e1c-228">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="31e1c-229">Форма проверяется с помощью <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы.</span><span class="sxs-lookup"><span data-stu-id="31e1c-229">The form is validated using the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>
* <span data-ttu-id="31e1c-230">Затем форма проверяется с помощью передачи <xref:Microsoft.AspNetCore.Components.Forms.EditContext> методу `ServerValidate`, который вызывает конечную точку веб-API на сервере (*не отображается*).</span><span class="sxs-lookup"><span data-stu-id="31e1c-230">The form is further validated by passing the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="31e1c-231">Дополнительный код выполняется в зависимости от результатов проверки на стороне клиента и сервера путем проверки `isValid`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-231">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

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

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="31e1c-232">InputText на основе события ввода</span><span class="sxs-lookup"><span data-stu-id="31e1c-232">InputText based on the input event</span></span>

<span data-ttu-id="31e1c-233">Используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.InputText>, чтобы создать пользовательский компонент, использующий событие `input`, а не событие `change`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-233">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="31e1c-234">Создайте компонент со следующей разметкой и используйте компонент так же, как используется <xref:Microsoft.AspNetCore.Components.Forms.InputText>:</span><span class="sxs-lookup"><span data-stu-id="31e1c-234">Create a component with the following markup, and use the component just as <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="31e1c-235">Работа с переключателями</span><span class="sxs-lookup"><span data-stu-id="31e1c-235">Work with radio buttons</span></span>

<span data-ttu-id="31e1c-236">При работе с переключателями в форме привязка данных обрабатывается иначе, чем другие элементы, так как переключатели оцениваются как группа.</span><span class="sxs-lookup"><span data-stu-id="31e1c-236">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="31e1c-237">Значение каждого переключателя является фиксированным, но значение группы переключателей является значением выбранного переключателя.</span><span class="sxs-lookup"><span data-stu-id="31e1c-237">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="31e1c-238">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="31e1c-238">The following example shows how to:</span></span>

* <span data-ttu-id="31e1c-239">Обработка привязки данных для группы переключателей.</span><span class="sxs-lookup"><span data-stu-id="31e1c-239">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="31e1c-240">Поддержка проверки с помощью пользовательского компонента `InputRadio`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-240">Support validation using a custom `InputRadio` component.</span></span>

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

<span data-ttu-id="31e1c-241">Следующий <xref:Microsoft.AspNetCore.Components.Forms.EditForm> использует предыдущий компонент `InputRadio` для получения и проверки оценки пользователя:</span><span class="sxs-lookup"><span data-stu-id="31e1c-241">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

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

## <a name="validation-support"></a><span data-ttu-id="31e1c-242">Поддержка проверки</span><span class="sxs-lookup"><span data-stu-id="31e1c-242">Validation support</span></span>

<span data-ttu-id="31e1c-243">Компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> привязывает поддержку проверки с помощью заметок к данным к каскадному <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-243">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="31e1c-244">Чтобы включить поддержку проверки с помощью заметок к данным, требуется это явное действие.</span><span class="sxs-lookup"><span data-stu-id="31e1c-244">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="31e1c-245">Чтобы использовать другую систему проверки, а не заметки к данным, замените <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пользовательской реализацией.</span><span class="sxs-lookup"><span data-stu-id="31e1c-245">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="31e1c-246">Реализация ASP.NET Core доступна для проверки в эталонном источнике: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="31e1c-246">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="31e1c-247"> выполняет два типа проверки данных:</span><span class="sxs-lookup"><span data-stu-id="31e1c-247"> performs two types of validation:</span></span>

* <span data-ttu-id="31e1c-248">*Проверка поля* выполняется, когда пользователь выходит за пределы поля.</span><span class="sxs-lookup"><span data-stu-id="31e1c-248">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="31e1c-249">Во время проверки поля компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> связывает все результаты проверки с полем.</span><span class="sxs-lookup"><span data-stu-id="31e1c-249">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="31e1c-250">*Проверка модели* выполняется, когда пользователь отправляет форму.</span><span class="sxs-lookup"><span data-stu-id="31e1c-250">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="31e1c-251">Во время проверки модели компонент <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> пытается определить поле на основе имени члена из результатов проверки.</span><span class="sxs-lookup"><span data-stu-id="31e1c-251">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="31e1c-252">Результаты проверки, не связанные с отдельным элементом, связаны с моделью, а не с полем.</span><span class="sxs-lookup"><span data-stu-id="31e1c-252">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="31e1c-253">Сводка проверки и компоненты сообщений о проверке</span><span class="sxs-lookup"><span data-stu-id="31e1c-253">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="31e1c-254">Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> содержит обзор всех сообщений проверки аналогично [вспомогательному методу тега сводки проверки](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="31e1c-254">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="31e1c-255">Выходные сообщения проверки для конкретной модели с параметром `Model`:</span><span class="sxs-lookup"><span data-stu-id="31e1c-255">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="31e1c-256">Компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> отображает сообщения проверки для определенного поля, которые похожи на [вспомогательный метод тега сообщения проверки](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="31e1c-256">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="31e1c-257">Укажите поле для проверки с помощью атрибута `For` и лямбда-выражения с именем свойства модели:</span><span class="sxs-lookup"><span data-stu-id="31e1c-257">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="31e1c-258">Компоненты <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> и <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> поддерживают произвольные атрибуты.</span><span class="sxs-lookup"><span data-stu-id="31e1c-258">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="31e1c-259">В созданный элемент `<div>` или `<ul>` добавляется любой атрибут, который не соответствует параметру компонента.</span><span class="sxs-lookup"><span data-stu-id="31e1c-259">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="31e1c-260">Пользовательские атрибуты проверки</span><span class="sxs-lookup"><span data-stu-id="31e1c-260">Custom validation attributes</span></span>

<span data-ttu-id="31e1c-261">Чтобы убедиться, что результат проверки правильно связан с полем при использовании [настраиваемого атрибута проверки](xref:mvc/models/validation#custom-attributes), передайте <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> контекста проверки при создании <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="31e1c-261">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="blazor-data-annotations-validation-package"></a><span data-ttu-id="31e1c-262">Пакет проверки заметок к данным в Blazor</span><span class="sxs-lookup"><span data-stu-id="31e1c-262">Blazor data annotations validation package</span></span>

<span data-ttu-id="31e1c-263">[Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) — это пакет, который заполняет пропуски проверки с помощью компонента <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-263">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="31e1c-264">В настоящее время пакет является *экспериментальным*.</span><span class="sxs-lookup"><span data-stu-id="31e1c-264">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="31e1c-265">Атрибут [CompareProperty]</span><span class="sxs-lookup"><span data-stu-id="31e1c-265">[CompareProperty] attribute</span></span>

<span data-ttu-id="31e1c-266"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> плохо работает с компонентом <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>, так как он не связывает результат проверки с конкретным элементом.</span><span class="sxs-lookup"><span data-stu-id="31e1c-266">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="31e1c-267">Это может привести к несогласованному поведению при проверке на уровне полей и при проверке всей модели при отправке.</span><span class="sxs-lookup"><span data-stu-id="31e1c-267">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="31e1c-268">*Экспериментальный пакет* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) содержит дополнительный атрибут проверки `ComparePropertyAttribute`, который обходит эти ограничения.</span><span class="sxs-lookup"><span data-stu-id="31e1c-268">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="31e1c-269">В приложении Blazor объект `[CompareProperty]` является непосредственной заменой атрибута [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute).</span><span class="sxs-lookup"><span data-stu-id="31e1c-269">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="31e1c-270">Вложенные модели, типы коллекций и сложные типы</span><span class="sxs-lookup"><span data-stu-id="31e1c-270">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="31e1c-271"> обеспечивает поддержку проверки входных данных формы с помощью заметок к данным со встроенным <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-271"> provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="31e1c-272">Однако <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> проверяет только свойства верхнего уровня модели, привязанной к форме, которые не являются свойствами типа коллекции или сложного типа.</span><span class="sxs-lookup"><span data-stu-id="31e1c-272">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="31e1c-273">Чтобы проверить весь граф объектов привязанной модели, включая свойства типа коллекции и сложного типа, используйте `ObjectGraphDataAnnotationsValidator`, предоставляемый *экспериментальным* пакетом [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation):</span><span class="sxs-lookup"><span data-stu-id="31e1c-273">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="31e1c-274">Делайте заметки для свойств модели с помощью `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-274">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="31e1c-275">В следующих классах модели класс `ShipDescription` содержит дополнительные заметки к данным для проверки, когда модель привязана к форме:</span><span class="sxs-lookup"><span data-stu-id="31e1c-275">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="31e1c-276">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="31e1c-276">*Starship.cs*:</span></span>

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

<span data-ttu-id="31e1c-277">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="31e1c-277">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="31e1c-278">Предоставление кнопки "Отправить" на основе проверки формы</span><span class="sxs-lookup"><span data-stu-id="31e1c-278">Enable the submit button based on form validation</span></span>

<span data-ttu-id="31e1c-279">Чтобы включить или отключить кнопку "Отправить" на основе проверки формы, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="31e1c-279">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="31e1c-280">Используйте <xref:Microsoft.AspNetCore.Components.Forms.EditContext> формы, чтобы назначить модель при инициализации компонента.</span><span class="sxs-lookup"><span data-stu-id="31e1c-280">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="31e1c-281">Проверьте форму в обратном вызове <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> контекста, чтобы включить и отключить кнопку "Отправить".</span><span class="sxs-lookup"><span data-stu-id="31e1c-281">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="31e1c-282">Отсоедините обработчик событий в методе `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="31e1c-282">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="31e1c-283">Для получения дополнительной информации см. <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-283">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

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

<span data-ttu-id="31e1c-284">В примерах выше установите для параметра `formInvalid` значение `false`, если:</span><span class="sxs-lookup"><span data-stu-id="31e1c-284">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="31e1c-285">Форма предварительно загружена с допустимыми значениями по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="31e1c-285">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="31e1c-286">Вы хотите, чтобы кнопка отправки была включена при загрузке формы.</span><span class="sxs-lookup"><span data-stu-id="31e1c-286">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="31e1c-287">Побочным эффектом предыдущего подхода является то, что компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> заполняется недопустимыми полями после того, как пользователь взаимодействует с одним полем.</span><span class="sxs-lookup"><span data-stu-id="31e1c-287">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="31e1c-288">Этот сценарий можно решить одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="31e1c-288">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="31e1c-289">Не используйте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> в форме.</span><span class="sxs-lookup"><span data-stu-id="31e1c-289">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="31e1c-290">Сделайте компонент <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> видимым при нажатии кнопки "Отправить" (например, в методе `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="31e1c-290">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="31e1c-291">Устранение неполадок</span><span class="sxs-lookup"><span data-stu-id="31e1c-291">Troubleshoot</span></span>

> <span data-ttu-id="31e1c-292">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both (Для EditForm требуется указать параметр Model или EditContext, но не оба).</span><span class="sxs-lookup"><span data-stu-id="31e1c-292">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="31e1c-293">Убедитесь, что <xref:Microsoft.AspNetCore.Components.Forms.EditForm> назначен параметр <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> или <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span><span class="sxs-lookup"><span data-stu-id="31e1c-293">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> or <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="31e1c-294">При назначении <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> форме убедитесь, что создается экземпляр типа модели, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="31e1c-294">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
