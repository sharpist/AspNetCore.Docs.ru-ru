---
title: Библиотеки классов компонентов Razor в ASP.NET Core
author: guardrex
description: Сведения о включении компонентов в приложения Blazor из внешней библиотеки компонентов.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: ecc9873d7f652f27767df98196786d12789518c9
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103472"
---
# <a name="aspnet-core-razor-components-class-libraries"></a><span data-ttu-id="7d116-103">Библиотеки классов компонентов Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7d116-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="7d116-104">Автор: [Саймон Тиммс](https://github.com/stimms) (Simon Timms)</span><span class="sxs-lookup"><span data-stu-id="7d116-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="7d116-105">Компоненты можно совместно использовать в проектах посредством [библиотеки классов Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="7d116-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="7d116-106">*Библиотека классов компонентов Razor* может включаться из следующих источников:</span><span class="sxs-lookup"><span data-stu-id="7d116-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="7d116-107">другого проекта в решении;</span><span class="sxs-lookup"><span data-stu-id="7d116-107">Another project in the solution.</span></span>
* <span data-ttu-id="7d116-108">пакета NuGet;</span><span class="sxs-lookup"><span data-stu-id="7d116-108">A NuGet package.</span></span>
* <span data-ttu-id="7d116-109">связанной библиотеки .NET.</span><span class="sxs-lookup"><span data-stu-id="7d116-109">A referenced .NET library.</span></span>

<span data-ttu-id="7d116-110">Так же как компоненты являются обычными типами .NET, компоненты, предоставляемые RCL, представляют собой обычные сборки .NET.</span><span class="sxs-lookup"><span data-stu-id="7d116-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="7d116-111">Создание библиотеки RCL</span><span class="sxs-lookup"><span data-stu-id="7d116-111">Create an RCL</span></span>

