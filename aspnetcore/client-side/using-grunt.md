---
title: Использование Grunt в ASP.NET Core
author: rick-anderson
description: Использование Grunt в ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
uid: client-side/using-grunt
ms.openlocfilehash: e8e4459f7fe496135d6cfd7f4ff52511a5e1c064
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628031"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="f687f-103">Использование Grunt в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f687f-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="f687f-104">Grunt — это запускатель задач JavaScript, который автоматизирует скрипты минификации, компиляцию TypeScript, контроль качества кода, предварительные процессоры CSS и практически любую рутинную работу, которая характерна для поддержки разработки клиентов.</span><span class="sxs-lookup"><span data-stu-id="f687f-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="f687f-105">Grunt полностью поддерживается в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f687f-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="f687f-106">В этом примере в качестве отправной точки используется пустой проект ASP.NET Core, чтобы продемонстрировать, как автоматизировать процесс создания клиента с нуля.</span><span class="sxs-lookup"><span data-stu-id="f687f-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="f687f-107">В готовом примере очищается целевой каталог развертывания, объединяются файлы JavaScript, проверяется качество кода, содержимое файлов JavaScript сжимается и развертывается в корне веб-приложения.</span><span class="sxs-lookup"><span data-stu-id="f687f-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="f687f-108">Мы будем использовать следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="f687f-108">We will use the following packages:</span></span>

* <span data-ttu-id="f687f-109">**grunt**: пакет запускателя задач Grunt.</span><span class="sxs-lookup"><span data-stu-id="f687f-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="f687f-110">**grunt-contrib-clean**: подключаемый модуль, который удаляет файлы или каталоги.</span><span class="sxs-lookup"><span data-stu-id="f687f-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="f687f-111">**grunt-contrib-jshint**: подключаемый модуль, который проверяет качество кода JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f687f-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="f687f-112">**grunt-contrib-concat**: подключаемый модуль, который объединяет несколько файлов в один файл.</span><span class="sxs-lookup"><span data-stu-id="f687f-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="f687f-113">**grunt-contrib-uglify**: подключаемый модуль, который минифицирует JavaScript для уменьшения размера.</span><span class="sxs-lookup"><span data-stu-id="f687f-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="f687f-114">**grunt-contrib-watch**: подключаемый модуль, который отслеживает активность файлов.</span><span class="sxs-lookup"><span data-stu-id="f687f-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="f687f-115">Подготовка приложения</span><span class="sxs-lookup"><span data-stu-id="f687f-115">Preparing the application</span></span>

<span data-ttu-id="f687f-116">Для начала настройте новое пустое веб-приложение и добавьте образцы файлов TypeScript.</span><span class="sxs-lookup"><span data-stu-id="f687f-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="f687f-117">Файлы TypeScript автоматически компилируются в JavaScript с помощью параметров Visual Studio по умолчанию и будут рассматриваться как необработанные материалы, которые следует обработать с помощью Grunt.</span><span class="sxs-lookup"><span data-stu-id="f687f-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="f687f-118">В Visual Studio создайте новый `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="f687f-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="f687f-119">В диалоговом окне **Новый проект ASP.NET** выберите **пустой** шаблон ASP.NET Core и нажмите кнопку "ОК".</span><span class="sxs-lookup"><span data-stu-id="f687f-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="f687f-120">В обозревателе решений просмотрите структуру проекта.</span><span class="sxs-lookup"><span data-stu-id="f687f-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="f687f-121">В папке `\src` содержатся пустые узлы `wwwroot` и `Dependencies`.</span><span class="sxs-lookup"><span data-stu-id="f687f-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![Пустое веб-решение](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="f687f-123">В каталог проекта добавьте новую папку с именем `TypeScript`.</span><span class="sxs-lookup"><span data-stu-id="f687f-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="f687f-124">Перед добавлением файлов убедитесь, что в Visual Studio для файлов TypeScript выбран параметр компилирования при сохранении.</span><span class="sxs-lookup"><span data-stu-id="f687f-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="f687f-125">Последовательно выберите **Сервис** > **Параметры** > **Текстовый редактор** > **Typescript** > **Проект**:</span><span class="sxs-lookup"><span data-stu-id="f687f-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![Параметры, задающие автоматическую компиляцию файлов TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="f687f-127">Щелкните правой кнопкой мыши каталог `TypeScript` и выберите в контекстном меню **Добавить > Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="f687f-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="f687f-128">Выберите пункт **Файл JavaScript** и назовите файл *Tastes.ts* (обратите внимание на расширение \*TS).</span><span class="sxs-lookup"><span data-stu-id="f687f-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="f687f-129">Скопируйте строку кода TypeScript, приведенную ниже, в файл (при сохранении новый файл *Tastes.ts* будет отображаться с источником JavaScript).</span><span class="sxs-lookup"><span data-stu-id="f687f-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="f687f-130">Добавьте второй файл в каталог **TypeScript** и назовите его `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="f687f-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="f687f-131">Скопируйте приведенный ниже код в файл.</span><span class="sxs-lookup"><span data-stu-id="f687f-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="f687f-132">Настройка NPM</span><span class="sxs-lookup"><span data-stu-id="f687f-132">Configuring NPM</span></span>

