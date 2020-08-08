---
title: Размещение образа ASP.NET Core в контейнере с помощью создания DOCKER с HTTPS
author: ravipal
description: Сведения о размещении образов ASP.NET Core с помощью Docker Compose по протоколу HTTPS
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: c3b627cdc74f1b40611d84bc3419e678e2dfbba4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022463"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Размещение образов ASP.NET Core с Docker Compose по протоколу HTTPS


По [умолчанию ASP.NET Core использует протокол HTTPS](/aspnet/core/security/enforcing-ssl). [Протокол HTTPS](https://en.wikipedia.org/wiki/HTTPS) использует [Сертификаты](https://en.wikipedia.org/wiki/Public_key_certificate) для доверия, удостоверений и шифрования.

В этом документе объясняется, как запускать предварительно созданные образы контейнеров с помощью протокола HTTPS.

Сценарии разработки см. [в статье Разработка приложений ASP.NET Core с помощью DOCKER по протоколу HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) .

Для этого примера требуется [docker 17,06](https://docs.docker.com/release-notes/docker-ce) или более поздней версии [клиента DOCKER](https://www.docker.com/products/docker).

## <a name="prerequisites"></a>Предварительные требования

Для выполнения некоторых инструкций в этом документе требуется [пакет SDK для .NET Core 2,2](https://dotnet.microsoft.com/download) или более поздней версии.

## <a name="certificates"></a>сертификаты;

Сертификат из [центра](https://wikipedia.org/wiki/Certificate_authority) сертификации необходим для [размещения в рабочей среде](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) для домена. [Let's Encrypt](https://letsencrypt.org/)— Это центр сертификации, предлагающий бесплатные сертификаты.

В этом документе используются [самозаверяющие сертификаты разработки](https://wikipedia.org/wiki/Self-signed_certificate) для размещения предварительно созданных образов `localhost` . Инструкции аналогичны использованию рабочих сертификатов.

Для рабочих сертификатов:

* `dotnet dev-certs`Средство не требуется.
* Сертификаты не нужно хранить в расположении, используемом в инструкциях. Храните сертификаты в любом расположении за пределами каталога сайта.

Инструкции, приведенные в следующем разделе, содержат сведения о подключении сертификатов в контейнеры с помощью `volumes` свойства в *DOCKER-Compose. yml.* Вы можете добавить сертификаты в образы контейнеров с помощью `COPY` команды в *Dockerfile*, но это не рекомендуется. Копирование сертификатов в образ не рекомендуется по следующим причинам:

* Это затрудняет использование одного и того же образа для тестирования с помощью сертификатов разработчика.
* Это затрудняет использование одного и того же образа для размещения с производственными сертификатами.
* Существует значительный риск раскрытия сертификата.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Запуск контейнера с поддержкой HTTPS с помощью создания DOCKER

Используйте следующие инструкции для настройки операционной системы.

### <a name="windows-using-linux-containers"></a>Windows с контейнерами Linux

Создать сертификат и настроить локальный компьютер:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

В предыдущих командах замените `{ password here }` паролем.

Создайте файл _DOCKER-Compose. Debug. yml_ со следующим содержимым:

```json
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
Пароль, указанный в файле создания DOCKER, должен совпадать с паролем, используемым для сертификата.

Запустите контейнер с ASP.NET Core, настроенным для HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS или Linux

Создать сертификат и настроить локальный компьютер:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust`поддерживается только в macOS и Windows. Необходимо доверять сертификатам в Linux так, как это поддерживается дистрибутивом. Вероятно, вам нужно доверять сертификату в браузере.

В предыдущих командах замените `{ password here }` паролем.

Создайте файл _DOCKER-Compose. Debug. yml_ со следующим содержимым:

```json
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
Пароль, указанный в файле создания DOCKER, должен совпадать с паролем, используемым для сертификата.

Запустите контейнер с ASP.NET Core, настроенным для HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows, использующих контейнеры Windows

Создать сертификат и настроить локальный компьютер:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

В предыдущих командах замените `{ password here }` паролем.

Создайте файл _DOCKER-Compose. Debug. yml_ со следующим содержимым:

```json
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
Пароль, указанный в файле создания DOCKER, должен совпадать с паролем, используемым для сертификата.

Запустите контейнер с ASP.NET Core, настроенным для HTTPS:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
