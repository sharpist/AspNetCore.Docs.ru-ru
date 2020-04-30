---
title: Начало работы с Blazor ASP.NET Core
author: guardrex
description: Приступите к работе с Blazor, создав приложение Blazor с помощью предпочтительных средств.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111075"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Начало работы с MVC ASP.NET Core Blazor

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Чтобы приступить к работе с Blazor, следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Чтобы создать приложения Blazor Server, установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.

   Чтобы создавать приложения Blazor Server и Blazor WebAssembly, установите последнюю предварительную версию [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.

1. Установите шаблон предварительной версии [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), выполнив следующую команду:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Создайте новый проект.

1. Выберите **Приложение Blazor**. Выберите **Далее**.

1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Выберите **Создать**.

1. Для работы с Blazor WebAssembly (предварительная версия 2 Visual Studio 16.6 или более поздней версии) выберите шаблон **Приложение WebAssembly Blazor**. Для работы с Blazor Server (Visual Studio 16.4 или более поздней версии) выберите шаблон **Серверное приложение Blazor**. Выберите **Создать**.

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Установите [пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. При необходимости установите шаблон предварительной версии [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), выполнив следующую команду:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > [Пакет SDK для .NET Core версии 3.1.201 или более поздней](https://dotnet.microsoft.com/download/dotnet-core/3.1) **обязательно** должен использовать шаблон Blazor WebAssembly 3.2, предварительная версия 4. Подтвердите установленную версию пакет SDK для .NET Core, запустив `dotnet --version` в командной оболочке.

1. Установите [Visual Studio Code](https://code.visualstudio.com/).

1. Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.

1. Для работы с Blazor Server выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Сведения о двух моделях размещения Blazor, *Blazor Server* и *Blazor WebAssembly*, см. в разделе <xref:blazor/hosting-models>.

1. Откройте папку *WebApplication1* в Visual Studio Code.

1. Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта. Выберите ответ **Да**.

1. При использовании Blazor Server запустите приложение с помощью отладчика Visual Studio Code.

   С помощью Blazor WebAssembly запустите конфигурацию **.NET Core Launch (Blazor Standalone**), а затем запустите браузер в конфигурации запуска **.NET Core Debug Blazor Web Assembly в Chrome** (эта конфигурация требует наличия Chrome). Для получения дополнительной информации см. <xref:blazor/debug#visual-studio-code>.

1. В браузере перейдите на адрес `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Blazor Server поддерживается в Visual Studio для Mac. Blazor WebAssembly сейчас не поддерживается. Чтобы создавать приложения Blazor WebAssembly в macOS, следуйте указаниям на вкладке **.NET Core CLI**.

1. Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Выберите **Файл** > **Создать решение** или создайте **новый проект**.

1. На боковой панели выберите **.NET Core** > **Приложение**.

1. Выберите шаблон **Серверное приложение Blazor**. Выберите **Создать**.

   Сведения о модели размещения Blazor Server см. в разделе <xref:blazor/hosting-models>.

1. Задайте для параметра **Целевая платформа** значение **.NET Core 3.1** и нажмите кнопку **Далее**.

1. В поле **Имя проекта** присвойте имя приложению `WebApplication1`. Выберите **Создать**.

1. Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*. Запустите приложение с помощью кнопки **Начать отладку**, чтобы запустить приложение *с отладчиком*.

При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

1. Установите [пакет SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. При необходимости установите шаблон предварительной версии [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), выполнив следующую команду:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > [Пакет SDK для .NET Core версии 3.1.201 или более поздней](https://dotnet.microsoft.com/download/dotnet-core/3.1) **обязательно** должен использовать шаблон Blazor WebAssembly 3.2, предварительная версия 4. Подтвердите установленную версию пакет SDK для .NET Core, запустив `dotnet --version` в командной оболочке.

1. Для работы с Blazor Server выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Сведения о двух моделях размещения Blazor, *Blazor Server* и *Blazor WebAssembly*, см. в разделе <xref:blazor/hosting-models>.

1. В браузере перейдите на адрес `https://localhost:5001`.

---

На вкладках на боковой панели доступно несколько страниц:

* Главная страница
* Счетчик
* Выборка данных

На странице Counter нажмите кнопку **Click me** (Щелкните здесь), чтобы увеличить значение счетчика без обновления страницы. Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript, однако с Blazor можно использовать C#.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Запрос `/counter` в браузере, как указано в директиве `@page` в верхней части, заставляет компонент `Counter` визуализировать свое содержимое. Компоненты преобразуются в представление дерева визуализации. Его затем можно использовать для гибкого и эффективного обновления пользовательского интерфейса.

Каждый раз при нажатии кнопки **Click me** (Щелкните здесь) выполняются следующие действия:

* Запускается событие `onclick`.
* вызывается метод `IncrementCount` .
* Увеличивается значение `currentCount`.
* Компонент визуализируется снова.

Среда выполнения сравнивает новое содержимое с предыдущим содержимым и применяет к модели DOM только измененное содержимое.

Добавьте компонент в другой компонент, используя синтаксис HTML. Например, добавьте компонент `Counter` на домашнюю страницу приложения, разместив элемент `<Counter />` внутри компонента `Index`.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Запустите приложение. На домашней странице имеется собственный счетчик, предоставленный компонентом `Counter`.

Параметры компонента указываются с помощью атрибутов или [дочернего содержимого](xref:blazor/components#child-content), которые позволяют задавать свойства дочернего компонента. Чтобы добавить параметр в компонент `Counter`, обновите блок `@code`компонента:

* Добавьте открытое свойство для `IncrementAmount` с атрибутом `[Parameter]`.
* Изменение метод `IncrementCount`, чтобы он использовал `IncrementAmount` при увеличении значения `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Укажите `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Запустите приложение. Теперь у компонента `Index` будет собственный счетчик, который будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь). Компонент `Counter` (*Counter.razor*) в компоненте `/counter` продолжает увеличиваться на единицу.

## <a name="next-steps"></a>Следующие шаги

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/templates>
* <xref:signalr/introduction>
