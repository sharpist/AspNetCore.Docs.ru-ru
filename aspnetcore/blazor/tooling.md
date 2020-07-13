---
title: Инструменты для Blazor ASP.NET Core
author: guardrex
description: Сведения об инструментах, доступных для создания приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/tooling
zone_pivot_groups: operating-systems-set-one
ms.openlocfilehash: 30a76eda0e94ee7bb2b2d3db918bc029865bdf1a
ms.sourcegitcommit: f7873c02c1505c99106cbc708f37e18fc0a496d1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147651"
---
# <a name="tooling-for-aspnet-core-blazor"></a>Инструменты для Blazor ASP.NET Core

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

::: zone pivot="os-windows"

1. Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.

1. Создайте новый проект.

1. Выберите **Приложение Blazor** . Выберите **Далее**.

1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Выберите **Создать**.

1. Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** . Для работы с Blazor Server выберите шаблон **приложения Blazor Server** . Выберите **Создать**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server* см. в статье <xref:blazor/hosting-models>.

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end

::: zone pivot="os-linux"

1. Установите последнюю версию [пакета SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:

   ```dotnetcli
   dotnet --version
   ```

1. Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com/).

1. Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Для работы с Blazor Server выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server* см. в статье <xref:blazor/hosting-models>.

1. Откройте папку `WebApplication1` в Visual Studio Code.

1. Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта. Выберите ответ **Да**.

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

## <a name="trust-a-development-certificate"></a>Установка доверия к сертификату разработки

В Linux отсутствует централизованный способ установки доверия к сертификату. Обычно используется один из следующих подходов:

* исключение URL-адреса приложения в списке исключений браузера;
* установка доверия ко всем самозаверяющим сертификатам для `localhost`;
* добавление сертификата в список доверенных сертификатов в браузере.

Дополнительные сведения см. в рекомендациях, предоставляемых для вашего браузера и дистрибутива Linux.

::: zone-end

::: zone pivot="os-macos"

1. Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.

1. На боковой панели выберите **Интернет и консоль** > **Приложение**.

   Для работы с Blazor WebAssembly выберите шаблон **приложения Blazor WebAssembly** . Для работы с Blazor Server выберите шаблон **приложения Blazor Server** . Выберите **Далее**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server* см. в статье <xref:blazor/hosting-models>.

1. Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**. Выберите **Далее**.

1. В поле **Имя проекта** присвойте имя приложению `WebApplication1`. Выберите **Создать**.

1. Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*. Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.

При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить. Для доверия к сертификату необходимы пароли пользователя и цепочки ключей. Дополнительные сведения об установке доверия к сертификату разработки HTTPS ASP.NET Core см. здесь: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

::: zone-end
