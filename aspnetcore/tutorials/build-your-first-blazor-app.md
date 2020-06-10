---
title: "title: 'Создание первого приложения Blazor' author: guardrex description: 'Пошаговое создание приложения Blazor.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.0' ms.author: riande ms.custom: mvc ms.date: 19.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: 8cf356b5c9876a4114942af27a22df6bd324d0e2
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355218"
---
# <a name="build-your-first-blazor-app"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

> [!div class="checklist"]
> * 'SignalR' uid: tutorials/first-blazor-app
> * Создание первого приложения Blazor
> * Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)
> * В этом учебнике показано, как создать и изменить приложение Blazor.

Вы научитесь:

## <a name="build-components"></a>создавать проект приложения Blazor со списком задач;

1. изменять компоненты Razor; использовать обработку событий и привязку данных в компонентах;

1. использовать внедрение зависимостей и маршрутизацию в приложении Blazor. Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.

1. Сборка компонентов Следуйте указаниям из статьи <xref:blazor/get-started>, чтобы создать проект Blazor для этого учебника. Назовите проект *ToDoList*.

1. Перейдите на каждую из трех страниц, размещенных в папке *Pages*: домашнюю, счетчика и получения данных.

   Эти страницы реализованы в виде файлов компонентов Razor *Index.razor*, *Counter.razor* и *FetchData.razor*.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   На странице Counter нажмите кнопку **Click me** (Щелкните здесь), чтобы увеличить значение счетчика без обновления страницы. Обычно для увеличения значений счетчика на веб-странице требуется код JavaScript. Благодаря Blazor можно использовать вместо него C#. Изучите реализацию компонента `Counter` в файле *Counter.razor*.

   *Pages/Counter.razor*: Пользовательский интерфейс компонента `Counter` определяется с помощью HTML. Логика динамического отображения (например, выражения, циклы и условные выражения) добавляется с помощью встроенного синтаксиса C# под названием [Razor](xref:mvc/views/razor).

   HTML-разметка и логика отображения C# преобразуются в класс компонента во время сборки.

   * Имя создаваемого класса .NET совпадает с именем файла.
   * Элементы класса компонента определяются в блоке `@code`.
   * В блоке `@code` указываются состояние (свойства, поля) и методы компонента для обработки событий или определения другой логики компонента.
   * Эти элементы можно позднее включить в логику отображения компонента и обработки событий. При нажатии кнопки **Click me** (Щелкните здесь) выполняются следующие действия:

