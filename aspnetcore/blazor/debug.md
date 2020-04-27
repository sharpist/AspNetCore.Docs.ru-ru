---
title: Отладка ASP.NET Core Blazor WebAssembly
author: guardrex
description: Узнайте, как выполнять отладку приложений Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 7273ae3d240de0b59a58069fdcc1880247379751
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661604"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a>Отладка ASP.NET Core Blazor WebAssembly

[Дэниэл Рот (Daniel Roth)](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Приложения Blazor WebAssembly можно отлаживать с помощью средств разработки браузера в браузерах на основе Chromium (Edge/Chrome).  Кроме того, можно выполнить отладку приложения с помощью Visual Studio или Visual Studio Code.

Поддерживаются следующие сценарии:

* Установка и удаление точек останова.
* Запустите приложение с поддержкой отладки в Visual Studio и Visual Studio Code (поддержка <kbd>F5</kbd>).
* Выполните код пошагово (<kbd>F10</kbd>).
* Возобновите выполнение кода с помощью клавиши <kbd>F8</kbd> в браузере или клавиши <kbd>F5</kbd> в Visual Studio или Visual Studio Code.
* На экране *Локальные переменные* просмотрите значения локальных переменных.
* См. стек вызовов, включая цепочки вызовов, которые поступают из JavaScript в .NET и из .NET в JavaScript.

Сейчас вам *недоступны* следующие функции:

* Проверка массивов.
* Проверка элементов с помощью наведения указателя мыши.
* Пошаговая отладка с заходом в управляемый код или выходом из него.
* Полная поддержка проверки типов значений.
* Останов при необработанных исключениях.
* Попадание в точки останова во время запуска приложения.
* Отладка приложения с помощью рабочей роли службы.

Мы будем улучшать процесс отладки в будущих выпусках.

## <a name="prerequisites"></a>Предварительные требования

Для отладки требуется один из следующих браузеров:

* Microsoft Edge (версии 80 и более поздних)
* Google Chrome (версии 70 и более поздних)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Включение отладки для Visual Studio и Visual Studio Code

Отладка включается автоматически для новых проектов, созданных с помощью шаблона проекта Blazor WebAssembly ASP.NET Core 3.2 (предварительная версия 3) или более поздней версии ([текущая версия 3.2 Preview 4](xref:blazor/get-started)).

Чтобы включить отладку для существующего приложения Blazor WebAssembly, измените файл *launchSettings.json* в запускаемом проекте, включив в него следующее свойство `inspectUri` в каждом профиле запуска:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

После изменения файл *launchSettings.json* должен выглядеть следующим образом:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Свойство `inspectUri`:

* Позволяет интегрированной среде разработки обнаруживать, что приложение является приложением Blazor WebAssembly.
* Предписывает инфраструктуре отладки сценариев подключаться к браузеру через прокси-сервер отладки Blazor.

## <a name="visual-studio"></a>Visual Studio

Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio, сделайте следующее:

1. Убедитесь, что у вас [установлен последний предварительный выпуск Visual Studio 2019 16.6](https://visualstudio.com/preview) (предварительная версия 2 или более поздняя).
1. Создайте размещенное приложение Blazor WebAssembly ASP.NET Core.
1. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить приложение в отладчике.
1. Установите точку останова в *Counter.razor* в методе `IncrementCount`.
1. Перейдите на вкладку **Счетчик** и нажмите кнопку для попадания в точку останова:

   ![Счетчик отладки](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Просмотрите значение поля `currentCount` в окне локальных переменных:

   ![Просмотр локальных переменных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение.

При отладке приложения Blazor WebAssembly можно также отлаживать серверный код:

1. Установите точку останова на странице *FetchData.razor* в `OnInitializedAsync`.
1. Установите точку останова в `WeatherForecastController` в методе действия `Get`.
1. Перейдите на вкладку **Получение данных**, чтобы попасть в первую точку останова в компоненте `FetchData` непосредственно перед отправкой HTTP-запроса на сервер:

   ![Отладка получения данных](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Нажмите клавишу <kbd>F5</kbd>, чтобы продолжить выполнение, и попадите точку останова на сервере в `WeatherForecastController`:

   ![Отладка сервера](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Нажмите клавишу <kbd>F5</kbd> еще раз, чтобы продолжить выполнение и увидеть отрисовку таблицы с прогнозом погоды.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

Чтобы выполнить отладку приложения Blazor WebAssembly в Visual Studio Code, сделайте следующее:
 
1. Установите [расширение C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.

   ![Расширения](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![Отладчик предварительной версии JS](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. Откройте существующее приложение Blazor WebAssembly с включенной отладкой.

   * Если вы получаете следующее уведомление о том, что для включения отладки требуется дополнительная настройка, убедитесь, что установлены подходящие расширения и Отладка предварительной версии JavaScript включена, а затем перезагрузите окно:

     ![Требуется дополнительная настройка](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * Уведомление предлагает добавить необходимые ресурсы в приложение для сборки и отладки. Выберите **Да**:

     ![Добавление необходимых ресурсов](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Запуск приложения в отладчике состоит из двух этапов:

   1\. **Сначала** запустите приложение, используя конфигурацию запуска **.NET Core Launch (Blazor Standalone)** (Запуск .NET Core — автономный Blazor).

   2\. **После запуска приложения** запустите браузер, используя конфигурацию запуска **.NET Core Debug Blazor Web Assembly in Chrome** (Blazor Web Assembly отладки .NET Core в Chrome) (требуется Chrome). Чтобы использовать Edge вместо Chrome, измените `type` конфигурации запуска в *.vscode/launch.json* с `pwa-chrome` на `pwa-msedge`.

1. Установите точку останова в методе `IncrementCount` в компоненте `Counter`, а затем нажмите кнопку, чтобы попасть в точку останова:

   ![Счетчик отладки в VS Code](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a>Отладка в браузере

1. Запустите отладочную сборку приложения в среде разработки.

1. Нажмите клавиши <kbd>SHIFT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd>.

1. Браузер должен быть запущен с включенной удаленной отладкой. Если удаленная отладка отключена, открывается страница ошибки **Не удается найти вкладку браузера для отладки**. На странице ошибки содержатся инструкции для запуска браузера с открытым портом отладки, чтобы прокси-сервер отладки Blazor мог подключиться к приложению. *Закройте все экземпляры браузера* и перезапустите браузер.

Когда браузер будет запущен с включенной удаленной отладкой, откройте новую вкладку отладчика с помощью сочетания клавиш. Через некоторое время на вкладке **Источники** отобразится список сборок .NET в приложении. Разверните каждую сборку и найдите исходные файлы *CS*/*RAZOR*, доступные для отладки. Установите точки останова, переключитесь обратно на вкладку приложения, и точки останова будут достигнуты при выполнении кода. После попадания в точку останова выполняйте код пошагово (<kbd>F10</kbd>) или возобновите его обычное выполнение (<kbd>F8</kbd>).

Blazor предоставляет прокси-сервер отладки, который реализует протокол [Chrome DevTools](https://chromedevtools.github.io/devtools-protocol/) и дополняет его сведениями, относящимися к .NET. При нажатии сочетания клавиш для отладки Blazor указывает Chrome DevTools на прокси-сервер. Прокси-сервер подключается к окну браузера, для которого выполняется отладка (поэтому и надо было включить удаленную отладку).

## <a name="browser-source-maps"></a>Сопоставители с исходным кодом в браузере

Сопоставители с исходным кодом в браузере позволяют браузеру сопоставлять скомпилированные файлы с исходными файлами и обычно используются для отладки на стороне клиента. Однако Blazor в настоящее время не сопоставляет C# напрямую с JavaScript/WASM. Вместо этого Blazor выполняет интерпретацию IL в браузере, поэтому сопоставление с исходным кодом не актуально.

## <a name="troubleshoot"></a>Устранение неполадок

При возникновении ошибок вам поможет следующий совет:

На вкладке **Отладчик** откройте средства разработчика в браузере. В консоли выполните `localStorage.clear()`, чтобы удалить все точки останова.
