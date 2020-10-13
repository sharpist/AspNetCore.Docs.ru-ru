---
title: Файл отключения приложения (app_offline.htm)
author: rick-anderson
description: Сведения об использовании файла отключения приложения (`app_offline.htm`) с модулем ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755211"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="055ed-103">Файл отключения приложения (`app_offline.htm`)</span><span class="sxs-lookup"><span data-stu-id="055ed-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="055ed-104">Файл отключения приложения (`app_offline.htm`) используется модулем ASP.NET Core для завершения работы приложения.</span><span class="sxs-lookup"><span data-stu-id="055ed-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="055ed-105">Если в корневом каталоге приложения обнаружен файл с именем `app_offline.htm`, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов.</span><span class="sxs-lookup"><span data-stu-id="055ed-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="055ed-106">Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.</span><span class="sxs-lookup"><span data-stu-id="055ed-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="055ed-107">Хотя файл `app_offline.htm` присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла `app_offline.htm`.</span><span class="sxs-lookup"><span data-stu-id="055ed-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="055ed-108">Когда `app_offline.htm` файл удаляется, следующий запрос запускает приложение.</span><span class="sxs-lookup"><span data-stu-id="055ed-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="055ed-109">При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения.</span><span class="sxs-lookup"><span data-stu-id="055ed-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="055ed-110">Например, подключение WebSocket может задерживать завершение работы приложения.</span><span class="sxs-lookup"><span data-stu-id="055ed-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="055ed-111">Заблокированные файлы развертывания</span><span class="sxs-lookup"><span data-stu-id="055ed-111">Locked deployment files</span></span>

<span data-ttu-id="055ed-112">Во время выполнения приложения файлы в папке развертывания блокируются.</span><span class="sxs-lookup"><span data-stu-id="055ed-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="055ed-113">Заблокированные файлы невозможно перезаписать во время развертывания.</span><span class="sxs-lookup"><span data-stu-id="055ed-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="055ed-114">`app_offline.htm` является основным механизмом для освобождения заблокированных файлов.</span><span class="sxs-lookup"><span data-stu-id="055ed-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="055ed-115">`app_offline.htm` используется веб-развертыванием для корректной остановки и запуска приложения.</span><span class="sxs-lookup"><span data-stu-id="055ed-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="055ed-116">`app_offline.htm` можно использовать для запуска и остановки приложения вручную (требуется PowerShell 5 или более поздней версии).</span><span class="sxs-lookup"><span data-stu-id="055ed-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="055ed-117">В предыдущем скрипте PowerShell:</span><span class="sxs-lookup"><span data-stu-id="055ed-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="055ed-118">заполнитель `{PATH TO APP}` — это путь к приложению;</span><span class="sxs-lookup"><span data-stu-id="055ed-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="055ed-119">команда `New-Item` останавливает работу пула приложений;</span><span class="sxs-lookup"><span data-stu-id="055ed-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="055ed-120">команда `Remove-Item` запускает пул приложений;</span><span class="sxs-lookup"><span data-stu-id="055ed-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="055ed-121">команды между командами `New-Item` и `Remove-Item` предоставляются разработчиком для развертывания приложения.</span><span class="sxs-lookup"><span data-stu-id="055ed-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="055ed-122">Файлы также можно разблокировать путем остановки пула приложений вручную в диспетчере служб IIS на сервере.</span><span class="sxs-lookup"><span data-stu-id="055ed-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="055ed-123">Не используйте файл `app_offine.htm` при остановке и перезапуске пула приложений с помощью диспетчера IIS.</span><span class="sxs-lookup"><span data-stu-id="055ed-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
