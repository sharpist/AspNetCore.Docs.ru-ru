---
title: Команда dotnet aspnet-codegenerator
author: rick-anderson
description: Команда dotnet aspnet-codegenerator формирует шаблоны для проектов ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920707"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="cc0b0-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cc0b0-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="cc0b0-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="cc0b0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cc0b0-105">Команда `dotnet aspnet-codegenerator` запускает подсистему формирования шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="cc0b0-106">Команда `dotnet aspnet-codegenerator` нужна только для командной строки. Она не требуется для формирования шаблонов в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="cc0b0-107">Установка и обновление aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cc0b0-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="cc0b0-108">Установите [пакет SDK для .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="cc0b0-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="cc0b0-109">`dotnet-aspnet-codegenerator` — [глобальное средство](/dotnet/core/tools/global-tools), которое нужно установить.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="cc0b0-110">Следующая команда позволяет установить последнюю стабильную версию средства `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="cc0b0-111">Приведенная ниже команда позволяет обновить `dotnet-aspnet-codegenerator` до последней стабильной версии, доступной из установленных пакетов SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="cc0b0-112">Удаление aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cc0b0-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="cc0b0-113">Для устранения проблем, возможно, потребуется удалить `aspnet-codegenerator`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="cc0b0-114">Например, если вы установили предварительную версию `aspnet-codegenerator`, удалите ее перед установкой выпущенной версии.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="cc0b0-115">Следующая команда позволяет удалить средство `dotnet-aspnet-codegenerator` и установить последнюю стабильную версию:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="cc0b0-116">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="cc0b0-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="cc0b0-117">Описание</span><span class="sxs-lookup"><span data-stu-id="cc0b0-117">Description</span></span>

<span data-ttu-id="cc0b0-118">Глобальная команда `dotnet aspnet-codegenerator` запускает генератор кода и подсистему формирования шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="cc0b0-119">Аргументы</span><span class="sxs-lookup"><span data-stu-id="cc0b0-119">Arguments</span></span>

`generator`

<span data-ttu-id="cc0b0-120">Запускаемый генератор кода.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-120">The code generator to run.</span></span> <span data-ttu-id="cc0b0-121">Доступны такие генераторы:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-121">The following generators are available:</span></span>

| <span data-ttu-id="cc0b0-122">Generator</span><span class="sxs-lookup"><span data-stu-id="cc0b0-122">Generator</span></span>  | <span data-ttu-id="cc0b0-123">Операция</span><span class="sxs-lookup"><span data-stu-id="cc0b0-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="cc0b0-124">площадь</span><span class="sxs-lookup"><span data-stu-id="cc0b0-124">area</span></span>       | [<span data-ttu-id="cc0b0-125">Формирует шаблон области</span><span class="sxs-lookup"><span data-stu-id="cc0b0-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="cc0b0-126">контроллер</span><span class="sxs-lookup"><span data-stu-id="cc0b0-126">controller</span></span> | [<span data-ttu-id="cc0b0-127">Формирует шаблон контроллера</span><span class="sxs-lookup"><span data-stu-id="cc0b0-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="cc0b0-128">удостоверение</span><span class="sxs-lookup"><span data-stu-id="cc0b0-128">identity</span></span>   | [<span data-ttu-id="cc0b0-129">Формирует шаблон Identity</span><span class="sxs-lookup"><span data-stu-id="cc0b0-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="cc0b0-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="cc0b0-130">razorpage</span></span>  | [<span data-ttu-id="cc0b0-131">Формирует шаблон страниц Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cc0b0-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="cc0b0-132">view</span><span class="sxs-lookup"><span data-stu-id="cc0b0-132">view</span></span>       | [<span data-ttu-id="cc0b0-133">Формирует шаблон представления</span><span class="sxs-lookup"><span data-stu-id="cc0b0-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="cc0b0-134">Параметры</span><span class="sxs-lookup"><span data-stu-id="cc0b0-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="cc0b0-135">Позволяет указать каталог пакета NuGet.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="cc0b0-136">Определяет конфигурацию сборки.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-136">Defines the build configuration.</span></span> <span data-ttu-id="cc0b0-137">Значение по умолчанию — `Debug`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="cc0b0-138">[Целевая платформа](/dotnet/standard/frameworks) для использования.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="cc0b0-139">Например, `net46`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="cc0b0-140">Базовый путь сборки.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="cc0b0-141">Выводит краткую справку по команде.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="cc0b0-142">Не выполняет сборку проекта перед запуском.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-142">Doesn't build the project before running.</span></span> <span data-ttu-id="cc0b0-143">Он также неявно задает флаг `--no-restore`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="cc0b0-144">Задает путь к запускаемому файлу проекта (имя папки или полный путь).</span><span class="sxs-lookup"><span data-stu-id="cc0b0-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="cc0b0-145">Если значение не задано, по умолчанию используется текущий каталог.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="cc0b0-146">Параметры генератора</span><span class="sxs-lookup"><span data-stu-id="cc0b0-146">Generator options</span></span>

<span data-ttu-id="cc0b0-147">В следующих разделах подробно описаны параметры, доступные для поддерживаемых генераторов.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="cc0b0-148">Область</span><span class="sxs-lookup"><span data-stu-id="cc0b0-148">Area</span></span>
* <span data-ttu-id="cc0b0-149">Контроллер</span><span class="sxs-lookup"><span data-stu-id="cc0b0-149">Controller</span></span>
* Identity  
* <span data-ttu-id="cc0b0-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="cc0b0-150">Razorpage</span></span>
* <span data-ttu-id="cc0b0-151">Просмотр</span><span class="sxs-lookup"><span data-stu-id="cc0b0-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="cc0b0-152">Параметры области</span><span class="sxs-lookup"><span data-stu-id="cc0b0-152">Area options</span></span>

<span data-ttu-id="cc0b0-153">Это средство предназначено для веб-проектов ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="cc0b0-154">Оно не предназначено для приложений Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="cc0b0-155">Использование: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="cc0b0-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="cc0b0-156">Приведенная выше команда создает такие папки:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="cc0b0-157">*Области*</span><span class="sxs-lookup"><span data-stu-id="cc0b0-157">*Areas*</span></span>
  * <span data-ttu-id="cc0b0-158">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="cc0b0-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="cc0b0-159">*Контроллеры*</span><span class="sxs-lookup"><span data-stu-id="cc0b0-159">*Controllers*</span></span>
    * <span data-ttu-id="cc0b0-160">*Данные*</span><span class="sxs-lookup"><span data-stu-id="cc0b0-160">*Data*</span></span>
    * <span data-ttu-id="cc0b0-161">*Models*</span><span class="sxs-lookup"><span data-stu-id="cc0b0-161">*Models*</span></span>
    * <span data-ttu-id="cc0b0-162">*Представления*</span><span class="sxs-lookup"><span data-stu-id="cc0b0-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="cc0b0-163">Параметры контроллера</span><span class="sxs-lookup"><span data-stu-id="cc0b0-163">Controller options</span></span>

<span data-ttu-id="cc0b0-164">В таблице ниже перечислены параметры для `aspnet-codegenerator` `controller` и `razorpage`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="cc0b0-165">В таблице ниже перечислены параметры только для `aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="cc0b0-166">Параметр</span><span class="sxs-lookup"><span data-stu-id="cc0b0-166">Option</span></span>                         | <span data-ttu-id="cc0b0-167">Описание</span><span class="sxs-lookup"><span data-stu-id="cc0b0-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="cc0b0-168">--controllerName или -name</span><span class="sxs-lookup"><span data-stu-id="cc0b0-168">--controllerName or -name</span></span>      | <span data-ttu-id="cc0b0-169">Имя контроллера.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="cc0b0-170">--useAsyncActions или -async</span><span class="sxs-lookup"><span data-stu-id="cc0b0-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="cc0b0-171">Создание асинхронных действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="cc0b0-172">--noViews или -nv</span><span class="sxs-lookup"><span data-stu-id="cc0b0-172">--noViews or -nv</span></span>               | <span data-ttu-id="cc0b0-173">Представления **не** создаются.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="cc0b0-174">--restWithNoViews или -api</span><span class="sxs-lookup"><span data-stu-id="cc0b0-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="cc0b0-175">Создание контроллера с API в стиле REST.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="cc0b0-176">Используется `noViews`, а все параметры, связанные с представлением, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="cc0b0-177">--readWriteActions или -actions</span><span class="sxs-lookup"><span data-stu-id="cc0b0-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="cc0b0-178">Создание контроллера с действиями чтения и записи без модели.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="cc0b0-179">Чтобы получить справку по команде `aspnet-codegenerator controller`, используйте параметр `-h`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="cc0b0-180">Пример `dotnet aspnet-codegenerator controller` см. в разделе [Создание модели фильма](xref:tutorials/first-mvc-app/adding-model).</span><span class="sxs-lookup"><span data-stu-id="cc0b0-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="cc0b0-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="cc0b0-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="cc0b0-182">Шаблоны для страниц Razor Pages можно формировать по отдельности. Для этого нужно указать имя новой страницы и используемый шаблон.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="cc0b0-183">Поддерживаются такие шаблоны:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="cc0b0-184">Например, в следующей команде используется шаблон Edit для создания *MyEdit.cshtml* и *MyEdit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="cc0b0-185">Создаются следующие шаблоны, а шаблон и имя созданного файла обычно не указываются:</span><span class="sxs-lookup"><span data-stu-id="cc0b0-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="cc0b0-186">В таблице ниже перечислены параметры для `aspnet-codegenerator` `razorpage` и `controller`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="cc0b0-187">В таблице ниже перечислены параметры только для `aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="cc0b0-188">Параметр</span><span class="sxs-lookup"><span data-stu-id="cc0b0-188">Option</span></span>                        | <span data-ttu-id="cc0b0-189">Описание</span><span class="sxs-lookup"><span data-stu-id="cc0b0-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="cc0b0-190">--namespaceName или -namespace</span><span class="sxs-lookup"><span data-stu-id="cc0b0-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="cc0b0-191">Имя пространства имен, используемого для созданной модели PageModel.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="cc0b0-192">--partialView или -partial</span><span class="sxs-lookup"><span data-stu-id="cc0b0-192">--partialView or -partial</span></span>     | <span data-ttu-id="cc0b0-193">Создание частичного представления.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-193">Generate a partial view.</span></span> <span data-ttu-id="cc0b0-194">Если указан этот параметр, параметры макета -l и -udl игнорируются.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="cc0b0-195">--noPageModel или -npm</span><span class="sxs-lookup"><span data-stu-id="cc0b0-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="cc0b0-196">Параметр, при использовании которого не создается класс PageModel для пустого шаблона.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="cc0b0-197">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`.</span><span class="sxs-lookup"><span data-stu-id="cc0b0-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="cc0b0-198">Пример `dotnet aspnet-codegenerator razorpage` см. в разделе [Создание модели фильма](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="cc0b0-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="cc0b0-199">См. [Формирование шаблонов Identity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="cc0b0-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
