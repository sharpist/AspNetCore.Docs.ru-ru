---
title: Библиотеки классов компонентов Razor в ASP.NET Core
author: guardrex
description: Сведения о включении компонентов в приложения Blazor из внешней библиотеки компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 8293d61f88f53e55d94b114ca2143fdfb6fd8468
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819071"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="46b03-103">Библиотеки классов компонентов Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46b03-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="46b03-104">Автор: [Саймон Тиммс](https://github.com/stimms) (Simon Timms)</span><span class="sxs-lookup"><span data-stu-id="46b03-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="46b03-105">Компоненты можно совместно использовать в проектах посредством [библиотеки классов Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="46b03-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="46b03-106">*Библиотека классов компонентов Razor* может включаться из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="46b03-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="46b03-107">другого проекта в решении;</span><span class="sxs-lookup"><span data-stu-id="46b03-107">Another project in the solution.</span></span>
* <span data-ttu-id="46b03-108">пакета NuGet;</span><span class="sxs-lookup"><span data-stu-id="46b03-108">A NuGet package.</span></span>
* <span data-ttu-id="46b03-109">связанной библиотеки .NET.</span><span class="sxs-lookup"><span data-stu-id="46b03-109">A referenced .NET library.</span></span>

<span data-ttu-id="46b03-110">Так же как компоненты являются обычными типами .NET, компоненты, предоставляемые RCL, представляют собой обычные сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="46b03-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="46b03-111">Создание библиотеки RCL</span><span class="sxs-lookup"><span data-stu-id="46b03-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="46b03-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="46b03-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="46b03-113">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="46b03-113">Create a new project.</span></span>
1. <span data-ttu-id="46b03-114">Выберите **Библиотека классов Razor** .</span><span class="sxs-lookup"><span data-stu-id="46b03-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="46b03-115">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="46b03-115">Select **Next**.</span></span>
1. <span data-ttu-id="46b03-116">В диалоговом окне **Создать библиотеку классов Razor** щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="46b03-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="46b03-117">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="46b03-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="46b03-118">В примерах в этой статье используется имя проекта `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="46b03-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="46b03-119">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="46b03-119">Select **Create**.</span></span>
1. <span data-ttu-id="46b03-120">Добавьте библиотеку RCL в решение.</span><span class="sxs-lookup"><span data-stu-id="46b03-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="46b03-121">Щелкните решение правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="46b03-121">Right-click the solution.</span></span> <span data-ttu-id="46b03-122">Выберите **Добавить** > **Существующий проект**.</span><span class="sxs-lookup"><span data-stu-id="46b03-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="46b03-123">Перейдите к файлу проекта RCL.</span><span class="sxs-lookup"><span data-stu-id="46b03-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="46b03-124">Выберите файл проекта RCL (`.csproj`).</span><span class="sxs-lookup"><span data-stu-id="46b03-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="46b03-125">Добавьте ссылку на RCL из приложения.</span><span class="sxs-lookup"><span data-stu-id="46b03-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="46b03-126">Щелкните проект приложения правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="46b03-126">Right-click the app project.</span></span> <span data-ttu-id="46b03-127">Выберите **Добавить** > **Ссылка**.</span><span class="sxs-lookup"><span data-stu-id="46b03-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="46b03-128">Выберите проект RCL.</span><span class="sxs-lookup"><span data-stu-id="46b03-128">Select the RCL project.</span></span> <span data-ttu-id="46b03-129">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="46b03-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="46b03-130">Если при создании библиотеки RCL на основе шаблона был установлен флажок **Представления и страницы поддержки**, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="46b03-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="46b03-131">Вручную добавьте файл в корневой каталог созданного проекта.</span><span class="sxs-lookup"><span data-stu-id="46b03-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="46b03-132">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="46b03-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="46b03-133">Используйте шаблон **Библиотека классов Razor** (`razorclasslib`) с командой [`dotnet new`](/dotnet/core/tools/dotnet-new) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="46b03-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="46b03-134">В приведенном ниже примере создается библиотека RCL с именем `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="46b03-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="46b03-135">Папка с `ComponentLibrary` создается автоматически при выполнении команды.</span><span class="sxs-lookup"><span data-stu-id="46b03-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="46b03-136">Если при создании библиотеки RCL на основе шаблона используется параметр `-s|--support-pages-and-views`, также добавьте в корневой каталог созданного проекта файл `_Imports.razor` со следующим содержимым, чтобы включить разработку компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="46b03-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="46b03-137">Вручную добавьте файл в корневой каталог созданного проекта.</span><span class="sxs-lookup"><span data-stu-id="46b03-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="46b03-138">Чтобы добавить библиотеку в существующий проект, используйте команду [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="46b03-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="46b03-139">В приведенном ниже примере в приложение добавляется библиотека RCL.</span><span class="sxs-lookup"><span data-stu-id="46b03-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="46b03-140">Выполните следующую команду из папки проекта приложения, указав путь к библиотеке:</span><span class="sxs-lookup"><span data-stu-id="46b03-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="46b03-141">Использование компонента из библиотеки</span><span class="sxs-lookup"><span data-stu-id="46b03-141">Consume a library component</span></span>

<span data-ttu-id="46b03-142">Чтобы использовать компоненты, определенные в библиотеке в другом проекте, используйте один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="46b03-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="46b03-143">Используйте полное имя типа с пространством имен.</span><span class="sxs-lookup"><span data-stu-id="46b03-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="46b03-144">Используйте директиву [`@using`](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="46b03-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="46b03-145">Отдельные компоненты можно добавлять по имени.</span><span class="sxs-lookup"><span data-stu-id="46b03-145">Individual components can be added by name.</span></span>

<span data-ttu-id="46b03-146">В приведенном ниже примере `ComponentLibrary` — это библиотека компонентов, содержащая компонент `Component1` (`Component1.razor`).</span><span class="sxs-lookup"><span data-stu-id="46b03-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="46b03-147">Компонент `Component1` — это пример компонента, автоматически добавляемого шаблоном проекта RCL при создании библиотеки.</span><span class="sxs-lookup"><span data-stu-id="46b03-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="46b03-148">Сослаться на компонент `Component1`, используя его пространство имен, можно так:</span><span class="sxs-lookup"><span data-stu-id="46b03-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="46b03-149">Кроме того, можно перенести библиотеку в область с директивой [`@using`](xref:mvc/views/razor#using) и использовать компонент без его пространства имен:</span><span class="sxs-lookup"><span data-stu-id="46b03-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="46b03-150">Кроме того, чтобы сделать компоненты библиотеки доступными для всего проекта, включите директиву `@using ComponentLibrary` в файл `_Import.razor` верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="46b03-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="46b03-151">Чтобы применить пространство имен к одному компоненту или набору компонентов в папке, добавьте директиву в файл `_Import.razor` на любом уровне.</span><span class="sxs-lookup"><span data-stu-id="46b03-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="46b03-152">Чтобы предоставить компоненту класс CSS `my-component` из `Component1`, свяжите его с таблицей стилей библиотеки с помощью компонента [`Link` платформы](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) в `Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="46b03-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="46b03-153">Чтобы предоставить таблицу стилей для приложения, можно также привязать таблицу стилей библиотеки в файле `wwwroot/index.html` приложения (Blazor WebAssembly) или в файле `Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="46b03-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="46b03-154">Если компонент `Link` используется в дочернем компоненте, связанный ресурс также доступен для любого другого дочернего компонента родительского компонента при преобразовании для просмотра дочернего элемента с компонентом `Link`.</span><span class="sxs-lookup"><span data-stu-id="46b03-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="46b03-155">Различие между использованием компонента `Link` в дочернем компоненте и помещение HTML-тега `<link>` в `wwwroot/index.html` или `Pages/_Host.cshtml` заключается в том, что отрисованный HTML-компонент платформы выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="46b03-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="46b03-156">Может быть изменен состоянием приложения.</span><span class="sxs-lookup"><span data-stu-id="46b03-156">Can be modified by application state.</span></span> <span data-ttu-id="46b03-157">Жестко заданный HTML-тег `<link>` не может быть изменен состоянием приложения.</span><span class="sxs-lookup"><span data-stu-id="46b03-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="46b03-158">Удаляется из HTML `<head>`, когда родительский компонент больше не отрисовывается.</span><span class="sxs-lookup"><span data-stu-id="46b03-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="46b03-159">Чтобы предоставить класс CSS `Component1` из `my-component`, свяжите его с таблицей стилей в файле `wwwroot/index.html` приложения (Blazor WebAssembly) или в файле `Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="46b03-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="46b03-160">Создание библиотеки классов компонентов Razor со статическими ресурсами</span><span class="sxs-lookup"><span data-stu-id="46b03-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="46b03-161">Библиотека RCL может включать в себя статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="46b03-161">An RCL can include static assets.</span></span> <span data-ttu-id="46b03-162">Такие ресурсы доступны любому приложению, использующему библиотеку.</span><span class="sxs-lookup"><span data-stu-id="46b03-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="46b03-163">Для получения дополнительной информации см. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="46b03-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="46b03-164">Предоставление компонентов и статических ресурсов нескольким размещенным приложениям Blazor</span><span class="sxs-lookup"><span data-stu-id="46b03-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="46b03-165">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="46b03-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="46b03-166">Сборка, упаковка и отправка в NuGet</span><span class="sxs-lookup"><span data-stu-id="46b03-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="46b03-167">Так как библиотеки компонентов являются стандартными библиотеками .NET, их упаковка и передача в NuGet не отличается от упаковки и передачи любых других библиотек.</span><span class="sxs-lookup"><span data-stu-id="46b03-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="46b03-168">Упаковка выполняется с помощью команды [`dotnet pack`](/dotnet/core/tools/dotnet-pack) в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="46b03-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="46b03-169">Чтобы отправить пакет в NuGet, используйте команду [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="46b03-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46b03-170">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="46b03-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="46b03-171">Добавление файла конфигурации компоновщика XML в библиотеку</span><span class="sxs-lookup"><span data-stu-id="46b03-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
