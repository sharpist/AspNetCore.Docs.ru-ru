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
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="794b0-103">Создание приложения Blazor со списком дел</span><span class="sxs-lookup"><span data-stu-id="794b0-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="794b0-104">Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="794b0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="794b0-105">В этом учебнике показано, как создать и изменить приложение Blazor.</span><span class="sxs-lookup"><span data-stu-id="794b0-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="794b0-106">Вы научитесь:</span><span class="sxs-lookup"><span data-stu-id="794b0-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="794b0-107">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="794b0-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="794b0-108">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="794b0-108">Modify Razor components</span></span>
> * <span data-ttu-id="794b0-109">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="794b0-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="794b0-110">использовать внедрение зависимостей и маршрутизацию в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="794b0-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="794b0-111">Когда вы выполните задачи из этого руководства, у вас будет работающее приложение списка задач.</span><span class="sxs-lookup"><span data-stu-id="794b0-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

1. <span data-ttu-id="794b0-112">Создайте в командной строке новое приложение Blazor с именем `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="794b0-112">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="794b0-113">Предыдущая команда создает папку с именем `TodoList` для хранения приложения.</span><span class="sxs-lookup"><span data-stu-id="794b0-113">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="794b0-114">Перейдите в папку `TodoList` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="794b0-114">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="794b0-115">Добавьте в приложение новый компонент Razor `Todo` в папке `Pages` с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="794b0-115">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="794b0-116">Имена файлов компонентов Razor должны начинаться с прописной буквы, поэтому убедитесь в том, что имя файла компонента `Todo` начинается с прописной буквы `T`.</span><span class="sxs-lookup"><span data-stu-id="794b0-116">Razor component file names require a capitalized first letter, so confirm that the `Todo` component file name starts with a capital letter `T`.</span></span>

1. <span data-ttu-id="794b0-117">В `Pages/Todo.razor` задайте начальную разметку компонента:</span><span class="sxs-lookup"><span data-stu-id="794b0-117">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="794b0-118">Добавьте компонент `Todo` на панель навигации.</span><span class="sxs-lookup"><span data-stu-id="794b0-118">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="794b0-119">Компонент `NavMenu` (`Shared/NavMenu.razor`) используется в макете этого приложения.</span><span class="sxs-lookup"><span data-stu-id="794b0-119">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="794b0-120">Макетами называются компоненты, которые избавляют от дублирования содержимого в приложении.</span><span class="sxs-lookup"><span data-stu-id="794b0-120">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="794b0-121">Добавьте `<NavLink>` для компонента `Todo`, включив следующую разметку с элементом списка под существующими элементами списка в файле `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="794b0-121">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="794b0-122">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="794b0-122">Rebuild and run the app.</span></span> <span data-ttu-id="794b0-123">Перейдите на новую страницу Todo, чтобы убедиться, что ссылка на компонент `Todo` работает правильно.</span><span class="sxs-lookup"><span data-stu-id="794b0-123">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="794b0-124">Добавьте в корень проекта файл `TodoItem.cs`, который будет содержать класс для элемента списка дел.</span><span class="sxs-lookup"><span data-stu-id="794b0-124">Add a `TodoItem.cs` file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="794b0-125">Используйте следующий код C# для класса `TodoItem`.</span><span class="sxs-lookup"><span data-stu-id="794b0-125">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="794b0-126">Снова вернитесь к компоненту `Todo` (`Pages/Todo.razor`).</span><span class="sxs-lookup"><span data-stu-id="794b0-126">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="794b0-127">Добавьте поле для элементов списка дел в блоке `@code`.</span><span class="sxs-lookup"><span data-stu-id="794b0-127">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="794b0-128">Компонент `Todo` использует это поле для сохранения состояния списка дел.</span><span class="sxs-lookup"><span data-stu-id="794b0-128">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="794b0-129">Добавьте разметку неупорядоченного списка и цикл `foreach` для отображения каждого элемента списка дела в виде элемента списка (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="794b0-129">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="794b0-130">Приложению необходимы элементы пользовательского интерфейса для добавления элементов в список дел.</span><span class="sxs-lookup"><span data-stu-id="794b0-130">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="794b0-131">Добавьте текстовое поле (`<input>`) и кнопку (`<button>`) под неупорядоченным списком (`<ul>...</ul>`).</span><span class="sxs-lookup"><span data-stu-id="794b0-131">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="794b0-132">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="794b0-132">Rebuild and run the app.</span></span> <span data-ttu-id="794b0-133">При нажатии кнопки **`Add todo`** ничего не происходит, так как к кнопке не привязан обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="794b0-133">When the **`Add todo`** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="794b0-134">Добавьте метод `AddTodo` в компонент `Todo` и зарегистрируйте его для нажатий кнопки с помощью атрибута `@onclick`.</span><span class="sxs-lookup"><span data-stu-id="794b0-134">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="794b0-135">Теперь при нажатии кнопки вызывается метод C# `AddTodo`:</span><span class="sxs-lookup"><span data-stu-id="794b0-135">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="794b0-136">Чтобы получить заголовок нового элемента списка дел, добавьте строку поля `newTodo` в верхней части блока `@code` и привяжите ее к значению вводимого текста с помощью атрибута `bind` в элементе `<input>`.</span><span class="sxs-lookup"><span data-stu-id="794b0-136">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="794b0-137">Обновите метод `AddTodo`, чтобы добавить `TodoItem` с указываемым названием в список.</span><span class="sxs-lookup"><span data-stu-id="794b0-137">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="794b0-138">Очистите значение текстового поля, задав пустую строку в качестве значения для `newTodo`.</span><span class="sxs-lookup"><span data-stu-id="794b0-138">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="794b0-139">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="794b0-139">Rebuild and run the app.</span></span> <span data-ttu-id="794b0-140">Добавьте несколько задач в список дел, чтобы проверить работу нового кода.</span><span class="sxs-lookup"><span data-stu-id="794b0-140">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="794b0-141">Вы можете сделать текст заголовка для каждого элемента списка дел редактируемым, а по дополнительному флажку пользователь сможет отслеживать завершение задач.</span><span class="sxs-lookup"><span data-stu-id="794b0-141">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="794b0-142">Добавьте флажок для каждого элемента списка дел и привяжите его значение к свойству `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="794b0-142">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="794b0-143">Замените `@todo.Title` элементом `<input>`, который привязан к `@todo.Title`.</span><span class="sxs-lookup"><span data-stu-id="794b0-143">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="794b0-144">Чтобы проверить привязку значений, обновите заголовок `<h3>`, чтобы в нем отображалось количество незавершенных дел в списке (`IsDone` имеет значение `false`).</span><span class="sxs-lookup"><span data-stu-id="794b0-144">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="794b0-145">Готовый компонент `Todo` (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="794b0-145">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="794b0-146">Скомпилируйте и запустите приложение.</span><span class="sxs-lookup"><span data-stu-id="794b0-146">Rebuild and run the app.</span></span> <span data-ttu-id="794b0-147">Добавьте элементы в список дел, чтобы протестировать новый код.</span><span class="sxs-lookup"><span data-stu-id="794b0-147">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="794b0-148">Следующие шаги</span><span class="sxs-lookup"><span data-stu-id="794b0-148">Next steps</span></span>

<span data-ttu-id="794b0-149">В этом руководстве вы узнали, как:</span><span class="sxs-lookup"><span data-stu-id="794b0-149">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="794b0-150">создавать проект приложения Blazor со списком задач;</span><span class="sxs-lookup"><span data-stu-id="794b0-150">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="794b0-151">изменять компоненты Razor;</span><span class="sxs-lookup"><span data-stu-id="794b0-151">Modify Razor components</span></span>
> * <span data-ttu-id="794b0-152">использовать обработку событий и привязку данных в компонентах;</span><span class="sxs-lookup"><span data-stu-id="794b0-152">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="794b0-153">использовать внедрение зависимостей и маршрутизацию в приложении Blazor.</span><span class="sxs-lookup"><span data-stu-id="794b0-153">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="794b0-154">Подробнее о шаблонах проектов Blazor:</span><span class="sxs-lookup"><span data-stu-id="794b0-154">Learn about the Blazor project templates:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/templates>
