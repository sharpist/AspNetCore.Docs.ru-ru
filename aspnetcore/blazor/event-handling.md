---
<span data-ttu-id="dbbf3-101">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-101">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-103">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-104">'Identity'</span></span>
- <span data-ttu-id="dbbf3-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-106">'Razor'</span></span>
- <span data-ttu-id="dbbf3-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="dbbf3-108">Обработка событий Blazor в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbbf3-108">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="dbbf3-109">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="dbbf3-109">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="dbbf3-110">Компоненты Razor предоставляют функции обработки событий.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-110">Razor components provide event handling features.</span></span> <span data-ttu-id="dbbf3-111">Для атрибута HTML-элемента с именем [`@on{EVENT}`](xref:mvc/views/razor#onevent) (например, `@onclick`) и значением типа делегата компонент Razor рассматривает значение атрибута как обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-111">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="dbbf3-112">Следующий код вызывает метод `UpdateHeading`, когда в пользовательском интерфейсе выбрана кнопка:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-112">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="dbbf3-113">Следующий код вызывает метод `CheckChanged`, когда в пользовательском интерфейсе установлен флажок:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-113">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="dbbf3-114">Обработчики событий также могут быть асинхронными и возвращать <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-114">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="dbbf3-115">Нет необходимости вручную вызывать [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="dbbf3-115">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="dbbf3-116">Исключения регистрируются при их возникновении.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-116">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="dbbf3-117">В следующем примере `UpdateHeading` вызывается асинхронно при выборе кнопки:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-117">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a><span data-ttu-id="dbbf3-118">Типы аргументов событий</span><span class="sxs-lookup"><span data-stu-id="dbbf3-118">Event argument types</span></span>

<span data-ttu-id="dbbf3-119">Для некоторых событий разрешены типы аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-119">For some events, event argument types are permitted.</span></span> <span data-ttu-id="dbbf3-120">Указание типа события в вызове метода требуется только в том случае, если тип события используется в методе.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-120">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="dbbf3-121">Поддерживаемые параметры <xref:System.EventArgs> приведены в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-121">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="dbbf3-122">событие</span><span class="sxs-lookup"><span data-stu-id="dbbf3-122">Event</span></span>            | <span data-ttu-id="dbbf3-123">Класс</span><span class="sxs-lookup"><span data-stu-id="dbbf3-123">Class</span></span>                | <span data-ttu-id="dbbf3-124">События DOM и примечания</span><span class="sxs-lookup"><span data-stu-id="dbbf3-124">DOM events and notes</span></span> |
| ---
<span data-ttu-id="dbbf3-125">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-125">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-126">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-127">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-127">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-128">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-128">'Identity'</span></span>
- <span data-ttu-id="dbbf3-129">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-129">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-130">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-130">'Razor'</span></span>
- <span data-ttu-id="dbbf3-131">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-131">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-132">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-132">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-133">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-134">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-134">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-135">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-135">'Identity'</span></span>
- <span data-ttu-id="dbbf3-136">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-136">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-137">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-137">'Razor'</span></span>
- <span data-ttu-id="dbbf3-138">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-138">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-139">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-139">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-140">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-141">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-141">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-142">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-142">'Identity'</span></span>
- <span data-ttu-id="dbbf3-143">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-143">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-144">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-144">'Razor'</span></span>
- <span data-ttu-id="dbbf3-145">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-145">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-146">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-146">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-147">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-148">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-149">'Identity'</span></span>
- <span data-ttu-id="dbbf3-150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-150">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-151">'Razor'</span></span>
- <span data-ttu-id="dbbf3-152">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-153">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-153">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-154">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-155">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-156">'Identity'</span></span>
- <span data-ttu-id="dbbf3-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-158">'Razor'</span></span>
- <span data-ttu-id="dbbf3-159">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-160">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-160">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-161">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-162">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-163">'Identity'</span></span>
- <span data-ttu-id="dbbf3-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-165">'Razor'</span></span>
- <span data-ttu-id="dbbf3-166">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-166">'SignalR' uid:</span></span> 

