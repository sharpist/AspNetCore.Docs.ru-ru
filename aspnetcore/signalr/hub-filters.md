---
title: Использование фильтров концентратора в ASP.NET CoreSignalR
author: brecon
description: Узнайте, как использовать фильтры концентратора в ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/hub-filters
ms.openlocfilehash: afdb52039c0eff53a421038518c687c78e1d509b
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756071"
---
# <a name="use-hub-filters-in-aspnet-core-signalr"></a><span data-ttu-id="fab7e-103">Использование фильтров концентратора в ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="fab7e-103">Use hub filters in ASP.NET Core SignalR</span></span>

<span data-ttu-id="fab7e-104">Фильтры концентраторов:</span><span class="sxs-lookup"><span data-stu-id="fab7e-104">Hub filters:</span></span>

* <span data-ttu-id="fab7e-105">Доступны в ASP.NET Core 5,0 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="fab7e-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="fab7e-106">Разрешить выполнение логики до и после вызова методов концентратора клиентами.</span><span class="sxs-lookup"><span data-stu-id="fab7e-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="fab7e-107">В этой статье приводятся рекомендации по написанию и использованию фильтров концентраторов.</span><span class="sxs-lookup"><span data-stu-id="fab7e-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="fab7e-108">Настройка фильтров концентратора</span><span class="sxs-lookup"><span data-stu-id="fab7e-108">Configure hub filters</span></span>

<span data-ttu-id="fab7e-109">Фильтры концентраторов можно применять глобально или по типам концентраторов.</span><span class="sxs-lookup"><span data-stu-id="fab7e-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="fab7e-110">Порядок, в котором добавляются фильтры, — это порядок, в котором выполняются фильтры.</span><span class="sxs-lookup"><span data-stu-id="fab7e-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="fab7e-111">Глобальные фильтры концентратора выполняются перед фильтрами локальных концентраторов.</span><span class="sxs-lookup"><span data-stu-id="fab7e-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="fab7e-112">Фильтр концентратора можно добавить одним из следующих способов:</span><span class="sxs-lookup"><span data-stu-id="fab7e-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="fab7e-113">Добавьте фильтр по конкретному типу:</span><span class="sxs-lookup"><span data-stu-id="fab7e-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="fab7e-114">Это будет разрешено при внедрении зависимостей (DI) или активации типа.</span><span class="sxs-lookup"><span data-stu-id="fab7e-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="fab7e-115">Добавьте фильтр по типу среды выполнения:</span><span class="sxs-lookup"><span data-stu-id="fab7e-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="fab7e-116">Это будет разрешено из DI или типа activated.</span><span class="sxs-lookup"><span data-stu-id="fab7e-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="fab7e-117">Добавьте фильтр по экземпляру:</span><span class="sxs-lookup"><span data-stu-id="fab7e-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="fab7e-118">Этот экземпляр будет использоваться как одноэлементный.</span><span class="sxs-lookup"><span data-stu-id="fab7e-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="fab7e-119">Все вызовы метода концентратора будут использовать один и тот же экземпляр.</span><span class="sxs-lookup"><span data-stu-id="fab7e-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="fab7e-120">Фильтры концентраторов создаются и удаляются на каждом вызове концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="fab7e-121">Если вы хотите сохранить глобальное состояние в фильтре или без состояния, добавьте тип фильтра концентратора в класс DI в качестве одноэлементного для повышения производительности.</span><span class="sxs-lookup"><span data-stu-id="fab7e-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="fab7e-122">Кроме того, можно добавить фильтр в качестве экземпляра, если это возможно.</span><span class="sxs-lookup"><span data-stu-id="fab7e-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="fab7e-123">Создание фильтров концентратора</span><span class="sxs-lookup"><span data-stu-id="fab7e-123">Create hub filters</span></span>

<span data-ttu-id="fab7e-124">Создайте фильтр, объявляя класс, наследующий от `IHubFilter` , и добавьте `InvokeMethodAsync` метод.</span><span class="sxs-lookup"><span data-stu-id="fab7e-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="fab7e-125">Существует также `OnConnectedAsync` и `OnDisconnectedAsync` , что при необходимости можно реализовать для создания оболочки для `OnConnectedAsync` `OnDisconnectedAsync` методов концентратора и соответственно.</span><span class="sxs-lookup"><span data-stu-id="fab7e-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="fab7e-126">Фильтры очень похожи на по промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fab7e-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="fab7e-127">`next`Метод вызывает следующий фильтр.</span><span class="sxs-lookup"><span data-stu-id="fab7e-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="fab7e-128">Окончательный фильтр будет вызывать метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="fab7e-129">Фильтры также могут сохранять результат ожидания `next` и выполнения логики после вызова метода концентратора перед возвратом результата из `next` .</span><span class="sxs-lookup"><span data-stu-id="fab7e-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="fab7e-130">Чтобы пропустить вызов метода концентратора в фильтре, вызовите исключение типа `HubException` вместо вызова `next` .</span><span class="sxs-lookup"><span data-stu-id="fab7e-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="fab7e-131">Клиент получит сообщение об ошибке, если ожидался результат.</span><span class="sxs-lookup"><span data-stu-id="fab7e-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="fab7e-132">Использование фильтров концентратора</span><span class="sxs-lookup"><span data-stu-id="fab7e-132">Use hub filters</span></span>

