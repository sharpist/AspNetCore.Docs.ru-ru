---
title: Создание клиента и сервера gRPC .NET Core в ASP.NET Core
author: juntaoluo
description: В этом учебнике объясняется, как создать службу и клиента gRPC в ASP.NET Core. Узнайте, как создать проект службы gRPC, изменить файл proto и добавить дуплексный режим потоковой передачи вызовов.
ms.author: johluo
ms.date: 04/08/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: b3c210e50f4bf2869100976176ded939b39e3712
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022060"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="fc00b-104">Учебник. Создание клиента и сервера gRPC в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc00b-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="fc00b-105">Автор: [John Luo](https://github.com/juntaoluo) (Джон Луо)</span><span class="sxs-lookup"><span data-stu-id="fc00b-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="fc00b-106">В этом руководстве показано, как создать клиент [gRPC](https://grpc.io/docs/guides/) в .NET Core и сервер gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc00b-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="fc00b-107">В итоге вы получите клиент gRPC, который взаимодействует со службой Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="fc00b-108">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fc00b-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="fc00b-109">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="fc00b-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fc00b-110">Создание сервера gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="fc00b-111">Создание клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="fc00b-112">Тестирование службы клиента gRPC с помощью службы Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fc00b-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="fc00b-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fc00b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc00b-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="fc00b-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc00b-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fc00b-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fc00b-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a><span data-ttu-id="fc00b-117">Создание службы gRPC</span><span class="sxs-lookup"><span data-stu-id="fc00b-117">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fc00b-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc00b-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fc00b-119">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="fc00b-119">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="fc00b-120">или в меню **Файл** в Visual Studio выберите **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-120">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="fc00b-121">В диалоговом окне **Создание проекта** выберите **Служба gRPC** и нажмите кнопку **Далее**:</span><span class="sxs-lookup"><span data-stu-id="fc00b-121">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![Диалоговое окно создания проекта](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="fc00b-123">Присвойте проекту имя **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-123">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="fc00b-124">Для проекта необходимо установить имя *GrpcGreeter*, чтобы при копировании и вставке кода совпадали пространства имен.</span><span class="sxs-lookup"><span data-stu-id="fc00b-124">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="fc00b-125">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-125">Select **Create**.</span></span>
* <span data-ttu-id="fc00b-126">В диалоговом окне **Создать службу gRPC** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="fc00b-126">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="fc00b-127">Шаблон **gRPC Service** выбран.</span><span class="sxs-lookup"><span data-stu-id="fc00b-127">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="fc00b-128">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-128">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fc00b-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc00b-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fc00b-130">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fc00b-130">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fc00b-131">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="fc00b-131">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fc00b-132">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fc00b-132">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="fc00b-133">С помощью команды `dotnet new` в папке *GrpcGreeter* создается служба gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-133">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="fc00b-134">С помощью команды `code` папка *GrpcGreeter* открывается в новом экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="fc00b-134">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="fc00b-135">Появится диалоговое окно с предупреждением **Required assets to build and debug are missing from 'GrpcGreeter'. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="fc00b-135">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="fc00b-136">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-136">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fc00b-137">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fc00b-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fc00b-138">Из терминала выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fc00b-138">From a terminal, run the following commands:</span></span>

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

<span data-ttu-id="fc00b-139">Эти команды позволяют создать службу gRPC с помощью [.NET Core CLI](/dotnet/core/tools/dotnet).</span><span class="sxs-lookup"><span data-stu-id="fc00b-139">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a gRPC service.</span></span>

### <a name="open-the-project"></a><span data-ttu-id="fc00b-140">Открытие проекта</span><span class="sxs-lookup"><span data-stu-id="fc00b-140">Open the project</span></span>

<span data-ttu-id="fc00b-141">В Visual Studio выберите **Файл** > **Открыть** и щелкните файл *GrpcGreeter.csproj*.</span><span class="sxs-lookup"><span data-stu-id="fc00b-141">From Visual Studio, select **File** > **Open**, and then select the *GrpcGreeter.csproj* file.</span></span>

---

### <a name="run-the-service"></a><span data-ttu-id="fc00b-142">Запуск службы</span><span class="sxs-lookup"><span data-stu-id="fc00b-142">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="fc00b-143">В журналах отобразится служба, которая ожидает передачи данных через `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-143">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="fc00b-144">Шаблон gRPC настроен для использования [протокола TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="fc00b-144">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="fc00b-145">Клиенты gRPC должны использовать протокол HTTPS для обращения к серверу.</span><span class="sxs-lookup"><span data-stu-id="fc00b-145">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="fc00b-146">macOS не поддерживает ASP.NET Core gRPC с TLS.</span><span class="sxs-lookup"><span data-stu-id="fc00b-146">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="fc00b-147">Для успешного запуска служб gRPC в macOS требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="fc00b-147">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="fc00b-148">Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="fc00b-148">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="fc00b-149">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="fc00b-149">Examine the project files</span></span>

<span data-ttu-id="fc00b-150">Файлы проекта *GrpcGreeter*:</span><span class="sxs-lookup"><span data-stu-id="fc00b-150">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="fc00b-151">*greet.proto*: Файл *Protos/greet.proto* определяет службу gRPC `Greeter` и используется для создания ресурсов сервера gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-151">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="fc00b-152">Дополнительные сведения см. в разделе [Введение в gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="fc00b-152">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="fc00b-153">Папка *Services* содержит реализацию службы `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-153">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="fc00b-154">*appSettings.json*: содержит данные конфигурации, такие как протокол, используемый в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fc00b-154">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="fc00b-155">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="fc00b-155">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="fc00b-156">*Program.cs*: содержит точку входа для службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-156">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="fc00b-157">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="fc00b-157">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="fc00b-158">*Startup.cs*: содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="fc00b-158">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="fc00b-159">Дополнительные сведения: [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="fc00b-159">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="fc00b-160">Создание клиента gRPC в консольном приложении .NET</span><span class="sxs-lookup"><span data-stu-id="fc00b-160">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fc00b-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc00b-161">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fc00b-162">Откройте второй экземпляр Visual Studio и щелкните **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-162">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="fc00b-163">В диалоговом окне **Создание проекта** выберите **Консольное приложение (.NET Core)** и щелкните **Далее**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-163">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="fc00b-164">В текстовое поле **Имя проекта** введите **GrpcGreeterClient** и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-164">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fc00b-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc00b-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fc00b-166">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="fc00b-166">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="fc00b-167">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="fc00b-167">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="fc00b-168">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fc00b-168">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fc00b-169">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fc00b-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="fc00b-170">Чтобы создать консольное приложение с именем *GrpcGreeterClient*, см. руководство по [созданию полного решения .NET Core в macOS с помощью Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span><span class="sxs-lookup"><span data-stu-id="fc00b-170">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="fc00b-171">Добавление необходимых пакетов</span><span class="sxs-lookup"><span data-stu-id="fc00b-171">Add required packages</span></span>

<span data-ttu-id="fc00b-172">Для клиентского проекта gRPC требуются следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="fc00b-172">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="fc00b-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), который содержит клиент .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fc00b-173">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="fc00b-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), который содержит API сообщений protobuf для C#;</span><span class="sxs-lookup"><span data-stu-id="fc00b-174">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="fc00b-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), который содержит поддержку инструментов C# для файлов protobuf.</span><span class="sxs-lookup"><span data-stu-id="fc00b-175">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="fc00b-176">Пакет инструментов не требуется во время выполнения, поэтому зависимость помечается `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-176">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fc00b-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc00b-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="fc00b-178">Установка пакетов с помощью консоли диспетчера пакетов (PMC) или управления пакетами NuGet.</span><span class="sxs-lookup"><span data-stu-id="fc00b-178">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="fc00b-179">Установка пакетов с помощью консоли диспетчера пакетов</span><span class="sxs-lookup"><span data-stu-id="fc00b-179">PMC option to install packages</span></span>

* <span data-ttu-id="fc00b-180">В Visual Studio выберите пункты меню **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-180">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="fc00b-181">В **консоли диспетчера пакетов** выполните команду `cd GrpcGreeterClient`, чтобы перейти в папку с файлами *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="fc00b-181">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="fc00b-182">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fc00b-182">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="fc00b-183">Установка пакетов с помощью раздела управления пакетами NuGet</span><span class="sxs-lookup"><span data-stu-id="fc00b-183">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="fc00b-184">Щелкните правой кнопкой мыши проект в **обозревателе решений** > **Управление пакетами NuGet**</span><span class="sxs-lookup"><span data-stu-id="fc00b-184">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="fc00b-185">Выберите вкладку **Обзор**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-185">Select the **Browse** tab.</span></span>
* <span data-ttu-id="fc00b-186">В поле поиска введите **Grpc.Net.Client**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-186">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="fc00b-187">Выберите пакет **Grpc.Net.Client** на вкладке **Обзор** и нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-187">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="fc00b-188">Повторите все шаги для `Google.Protobuf` и `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-188">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fc00b-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc00b-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="fc00b-190">Во **встроенном терминале** выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="fc00b-190">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fc00b-191">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fc00b-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fc00b-192">Щелкните правой кнопкой мыши папку **Пакеты** на **панели решения** > **Добавление пакетов**</span><span class="sxs-lookup"><span data-stu-id="fc00b-192">Right-click the **Packages** folder in **Solution Pad** > **Add Packages**</span></span>
* <span data-ttu-id="fc00b-193">В поле поиска введите **Grpc.Net.Client**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-193">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="fc00b-194">В области результатов выберите пакет **Grpc.Net.Client** и нажмите кнопку **Добавить пакет**</span><span class="sxs-lookup"><span data-stu-id="fc00b-194">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="fc00b-195">Повторите все шаги для `Google.Protobuf` и `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-195">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="fc00b-196">Добавление greet.proto</span><span class="sxs-lookup"><span data-stu-id="fc00b-196">Add greet.proto</span></span>

* <span data-ttu-id="fc00b-197">Создайте папку *Protos* в клиентском проекте gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-197">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="fc00b-198">Скопируйте файл *Protos\greet.proto* из службы Greeter gRPC в проект клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-198">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="fc00b-199">Измените файл проекта *GrpcGreeterClient.csproj*:</span><span class="sxs-lookup"><span data-stu-id="fc00b-199">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="fc00b-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc00b-200">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="fc00b-201">Щелкните проект правой кнопкой мыши и выберите **Изменить файл проекта**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-201">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="fc00b-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc00b-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="fc00b-203">Выберите файл *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="fc00b-203">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fc00b-204">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fc00b-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="fc00b-205">Щелкните проект правой кнопкой мыши и выберите **Сервис** > **Изменить файл**.</span><span class="sxs-lookup"><span data-stu-id="fc00b-205">Right-click the project and select **Tools** > **Edit File**.</span></span>

  ---

* <span data-ttu-id="fc00b-206">Добавьте группу элементов с элементом `<Protobuf>`, ссылающимся на файл *greet.proto*:</span><span class="sxs-lookup"><span data-stu-id="fc00b-206">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="fc00b-207">Создание клиента Greeter</span><span class="sxs-lookup"><span data-stu-id="fc00b-207">Create the Greeter client</span></span>

<span data-ttu-id="fc00b-208">Скомпилируйте проект, чтобы создать типы в пространстве имен `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-208">Build the project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="fc00b-209">Типы `GrpcGreeter` создаются автоматически в процессе сборки.</span><span class="sxs-lookup"><span data-stu-id="fc00b-209">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="fc00b-210">Добавьте в файл *Program.cs* клиента gRPC следующий код:</span><span class="sxs-lookup"><span data-stu-id="fc00b-210">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="fc00b-211">файл *Program.cs* содержит точку входа и логику для клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-211">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="fc00b-212">Клиент Greeter создается следующим образом:</span><span class="sxs-lookup"><span data-stu-id="fc00b-212">The Greeter client is created by:</span></span>

* <span data-ttu-id="fc00b-213">Создание экземпляра `GrpcChannel` со сведениями для создания подключения к службе gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-213">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="fc00b-214">Использование `GrpcChannel` для создания клиента Greeter:</span><span class="sxs-lookup"><span data-stu-id="fc00b-214">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="fc00b-215">Клиент Greeter вызывает асинхронный метод `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="fc00b-215">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="fc00b-216">Отображается результат вызова `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="fc00b-216">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="fc00b-217">Тестирование клиента gRPC с помощью службы Greeter gRPC</span><span class="sxs-lookup"><span data-stu-id="fc00b-217">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fc00b-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fc00b-218">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fc00b-219">В службе Greeter нажмите `Ctrl+F5` для запуска сервера без отладчика.</span><span class="sxs-lookup"><span data-stu-id="fc00b-219">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="fc00b-220">В проекте `GrpcGreeterClient` нажмите `Ctrl+F5` для запуска клиента без отладчика.</span><span class="sxs-lookup"><span data-stu-id="fc00b-220">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="fc00b-221">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="fc00b-221">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="fc00b-222">Запустите службу Greeter.</span><span class="sxs-lookup"><span data-stu-id="fc00b-222">Start the Greeter service.</span></span>
* <span data-ttu-id="fc00b-223">Запустите клиент.</span><span class="sxs-lookup"><span data-stu-id="fc00b-223">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fc00b-224">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="fc00b-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fc00b-225">Запустите службу Greeter.</span><span class="sxs-lookup"><span data-stu-id="fc00b-225">Start the Greeter service.</span></span>
* <span data-ttu-id="fc00b-226">Запустите клиент.</span><span class="sxs-lookup"><span data-stu-id="fc00b-226">Start the client.</span></span>

---

<span data-ttu-id="fc00b-227">Клиент отправляет приветствие в службу с сообщением, в котором содержится его имя *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="fc00b-227">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="fc00b-228">Служба отправляет сообщение "Hello GreeterClient" в качестве ответа.</span><span class="sxs-lookup"><span data-stu-id="fc00b-228">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="fc00b-229">Ответ "Hello GreeterClient" отображается в командной строке:</span><span class="sxs-lookup"><span data-stu-id="fc00b-229">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="fc00b-230">Служба gRPC записывает сведения об успешном вызове в журналы, что отображается в командной строке:</span><span class="sxs-lookup"><span data-stu-id="fc00b-230">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="fc00b-231">Чтобы применить код, описанный в этой статье, требуется сертификат разработки HTTPS ASP.NET Core для защиты службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="fc00b-231">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="fc00b-232">Если .NET gRPC возвращает сообщение `The remote certificate is invalid according to the validation procedure.` или `The SSL connection could not be established.`, это значит, что сертификат разработки не является доверенным.</span><span class="sxs-lookup"><span data-stu-id="fc00b-232">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="fc00b-233">Чтобы исправить эту проблему, см. [Вызов службы gRPC с использованием ненадежного или недействительного сертификата](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="fc00b-233">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="fc00b-234">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="fc00b-234">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
