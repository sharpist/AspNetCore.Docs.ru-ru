---
title: Начало работы с [SignalR ASP.NET Core
author: bradygaster
description: В этом учебнике рассказывается как создать приложение чата, которое использует [SignalR для ASP.NET Core.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- '[Blazor'
- '[Blazor Server'
- '[Blazor WebAssembly'
- '[Identity'
- "[Let's Encrypt"
- '[Razor'
- '[SignalR'
uid: tutorials/signalr
ms.openlocfilehash: 91d7108748f3e2ae4d7db3791ebc1536e104e2a8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406957"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="6a5e1-103">Учебник. Начало работы с [SignalR ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a5e1-103">Tutorial: Get started with ASP.NET Core [SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a5e1-104">В этом учебнике описаны основы создания приложения, работающего в режиме реального времени, с помощью [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-104">This tutorial teaches the basics of building a real-time app using [SignalR.</span></span> <span data-ttu-id="6a5e1-105">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6a5e1-106">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-106">Create a web project.</span></span>
> * <span data-ttu-id="6a5e1-107">добавлять клиентскую библиотеку [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-107">Add the [SignalR client library.</span></span>
> * <span data-ttu-id="6a5e1-108">создавать концентратор [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-108">Create a [SignalR hub.</span></span>
> * <span data-ttu-id="6a5e1-109">настраивать проект для использования [SignalR;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-109">Configure the project to use [SignalR.</span></span>
> * <span data-ttu-id="6a5e1-110">Добавлять код для отправки сообщений из любого клиента всем подключенным клиентам.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="6a5e1-111">В итоге вы получите работающее приложение чата:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-111">At the end, you'll have a working chat app:</span></span>

<span data-ttu-id="6a5e1-112">![[SignalRПример приложения](signalr/_static/3.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6a5e1-112">![[SignalR sample app](signalr/_static/3.x/signalr-get-started-finished.png)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a5e1-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="6a5e1-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="6a5e1-117">Создание проекта веб-приложения</span><span class="sxs-lookup"><span data-stu-id="6a5e1-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="6a5e1-119">В меню выберите **Файл > Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="6a5e1-120">В диалоговом окне **Создать проект** выберите **Веб-приложение ASP.NET Core** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="6a5e1-121">В диалоговом окне **Настроить новый проект** укажите имя проекта *SignalRChat*, а затем выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="6a5e1-122">В диалоговом окне **Создание веб-приложения ASP.NET Core** выберите платформы **.NET Core** и **ASP.NET Core 3.0**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="6a5e1-123">Выберите **Веб-приложение**, чтобы создать проект, который использует [Razor Pages, и нажмите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-123">Select **Web Application** to create a project that uses [Razor Pages, and then select **Create**.</span></span>

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="6a5e1-126">Откройте [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) и перейдите в папку, в которой будет создаваться папка нового проекта.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="6a5e1-127">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-128">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a5e1-129">В меню выберите **Файл > Создать решение**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="6a5e1-130">Выберите **.NET Core > Приложение > Веб-приложение** (не выбирайте **Веб-приложение (модель — представление — контроллер)** ), а затем выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="6a5e1-131">Убедитесь, что для параметра **Требуемая версия .NET Framework** указано **.NET Core 3.0**, а затем выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="6a5e1-132">Присвойте проекту имя *SignalRChat* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="6a5e1-133">Добавление клиентской библиотеки [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-133">Add the [SignalR client library</span></span>

<span data-ttu-id="6a5e1-134">Серверная библиотека [SignalR входит в состав общей платформы ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-134">The [SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="6a5e1-135">Клиентская библиотека JavaScript не добавляется в проект автоматически.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="6a5e1-136">В рамках этого руководства вы будете использовать диспетчер библиотек (LibMan), чтобы получить клиентскую библиотеку из *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="6a5e1-137">unpkg — это сеть доставки содержимого, которая позволяет доставить любое содержимое из npm (диспетчера пакетов Node.js).</span><span class="sxs-lookup"><span data-stu-id="6a5e1-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="6a5e1-139">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Добавить** > **Клиентская библиотека**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="6a5e1-140">В диалоговом окне **Add Client-Side Library** (Добавить клиентскую библиотеку) для параметра **Поставщик** выберите **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="6a5e1-141">Для параметра **Библиотека** введите `@microsoft/signalr@latest`.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="6a5e1-142">Щелкните **Choose specific files** (Выбрать определенные файлы), разверните папку *dist/browser* и выберите *signalr.js* и *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="6a5e1-143">В поле **Целевое расположение** укажите *wwwroot/js/signalr/* и нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Диалоговое окно добавления клиентской библиотеки — выбор библиотеки](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="6a5e1-145">LibMan создает папку *wwwroot/js/signalr* и копирует в нее выбранные файлы.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="6a5e1-147">В интегрированном терминале выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="6a5e1-148">Выполните команду ниже, чтобы получить клиентскую библиотеку [SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-148">Run the following command to get the [SignalR client library by using LibMan.</span></span> <span data-ttu-id="6a5e1-149">Возможно, придется подождать несколько секунд, прежде чем появятся выходные данные.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="6a5e1-150">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="6a5e1-151">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="6a5e1-152">копирование файлов в назначение *wwwroot/js/signalr*;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="6a5e1-153">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-153">Copy only the specified files.</span></span>

  <span data-ttu-id="6a5e1-154">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-155">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a5e1-156">В **терминале** выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="6a5e1-157">Перейдите в папку проекта (где расположен файл *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6a5e1-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="6a5e1-158">Выполните команду ниже, чтобы получить клиентскую библиотеку [SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-158">Run the following command to get the [SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="6a5e1-159">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="6a5e1-160">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="6a5e1-161">копирование файлов в назначение *wwwroot/js/signalr*;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="6a5e1-162">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-162">Copy only the specified files.</span></span>

  <span data-ttu-id="6a5e1-163">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="6a5e1-164">Создание концентратора [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-164">Create a [SignalR hub</span></span>

<span data-ttu-id="6a5e1-165">*hub* — это класс, который служит в качестве конвейера высокого уровня для обработки взаимодействия между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="6a5e1-166">В папке проекта SignalRChat создайте папку *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="6a5e1-167">В папке *Hubs* создайте файл *ChatHub.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="6a5e1-168">Класс `ChatHub` наследуется от класса [SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-168">The `ChatHub` class inherits from the [SignalR `Hub` class.</span></span> <span data-ttu-id="6a5e1-169">Класс `Hub` управляет подключениями, группами и обменом сообщениями.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="6a5e1-170">Метод `SendMessage` может вызываться подключенным клиентом, чтобы отправить сообщение всем клиентам.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="6a5e1-171">Далее в этом учебника показан клиентский код JavaScript, который вызывает метод.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="6a5e1-172">Код [SignalR является асинхронным, поэтому обеспечивает максимальную масштабируемость.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-172">[SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="6a5e1-173">Настройка [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-173">Configure [SignalR</span></span>

<span data-ttu-id="6a5e1-174">Сервер [SignalR должен быть настроен для передачи запросов [SignalR к [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-174">The [SignalR server must be configured to pass [SignalR requests to [SignalR.</span></span>

* <span data-ttu-id="6a5e1-175">Добавьте следующий выделенный код в файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="6a5e1-176">В результате [SignalR будет добавлен в системы внедрения зависимостей и маршрутизации ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-176">These changes add [SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="6a5e1-177">Добавление клиентского кода [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-177">Add [SignalR client code</span></span>

* <span data-ttu-id="6a5e1-178">Замените содержимое в файле *Pages\Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="6a5e1-179">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-179">The preceding code:</span></span>

  * <span data-ttu-id="6a5e1-180">Создает текстовые поля для имени и текста сообщения и кнопку отправки.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="6a5e1-181">Создает список с `id="messagesList"` для отображения сообщений, полученных от концентратора [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-181">Creates a list with `id="messagesList"` for displaying messages that are received from the [SignalR hub.</span></span>
  * <span data-ttu-id="6a5e1-182">Содержит ссылки на скрипты для [SignalR и код приложения *chat.js*, который создается в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-182">Includes script references to [SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="6a5e1-183">В папке *wwwroot/js* создайте файл *chat.js* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="6a5e1-184">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-184">The preceding code:</span></span>

  * <span data-ttu-id="6a5e1-185">Создает и запускает подключение.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="6a5e1-186">Добавляет к кнопке отправки обработчик, который отправляет сообщения в концентратор.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="6a5e1-187">Добавляет к объекту подключения обработчик, который получает сообщения из концентратора и добавляет их в список.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="6a5e1-188">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="6a5e1-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a5e1-190">Нажмите клавиши **CTRL+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6a5e1-192">В интегрированном терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-193">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a5e1-194">В меню выберите **Запуск > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="6a5e1-195">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="6a5e1-196">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить сообщение**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="6a5e1-197">Имя и сообщение отображаются на обеих страницах мгновенно.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-197">The name and message are displayed on both pages instantly.</span></span>

  <span data-ttu-id="6a5e1-198">![[SignalRПример приложения](signalr/_static/3.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6a5e1-198">![[SignalR sample app](signalr/_static/3.x/signalr-get-started-finished.png)</span></span>

> [!TIP]
> * <span data-ttu-id="6a5e1-199">Если приложение не работает, откройте средства разработчика для браузера (F12) и перейдите в консоль.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="6a5e1-200">Вы можете увидеть ошибки, связанные с вашим кодом HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="6a5e1-201">Предположим, вы поместили *signalr.js* не в ту папку, которую указали.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="6a5e1-202">В этом случае ссылка на этот файл не будет работать, и вы увидите сообщение об ошибке 404 в консоли.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="6a5e1-203">![ошибка: signalr.js не найден](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="6a5e1-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="6a5e1-204">Если возникает ошибка ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY в Chrome, выполните эти команды, чтобы обновить сертификат разработки:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6a5e1-205">В этом учебнике описаны основы создания приложения, работающего в режиме реального времени, с помощью [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-205">This tutorial teaches the basics of building a real-time app using [SignalR.</span></span> <span data-ttu-id="6a5e1-206">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="6a5e1-207">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-207">Create a web project.</span></span>   
> * <span data-ttu-id="6a5e1-208">добавлять клиентскую библиотеку [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-208">Add the [SignalR client library.</span></span>   
> * <span data-ttu-id="6a5e1-209">создавать концентратор [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-209">Create a [SignalR hub.</span></span> 
> * <span data-ttu-id="6a5e1-210">настраивать проект для использования [SignalR;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-210">Configure the project to use [SignalR.</span></span> 
> * <span data-ttu-id="6a5e1-211">Добавлять код для отправки сообщений из любого клиента всем подключенным клиентам.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="6a5e1-212">В итоге вы получите работающее приложение чата — пример приложения ![[SignalR](signalr/_static/2.x/signalr-get-started-finished.png).</span><span class="sxs-lookup"><span data-stu-id="6a5e1-212">At the end, you'll have a working chat app: ![[SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="6a5e1-213">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="6a5e1-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-216">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="6a5e1-217">Создайте веб-проект.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="6a5e1-219">В меню выберите **Файл > Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="6a5e1-220">В диалоговом окне **Новый проект** выберите **Установленные > Visual C# > Веб > Веб-приложение ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6a5e1-221">Назовите проект *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-221">Name the project *SignalRChat*.</span></span> 

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="6a5e1-223">Выберите **Веб-приложение**, чтобы создать проект, который использует [Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-223">Select **Web Application** to create a project that uses [Razor Pages.</span></span>   

* <span data-ttu-id="6a5e1-224">Выберите целевую платформу **.NET Core**, **ASP.NET Core 2.2** и нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Диалоговое окно создания проекта в Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="6a5e1-227">Откройте [интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal) и перейдите в папку, в которой будет создаваться папка нового проекта.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="6a5e1-228">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-229">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="6a5e1-230">В меню выберите **Файл > Создать решение**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="6a5e1-231">Выберите **.NET Core > Приложение > Веб-приложение ASP.NET Core** (не выбирайте **Веб-приложение ASP.NET Core (MVC)** ).</span><span class="sxs-lookup"><span data-stu-id="6a5e1-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="6a5e1-232">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-232">Select **Next**.</span></span>  

* <span data-ttu-id="6a5e1-233">Присвойте проекту имя *SignalRChat* и нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-233">Name the project *SignalRChat*, and then select **Create**.</span></span>   

--- 

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="6a5e1-234">Добавление клиентской библиотеки [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-234">Add the [SignalR client library</span></span> 

<span data-ttu-id="6a5e1-235">Библиотека сервера [SignalR входит в состав метапакета `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-235">The [SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="6a5e1-236">Клиентская библиотека JavaScript не добавляется в проект автоматически.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="6a5e1-237">В рамках этого руководства вы будете использовать диспетчер библиотек (LibMan), чтобы получить клиентскую библиотеку из *unpkg*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="6a5e1-238">unpkg — это сеть доставки содержимого, которая позволяет доставить любое содержимое из npm (диспетчера пакетов Node.js).</span><span class="sxs-lookup"><span data-stu-id="6a5e1-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="6a5e1-240">В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите **Добавить** > **Клиентская библиотека**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="6a5e1-241">В диалоговом окне **Add Client-Side Library** (Добавить клиентскую библиотеку) для параметра **Поставщик** выберите **unpkg**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="6a5e1-242">В поле **Библиотека** введите `@microsoft/signalr@3` и выберите последнюю версию, но не предварительную.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Диалоговое окно добавления клиентской библиотеки — выбор библиотеки](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="6a5e1-244">Щелкните **Choose specific files** (Выбрать определенные файлы), разверните папку *dist/browser* и выберите *signalr.js* и *signalr.min.js*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="6a5e1-245">В поле **Целевое расположение** укажите *wwwroot/lib/signalr/* и нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Диалоговое окно добавления клиентской библиотеки — выбор файлов и назначения](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="6a5e1-247">LibMan создает папку *wwwroot/lib/signalr* и копирует в нее выбранные файлы.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="6a5e1-249">В интегрированном терминале выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="6a5e1-250">Выполните команду ниже, чтобы получить клиентскую библиотеку [SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-250">Run the following command to get the [SignalR client library by using LibMan.</span></span> <span data-ttu-id="6a5e1-251">Возможно, придется подождать несколько секунд, прежде чем появятся выходные данные.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="6a5e1-252">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="6a5e1-253">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="6a5e1-254">копирование файлов в назначение *wwwroot/lib/signalr*;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="6a5e1-255">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="6a5e1-256">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-257">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="6a5e1-258">В **терминале** выполните следующую команду, чтобы установить LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="6a5e1-259">Перейдите в папку проекта (где расположен файл *SignalRChat.csproj*).</span><span class="sxs-lookup"><span data-stu-id="6a5e1-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span> 

* <span data-ttu-id="6a5e1-260">Выполните команду ниже, чтобы получить клиентскую библиотеку [SignalR, используя LibMan.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-260">Run the following command to get the [SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="6a5e1-261">В параметрах указываются следующие свойства:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="6a5e1-262">использование поставщика unpkg;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="6a5e1-263">копирование файлов в назначение *wwwroot/lib/signalr*;</span><span class="sxs-lookup"><span data-stu-id="6a5e1-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="6a5e1-264">копирование только выбранных файлов.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="6a5e1-265">Выходные данные выглядят примерно так, как в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-signalr-hub"></a><span data-ttu-id="6a5e1-266">Создание концентратора [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-266">Create a [SignalR hub</span></span>   

<span data-ttu-id="6a5e1-267">*hub* — это класс, который служит в качестве конвейера высокого уровня для обработки взаимодействия между клиентом и сервером.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="6a5e1-268">В папке проекта SignalRChat создайте папку *Hubs*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>    

* <span data-ttu-id="6a5e1-269">В папке *Hubs* создайте файл *ChatHub.cs* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="6a5e1-270">Класс `ChatHub` наследуется от класса [SignalR `Hub`.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-270">The `ChatHub` class inherits from the [SignalR `Hub` class.</span></span> <span data-ttu-id="6a5e1-271">Класс `Hub` управляет подключениями, группами и обменом сообщениями.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="6a5e1-272">Метод `SendMessage` может вызываться подключенным клиентом, чтобы отправить сообщение всем клиентам.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="6a5e1-273">Далее в этом учебника показан клиентский код JavaScript, который вызывает метод.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="6a5e1-274">Код [SignalR является асинхронным, поэтому обеспечивает максимальную масштабируемость.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-274">[SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-signalr"></a><span data-ttu-id="6a5e1-275">Настройка [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-275">Configure [SignalR</span></span>  

<span data-ttu-id="6a5e1-276">Сервер [SignalR должен быть настроен для передачи запросов [SignalR к [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-276">The [SignalR server must be configured to pass [SignalR requests to [SignalR.</span></span>    

* <span data-ttu-id="6a5e1-277">Добавьте следующий выделенный код в файл *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="6a5e1-278">В результате [SignalR будет добавлен в систему внедрения зависимостей ASP.NET Core и конвейер ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-278">These changes add [SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-signalr-client-code"></a><span data-ttu-id="6a5e1-279">Добавление клиентского кода [SignalR</span><span class="sxs-lookup"><span data-stu-id="6a5e1-279">Add [SignalR client code</span></span>    

* <span data-ttu-id="6a5e1-280">Замените содержимое в файле *Pages\Index.cshtml* следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="6a5e1-281">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-281">The preceding code:</span></span>   

  * <span data-ttu-id="6a5e1-282">Создает текстовые поля для имени и текста сообщения и кнопку отправки.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="6a5e1-283">Создает список с `id="messagesList"` для отображения сообщений, полученных от концентратора [SignalR.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-283">Creates a list with `id="messagesList"` for displaying messages that are received from the [SignalR hub.</span></span>   
  * <span data-ttu-id="6a5e1-284">Содержит ссылки на скрипты для [SignalR и код приложения *chat.js*, который создается в следующем шаге.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-284">Includes script references to [SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="6a5e1-285">В папке *wwwroot/js* создайте файл *chat.js* со следующим кодом:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="6a5e1-286">Предыдущий код:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-286">The preceding code:</span></span>   

  * <span data-ttu-id="6a5e1-287">Создает и запускает подключение.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="6a5e1-288">Добавляет к кнопке отправки обработчик, который отправляет сообщения в концентратор.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="6a5e1-289">Добавляет к объекту подключения обработчик, который получает сообщения из концентратора и добавляет их в список.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="6a5e1-290">Запуск приложения</span><span class="sxs-lookup"><span data-stu-id="6a5e1-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="6a5e1-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5e1-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="6a5e1-292">Нажмите клавиши **CTRL+F5**, чтобы запустить приложение без отладки.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a5e1-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a5e1-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="6a5e1-294">В интегрированном терминале выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="6a5e1-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a5e1-295">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="6a5e1-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a5e1-296">В меню выберите **Запуск > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="6a5e1-297">Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="6a5e1-298">Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить сообщение**.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="6a5e1-299">Имя и сообщение отображаются на обеих страницах мгновенно.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-299">The name and message are displayed on both pages instantly.</span></span>   

  <span data-ttu-id="6a5e1-300">![[SignalRПример приложения](signalr/_static/2.x/signalr-get-started-finished.png)</span><span class="sxs-lookup"><span data-stu-id="6a5e1-300">![[SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span> 

> [!TIP]    
> <span data-ttu-id="6a5e1-301">Если приложение не работает, откройте средства разработчика для браузера (F12) и перейдите в консоль.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="6a5e1-302">Вы можете увидеть ошибки, связанные с вашим кодом HTML и JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="6a5e1-303">Предположим, вы поместили *signalr.js* не в ту папку, которую указали.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="6a5e1-304">В этом случае ссылка на этот файл не будет работать, и вы увидите сообщение об ошибке 404 в консоли.</span><span class="sxs-lookup"><span data-stu-id="6a5e1-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="6a5e1-305">![ошибка: signalr.js не найден](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="6a5e1-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="6a5e1-306">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="6a5e1-306">Additional resources</span></span> 
* [<span data-ttu-id="6a5e1-307">Версия учебника на YouTube</span><span class="sxs-lookup"><span data-stu-id="6a5e1-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