<span data-ttu-id="dbbf3-167">-------- | --- название: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-167">-------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-168">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-169">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-170">'Identity'</span></span>
- <span data-ttu-id="dbbf3-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-172">'Razor'</span></span>
- <span data-ttu-id="dbbf3-173">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-174">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-174">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-175">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-176">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-176">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-177">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-177">'Identity'</span></span>
- <span data-ttu-id="dbbf3-178">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-178">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-179">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-179">'Razor'</span></span>
- <span data-ttu-id="dbbf3-180">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-180">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-181">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-181">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-182">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-183">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-184">'Identity'</span></span>
- <span data-ttu-id="dbbf3-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-186">'Razor'</span></span>
- <span data-ttu-id="dbbf3-187">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-187">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-188">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-188">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-189">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-190">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-191">'Identity'</span></span>
- <span data-ttu-id="dbbf3-192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-192">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-193">'Razor'</span></span>
- <span data-ttu-id="dbbf3-194">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-195">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-195">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-196">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-197">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-198">'Identity'</span></span>
- <span data-ttu-id="dbbf3-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-200">'Razor'</span></span>
- <span data-ttu-id="dbbf3-201">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-201">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-202">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-202">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-203">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-204">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-205">'Identity'</span></span>
- <span data-ttu-id="dbbf3-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-207">'Razor'</span></span>
- <span data-ttu-id="dbbf3-208">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-209">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-209">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-210">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-211">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-211">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-212">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-212">'Identity'</span></span>
- <span data-ttu-id="dbbf3-213">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-213">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-214">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-214">'Razor'</span></span>
- <span data-ttu-id="dbbf3-215">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-215">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-216">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-216">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-217">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-218">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-218">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-219">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-219">'Identity'</span></span>
- <span data-ttu-id="dbbf3-220">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-220">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-221">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-221">'Razor'</span></span>
- <span data-ttu-id="dbbf3-222">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-222">'SignalR' uid:</span></span> 

<span data-ttu-id="dbbf3-223">---------- | --- название: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-223">---------- | --- title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-224">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-225">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-225">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-226">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-226">'Identity'</span></span>
- <span data-ttu-id="dbbf3-227">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-227">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-228">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-228">'Razor'</span></span>
- <span data-ttu-id="dbbf3-229">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-229">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-230">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-230">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-231">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-232">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-233">'Identity'</span></span>
- <span data-ttu-id="dbbf3-234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-234">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-235">'Razor'</span></span>
- <span data-ttu-id="dbbf3-236">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-237">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-237">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-238">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-239">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-240">'Identity'</span></span>
- <span data-ttu-id="dbbf3-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-242">'Razor'</span></span>
- <span data-ttu-id="dbbf3-243">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-244">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-244">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-245">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-246">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-247">'Identity'</span></span>
- <span data-ttu-id="dbbf3-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-249">'Razor'</span></span>
- <span data-ttu-id="dbbf3-250">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-251">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-251">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-252">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-253">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-254">'Identity'</span></span>
- <span data-ttu-id="dbbf3-255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-255">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-256">'Razor'</span></span>
- <span data-ttu-id="dbbf3-257">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-258">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-258">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-259">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-260">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-260">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-261">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-261">'Identity'</span></span>
- <span data-ttu-id="dbbf3-262">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-262">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-263">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-263">'Razor'</span></span>
- <span data-ttu-id="dbbf3-264">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-264">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-265">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-265">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-266">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-267">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-268">'Identity'</span></span>
- <span data-ttu-id="dbbf3-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-270">'Razor'</span></span>
- <span data-ttu-id="dbbf3-271">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dbbf3-272">title: "Обработка событий Blazor в ASP.NET Core" author: description: "Сведения о функциях обработки событий Blazor, включая типы аргументов событий, обратные вызовы событий и управление событиями браузера по умолчанию."</span><span class="sxs-lookup"><span data-stu-id="dbbf3-272">title: 'ASP.NET Core Blazor event handling' author: description: 'Learn about Blazor's event handling features, including event argument types, event callbacks, and managing default browser events.'</span></span>
<span data-ttu-id="dbbf3-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-273">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dbbf3-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-274">'Blazor'</span></span>
- <span data-ttu-id="dbbf3-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-275">'Identity'</span></span>
- <span data-ttu-id="dbbf3-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="dbbf3-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dbbf3-277">'Razor'</span></span>
- <span data-ttu-id="dbbf3-278">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dbbf3-278">'SignalR' uid:</span></span> 

