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
# <a name="use-hub-filters-in-aspnet-core-signalr"></a>Использование фильтров концентратора в ASP.NET CoreSignalR

Фильтры концентраторов:

* Доступны в ASP.NET Core 5,0 или более поздней версии.
* Разрешить выполнение логики до и после вызова методов концентратора клиентами.

В этой статье приводятся рекомендации по написанию и использованию фильтров концентраторов.

## <a name="configure-hub-filters"></a>Настройка фильтров концентратора

Фильтры концентраторов можно применять глобально или по типам концентраторов. Порядок, в котором добавляются фильтры, — это порядок, в котором выполняются фильтры. Глобальные фильтры концентратора выполняются перед фильтрами локальных концентраторов.

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

Фильтр концентратора можно добавить одним из следующих способов:

* Добавьте фильтр по конкретному типу:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Это будет разрешено при внедрении зависимостей (DI) или активации типа.

* Добавьте фильтр по типу среды выполнения:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Это будет разрешено из DI или типа activated.

* Добавьте фильтр по экземпляру:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Этот экземпляр будет использоваться как одноэлементный. Все вызовы метода концентратора будут использовать один и тот же экземпляр.

Фильтры концентраторов создаются и удаляются на каждом вызове концентратора. Если вы хотите сохранить глобальное состояние в фильтре или без состояния, добавьте тип фильтра концентратора в класс DI в качестве одноэлементного для повышения производительности. Кроме того, можно добавить фильтр в качестве экземпляра, если это возможно.

## <a name="create-hub-filters"></a>Создание фильтров концентратора

Создайте фильтр, объявляя класс, наследующий от `IHubFilter` , и добавьте `InvokeMethodAsync` метод. Существует также `OnConnectedAsync` и `OnDisconnectedAsync` , что при необходимости можно реализовать для создания оболочки для `OnConnectedAsync` `OnDisconnectedAsync` методов концентратора и соответственно.

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

Фильтры очень похожи на по промежуточного слоя. `next`Метод вызывает следующий фильтр. Окончательный фильтр будет вызывать метод концентратора. Фильтры также могут сохранять результат ожидания `next` и выполнения логики после вызова метода концентратора перед возвратом результата из `next` .

Чтобы пропустить вызов метода концентратора в фильтре, вызовите исключение типа `HubException` вместо вызова `next` . Клиент получит сообщение об ошибке, если ожидался результат.

## <a name="use-hub-filters"></a>Использование фильтров концентратора

При написании логики фильтра попробуйте сделать ее универсальной с помощью атрибутов в методах концентратора вместо проверки имен методов концентратора.

Рассмотрим фильтр, который будет проверять аргумент метода концентратора на наличие запрещенных фраз и заменять все найденные фразы `***` .
В этом примере предполагается, что `LanguageFilterAttribute` определен класс. Класс имеет свойство с именем `FilterArgument` , которое может быть задано при использовании атрибута.

1. Поместите атрибут в метод концентратора, содержащий строковый аргумент для очистки:

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

1. Определите фильтр концентратора для проверки атрибута и замените запрещенные фразы в аргументе метода концентратора `***` следующим:

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

1. Зарегистрируйте фильтр концентратора в `Startup.ConfigureServices` методе. Чтобы избежать повторной инициализации списка запрещенных фраз для каждого вызова, фильтр концентратора регистрируется как одноэлементный.

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

## <a name="the-hubinvocationcontext-object"></a>Объект Хубинвокатионконтекст

`HubInvocationContext`Содержит сведения о текущем вызове метода концентратора.

| Свойство | Описание | Тип |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Содержит сведения о соединении. | `HubCallerContext` |
| `Hub` | Экземпляр концентратора, используемый для этого вызова метода концентратора. | `Hub` |
| `HubMethodName` | Имя вызываемого метода концентратора. | `string` |
| `HubMethodArguments` | Список аргументов, передаваемых в метод концентратора. | `IReadOnlyList<string>` |
| `ServiceProvider` | Поставщик служб с заданной областью для этого вызова метода концентратора. | `IServiceProvider` |
| `HubMethod` | Сведения о методе концентратора. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>Объект Хублифетимеконтекст

`HubLifetimeContext`Содержит сведения для `OnConnectedAsync` `OnDisconnectedAsync` методов концентратора и.

| Свойство | Описание | Тип |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Содержит сведения о соединении. | `HubCallerContext` |
| `Hub` | Экземпляр концентратора, используемый для этого вызова метода концентратора. | `Hub` |
| `ServiceProvider` | Поставщик служб с заданной областью для этого вызова метода концентратора. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Авторизация и фильтры

[Авторизация атрибутов в методах концентратора](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) выполняется перед фильтрами концентратора.
