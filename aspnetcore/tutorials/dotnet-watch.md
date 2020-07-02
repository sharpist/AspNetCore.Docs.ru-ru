---
title: Разработка приложений ASP.NET Core с использованием наблюдателя файлов
author: rick-anderson
description: В этом руководстве показано, как установить и использовать наблюдатель за файлами .NET Core CLI (dotnet watch) в приложении ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 0c81dc2fd83cb9327dad46dab840d4352de89baf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409037"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="f6c5d-103">Разработка приложений ASP.NET Core с использованием наблюдателя файлов</span><span class="sxs-lookup"><span data-stu-id="f6c5d-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="f6c5d-104">Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Виктор Хурдугачи](https://twitter.com/victorhurdugaci) (Victor Hurdugaci)</span><span class="sxs-lookup"><span data-stu-id="f6c5d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="f6c5d-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) — это средство, которое запускает команду [.NET Core CLI](/dotnet/core/tools) при изменении исходных файлов.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="f6c5d-106">Например, в результате изменения файла может выполняться компиляция, тестирование или развертывание.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="f6c5d-107">В этом руководстве используется уже существующее приложение веб-API с двумя конечными точками, одна из которых возвращает сумму, а другая — произведение.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="f6c5d-108">В методе произведения есть ошибка, которая исправлена в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="f6c5d-109">Скачайте [образец приложения](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="f6c5d-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="f6c5d-110">Он содержит два проекта: *WebApp* (веб-API ASP.NET Core) и *WebAppTests* (модульные тесты для веб-API).</span><span class="sxs-lookup"><span data-stu-id="f6c5d-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="f6c5d-111">В командной оболочке перейдите в папку *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="f6c5d-112">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="f6c5d-113">Можно использовать `dotnet run --project <PROJECT>`, чтобы указать проект для запуска.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="f6c5d-114">Например, при выполнении `dotnet run --project WebApp` из корневой папки примера приложения также будет запущен проект *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="f6c5d-115">В выходных данных в консоли будут показаны аналогичные следующим сообщения, которые свидетельствуют о том, что приложение выполняется и ожидает запросов:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="f6c5d-116">В браузере перейдите на адрес `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="f6c5d-117">Должен появиться результат `9`.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-117">You should see the result of `9`.</span></span>

<span data-ttu-id="f6c5d-118">Перейдите к API продукта (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="f6c5d-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="f6c5d-119">Он возвращает `9`, а не `20`, как ожидалось.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="f6c5d-120">Эта проблема устраняется далее в этом руководстве.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="f6c5d-121">Добавление `dotnet watch` в проект</span><span class="sxs-lookup"><span data-stu-id="f6c5d-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="f6c5d-122">Средство наблюдения за файлами `dotnet watch` входит в версию 2.1.300 пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="f6c5d-123">Выполните следующие действия при использовании более ранней версии пакета SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="f6c5d-124">Добавьте ссылку на пакет `Microsoft.DotNet.Watcher.Tools` в *CSPROJ*-файл:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="f6c5d-125">Установите пакет `Microsoft.DotNet.Watcher.Tools`, запустив следующую команду:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="f6c5d-126">Запуск команд .NET Core CLI с помощью `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f6c5d-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="f6c5d-127">Любую [команду .NET Core CLI](/dotnet/core/tools#cli-commands) можно запустить с `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="f6c5d-128">Пример:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-128">For example:</span></span>

| <span data-ttu-id="f6c5d-129">Команда</span><span class="sxs-lookup"><span data-stu-id="f6c5d-129">Command</span></span> | <span data-ttu-id="f6c5d-130">Команда с контрольным значением</span><span class="sxs-lookup"><span data-stu-id="f6c5d-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="f6c5d-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="f6c5d-131">dotnet run</span></span> | <span data-ttu-id="f6c5d-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="f6c5d-132">dotnet watch run</span></span> |
| <span data-ttu-id="f6c5d-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="f6c5d-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="f6c5d-134">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="f6c5d-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="f6c5d-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="f6c5d-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="f6c5d-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="f6c5d-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="f6c5d-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="f6c5d-137">dotnet test</span></span> | <span data-ttu-id="f6c5d-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="f6c5d-138">dotnet watch test</span></span> |

<span data-ttu-id="f6c5d-139">Запустите `dotnet watch run` в папке *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="f6c5d-140">В выходных данных консоли будет указано, что запущен `watch`.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="f6c5d-141">Можно использовать `dotnet watch --project <PROJECT>`, чтобы указать проект для наблюдения.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="f6c5d-142">Например, при выполнении `dotnet watch --project WebApp run` из корневой папки примера приложения также будет запущен проект *WebApp*, в том числе для наблюдения.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="f6c5d-143">Изменения с помощью `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f6c5d-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="f6c5d-144">Убедитесь, что `dotnet watch` выполняется.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="f6c5d-145">Исправьте ошибку в методе `Product` для *MathController.cs* так, чтобы он возвращал произведение, а не сумму чисел.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="f6c5d-146">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-146">Save the file.</span></span> <span data-ttu-id="f6c5d-147">В выходных данных консоли будет показано, что `dotnet watch` обнаружил изменение файла и перезапустил приложение.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="f6c5d-148">Убедитесь, что `http://localhost:<port number>/api/math/product?a=4&b=5` возвращает правильный результат.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="f6c5d-149">Тестирование с помощью `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="f6c5d-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="f6c5d-150">Снова измените метод `Product` для *MathController.cs* так, чтобы он возвращал сумму чисел.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="f6c5d-151">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-151">Save the file.</span></span>
1. <span data-ttu-id="f6c5d-152">В командной оболочке перейдите в папку *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="f6c5d-153">Запустите [dotnet restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="f6c5d-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="f6c5d-154">Запустите `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="f6c5d-155">В выходных данных будет указано, что проверка не пройдена и наблюдатель ожидает изменений в файле:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="f6c5d-156">Исправьте код метода `Product` так, чтобы он возвращал произведение.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="f6c5d-157">Сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-157">Save the file.</span></span>

<span data-ttu-id="f6c5d-158">`dotnet watch` обнаруживает изменения в файле и повторно запускает тесты.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="f6c5d-159">В выходных данных консоли будет указано, что проверка пройдена.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="f6c5d-160">Настройка списка файлов для наблюдения</span><span class="sxs-lookup"><span data-stu-id="f6c5d-160">Customize files list to watch</span></span>

<span data-ttu-id="f6c5d-161">По умолчанию `dotnet-watch` отслеживает все файлы, соответствующие следующим стандартным маскам:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="f6c5d-162">Можно добавить в список наблюдения еще элементы, отредактировав файл *.csproj*.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="f6c5d-163">Элементы можно указывать по отдельности или с помощью стандартной маски.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="f6c5d-164">Удаление файлов из списка наблюдения</span><span class="sxs-lookup"><span data-stu-id="f6c5d-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="f6c5d-165">Можно настроить `dotnet-watch` так, чтобы игнорировать параметры по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="f6c5d-166">Чтобы пропускать определенные файлы, добавьте атрибут `Watch="false"` в определение элемента в файле *.csproj*:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="f6c5d-167">Пользовательское наблюдение за проектами</span><span class="sxs-lookup"><span data-stu-id="f6c5d-167">Custom watch projects</span></span>

<span data-ttu-id="f6c5d-168">`dotnet-watch` используется не только с проектами C#.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="f6c5d-169">Создайте пользовательское наблюдение за проектами для различных ситуаций.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="f6c5d-170">Вы можете использовать следующий макет проекта:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-170">Consider the following project layout:</span></span>

* <span data-ttu-id="f6c5d-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="f6c5d-171">**test/**</span></span>
  * <span data-ttu-id="f6c5d-172">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="f6c5d-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="f6c5d-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="f6c5d-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="f6c5d-174">Если вы хотите наблюдать за обоими проектами, создайте пользовательский файл проекта, настроенный для наблюдения за обоими проектами:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="f6c5d-175">Чтобы запустить наблюдение за файлами для обоих проектов, внесите изменения в папку *test*.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="f6c5d-176">Выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="f6c5d-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="f6c5d-177">VSTest выполняется при изменении любого файла в любом из тестовых проектов.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="f6c5d-178">`dotnet-watch` на GitHub</span><span class="sxs-lookup"><span data-stu-id="f6c5d-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="f6c5d-179">`dotnet-watch` предоставляется в репозитории [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch) на GitHub.</span><span class="sxs-lookup"><span data-stu-id="f6c5d-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