<span data-ttu-id="dbbf3-279">---------- | | Буфер обмена | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Перетаскивание | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="dbbf3-279">---------- | | Clipboard        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Drag             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="dbbf3-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> и <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> содержат данные перетаскиваемого элемента.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-280"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> <span data-ttu-id="dbbf3-281">| | Ошибка | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Событие | <xref:System.EventArgs> | *Общие*</span><span class="sxs-lookup"><span data-stu-id="dbbf3-281">| | Error            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Event            | <xref:System.EventArgs> | *General*</span></span><br><span data-ttu-id="dbbf3-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="dbbf3-282">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="dbbf3-283">*Буфер обмена*</span><span class="sxs-lookup"><span data-stu-id="dbbf3-283">*Clipboard*</span></span><br><span data-ttu-id="dbbf3-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="dbbf3-284">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="dbbf3-285">*Ввод*</span><span class="sxs-lookup"><span data-stu-id="dbbf3-285">*Input*</span></span><br><span data-ttu-id="dbbf3-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="dbbf3-286">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="dbbf3-287">*Носитель*</span><span class="sxs-lookup"><span data-stu-id="dbbf3-287">*Media*</span></span><br><span data-ttu-id="dbbf3-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="dbbf3-288">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="dbbf3-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> содержит атрибуты для настройки сопоставлений между именами событий и типами аргументов событий.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-289"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> <span data-ttu-id="dbbf3-290">| | Фокус | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin` `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="dbbf3-290">| | Focus            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="dbbf3-291">Не включает поддержку `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-291">Doesn't include support for `relatedTarget`.</span></span> <span data-ttu-id="dbbf3-292">| | Входные данные | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Клавиатура | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Мышь | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Указатель мыши | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Колесо мыши | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Ход выполнения | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Сенсорный ввод | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave` `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="dbbf3-292">| | Input            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Keyboard         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Mouse            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Mouse pointer    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Mouse wheel      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Progress         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Touch            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="dbbf3-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> представляет одну точку касания на устройстве с сенсорным вводом.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-293"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="dbbf3-294">Дополнительные сведения см. в следующих ресурсах:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-294">For more information, see the following resources:</span></span>

* <span data-ttu-id="dbbf3-295">[Классы EventArgs в источнике ссылки на ASP.NET Core (ветвь DotNet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="dbbf3-295">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="dbbf3-296">[Веб-документы MDN: GlobalEventHandlers.](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) Содержит сведения о том, какие элементы HTML поддерживают каждое из событий DOM.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-296">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="dbbf3-297">Лямбда-выражения</span><span class="sxs-lookup"><span data-stu-id="dbbf3-297">Lambda expressions</span></span>

<span data-ttu-id="dbbf3-298">Также можно использовать [лямбда-выражения](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions):</span><span class="sxs-lookup"><span data-stu-id="dbbf3-298">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="dbbf3-299">Часто бывает удобно закрывать дополнительные значения, например при переборе набора элементов.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-299">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="dbbf3-300">В следующем примере создаются три кнопки, каждая из которых вызывает `UpdateHeading` для передачи аргумента события (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) и номера кнопки (`buttonNumber`) при выборе в пользовательском интерфейсе:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-300">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="dbbf3-301">**Не** используйте переменную цикла (`i`) в цикле `for` непосредственно в лямбда-выражении.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-301">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="dbbf3-302">В противном случае одна и та же переменная будет использоваться во всех лямбда-выражениях, в результате чего значение `i` будет одинаковым во всех лямбда-выражениях.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-302">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="dbbf3-303">Всегда записывайте значение в локальную переменную (`buttonNumber` в предыдущем примере), а затем используйте ее.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-303">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="dbbf3-304">EventCallback</span><span class="sxs-lookup"><span data-stu-id="dbbf3-304">EventCallback</span></span>

<span data-ttu-id="dbbf3-305">Распространенным сценарием с вложенными компонентами является запуск метода родительского компонента при возникновении события дочернего компонента,</span><span class="sxs-lookup"><span data-stu-id="dbbf3-305">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="dbbf3-306">например, когда в дочернем элементе возникает событие `onclick`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-306">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="dbbf3-307">Чтобы обеспечить доступ к событиям в компонентах, используйте <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-307">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="dbbf3-308">Родительский компонент может назначить метод обратного вызова <xref:Microsoft.AspNetCore.Components.EventCallback> дочернего компонента.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-308">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="dbbf3-309">В `ChildComponent` в примере приложения (*Components/ChildComponent.razor*) показано, как обработчик `onclick` кнопки настроен на получение делегата <xref:Microsoft.AspNetCore.Components.EventCallback> из `ParentComponent` образца.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-309">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="dbbf3-310"><xref:Microsoft.AspNetCore.Components.EventCallback> вводится с `MouseEventArgs`, что подходит для события `onclick` от периферийного устройства:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-310">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="dbbf3-311">`ParentComponent` задает для <xref:Microsoft.AspNetCore.Components.EventCallback%601> дочернего компонента (`OnClickCallback`) его метод `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-311">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="dbbf3-312">*Pages/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-312">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="dbbf3-313">При выборе кнопки в `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-313">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="dbbf3-314">Вызывается метод `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-314">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="dbbf3-315">`messageText` обновляется и отображается в `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-315">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="dbbf3-316">Вызов [StateHasChanged](xref:blazor/lifecycle#state-changes) не требуется в методе обратного вызова (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="dbbf3-316">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="dbbf3-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> вызывается автоматически для повторной отрисовки `ParentComponent`, так же как и дочерние события запускают повторную отрисовку компонента в обработчиках событий, которые выполняются в дочернем элементе.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-317"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="dbbf3-318"><xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> разрешают выполнять асинхронные делегаты.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-318"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="dbbf3-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> является строго типизированным и требует определенного типа аргумента.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-319"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="dbbf3-320"><xref:Microsoft.AspNetCore.Components.EventCallback> слабо типизирован и допускает любой тип аргумента.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-320"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="dbbf3-321">Вызов <xref:Microsoft.AspNetCore.Components.EventCallback> или <xref:Microsoft.AspNetCore.Components.EventCallback%601> с <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> и ожидание <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-321">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="dbbf3-322">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback> и <xref:Microsoft.AspNetCore.Components.EventCallback%601> для обработки событий и параметров компонента привязки.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-322">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="dbbf3-323">Предпочтительнее использовать строго типизированный <xref:Microsoft.AspNetCore.Components.EventCallback%601> вместо <xref:Microsoft.AspNetCore.Components.EventCallback>.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-323">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="dbbf3-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> обеспечивает более эффективное реагирование на ошибки для пользователей компонента.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-324"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="dbbf3-325">Как и в случае с другими обработчиками событий пользовательского интерфейса, указание параметра события является необязательным.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-325">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="dbbf3-326">Используйте <xref:Microsoft.AspNetCore.Components.EventCallback>, если в обратный вызов не было передано значение.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-326">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="dbbf3-327">Запрет действий по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dbbf3-327">Prevent default actions</span></span>

<span data-ttu-id="dbbf3-328">Используйте атрибут директивы [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault), чтобы запретить выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-328">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="dbbf3-329">Если на устройстве ввода выбран ключ, а фокус находится на текстовом поле, то в браузере в текстовом поле обычно отображается символ ключа.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-329">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="dbbf3-330">В следующем примере поведение по умолчанию запрещено путем указания атрибута директивы `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-330">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="dbbf3-331">Счетчик увеличивается, а ключ **+** не записывается в значение элемента `<input>`:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-331">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

<span data-ttu-id="dbbf3-332">Указание атрибута `@on{EVENT}:preventDefault` без значения эквивалентно `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-332">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="dbbf3-333">Значение атрибута также может быть выражением.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-333">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="dbbf3-334">В следующем примере `shouldPreventDefault` является полем `bool`, для которого задано значение `true` или `false`:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-334">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="dbbf3-335">Для запрета действий по умолчанию не требуется обработчик событий.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-335">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="dbbf3-336">Обработчик событий и сценарий запрета действий по умолчанию можно использовать независимо друг от друга.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-336">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="dbbf3-337">Остановка распространения событий</span><span class="sxs-lookup"><span data-stu-id="dbbf3-337">Stop event propagation</span></span>

<span data-ttu-id="dbbf3-338">Используйте атрибут директивы [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation), чтобы остановить распространение событий.</span><span class="sxs-lookup"><span data-stu-id="dbbf3-338">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="dbbf3-339">В следующем примере при установке флажка события щелчка мышью из второго дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:</span><span class="sxs-lookup"><span data-stu-id="dbbf3-339">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