<span data-ttu-id="7d116-112">Следуйте указаниям из статьи <xref:blazor/get-started>, чтобы настроить среду для Blazor.</span><span class="sxs-lookup"><span data-stu-id="7d116-112">Follow the guidance in the <xref:blazor/get-started> article to configure your environment for Blazor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7d116-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7d116-113">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="7d116-114">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="7d116-114">Create a new project.</span></span>
1. <span data-ttu-id="7d116-115">Выберите **Библиотека классов Razor** .</span><span class="sxs-lookup"><span data-stu-id="7d116-115">Select **Razor Class Library**.</span></span> <span data-ttu-id="7d116-116">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="7d116-116">Select **Next**.</span></span>
1. <span data-ttu-id="7d116-117">В диалоговом окне **Создать библиотеку классов Razor** щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7d116-117">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="7d116-118">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="7d116-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="7d116-119">В примерах в этой статье используется имя проекта `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="7d116-119">The examples in this topic use the project name `MyComponentLib1`.</span></span> <span data-ttu-id="7d116-120">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="7d116-120">Select **Create**.</span></span>
1. <span data-ttu-id="7d116-121">Добавьте библиотеку RCL в решение.</span><span class="sxs-lookup"><span data-stu-id="7d116-121">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="7d116-122">Щелкните решение правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="7d116-122">Right-click the solution.</span></span> <span data-ttu-id="7d116-123">Выберите **Добавить** > **Существующий проект**.</span><span class="sxs-lookup"><span data-stu-id="7d116-123">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="7d116-124">Перейдите к файлу проекта RCL.</span><span class="sxs-lookup"><span data-stu-id="7d116-124">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="7d116-125">Выберите файл проекта RCL (*CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="7d116-125">Select the RCL's project file (*.csproj*).</span></span>
1. <span data-ttu-id="7d116-126">Добавьте ссылку на RCL из приложения.</span><span class="sxs-lookup"><span data-stu-id="7d116-126">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="7d116-127">Щелкните проект приложения правой кнопкой мыши.</span><span class="sxs-lookup"><span data-stu-id="7d116-127">Right-click the app project.</span></span> <span data-ttu-id="7d116-128">Выберите **Добавить** > **Ссылка**.</span><span class="sxs-lookup"><span data-stu-id="7d116-128">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="7d116-129">Выберите проект RCL.</span><span class="sxs-lookup"><span data-stu-id="7d116-129">Select the RCL project.</span></span> <span data-ttu-id="7d116-130">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="7d116-130">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="7d116-131">Если при создании библиотеки RCL на основе шаблона был установлен флажок **Представления и страницы поддержки**, также добавьте в корневой каталог созданного проекта файл *_Imports.razor* со следующим содержимым, чтобы включить разработку компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="7d116-131">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="7d116-132">Вручную добавьте файл в корневой каталог созданного проекта.</span><span class="sxs-lookup"><span data-stu-id="7d116-132">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7d116-133">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="7d116-133">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="7d116-134">Используйте шаблон **Библиотека классов Razor** (`razorclasslib`) с командой [dotnet new](/dotnet/core/tools/dotnet-new) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="7d116-134">Use the **Razor Class Library** template (`razorclasslib`) with the [dotnet new](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="7d116-135">В приведенном ниже примере создается библиотека RCL с именем `MyComponentLib1`.</span><span class="sxs-lookup"><span data-stu-id="7d116-135">In the following example, an RCL is created named `MyComponentLib1`.</span></span> <span data-ttu-id="7d116-136">Папка с `MyComponentLib1` создается автоматически при выполнении команды.</span><span class="sxs-lookup"><span data-stu-id="7d116-136">The folder that holds `MyComponentLib1` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > <span data-ttu-id="7d116-137">Если при создании библиотеки RCL на основе шаблона используется параметр `-s|--support-pages-and-views`, также добавьте в корневой каталог созданного проекта файл *_Imports.razor* со следующим содержимым, чтобы включить разработку компонента Razor:</span><span class="sxs-lookup"><span data-stu-id="7d116-137">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an *_Imports.razor* file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="7d116-138">Вручную добавьте файл в корневой каталог созданного проекта.</span><span class="sxs-lookup"><span data-stu-id="7d116-138">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="7d116-139">Чтобы добавить библиотеку в существующий проект, используйте команду [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="7d116-139">To add the library to an existing project, use the [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="7d116-140">В приведенном ниже примере в приложение добавляется библиотека RCL.</span><span class="sxs-lookup"><span data-stu-id="7d116-140">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="7d116-141">Выполните следующую команду из папки проекта приложения, указав путь к библиотеке:</span><span class="sxs-lookup"><span data-stu-id="7d116-141">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="7d116-142">Использование компонента из библиотеки</span><span class="sxs-lookup"><span data-stu-id="7d116-142">Consume a library component</span></span>

<span data-ttu-id="7d116-143">Чтобы использовать компоненты, определенные в библиотеке в другом проекте, используйте один из описанных ниже подходов.</span><span class="sxs-lookup"><span data-stu-id="7d116-143">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="7d116-144">Используйте полное имя типа с пространством имен.</span><span class="sxs-lookup"><span data-stu-id="7d116-144">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="7d116-145">Используйте директиву [`@using`](xref:mvc/views/razor#using) Razor.</span><span class="sxs-lookup"><span data-stu-id="7d116-145">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="7d116-146">Отдельные компоненты можно добавлять по имени.</span><span class="sxs-lookup"><span data-stu-id="7d116-146">Individual components can be added by name.</span></span>

<span data-ttu-id="7d116-147">В приведенном ниже примере `MyComponentLib1` — это библиотека компонентов, содержащая компонент `SalesReport`.</span><span class="sxs-lookup"><span data-stu-id="7d116-147">In the following examples, `MyComponentLib1` is a component library containing a `SalesReport` component.</span></span>

<span data-ttu-id="7d116-148">На компонент `SalesReport` можно ссылаться, используя полное имя типа с пространством имен:</span><span class="sxs-lookup"><span data-stu-id="7d116-148">The `SalesReport` component can be referenced using its full type name with namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

<span data-ttu-id="7d116-149">На компонент также можно ссылаться, если библиотека перенесена в область действия с помощью директивы `@using`:</span><span class="sxs-lookup"><span data-stu-id="7d116-149">The component can also be referenced if the library is brought into scope with an `@using` directive:</span></span>

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

<span data-ttu-id="7d116-150">Чтобы сделать компоненты библиотеки доступными для всего проекта, включите директиву `@using MyComponentLib1` в файл *_Import.razor* верхнего уровня.</span><span class="sxs-lookup"><span data-stu-id="7d116-150">Include the `@using MyComponentLib1` directive in the top-level *_Import.razor* file to make the library's components available to an entire project.</span></span> <span data-ttu-id="7d116-151">Чтобы применить пространство имен к одной странице или набору страниц в папке, добавьте директиву в файл *_Import.razor* на любом уровне.</span><span class="sxs-lookup"><span data-stu-id="7d116-151">Add the directive to an *_Import.razor* file at any level to apply the namespace to a single page or set of pages within a folder.</span></span>

## <a name="create-a-razor-components-class-library-with-static-assets"></a><span data-ttu-id="7d116-152">Создание библиотеки классов компонентов Razor со статическими ресурсами</span><span class="sxs-lookup"><span data-stu-id="7d116-152">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="7d116-153">Библиотека RCL может включать в себя статические ресурсы.</span><span class="sxs-lookup"><span data-stu-id="7d116-153">An RCL can include static assets.</span></span> <span data-ttu-id="7d116-154">Такие ресурсы доступны любому приложению, использующему библиотеку.</span><span class="sxs-lookup"><span data-stu-id="7d116-154">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="7d116-155">Для получения дополнительной информации см. <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="7d116-155">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="7d116-156">Сборка, упаковка и отправка в NuGet</span><span class="sxs-lookup"><span data-stu-id="7d116-156">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="7d116-157">Так как библиотеки компонентов являются стандартными библиотеками .NET, их упаковка и передача в NuGet не отличается от упаковки и передачи любых других библиотек.</span><span class="sxs-lookup"><span data-stu-id="7d116-157">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="7d116-158">Упаковка выполняется с помощью команды [dotnet pack](/dotnet/core/tools/dotnet-pack) в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="7d116-158">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="7d116-159">Чтобы отправить пакет в NuGet, используйте команду [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="7d116-159">Upload the package to NuGet using the [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7d116-160">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7d116-160">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="7d116-161">Добавление файла конфигурации компоновщика XML в библиотеку</span><span class="sxs-lookup"><span data-stu-id="7d116-161">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
