---
title: Привязывание к браузеру в ASP.NET Core
author: ncarandini
description: Узнайте о том, как функция привязывания к браузеру Visual Studio связывает среду разработки с одним или несколькими веб-браузерами.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 737abc8b9abea45654a86a4dc20584948ece9fc5
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013181"
---
# <a name="browser-link-in-aspnet-core"></a>Привязывание к браузеру в ASP.NET Core

Авторы: [Николо Карандини (Nicolò Carandini)](https://github.com/ncarandini), [Майк Вассон (Mike Wasson)](https://github.com/MikeWasson) и [Том Дайкстра (Tom Dykstra)](https://github.com/tdykstra)

Привязывание к браузеру является компонентом Visual Studio. Оно создает канал связи между средой разработки и одним или несколькими веб-браузерами. Можно использовать привязывание к браузеру для обновления веб-приложения одновременно в нескольких браузерах, что полезно для тестирования в разных браузерах.

## <a name="browser-link-setup"></a>Настройка привязывания к браузеру

::: moniker range=">= aspnetcore-3.0"

Добавьте пакет [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) в проект. Для проектов ASP.NET Core Razor Pages или MVC также включите компиляцию в среде выполнения Razor (*CSHTML*), как описано в <xref:mvc/views/view-compilation>. Изменения синтаксиса Razor применяются только при включенной компиляции среды выполнения.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

При преобразовании проекта ASP.NET Core 2.0 в ASP.NET Core 2.1 и переходе к [метапакету Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) установите пакет [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) для функции привязывания к браузеру. Шаблоны проектов ASP.NET Core 2.1 по умолчанию используют метапакет `Microsoft.AspNetCore.App`.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Шаблоны ASP.NET Core 2.0 **Веб-приложение**, **Пустой** и **Веб-API** используют [метапакет Microsoft.AspNetCore.All](xref:fundamentals/metapackage), который содержит ссылку на пакет для [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Поэтому использование метапакета `Microsoft.AspNetCore.All` не требует дальнейших действий, чтобы сделать привязывание к браузеру доступным для использования.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Шаблон проекта ASP.NET Core 1.x **Веб-приложение** содержит ссылку на пакет для пакета [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Для других типов проектов требуется добавить ссылку на пакет `Microsoft.VisualStudio.Web.BrowserLink`.

::: moniker-end

### <a name="configuration"></a>Параметр Configuration

Вызовите `Startup.Configure` в методе `UseBrowserLink`:

```csharp
app.UseBrowserLink();
```

Вызов `UseBrowserLink` обычно размещается в блоке `if`, который разрешает привязывание к браузеру только в среде разработки. Пример:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Для получения дополнительной информации см. <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Использование привязывания к браузеру

При открытии проекта ASP.NET Core в Visual Studio элемент управления панели инструментов "Привязывание к браузеру" отображается рядом с элементом управления **Целевой объект отладки**:

![Раскрывающееся меню "Привязывание к браузеру"](using-browserlink/_static/browserLink-dropdown-menu.png)

С помощью элемента управления панели инструментов "Привязывание к браузеру" можно:

* Одновременно обновить веб-приложение в нескольких браузерах.
* Открыть **Панель мониторинга "Привязывание к браузеру"** .
* Включить или отключить **привязывание к браузеру**. Примечание. По умолчанию в Visual Studio привязывание к браузеру отключено.
* Включить или отключить [Автосинхронизацию в CSS](#enable-or-disable-css-auto-sync).

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Одновременное обновление веб-приложения в нескольких браузерах

Чтобы выбрать один веб-браузер для запуска при запуске проекта, используйте раскрывающееся меню в элементе управления панели инструментов **Целевой объект отладки**:

![Раскрывающееся меню F5](using-browserlink/_static/debug-target-dropdown-menu.png)

Чтобы открыть сразу несколько браузеров, выберите **Просмотреть с помощью...** из того же раскрывающегося списка. Удерживайте клавишу <kbd>CTRL</kbd>, чтобы выбрать нужные браузеры, а затем щелкните **Обзор**:

![Одновременное открытие нескольких браузеров](using-browserlink/_static/open-many-browsers-at-once.png)

На следующем снимке экрана показана Visual Studio с открытым представлением индекса и двумя открытыми браузерами:

![Пример синхронизации с двумя браузерами](using-browserlink/_static/sync-with-two-browsers-example.png)

Наведите указатель мыши на элемент управления панели инструментов "Привязывание к браузеру", чтобы просмотреть браузеры, подключенные к проекту:

![Подсказка при наведении](using-browserlink/_static/hoover-tip.png)

Измените представление индекса, и все подключенные браузеры будут обновлены при нажатии кнопки обновления для привязывания к браузеру:

![browsers-sync-to-changes](using-browserlink/_static/browsers-sync-to-changes.png)

Привязывание к браузеру также работает с браузерами, которые вы запускаете не в Visual Studio и используете для перехода по URL-адресу приложения.

### <a name="the-browser-link-dashboard"></a>Панель мониторинга привязывания к браузеру

Чтобы управлять соединением с открытыми браузерами, откройте **Панель мониторинга привязывания к браузеру** в раскрывающемся меню "Привязывание к браузеру":

![open-browserslink-dashboard](using-browserlink/_static/open-browserlink-dashboard.png)

Если ни один браузер не подключен, можно запустить сеанс без отладки, выбрав **Просмотреть в браузере**:

![browserlink-dashboard-no-connections](using-browserlink/_static/browserlink-dashboard-no-connections.png)

В противном случае отображаются подключенные браузеры с указанием пути к странице, отображаемой в каждом браузере:

![browserlink-dashboard-two-connections](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Можно также щелкнуть имя отдельного браузера, чтобы обновить только его.

### <a name="enable-or-disable-browser-link"></a>Включение или отключение привязывания к браузеру

При повторном включении привязывания к браузеру после его отключения необходимо обновить браузеры, чтобы повторно подключить их.

### <a name="enable-or-disable-css-auto-sync"></a>Включение или отключение автосинхронизации в CSS

Когда автоматическая синхронизация в CSS включена, подключенные браузеры автоматически обновляются при внесении любых изменений в файлы CSS.

## <a name="how-it-works"></a>Принцип работы

Для создания канала связи между Visual Studio и браузером привязывание к браузеру использует [SignalR](xref:signalr/introduction). Если привязывание к браузеру включено, Visual Studio выступает в качестве сервера SignalR, к которому могут подключаться несколько клиентов (браузеров). Привязывание к браузеру также регистрирует компонент ПО промежуточного слоя в конвейере запросов ASP.NET Core. Этот компонент внедряет специальные ссылки `<script>` в каждый запрос страницы с сервера. Чтобы просмотреть ссылки на скрипты, выберите **Просмотреть источник** в браузере и прокрутите до конца содержимого тега `<body>`:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Исходные файлы не изменяются. Компонент ПО промежуточного слоя динамически вставляет ссылки на скрипты.

Так как на стороне браузера используется только код JavaScript, он работает во всех браузерах, которые поддерживаются SignalR, без использования подключаемого модуля браузера.