<span data-ttu-id="f687f-133">Затем настройте NPM для загрузки grunt и grunt-tasks.</span><span class="sxs-lookup"><span data-stu-id="f687f-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="f687f-134">В обозревателе решений щелкните проект правой кнопкой мыши и выберите в контекстном меню **Добавить > Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="f687f-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="f687f-135">Выберите элемент **Файл конфигурации NPM**, оставьте имя по умолчанию (*package.json*) и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="f687f-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="f687f-136">В файле *package.json* внутри объекта `devDependencies` в фигурных скобках введите "grunt".</span><span class="sxs-lookup"><span data-stu-id="f687f-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="f687f-137">Выберите `grunt` из списка IntelliSense и нажмите клавишу ВВОД.</span><span class="sxs-lookup"><span data-stu-id="f687f-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="f687f-138">Visual Studio заключит имя пакета grunt в двойные кавычки и добавит двоеточие.</span><span class="sxs-lookup"><span data-stu-id="f687f-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="f687f-139">Справа от двоеточия выберите последнюю стабильную версию пакета в верхней части списка IntelliSense (нажмите `Ctrl-Space`, если IntelliSense не отображается).</span><span class="sxs-lookup"><span data-stu-id="f687f-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="f687f-141">Для организации зависимостей NPM использует [семантическое версионирование](https://semver.org/).</span><span class="sxs-lookup"><span data-stu-id="f687f-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="f687f-142">Семантическое версионирование, также известное как SemVer, определяет пакеты со схемой нумерации \<major>.\<minor>.\<patch>. IntelliSense упрощает семантическое версионирование, отображая лишь несколько распространенных вариантов.</span><span class="sxs-lookup"><span data-stu-id="f687f-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="f687f-143">Верхний элемент в списке IntelliSense (0.4.5 в примере выше) считается последней стабильной версией пакета.</span><span class="sxs-lookup"><span data-stu-id="f687f-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="f687f-144">Символ каретки (^) соответствует самой последней основной версии, а тильда (~) — самой последней дополнительной версии.</span><span class="sxs-lookup"><span data-stu-id="f687f-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="f687f-145">Дополнительные сведения о выражениях в SemVer см. в [справочнике по средству синтаксического анализа версий SemVer NPM](https://www.npmjs.com/package/semver).</span><span class="sxs-lookup"><span data-stu-id="f687f-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="f687f-146">Добавьте дополнительные зависимости для загрузки пакетов grunt-contrib-\* для *clean*, *jshint*, *concat*, *uglify* и *watch*, как показано в примере ниже.</span><span class="sxs-lookup"><span data-stu-id="f687f-146">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="f687f-147">Версии не обязательно должны соответствовать примеру.</span><span class="sxs-lookup"><span data-stu-id="f687f-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="f687f-148">Сохраните файл *package.json*.</span><span class="sxs-lookup"><span data-stu-id="f687f-148">Save the *package.json* file.</span></span>

