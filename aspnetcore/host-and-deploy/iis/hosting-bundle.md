---
title: Пакет размещения
author: rick-anderson
description: Узнайте, как настроить пакет размещения .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755178"
---
# <a name="the-net-core-hosting-bundle"></a>Пакет размещения .NET Core

Пакет размещения .NET Core — это установщик среды выполнения .NET Core и [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Пакет позволяет запускать приложения ASP.NET Core в службах IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Установка пакета размещения .NET Core

> [!IMPORTANT]
> Если пакет размещения устанавливается до установки служб IIS, его нужно восстановить. После установки служб IIS запустите установщик пакета размещения еще раз.
>
> Если пакет размещения устанавливается после установки 64-разрядной (x 64) версии .NET Core, пакеты SDK могут не отображаться (см. раздел [Пакеты SDK .NET Core не обнаружены](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Сведения об устранении проблемы см. в статье <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Прямая загрузка (текущая версия)

Скачайте установщик по следующей ссылке:

[Текущий установщик пакета размещения .NET Core (прямая загрузка)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Более ранние версии установщика

Получение более ранней версии установщика:

1. Перейдите на страницу [загрузки .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
1. Выберите требуемую версию .NET Core.
1. В столбце **Запуск приложений — среда выполнения** найдите строку, содержащую нужную версию среды выполнения .NET Core.
1. Скачайте установщик по ссылке **Hosting Bundle** (Пакет размещения).

> [!WARNING]
> Некоторые установщики содержат версии выпусков, которые достигли конца своего жизненного цикла и больше не поддерживаются корпорацией Майкрософт. Дополнительные сведения см. в разделе [Политика поддержки](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Установка пакета размещения .NET Core

1. Запустите установщик на сервере. При запуске установщика из командной оболочки администратора доступны следующие параметры:

   * `OPT_NO_ANCM=1`. Пропуск установки модуля ASP.NET Core.
   * `OPT_NO_RUNTIME=1`. Пропуск установки среды выполнения .NET Core. Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`. Пропуск установки общей платформы ASP.NET (среды выполнения ASP.NET). Используется, когда на сервере размещаются только [автономные развертывания](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`. Пропуск установки сред выполнения x86. Этот параметр следует использовать, если вы наверняка не будете размещать 32-разрядные приложения. Если есть хоть малейшая возможность, что в будущем придется размещать и 32-разрядные, и 64-разрядные приложения, не указывайте этот параметр и установите обе среды выполнения.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`. Отключение проверки использования общей конфигурации IIS, если файл общей конфигурации (`applicationHost.config`) находится на том же компьютере, что и установка IIS. *Доступен только для пакетных установщиков размещения ASP.NET Core 2.2 или более поздней версии.* Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Перезапустите систему или выполните в командной оболочке следующие команды:

   ```console
   net stop was /y
   net start w3svc
   ```
   Перезапуск служб IIS позволит обнаружить внесенные установщиком изменения в системном пути, который является переменной среды.

ASP.NET Core не наследует поведение наката для выпусков исправлений пакетов общей платформы. После обновления общей платформы путем установки нового пакета размещения перезапустите систему или выполните в командной оболочке следующие команды:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Сведения об общей конфигурации IIS см. в разделе [Модуль ASP.NET Core с общей конфигурацией IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Версия модуля и журналы установщика хостинга Bundle

Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.

1. В системе размещения перейдите к папке `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.
1. Найдите файл `aspnetcorev2.dll`.
1. Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.
1. Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.

Журналы установщика пакета размещения для модуля находятся в папке `C:\Users\%UserName%\AppData\Local\Temp`. Файл имеет имя `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, где заполнитель `{TIMESTAMP}` — это метка времени файла.
