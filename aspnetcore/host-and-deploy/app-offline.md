---
title: Файл отключения приложения (app_offline.htm)
author: rick-anderson
description: Сведения об использовании файла отключения приложения (`app_offline.htm`) с модулем ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 4d71b95680a9b160ebb25116e35096495a2eaf93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058653"
---
# <a name="app-offline-file-app_offlinehtm"></a>Файл отключения приложения (`app_offline.htm`)

Файл отключения приложения (`app_offline.htm`) используется модулем ASP.NET Core для завершения работы приложения.

Если в корневом каталоге приложения обнаружен файл с именем `app_offline.htm`, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов. Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.

Хотя файл `app_offline.htm` присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла `app_offline.htm`. Когда `app_offline.htm` файл удаляется, следующий запрос запускает приложение.

При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения. Например, подключение WebSocket может задерживать завершение работы приложения.

## <a name="locked-deployment-files"></a>Заблокированные файлы развертывания

Во время выполнения приложения файлы в папке развертывания блокируются. Заблокированные файлы невозможно перезаписать во время развертывания.

`app_offline.htm` является основным механизмом для освобождения заблокированных файлов. `app_offline.htm` используется веб-развертыванием для корректной остановки и запуска приложения.

`app_offline.htm` можно использовать для запуска и остановки приложения вручную (требуется PowerShell 5 или более поздней версии).

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

В предыдущем скрипте PowerShell:

* заполнитель `{PATH TO APP}` — это путь к приложению;
* команда `New-Item` останавливает работу пула приложений;
* команда `Remove-Item` запускает пул приложений;
* команды между командами `New-Item` и `Remove-Item` предоставляются разработчиком для развертывания приложения.

Файлы также можно разблокировать путем остановки пула приложений вручную в диспетчере служб IIS на сервере. Не используйте файл `app_offine.htm` при остановке и перезапуске пула приложений с помощью диспетчера IIS.
