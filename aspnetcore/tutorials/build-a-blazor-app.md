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
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="0b736-103">Создание приложения Blazor со списком дел</span><span class="sxs-lookup"><span data-stu-id="0b736-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="0b736-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0b736-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0b736-105">В этом учебнике показано, как создать и изменить приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="0b736-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="0b736-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="0b736-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b736-107">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="0b736-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="0b736-108">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="0b736-108">Modify Razor components</span></span>
> * <span data-ttu-id="0b736-109">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="0b736-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="0b736-110">Использование маршрутизации в приложении Blazor</span><span class="sxs-lookup"><span data-stu-id="0b736-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="0b736-111">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.</span><span class="sxs-lookup"><span data-stu-id="0b736-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0b736-112">Предварительные требования</span><span class="sxs-lookup"><span data-stu-id="0b736-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="0b736-113">Создание приложения Blazor со списком задач</span><span class="sxs-lookup"><span data-stu-id="0b736-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="0b736-114">Создайте в командной строке новое приложение Blazor с именем `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="0b736-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="0b736-115">Предыдущая команда создает папку с именем `TodoList` для хранения приложения.</span><span class="sxs-lookup"><span data-stu-id="0b736-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="0b736-116">Папка `TodoList` находится в *корневой папке* проекта.</span><span class="sxs-lookup"><span data-stu-id="0b736-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="0b736-117">Перейдите в папку `TodoList` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="0b736-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="0b736-118">Добавьте в приложение новый компонент Razor `Todo` в папке `Pages` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="0b736-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="0b736-119">Имена файлов компонентов Razor должны начинаться с прописной буквы.</span><span class="sxs-lookup"><span data-stu-id="0b736-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="0b736-120">Откройте папку `Pages` и убедитесь, что имя файла компонента `Todo` начинается с заглавной буквы `T`.</span><span class="sxs-lookup"><span data-stu-id="0b736-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="0b736-121">Имя файла должно быть `Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0b736-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="0b736-122">В `Pages/Todo.razor` задайте начальную разметку компонента:</span><span class="sxs-lookup"><span data-stu-id="0b736-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="0b736-123">Сохраните файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0b736-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="0b736-124">Добавьте компонент `Todo` на панель навигации.</span><span class="sxs-lookup"><span data-stu-id="0b736-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="0b736-125">Компонент `NavMenu` (`Shared/NavMenu.razor`) используется в макете этого приложения.</span><span class="sxs-lookup"><span data-stu-id="0b736-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="0b736-126">Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.</span><span class="sxs-lookup"><span data-stu-id="0b736-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="0b736-127">Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файле `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="0b736-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="0b736-128">Сохраните файл `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="0b736-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="0b736-129">Скомпилируйте и запустите приложение, выполнив команду [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) в командной оболочке из папки `TodoList`.</span><span class="sxs-lookup"><span data-stu-id="0b736-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="0b736-130">Перейдите на новую страницу списка задач по адресу `https://localhost:5001/todo` и убедитесь, что на боковой панели навигации ссылка на компонент `Todo` работает правильно.</span><span class="sxs-lookup"><span data-stu-id="0b736-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="0b736-131">Добавьте в корень проекта (папка `TodoList`) файл `TodoItem.cs`, который будет содержать класс для элемента списка дел.</span><span class="sxs-lookup"><span data-stu-id="0b736-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="0b736-132">Используйте следующий код C# для класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="0b736-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="0b736-133">Снова вернитесь к компоненту `Todo` (`Pages/Todo.razor`).</span><span class="sxs-lookup"><span data-stu-id="0b736-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="0b736-134">Добавьте поле для элементов списка дел в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="0b736-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="0b736-135">Компонент `Todo` использует это поле для сохранения состояния списка дел.</span><span class="sxs-lookup"><span data-stu-id="0b736-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="0b736-136">Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="0b736-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="0b736-137">Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.</span><span class="sxs-lookup"><span data-stu-id="0b736-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="0b736-138">Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="0b736-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="0b736-139">Сохраните файл `TodoItem.cs` и обновленный файл `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0b736-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="0b736-140">При сохранении файлов в командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="0b736-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="0b736-141">Браузер временно утратит подключение к приложению, но когда подключение будет восстановлено, браузер перезагрузит страницу.</span><span class="sxs-lookup"><span data-stu-id="0b736-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="0b736-142">При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не подключен обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="0b736-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="0b736-143">Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="0b736-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="0b736-144">Теперь при нажатии кнопки вызывается метод C# `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="0b736-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="0b736-145">Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.</span><span class="sxs-lookup"><span data-stu-id="0b736-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="0b736-146">Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список.</span><span class="sxs-lookup"><span data-stu-id="0b736-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="0b736-147">Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.</span><span class="sxs-lookup"><span data-stu-id="0b736-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="0b736-148">Сохраните файл `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0b736-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="0b736-149">В командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="0b736-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="0b736-150">После повторного подключения браузера к приложению страница в браузере перезагрузится.</span><span class="sxs-lookup"><span data-stu-id="0b736-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="0b736-151">Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач.</span><span class="sxs-lookup"><span data-stu-id="0b736-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="0b736-152">Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="0b736-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="0b736-153">Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.</span><span class="sxs-lookup"><span data-stu-id="0b736-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="0b736-154">Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).</span><span class="sxs-lookup"><span data-stu-id="0b736-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="0b736-155">Готовый компонент `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="0b736-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="0b736-156">Сохраните файл `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="0b736-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="0b736-157">В командной оболочке будет автоматически выполнена повторная сборка приложения.</span><span class="sxs-lookup"><span data-stu-id="0b736-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="0b736-158">После повторного подключения браузера к приложению страница в браузере перезагрузится.</span><span class="sxs-lookup"><span data-stu-id="0b736-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="0b736-159">Добавьте элементы в список дел, чтобы протестировать новый код.</span><span class="sxs-lookup"><span data-stu-id="0b736-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="0b736-160">По окончании завершите работу приложения в командной оболочке.</span><span class="sxs-lookup"><span data-stu-id="0b736-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="0b736-161">Многие командные оболочки принимают команду клавиатуры <kbd>CTRL</kbd>+<kbd>C</kbd> для остановки приложения.</span><span class="sxs-lookup"><span data-stu-id="0b736-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0b736-162">Дальнейшие действия</span><span class="sxs-lookup"><span data-stu-id="0b736-162">Next steps</span></span>

<span data-ttu-id="0b736-163">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="0b736-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0b736-164">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="0b736-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="0b736-165">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="0b736-165">Modify Razor components</span></span>
> * <span data-ttu-id="0b736-166">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="0b736-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="0b736-167">Использование маршрутизации в приложении Blazor</span><span class="sxs-lookup"><span data-stu-id="0b736-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="0b736-168">Подробные сведения об инструментах для ASP.NET Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="0b736-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