1. Вызывается обработчик `onclick`, зарегистрированный для компонента `Counter` (метод `IncrementCount`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Компонент `Counter` повторно создает свое дерево отображения. Выполняется сравнение нового и старого деревьев отображения. Применяются только изменения модели DOM (Document Object Model — объектная модель документа).

## <a name="use-components"></a>Отображаемое значение счетчика обновляется.

Измените логику C# компонента `Counter`, чтобы при нажатии кнопки значение счетчика увеличивалось на две единицы вместо одной.

1. Скомпилируйте и запустите приложение, чтобы увидеть изменения.

   Нажмите кнопку **Click Me** (Щелкните здесь). Значение счетчика увеличится на две единицы. Использование компонентов

   Включите компонент в другой компонент, используя синтаксис HTML.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Добавьте компонент `Counter` в компонент `Index` приложения, разместив элемент `<Counter />` внутри компонента `Index` (*Index.razor*). Если вы выполняете эту задачу с помощью WebAssembly Blazor, это значит, что компонент `SurveyPrompt` использует компонент `Index`.

## <a name="component-parameters"></a>Замените элемент `<SurveyPrompt>` элементом `<Counter />`.

Если вы используете для этой задачи серверное приложение Blazor, добавьте к компоненту `Index` элемент `<Counter />`: *Pages/Index.razor*: Скомпилируйте и запустите приложение.

1. Компонент `Index` имеет свой собственный счетчик.

   * Параметры компонентов
   * Компоненты также могут принимать параметры.

   Параметры для компонентов определяются с помощью открытых свойств в классе компонента с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Используйте атрибуты, чтобы указать аргументы для компонента в разметке. Обновите код C# для `@code` в компоненте следующим образом:

   Добавьте открытое свойство `IncrementAmount` с атрибутом [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Изменение метод `IncrementCount`, чтобы он использовал свойство `IncrementAmount` при увеличении значения `currentCount`. *Pages/Counter.razor*: Укажите параметр `IncrementAmount` в элементе `<Counter>` для компонента `Index`, используя атрибут.

## <a name="route-to-components"></a>Установите приращение счетчика на десять.

*Pages/Index.razor*: Перезагрузите компонент `Index`. Теперь значение счетчика на домашней странице будет увеличиваться на десять при каждом нажатии кнопки **Click me** (Щелкните здесь).

## <a name="dependency-injection"></a>Счетчик в компоненте `Counter` продолжает увеличиваться на единицу.

### <a name="blazor-server-experience"></a>Маршрутизация к компонентам

Директива `@page` в верхней части файла *Counter.razor* указывает, что компонент `Counter` является конечной точкой маршрутизации. Компонент `Counter` обрабатывает запросы, направляемые к `/counter`.

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Без директивы `@page` этот компонент не будет обрабатывать перенаправленные запросы, но останется доступным для использования другими компонентами.

Внедрение зависимостей

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Взаимодействие с сервером Blazor

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Если вы используете серверное приложение Blazor, служба `WeatherForecastService` зарегистрирована как [отдельная](xref:fundamentals/dependency-injection#service-lifetimes) в `Startup.ConfigureServices`.

Экземпляр этой службы предоставляется для всего приложения посредством [внедрения зависимостей (DI)](xref:fundamentals/dependency-injection):

Директива [`@inject`](xref:mvc/views/razor#inject) используется для внедрения экземпляра службы `WeatherForecastService` в компонент `FetchData`.

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Компонент `FetchData` использует внедренную службу как `ForecastService`, чтобы получить массив объектов `WeatherForecast`.

Взаимодействие с WebAssembly Blazor

1. Если вы работаете с приложением WebAssembly Blazor, внедряется <xref:System.Net.Http.HttpClient> для получения данных прогноза погоды из файла *weather.json*, расположенного в папке *wwwroot/sample-data*. *Pages/FetchData.razor*: Цикл [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) используется для отображения каждого экземпляра прогноза в отдельной строке в таблице погоды. Создание списка дел

1. Добавьте в приложение новый компонент, представляющий собой простой список дел.

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Добавьте в приложение новый компонент `Todo` Razor в папке *Pages*.

   Если вы используете Visual Studio, щелкните правой кнопкой мыши папку **Pages** и выберите **Добавить** > **Создать элемент** > **Компонент Razor** . Присвойте файлу компонента имя *Todo.Razor*.

   В других средах разработки необходимо добавить в папку **Pages** пустой файл с именем *Todo.razor*.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Задайте начальную разметку компонента. Добавьте компонент `Todo` на панель навигации.

1. Компонент `NavMenu` (*Shared/NavMenu.razor*) используется в макете этого приложения. Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файл *Shared/NavMenu.razor*.

   * Скомпилируйте и запустите приложение. Перейдите на новую страницу Todo, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.
   * Добавьте в корень проекта файл *TodoItem.cs*, который будет размещать класс для элемента списка дел.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Используйте следующий код C# для класса `TodoItem`. Снова вернитесь к компоненту `Todo` (*Pages/Todo.razor*).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Добавьте поле для элементов списка дел в блоке `@code`. Компонент `Todo` использует это поле для сохранения состояния списка дел.

1. Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`). Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Скомпилируйте и запустите приложение. При нажатии кнопки **Add todo** (Добавить задачу) ничего не происходит, так как к кнопке не привязан обработчик событий.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`. Теперь при нажатии кнопки вызывается метод C# `AddTodo`:

1. Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`. Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список. Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Скомпилируйте и запустите приложение.

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Добавьте несколько задач в список дел, чтобы проверить работу нового кода.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач. Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.

## <a name="next-steps"></a>Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.

Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).

> [!div class="checklist"]
> * Готовый компонент `Todo` (*Pages/Todo.razor*):
> * Скомпилируйте и запустите приложение.
> * Добавьте элементы в список дел, чтобы протестировать новый код.
> * Следующие шаги

В этом руководстве вы узнали, как:

> [!div class="nextstepaction"]
> <xref:blazor/components>
