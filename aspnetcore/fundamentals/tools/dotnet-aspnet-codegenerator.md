---
title: Команда dotnet aspnet-codegenerator
author: rick-anderson
description: Команда dotnet aspnet-codegenerator формирует шаблоны для проектов ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 88d761b09833a14de5af9f9610753174867aa09a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059992"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="b90f6-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="b90f6-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="b90f6-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="b90f6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b90f6-105">Команда `dotnet aspnet-codegenerator` запускает подсистему формирования шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b90f6-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="b90f6-106">Команда `dotnet aspnet-codegenerator` нужна только для командной строки. Она не требуется для формирования шаблонов в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b90f6-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="b90f6-107">Эта статья относится к [пакету SDK для .NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="b90f6-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="b90f6-108">Установка aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="b90f6-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="b90f6-109">`dotnet-aspnet-codegenerator` — [глобальное средство](/dotnet/core/tools/global-tools), которое нужно установить.</span><span class="sxs-lookup"><span data-stu-id="b90f6-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="b90f6-110">Следующая команда позволяет установить последнюю стабильную версию средства `dotnet-aspnet-codegenerator`:</span><span class="sxs-lookup"><span data-stu-id="b90f6-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="b90f6-111">Приведенная ниже команда позволяет обновить `dotnet-aspnet-codegenerator` до последней стабильной версии, доступной из установленных пакетов SDK для .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b90f6-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="b90f6-112">Краткий обзор</span><span class="sxs-lookup"><span data-stu-id="b90f6-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="b90f6-113">Описание</span><span class="sxs-lookup"><span data-stu-id="b90f6-113">Description</span></span>

<span data-ttu-id="b90f6-114">Глобальная команда `dotnet aspnet-codegenerator` запускает генератор кода и подсистему формирования шаблонов ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b90f6-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="b90f6-115">Аргументы</span><span class="sxs-lookup"><span data-stu-id="b90f6-115">Arguments</span></span>

`generator`

<span data-ttu-id="b90f6-116">Запускаемый генератор кода.</span><span class="sxs-lookup"><span data-stu-id="b90f6-116">The code generator to run.</span></span> <span data-ttu-id="b90f6-117">Доступны такие генераторы:</span><span class="sxs-lookup"><span data-stu-id="b90f6-117">The following generators are available:</span></span>

| <span data-ttu-id="b90f6-118">Generator</span><span class="sxs-lookup"><span data-stu-id="b90f6-118">Generator</span></span>  | <span data-ttu-id="b90f6-119">Операция</span><span class="sxs-lookup"><span data-stu-id="b90f6-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="b90f6-120">площадь</span><span class="sxs-lookup"><span data-stu-id="b90f6-120">area</span></span>       | [<span data-ttu-id="b90f6-121">Формирует шаблон области</span><span class="sxs-lookup"><span data-stu-id="b90f6-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="b90f6-122">контроллер</span><span class="sxs-lookup"><span data-stu-id="b90f6-122">controller</span></span> | [<span data-ttu-id="b90f6-123">Формирует шаблон контроллера</span><span class="sxs-lookup"><span data-stu-id="b90f6-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="b90f6-124">удостоверение</span><span class="sxs-lookup"><span data-stu-id="b90f6-124">identity</span></span>   | [<span data-ttu-id="b90f6-125">Формирует шаблон Identity</span><span class="sxs-lookup"><span data-stu-id="b90f6-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="b90f6-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="b90f6-126">razorpage</span></span>  | [<span data-ttu-id="b90f6-127">Формирует шаблон страниц Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b90f6-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="b90f6-128">view</span><span class="sxs-lookup"><span data-stu-id="b90f6-128">view</span></span>       | [<span data-ttu-id="b90f6-129">Формирует шаблон представления</span><span class="sxs-lookup"><span data-stu-id="b90f6-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="b90f6-130">Параметры</span><span class="sxs-lookup"><span data-stu-id="b90f6-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="b90f6-131">Позволяет указать каталог пакета NuGet.</span><span class="sxs-lookup"><span data-stu-id="b90f6-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="b90f6-132">Определяет конфигурацию сборки.</span><span class="sxs-lookup"><span data-stu-id="b90f6-132">Defines the build configuration.</span></span> <span data-ttu-id="b90f6-133">Значение по умолчанию — `Debug`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="b90f6-134">[Целевая платформа](/dotnet/standard/frameworks) для использования.</span><span class="sxs-lookup"><span data-stu-id="b90f6-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="b90f6-135">Например, `net46`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="b90f6-136">Базовый путь сборки.</span><span class="sxs-lookup"><span data-stu-id="b90f6-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="b90f6-137">Выводит краткую справку по команде.</span><span class="sxs-lookup"><span data-stu-id="b90f6-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="b90f6-138">Не выполняет сборку проекта перед запуском.</span><span class="sxs-lookup"><span data-stu-id="b90f6-138">Doesn't build the project before running.</span></span> <span data-ttu-id="b90f6-139">Он также неявно задает флаг `--no-restore`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="b90f6-140">Задает путь к запускаемому файлу проекта (имя папки или полный путь).</span><span class="sxs-lookup"><span data-stu-id="b90f6-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="b90f6-141">Если значение не задано, по умолчанию используется текущий каталог.</span><span class="sxs-lookup"><span data-stu-id="b90f6-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="b90f6-142">Параметры генератора</span><span class="sxs-lookup"><span data-stu-id="b90f6-142">Generator options</span></span>

<span data-ttu-id="b90f6-143">В следующих разделах подробно описаны параметры, доступные для поддерживаемых генераторов.</span><span class="sxs-lookup"><span data-stu-id="b90f6-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="b90f6-144">Область</span><span class="sxs-lookup"><span data-stu-id="b90f6-144">Area</span></span>
* <span data-ttu-id="b90f6-145">Контроллер</span><span class="sxs-lookup"><span data-stu-id="b90f6-145">Controller</span></span>
* Identity  
* <span data-ttu-id="b90f6-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="b90f6-146">Razorpage</span></span>
* <span data-ttu-id="b90f6-147">Просмотр</span><span class="sxs-lookup"><span data-stu-id="b90f6-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="b90f6-148">Параметры области</span><span class="sxs-lookup"><span data-stu-id="b90f6-148">Area options</span></span>

<span data-ttu-id="b90f6-149">Это средство предназначено для веб-проектов ASP.NET Core с контроллерами и представлениями.</span><span class="sxs-lookup"><span data-stu-id="b90f6-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="b90f6-150">Оно не предназначено для приложений Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="b90f6-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="b90f6-151">Использование: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="b90f6-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="b90f6-152">Приведенная выше команда создает такие папки:</span><span class="sxs-lookup"><span data-stu-id="b90f6-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="b90f6-153">*Области*</span><span class="sxs-lookup"><span data-stu-id="b90f6-153">*Areas*</span></span>
  * <span data-ttu-id="b90f6-154">*AreaNameToGenerate*</span><span class="sxs-lookup"><span data-stu-id="b90f6-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="b90f6-155">*Контроллеры*</span><span class="sxs-lookup"><span data-stu-id="b90f6-155">*Controllers*</span></span>
    * <span data-ttu-id="b90f6-156">*Данные*</span><span class="sxs-lookup"><span data-stu-id="b90f6-156">*Data*</span></span>
    * <span data-ttu-id="b90f6-157">*Models*</span><span class="sxs-lookup"><span data-stu-id="b90f6-157">*Models*</span></span>
    * <span data-ttu-id="b90f6-158">*Представления*</span><span class="sxs-lookup"><span data-stu-id="b90f6-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="b90f6-159">Параметры контроллера</span><span class="sxs-lookup"><span data-stu-id="b90f6-159">Controller options</span></span>

<span data-ttu-id="b90f6-160">В таблице ниже перечислены параметры для `aspnet-codegenerator` `controller` и `razorpage`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="b90f6-161">В таблице ниже перечислены параметры только для `aspnet-codegenerator controller`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="b90f6-162">Параметр</span><span class="sxs-lookup"><span data-stu-id="b90f6-162">Option</span></span>                         | <span data-ttu-id="b90f6-163">Описание</span><span class="sxs-lookup"><span data-stu-id="b90f6-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="b90f6-164">--controllerName или -name</span><span class="sxs-lookup"><span data-stu-id="b90f6-164">--controllerName or -name</span></span>      | <span data-ttu-id="b90f6-165">Имя контроллера.</span><span class="sxs-lookup"><span data-stu-id="b90f6-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="b90f6-166">--useAsyncActions или -async</span><span class="sxs-lookup"><span data-stu-id="b90f6-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="b90f6-167">Создание асинхронных действий контроллера.</span><span class="sxs-lookup"><span data-stu-id="b90f6-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="b90f6-168">--noViews или -nv</span><span class="sxs-lookup"><span data-stu-id="b90f6-168">--noViews or -nv</span></span>               | <span data-ttu-id="b90f6-169">Представления **не** создаются.</span><span class="sxs-lookup"><span data-stu-id="b90f6-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="b90f6-170">--restWithNoViews или -api</span><span class="sxs-lookup"><span data-stu-id="b90f6-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="b90f6-171">Создание контроллера с API в стиле REST.</span><span class="sxs-lookup"><span data-stu-id="b90f6-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="b90f6-172">Используется `noViews`, а все параметры, связанные с представлением, игнорируются.</span><span class="sxs-lookup"><span data-stu-id="b90f6-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="b90f6-173">--readWriteActions или -actions</span><span class="sxs-lookup"><span data-stu-id="b90f6-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="b90f6-174">Создание контроллера с действиями чтения и записи без модели.</span><span class="sxs-lookup"><span data-stu-id="b90f6-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="b90f6-175">Чтобы получить справку по команде `aspnet-codegenerator controller`, используйте параметр `-h`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="b90f6-176">Пример `dotnet aspnet-codegenerator controller` см. в разделе [Создание модели фильма](xref:tutorials/first-mvc-app/adding-model).</span><span class="sxs-lookup"><span data-stu-id="b90f6-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="b90f6-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="b90f6-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="b90f6-178">Шаблоны для страниц Razor Pages можно формировать по отдельности. Для этого нужно указать имя новой страницы и используемый шаблон.</span><span class="sxs-lookup"><span data-stu-id="b90f6-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="b90f6-179">Поддерживаются такие шаблоны:</span><span class="sxs-lookup"><span data-stu-id="b90f6-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="b90f6-180">Например, в следующей команде используется шаблон Edit для создания *MyEdit.cshtml* и *MyEdit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="b90f6-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs* :</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="b90f6-181">Создаются следующие шаблоны, а шаблон и имя созданного файла обычно не указываются:</span><span class="sxs-lookup"><span data-stu-id="b90f6-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="b90f6-182">В таблице ниже перечислены параметры для `aspnet-codegenerator` `razorpage` и `controller`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="b90f6-183">В таблице ниже перечислены параметры только для `aspnet-codegenerator razorpage`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="b90f6-184">Параметр</span><span class="sxs-lookup"><span data-stu-id="b90f6-184">Option</span></span>                        | <span data-ttu-id="b90f6-185">Описание</span><span class="sxs-lookup"><span data-stu-id="b90f6-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="b90f6-186">--namespaceName или -namespace</span><span class="sxs-lookup"><span data-stu-id="b90f6-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="b90f6-187">Имя пространства имен, используемого для созданной модели PageModel.</span><span class="sxs-lookup"><span data-stu-id="b90f6-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="b90f6-188">--partialView или -partial</span><span class="sxs-lookup"><span data-stu-id="b90f6-188">--partialView or -partial</span></span>     | <span data-ttu-id="b90f6-189">Создание частичного представления.</span><span class="sxs-lookup"><span data-stu-id="b90f6-189">Generate a partial view.</span></span> <span data-ttu-id="b90f6-190">Если указан этот параметр, параметры макета -l и -udl игнорируются.</span><span class="sxs-lookup"><span data-stu-id="b90f6-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="b90f6-191">--noPageModel или -npm</span><span class="sxs-lookup"><span data-stu-id="b90f6-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="b90f6-192">Параметр, при использовании которого не создается класс PageModel для пустого шаблона.</span><span class="sxs-lookup"><span data-stu-id="b90f6-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="b90f6-193">Чтобы получить справку по команде `aspnet-codegenerator razorpage`, используйте параметр `-h`.</span><span class="sxs-lookup"><span data-stu-id="b90f6-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="b90f6-194">Пример `dotnet aspnet-codegenerator razorpage` см. в разделе [Создание модели фильма](xref:tutorials/razor-pages/model).</span><span class="sxs-lookup"><span data-stu-id="b90f6-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="b90f6-195">См. [Формирование шаблонов Identity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="b90f6-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
