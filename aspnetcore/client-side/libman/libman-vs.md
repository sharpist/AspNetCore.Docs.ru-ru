---
title: Использование LibMan с ASP.NET Core в Visual Studio
author: scottaddie
description: Сведения об использовании LibMan в проекте ASP.NET Core с Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: 1c97f5d7fbf64c5043e6d2277091b9a477833bf1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054714"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="39893-103">Использование LibMan с ASP.NET Core в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39893-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="39893-104">Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)</span><span class="sxs-lookup"><span data-stu-id="39893-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="39893-105">В Visual Studio имеется встроенная поддержка [LibMan](xref:client-side/libman/index) в проектах ASP.NET Core, которая включает в себя:</span><span class="sxs-lookup"><span data-stu-id="39893-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="39893-106">поддержку настройки и выполнения операций восстановления LibMan при сборке;</span><span class="sxs-lookup"><span data-stu-id="39893-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="39893-107">пункты меню для запуска операций восстановления и очистки LibMan;</span><span class="sxs-lookup"><span data-stu-id="39893-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="39893-108">диалоговое окно для поиска библиотек и добавления файлов в проект;</span><span class="sxs-lookup"><span data-stu-id="39893-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="39893-109">поддержку редактирования *libman.json* &mdash; файла манифеста LibMan.</span><span class="sxs-lookup"><span data-stu-id="39893-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="39893-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) ([описание загрузки](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39893-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39893-111">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="39893-111">Prerequisites</span></span>

* <span data-ttu-id="39893-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) с рабочей нагрузкой **ASP.NET и веб-разработка**</span><span class="sxs-lookup"><span data-stu-id="39893-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="39893-113">Добавление файлов библиотеки</span><span class="sxs-lookup"><span data-stu-id="39893-113">Add library files</span></span>

<span data-ttu-id="39893-114">Файлы библиотек можно добавлять в проект ASP.NET Core двумя разными способами:</span><span class="sxs-lookup"><span data-stu-id="39893-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="39893-115">Используйте диалоговое окно "Добавьте клиентские библиотеки".</span><span class="sxs-lookup"><span data-stu-id="39893-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="39893-116">Настройте записи в файле манифеста LibMan вручную.</span><span class="sxs-lookup"><span data-stu-id="39893-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="39893-117">Использование диалогового окна "Добавьте клиентские библиотеки"</span><span class="sxs-lookup"><span data-stu-id="39893-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="39893-118">Чтобы установить клиентскую библиотеку, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39893-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="39893-119">В **обозревателе решений** щелкните правой кнопкой мыши папку проекта, в которую нужно добавить файлы.</span><span class="sxs-lookup"><span data-stu-id="39893-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="39893-120">Выберите пункты **Добавить** > **Клиентская библиотека**.</span><span class="sxs-lookup"><span data-stu-id="39893-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="39893-121">Появится диалоговое окно **Добавьте клиентские библиотеки**.</span><span class="sxs-lookup"><span data-stu-id="39893-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Диалоговое окно "Добавьте клиентские библиотеки"](_static/add-library-dialog.png)

* <span data-ttu-id="39893-123">В раскрывающемся списке **Поставщик** выберите поставщика библиотеки.</span><span class="sxs-lookup"><span data-stu-id="39893-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="39893-124">Поставщик по умолчанию — CDNJS.</span><span class="sxs-lookup"><span data-stu-id="39893-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="39893-125">В текстовом поле **Библиотека** введите имя библиотеки, которую нужно получить.</span><span class="sxs-lookup"><span data-stu-id="39893-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="39893-126">IntelliSense предлагает список библиотек, имена которых начинаются с введенного текста.</span><span class="sxs-lookup"><span data-stu-id="39893-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="39893-127">Выберите библиотеку в списке IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="39893-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="39893-128">Обратите внимание, что после имени библиотеки стоит символ `@` и номер последней стабильной версии, известной выбранному поставщику.</span><span class="sxs-lookup"><span data-stu-id="39893-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="39893-129">Решите, какие файлы следует включить.</span><span class="sxs-lookup"><span data-stu-id="39893-129">Decide which files to include:</span></span>
  * <span data-ttu-id="39893-130">Установите переключатель в положение **Включить все файлы библиотеки**, чтобы включить все файлы библиотеки.</span><span class="sxs-lookup"><span data-stu-id="39893-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="39893-131">Установите переключатель в положение **Выбрать определенные файлы**, чтобы включить часть файлов библиотеки.</span><span class="sxs-lookup"><span data-stu-id="39893-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="39893-132">После этого станет доступно дерево выбора файлов.</span><span class="sxs-lookup"><span data-stu-id="39893-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="39893-133">Установите флажки слева от имен файлов, которые нужно скачать.</span><span class="sxs-lookup"><span data-stu-id="39893-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="39893-134">В текстовом поле **Целевое расположение** укажите папку проекта, в которой необходимо сохранить файлы.</span><span class="sxs-lookup"><span data-stu-id="39893-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="39893-135">Каждую библиотеку рекомендуется хранить в отдельной папке.</span><span class="sxs-lookup"><span data-stu-id="39893-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="39893-136">Предлагаемое **целевое расположение** зависит от того, откуда было запущено диалоговое окно.</span><span class="sxs-lookup"><span data-stu-id="39893-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="39893-137">При запуске из корневого каталога проекта:</span><span class="sxs-lookup"><span data-stu-id="39893-137">If launched from the project root:</span></span>
    * <span data-ttu-id="39893-138">используется папка *wwwroot/lib*, если *wwwroot* существует;</span><span class="sxs-lookup"><span data-stu-id="39893-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="39893-139">используется папка *lib*, если *wwwroot* не существует.</span><span class="sxs-lookup"><span data-stu-id="39893-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="39893-140">При запуске из папки проекта используется соответствующее имя папки.</span><span class="sxs-lookup"><span data-stu-id="39893-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="39893-141">После папки указывается имя библиотеки.</span><span class="sxs-lookup"><span data-stu-id="39893-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="39893-142">В приведенной ниже таблице показаны папки, предлагаемые при установке jQuery в проекте Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="39893-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="39893-143">Место запуска</span><span class="sxs-lookup"><span data-stu-id="39893-143">Launch location</span></span>                           |<span data-ttu-id="39893-144">Предлагаемая папка</span><span class="sxs-lookup"><span data-stu-id="39893-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="39893-145">корневой каталог проекта (если *wwwroot* существует)</span><span class="sxs-lookup"><span data-stu-id="39893-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="39893-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="39893-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="39893-147">корневой каталог проекта (если *wwwroot* не существует)</span><span class="sxs-lookup"><span data-stu-id="39893-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="39893-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="39893-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="39893-149">Папка *Pages* проекта</span><span class="sxs-lookup"><span data-stu-id="39893-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="39893-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="39893-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="39893-151">Нажмите кнопку **Установить**, чтобы скачать файлы в соответствии с конфигурацией в файле *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="39893-152">Ознакомьтесь со сведениями об установке в канале **Диспетчер библиотек** окна **Выходные данные**.</span><span class="sxs-lookup"><span data-stu-id="39893-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="39893-153">Пример:</span><span class="sxs-lookup"><span data-stu-id="39893-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="39893-154">Настройка записей в файле манифеста LibMan вручную</span><span class="sxs-lookup"><span data-stu-id="39893-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="39893-155">Все операции LibMan в Visual Studio основаны на содержимом манифеста LibMan в корневом каталоге проекта (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="39893-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="39893-156">Вы можете вручную изменить файл *libman.json*, чтобы настроить файлы библиотек для проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="39893-157">Visual Studio восстанавливает все файлы библиотек после сохранения файла *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="39893-158">Открыть файл *libman.json* для изменения можно указанными ниже способами.</span><span class="sxs-lookup"><span data-stu-id="39893-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="39893-159">Дважды щелкните файл *libman.json* в **обозревателе решений**.</span><span class="sxs-lookup"><span data-stu-id="39893-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="39893-160">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Управление клиентскими библиотеками**.</span><span class="sxs-lookup"><span data-stu-id="39893-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="39893-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="39893-161">**&#8224;**</span></span>
* <span data-ttu-id="39893-162">В меню **Проект** в Visual Studio выберите пункт **Управление клиентскими библиотеками**.</span><span class="sxs-lookup"><span data-stu-id="39893-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="39893-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="39893-163">**&#8224;**</span></span>

<span data-ttu-id="39893-164">**&#8224;** Если файла *libman.json* еще нет в корневом каталоге проекта, он будет создан с содержимым по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="39893-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="39893-165">В Visual Studio предлагаются широкие возможности редактирования JSON, такие как раскраска, форматирование, IntelliSense и проверка схемы.</span><span class="sxs-lookup"><span data-stu-id="39893-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="39893-166">Схему JSON манифеста LibMan можно найти на странице [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="39893-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="39893-167">При использовании приведенного ниже файла манифеста LibMan извлекает файлы в соответствии с конфигурацией, определенной в свойстве `libraries`.</span><span class="sxs-lookup"><span data-stu-id="39893-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="39893-168">Далее приводится описание объектных литералов, определенных в `libraries`.</span><span class="sxs-lookup"><span data-stu-id="39893-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="39893-169">От поставщика CDNJS получается подмножество файлов [jQuery](https://jquery.com/) версии 3.3.1.</span><span class="sxs-lookup"><span data-stu-id="39893-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="39893-170">Это подмножество определено в свойстве `files` &mdash; *jquery.min.js*, *jquery.js* и *jquery.min.map*.</span><span class="sxs-lookup"><span data-stu-id="39893-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="39893-171">Файлы помещаются в папку *wwwroot/lib/jquery* проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="39893-172">Все файлы [Bootstrap](https://getbootstrap.com/) версии 4.1.3 получаются и помещаются в папку *wwwroot/lib/bootstrap*.</span><span class="sxs-lookup"><span data-stu-id="39893-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="39893-173">Свойство `provider` объектного литерала переопределяет значение свойства `defaultProvider`.</span><span class="sxs-lookup"><span data-stu-id="39893-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="39893-174">LibMan получает файлы Bootstrap от поставщика unpkg.</span><span class="sxs-lookup"><span data-stu-id="39893-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="39893-175">Подмножество файлов [Lodash](https://lodash.com/) утверждается руководством организации.</span><span class="sxs-lookup"><span data-stu-id="39893-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="39893-176">Файлы *lodash.js* и *lodash.min.js* получаются из папки *C:\\temp\\lodash\\* локальной файловой системы.</span><span class="sxs-lookup"><span data-stu-id="39893-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="39893-177">Они копируются в папку *wwwroot/lib/lodash* проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="39893-178">LibMan поддерживает только одну версию каждой библиотеки от каждого поставщика.</span><span class="sxs-lookup"><span data-stu-id="39893-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="39893-179">Файл *libman.json* не пройдет проверку схемы, если он содержит две библиотеки с одним и тем же именем для данного поставщика.</span><span class="sxs-lookup"><span data-stu-id="39893-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="39893-180">Восстановление файлов библиотек</span><span class="sxs-lookup"><span data-stu-id="39893-180">Restore library files</span></span>

<span data-ttu-id="39893-181">Для восстановления файлов библиотек из Visual Studio в корневом каталоге проекта должен быть правильный файл *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="39893-182">Восстановленные файлы помещаются в проект в расположение, указанное для каждой библиотеки.</span><span class="sxs-lookup"><span data-stu-id="39893-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="39893-183">Файлы библиотек можно восстановить в проекте ASP.NET Core двумя способами:</span><span class="sxs-lookup"><span data-stu-id="39893-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="39893-184">Восстановление файлов во время сборки</span><span class="sxs-lookup"><span data-stu-id="39893-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="39893-185">Восстановление файлов вручную</span><span class="sxs-lookup"><span data-stu-id="39893-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="39893-186">Восстановление файлов во время сборки</span><span class="sxs-lookup"><span data-stu-id="39893-186">Restore files during build</span></span>

<span data-ttu-id="39893-187">LibMan может восстанавливать определенные файлы библиотек в процессе сборки.</span><span class="sxs-lookup"><span data-stu-id="39893-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="39893-188">По умолчанию *восстановление при сборке* отключено.</span><span class="sxs-lookup"><span data-stu-id="39893-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="39893-189">Чтобы включить и протестировать восстановление при сборке, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39893-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="39893-190">В **обозревателе решений** щелкните файл *libman.json* правой кнопкой мыши и выберите в контекстном меню пункт **Включить восстановление клиентских библиотек при сборке**.</span><span class="sxs-lookup"><span data-stu-id="39893-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="39893-191">При появлении запроса на установку пакета NuGet нажмите кнопку **Да**.</span><span class="sxs-lookup"><span data-stu-id="39893-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="39893-192">Пакет NuGet [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) добавится в проект:</span><span class="sxs-lookup"><span data-stu-id="39893-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="39893-193">Выполните сборку проекта, чтобы убедиться в том, что диспетчер LibMan восстановил файлы.</span><span class="sxs-lookup"><span data-stu-id="39893-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="39893-194">Пакет `Microsoft.Web.LibraryManager.Build` внедряет целевой объект MSBuild, который запускает LibMan во время операции сборки проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="39893-195">В канале **Сборка** окна **Выходные данные** просмотрите журнал действий LibMan:</span><span class="sxs-lookup"><span data-stu-id="39893-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="39893-196">После включения восстановления при сборке в контекстном меню файла *libman.json* появляется пункт **Отключить восстановление клиентских библиотек при сборке**.</span><span class="sxs-lookup"><span data-stu-id="39893-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="39893-197">При его выборе ссылка на пакет `Microsoft.Web.LibraryManager.Build` удаляется из файла проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="39893-198">В результате клиентские библиотеки больше не будут восстанавливаться при каждой сборке.</span><span class="sxs-lookup"><span data-stu-id="39893-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="39893-199">Независимо от того, включено ли восстановление при сборке, вы можете в любой момент выполнить восстановление вручную из контекстного меню файла *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="39893-200">Дополнительные сведения см. в разделе [Восстановление файлов вручную](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="39893-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="39893-201">Восстановление файлов вручную</span><span class="sxs-lookup"><span data-stu-id="39893-201">Restore files manually</span></span>

<span data-ttu-id="39893-202">Чтобы вручную восстановить файлы библиотек, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39893-202">To manually restore library files:</span></span>

* <span data-ttu-id="39893-203">Для всех проектов в решении:</span><span class="sxs-lookup"><span data-stu-id="39893-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="39893-204">В **обозревателе решений** щелкните правой кнопкой мыши имя решения.</span><span class="sxs-lookup"><span data-stu-id="39893-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="39893-205">Выберите пункт **Восстановить клиентские библиотеки**.</span><span class="sxs-lookup"><span data-stu-id="39893-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="39893-206">Для определенного проекта:</span><span class="sxs-lookup"><span data-stu-id="39893-206">For a specific project:</span></span>
  * <span data-ttu-id="39893-207">В **обозревателе решений** щелкните правой кнопкой мыши файл *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="39893-208">Выберите пункт **Восстановить клиентские библиотеки**.</span><span class="sxs-lookup"><span data-stu-id="39893-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="39893-209">Во время операции восстановления происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="39893-209">While the restore operation is running:</span></span>

* <span data-ttu-id="39893-210">В строке состояния Visual Studio будет анимироваться значок Центра состояния задач (TSC) с сообщением *Операция восстановления начата*.</span><span class="sxs-lookup"><span data-stu-id="39893-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="39893-211">Если щелкнуть значок, откроется подсказка со списком известных фоновых задач.</span><span class="sxs-lookup"><span data-stu-id="39893-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="39893-212">В строку состояния и канал **Диспетчер библиотек** окна **Выходные данные** будут передаваться сообщения.</span><span class="sxs-lookup"><span data-stu-id="39893-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="39893-213">Пример:</span><span class="sxs-lookup"><span data-stu-id="39893-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="39893-214">Удаление файлов библиотек</span><span class="sxs-lookup"><span data-stu-id="39893-214">Delete library files</span></span>

<span data-ttu-id="39893-215">Чтобы произвести *операцию очистки*, которая удаляет файлы библиотек, восстановленные ранее в Visual Studio, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39893-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="39893-216">В **обозревателе решений** щелкните правой кнопкой мыши файл *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="39893-217">Выберите пункт **Очистить клиентские библиотеки**.</span><span class="sxs-lookup"><span data-stu-id="39893-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="39893-218">Во избежание непреднамеренного удаления файлов, не относящихся к библиотекам, операция очистки не удаляет каталоги полностью.</span><span class="sxs-lookup"><span data-stu-id="39893-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="39893-219">Она удаляет только те файлы, которые были включены в предыдущую операцию восстановления.</span><span class="sxs-lookup"><span data-stu-id="39893-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="39893-220">Во время операции очистки происходит следующее:</span><span class="sxs-lookup"><span data-stu-id="39893-220">While the clean operation is running:</span></span>

* <span data-ttu-id="39893-221">В строке состояния Visual Studio будет анимироваться значок Центра состояния задач с сообщением *Начата операция с клиентскими библиотеками*.</span><span class="sxs-lookup"><span data-stu-id="39893-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="39893-222">Если щелкнуть значок, откроется подсказка со списком известных фоновых задач.</span><span class="sxs-lookup"><span data-stu-id="39893-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="39893-223">В строку состояния и канал **Диспетчер библиотек** окна **Выходные данные** передаются сообщения.</span><span class="sxs-lookup"><span data-stu-id="39893-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="39893-224">Пример:</span><span class="sxs-lookup"><span data-stu-id="39893-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="39893-225">Операция очистки удаляет файлы только из проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="39893-226">При этом файлы библиотек остаются в кэше для более быстрого извлечения при последующих операциях восстановления.</span><span class="sxs-lookup"><span data-stu-id="39893-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="39893-227">Для управления файлами библиотек, хранящимися в кэше локального компьютера, используйте [интерфейс командной строки LibMan](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="39893-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="39893-228">Удаление файлов библиотек</span><span class="sxs-lookup"><span data-stu-id="39893-228">Uninstall library files</span></span>

<span data-ttu-id="39893-229">Чтобы удалить файлы библиотек, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39893-229">To uninstall library files:</span></span>

* <span data-ttu-id="39893-230">Откройте файл *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-230">Open *libman.json*.</span></span>
* <span data-ttu-id="39893-231">Поместите курсор внутри соответствующего объектного литерала `libraries`.</span><span class="sxs-lookup"><span data-stu-id="39893-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="39893-232">Щелкните значок лампочки, появившийся в левом поле и нажмите **Удалить \<library_name>@\<library_version>** .</span><span class="sxs-lookup"><span data-stu-id="39893-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Команда удаления библиотеки в контекстном меню](_static/uninstall-menu-option.png)

<span data-ttu-id="39893-234">Кроме того, вы можете вручную изменить и сохранить манифест LibMan (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="39893-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="39893-235">[Операция восстановления](#restore-library-files) выполняется при сохранении файла.</span><span class="sxs-lookup"><span data-stu-id="39893-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="39893-236">Файлы библиотеки, которые больше не определены в файле *libman.json*, удаляются из проекта.</span><span class="sxs-lookup"><span data-stu-id="39893-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="39893-237">Обновление версии библиотеки</span><span class="sxs-lookup"><span data-stu-id="39893-237">Update library version</span></span>

<span data-ttu-id="39893-238">Чтобы проверить наличие новой версии библиотеки, выполните указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="39893-238">To check for an updated library version:</span></span>

* <span data-ttu-id="39893-239">Откройте файл *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-239">Open *libman.json*.</span></span>
* <span data-ttu-id="39893-240">Поместите курсор внутри соответствующего объектного литерала `libraries`.</span><span class="sxs-lookup"><span data-stu-id="39893-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="39893-241">Щелкните значок лампочки, появившийся в левом поле.</span><span class="sxs-lookup"><span data-stu-id="39893-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="39893-242">Наведите указатель на пункт **Проверить обновления**.</span><span class="sxs-lookup"><span data-stu-id="39893-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="39893-243">LibMan проверит наличие версии библиотеки, более поздней, чем установленная.</span><span class="sxs-lookup"><span data-stu-id="39893-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="39893-244">Возможен один из следующих результатов:</span><span class="sxs-lookup"><span data-stu-id="39893-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="39893-245">Если установлена последняя версия, появится сообщение **Обновления не найдены**.</span><span class="sxs-lookup"><span data-stu-id="39893-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="39893-246">Будет показана последняя стабильная версия, если она еще не установлена.</span><span class="sxs-lookup"><span data-stu-id="39893-246">The latest stable version is displayed if not already installed.</span></span>

  ![Пункт "Проверить обновления" в контекстном меню](_static/update-menu-option.png)

* <span data-ttu-id="39893-248">Если имеется предварительный выпуск, более поздний, чем установленная версия, он будет показан.</span><span class="sxs-lookup"><span data-stu-id="39893-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="39893-249">Чтобы перейти на более старую версию библиотеки, вручную измените файл *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="39893-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="39893-250">При сохранении этого файла [операция восстановления](#restore-library-files) LibMan выполняет следующие действия:</span><span class="sxs-lookup"><span data-stu-id="39893-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="39893-251">удаляет лишние файлы из предыдущей версии;</span><span class="sxs-lookup"><span data-stu-id="39893-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="39893-252">добавляет новые и обновленные файлы из новой версии.</span><span class="sxs-lookup"><span data-stu-id="39893-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39893-253">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="39893-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="39893-254">Репозиторий LibMan на GitHub</span><span class="sxs-lookup"><span data-stu-id="39893-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
