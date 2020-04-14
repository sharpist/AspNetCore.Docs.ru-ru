---
title: Компиляция файлов Razor в ASP.NET Core
author: rick-anderson
description: Узнайте, как происходит компиляция файлов Razor в приложении ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/13/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 67bbeb88cd944791b522900b69bd10cff38c9f3a
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277277"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="a663f-103">Компиляция файлов Razor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a663f-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="a663f-104">Автор: [Рик Андерсон (Rick Anderson)](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a663f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="a663f-105">Файлы Razor с расширением *CSHTML* компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="a663f-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a663f-106">Компиляция runtime может быть опционально включена путем настройки проекта.</span><span class="sxs-lookup"><span data-stu-id="a663f-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="a663f-107">Компиляция Razor</span><span class="sxs-lookup"><span data-stu-id="a663f-107">Razor compilation</span></span>

<span data-ttu-id="a663f-108">Сборка времени и время публикации компиляции файлов Razor включена по умолчанию SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="a663f-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a663f-109">При включении компиляция времени выполнения дополняет компиляцию времени сборки, позволяя обновлять файлы Razor, если они редактируются.</span><span class="sxs-lookup"><span data-stu-id="a663f-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="a663f-110">Включить компиляцию времени выполнения при создании проекта</span><span class="sxs-lookup"><span data-stu-id="a663f-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="a663f-111">Шаблоны проектов Razor Pages и MVC включают опцию для включения компиляции времени выполнения при создании проекта.</span><span class="sxs-lookup"><span data-stu-id="a663f-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="a663f-112">Эта опция поддерживается в ASP.NET Core 3.1 и позже.</span><span class="sxs-lookup"><span data-stu-id="a663f-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a663f-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a663f-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a663f-114">В **создании нового ASP.NET журнале веб-приложений Core:**</span><span class="sxs-lookup"><span data-stu-id="a663f-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="a663f-115">Выберите шаблон проекта **Web Application** или Web **Application (Model-View-Controller).**</span><span class="sxs-lookup"><span data-stu-id="a663f-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="a663f-116">Выберите **флажок компиляции времени выполнения Enable Razor.**</span><span class="sxs-lookup"><span data-stu-id="a663f-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a663f-117">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="a663f-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a663f-118">Используйте `-rrc` `--razor-runtime-compilation` опцию или шаблон.</span><span class="sxs-lookup"><span data-stu-id="a663f-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="a663f-119">Например, следующая команда создает новый проект Razor Pages с включенной компиляцией времени выполнения:</span><span class="sxs-lookup"><span data-stu-id="a663f-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="a663f-120">Включение компиляции времени выполнения в существующем проекте</span><span class="sxs-lookup"><span data-stu-id="a663f-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="a663f-121">Для обеспечения компиляции времени выполнения для всех сред в существующем проекте:</span><span class="sxs-lookup"><span data-stu-id="a663f-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="a663f-122">Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a663f-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="a663f-123">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="a663f-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="a663f-124">Пример:</span><span class="sxs-lookup"><span data-stu-id="a663f-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="a663f-125">Условно включить компиляцию времени выполнения в существующем проекте</span><span class="sxs-lookup"><span data-stu-id="a663f-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="a663f-126">Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="a663f-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="a663f-127">Подобное условное включение гарантирует, что опубликованные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="a663f-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="a663f-128">используют скомпилированные представления;</span><span class="sxs-lookup"><span data-stu-id="a663f-128">Uses compiled views.</span></span>
* <span data-ttu-id="a663f-129">не включают наблюдатели файлов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="a663f-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="a663f-130">Для обеспечения компиляции времени выполнения только в среде разработки:</span><span class="sxs-lookup"><span data-stu-id="a663f-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="a663f-131">Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a663f-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="a663f-132">Измените раздел `environmentVariables` профиля запуска в *launchSettings.json:*</span><span class="sxs-lookup"><span data-stu-id="a663f-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="a663f-133">Проверка `ASPNETCORE_ENVIRONMENT` установлена `"Development"`на .</span><span class="sxs-lookup"><span data-stu-id="a663f-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="a663f-134">Присвойте параметру `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` значение `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span><span class="sxs-lookup"><span data-stu-id="a663f-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="a663f-135">В следующем примере компиляция времени выполнения включена в среде разработки для профилей `IIS Express` и `RazorPagesApp` запусков:</span><span class="sxs-lookup"><span data-stu-id="a663f-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="a663f-136">В классе проекта не требуется `Startup` никаких изменений кода.</span><span class="sxs-lookup"><span data-stu-id="a663f-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="a663f-137">В runtime ASP.NET Core ищет [атрибут HostingStartup на уровне сборки](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) в `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="a663f-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="a663f-138">Атрибут `HostingStartup` определяет код запуска приложения для выполнения.</span><span class="sxs-lookup"><span data-stu-id="a663f-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="a663f-139">Этот код запуска позволяет компиляцию времени выполнения.</span><span class="sxs-lookup"><span data-stu-id="a663f-139">That startup code enables runtime compilation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a663f-140">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a663f-140">Additional resources</span></span>

