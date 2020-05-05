---
title: Вспомогательные компоненты тегов в ASP.NET Core
author: scottaddie
description: Сведения о вспомогательных компонентах тегов и его использовании в ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: df118cdc8346b99e4e5c60c9f0441c963543f4b4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767516"
---
# <a name="tag-helper-components-in-aspnet-core"></a><span data-ttu-id="3423a-103">Вспомогательные компоненты тегов в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3423a-103">Tag Helper Components in ASP.NET Core</span></span>

<span data-ttu-id="3423a-104">Авторы: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie) и [Хасан Фияз Бин](https://github.com/fiyazbinhasan) (Hasan Fiyaz Bin)</span><span class="sxs-lookup"><span data-stu-id="3423a-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)</span></span>

<span data-ttu-id="3423a-105">Вспомогательный компонент тегов позволяет на основе условий изменять или добавлять элементы HTML из серверного кода.</span><span class="sxs-lookup"><span data-stu-id="3423a-105">A Tag Helper Component is a Tag Helper that allows you to conditionally modify or add HTML elements from server-side code.</span></span> <span data-ttu-id="3423a-106">Эта функция доступна в ASP.NET Core 2.0 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="3423a-106">This feature is available in ASP.NET Core 2.0 or later.</span></span>

<span data-ttu-id="3423a-107">ASP.NET Core включает в себя два встроенных вспомогательных компонента тегов: `head` и `body`.</span><span class="sxs-lookup"><span data-stu-id="3423a-107">ASP.NET Core includes two built-in Tag Helper Components: `head` and `body`.</span></span> <span data-ttu-id="3423a-108">Они находятся в <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> пространстве имен и могут использоваться как в MVC, так и Razor на страницах.</span><span class="sxs-lookup"><span data-stu-id="3423a-108">They're located in the <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> namespace and can be used in both MVC and Razor Pages.</span></span> <span data-ttu-id="3423a-109">Вспомогательные компоненты тегов не нужно регистрировать в *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3423a-109">Tag Helper Components don't require registration with the app in *_ViewImports.cshtml*.</span></span>

<span data-ttu-id="3423a-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3423a-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="use-cases"></a><span data-ttu-id="3423a-111">Варианты использования</span><span class="sxs-lookup"><span data-stu-id="3423a-111">Use cases</span></span>

<span data-ttu-id="3423a-112">Два распространенных варианта для использования вспомогательных компонентов тегов:</span><span class="sxs-lookup"><span data-stu-id="3423a-112">Two common use cases of Tag Helper Components include:</span></span>

1. [<span data-ttu-id="3423a-113">Внедрение `<link>` в `<head>`.</span><span class="sxs-lookup"><span data-stu-id="3423a-113">Injecting a `<link>` into the `<head>`.</span></span>](#inject-into-html-head-element)
1. [<span data-ttu-id="3423a-114">Внедрение `<script>` в `<body>`.</span><span class="sxs-lookup"><span data-stu-id="3423a-114">Injecting a `<script>` into the `<body>`.</span></span>](#inject-into-html-body-element)

<span data-ttu-id="3423a-115">Эти варианты использования описаны в следующих разделах.</span><span class="sxs-lookup"><span data-stu-id="3423a-115">The following sections describe these use cases.</span></span>

### <a name="inject-into-html-head-element"></a><span data-ttu-id="3423a-116">Внедрение в элемент HTML "head"</span><span class="sxs-lookup"><span data-stu-id="3423a-116">Inject into HTML head element</span></span>

<span data-ttu-id="3423a-117">Внутри элемента HTML `<head>` файлы CSS обычно импортируются с помощью элемента HTML `<link>`.</span><span class="sxs-lookup"><span data-stu-id="3423a-117">Inside the HTML `<head>` element, CSS files are commonly imported with the HTML `<link>` element.</span></span> <span data-ttu-id="3423a-118">Следующий код позволяет вставить элемент `<link>` в элемент `<head>` с помощью вспомогательного компонента тегов `head`:</span><span class="sxs-lookup"><span data-stu-id="3423a-118">The following code injects a `<link>` element into the `<head>` element using the `head` Tag Helper Component:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

<span data-ttu-id="3423a-119">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="3423a-119">In the preceding code:</span></span>

* <span data-ttu-id="3423a-120">Объект `AddressStyleTagHelperComponent` реализует интерфейс <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span><span class="sxs-lookup"><span data-stu-id="3423a-120">`AddressStyleTagHelperComponent` implements <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>.</span></span> <span data-ttu-id="3423a-121">Абстракция:</span><span class="sxs-lookup"><span data-stu-id="3423a-121">The abstraction:</span></span>
  * <span data-ttu-id="3423a-122">Позволяет инициализировать класс с <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span><span class="sxs-lookup"><span data-stu-id="3423a-122">Allows initialization of the class with a <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.</span></span>
  * <span data-ttu-id="3423a-123">Позволяет использовать вспомогательные компоненты тегов для добавления или изменения элементов HTML.</span><span class="sxs-lookup"><span data-stu-id="3423a-123">Enables the use of Tag Helper Components to add or modify HTML elements.</span></span>
* <span data-ttu-id="3423a-124">Свойство <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> определяет порядок отображения компонентов.</span><span class="sxs-lookup"><span data-stu-id="3423a-124">The <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> property defines the order in which the Components are rendered.</span></span> <span data-ttu-id="3423a-125">Свойство `Order` необходимо, если вспомогательные компоненты тегов используются в приложении несколько раз.</span><span class="sxs-lookup"><span data-stu-id="3423a-125">`Order` is necessary when there are multiple usages of Tag Helper Components in an app.</span></span>
* <span data-ttu-id="3423a-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> сравнивает свойство <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> контекста выполнения со значением `head`.</span><span class="sxs-lookup"><span data-stu-id="3423a-126"><xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> compares the execution context's <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> property value to `head`.</span></span> <span data-ttu-id="3423a-127">Если сравнение возвращает результат TRUE, содержимое поля `_style` внедряется в элемент HTML `<head>`.</span><span class="sxs-lookup"><span data-stu-id="3423a-127">If the comparison evaluates to true, the content of the `_style` field is injected into the HTML `<head>` element.</span></span>

### <a name="inject-into-html-body-element"></a><span data-ttu-id="3423a-128">Внедрение в элемент HTML "body"</span><span class="sxs-lookup"><span data-stu-id="3423a-128">Inject into HTML body element</span></span>

<span data-ttu-id="3423a-129">Вспомогательный компонент тэгов `body` позволяет внедрять элемент `<script>` в элемент `<body>`.</span><span class="sxs-lookup"><span data-stu-id="3423a-129">The `body` Tag Helper Component can inject a `<script>` element into the `<body>` element.</span></span> <span data-ttu-id="3423a-130">Этот метод демонстрируется в приведенном ниже коде.</span><span class="sxs-lookup"><span data-stu-id="3423a-130">The following code demonstrates this technique:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

<span data-ttu-id="3423a-131">Для хранения элемента `<script>` используется отдельный HTML-файл.</span><span class="sxs-lookup"><span data-stu-id="3423a-131">A separate HTML file is used to store the `<script>` element.</span></span> <span data-ttu-id="3423a-132">Этот HTML-файл делает код более чистым и удобным в обслуживании.</span><span class="sxs-lookup"><span data-stu-id="3423a-132">The HTML file makes the code cleaner and more maintainable.</span></span> <span data-ttu-id="3423a-133">С помощью приведенного выше кода считывается содержимое *TagHelpers/Templates/AddressToolTipScript.html* и к нему присоединяются выходные данные вспомогательного компонента тегов.</span><span class="sxs-lookup"><span data-stu-id="3423a-133">The preceding code reads the contents of *TagHelpers/Templates/AddressToolTipScript.html* and appends it with the Tag Helper output.</span></span> <span data-ttu-id="3423a-134">Файл *AddressToolTipScript.html* содержит следующую разметку:</span><span class="sxs-lookup"><span data-stu-id="3423a-134">The *AddressToolTipScript.html* file includes the following markup:</span></span>

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

<span data-ttu-id="3423a-135">Приведенный выше код привязывает [мини-приложение подсказки начальной загрузки](https://getbootstrap.com/docs/3.3/javascript/#tooltips) к любому элементу `<address>`, который содержит атрибут `printable`.</span><span class="sxs-lookup"><span data-stu-id="3423a-135">The preceding code binds a [Bootstrap tooltip widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) to any `<address>` element that includes a `printable` attribute.</span></span> <span data-ttu-id="3423a-136">Результат можно проследить при наведении указателя мыши на соответствующий элемент.</span><span class="sxs-lookup"><span data-stu-id="3423a-136">The effect is visible when a mouse pointer hovers over the element.</span></span>

## <a name="register-a-component"></a><span data-ttu-id="3423a-137">Регистрация компонента</span><span class="sxs-lookup"><span data-stu-id="3423a-137">Register a Component</span></span>

<span data-ttu-id="3423a-138">Вспомогательные компоненты тегов необходимо добавить в коллекцию вспомогательных компонентов тегов приложения.</span><span class="sxs-lookup"><span data-stu-id="3423a-138">A Tag Helper Component must be added to the app's Tag Helper Components collection.</span></span> <span data-ttu-id="3423a-139">Добавить его в коллекцию можно тремя способами:</span><span class="sxs-lookup"><span data-stu-id="3423a-139">There are three ways to add to the collection:</span></span>

* <span data-ttu-id="3423a-140">[регистрация с помощью контейнера служб](#registration-via-services-container);</span><span class="sxs-lookup"><span data-stu-id="3423a-140">[Registration via services container](#registration-via-services-container)</span></span>
* <span data-ttu-id="3423a-141">[Регистрация с Razor помощью файла](#registration-via-razor-file)</span><span class="sxs-lookup"><span data-stu-id="3423a-141">[Registration via Razor file](#registration-via-razor-file)</span></span>
* <span data-ttu-id="3423a-142">[регистрация с помощью модели страницы или контроллера](#registration-via-page-model-or-controller).</span><span class="sxs-lookup"><span data-stu-id="3423a-142">[Registration via Page Model or controller](#registration-via-page-model-or-controller)</span></span>

### <a name="registration-via-services-container"></a><span data-ttu-id="3423a-143">Регистрация с помощью контейнера служб</span><span class="sxs-lookup"><span data-stu-id="3423a-143">Registration via services container</span></span>

<span data-ttu-id="3423a-144">Если <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> не управляет классом вспомогательного компонента тегов, класс нужно зарегистрировать в системе [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3423a-144">If the Tag Helper Component class isn't managed with <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager>, it must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) system.</span></span> <span data-ttu-id="3423a-145">Следующий код `Startup.ConfigureServices` позволяет зарегистрировать классы `AddressStyleTagHelperComponent` и `AddressScriptTagHelperComponent` с [ограниченным временем существования](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span><span class="sxs-lookup"><span data-stu-id="3423a-145">The following `Startup.ConfigureServices` code registers the `AddressStyleTagHelperComponent` and `AddressScriptTagHelperComponent` classes with a [transient lifetime](xref:fundamentals/dependency-injection#lifetime-and-registration-options):</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a><span data-ttu-id="3423a-146">Регистрация с Razor помощью файла</span><span class="sxs-lookup"><span data-stu-id="3423a-146">Registration via Razor file</span></span>

<span data-ttu-id="3423a-147">Если вспомогательный компонент тега не зарегистрирован в режиме внедрения, его можно зарегистрировать на странице Razor страниц или в представлении MVC.</span><span class="sxs-lookup"><span data-stu-id="3423a-147">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page or an MVC view.</span></span> <span data-ttu-id="3423a-148">Этот метод используется для управления внедренной разметкой и порядком выполнения компонента из Razor файла.</span><span class="sxs-lookup"><span data-stu-id="3423a-148">This technique is used for controlling the injected markup and the component execution order from a Razor file.</span></span>

<span data-ttu-id="3423a-149">`ITagHelperComponentManager` позволяет добавлять вспомогательные компоненты тегов и (или) удалять их из приложения.</span><span class="sxs-lookup"><span data-stu-id="3423a-149">`ITagHelperComponentManager` is used to add Tag Helper Components or remove them from the app.</span></span> <span data-ttu-id="3423a-150">Этот метод демонстрируется в следующем коде на примере `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="3423a-150">The following code demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

<span data-ttu-id="3423a-151">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="3423a-151">In the preceding code:</span></span>

* <span data-ttu-id="3423a-152">Директива `@inject` предоставляет экземпляр `ITagHelperComponentManager`.</span><span class="sxs-lookup"><span data-stu-id="3423a-152">The `@inject` directive provides an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="3423a-153">Экземпляр присваивается переменной с именем `manager` для доступа в Razor файле.</span><span class="sxs-lookup"><span data-stu-id="3423a-153">The instance is assigned to a variable named `manager` for access downstream in the Razor file.</span></span>
* <span data-ttu-id="3423a-154">Экземпляр `AddressTagHelperComponent` добавляется в коллекцию вспомогательных компонентов тегов.</span><span class="sxs-lookup"><span data-stu-id="3423a-154">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

<span data-ttu-id="3423a-155">В `AddressTagHelperComponent` вносятся изменения для создания конструктора, который принимает параметры `markup` и `order`:</span><span class="sxs-lookup"><span data-stu-id="3423a-155">`AddressTagHelperComponent` is modified to accommodate a constructor that accepts the `markup` and `order` parameters:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

<span data-ttu-id="3423a-156">Предоставленный параметр `markup` используется в `ProcessAsync` следующим образом:</span><span class="sxs-lookup"><span data-stu-id="3423a-156">The provided `markup` parameter is used in `ProcessAsync` as follows:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a><span data-ttu-id="3423a-157">Регистрация с помощью модели страницы или контроллера</span><span class="sxs-lookup"><span data-stu-id="3423a-157">Registration via Page Model or controller</span></span>

<span data-ttu-id="3423a-158">Если вспомогательный компонент тега не зарегистрирован в ходе внедрения, его можно зарегистрировать на Razor странице страницы или в контроллере MVC.</span><span class="sxs-lookup"><span data-stu-id="3423a-158">If the Tag Helper Component isn't registered with DI, it can be registered from a Razor Pages page model or an MVC controller.</span></span> <span data-ttu-id="3423a-159">Этот метод полезен для отделения логики C# от Razor файлов.</span><span class="sxs-lookup"><span data-stu-id="3423a-159">This technique is useful for separating C# logic from Razor files.</span></span>

<span data-ttu-id="3423a-160">Для доступа к экземпляру `ITagHelperComponentManager` внедряется конструктор.</span><span class="sxs-lookup"><span data-stu-id="3423a-160">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span> <span data-ttu-id="3423a-161">Вспомогательный компонент тегов добавляется в коллекцию вспомогательных компонентов тегов.</span><span class="sxs-lookup"><span data-stu-id="3423a-161">The Tag Helper Component is added to the instance's Tag Helper Components collection.</span></span> <span data-ttu-id="3423a-162">Эта методика показана в следующей Razor модели страниц `AddressTagHelperComponent`:</span><span class="sxs-lookup"><span data-stu-id="3423a-162">The following Razor Pages page model demonstrates this technique with `AddressTagHelperComponent`:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

<span data-ttu-id="3423a-163">В приведенном выше коде:</span><span class="sxs-lookup"><span data-stu-id="3423a-163">In the preceding code:</span></span>

* <span data-ttu-id="3423a-164">Для доступа к экземпляру `ITagHelperComponentManager` внедряется конструктор.</span><span class="sxs-lookup"><span data-stu-id="3423a-164">Constructor injection is used to access an instance of `ITagHelperComponentManager`.</span></span>
* <span data-ttu-id="3423a-165">Экземпляр `AddressTagHelperComponent` добавляется в коллекцию вспомогательных компонентов тегов.</span><span class="sxs-lookup"><span data-stu-id="3423a-165">An instance of `AddressTagHelperComponent` is added to the app's Tag Helper Components collection.</span></span>

## <a name="create-a-component"></a><span data-ttu-id="3423a-166">Создание компонента</span><span class="sxs-lookup"><span data-stu-id="3423a-166">Create a Component</span></span>

<span data-ttu-id="3423a-167">Чтобы создать пользовательский вспомогательный компонент тегов, выполните следующие действия:</span><span class="sxs-lookup"><span data-stu-id="3423a-167">To create a custom Tag Helper Component:</span></span>

* <span data-ttu-id="3423a-168">Создайте открытый класс, который наследует от <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span><span class="sxs-lookup"><span data-stu-id="3423a-168">Create a public class deriving from <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper>.</span></span>
* <span data-ttu-id="3423a-169">Примените [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) к классу атрибут.</span><span class="sxs-lookup"><span data-stu-id="3423a-169">Apply an [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) attribute to the class.</span></span> <span data-ttu-id="3423a-170">Укажите имя целевого элемента HTML.</span><span class="sxs-lookup"><span data-stu-id="3423a-170">Specify the name of the target HTML element.</span></span>
* <span data-ttu-id="3423a-171">*Необязательно*: применение [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) атрибута к классу для подавления отображения типа в IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="3423a-171">*Optional*: Apply an [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) attribute to the class to suppress the type's display in IntelliSense.</span></span>

<span data-ttu-id="3423a-172">Следующий код позволяет создать пользовательский вспомогательный компонент тегов, который работает с элементом HTML `<address>`:</span><span class="sxs-lookup"><span data-stu-id="3423a-172">The following code creates a custom Tag Helper Component that targets the `<address>` HTML element:</span></span>

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

<span data-ttu-id="3423a-173">С помощью настраиваемого вспомогательного компонента тегов `address` внедрите HTML-разметку, как указано ниже:</span><span class="sxs-lookup"><span data-stu-id="3423a-173">Use the custom `address` Tag Helper Component to inject HTML markup as follows:</span></span>

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

<span data-ttu-id="3423a-174">Приведенный выше метод `ProcessAsync` позволяет внедрить предоставленный в <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> HTML-код в соответствующий элемент `<address>`.</span><span class="sxs-lookup"><span data-stu-id="3423a-174">The preceding `ProcessAsync` method injects the HTML provided to <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> into the matching `<address>` element.</span></span> <span data-ttu-id="3423a-175">Внедрение происходит при следующих условиях:</span><span class="sxs-lookup"><span data-stu-id="3423a-175">The injection occurs when:</span></span>

* <span data-ttu-id="3423a-176">Свойство `TagName` контекста выполнения совпадает со значением `address`.</span><span class="sxs-lookup"><span data-stu-id="3423a-176">The execution context's `TagName` property value equals `address`.</span></span>
* <span data-ttu-id="3423a-177">Соответствующий элемент `<address>` имеет атрибут `printable`.</span><span class="sxs-lookup"><span data-stu-id="3423a-177">The corresponding `<address>` element has a `printable` attribute.</span></span>

<span data-ttu-id="3423a-178">Например, инструкция `if` возвращает значение TRUE при обработке следующего элемента `<address>`:</span><span class="sxs-lookup"><span data-stu-id="3423a-178">For example, the `if` statement evaluates to true when processing the following `<address>` element:</span></span>

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a><span data-ttu-id="3423a-179">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3423a-179">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
