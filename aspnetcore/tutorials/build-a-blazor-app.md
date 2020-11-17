---
title: Создание приложения Blazor со списком дел
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 4d5bd977b52dd20ffb62519720106ef0a4560914
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508140"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Создание приложения Blazor со списком дел

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

В этом учебнике показано, как создать и изменить приложение Blazor. Вы научитесь:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * Использование маршрутизации в приложении Blazor

Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.

## <a name="prerequisites"></a>Предварительные требования

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>Создание приложения Blazor со списком задач

1. Создайте в командной строке новое приложение Blazor с именем `TodoList`:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Предыдущая команда создает папку с именем `TodoList` для хранения приложения. Папка `TodoList` находится в *корневой папке* проекта. Перейдите в папку `TodoList` с помощью следующей команды:

   ```dotnetcli
   cd TodoList
   ```

1. Добавьте в приложение новый компонент Razor `Todo` в папке `Pages` с помощью следующей команды:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Имена файлов компонентов Razor должны начинаться с прописной буквы. Откройте папку `Pages` и убедитесь, что имя файла компонента `Todo` начинается с заглавной буквы `T`. Имя файла должно быть `Todo.razor`.

1. В `Pages/Todo.razor` задайте начальную разметку компонента:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Добавьте компонент `Todo` на панель навигации.

   Компонент `NavMenu` (`Shared/NavMenu.razor`) используется в макете этого приложения. Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.

   Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файле `Shared/NavMenu.razor`:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Выполните сборку и запустите приложение, выполнив команду `dotnet run` в командной оболочке из папки `TodoList`. Перейдите на новую страницу Todo по адресу `https://localhost:5001/todo`, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.

1. Добавьте в корень проекта (папка `TodoList`) файл `TodoItem.cs`, который будет содержать класс для элемента списка дел. Используйте следующий код C# для класса `TodoItem`.

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Снова вернитесь к компоненту `Todo` (`Pages/Todo.razor`).

   * Добавьте поле для элементов списка дел в блоке `@code`. Компонент `Todo` использует это поле для сохранения состояния списка дел.
   * Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел. Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Остановите выполняющееся приложение в командной оболочке. Многие командные оболочки принимают команду клавиатуры <kbd>CTRL</kbd>+<kbd>C</kbd> для остановки приложения. Выполните повторную сборку и запустите приложение с помощью команды `dotnet run`. При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не привязан обработчик событий.

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`. Теперь при нажатии кнопки вызывается метод C# `AddTodo`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список. Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Остановите выполняющееся приложение в командной оболочке. Выполните повторную сборку и запустите приложение с помощью команды `dotnet run`. Добавьте несколько задач в список дел, чтобы проверить работу нового кода.

1. Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач. Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`. Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Готовый компонент `Todo` (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Остановите выполняющееся приложение в командной оболочке. Выполните повторную сборку и запустите приложение с помощью команды `dotnet run`. Добавьте элементы в список дел, чтобы протестировать новый код.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * Использование маршрутизации в приложении Blazor

Подробные сведения об инструментах для ASP.NET Core Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
