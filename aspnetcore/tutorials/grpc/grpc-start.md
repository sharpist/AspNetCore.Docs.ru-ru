---
title: Создание клиента и сервера gRPC .NET Core в ASP.NET Core
author: juntaoluo
description: В этом учебнике объясняется, как создать службу и клиента gRPC в ASP.NET Core. Узнайте, как создать проект службы gRPC, изменить файл proto и добавить дуплексный режим потоковой передачи вызовов.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: fbfd37b8f796990ff035f7fffeb906e23a8739d4
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678573"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="2de02-104">Учебник. Создание клиента и сервера gRPC в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2de02-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="2de02-105">Автор: [John Luo](https://github.com/juntaoluo) (Джон Луо)</span><span class="sxs-lookup"><span data-stu-id="2de02-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="2de02-106">В этом руководстве показано, как создать клиент [gRPC](https://grpc.io/docs/guides/) в .NET Core и сервер gRPC ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de02-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="2de02-107">В итоге вы получите клиент gRPC, который взаимодействует со службой Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="2de02-108">[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2de02-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2de02-109">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="2de02-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2de02-110">Создание сервера gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="2de02-111">Создание клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="2de02-112">Тестирование службы клиента gRPC с помощью службы Greeter gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2de02-113">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="2de02-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2de02-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de02-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2de02-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de02-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2de02-116">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2de02-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="2de02-117">Visual Studio для Mac 8.7 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="2de02-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="2de02-118">Создание службы gRPC</span><span class="sxs-lookup"><span data-stu-id="2de02-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2de02-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de02-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2de02-120">Запустите Visual Studio и щелкните **Создать проект**</span><span class="sxs-lookup"><span data-stu-id="2de02-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="2de02-121">или в меню **Файл** в Visual Studio выберите **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="2de02-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2de02-122">В диалоговом окне **Создание проекта** выберите **Служба gRPC** и нажмите кнопку **Далее** :</span><span class="sxs-lookup"><span data-stu-id="2de02-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Диалоговое окно создания проекта](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="2de02-124">Присвойте проекту имя **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="2de02-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="2de02-125">Для проекта необходимо установить имя *GrpcGreeter* , чтобы при копировании и вставке кода совпадали пространства имен.</span><span class="sxs-lookup"><span data-stu-id="2de02-125">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="2de02-126">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2de02-126">Select **Create**.</span></span>
* <span data-ttu-id="2de02-127">В диалоговом окне **Создать службу gRPC** сделайте следующее:</span><span class="sxs-lookup"><span data-stu-id="2de02-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="2de02-128">Шаблон **gRPC Service** выбран.</span><span class="sxs-lookup"><span data-stu-id="2de02-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="2de02-129">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2de02-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2de02-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de02-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2de02-131">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2de02-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2de02-132">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="2de02-132">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2de02-133">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2de02-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="2de02-134">С помощью команды `dotnet new` в папке *GrpcGreeter* создается служба gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="2de02-135">С помощью команды `code` папка *GrpcGreeter* открывается в новом экземпляре Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="2de02-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="2de02-136">Появится диалоговое окно с предупреждением **Required assets to build and debug are missing from 'GrpcGreeter'. Добавить их?**</span><span class="sxs-lookup"><span data-stu-id="2de02-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="2de02-137">Выберите ответ **Да**.</span><span class="sxs-lookup"><span data-stu-id="2de02-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2de02-138">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2de02-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2de02-139">Запустите Visual Studio для Mac и щелкните **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="2de02-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="2de02-140">или в меню **Файл** в Visual Studio выберите **Создать** > **Проект**.</span><span class="sxs-lookup"><span data-stu-id="2de02-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2de02-141">В диалоговом окне **Создание проекта** выберите **Интернет и консоль** > **Приложение** > **Служба gRPC** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2de02-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![Диалоговое окно создания проекта](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="2de02-143">В качестве требуемой версии .NET Framework выберите **.NET Core 3.1** и нажмите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2de02-143">Select **.NET Core 3.1** for the target framework and click **Next**</span></span>
* <span data-ttu-id="2de02-144">Присвойте проекту имя **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="2de02-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="2de02-145">Для проекта необходимо установить имя *GrpcGreeter* , чтобы при копировании и вставке кода совпадали пространства имен.</span><span class="sxs-lookup"><span data-stu-id="2de02-145">It's important to name the project *GrpcGreeter* so the namespaces will match when you copy and paste code.</span></span>
* <span data-ttu-id="2de02-146">Выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2de02-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="2de02-147">Запуск службы</span><span class="sxs-lookup"><span data-stu-id="2de02-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="2de02-148">В журналах отобразится служба, которая ожидает передачи данных через `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="2de02-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="2de02-149">Шаблон gRPC настроен для использования [протокола TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="2de02-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="2de02-150">Клиенты gRPC должны использовать протокол HTTPS для обращения к серверу.</span><span class="sxs-lookup"><span data-stu-id="2de02-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="2de02-151">macOS не поддерживает ASP.NET Core gRPC с TLS.</span><span class="sxs-lookup"><span data-stu-id="2de02-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="2de02-152">Для успешного запуска служб gRPC в macOS требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="2de02-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="2de02-153">Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="2de02-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="2de02-154">Анализ файлов проекта</span><span class="sxs-lookup"><span data-stu-id="2de02-154">Examine the project files</span></span>

<span data-ttu-id="2de02-155">Файлы проекта *GrpcGreeter* :</span><span class="sxs-lookup"><span data-stu-id="2de02-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="2de02-156">*greet.proto* : Файл *Protos/greet.proto* определяет службу gRPC `Greeter` и используется для создания ресурсов сервера gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="2de02-157">Дополнительные сведения см. в разделе [Введение в gRPC](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="2de02-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="2de02-158">Папка *Services* содержит реализацию службы `Greeter`.</span><span class="sxs-lookup"><span data-stu-id="2de02-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="2de02-159">*appSettings.json* : содержит данные конфигурации, такие как протокол, используемый в Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2de02-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="2de02-160">Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="2de02-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="2de02-161">*Program.cs* : содержит точку входа для службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="2de02-162">Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="2de02-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="2de02-163">*Startup.cs* : содержит код, задающий поведение приложения.</span><span class="sxs-lookup"><span data-stu-id="2de02-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="2de02-164">Дополнительные сведения: [Запуск приложения](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="2de02-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="2de02-165">Создание клиента gRPC в консольном приложении .NET</span><span class="sxs-lookup"><span data-stu-id="2de02-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2de02-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de02-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2de02-167">Откройте второй экземпляр Visual Studio и щелкните **Создать проект**.</span><span class="sxs-lookup"><span data-stu-id="2de02-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="2de02-168">В диалоговом окне **Создание проекта** выберите **Консольное приложение (.NET Core)** и щелкните **Далее**.</span><span class="sxs-lookup"><span data-stu-id="2de02-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="2de02-169">В текстовое поле **Имя проекта** введите **GrpcGreeterClient** и щелкните **Создать**.</span><span class="sxs-lookup"><span data-stu-id="2de02-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2de02-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de02-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2de02-171">Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="2de02-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2de02-172">Смените каталог (`cd`) на папку, в которой будет содержаться проект.</span><span class="sxs-lookup"><span data-stu-id="2de02-172">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2de02-173">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2de02-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2de02-174">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2de02-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2de02-175">Чтобы создать консольное приложение с именем *GrpcGreeterClient* , см. руководство по [созданию полного решения .NET Core в macOS с помощью Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span><span class="sxs-lookup"><span data-stu-id="2de02-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="2de02-176">Добавление необходимых пакетов</span><span class="sxs-lookup"><span data-stu-id="2de02-176">Add required packages</span></span>

<span data-ttu-id="2de02-177">Для клиентского проекта gRPC требуются следующие пакеты:</span><span class="sxs-lookup"><span data-stu-id="2de02-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="2de02-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), который содержит клиент .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2de02-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="2de02-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), который содержит API сообщений protobuf для C#;</span><span class="sxs-lookup"><span data-stu-id="2de02-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="2de02-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), который содержит поддержку инструментов C# для файлов protobuf.</span><span class="sxs-lookup"><span data-stu-id="2de02-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="2de02-181">Пакет инструментов не требуется во время выполнения, поэтому зависимость помечается `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="2de02-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2de02-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de02-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2de02-183">Установка пакетов с помощью консоли диспетчера пакетов (PMC) или управления пакетами NuGet.</span><span class="sxs-lookup"><span data-stu-id="2de02-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="2de02-184">Установка пакетов с помощью консоли диспетчера пакетов</span><span class="sxs-lookup"><span data-stu-id="2de02-184">PMC option to install packages</span></span>

* <span data-ttu-id="2de02-185">В Visual Studio выберите пункты меню **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.</span><span class="sxs-lookup"><span data-stu-id="2de02-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="2de02-186">В **консоли диспетчера пакетов** выполните команду `cd GrpcGreeterClient`, чтобы перейти в папку с файлами *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2de02-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="2de02-187">Выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2de02-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="2de02-188">Установка пакетов с помощью раздела управления пакетами NuGet</span><span class="sxs-lookup"><span data-stu-id="2de02-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="2de02-189">Щелкните правой кнопкой мыши проект в **обозревателе решений** > **Управление пакетами NuGet**</span><span class="sxs-lookup"><span data-stu-id="2de02-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="2de02-190">Выберите вкладку **Обзор**.</span><span class="sxs-lookup"><span data-stu-id="2de02-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="2de02-191">В поле поиска введите **Grpc.Net.Client**.</span><span class="sxs-lookup"><span data-stu-id="2de02-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="2de02-192">Выберите пакет **Grpc.Net.Client** на вкладке **Обзор** и нажмите кнопку **Установить**.</span><span class="sxs-lookup"><span data-stu-id="2de02-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="2de02-193">Повторите все шаги для `Google.Protobuf` и `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="2de02-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2de02-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de02-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2de02-195">Во **встроенном терминале** выполните следующие команды:</span><span class="sxs-lookup"><span data-stu-id="2de02-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2de02-196">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2de02-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2de02-197">Щелкните правой кнопкой на проекте **GrpcGreeterClient** на **панели решений** и выберите **Управление пакетами NuGet**.</span><span class="sxs-lookup"><span data-stu-id="2de02-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**</span></span>
* <span data-ttu-id="2de02-198">В поле поиска введите **Grpc.Net.Client**.</span><span class="sxs-lookup"><span data-stu-id="2de02-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="2de02-199">В области результатов выберите пакет **Grpc.Net.Client** и нажмите кнопку **Добавить пакет**</span><span class="sxs-lookup"><span data-stu-id="2de02-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**</span></span>
* <span data-ttu-id="2de02-200">Нажмите кнопку **Принимаю** в диалоговом окне **Принятие условий лицензионного соглашения**.</span><span class="sxs-lookup"><span data-stu-id="2de02-200">Click the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="2de02-201">Повторите все шаги для `Google.Protobuf` и `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="2de02-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="2de02-202">Добавление greet.proto</span><span class="sxs-lookup"><span data-stu-id="2de02-202">Add greet.proto</span></span>

* <span data-ttu-id="2de02-203">Создайте папку *Protos* в клиентском проекте gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="2de02-204">Скопируйте файл *Protos\greet.proto* из службы Greeter gRPC в проект клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="2de02-205">Измените пространство имен в файле `greet.proto` на пространство имен проекта:</span><span class="sxs-lookup"><span data-stu-id="2de02-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="2de02-206">Измените файл проекта *GrpcGreeterClient.csproj* :</span><span class="sxs-lookup"><span data-stu-id="2de02-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="2de02-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de02-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="2de02-208">Щелкните проект правой кнопкой мыши и выберите **Изменить файл проекта**.</span><span class="sxs-lookup"><span data-stu-id="2de02-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="2de02-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de02-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="2de02-210">Выберите файл *GrpcGreeterClient.csproj*.</span><span class="sxs-lookup"><span data-stu-id="2de02-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2de02-211">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2de02-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="2de02-212">Щелкните проект правой кнопкой мыши и выберите **Изменить файл проекта**.</span><span class="sxs-lookup"><span data-stu-id="2de02-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="2de02-213">Добавьте группу элементов с элементом `<Protobuf>`, ссылающимся на файл *greet.proto* :</span><span class="sxs-lookup"><span data-stu-id="2de02-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="2de02-214">Создание клиента Greeter</span><span class="sxs-lookup"><span data-stu-id="2de02-214">Create the Greeter client</span></span>

<span data-ttu-id="2de02-215">Скомпилируйте клиентский проект, чтобы создать типы в пространстве имен `GrpcGreeter`.</span><span class="sxs-lookup"><span data-stu-id="2de02-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="2de02-216">Типы `GrpcGreeter` создаются автоматически в процессе сборки.</span><span class="sxs-lookup"><span data-stu-id="2de02-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="2de02-217">Добавьте в файл *Program.cs* клиента gRPC следующий код:</span><span class="sxs-lookup"><span data-stu-id="2de02-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="2de02-218">файл *Program.cs* содержит точку входа и логику для клиента gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="2de02-219">Клиент Greeter создается следующим образом:</span><span class="sxs-lookup"><span data-stu-id="2de02-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="2de02-220">Создание экземпляра `GrpcChannel` со сведениями для создания подключения к службе gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="2de02-221">Использование `GrpcChannel` для создания клиента Greeter:</span><span class="sxs-lookup"><span data-stu-id="2de02-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="2de02-222">Клиент Greeter вызывает асинхронный метод `SayHello`.</span><span class="sxs-lookup"><span data-stu-id="2de02-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="2de02-223">Отображается результат вызова `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="2de02-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="2de02-224">Тестирование клиента gRPC с помощью службы Greeter gRPC</span><span class="sxs-lookup"><span data-stu-id="2de02-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2de02-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2de02-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2de02-226">В службе Greeter нажмите `Ctrl+F5` для запуска сервера без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2de02-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="2de02-227">В проекте `GrpcGreeterClient` нажмите `Ctrl+F5` для запуска клиента без отладчика.</span><span class="sxs-lookup"><span data-stu-id="2de02-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2de02-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2de02-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2de02-229">Запустите службу Greeter.</span><span class="sxs-lookup"><span data-stu-id="2de02-229">Start the Greeter service.</span></span>
* <span data-ttu-id="2de02-230">Запустите клиент.</span><span class="sxs-lookup"><span data-stu-id="2de02-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2de02-231">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="2de02-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2de02-232">Из-за упомянутого выше [обходного пути для проблемы HTTP/2 TLS в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos) необходимо изменить адрес канала в клиенте на "http://localhost:5000".</span><span class="sxs-lookup"><span data-stu-id="2de02-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="2de02-233">Измените строку 13 в файле **GrpcGreeterClient/Program.cs** на следующую:</span><span class="sxs-lookup"><span data-stu-id="2de02-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="2de02-234">Запустите службу Greeter.</span><span class="sxs-lookup"><span data-stu-id="2de02-234">Start the Greeter service.</span></span>
* <span data-ttu-id="2de02-235">Запустите клиент.</span><span class="sxs-lookup"><span data-stu-id="2de02-235">Start the client.</span></span>

---

<span data-ttu-id="2de02-236">Клиент отправляет приветствие в службу с сообщением, в котором содержится его имя *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="2de02-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="2de02-237">Служба отправляет сообщение "Hello GreeterClient" в качестве ответа.</span><span class="sxs-lookup"><span data-stu-id="2de02-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="2de02-238">Ответ "Hello GreeterClient" отображается в командной строке:</span><span class="sxs-lookup"><span data-stu-id="2de02-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="2de02-239">Служба gRPC записывает сведения об успешном вызове в журналы, что отображается в командной строке:</span><span class="sxs-lookup"><span data-stu-id="2de02-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

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
> <span data-ttu-id="2de02-240">Чтобы применить код, описанный в этой статье, требуется сертификат разработки HTTPS ASP.NET Core для защиты службы gRPC.</span><span class="sxs-lookup"><span data-stu-id="2de02-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="2de02-241">Если .NET gRPC возвращает сообщение `The remote certificate is invalid according to the validation procedure.` или `The SSL connection could not be established.`, это значит, что сертификат разработки не является доверенным.</span><span class="sxs-lookup"><span data-stu-id="2de02-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="2de02-242">Чтобы исправить эту проблему, см. [Вызов службы gRPC с использованием ненадежного или недействительного сертификата](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="2de02-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="2de02-243">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="2de02-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
