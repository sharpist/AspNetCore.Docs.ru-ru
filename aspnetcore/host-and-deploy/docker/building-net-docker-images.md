---
title: Образы Docker для ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать опубликованные образы Docker для .NET Core из реестра Docker. Извлечение и создание образов.
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: ced0cb7cbeed1b8811813a70035c2e0b42c3e35a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440783"
---
# <a name="docker-images-for-aspnet-core"></a>Образы Docker для ASP.NET Core

В этом руководстве показано, как запустить приложение ASP.NET Core в контейнерах Docker.

В этом учебнике рассмотрены следующие задачи.
> [!div class="checklist"]
> * узнаете о создании образов Docker для Microsoft .NET Core;
> * скачивание примера приложения ASP.NET Core;
> * локальное выполнение примера приложения;
> * выполнение примера приложения в контейнерах Linux;
> * выполнение примера приложения в контейнерах Windows;
> * локальная сборка и развертывание.

## <a name="aspnet-core-docker-images"></a>Образы Docker для .NET Core

Для работы с этим руководством следует скачать пример приложения ASP.NET Core и запустить его в контейнерах Docker. Этот пример поддерживается в контейнерах Linux и Windows.

Пример файла Dockerfile использует [функцию многоэтапной сборки Docker](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) для сборки и выполнения в разных контейнерах. Контейнеры для сборки и выполнения создаются на основе образов, предоставленных корпорацией Майкрософт в Docker Hub:

* `dotnet/core/sdk`

  Этот образ используется в этом примере для создания приложения. Образ содержит пакет SDK для .NET Core, который включает программы командной строки (CLI). Он оптимизирован для локальной разработки, отладки и модульного тестирования. Установленные средства разработки и компиляции делают этот образ относительно большим. 

* `dotnet/core/aspnet`

   Этот образ используется в этом примере для выполнения приложения. Этот образ содержит среду выполнения и библиотеки ASP.NET Core и оптимизирован для запуска приложений в рабочей среде. Образ нацелен на высокую скорость развертывания и запуска приложений, поэтому он сравнительно невелик, что позволяет оптимизировать производительность сети между реестром Docker и узлом Docker. В контейнер копируются только двоичные файлы и содержимое, необходимые для запуска приложений. Все содержимое готово к запуску, что гарантирует самый короткий срок от запуска `Docker run` до запуска приложения. В модели Docker динамическая компиляция кода не требуется.

## <a name="prerequisites"></a>Предварительные требования
::: moniker range="< aspnetcore-3.0"

* [Пакет SDK для .NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [Пакет SDK для .NET Core 3.0](https://dotnet.microsoft.com/download)

::: moniker-end

* Клиент Docker 18.03 или более поздней версии

  * Дистрибутивах Linux
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Скачивание примера приложения

* Скачайте пример, клонировав [репозиторий Docker для .NET Core](https://github.com/dotnet/dotnet-docker): 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Локальный запуск приложения

* Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Выполните следующую команду, чтобы собрать и запустить приложение локально:

  ```dotnetcli
  dotnet run
  ```

* В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.

* Нажмите сочетание клавиш CTRL+C в командной строке, чтобы остановить приложение.

## <a name="run-in-a-linux-container"></a>Выполнение в контейнере Linux

* Переключите клиент Docker на контейнеры Linux.

* Перейдите в папку проекта Dockerfile *dotnet-docker/samples/aspnetapp*.

* Выполните следующие команды, чтобы собрать и запустить пример в Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Команда `build` выполняет следующее:
  * присваивает образу имя aspnetapp;
  * ищет файл Dockerfile в текущей папке (точка в конце).

  Команда run выполняет следующее:
  * создает псевдотерминал и сохраняет это окно открытым, даже если он не подключен (действует так же, как `--interactive --tty`);
  * автоматически удаляет контейнер при завершении работы;
  * сопоставляет порт 5000 на локальном компьютере с портом 80 в контейнере;
  * присваивает контейнеру имя aspnetcore_sample;
  * указывает образ aspnetapp.

* В браузере перейдите по адресу `http://localhost:5000`, чтобы протестировать приложение.

## <a name="run-in-a-windows-container"></a>Запуск в контейнере Windows

* Переключите клиент Docker на контейнеры Windows.

Перейдите к папке файла docker: `dotnet-docker/samples/aspnetapp`.

* Выполните следующие команды, чтобы собрать и запустить пример в Docker:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Для контейнеров Windows вам нужен IP-адрес контейнера (адрес `http://localhost:5000` не будет работать):
  * Откройте другую командную строку.
  * Запустите `docker ps`, чтобы получить список запущенных контейнеров. Убедитесь, что в списке присутствует контейнер "aspnetcore_sample".
  * Выполните `docker exec aspnetcore_sample ipconfig`, чтобы отобразить IP-адрес контейнера. Эта команда возвращает примерно такой результат:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Скопируйте IPv4-адрес контейнера (например 172.29.245.43) и вставьте его в адресную строку браузера, чтобы протестировать приложение.

## <a name="build-and-deploy-manually"></a>Локальная сборка и развертывание

В некоторых сценариях вам потребуется развернуть приложение в контейнер, скопировав нужные для выполнения файлы приложения. В этом разделе показано, как развернуть приложение вручную.

* Перейдите в папку проекта *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Выполните команду [dotnet publish](/dotnet/core/tools/dotnet-publish).

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Эта команда выполняет следующее:
  * собирает приложение в режиме выпуска (по умолчанию используется режим отладки);
  * создает файлы в папке *published*.

* Запустите приложение.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Перейдите по адресу `http://localhost:5000` на домашнюю страницу приложения.

Чтобы использовать приложение, опубликованное вручную в контейнере Docker, создайте новый Dockerfile и выполните команду `docker build .`, чтобы создать контейнер.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Файл Dockerfile

Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.  Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.  

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

### <a name="the-dockerfile"></a>Файл Dockerfile

Представленный здесь файл *Dockerfile* используется в команде `docker build`, которую вы выполняли ранее.  Она использует `dotnet publish` для создания и развертывания так же, как показано в этом разделе.  

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

::: moniker-end

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Команда Docker build](https://docs.docker.com/engine/reference/commandline/build)
* [Команда Docker run](https://docs.docker.com/engine/reference/commandline/run)
* [Пример ASP.NET Core для Docker](https://github.com/dotnet/dotnet-docker) (который используется в этом руководстве).
* [Настройка ASP.NET Core для работы с прокси-серверами и подсистемами балансировки нагрузки](/aspnet/core/host-and-deploy/proxy-load-balancer)
* [Работа со средствами Visual Studio для Docker](https://docs.microsoft.com/aspnet/core/publishing/visual-studio-tools-for-docker)
* [Отладка с помощью Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC с использованием DOCKER и небольших контейнеров](xref:performance/memory#sc)

## <a name="next-steps"></a>Следующие шаги

Репозиторий Git помимо примера приложения содержит и документацию. Обзор ресурсов, доступных в этом репозитории, см. [в файле README](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). Особый интерес представляют сведения о реализации протокола HTTPS:

> [!div class="nextstepaction"]
> [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) (Разработка приложений ASP.NET Core с применением Docker по протоколу HTTPS)
