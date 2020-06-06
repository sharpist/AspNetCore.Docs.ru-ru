::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c7da6-101">Запуск шаблона идентификации:</span><span class="sxs-lookup"><span data-stu-id="c7da6-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c7da6-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c7da6-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c7da6-103">В **Обозреватель решений**щелкните правой кнопкой мыши проект > **Добавить** > **Новый**шаблонный элемент.</span><span class="sxs-lookup"><span data-stu-id="c7da6-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c7da6-104">В левой области диалогового окна **Добавление шаблона** выберите **удостоверение** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c7da6-105">В диалоговом окне **Добавление удостоверения** выберите нужные параметры.</span><span class="sxs-lookup"><span data-stu-id="c7da6-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c7da6-106">Выберите существующую страницу макета, чтобы файл макета не перезаписывался неверной разметкой.</span><span class="sxs-lookup"><span data-stu-id="c7da6-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="c7da6-107">При выборе существующего файла \* \_ Layout. cshtml\* он **не** перезаписывается.</span><span class="sxs-lookup"><span data-stu-id="c7da6-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="c7da6-108">Пример:</span><span class="sxs-lookup"><span data-stu-id="c7da6-108">For example:</span></span>
    * <span data-ttu-id="c7da6-109">`~/Pages/Shared/_Layout.cshtml`для проектов Razor Pages или Блазор с существующей инфраструктурой Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c7da6-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="c7da6-110">`~/Views/Shared/_Layout.cshtml`для проектов MVC или проектов Блазор Server с существующей инфраструктурой MVC</span><span class="sxs-lookup"><span data-stu-id="c7da6-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="c7da6-111">Чтобы использовать существующий контекст данных, выберите по меньшей мере один файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="c7da6-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c7da6-112">Необходимо выбрать по крайней мере один файл для добавления контекста данных.</span><span class="sxs-lookup"><span data-stu-id="c7da6-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c7da6-113">Выберите класс контекста данных.</span><span class="sxs-lookup"><span data-stu-id="c7da6-113">Select your data context class.</span></span>
  * <span data-ttu-id="c7da6-114">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-114">Select **Add**.</span></span>
* <span data-ttu-id="c7da6-115">Чтобы создать новый контекст пользователя и, возможно, создать настраиваемый класс пользователя для удостоверения:</span><span class="sxs-lookup"><span data-stu-id="c7da6-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c7da6-116">Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c7da6-117">Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="c7da6-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="c7da6-118">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-118">Select **Add**.</span></span>

