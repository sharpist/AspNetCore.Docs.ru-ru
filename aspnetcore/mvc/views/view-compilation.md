---
title: RazorКомпиляция файла в ASP.NET Core
author: rick-anderson
description: Узнайте Razor , как происходит компиляция файлов в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-compilation
ms.openlocfilehash: fc7924f8f8b321ae017b7acd729fe11c4e0e3c7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021085"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="d30d6-103">RazorКомпиляция файла в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d30d6-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="d30d6-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="d30d6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="d30d6-105">Razorфайлы с расширением *. cshtml* компилируются как во время сборки, так и при публикации с помощью [ Razor пакета SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="d30d6-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="d30d6-106">Компиляцию среды выполнения можно дополнительно включить, настроив проект.</span><span class="sxs-lookup"><span data-stu-id="d30d6-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="d30d6-107">Razorкомпиляции</span><span class="sxs-lookup"><span data-stu-id="d30d6-107">Razor compilation</span></span>

<span data-ttu-id="d30d6-108">Компиляция файлов во время сборки и времени публикации Razor по умолчанию включена Razor пакетом SDK.</span><span class="sxs-lookup"><span data-stu-id="d30d6-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="d30d6-109">При включении компиляция среды выполнения дополняет компиляцию во время сборки, позволяя Razor обновлять файлы, если они редактируются.</span><span class="sxs-lookup"><span data-stu-id="d30d6-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="d30d6-110">Включить компиляцию во время выполнения при создании проекта</span><span class="sxs-lookup"><span data-stu-id="d30d6-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="d30d6-111">RazorШаблоны проектов Pages и MVC включают в себя возможность включения компиляции во время создания проекта.</span><span class="sxs-lookup"><span data-stu-id="d30d6-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="d30d6-112">Этот параметр поддерживается в ASP.NET Core 3,1 и более поздних версиях.</span><span class="sxs-lookup"><span data-stu-id="d30d6-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d30d6-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d30d6-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d30d6-114">В диалоговом окне **Создание нового ASP.NET Core веб-приложения** выполните следующие действия.</span><span class="sxs-lookup"><span data-stu-id="d30d6-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="d30d6-115">Выберите шаблон проекта веб- **приложение** или **веб-приложение (модель-представление-контроллер)** .</span><span class="sxs-lookup"><span data-stu-id="d30d6-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="d30d6-116">Установите флажок **включить Razor компиляцию во время выполнения** .</span><span class="sxs-lookup"><span data-stu-id="d30d6-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d30d6-117">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="d30d6-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d30d6-118">Используйте `-rrc` `--razor-runtime-compilation` параметр шаблона или.</span><span class="sxs-lookup"><span data-stu-id="d30d6-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="d30d6-119">Например, следующая команда создает новый Razor проект страниц с включенной компиляцией среды выполнения:</span><span class="sxs-lookup"><span data-stu-id="d30d6-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="d30d6-120">Включение компиляции среды выполнения в существующем проекте</span><span class="sxs-lookup"><span data-stu-id="d30d6-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="d30d6-121">Включение компиляции во время выполнения для всех сред в существующем проекте:</span><span class="sxs-lookup"><span data-stu-id="d30d6-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="d30d6-122">Установите [Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Пакет NuGet рунтимекомпилатион.</span><span class="sxs-lookup"><span data-stu-id="d30d6-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="d30d6-123">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="d30d6-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="d30d6-124">Например:</span><span class="sxs-lookup"><span data-stu-id="d30d6-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="d30d6-125">Условно включить компиляцию среды выполнения в существующем проекте</span><span class="sxs-lookup"><span data-stu-id="d30d6-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="d30d6-126">Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="d30d6-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="d30d6-127">Подобное условное включение гарантирует, что опубликованные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d30d6-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="d30d6-128">используют скомпилированные представления;</span><span class="sxs-lookup"><span data-stu-id="d30d6-128">Uses compiled views.</span></span>
* <span data-ttu-id="d30d6-129">не включают наблюдатели файлов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="d30d6-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="d30d6-130">Включение компиляции во время выполнения только в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="d30d6-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="d30d6-131">Установите [Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Пакет NuGet рунтимекомпилатион.</span><span class="sxs-lookup"><span data-stu-id="d30d6-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="d30d6-132">Измените раздел "профиль запуска" `environmentVariables` в *launchSettings.js*:</span><span class="sxs-lookup"><span data-stu-id="d30d6-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="d30d6-133">`ASPNETCORE_ENVIRONMENT`Для параметра проверить задано значение `"Development"` .</span><span class="sxs-lookup"><span data-stu-id="d30d6-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="d30d6-134">Задайте для параметра `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` значение `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="d30d6-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="d30d6-135">В следующем примере компиляция среды выполнения включается в среде разработки для `IIS Express` `RazorPagesApp` профилей запуска и.</span><span class="sxs-lookup"><span data-stu-id="d30d6-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="d30d6-136">В классе проекта не требуется вносить изменения в код `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d30d6-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="d30d6-137">Во время выполнения ASP.NET Core выполняет поиск [атрибута хостингстартуп на уровне сборки](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) в `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` .</span><span class="sxs-lookup"><span data-stu-id="d30d6-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="d30d6-138">`HostingStartup`Атрибут указывает код запуска приложения для выполнения.</span><span class="sxs-lookup"><span data-stu-id="d30d6-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="d30d6-139">Код запуска включает компиляцию среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="d30d6-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="d30d6-140">Включение компиляции среды выполнения для Razor библиотеки классов</span><span class="sxs-lookup"><span data-stu-id="d30d6-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="d30d6-141">Рассмотрим ситуацию, в которой Razor проект страниц ссылается на [ Razor БИБЛИОТЕКУ классов (РКЛ)](xref:razor-pages/ui-class) с именем *микласслиб*.</span><span class="sxs-lookup"><span data-stu-id="d30d6-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="d30d6-142">РКЛ содержит файл *_layout. cshtml* , который используется всеми проектами MVC и Razor pages вашей команды.</span><span class="sxs-lookup"><span data-stu-id="d30d6-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="d30d6-143">Необходимо включить компиляцию среды выполнения для файла *_layout. cshtml* в этой РКЛ.</span><span class="sxs-lookup"><span data-stu-id="d30d6-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="d30d6-144">Внесите следующие изменения в Razor проект страниц.</span><span class="sxs-lookup"><span data-stu-id="d30d6-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="d30d6-145">Включите компиляцию среды выполнения с инструкциями по [условию включить компиляцию среды выполнения в существующем проекте](#conditionally-enable-runtime-compilation-in-an-existing-project).</span><span class="sxs-lookup"><span data-stu-id="d30d6-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="d30d6-146">Настройте параметры компиляции среды выполнения в `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d30d6-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="d30d6-147">В приведенном выше коде создается абсолютный путь к *микласслиб* РКЛ.</span><span class="sxs-lookup"><span data-stu-id="d30d6-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="d30d6-148">[API фисикалфилепровидер](xref:fundamentals/file-providers#physicalfileprovider) используется для поиска каталогов и файлов по этому абсолютному пути.</span><span class="sxs-lookup"><span data-stu-id="d30d6-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="d30d6-149">Наконец, `PhysicalFileProvider` экземпляр добавляется в коллекцию поставщиков файлов, которая разрешает доступ к ФАЙЛАМ РКЛ *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="d30d6-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d30d6-150">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d30d6-150">Additional resources</span></span>

* <span data-ttu-id="d30d6-151">Свойства [ Razor компилеонбуилд и Razor компилеонпублиш](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="d30d6-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="d30d6-152">Razorфайлы с расширением *. cshtml* компилируются как во время сборки, так и при публикации с помощью [ Razor пакета SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="d30d6-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="d30d6-153">Компиляцию в среде выполнения при необходимости можно включить, настроив приложение.</span><span class="sxs-lookup"><span data-stu-id="d30d6-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="d30d6-154">Razorкомпиляции</span><span class="sxs-lookup"><span data-stu-id="d30d6-154">Razor compilation</span></span>

<span data-ttu-id="d30d6-155">Компиляция файлов во время сборки и времени публикации Razor по умолчанию включена Razor пакетом SDK.</span><span class="sxs-lookup"><span data-stu-id="d30d6-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="d30d6-156">При включении компиляция среды выполнения дополняет компиляцию во время сборки, позволяя Razor обновлять файлы, если они редактируются.</span><span class="sxs-lookup"><span data-stu-id="d30d6-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="d30d6-157">компиляция среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="d30d6-157">Runtime compilation</span></span>

<span data-ttu-id="d30d6-158">Чтобы включить компиляцию в среде выполнения для всех сред и режимов конфигурации, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="d30d6-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="d30d6-159">Установите [Microsoft. AspNetCore. MVC. Razor . ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)Пакет NuGet рунтимекомпилатион.</span><span class="sxs-lookup"><span data-stu-id="d30d6-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="d30d6-160">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="d30d6-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="d30d6-161">Например:</span><span class="sxs-lookup"><span data-stu-id="d30d6-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="d30d6-162">Условное включение компиляции в среде выполнения</span><span class="sxs-lookup"><span data-stu-id="d30d6-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="d30d6-163">Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="d30d6-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="d30d6-164">Подобное условное включение гарантирует, что опубликованные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="d30d6-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="d30d6-165">используют скомпилированные представления;</span><span class="sxs-lookup"><span data-stu-id="d30d6-165">Uses compiled views.</span></span>
* <span data-ttu-id="d30d6-166">имеют меньший размер;</span><span class="sxs-lookup"><span data-stu-id="d30d6-166">Is smaller in size.</span></span>
* <span data-ttu-id="d30d6-167">не включают наблюдатели файлов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="d30d6-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="d30d6-168">Чтобы включить компиляцию в среде выполнения для конкретной среды и конкретного режима конфигурации, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="d30d6-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="d30d6-169">Условная ссылка на [Microsoft. AspNetCore. MVC. Razor . Рунтимекомпилатион](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) пакет на основе активного `Configuration` значения:</span><span class="sxs-lookup"><span data-stu-id="d30d6-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="d30d6-170">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="d30d6-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="d30d6-171">Условно выполните `AddRazorRuntimeCompilation`, чтобы он выполнялся только в режиме отладки, если для переменной `ASPNETCORE_ENVIRONMENT` задано значение `Development`:</span><span class="sxs-lookup"><span data-stu-id="d30d6-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="d30d6-172">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d30d6-172">Additional resources</span></span>

* <span data-ttu-id="d30d6-173">Свойства [ Razor компилеонбуилд и Razor компилеонпублиш](xref:razor-pages/sdk#properties) .</span><span class="sxs-lookup"><span data-stu-id="d30d6-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="d30d6-174">Пример, демонстрирующий работу компиляции во время выполнения проектов, см. в [примере компиляции среды выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) .</span><span class="sxs-lookup"><span data-stu-id="d30d6-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d30d6-175">RazorФайл компилируется во время выполнения, когда Razor вызывается связанная страница или представление MVC.</span><span class="sxs-lookup"><span data-stu-id="d30d6-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="d30d6-176">Razorфайлы компилируются как во время сборки, так и при публикации с помощью [ Razor пакета SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="d30d6-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="d30d6-177">Razorкомпиляции</span><span class="sxs-lookup"><span data-stu-id="d30d6-177">Razor compilation</span></span>

<span data-ttu-id="d30d6-178">Компиляция файлов и времени публикации Razor по умолчанию включается Razor пакетом SDK.</span><span class="sxs-lookup"><span data-stu-id="d30d6-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="d30d6-179">Редактирование Razor файлов после их обновления поддерживается во время сборки.</span><span class="sxs-lookup"><span data-stu-id="d30d6-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="d30d6-180">По умолчанию в приложении развертываются только скомпилированные *Views.dll* и файлы с *. cshtml* или ссылки на сборки, необходимые для компиляции Razor файлов.</span><span class="sxs-lookup"><span data-stu-id="d30d6-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d30d6-181">Средство предварительной компиляции является нерекомендуемым и будет удалено в ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="d30d6-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="d30d6-182">Мы рекомендуем переходить на [ Razor пакет SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="d30d6-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="d30d6-183">RazorПакет SDK эффективен, только если в файле проекта не заданы свойства, относящиеся к предварительной компиляции.</span><span class="sxs-lookup"><span data-stu-id="d30d6-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="d30d6-184">Например, *.csproj* `MvcRazorCompileOnPublish` чтобы `true` Отключить Razor пакет SDK, присвоив свойству файла CSPROJ значение.</span><span class="sxs-lookup"><span data-stu-id="d30d6-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="d30d6-185">компиляция среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="d30d6-185">Runtime compilation</span></span>

<span data-ttu-id="d30d6-186">Компиляция во время сборки дополняется компиляцией файлов в среде выполнения Razor .</span><span class="sxs-lookup"><span data-stu-id="d30d6-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="d30d6-187">ASP.NET Core MVC будет перекомпилировать Razor файлы при изменении содержимого файла *CSHTML* .</span><span class="sxs-lookup"><span data-stu-id="d30d6-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d30d6-188">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d30d6-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
