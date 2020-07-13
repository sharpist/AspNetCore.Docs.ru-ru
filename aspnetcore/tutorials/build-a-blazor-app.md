---
title: Создание приложения Blazor со списком дел
author: guardrex
description: Узнайте, как создать приложение Blazor по шагам.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 726380c42c952f47d6fdff09a811f35a20462d96
ms.sourcegitcommit: 66fca14611eba141d455fe0bd2c37803062e439c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2020
ms.locfileid: "85944925"
---
# <a name="build-a-blazor-todo-list-app"></a>Создание приложения Blazor со списком дел

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

В этом учебнике показано, как создать и изменить приложение Blazor. Вы научитесь:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * использовать внедрение зависимостей и маршрутизацию в приложении Blazor.

Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.

1. Создайте в командной строке новое приложение Blazor с именем `TodoList`:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Предыдущая команда создает папку с именем `TodoList` для хранения приложения. Перейдите в папку `TodoList` с помощью следующей команды:

   ```dotnetcli
   cd TodoList
   ```

1. Добавьте в приложение новый компонент Razor `Todo` в папке `Pages` с помощью следующей команды:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Имена файлов компонентов Razor должны начинаться с прописной буквы, поэтому убедитесь в том, что имя файла компонента `Todo` начинается с прописной буквы `T`.

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

1. Скомпилируйте и запустите приложение. Перейдите на новую страницу Todo, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.

1. Добавьте в корень проекта файл `TodoItem.cs`, который будет содержать класс для элемента списка дел. Используйте следующий код C# для класса `TodoItem`.

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Снова вернитесь к компоненту `Todo` (`Pages/Todo.razor`).

   * Добавьте поле для элементов списка дел в блоке `@code`. Компонент `Todo` использует это поле для сохранения состояния списка дел.
   * Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел. Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Скомпилируйте и запустите приложение. При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не привязан обработчик событий.

1. Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`. Теперь при нажатии кнопки вызывается метод C# `AddTodo`:

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список. Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Скомпилируйте и запустите приложение. Добавьте несколько задач в список дел, чтобы проверить работу нового кода.

1. Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач. Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`. Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Готовый компонент `Todo` (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Скомпилируйте и запустите приложение. Добавьте элементы в список дел, чтобы протестировать новый код.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как:

> [!div class="checklist"]
> * создавать проект приложения Blazor со списком задач;
> * изменять компоненты Razor;
> * использовать обработку событий и привязку данных в компонентах;
> * использовать внедрение зависимостей и маршрутизацию в приложении Blazor.

Подробнее о шаблонах проектов Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/templates>
