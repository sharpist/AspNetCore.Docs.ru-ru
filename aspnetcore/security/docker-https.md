---
title: Размещение образов ASP.NET Core с помощью DOCKER по протоколу HTTPS
author: rick-anderson
description: Сведения о размещении образов ASP.NET Core с помощью DOCKER по протоколу HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: bba72a8c795312b3f7fe3fbf16514ba9f2448fbf
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020344"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>Размещение образов ASP.NET Core с помощью DOCKER по протоколу HTTPS

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

По [умолчанию ASP.NET Core использует протокол HTTPS](/aspnet/core/security/enforcing-ssl). [Протокол HTTPS](https://en.wikipedia.org/wiki/HTTPS) использует [Сертификаты](https://en.wikipedia.org/wiki/Public_key_certificate) для доверия, удостоверений и шифрования.

В этом документе объясняется, как запускать предварительно созданные образы контейнеров с помощью протокола HTTPS.

Сценарии разработки см. [в статье Разработка приложений ASP.NET Core с помощью DOCKER по протоколу HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .

Для этого примера требуется [docker 17,06](https://docs.docker.com/release-notes/docker-ce) или более поздней версии [клиента DOCKER](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Предварительные требования

Для выполнения некоторых инструкций в этом документе требуется [пакет SDK для .NET Core 2,2](https://dotnet.microsoft.com/download) или более поздней версии.

## <a name="certificates"></a>сертификаты;

Сертификат из [центра](https://wikipedia.org/wiki/Certificate_authority) сертификации необходим для [размещения в рабочей среде](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) для домена. [Let's Encrypt](https://letsencrypt.org/)— Это центр сертификации, предлагающий бесплатные сертификаты.

В этом документе используются [самозаверяющие сертификаты разработки](https://en.wikipedia.org/wiki/Self-signed_certificate) для размещения предварительно созданных образов `localhost` . Инструкции аналогичны использованию рабочих сертификатов.

Для производственных сертификатов:

* `dotnet dev-certs`Средство не требуется.
* Сертификаты не обязательно хранить в расположении, используемом в инструкциях. Должно работать любое расположение, хотя не рекомендуется хранить сертификаты в каталоге сайта.

Инструкции, приведенные в следующем разделе, содержат сведения о подключении сертификатов к контейнерам с помощью `-v` параметра командной строки DOCKER. Вы можете добавить сертификаты в образы контейнеров с помощью `COPY` команды в *Dockerfile*, но это не рекомендуется. Копирование сертификатов в образ не рекомендуется по следующим причинам:

* Использование одного и того же образа для тестирования с помощью сертификатов разработчика затрудняется.
* Использование одного и того же образа для размещения с производственными сертификатами затрудняется.
* Существует значительный риск раскрытия сертификата.

## <a name="running-pre-built-container-images-with-https"></a>Запуск предварительно созданных образов контейнеров с помощью HTTPS

Используйте следующие инструкции для настройки операционной системы.

### <a name="windows-using-linux-containers"></a>Windows с контейнерами Linux

Создать сертификат и настроить локальный компьютер:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

В предыдущих командах замените `{ password here }` паролем.

Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS, в командной оболочке:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .

Пароль должен совпадать с паролем, используемым для сертификата.

### <a name="macos-or-linux"></a>macOS или Linux

Создать сертификат и настроить локальный компьютер:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`поддерживается только в macOS и Windows. Необходимо доверять сертификатам в Linux так, как это поддерживается дистрибутивом. Вероятно, вам нужно доверять сертификату в браузере.

В предыдущих командах замените `{ password here }` паролем.

Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Пароль должен совпадать с паролем, используемым для сертификата.

### <a name="windows-using-windows-containers"></a>Windows, использующих контейнеры Windows

Создать сертификат и настроить локальный компьютер:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

В предыдущих командах замените `{ password here }` паролем. При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .

Запустите образ контейнера с ASP.NET Core, настроенным для HTTPS:

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

Пароль должен совпадать с паролем, используемым для сертификата. При использовании [PowerShell](/powershell/scripting/overview)замените `%USERPROFILE%` на `$env:USERPROFILE` .
