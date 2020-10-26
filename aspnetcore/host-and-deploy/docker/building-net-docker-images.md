---
title: Образы Docker для ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать опубликованные образы Docker для .NET Core из реестра Docker. Извлечение и создание образов.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 6f94323873937d1428d6cfa1b0ef15e44611fd47
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92153643"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="d2d04-104">Образы Docker для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2d04-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="d2d04-105">В этом руководстве показано, как запустить приложение ASP.NET Core в контейнерах Docker.</span><span class="sxs-lookup"><span data-stu-id="d2d04-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="d2d04-106">В этом учебнике рассмотрены следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="d2d04-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="d2d04-107">узнаете о создании образов Docker для Microsoft .NET Core;</span><span class="sxs-lookup"><span data-stu-id="d2d04-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="d2d04-108">скачивание примера приложения ASP.NET Core;</span><span class="sxs-lookup"><span data-stu-id="d2d04-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="d2d04-109">локальное выполнение примера приложения;</span><span class="sxs-lookup"><span data-stu-id="d2d04-109">Run the sample app locally</span></span>
> * <span data-ttu-id="d2d04-110">выполнение примера приложения в контейнерах Linux;</span><span class="sxs-lookup"><span data-stu-id="d2d04-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="d2d04-111">выполнение примера приложения в контейнерах Windows;</span><span class="sxs-lookup"><span data-stu-id="d2d04-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="d2d04-112">локальная сборка и развертывание.</span><span class="sxs-lookup"><span data-stu-id="d2d04-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="d2d04-113">Образы Docker для .NET Core</span><span class="sxs-lookup"><span data-stu-id="d2d04-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="d2d04-114">Для работы с этим руководством следует скачать пример приложения ASP.NET Core и запустить его в контейнерах Docker.</span><span class="sxs-lookup"><span data-stu-id="d2d04-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="d2d04-115">Этот пример поддерживается в контейнерах Linux и Windows.</span><span class="sxs-lookup"><span data-stu-id="d2d04-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="d2d04-116">Пример файла Dockerfile использует [функцию многоэтапной сборки Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) для сборки и выполнения в разных контейнерах.</span><span class="sxs-lookup"><span data-stu-id="d2d04-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="d2d04-117">Контейнеры для сборки и выполнения создаются на основе образов, предоставленных корпорацией Майкрософт в Docker Hub:</span><span class="sxs-lookup"><span data-stu-id="d2d04-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="d2d04-118">Этот образ используется в этом примере для создания приложения.</span><span class="sxs-lookup"><span data-stu-id="d2d04-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="d2d04-119">Образ содержит пакет SDK для .NET Core, который включает программы командной строки (CLI).</span><span class="sxs-lookup"><span data-stu-id="d2d04-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="d2d04-120">Он оптимизирован для локальной разработки, отладки и модульного тестирования.</span><span class="sxs-lookup"><span data-stu-id="d2d04-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="d2d04-121">Установленные средства разработки и компиляции делают этот образ относительно большим.</span><span class="sxs-lookup"><span data-stu-id="d2d04-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="d2d04-122">Этот образ используется в этом примере для выполнения приложения.</span><span class="sxs-lookup"><span data-stu-id="d2d04-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="d2d04-123">Этот образ содержит среду выполнения и библиотеки ASP.NET Core и оптимизирован для запуска приложений в рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="d2d04-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="d2d04-124">Образ нацелен на высокую скорость развертывания и запуска приложений, поэтому он сравнительно невелик, что позволяет оптимизировать производительность сети между реестром Docker и узлом Docker.</span><span class="sxs-lookup"><span data-stu-id="d2d04-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="d2d04-125">В контейнер копируются только двоичные файлы и содержимое, необходимые для запуска приложений.</span><span class="sxs-lookup"><span data-stu-id="d2d04-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="d2d04-126">Все содержимое готово к запуску, что гарантирует самый короткий срок от запуска `Docker run` до запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="d2d04-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="d2d04-127">В модели Docker динамическая компиляция кода не требуется.</span><span class="sxs-lookup"><span data-stu-id="d2d04-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d2d04-128">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="d2d04-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="d2d04-129">Пакет SDK для .NET Core 2.2</span><span class="sxs-lookup"><span data-stu-id="d2d04-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="d2d04-130">Пакет SDK для .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="d2d04-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="d2d04-131">Клиент Docker 18.03 или более поздней версии</span><span class="sxs-lookup"><span data-stu-id="d2d04-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="d2d04-132">Дистрибутивах Linux</span><span class="sxs-lookup"><span data-stu-id="d2d04-132">Linux distributions</span></span>
    * [<span data-ttu-id="d2d04-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="d2d04-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="d2d04-134">Debian</span><span class="sxs-lookup"><span data-stu-id="d2d04-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="d2d04-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="d2d04-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="d2d04-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="d2d04-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="d2d04-137">macOS</span><span class="sxs-lookup"><span data-stu-id="d2d04-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="d2d04-138">Windows</span><span class="sxs-lookup"><span data-stu-id="d2d04-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="d2d04-139">Git</span><span class="sxs-lookup"><span data-stu-id="d2d04-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="d2d04-140">Скачивание примера приложения</span><span class="sxs-lookup"><span data-stu-id="d2d04-140">Download the sample app</span></span>

* <span data-ttu-id="d2d04-141">Скачайте пример, клонировав [репозиторий Docker для .NET Core](https://github.com/dotnet/dotnet-docker):</span><span class="sxs-lookup"><span data-stu-id="d2d04-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="d2d04-142">Локальный запуск приложения</span><span class="sxs-lookup"><span data-stu-id="d2d04-142">Run the app locally</span></span>

* <span data-ttu-id="d2d04-143">Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp* .</span><span class="sxs-lookup"><span data-stu-id="d2d04-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp* .</span></span>

* <span data-ttu-id="d2d04-144">Выполните следующую команду, чтобы собрать и запустить приложение локально:</span><span class="sxs-lookup"><span data-stu-id="d2d04-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="d2d04-145">В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="d2d04-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="d2d04-146">Нажмите сочетание клавиш CTRL+C в командной строке, чтобы остановить приложение.</span><span class="sxs-lookup"><span data-stu-id="d2d04-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="d2d04-147">Выполнение в контейнере Linux</span><span class="sxs-lookup"><span data-stu-id="d2d04-147">Run in a Linux container</span></span>

* <span data-ttu-id="d2d04-148">Переключите клиент Docker на [контейнеры Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="d2d04-148">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="d2d04-149">Перейдите в папку проекта Dockerfile *dotnet-docker/samples/aspnetapp* .</span><span class="sxs-lookup"><span data-stu-id="d2d04-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp* .</span></span>

* <span data-ttu-id="d2d04-150">Выполните следующие команды, чтобы собрать и запустить пример в Docker:</span><span class="sxs-lookup"><span data-stu-id="d2d04-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="d2d04-151">Команда `build` выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="d2d04-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="d2d04-152">присваивает образу имя aspnetapp;</span><span class="sxs-lookup"><span data-stu-id="d2d04-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="d2d04-153">ищет файл Dockerfile в текущей папке (точка в конце).</span><span class="sxs-lookup"><span data-stu-id="d2d04-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="d2d04-154">Команда run выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="d2d04-154">The run command arguments:</span></span>
  * <span data-ttu-id="d2d04-155">создает псевдотерминал и сохраняет это окно открытым, даже если он не подключен</span><span class="sxs-lookup"><span data-stu-id="d2d04-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="d2d04-156">(действует так же, как `--interactive --tty`);</span><span class="sxs-lookup"><span data-stu-id="d2d04-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="d2d04-157">автоматически удаляет контейнер при завершении работы;</span><span class="sxs-lookup"><span data-stu-id="d2d04-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="d2d04-158">сопоставляет порт 5000 на локальном компьютере с портом 80 в контейнере;</span><span class="sxs-lookup"><span data-stu-id="d2d04-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="d2d04-159">присваивает контейнеру имя aspnetcore_sample;</span><span class="sxs-lookup"><span data-stu-id="d2d04-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="d2d04-160">указывает образ aspnetapp.</span><span class="sxs-lookup"><span data-stu-id="d2d04-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="d2d04-161">В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="d2d04-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="d2d04-162">Запуск в контейнере Windows</span><span class="sxs-lookup"><span data-stu-id="d2d04-162">Run in a Windows container</span></span>

* <span data-ttu-id="d2d04-163">Переключите клиент Docker на [контейнеры Windows](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="d2d04-163">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="d2d04-164">Перейдите к папке файла docker: `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="d2d04-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="d2d04-165">Выполните следующие команды, чтобы собрать и запустить пример в Docker:</span><span class="sxs-lookup"><span data-stu-id="d2d04-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="d2d04-166">Для контейнеров Windows вам нужен IP-адрес контейнера (адрес `http://localhost:5000` не будет работать):</span><span class="sxs-lookup"><span data-stu-id="d2d04-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="d2d04-167">Откройте другую командную строку.</span><span class="sxs-lookup"><span data-stu-id="d2d04-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="d2d04-168">Запустите `docker ps`, чтобы получить список запущенных контейнеров.</span><span class="sxs-lookup"><span data-stu-id="d2d04-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="d2d04-169">Убедитесь, что в списке присутствует контейнер "aspnetcore_sample".</span><span class="sxs-lookup"><span data-stu-id="d2d04-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="d2d04-170">Выполните `docker exec aspnetcore_sample ipconfig`, чтобы отобразить IP-адрес контейнера.</span><span class="sxs-lookup"><span data-stu-id="d2d04-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="d2d04-171">Эта команда возвращает примерно такой результат:</span><span class="sxs-lookup"><span data-stu-id="d2d04-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="d2d04-172">Скопируйте IPv4-адрес контейнера (например 172.29.245.43) и вставьте его в адресную строку браузера, чтобы протестировать приложение.</span><span class="sxs-lookup"><span data-stu-id="d2d04-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="d2d04-173">Локальная сборка и развертывание</span><span class="sxs-lookup"><span data-stu-id="d2d04-173">Build and deploy manually</span></span>

<span data-ttu-id="d2d04-174">В некоторых сценариях вам потребуется развернуть приложение в контейнер, скопировав нужные для выполнения файлы приложения.</span><span class="sxs-lookup"><span data-stu-id="d2d04-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="d2d04-175">В этом разделе показано, как развернуть приложение вручную.</span><span class="sxs-lookup"><span data-stu-id="d2d04-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="d2d04-176">Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp* .</span><span class="sxs-lookup"><span data-stu-id="d2d04-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp* .</span></span>

* <span data-ttu-id="d2d04-177">Выполните команду [dotnet publish](/dotnet/core/tools/dotnet-publish).</span><span class="sxs-lookup"><span data-stu-id="d2d04-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="d2d04-178">Эта команда выполняет следующее:</span><span class="sxs-lookup"><span data-stu-id="d2d04-178">The command arguments:</span></span>
  * <span data-ttu-id="d2d04-179">собирает приложение в режиме выпуска (по умолчанию используется режим отладки);</span><span class="sxs-lookup"><span data-stu-id="d2d04-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="d2d04-180">создает файлы в папке *published* .</span><span class="sxs-lookup"><span data-stu-id="d2d04-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="d2d04-181">Запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="d2d04-181">Run the application.</span></span>

  * <span data-ttu-id="d2d04-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="d2d04-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="d2d04-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="d2d04-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="d2d04-184">Перейдите по адресу `http://localhost:5000` на домашнюю страницу приложения.</span><span class="sxs-lookup"><span data-stu-id="d2d04-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="d2d04-185">Чтобы использовать приложение, опубликованное вручную в контейнере Docker, создайте новый Dockerfile и выполните команду `docker build .`, чтобы создать контейнер.</span><span class="sxs-lookup"><span data-stu-id="d2d04-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="d2d04-186">Файл Dockerfile</span><span class="sxs-lookup"><span data-stu-id="d2d04-186">The Dockerfile</span></span>

<span data-ttu-id="d2d04-187">Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.</span><span class="sxs-lookup"><span data-stu-id="d2d04-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="d2d04-188">Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="d2d04-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="d2d04-189">Файл Dockerfile</span><span class="sxs-lookup"><span data-stu-id="d2d04-189">The Dockerfile</span></span>

<span data-ttu-id="d2d04-190">Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.</span><span class="sxs-lookup"><span data-stu-id="d2d04-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="d2d04-191">Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.</span><span class="sxs-lookup"><span data-stu-id="d2d04-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="d2d04-192">Как отмечалось в предыдущем Dockerfile, файлы `*.csproj` копируются и восстанавливаются в виде отдельных *слоев* .</span><span class="sxs-lookup"><span data-stu-id="d2d04-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers* .</span></span> <span data-ttu-id="d2d04-193">При создании образа команда `docker build` использует встроенный кэш.</span><span class="sxs-lookup"><span data-stu-id="d2d04-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="d2d04-194">Если файлы `*.csproj` не изменялись с момента последнего запуска команды `docker build`, выполнять команду `dotnet restore` повторно не нужно.</span><span class="sxs-lookup"><span data-stu-id="d2d04-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="d2d04-195">Вместо нее используется встроенный кэш для соответствующего слоя `dotnet restore`.</span><span class="sxs-lookup"><span data-stu-id="d2d04-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="d2d04-196">Дополнительные сведения см. в статье [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache) (Рекомендации по созданию файлов Dockerfile).</span><span class="sxs-lookup"><span data-stu-id="d2d04-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d2d04-197">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d2d04-197">Additional resources</span></span>

* [<span data-ttu-id="d2d04-198">Команда Docker build</span><span class="sxs-lookup"><span data-stu-id="d2d04-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="d2d04-199">Команда Docker run</span><span class="sxs-lookup"><span data-stu-id="d2d04-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="d2d04-200">[Пример ASP.NET Core для Docker](https://github.com/dotnet/dotnet-docker) (который используется в этом руководстве).</span><span class="sxs-lookup"><span data-stu-id="d2d04-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="d2d04-201">Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки</span><span class="sxs-lookup"><span data-stu-id="d2d04-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="d2d04-202">Работа со средствами Visual Studio для Docker</span><span class="sxs-lookup"><span data-stu-id="d2d04-202">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="d2d04-203">Отладка с помощью Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2d04-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="d2d04-204">GC с использованием DOCKER и небольших контейнеров</span><span class="sxs-lookup"><span data-stu-id="d2d04-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="d2d04-205">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="d2d04-205">Next steps</span></span>

<span data-ttu-id="d2d04-206">Репозиторий Git помимо примера приложения содержит и документацию.</span><span class="sxs-lookup"><span data-stu-id="d2d04-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="d2d04-207">Обзор ресурсов, доступных в этом репозитории, см. [в файле README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="d2d04-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="d2d04-208">Особый интерес представляют сведения о реализации протокола HTTPS:</span><span class="sxs-lookup"><span data-stu-id="d2d04-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="d2d04-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) (Разработка приложений ASP.NET Core с применением Docker по протоколу HTTPS)</span><span class="sxs-lookup"><span data-stu-id="d2d04-209">[Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)</span></span>
