---
title: Вспомогательная функция тегов кэша в MVC-моделях ASP.NET Core
author: pkellner
description: Сведения об использовании вспомогательной функции для тэга кэша.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: b1cab7ab8b491529ee4208d92fb30082be795eda
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635064"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Вспомогательная функция тегов кэша в MVC-моделях ASP.NET Core

Автор: [Питер Кельнер (Peter Kellner)](https://peterkellner.net)

Вспомогательная функция тегов кэша позволяет повысить производительность приложения ASP.NET Core за счет кэширования его содержимого во внутренний поставщик кэша ASP.NET Core.

Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.

Следующая Razor Разметка кэширует текущую дату:

```cshtml
<cache>@DateTime.Now</cache>
```

Первый запрос к странице, содержащей вспомогательную функцию тегов, отобразит текущую дату. Последующие запросы будут показывать кэшированное значение, пока срок действия кэша не истечет (по умолчанию — 20 минут) или пока кэшированная дата не будет удалена из кэша.

## <a name="cache-tag-helper-attributes"></a>Атрибуты вспомогательной функции тегов кэша

### <a name="enabled"></a>Включено

| Тип атрибута  | Примеры        | По умолчанию |
| --------------- | --------------- | ------- |
| Логическое         | `true`, `false` | `true`  |

`enabled` определяет, кэшируется ли содержимое, охватываемое вспомогательной функцией тегов кэша. Значение по умолчанию — `true`. Если установлено значение `false`, выводимые данные **не** кэшируются.

Пример

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| Тип атрибута   | Пример                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` задает абсолютную дату окончания срока действия для элемента кэша.

В следующем примере содержимое вспомогательной функции тегов кэша будет кэшировано до 29 января 2025 г., 17:02:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| Тип атрибута | Пример                      | По умолчанию    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 минут |

`expires-after` задает интервал времени для кэширования содержимого с момента первого запроса.

Пример

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

RazorОбработчик представлений задает значение по умолчанию, `expires-after` равное двадцать минутам.

### <a name="expires-sliding"></a>expires-sliding

| Тип атрибута | Пример                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Задает время, по истечении которого запись кэша следует удалить, если к ней не было обращений.

Пример

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| Тип атрибута | Примеры                                    |
| -------------- | ------------------------------------------- |
| Строка         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` принимает список разделенных запятыми значений заголовков, запускающих обновление кэша при их изменении.

Следующий пример показывает отслеживание значения заголовка `User-Agent`. Содержимое будет кэшироваться для каждого отдельного заголовка `User-Agent`, представленного на веб-сервере:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| Тип атрибута | Примеры             |
| -------------- | -------------------- |
| Строка         | `Make`, `Make,Model` |

`vary-by-query` принимает список разделенных запятыми <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> в строке запроса (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), запускающих обновление кэша при изменении значения любого указанного ключа.

Следующий пример показывает отслеживание значений `Make` и `Model`. Содержимое будет кэшироваться для каждого отдельного заголовка `Make` и `Model`, представленного на веб-сервере:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| Тип атрибута | Примеры             |
| -------------- | -------------------- |
| Строка         | `Make`, `Make,Model` |

`vary-by-route` принимает список разделенных запятыми имен параметров маршрута, запускающих обновление кэша при изменении значения параметра данных маршрута.

Пример

*Startup.cs*:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index. cshtml*:

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a>в зависимости отcookie

| Тип атрибута | Примеры                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| Строка         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` принимает разделенный запятыми список cookie имен, которые активируют обновление кэша при cookie изменении значений.

В следующем примере отслеживается объект, cookie связанный с ASP.NET Core Identity . При проверке подлинности пользователя изменение Identity cookie активирует обновление кэша:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| Тип атрибута  | Примеры        | По умолчанию |
| --------------- | --------------- | ------- |
| Логическое         | `true`, `false` | `true`  |

`vary-by-user` указывает, следует ли сбрасывать кэш при изменении вошедшего в систему пользователя (или участника контекста). Текущий пользователь также известен как участник контекста запроса, и его можно просмотреть в Razor представлении, обратившись к `@User.Identity.Name` .

В следующем примере отслеживается текущий вошедший пользователь для активации обновления кэша:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

При использовании этого атрибута содержимое сохраняется в кэше в течение цикла входа и выхода. Если присвоено значение `true`, цикл проверки подлинности делает недействительным кэш для прошедшего проверку подлинности пользователя. Кэш становится недействительным, так как cookie при проверке подлинности пользователя создается новое уникальное значение. Кэш сохраняется для анонимного состояния, если нет cookie или cookie истек срок действия. Если пользователь **не** прошел проверку подлинности, кэш сохраняется.

### <a name="vary-by"></a>vary-by

| Тип атрибута | Пример  |
| -------------- | -------- |
| Строка         | `@Model` |

`vary-by` позволяет настраивать, какие данные кэшируются. Содержимое вспомогательной функции тегов кэша обновляется при изменении объекта, на который ссылается строковое значение атрибута. Часто этому атрибуту назначается объединенная строка значений модели. По сути, это приводит к ситуации, когда обновление любого из объединенных значений приводит к сбросу кэша.

В следующем примере предполагается, что метод контроллера, визуализирующий представление, суммирует целочисленные значения двух параметров маршрута (`myParam1` и `myParam2`) и возвращает сумму как одно свойство модели. При изменении этой суммы содержимое вспомогательной функции тегов кэша визуализируется и кэшируется заново.  

Действие:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index. cshtml*:

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Тип атрибута      | Примеры                               | По умолчанию  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` предоставляет встроенному поставщику кэша инструкции по удалению кэша. При нехватке памяти веб-сервер будет первыми удалять записи кэша с приоритетом `Low`.

Пример

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Атрибут `priority` не гарантирует определенный уровень периода удержания кэша. `CacheItemPriority` носит лишь рекомендательный характер. Установка для этого атрибута значения `NeverRemove` не гарантирует постоянное хранение элементов кэша. Дополнительные сведения см. в разделе [Дополнительные ресурсы](#additional-resources).

Вспомогательная функция тегов кэша зависит от [службы кэширования в памяти](xref:performance/caching/memory). Вспомогательная функция тегов кэша добавляет эту службу, если она не добавлена.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