<span data-ttu-id="c7da6-119">Примечание. Если вы создаете новый контекст пользователя, вам не нужно выбирать файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="c7da6-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c7da6-120">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="c7da6-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c7da6-121">Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:</span><span class="sxs-lookup"><span data-stu-id="c7da6-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c7da6-122">Добавьте необходимые ссылки на пакет NuGet в файл проекта (*CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="c7da6-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="c7da6-123">Выполните следующие команды в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="c7da6-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="c7da6-124">Выполните следующую команду, чтобы вывести список параметров шаблона удостоверений:</span><span class="sxs-lookup"><span data-stu-id="c7da6-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="c7da6-125">В папке проекта запустите механизм формирования удостоверений с нужными параметрами.</span><span class="sxs-lookup"><span data-stu-id="c7da6-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c7da6-126">Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="c7da6-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c7da6-127">Используйте правильное полное имя для контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="c7da6-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="c7da6-128">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="c7da6-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c7da6-129">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="c7da6-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c7da6-130">Пример:</span><span class="sxs-lookup"><span data-stu-id="c7da6-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c7da6-131">Если вы запустите механизм формирования удостоверений без указания `--files` флага или `--useDefaultUI` флага, в проекте будут созданы все доступные страницы пользовательского интерфейса удостоверений.</span><span class="sxs-lookup"><span data-stu-id="c7da6-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c7da6-132">Запуск шаблона идентификации:</span><span class="sxs-lookup"><span data-stu-id="c7da6-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c7da6-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c7da6-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c7da6-134">В **Обозреватель решений**щелкните правой кнопкой мыши проект > **Добавить** > **Новый**шаблонный элемент.</span><span class="sxs-lookup"><span data-stu-id="c7da6-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="c7da6-135">В левой области диалогового окна **Добавление шаблона** выберите **удостоверение** > **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="c7da6-136">В диалоговом окне **Добавление удостоверения** выберите нужные параметры.</span><span class="sxs-lookup"><span data-stu-id="c7da6-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="c7da6-137">Выберите существующую страницу макета, или файл макета будет перезаписан с неверной разметкой.</span><span class="sxs-lookup"><span data-stu-id="c7da6-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="c7da6-138">При выборе существующего файла \* \_ Layout. cshtml\* он **не** перезаписывается.</span><span class="sxs-lookup"><span data-stu-id="c7da6-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="c7da6-139">Пример:</span><span class="sxs-lookup"><span data-stu-id="c7da6-139">For example:</span></span>
    * <span data-ttu-id="c7da6-140">`~/Pages/Shared/_Layout.cshtml`для Razor Pages</span><span class="sxs-lookup"><span data-stu-id="c7da6-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="c7da6-141">`~/Views/Shared/_Layout.cshtml`для проектов MVC</span><span class="sxs-lookup"><span data-stu-id="c7da6-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="c7da6-142">Чтобы использовать существующий контекст данных, выберите по меньшей мере один файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="c7da6-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="c7da6-143">Необходимо выбрать по крайней мере один файл для добавления контекста данных.</span><span class="sxs-lookup"><span data-stu-id="c7da6-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="c7da6-144">Выберите класс контекста данных.</span><span class="sxs-lookup"><span data-stu-id="c7da6-144">Select your data context class.</span></span>
  * <span data-ttu-id="c7da6-145">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-145">Select **Add**.</span></span>
* <span data-ttu-id="c7da6-146">Чтобы создать новый контекст пользователя и, возможно, создать настраиваемый класс пользователя для удостоверения:</span><span class="sxs-lookup"><span data-stu-id="c7da6-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="c7da6-147">Нажмите **+** кнопку, чтобы создать новый **класс контекста данных**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="c7da6-148">Примите значение по умолчанию или укажите класс (например, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="c7da6-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="c7da6-149">Выберите **Добавить**.</span><span class="sxs-lookup"><span data-stu-id="c7da6-149">Select **Add**.</span></span>

<span data-ttu-id="c7da6-150">Примечание. Если вы создаете новый контекст пользователя, вам не нужно выбирать файл для переопределения.</span><span class="sxs-lookup"><span data-stu-id="c7da6-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c7da6-151">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="c7da6-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c7da6-152">Если вы ранее не устанавливали шаблон ASP.NET Core, установите его сейчас:</span><span class="sxs-lookup"><span data-stu-id="c7da6-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="c7da6-153">Добавьте ссылку на пакет в [Microsoft. VisualStudio. Web. стратегию. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) в файл проекта (*CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="c7da6-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="c7da6-154">Выполните следующие команды в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="c7da6-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="c7da6-155">Выполните следующую команду, чтобы вывести список параметров шаблона удостоверений:</span><span class="sxs-lookup"><span data-stu-id="c7da6-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="c7da6-156">В папке проекта запустите механизм формирования удостоверений с нужными параметрами.</span><span class="sxs-lookup"><span data-stu-id="c7da6-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="c7da6-157">Например, чтобы настроить удостоверение с пользовательским интерфейсом по умолчанию и минимальным числом файлов, выполните следующую команду.</span><span class="sxs-lookup"><span data-stu-id="c7da6-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="c7da6-158">Используйте правильное полное имя для контекста базы данных:</span><span class="sxs-lookup"><span data-stu-id="c7da6-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="c7da6-159">В PowerShell используется точка с запятой в качестве разделителя команд.</span><span class="sxs-lookup"><span data-stu-id="c7da6-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c7da6-160">При использовании PowerShell заключите точку с запятой в список файлов или вставьте список файлов в двойные кавычки.</span><span class="sxs-lookup"><span data-stu-id="c7da6-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="c7da6-161">Пример:</span><span class="sxs-lookup"><span data-stu-id="c7da6-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c7da6-162">Если вы запустите механизм формирования удостоверений без указания `--files` флага или `--useDefaultUI` флага, в проекте будут созданы все доступные страницы пользовательского интерфейса удостоверений.</span><span class="sxs-lookup"><span data-stu-id="c7da6-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