<span data-ttu-id="f687f-149">Пакеты для каждого элемента `devDependencies` будут загружены вместе с любыми файлами, которые требуются для каждого пакета.</span><span class="sxs-lookup"><span data-stu-id="f687f-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="f687f-150">Файлы пакета находятся в каталоге *node_modules* (нажмите кнопку **Показать все файлы** в **обозревателе решений**).</span><span class="sxs-lookup"><span data-stu-id="f687f-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![Модули узлов grunt](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="f687f-152">При необходимости можно вручную восстановить зависимости в **обозреватель решений**, щелкнув `Dependencies\NPM` правой кнопкой мыши и выбрав в контекстном меню пункт **Восстановить пакеты**.</span><span class="sxs-lookup"><span data-stu-id="f687f-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![Пункт "Восстановить пакеты"](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="f687f-154">Настройка Grunt</span><span class="sxs-lookup"><span data-stu-id="f687f-154">Configuring Grunt</span></span>

<span data-ttu-id="f687f-155">Для настройки Grunt используется манифест *Gruntfile.js*, который определяет, загружает и регистрирует задачи, которые могут выполняться вручную или могут быть настроены на автоматический запуск на основе событий в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f687f-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="f687f-156">Щелкните проект правой кнопкой мыши и выберите **Добавить** > **Новый элемент**.</span><span class="sxs-lookup"><span data-stu-id="f687f-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="f687f-157">Выберите шаблон элемента **Файл JavaScript**, переименуйте его в *Gruntfile.js* и нажмите кнопку **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="f687f-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="f687f-158">Добавьте следующий код в файл *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="f687f-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="f687f-159">Функция `initConfig` задает параметры для каждого пакета, а оставшаяся часть модуля загружает и регистрирует задачи.</span><span class="sxs-lookup"><span data-stu-id="f687f-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="f687f-160">В функции `initConfig` добавьте параметры для задачи `clean`, как показано в примере *Gruntfile.js* ниже.</span><span class="sxs-lookup"><span data-stu-id="f687f-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="f687f-161">Задача `clean` принимает массив строк каталога.</span><span class="sxs-lookup"><span data-stu-id="f687f-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="f687f-162">Эта задача удаляет файлы из каталога *wwwroot/lib* и удаляет весь каталог */temp*.</span><span class="sxs-lookup"><span data-stu-id="f687f-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="f687f-163">Под функцией `initConfig` добавьте вызов `grunt.loadNpmTasks`.</span><span class="sxs-lookup"><span data-stu-id="f687f-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="f687f-164">Это позволит подготовить задачу к запуску из Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f687f-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="f687f-165">Сохраните файл *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="f687f-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="f687f-166">Файл должен выглядеть примерно так, как показано на снимке экрана ниже.</span><span class="sxs-lookup"><span data-stu-id="f687f-166">The file should look something like the screenshot below.</span></span>

    ![Исходный файл Gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="f687f-168">Щелкните правой кнопкой мыши файл *Gruntfile.js* и выберите в контекстном меню пункт **Диспетчер выполнения задач**.</span><span class="sxs-lookup"><span data-stu-id="f687f-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="f687f-169">Откроется окно **Task Runner Explorer**.</span><span class="sxs-lookup"><span data-stu-id="f687f-169">The **Task Runner Explorer** window will open.</span></span>

    ![Пункт меню "Диспетчер выполнения задач"](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="f687f-171">В окне **Диспетчер выполнения задач** убедитесь, что `clean` отображается в разделе **Задачи**.</span><span class="sxs-lookup"><span data-stu-id="f687f-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![Список задач в диспетчере выполнения задач](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="f687f-173">Щелкните правой кнопкой мыши задачу "clean" и выберите в контекстном меню пункт **Выполнить**.</span><span class="sxs-lookup"><span data-stu-id="f687f-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="f687f-174">В командном окне отображается ход выполнения задачи.</span><span class="sxs-lookup"><span data-stu-id="f687f-174">A command window displays progress of the task.</span></span>

    ![Диспетчер выполнения задач выполняет задачу "clean"](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="f687f-176">Пока нет файлов или каталогов для очистки.</span><span class="sxs-lookup"><span data-stu-id="f687f-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="f687f-177">При желании можно вручную создать их в обозревателе решений, а затем запустить задачу очистки.</span><span class="sxs-lookup"><span data-stu-id="f687f-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="f687f-178">Добавьте в функцию `initConfig` запись для `concat`, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f687f-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="f687f-179">Массив свойств `src` перечисляет файлы, которые нужно объединить (в том порядке, в котором они должны быть объединены).</span><span class="sxs-lookup"><span data-stu-id="f687f-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="f687f-180">Свойство `dest` присваивает путь к созданному объединенному файлу.</span><span class="sxs-lookup"><span data-stu-id="f687f-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="f687f-181">Свойство `all` в приведенном выше коде является именем целевого объекта.</span><span class="sxs-lookup"><span data-stu-id="f687f-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="f687f-182">Целевые объекты используются в некоторых задачах Grunt для разрешения нескольких сред сборки.</span><span class="sxs-lookup"><span data-stu-id="f687f-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="f687f-183">Встроенные целевые объекты можно просмотреть с помощью IntelliSense. Также можно назначить собственные целевые объекты.</span><span class="sxs-lookup"><span data-stu-id="f687f-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="f687f-184">Добавьте задачу `jshint`, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f687f-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="f687f-185">Для каждого файла JavaScript, находящегося в каталоге *temp*, выполняется служебная программа jshint `code-quality`.</span><span class="sxs-lookup"><span data-stu-id="f687f-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="f687f-186">Параметр "-W069" является ошибкой, созданной jshint, когда для назначения свойства JavaScript использует синтаксис со скобками вместо точек, т. е. `Tastes["Sweet"]` вместо `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="f687f-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="f687f-187">Параметр отключает предупреждение, чтобы продолжить оставшуюся часть процесса.</span><span class="sxs-lookup"><span data-stu-id="f687f-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="f687f-188">Добавьте задачу `uglify`, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f687f-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="f687f-189">Задача уменьшает файл *combined.js*, находящийся в каталоге temp, и создает файл результатов в каталоге wwwroot/lib, следуя стандартному соглашению об именовании *\<file name\>.min.js*.</span><span class="sxs-lookup"><span data-stu-id="f687f-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="f687f-190">В вызове `grunt.loadNpmTasks`, который загружает `grunt-contrib-clean`, включите тот же вызов для jshint, concat и uglify, используя приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f687f-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="f687f-191">Сохраните файл *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="f687f-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="f687f-192">Файл должен выглядеть примерно так, как показано в примере ниже.</span><span class="sxs-lookup"><span data-stu-id="f687f-192">The file should look something like the example below.</span></span>

    ![Пример готового файла Grunt](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="f687f-194">Обратите внимание, что список задач в **диспетчере выполнения задач** содержит задачи `clean`, `concat`, `jshint` и `uglify`.</span><span class="sxs-lookup"><span data-stu-id="f687f-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="f687f-195">Выполните каждую задачу по порядку и просмотрите результаты в **обозреватель решений**.</span><span class="sxs-lookup"><span data-stu-id="f687f-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="f687f-196">Каждая задача должна выполниться без ошибок.</span><span class="sxs-lookup"><span data-stu-id="f687f-196">Each task should run without errors.</span></span>

    ![Диспетчер выполнения задач выполняет каждую задачу](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="f687f-198">Задача concat создает новый файл *combined.js* и помещает его в каталог temp.</span><span class="sxs-lookup"><span data-stu-id="f687f-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="f687f-199">Задача `jshint` просто выполняется и не создает выходные данные.</span><span class="sxs-lookup"><span data-stu-id="f687f-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="f687f-200">Задача `uglify` создает новый файл *combined.js* и помещает его в каталог *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="f687f-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="f687f-201">По завершении решение должно выглядеть примерно так, как показано на снимке экрана ниже.</span><span class="sxs-lookup"><span data-stu-id="f687f-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![Обозреватель решений после выполнения всех задач](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="f687f-203">Чтобы получить дополнительные сведения о параметрах для каждого пакета, перейдите на страницу [https://www.npmjs.com/](https://www.npmjs.com/) и выполните поиск по имени пакета в поле поиска на главной странице.</span><span class="sxs-lookup"><span data-stu-id="f687f-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="f687f-204">Например, можно выполнить поиск пакета grunt-contrib-clean, чтобы получить ссылку на документацию с описанием всех его параметров.</span><span class="sxs-lookup"><span data-stu-id="f687f-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="f687f-205">Заключение</span><span class="sxs-lookup"><span data-stu-id="f687f-205">All together now</span></span>

<span data-ttu-id="f687f-206">Используйте метод Grunt `registerTask()` для выполнения набора задач в определенной последовательности.</span><span class="sxs-lookup"><span data-stu-id="f687f-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="f687f-207">Например, чтобы выполнить приведенные выше шаги в порядке clean -> concat -> jshint -> uglify, добавьте в модуль приведенный ниже код.</span><span class="sxs-lookup"><span data-stu-id="f687f-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="f687f-208">Код следует добавить на тот же уровень, что и вызовы loadNpmTasks(), за пределами initConfig.</span><span class="sxs-lookup"><span data-stu-id="f687f-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="f687f-209">Новая задача будет отображаться в диспетчере выполнения задач в разделе "Задачи псевдонимов".</span><span class="sxs-lookup"><span data-stu-id="f687f-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="f687f-210">Можно щелкнуть правой кнопкой мыши и запустить ее так же, как и другие задачи.</span><span class="sxs-lookup"><span data-stu-id="f687f-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="f687f-211">Задача `all` будет выполнять `clean`, `concat`, `jshint` и `uglify` по порядку.</span><span class="sxs-lookup"><span data-stu-id="f687f-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![Задачи псевдонимов Grunt](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="f687f-213">Просмотр изменений</span><span class="sxs-lookup"><span data-stu-id="f687f-213">Watching for changes</span></span>

<span data-ttu-id="f687f-214">Задача `watch` отслеживает файлы и каталоги.</span><span class="sxs-lookup"><span data-stu-id="f687f-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="f687f-215">watch активирует задачи автоматически при обнаружении изменений.</span><span class="sxs-lookup"><span data-stu-id="f687f-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="f687f-216">Добавьте приведенный ниже код в initConfig для отслеживания изменений в файлах \*. js в каталоге TypeScript.</span><span class="sxs-lookup"><span data-stu-id="f687f-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="f687f-217">При изменении файла JavaScript `watch` запустит задачу `all`.</span><span class="sxs-lookup"><span data-stu-id="f687f-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="f687f-218">Добавьте вызов `loadNpmTasks()`, чтобы отобразить задачу `watch` в диспетчере выполнения задач.</span><span class="sxs-lookup"><span data-stu-id="f687f-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="f687f-219">Щелкните правой кнопкой мыши задачу watch в диспетчере выполнения задач и выберите в контекстном меню пункт "Выполнить".</span><span class="sxs-lookup"><span data-stu-id="f687f-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="f687f-220">В командном окне, в котором отображается выполняемая задача watch, будет отображаться сообщение "Waiting…".</span><span class="sxs-lookup"><span data-stu-id="f687f-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="f687f-221">Откройте один из файлов TypeScript, добавьте пробел, а затем сохраните файл.</span><span class="sxs-lookup"><span data-stu-id="f687f-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="f687f-222">Это позволит активировать задачу watch и запустить выполнение других задач в указанном порядке.</span><span class="sxs-lookup"><span data-stu-id="f687f-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="f687f-223">Пример выполнения показан на снимке экрана ниже.</span><span class="sxs-lookup"><span data-stu-id="f687f-223">The screenshot below shows a sample run.</span></span>

![Выходные данные выполняемых задач](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="f687f-225">Привязка к событиям Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f687f-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="f687f-226">Если вы не хотите запускать задачи вручную каждый раз, когда работаете в Visual Studio, привяжите задачи к событиям **До сборки**, **После сборки**, **Очистка** и **Открытие проекта**.</span><span class="sxs-lookup"><span data-stu-id="f687f-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="f687f-227">Привяжите задачу `watch`, чтобы она выполнялась при каждом открытии Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f687f-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="f687f-228">В диспетчере выполнения задач щелкните правой кнопкой мыши задачу watch и выберите в контекстном меню пункт **Привязки** > **Открытие проекта**.</span><span class="sxs-lookup"><span data-stu-id="f687f-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![Привязка задачи к событию открытия проекта](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="f687f-230">Выгрузите и снова загрузите проект.</span><span class="sxs-lookup"><span data-stu-id="f687f-230">Unload and reload the project.</span></span> <span data-ttu-id="f687f-231">При повторной загрузке проекта задача watch начинает выполняться автоматически.</span><span class="sxs-lookup"><span data-stu-id="f687f-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="f687f-232">Сводка</span><span class="sxs-lookup"><span data-stu-id="f687f-232">Summary</span></span>

<span data-ttu-id="f687f-233">Grunt — это эффективный запускатель задач, который можно использовать для автоматизации большинства задач по сборке клиента.</span><span class="sxs-lookup"><span data-stu-id="f687f-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="f687f-234">Grunt использует NPM для доставки своих пакетов, а также обеспечивает интеграцию средств с Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f687f-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="f687f-235">Диспетчер выполнения задач Visual Studio обнаруживает изменения в файлах конфигурации и предоставляет удобный интерфейс для выполнения задач, просмотра выполняемых задач и привязки задач к событиям Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f687f-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
