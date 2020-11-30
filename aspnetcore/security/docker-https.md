---
title: Размещение образов ASP.NET Core с помощью DOCKER по протоколу HTTPS
author: rick-anderson
description: Сведения о размещении образов ASP.NET Core с помощью DOCKER по протоколу HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: a4aac2ce06fee20bdef157efc361f3099a217b1a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332158"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="a07db-103">Размещение образов ASP.NET Core с помощью DOCKER по протоколу HTTPS</span><span class="sxs-lookup"><span data-stu-id="a07db-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="a07db-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="a07db-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a07db-105">По [умолчанию ASP.NET Core использует протокол HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="a07db-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="a07db-106">[Протокол HTTPS](https://en.wikipedia.org/wiki/HTTPS) использует [Сертификаты](https://en.wikipedia.org/wiki/Public_key_certificate) для доверия, удостоверений и шифрования.</span><span class="sxs-lookup"><span data-stu-id="a07db-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="a07db-107">В этом документе объясняется, как запускать предварительно созданные образы контейнеров с помощью протокола HTTPS.</span><span class="sxs-lookup"><span data-stu-id="a07db-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="a07db-108">Сценарии разработки см. [в статье Разработка приложений ASP.NET Core с помощью DOCKER по протоколу HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .</span><span class="sxs-lookup"><span data-stu-id="a07db-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="a07db-109">Для этого примера требуется [docker 17,06](https://docs.docker.com/release-notes/docker-ce) или более поздней версии [клиента DOCKER](https://www.docker.com/products/docker).</span><span class="sxs-lookup"><span data-stu-id="a07db-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a07db-110">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="a07db-110">Prerequisites</span></span>

<span data-ttu-id="a07db-111">Для выполнения некоторых инструкций в этом документе требуется [пакет SDK для .NET Core 2,2](https://dotnet.microsoft.com/download) или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="a07db-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="a07db-112">Сертификаты</span><span class="sxs-lookup"><span data-stu-id="a07db-112">Certificates</span></span>

<span data-ttu-id="a07db-113">Сертификат из [центра](https://wikipedia.org/wiki/Certificate_authority) сертификации необходим для [размещения в рабочей среде](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) для домена.</span><span class="sxs-lookup"><span data-stu-id="a07db-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="a07db-114">[Let's Encrypt](https://letsencrypt.org/) — Это центр сертификации, предлагающий бесплатные сертификаты.</span><span class="sxs-lookup"><span data-stu-id="a07db-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="a07db-115">В этом документе используются [самозаверяющие сертификаты разработки](https://en.wikipedia.org/wiki/Self-signed_certificate) для размещения предварительно созданных образов `localhost` .</span><span class="sxs-lookup"><span data-stu-id="a07db-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="a07db-116">Инструкции аналогичны использованию рабочих сертификатов.</span><span class="sxs-lookup"><span data-stu-id="a07db-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="a07db-117">Используйте [DotNet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) для создания самозаверяющих сертификатов для разработки и тестирования.</span><span class="sxs-lookup"><span data-stu-id="a07db-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="a07db-118">Для производственных сертификатов:</span><span class="sxs-lookup"><span data-stu-id="a07db-118">For production certs:</span></span>

* <span data-ttu-id="a07db-119">`dotnet dev-certs`Средство не требуется.</span><span class="sxs-lookup"><span data-stu-id="a07db-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="a07db-120">Сертификаты не обязательно хранить в расположении, используемом в инструкциях.</span><span class="sxs-lookup"><span data-stu-id="a07db-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="a07db-121">Должно работать любое расположение, хотя не рекомендуется хранить сертификаты в каталоге сайта.</span><span class="sxs-lookup"><span data-stu-id="a07db-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="a07db-122">Инструкции, приведенные в следующем разделе, содержат сведения о подключении сертификатов к контейнерам с помощью `-v` параметра командной строки DOCKER.</span><span class="sxs-lookup"><span data-stu-id="a07db-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="a07db-123">Вы можете добавить сертификаты в образы контейнеров с помощью `COPY` команды в *Dockerfile*, но это не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="a07db-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="a07db-124">Копирование сертификатов в образ не рекомендуется по следующим причинам:</span><span class="sxs-lookup"><span data-stu-id="a07db-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="a07db-125">Использование одного и того же образа для тестирования с помощью сертификатов разработчика затрудняется.</span><span class="sxs-lookup"><span data-stu-id="a07db-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="a07db-126">Использование одного и того же образа для размещения с производственными сертификатами затрудняется.</span><span class="sxs-lookup"><span data-stu-id="a07db-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="a07db-127">Существует значительный риск раскрытия сертификата.</span><span class="sxs-lookup"><span data-stu-id="a07db-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="a07db-128">Запуск предварительно созданных образов контейнеров с помощью HTTPS</span><span class="sxs-lookup"><span data-stu-id="a07db-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="a07db-129">Используйте следующие инструкции для настройки операционной системы.</span><span class="sxs-lookup"><span data-stu-id="a07db-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="a07db-130">Windows с контейнерами Linux</span><span class="sxs-lookup"><span data-stu-id="a07db-130">Windows using Linux containers</span></span>

<span data-ttu-id="a07db-131">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="a07db-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="a07db-132">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="a07db-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="a07db-133">Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS, в командной оболочке:</span><span class="sxs-lookup"><span data-stu-id="a07db-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="a07db-134">При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="a07db-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="a07db-135">Пароль должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="a07db-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="a07db-136">Примечание. в этом случае сертификат должен быть `.pfx` файлом.</span><span class="sxs-lookup"><span data-stu-id="a07db-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="a07db-137">`.crt` `.key` Использование файла или с паролем или без него не поддерживается в образце контейнера.</span><span class="sxs-lookup"><span data-stu-id="a07db-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="a07db-138">Например, при указании `.crt` файла контейнер может вернуть сообщения об ошибке, например "серверный протокол SSL должен использовать сертификат со связанным закрытым ключом".</span><span class="sxs-lookup"><span data-stu-id="a07db-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="a07db-139">При использовании [WSL](/windows/wsl/about)Проверьте путь подключения, чтобы убедиться, что сертификат загружается правильно.</span><span class="sxs-lookup"><span data-stu-id="a07db-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="a07db-140">macOS или Linux</span><span class="sxs-lookup"><span data-stu-id="a07db-140">macOS or Linux</span></span>

<span data-ttu-id="a07db-141">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="a07db-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="a07db-142">`dotnet dev-certs https --trust` поддерживается только в macOS и Windows.</span><span class="sxs-lookup"><span data-stu-id="a07db-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="a07db-143">Необходимо доверять сертификатам в Linux так, как это поддерживается дистрибутивом.</span><span class="sxs-lookup"><span data-stu-id="a07db-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="a07db-144">Вероятно, вам нужно доверять сертификату в браузере.</span><span class="sxs-lookup"><span data-stu-id="a07db-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="a07db-145">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="a07db-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="a07db-146">Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="a07db-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="a07db-147">Пароль должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="a07db-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="a07db-148">Windows, использующих контейнеры Windows</span><span class="sxs-lookup"><span data-stu-id="a07db-148">Windows using Windows containers</span></span>

<span data-ttu-id="a07db-149">Создать сертификат и настроить локальный компьютер:</span><span class="sxs-lookup"><span data-stu-id="a07db-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="a07db-150">В предыдущих командах замените `{ password here }` паролем.</span><span class="sxs-lookup"><span data-stu-id="a07db-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="a07db-151">При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="a07db-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="a07db-152">Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS:</span><span class="sxs-lookup"><span data-stu-id="a07db-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="a07db-153">Пароль должен совпадать с паролем, используемым для сертификата.</span><span class="sxs-lookup"><span data-stu-id="a07db-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="a07db-154">При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="a07db-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
