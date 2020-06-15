---
title: Начало работы с Blazor ASP.NET Core
author: guardrex
description: Приступите к работе с Blazor, создав приложение Blazor с помощью предпочтительных средств.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452035"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Начало работы с Blazor ASP.NET Core

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

Чтобы приступить к работе с Blazor, следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Установите последнюю версию [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) с рабочей нагрузкой **ASP.NET и разработка веб-приложений**.

1. Создайте новый проект.

1. Выберите **Приложение Blazor** . Выберите **Далее**.

1. В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию. Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Выберите **Создать**.

1. Для работы с Blazor WebAssembly выберите шаблон **Приложение Blazor WebAssembly**. Для работы с Blazor Server выберите шаблон **Серверное приложение Blazor** . Выберите **Создать**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Установите последнюю версию [пакета SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:

   ```dotnetcli
   dotnet --version
   ```

1. Установите последнюю версию [Visual Studio Code](https://code.visualstudio.com/).

1. Установите актуальную версию [расширения C# для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) и расширения [отладчика JavaScript (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), задав для `debug.javascript.usePreview` значение `true`.

  Чтобы задать `debug.javascript.usePreview` для `true` с помощью пользовательского интерфейса VS Code, откройте **Файл** > **Настройки** > **Параметры** и выполните поиск `debug javascript use preview`. Установите флажок **Использовать новый отладчик JavaScript в предварительной версии для Node.js и Chrome**.

1. Для работы с Blazor WebAssembly выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Для работы с Blazor Server выполните следующую команду в командной оболочке:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.

1. Откройте папку *WebApplication1* в Visual Studio Code.

1. Среда IDE запрашивает добавление ресурсов для сборки и отладки проекта. Выберите ответ **Да**.

1. Нажмите клавишу <kbd>Ctrl</kbd>+<kbd>F5</kbd> для запуска приложения.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. Установите [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Выберите **Файл** > **Создать решение** или создайте **новый проект** из **окна запуска**.

1. На боковой панели выберите **Интернет и консоль** > **Приложение**.

   Для работы с Blazor WebAssembly выберите шаблон **Приложение Blazor WebAssembly**. Для работы с Blazor Server выберите шаблон **Серверное приложение Blazor** . Выберите **Далее**.

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.

1. Подтвердите следующие конфигурации.

   * Для параметра **Целевая платформа** задано значение **.NET Core 3.1**.
   * Для параметра **Проверка подлинности** задано значение **Без проверки подлинности**.
   
   Выберите **Далее**.

1. В поле **Имя проекта** присвойте имя приложению `WebApplication1`. Выберите **Создать**.

1. Выберите **Выполнить** > **Запуск без отладки**, чтобы запустить приложение *без отладчика*. Запустите приложение с помощью **Выполнить** > **Начать отладку** или с помощью кнопки "Выполнить" (&#9654;), чтобы запустить приложение *с отладчиком*.

При появлении подтверждения доверия к сертификату разработки подтвердите доверие, чтобы продолжить. Для доверия к сертификату необходимы пароли пользователя и цепочки ключей.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

1. Установите последнюю версию [пакета SDK для .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Если вы установили пакет SDK ранее, можно определить установленную версию, выполнив следующую команду в командной оболочке:

   ```dotnetcli
   dotnet --version
   ```

1. Для работы с Blazor WebAssembly выполните следующие команды в командной оболочке:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Для работы с Blazor Server выполните следующие команды в командной оболочке:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Сведения о двух моделях размещения Blazor, *Blazor WebAssembly* и *Blazor Server*, см. в разделе <xref:blazor/hosting-models>.

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

* Добавьте открытое свойство для `IncrementAmount` с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).
* Изменение метод `IncrementCount`, чтобы он использовал `IncrementAmount` при увеличении значения `currentCount`.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Укажите `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Запустите приложение. Теперь у компонента `Index` будет собственный счетчик, который будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь). Компонент `Counter` (*Counter.razor*) в компоненте `/counter` продолжает увеличиваться на единицу.

## <a name="next-steps"></a>Следующие шаги

Пошаговое создание приложения Blazor

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:blazor/templates>
* <xref:signalr/introduction>
