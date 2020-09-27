---
title: Размещение образа ASP.NET Core в контейнере с помощью создания DOCKER с HTTPS
author: ravipal
description: Сведения о размещении образов ASP.NET Core с помощью Docker Compose по протоколу HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: cd46fdcbe10dc0b7829fbe7eaef821889f395df4
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393708"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a><span data-ttu-id="b8f92-103">Размещение образов ASP.NET Core с Docker Compose по протоколу HTTPS</span><span class="sxs-lookup"><span data-stu-id="b8f92-103">Hosting ASP.NET Core images with Docker Compose over HTTPS</span></span>


<span data-ttu-id="b8f92-104">По [умолчанию ASP.NET Core использует протокол HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="b8f92-104">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="b8f92-105">[Протокол HTTPS](https://en.wikipedia.org/wiki/HTTPS) использует [Сертификаты](https://en.wikipedia.org/wiki/Public_key_certificate) для доверия, удостоверений и шифрования.</span><span class="sxs-lookup"><span data-stu-id="b8f92-105">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="b8f92-106">В этом документе объясняется, как запускать предварительно созданные образы контейнеров с помощью протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b8f92-106">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="b8f92-107">Сценарии разработки см. [в статье Разработка приложений ASP.NET Core с помощью DOCKER по протоколу HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="b8f92-107">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="b8f92-108">Для этого примера требуется [docker 17,06](https://docs.docker.com/release-notes/docker-ce) или более поздней версии [клиента DOCKER](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="b8f92-108">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b8f92-109">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="b8f92-109">Prerequisites</span></span>

<span data-ttu-id="b8f92-110">Для выполнения некоторых инструкций в этом документе требуется [пакет SDK для .NET Core 2,2](https://dotnet.microsoft.com/download) или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="b8f92-110">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="b8f92-111">Сертификаты</span><span class="sxs-lookup"><span data-stu-id="b8f92-111">Certificates</span></span>

<span data-ttu-id="b8f92-112">Сертификат из [центра](https://wikipedia.org/wiki/Certificate_authority) сертификации необходим для [размещения в рабочей среде](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) для домена.</span><span class="sxs-lookup"><span data-stu-id="b8f92-112">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="b8f92-113">[Let's Encrypt](https://letsencrypt.org/) — Это центр сертификации, предлагающий бесплатные сертификаты.</span><span class="sxs-lookup"><span data-stu-id="b8f92-113">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="b8f92-114">В этом документе используются [самозаверяющие сертификаты разработки](https://wikipedia.org/wiki/Self-signed_certificate) для размещения предварительно созданных образов `localhost` .</span><span class="sxs-lookup"><span data-stu-id="b8f92-114">This document uses [self-signed development certificates](https://wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="b8f92-115">Инструкции аналогичны использованию рабочих сертификатов.</span><span class="sxs-lookup"><span data-stu-id="b8f92-115">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="b8f92-116">Для рабочих сертификатов:</span><span class="sxs-lookup"><span data-stu-id="b8f92-116">For production certificates:</span></span>

* <span data-ttu-id="b8f92-117">`dotnet dev-certs`Средство не требуется.</span><span class="sxs-lookup"><span data-stu-id="b8f92-117">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="b8f92-118">Сертификаты не нужно хранить в расположении, используемом в инструкциях.</span><span class="sxs-lookup"><span data-stu-id="b8f92-118">Certificates don't need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="b8f92-119">Храните сертификаты в любом расположении за пределами каталога сайта.</span><span class="sxs-lookup"><span data-stu-id="b8f92-119">Store the certificates in any location outside the site directory.</span></span>

<span data-ttu-id="b8f92-120">Инструкции, приведенные в следующем разделе, содержат сведения о подключении сертификатов в контейнеры с помощью `volumes` свойства в *DOCKER-Compose. yml.*</span><span class="sxs-lookup"><span data-stu-id="b8f92-120">The instructions contained in the following section volume mount certificates into containers using the `volumes` property in *docker-compose.yml.*</span></span> <span data-ttu-id="b8f92-121">Вы можете добавить сертификаты в образы контейнеров с помощью `COPY` команды в *Dockerfile*, но это не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="b8f92-121">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="b8f92-122">Копирование сертификатов в образ не рекомендуется по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="b8f92-122">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="b8f92-123">Это затрудняет использование одного и того же образа для тестирования с помощью сертификатов разработчика.</span><span class="sxs-lookup"><span data-stu-id="b8f92-123">It makes it difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="b8f92-124">Это затрудняет использование одного и того же образа для размещения с производственными сертификатами.</span><span class="sxs-lookup"><span data-stu-id="b8f92-124">It makes it difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="b8f92-125">Существует значительный риск раскрытия сертификата.</span><span class="sxs-lookup"><span data-stu-id="b8f92-125">There is significant risk of certificate disclosure.</span></span>

## <a name="starting-a-container-with-https-support-using-docker-compose"></a><span data-ttu-id="b8f92-126">Запуск контейнера с поддержкой HTTPS с помощью создания DOCKER</span><span class="sxs-lookup"><span data-stu-id="b8f92-126">Starting a container with https support using docker compose</span></span>

<span data-ttu-id="b8f92-127">Используйте следующие инструкции для настройки операционной системы.</span><span class="sxs-lookup"><span data-stu-id="b8f92-127">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="b8f92-128">Windows с контейнерами Linux</span><span class="sxs-lookup"><span data-stu-id="b8f92-128">Windows using Linux containers</span></span>

<span data-ttu-id="b8f92-129">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="b8f92-129">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="b8f92-130">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="b8f92-130">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="b8f92-131">Создайте файл _DOCKER-Compose. Debug. yml_ со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="b8f92-131">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="b8f92-132">Пароль, указанный в файле создания DOCKER, должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="b8f92-132">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="b8f92-133">Запустите контейнер с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="b8f92-133">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a><span data-ttu-id="b8f92-134">macOS или Linux</span><span class="sxs-lookup"><span data-stu-id="b8f92-134">macOS or Linux</span></span>

<span data-ttu-id="b8f92-135">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="b8f92-135">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="b8f92-136">`dotnet dev-certs https --trust` поддерживается только в macOS и Windows.</span><span class="sxs-lookup"><span data-stu-id="b8f92-136">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="b8f92-137">Необходимо доверять сертификатам в Linux так, как это поддерживается дистрибутивом.</span><span class="sxs-lookup"><span data-stu-id="b8f92-137">You need to trust certificates on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="b8f92-138">Вероятно, вам нужно доверять сертификату в браузере.</span><span class="sxs-lookup"><span data-stu-id="b8f92-138">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="b8f92-139">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="b8f92-139">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="b8f92-140">Создайте файл _DOCKER-Compose. Debug. yml_ со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="b8f92-140">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
<span data-ttu-id="b8f92-141">Пароль, указанный в файле создания DOCKER, должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="b8f92-141">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="b8f92-142">Запустите контейнер с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="b8f92-142">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a><span data-ttu-id="b8f92-143">Windows, использующих контейнеры Windows</span><span class="sxs-lookup"><span data-stu-id="b8f92-143">Windows using Windows containers</span></span>

<span data-ttu-id="b8f92-144">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="b8f92-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="b8f92-145">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="b8f92-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="b8f92-146">Создайте файл _DOCKER-Compose. Debug. yml_ со следующим содержимым:</span><span class="sxs-lookup"><span data-stu-id="b8f92-146">Create a _docker-compose.debug.yml_ file with the following content:</span></span>

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
<span data-ttu-id="b8f92-147">Пароль, указанный в файле создания DOCKER, должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="b8f92-147">The password specified in the docker compose file must match the password used for the certificate.</span></span>

<span data-ttu-id="b8f92-148">Запустите контейнер с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="b8f92-148">Start the container with ASP.NET Core configured for HTTPS:</span></span>

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
