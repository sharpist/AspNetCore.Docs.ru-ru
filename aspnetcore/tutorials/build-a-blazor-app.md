---
title: Создание приложения Blazor со списком дел
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870442"
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

   Сохраните файл `Pages/Todo.razor`.

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

   Сохраните файл `Shared/NavMenu.razor`.

1. Скомпилируйте и запустите приложение, выполнив команду [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) в командной оболочке из папки `TodoList`. Перейдите на новую страницу списка задач по адресу `https://localhost:5001/todo` и убедитесь, что на боковой панели навигации ссылка на компонент `Todo` работает правильно.

1. Добавьте в корень проекта (папка `TodoList`) файл `TodoItem.cs`, который будет содержать класс для элемента списка дел. Используйте следующий код C# для класса `TodoItem`.

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Снова вернитесь к компоненту `Todo` (`Pages/Todo.razor`).

   * Добавьте поле для элементов списка дел в блоке `@code`. Компонент `Todo` использует это поле для сохранения состояния списка дел.
   * Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел. Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Сохраните файл `TodoItem.cs` и обновленный файл `Pages/Todo.razor`. При сохранении файлов в командной оболочке будет автоматически выполнена повторная сборка приложения. Браузер временно утратит подключение к приложению, но когда подключение будет восстановлено, браузер перезагрузит страницу.

1. При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не подключен обработчик событий.

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`. Теперь при нажатии кнопки вызывается метод C# `AddTodo`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список. Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Сохраните файл `Pages/ToDo.razor`. В командной оболочке будет автоматически выполнена повторная сборка приложения. После повторного подключения браузера к приложению страница в браузере перезагрузится.

1. Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач. Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`. Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Готовый компонент `Todo` (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Сохраните файл `Pages/ToDo.razor`. В командной оболочке будет автоматически выполнена повторная сборка приложения. После повторного подключения браузера к приложению страница в браузере перезагрузится.

1. Добавьте элементы в список дел, чтобы протестировать новый код.

1. По окончании завершите работу приложения в командной оболочке. Многие командные оболочки принимают команду клавиатуры <kbd>CTRL</kbd>+<kbd>C</kbd> для остановки приложения.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы узнали, как:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * Использование маршрутизации в приложении Blazor

Подробные сведения об инструментах для ASP.NET Core Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
