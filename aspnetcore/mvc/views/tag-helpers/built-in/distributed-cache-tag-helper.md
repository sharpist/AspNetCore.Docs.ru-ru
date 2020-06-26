---
title: Вспомогательная функция тега распределенного кэша в ASP.NET Core
author: pkellner
description: Сведения об использовании вспомогательной функции для тэга распределенного кэша.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 4b8e393542c56502a825000773bbf714d91e4128
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399235"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>Вспомогательная функция тега распределенного кэша в ASP.NET Core

Автор: [Питер Кельнер (Peter Kellner)](https://peterkellner.net)

Вспомогательная функция тега распределенного кэша позволяет существенно повысить производительность приложения ASP.NET Core за счет кэширования его содержимого в источник распределенного кэша.

Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.

Вспомогательная функция тега распределенного кэша наследуется от того же базового класса, что и вспомогательная функция тега кэша. Все атрибуты [вспомогательной функции тега кэша](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) доступны вспомогательной функции тега распределенного кэша.

Вспомогательная функция тега распределенного кэша использует [внедрение через конструктор](xref:fundamentals/dependency-injection#constructor-injection-behavior). Интерфейс <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> передается в конструктор вспомогательной функции тега распределенного кэша. Если конкретная реализация `IDistributedCache` не создается в `Startup.ConfigureServices` (*Startup.cs*), вспомогательная функция тега распределенного кэша использует тот же поставщик в памяти для хранения кэшированных данных, что и [вспомогательная функция тега кэша](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

## <a name="distributed-cache-tag-helper-attributes"></a>Атрибуты вспомогательной функции тега распределенного кэша

### <a name="attributes-shared-with-the-cache-tag-helper"></a>Атрибуты, используемые совместно с вспомогательной функцией тега кэша

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

Вспомогательная функция тега распределенного кэша наследует от того же класса, что и вспомогательная функция тега кэша. Описание этих атрибутов см. в разделе [Вспомогательная функция тега кэша](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="name"></a>name

| Тип атрибута | Пример                               |
| -------------- | ------------------------------------- |
| Строка         | `my-distributed-cache-unique-key-101` |

`name` является обязательным. Атрибут `name` используется в качестве ключа для каждого хранимого экземпляра кэша. В отличие от вспомогательной функции тега кэша, которая назначает ключ кэша каждому экземпляру на основе Razor имени страницы и расположения на Razor странице, вспомогательная функция тега распределенного кэша использует только его ключ к атрибуту `name` .

Пример.

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>Реализации IDistributedCache для вспомогательной функции тега распределенного кэша

В ASP.NET Core есть две встроенные реализации интерфейса <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>. Одна из них основана на SQL Server, а другая — на Redis. Также доступны сторонние реализации, такие как [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html). Сведения об этих реализациях можно найти по адресу <xref:performance/caching/distributed>. Обе реализации предусматривают задание экземпляра `IDistributedCache` в `Startup`.

Атрибуты тегов, связанные с использованием определенной реализации `IDistributedCache`, отсутствуют.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