<span data-ttu-id="fab7e-133">При написании логики фильтра попробуйте сделать ее универсальной с помощью атрибутов в методах концентратора вместо проверки имен методов концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="fab7e-134">Рассмотрим фильтр, который будет проверять аргумент метода концентратора на наличие запрещенных фраз и заменять все найденные фразы `***` .</span><span class="sxs-lookup"><span data-stu-id="fab7e-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="fab7e-135">В этом примере предполагается, что `LanguageFilterAttribute` определен класс.</span><span class="sxs-lookup"><span data-stu-id="fab7e-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="fab7e-136">Класс имеет свойство с именем `FilterArgument` , которое может быть задано при использовании атрибута.</span><span class="sxs-lookup"><span data-stu-id="fab7e-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="fab7e-137">Поместите атрибут в метод концентратора, содержащий строковый аргумент для очистки:</span><span class="sxs-lookup"><span data-stu-id="fab7e-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="fab7e-138">Определите фильтр концентратора для проверки атрибута и замените запрещенные фразы в аргументе метода концентратора `***` следующим:</span><span class="sxs-lookup"><span data-stu-id="fab7e-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `***`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "***");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="fab7e-139">Зарегистрируйте фильтр концентратора в `Startup.ConfigureServices` методе.</span><span class="sxs-lookup"><span data-stu-id="fab7e-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fab7e-140">Чтобы избежать повторной инициализации списка запрещенных фраз для каждого вызова, фильтр концентратора регистрируется как одноэлементный.</span><span class="sxs-lookup"><span data-stu-id="fab7e-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="fab7e-141">Объект Хубинвокатионконтекст</span><span class="sxs-lookup"><span data-stu-id="fab7e-141">The HubInvocationContext object</span></span>

<span data-ttu-id="fab7e-142">`HubInvocationContext`Содержит сведения о текущем вызове метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="fab7e-143">Свойство</span><span class="sxs-lookup"><span data-stu-id="fab7e-143">Property</span></span> | <span data-ttu-id="fab7e-144">Описание</span><span class="sxs-lookup"><span data-stu-id="fab7e-144">Description</span></span> | <span data-ttu-id="fab7e-145">Тип</span><span class="sxs-lookup"><span data-stu-id="fab7e-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="fab7e-146">`HubCallerContext`Содержит сведения о соединении.</span><span class="sxs-lookup"><span data-stu-id="fab7e-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="fab7e-147">Экземпляр концентратора, используемый для этого вызова метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="fab7e-148">Имя вызываемого метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="fab7e-149">Список аргументов, передаваемых в метод концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="fab7e-150">Поставщик служб с заданной областью для этого вызова метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="fab7e-151">Сведения о методе концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="fab7e-152">Объект Хублифетимеконтекст</span><span class="sxs-lookup"><span data-stu-id="fab7e-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="fab7e-153">`HubLifetimeContext`Содержит сведения для `OnConnectedAsync` `OnDisconnectedAsync` методов концентратора и.</span><span class="sxs-lookup"><span data-stu-id="fab7e-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="fab7e-154">Свойство</span><span class="sxs-lookup"><span data-stu-id="fab7e-154">Property</span></span> | <span data-ttu-id="fab7e-155">Описание</span><span class="sxs-lookup"><span data-stu-id="fab7e-155">Description</span></span> | <span data-ttu-id="fab7e-156">Тип</span><span class="sxs-lookup"><span data-stu-id="fab7e-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="fab7e-157">`HubCallerContext`Содержит сведения о соединении.</span><span class="sxs-lookup"><span data-stu-id="fab7e-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="fab7e-158">Экземпляр концентратора, используемый для этого вызова метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="fab7e-159">Поставщик служб с заданной областью для этого вызова метода концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="fab7e-160">Авторизация и фильтры</span><span class="sxs-lookup"><span data-stu-id="fab7e-160">Authorization and filters</span></span>

<span data-ttu-id="fab7e-161">[Авторизация атрибутов в методах концентратора](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) выполняется перед фильтрами концентратора.</span><span class="sxs-lookup"><span data-stu-id="fab7e-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