* <span data-ttu-id="a663f-141">[RazorCompileOnBuild и RazorCompileOnPublish](xref:razor-pages/sdk#properties) свойства.</span><span class="sxs-lookup"><span data-stu-id="a663f-141">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="a663f-142">Пример [компиляции времени выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) можно просмотреть пример, который показывает, что компиляция времени выполнения работает в проектах.</span><span class="sxs-lookup"><span data-stu-id="a663f-142">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="a663f-143">Файлы Razor с расширением *CSHTML* компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="a663f-143">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a663f-144">Компиляцию в среде выполнения при необходимости можно включить, настроив приложение.</span><span class="sxs-lookup"><span data-stu-id="a663f-144">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="a663f-145">Компиляция Razor</span><span class="sxs-lookup"><span data-stu-id="a663f-145">Razor compilation</span></span>

<span data-ttu-id="a663f-146">Сборка времени и время публикации компиляции файлов Razor включена по умолчанию SDK Razor.</span><span class="sxs-lookup"><span data-stu-id="a663f-146">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a663f-147">При включении компиляция времени выполнения дополняет компиляцию времени сборки, позволяя обновлять файлы Razor, если они редактируются.</span><span class="sxs-lookup"><span data-stu-id="a663f-147">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="a663f-148">компиляция среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="a663f-148">Runtime compilation</span></span>

<span data-ttu-id="a663f-149">Чтобы включить компиляцию в среде выполнения для всех сред и режимов конфигурации, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="a663f-149">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="a663f-150">Установите пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="a663f-150">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="a663f-151">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span><span class="sxs-lookup"><span data-stu-id="a663f-151">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="a663f-152">Пример:</span><span class="sxs-lookup"><span data-stu-id="a663f-152">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="a663f-153">Условное включение компиляции в среде выполнения</span><span class="sxs-lookup"><span data-stu-id="a663f-153">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="a663f-154">Компиляцию в среде выполнения можно включить так, чтобы она была доступна только для локальной рабочей среды.</span><span class="sxs-lookup"><span data-stu-id="a663f-154">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="a663f-155">Подобное условное включение гарантирует, что опубликованные выходные данные:</span><span class="sxs-lookup"><span data-stu-id="a663f-155">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="a663f-156">используют скомпилированные представления;</span><span class="sxs-lookup"><span data-stu-id="a663f-156">Uses compiled views.</span></span>
* <span data-ttu-id="a663f-157">имеют меньший размер;</span><span class="sxs-lookup"><span data-stu-id="a663f-157">Is smaller in size.</span></span>
* <span data-ttu-id="a663f-158">не включают наблюдатели файлов в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="a663f-158">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="a663f-159">Чтобы включить компиляцию в среде выполнения для конкретной среды и конкретного режима конфигурации, сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="a663f-159">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="a663f-160">Условно сошлитесь на пакет [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) с учетом активного значения `Configuration`:</span><span class="sxs-lookup"><span data-stu-id="a663f-160">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="a663f-161">обновите метод `Startup.ConfigureServices` в проекте, чтобы включить вызов `AddRazorRuntimeCompilation`.</span><span class="sxs-lookup"><span data-stu-id="a663f-161">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="a663f-162">Условно выполните `AddRazorRuntimeCompilation`, чтобы он выполнялся только в режиме отладки, если для переменной `ASPNETCORE_ENVIRONMENT` задано значение `Development`:</span><span class="sxs-lookup"><span data-stu-id="a663f-162">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="a663f-163">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a663f-163">Additional resources</span></span>

* <span data-ttu-id="a663f-164">[RazorCompileOnBuild и RazorCompileOnPublish](xref:razor-pages/sdk#properties) свойства.</span><span class="sxs-lookup"><span data-stu-id="a663f-164">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="a663f-165">Пример [компиляции времени выполнения на GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) можно просмотреть пример, который показывает, что компиляция времени выполнения работает в проектах.</span><span class="sxs-lookup"><span data-stu-id="a663f-165">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a663f-166">Файл Razor компилируется в среде выполнения при вызове связанной страницы Razor или представления MVC.</span><span class="sxs-lookup"><span data-stu-id="a663f-166">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="a663f-167">Файлы Razor компилируются и во время сборки, и во время публикации с помощью [пакета SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="a663f-167">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="a663f-168">Компиляция Razor</span><span class="sxs-lookup"><span data-stu-id="a663f-168">Razor compilation</span></span>

<span data-ttu-id="a663f-169">Компиляция файлов Razor во время сборки и публикации включена по умолчанию с помощью пакета SDK для Razor.</span><span class="sxs-lookup"><span data-stu-id="a663f-169">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a663f-170">Редактирование файлов Razor после их обновления поддерживается во время сборки.</span><span class="sxs-lookup"><span data-stu-id="a663f-170">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="a663f-171">По умолчанию только скомпилированные файлы *Views.dll*, а не *.cshtml*, и сборки, необходимые для компиляции файлов Razor, развертываются с приложением.</span><span class="sxs-lookup"><span data-stu-id="a663f-171">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a663f-172">Средство предварительной компиляции является нерекомендуемым и будет удалено в ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="a663f-172">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a663f-173">Мы советуем перейти на [пакет SDK для Razor](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="a663f-173">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="a663f-174">Пакет SDK для Razor применяется только в том случае, если в файле проекта не заданы свойства предварительной компиляции.</span><span class="sxs-lookup"><span data-stu-id="a663f-174">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="a663f-175">Например, если в *CSPROJ*-файле для свойства `MvcRazorCompileOnPublish` установлено значение `true`, пакет SDK для Razor будет отключен.</span><span class="sxs-lookup"><span data-stu-id="a663f-175">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="a663f-176">компиляция среды выполнения.</span><span class="sxs-lookup"><span data-stu-id="a663f-176">Runtime compilation</span></span>

<span data-ttu-id="a663f-177">Компиляция во время сборки дополняется компиляцией файлов Razor в среде выполнения.</span><span class="sxs-lookup"><span data-stu-id="a663f-177">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="a663f-178">ASP.NET Core MVC будет перекомпилировать файлы Razor, когда содержимое файла *.cshtml* меняется.</span><span class="sxs-lookup"><span data-stu-id="a663f-178">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a663f-179">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="a663f-179">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
