---
title: Изоляция CSS в ASP.NET Core Blazor
author: daveabrock
description: Узнайте, как изоляция CSS позволяет ограничить область применения CSS к компонентам, что позволяет упростить CSS и избежать конфликтов с другими компонентами или библиотеками.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
no-loc:
- appsettings.json
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 92545eab4004f6b67080f79d64b94bb424d5a102
ms.sourcegitcommit: 43a540e703b9096921de27abc6b66bc0783fe905
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320087"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="38205-103">Изоляция CSS в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="38205-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="38205-104">Автор: [Дейв Брок](https://twitter.com/daveabrock) (Dave Brock)</span><span class="sxs-lookup"><span data-stu-id="38205-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="38205-105">Изоляция CSS упрощает использование CSS в приложении, предотвращая зависимости от глобальных стилей, и помогает избежать конфликтов стилей между компонентами и библиотеками.</span><span class="sxs-lookup"><span data-stu-id="38205-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="38205-106">Включение изоляции CSS</span><span class="sxs-lookup"><span data-stu-id="38205-106">Enable CSS isolation</span></span> 

<span data-ttu-id="38205-107">Чтобы определить стили, относящиеся к компоненту, создайте файл `.razor.css`, соответствующий имени файла `.razor` для компонента.</span><span class="sxs-lookup"><span data-stu-id="38205-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="38205-108">Этот файл `.razor.css` будет *файлом CSS с областью действия*.</span><span class="sxs-lookup"><span data-stu-id="38205-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="38205-109">Для компонента `MyComponent`, имеющего файл `MyComponent.razor`, создайте файл с именем `MyComponent.razor.css` рядом с компонентом.</span><span class="sxs-lookup"><span data-stu-id="38205-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="38205-110">Часть `MyComponent` в имени файла `.razor.css` **не** учитывает регистр.</span><span class="sxs-lookup"><span data-stu-id="38205-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="38205-111">Например, чтобы добавить изоляцию CSS в компонент `Counter` в шаблоне по умолчанию проекта Blazor, добавьте новый файл с именем `Counter.razor.css` в место расположения файла `Counter.razor`, а затем добавьте следующий код CSS:</span><span class="sxs-lookup"><span data-stu-id="38205-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="38205-112">Стили, определенные в файле `Counter.razor.css`, применяются только к отображаемым выходным данным компонента `Counter`.</span><span class="sxs-lookup"><span data-stu-id="38205-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="38205-113">Все объявления CSS `h1`, определенные в других местах приложения, не будут конфликтовать со стилями `Counter`.</span><span class="sxs-lookup"><span data-stu-id="38205-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="38205-114">Чтобы обеспечить изоляцию стиля при возникновении объединений, блоки Razor `@import` не поддерживаются в файлах CSS с заданной областью.</span><span class="sxs-lookup"><span data-stu-id="38205-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="38205-115">Объединение изоляций CSS</span><span class="sxs-lookup"><span data-stu-id="38205-115">CSS isolation bundling</span></span>

<span data-ttu-id="38205-116">Изоляция CSS выполняется во время сборки.</span><span class="sxs-lookup"><span data-stu-id="38205-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="38205-117">Во время этого процесса Blazor переписывает селекторы CSS в соответствии с разметкой, отображаемой компонентом.</span><span class="sxs-lookup"><span data-stu-id="38205-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="38205-118">Эти переписанные стили CSS объединяются и создаются как статические ресурсы в `{PROJECT NAME}.styles.css`, где заполнитель `{PROJECT NAME}` является названием пакета или продукта, на который указывает ссылка.</span><span class="sxs-lookup"><span data-stu-id="38205-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="38205-119">По умолчанию эти статические файлы ищутся от корневого пути приложения.</span><span class="sxs-lookup"><span data-stu-id="38205-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="38205-120">В приложении ссылайтесь на объединенный файл, проверив ссылку внутри тега `<head>` созданного HTML:</span><span class="sxs-lookup"><span data-stu-id="38205-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="38205-121">В объединенном файле каждый компонент связан с идентификатором области.</span><span class="sxs-lookup"><span data-stu-id="38205-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="38205-122">Для каждого компонента, имеющего стиль, атрибут HTML добавляется в формате `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="38205-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="38205-123">Идентификатор уникален и отличается для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="38205-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="38205-124">В отображаемом компоненте `Counter` Blazor добавляет идентификатор области к элементу `h1`:</span><span class="sxs-lookup"><span data-stu-id="38205-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="38205-125">Файл `MyProjectName.styles.css` использует идентификатор области для группирования объявления стиля с его компонентом.</span><span class="sxs-lookup"><span data-stu-id="38205-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="38205-126">В следующем примере представлен стиль для предыдущего элемента `<h1>`:</span><span class="sxs-lookup"><span data-stu-id="38205-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="38205-127">Во время сборки создается пакет проектов с использованием соглашения `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, где заполнитель `{STATIC WEB ASSETS BASE PATH}` является базовым путем к статическим веб-ресурсам.</span><span class="sxs-lookup"><span data-stu-id="38205-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="38205-128">Если используются другие проекты, такие как пакеты NuGet или [библиотеки классов Razor](xref:blazor/components/class-libraries), то объединенный файл:</span><span class="sxs-lookup"><span data-stu-id="38205-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="38205-129">ссылается на стили с помощью импорта CSS;</span><span class="sxs-lookup"><span data-stu-id="38205-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="38205-130">не публикуется как статический веб-ресурс приложения, использующего стили.</span><span class="sxs-lookup"><span data-stu-id="38205-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="38205-131">Поддержка дочерних компонентов</span><span class="sxs-lookup"><span data-stu-id="38205-131">Child component support</span></span>

<span data-ttu-id="38205-132">По умолчанию изоляция CSS применяется только к компоненту, привязанному с помощью формата `{COMPONENT NAME}.razor.css`, где заполнитель `{COMPONENT NAME}` обычно является именем компонента.</span><span class="sxs-lookup"><span data-stu-id="38205-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="38205-133">Чтобы применить изменения к дочернему компоненту, используйте объединение `::deep` для всех элементов-потомков в файле `.razor.css` родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="38205-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="38205-134">Объединение `::deep` выбирает элементы, которые являются *потомками* идентификатора области, созданного элементом.</span><span class="sxs-lookup"><span data-stu-id="38205-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="38205-135">В следующем примере показан родительский компонент с именем `Parent` и дочерний компонент с именем `Child`.</span><span class="sxs-lookup"><span data-stu-id="38205-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="38205-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="38205-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="38205-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="38205-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="38205-138">Измените объявление `h1` в `Parent.razor.css`, добавив объединение `::deep` для указания того, что объявление стиля `h1` должно применяться к родительскому компоненту и его дочерним элементам:</span><span class="sxs-lookup"><span data-stu-id="38205-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="38205-139">Стиль `h1` теперь применяется к компонентам `Parent` и `Child` без необходимости создания отдельного CSS-файла с областью действия для дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="38205-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="38205-140">Объединение `::deep` работает только с элементами-потомками.</span><span class="sxs-lookup"><span data-stu-id="38205-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="38205-141">Следующая структура HTML применяет стили `h1` к компонентам, как и ожидалось:</span><span class="sxs-lookup"><span data-stu-id="38205-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="38205-142">В этом сценарии ASP.NET Core применяет идентификатор области родительского компонента к элементу `div`, поэтому браузеру известно, что он наследует стили от родительского компонента.</span><span class="sxs-lookup"><span data-stu-id="38205-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="38205-143">Однако исключение элемента `div` удаляет отношение потомков, и стиль **не** применяется к дочернему компоненту:</span><span class="sxs-lookup"><span data-stu-id="38205-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="38205-144">Поддержка препроцессоров CSS</span><span class="sxs-lookup"><span data-stu-id="38205-144">CSS preprocessor support</span></span>

<span data-ttu-id="38205-145">Препроцессоры CSS полезны для улучшения разработки CSS с помощью таких функций, как переменные, вложенность, модули, примеси и наследование.</span><span class="sxs-lookup"><span data-stu-id="38205-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="38205-146">Хотя изоляция CSS изначально не поддерживает препроцессоры CSS, такие как Sass и Less, интеграция препроцессоров CSS происходит прозрачно, поскольку компиляция препроцессора выполняется до того, как Blazor перезаписывает селекторы CSS в процессе сборки.</span><span class="sxs-lookup"><span data-stu-id="38205-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="38205-147">С помощью Visual Studio, например, настройте существующую компиляцию препроцессора в качестве задачи **перед сборкой** в диспетчере выполнения задач Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="38205-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="38205-148">Многие сторонние пакеты NuGet, такие как [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), могут компилировать файлы SASS и SCSS в начале процесса сборки перед выполнением изоляции CSS, и дополнительная настройка не требуется.</span><span class="sxs-lookup"><span data-stu-id="38205-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="38205-149">Конфигурация изоляции CSS</span><span class="sxs-lookup"><span data-stu-id="38205-149">CSS isolation configuration</span></span>

<span data-ttu-id="38205-150">Изоляция CSS может работать без дополнительных настроек, но она предоставляет возможности конфигурации для некоторых сложных сценариев, например при наличии зависимостей от существующих инструментов или рабочих процессов.</span><span class="sxs-lookup"><span data-stu-id="38205-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="38205-151">Настройка формата идентификатора области</span><span class="sxs-lookup"><span data-stu-id="38205-151">Customize scope identifier format</span></span>

<span data-ttu-id="38205-152">По умолчанию для идентификаторов областей используется формат `b-<10-character-string>`.</span><span class="sxs-lookup"><span data-stu-id="38205-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="38205-153">Чтобы настроить формат идентификатора области, измените шаблон в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="38205-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="38205-154">В предыдущем примере CSS, сформированный для `MyComponent.Razor.css`, изменяет свой идентификатор области с `b-<10-character-string>` на `my-custom-scope-identifier`.</span><span class="sxs-lookup"><span data-stu-id="38205-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="38205-155">Изменение базового пути для статических веб-ресурсов</span><span class="sxs-lookup"><span data-stu-id="38205-155">Change base path for static web assets</span></span>

<span data-ttu-id="38205-156">Файл `scoped.styles.css` создается в корне приложения.</span><span class="sxs-lookup"><span data-stu-id="38205-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="38205-157">Чтобы изменить путь по умолчанию, используйте свойство `StaticWebAssetBasePath` в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="38205-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="38205-158">В следующем примере файл `scoped.styles.css` и остальные ресурсы приложения размещаются по пути `_content`:</span><span class="sxs-lookup"><span data-stu-id="38205-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="38205-159">Отключение автоматического объединения</span><span class="sxs-lookup"><span data-stu-id="38205-159">Disable automatic bundling</span></span>

<span data-ttu-id="38205-160">Чтобы отказаться от того, как Blazor публикует и загружает файлы с заданной областью во время выполнения, используйте свойство `DisableScopedCssBundling`.</span><span class="sxs-lookup"><span data-stu-id="38205-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="38205-161">При использовании этого свойства за получение изолированных файлов CSS из каталога `obj` и их публикацию и загрузку во время выполнения отвечают другие средства или процессы:</span><span class="sxs-lookup"><span data-stu-id="38205-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="38205-162">Поддержка библиотеки классов Razor (RCL)</span><span class="sxs-lookup"><span data-stu-id="38205-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="38205-163">Когда [библиотека классов Razor (RCL)](xref:razor-pages/ui-class) предоставляет изолированные стили, атрибут `href` тега `<link>` указывает на `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css` со следующими заполнителями:</span><span class="sxs-lookup"><span data-stu-id="38205-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="38205-164">`{STATIC WEB ASSET BASE PATH}`: базовый путь к статическому веб-ресурсу.</span><span class="sxs-lookup"><span data-stu-id="38205-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="38205-165">`{ASSEMBLY NAME}`: имя сборки библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="38205-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="38205-166">В следующем примере:</span><span class="sxs-lookup"><span data-stu-id="38205-166">In the following example:</span></span>

* <span data-ttu-id="38205-167">Базовый путь к статическому веб-ресурсу — `_content/ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="38205-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="38205-168">Имя сборки библиотеки классов — `ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="38205-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="38205-169">Дополнительные сведения о RCL и библиотеках компонентов см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="38205-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="38205-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="38205-170"><xref:blazor/components/class-libraries>.</span></span>
