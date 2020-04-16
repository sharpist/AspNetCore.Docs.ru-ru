---
title: Компиляция файлов Razor в ASP.NET Core
author: rick-anderson
description: Узнайте, как происходит компиляция файлов Razor в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440939"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="e6c39-103">Компиляция файлов Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e6c39-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="e6c39-104">Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e6c39-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="e6c39-105">Файлы Razor с расширением *CSHTML* компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e6c39-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="e6c39-106">Компиляция runtime может быть опционально включена путем настройки проекта.</span><span class="sxs-lookup"><span data-stu-id="e6c39-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="e6c39-107">Компиляция Razor</span><span class="sxs-lookup"><span data-stu-id="e6c39-107">Razor compilation</span></span>

<span data-ttu-id="e6c39-108">Сборка времени и время публикации компиляции файлов Razor включена по умолчанию SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="e6c39-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="e6c39-109">При включении компиляция времени выполнения дополняет компиляцию времени сборки, позволяя обновлять файлы Razor, если они редактируются.</span><span class="sxs-lookup"><span data-stu-id="e6c39-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="e6c39-110">Включить компиляцию времени выполнения при создании проекта</span><span class="sxs-lookup"><span data-stu-id="e6c39-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="e6c39-111">Шаблоны проектов Razor Pages и MVC включают опцию для включения компиляции времени выполнения при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="e6c39-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="e6c39-112">Эта опция поддерживается в ASP.NET Core 3.1 и позже.</span><span class="sxs-lookup"><span data-stu-id="e6c39-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e6c39-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e6c39-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e6c39-114">В **создании нового ASP.NET журнале веб-приложений Core:**</span><span class="sxs-lookup"><span data-stu-id="e6c39-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="e6c39-115">Выберите шаблон проекта **Web Application** или Web **Application (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="e6c39-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="e6c39-116">Выберите **флажок компиляции времени выполнения Enable Razor.**</span><span class="sxs-lookup"><span data-stu-id="e6c39-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="e6c39-117">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="e6c39-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e6c39-118">Используйте `-rrc` `--razor-runtime-compilation` опцию или шаблон.</span><span class="sxs-lookup"><span data-stu-id="e6c39-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="e6c39-119">Например, следующая команда создает новый проект Razor Pages с включенной компиляцией времени выполнения:</span><span class="sxs-lookup"><span data-stu-id="e6c39-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="e6c39-120">Включение компиляции времени выполнения в существующем проекте</span><span class="sxs-lookup"><span data-stu-id="e6c39-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="e6c39-121">Для обеспечения компиляции времени выполнения для всех сред в существующем проекте:</span><span class="sxs-lookup"><span data-stu-id="e6c39-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="e6c39-122">Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e6c39-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="e6c39-123">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="e6c39-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="e6c39-124">Пример:</span><span class="sxs-lookup"><span data-stu-id="e6c39-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="e6c39-125">Условно включить компиляцию времени выполнения в существующем проекте</span><span class="sxs-lookup"><span data-stu-id="e6c39-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="e6c39-126">Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="e6c39-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="e6c39-127">Подобное условное включение гарантирует, что опубликованные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="e6c39-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="e6c39-128">используют скомпилированные представления;</span><span class="sxs-lookup"><span data-stu-id="e6c39-128">Uses compiled views.</span></span>
* <span data-ttu-id="e6c39-129">не включают наблюдатели файлов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e6c39-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="e6c39-130">Для обеспечения компиляции времени выполнения только в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="e6c39-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="e6c39-131">Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e6c39-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="e6c39-132">Измените раздел `environmentVariables` профиля запуска в *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="e6c39-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="e6c39-133">Проверка `ASPNETCORE_ENVIRONMENT` установлена `"Development"`на .</span><span class="sxs-lookup"><span data-stu-id="e6c39-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="e6c39-134">Присвойте параметру `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` значение `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="e6c39-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="e6c39-135">В следующем примере компиляция времени выполнения включена в среде разработки для профилей `IIS Express` и `RazorPagesApp` запусков:</span><span class="sxs-lookup"><span data-stu-id="e6c39-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="e6c39-136">В классе проекта не требуется `Startup` никаких изменений кода.</span><span class="sxs-lookup"><span data-stu-id="e6c39-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="e6c39-137">В runtime ASP.NET Core ищет [атрибут HostingStartup на уровне сборки](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) в `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="e6c39-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="e6c39-138">Атрибут `HostingStartup` определяет код запуска приложения для выполнения.</span><span class="sxs-lookup"><span data-stu-id="e6c39-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="e6c39-139">Этот код запуска позволяет компиляцию времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="e6c39-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="e6c39-140">Включить компиляцию времени выполнения для библиотеки класса Razor</span><span class="sxs-lookup"><span data-stu-id="e6c39-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="e6c39-141">Рассмотрим сценарий, в котором проект Razor Pages ссылается на [библиотеку класса Razor (RCL)](xref:razor-pages/ui-class) под названием *MyClassLib.*</span><span class="sxs-lookup"><span data-stu-id="e6c39-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="e6c39-142">RCL содержит файл *_Layout.cshtml,* который потребляют все проекты MVC и Razor Pages вашей команды.</span><span class="sxs-lookup"><span data-stu-id="e6c39-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="e6c39-143">Вы хотите включить компиляцию времени выполнения для файла *_Layout.cshtml* в этом RCL.</span><span class="sxs-lookup"><span data-stu-id="e6c39-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="e6c39-144">Внести следующие изменения в проект Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="e6c39-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="e6c39-145">Включить компиляцию времени выполнения с инструкциями [в Условно мнимой компиляции времени выполнения в существующем проекте.](#conditionally-enable-runtime-compilation-in-an-existing-project)</span><span class="sxs-lookup"><span data-stu-id="e6c39-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="e6c39-146">Настройка параметров компиляции времени выполнения в: `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="e6c39-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="e6c39-147">В предыдущем коде построен абсолютный путь к *MyClassLib* RCL.</span><span class="sxs-lookup"><span data-stu-id="e6c39-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="e6c39-148">[API PhysicalFileProvider](xref:fundamentals/file-providers#physicalfileprovider) используется для поиска каталогов и файлов на этом абсолютном пути.</span><span class="sxs-lookup"><span data-stu-id="e6c39-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="e6c39-149">Наконец, `PhysicalFileProvider` экземпляр добавляется в коллекцию поставщиков файлов, что позволяет получить доступ к файлам *.cshtml* rCL.</span><span class="sxs-lookup"><span data-stu-id="e6c39-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6c39-150">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e6c39-150">Additional resources</span></span>

* <span data-ttu-id="e6c39-151">[RazorCompileOnBuild и RazorCompileOnPublish](xref:razor-pages/sdk#properties) свойства.</span><span class="sxs-lookup"><span data-stu-id="e6c39-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="e6c39-152">Файлы Razor с расширением *CSHTML* компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e6c39-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="e6c39-153">Компиляцию в среде выполнения при необходимости можно включить, настроив приложение.</span><span class="sxs-lookup"><span data-stu-id="e6c39-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="e6c39-154">Компиляция Razor</span><span class="sxs-lookup"><span data-stu-id="e6c39-154">Razor compilation</span></span>

<span data-ttu-id="e6c39-155">Сборка времени и время публикации компиляции файлов Razor включена по умолчанию SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="e6c39-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="e6c39-156">При включении компиляция времени выполнения дополняет компиляцию времени сборки, позволяя обновлять файлы Razor, если они редактируются.</span><span class="sxs-lookup"><span data-stu-id="e6c39-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="e6c39-157">компиляция среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="e6c39-157">Runtime compilation</span></span>

<span data-ttu-id="e6c39-158">Чтобы включить компиляцию в среде выполнения для всех сред и режимов конфигурации, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="e6c39-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="e6c39-159">Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="e6c39-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="e6c39-160">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="e6c39-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="e6c39-161">Пример:</span><span class="sxs-lookup"><span data-stu-id="e6c39-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="e6c39-162">Условное включение компиляции в среде выполнения</span><span class="sxs-lookup"><span data-stu-id="e6c39-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="e6c39-163">Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="e6c39-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="e6c39-164">Подобное условное включение гарантирует, что опубликованные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="e6c39-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="e6c39-165">используют скомпилированные представления;</span><span class="sxs-lookup"><span data-stu-id="e6c39-165">Uses compiled views.</span></span>
* <span data-ttu-id="e6c39-166">имеют меньший размер;</span><span class="sxs-lookup"><span data-stu-id="e6c39-166">Is smaller in size.</span></span>
* <span data-ttu-id="e6c39-167">не включают наблюдатели файлов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="e6c39-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="e6c39-168">Чтобы включить компиляцию в среде выполнения для конкретной среды и конкретного режима конфигурации, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="e6c39-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="e6c39-169">Условно сошлитесь на пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) с учетом активного значения `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="e6c39-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="e6c39-170">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="e6c39-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="e6c39-171">Условно выполните `AddRazorRuntimeCompilation`, чтобы он выполнялся только в режиме отладки, если для переменной `ASPNETCORE_ENVIRONMENT` задано значение `Development`:</span><span class="sxs-lookup"><span data-stu-id="e6c39-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="e6c39-172">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e6c39-172">Additional resources</span></span>

* <span data-ttu-id="e6c39-173">[RazorCompileOnBuild и RazorCompileOnPublish](xref:razor-pages/sdk#properties) свойства.</span><span class="sxs-lookup"><span data-stu-id="e6c39-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="e6c39-174">Пример [компиляции времени выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) можно просмотреть пример, который показывает, что компиляция времени выполнения работает в проектах.</span><span class="sxs-lookup"><span data-stu-id="e6c39-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e6c39-175">Файл Razor компилируется в среде выполнения при вызове связанной страницы Razor или представления MVC.</span><span class="sxs-lookup"><span data-stu-id="e6c39-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="e6c39-176">Файлы Razor компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e6c39-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="e6c39-177">Компиляция Razor</span><span class="sxs-lookup"><span data-stu-id="e6c39-177">Razor compilation</span></span>

<span data-ttu-id="e6c39-178">Компиляция файлов Razor во время сборки и публикации включена по умолчанию с помощью пакета SDK для Razor.</span><span class="sxs-lookup"><span data-stu-id="e6c39-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="e6c39-179">Редактирование файлов Razor после их обновления поддерживается во время сборки.</span><span class="sxs-lookup"><span data-stu-id="e6c39-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="e6c39-180">По умолчанию только скомпилированные файлы *Views.dll*, а не *.cshtml*, и сборки, необходимые для компиляции файлов Razor, развертываются с приложением.</span><span class="sxs-lookup"><span data-stu-id="e6c39-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e6c39-181">Средство предварительной компиляции является нерекомендуемым и будет удалено в ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="e6c39-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e6c39-182">Мы советуем перейти на [пакет SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="e6c39-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="e6c39-183">Пакет SDK для Razor применяется только в том случае, если в файле проекта не заданы свойства предварительной компиляции.</span><span class="sxs-lookup"><span data-stu-id="e6c39-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="e6c39-184">Например, если в *CSPROJ*-файле для свойства `MvcRazorCompileOnPublish` установлено значение `true`, пакет SDK для Razor будет отключен.</span><span class="sxs-lookup"><span data-stu-id="e6c39-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="e6c39-185">компиляция среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="e6c39-185">Runtime compilation</span></span>

<span data-ttu-id="e6c39-186">Компиляция во время сборки дополняется компиляцией файлов Razor в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="e6c39-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="e6c39-187">ASP.NET Core MVC будет перекомпилировать файлы Razor, когда содержимое файла *.cshtml* меняется.</span><span class="sxs-lookup"><span data-stu-id="e6c39-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e6c39-188">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e6c39-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
