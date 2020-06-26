---
title: Вспомогательная функция тегов кэша в MVC-моделях ASP.NET Core
author: pkellner
description: Сведения об использовании вспомогательной функции для тэга кэша.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: 65d8bbcdaed76a308b924ba024219e8f520bb585
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399287"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="7b802-103">Вспомогательная функция тегов кэша в MVC-моделях ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b802-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="7b802-104">Автор: [Питер Кельнер (Peter Kellner)](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="7b802-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="7b802-105">Вспомогательная функция тегов кэша позволяет повысить производительность приложения ASP.NET Core за счет кэширования его содержимого во внутренний поставщик кэша ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b802-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="7b802-106">Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="7b802-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="7b802-107">Следующая Razor Разметка кэширует текущую дату:</span><span class="sxs-lookup"><span data-stu-id="7b802-107">The following Razor markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="7b802-108">Первый запрос к странице, содержащей вспомогательную функцию тегов, отобразит текущую дату.</span><span class="sxs-lookup"><span data-stu-id="7b802-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="7b802-109">Последующие запросы будут показывать кэшированное значение, пока срок действия кэша не истечет (по умолчанию — 20 минут) или пока кэшированная дата не будет удалена из кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="7b802-110">Атрибуты вспомогательной функции тегов кэша</span><span class="sxs-lookup"><span data-stu-id="7b802-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="7b802-111">Включено</span><span class="sxs-lookup"><span data-stu-id="7b802-111">enabled</span></span>

| <span data-ttu-id="7b802-112">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-112">Attribute Type</span></span>  | <span data-ttu-id="7b802-113">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-113">Examples</span></span>        | <span data-ttu-id="7b802-114">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7b802-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="7b802-115">Логическое</span><span class="sxs-lookup"><span data-stu-id="7b802-115">Boolean</span></span>         | <span data-ttu-id="7b802-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="7b802-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="7b802-117">`enabled` определяет, кэшируется ли содержимое, охватываемое вспомогательной функцией тегов кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="7b802-118">Значение по умолчанию — `true`.</span><span class="sxs-lookup"><span data-stu-id="7b802-118">The default is `true`.</span></span> <span data-ttu-id="7b802-119">Если установлено значение `false`, выводимые данные **не** кэшируются.</span><span class="sxs-lookup"><span data-stu-id="7b802-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="7b802-120">Пример.</span><span class="sxs-lookup"><span data-stu-id="7b802-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="7b802-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="7b802-121">expires-on</span></span>

| <span data-ttu-id="7b802-122">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-122">Attribute Type</span></span>   | <span data-ttu-id="7b802-123">Пример</span><span class="sxs-lookup"><span data-stu-id="7b802-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="7b802-124">`expires-on` задает абсолютную дату окончания срока действия для элемента кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="7b802-125">В следующем примере содержимое вспомогательной функции тегов кэша будет кэшировано до 29 января 2025 г., 17:02:</span><span class="sxs-lookup"><span data-stu-id="7b802-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="7b802-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="7b802-126">expires-after</span></span>

| <span data-ttu-id="7b802-127">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-127">Attribute Type</span></span> | <span data-ttu-id="7b802-128">Пример</span><span class="sxs-lookup"><span data-stu-id="7b802-128">Example</span></span>                      | <span data-ttu-id="7b802-129">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7b802-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="7b802-130">20 минут</span><span class="sxs-lookup"><span data-stu-id="7b802-130">20 minutes</span></span> |

<span data-ttu-id="7b802-131">`expires-after` задает интервал времени для кэширования содержимого с момента первого запроса.</span><span class="sxs-lookup"><span data-stu-id="7b802-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="7b802-132">Пример.</span><span class="sxs-lookup"><span data-stu-id="7b802-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="7b802-133">RazorОбработчик представлений задает значение по умолчанию, `expires-after` равное двадцать минутам.</span><span class="sxs-lookup"><span data-stu-id="7b802-133">The Razor View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="7b802-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="7b802-134">expires-sliding</span></span>

| <span data-ttu-id="7b802-135">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-135">Attribute Type</span></span> | <span data-ttu-id="7b802-136">Пример</span><span class="sxs-lookup"><span data-stu-id="7b802-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="7b802-137">Задает время, по истечении которого запись кэша следует удалить, если к ней не было обращений.</span><span class="sxs-lookup"><span data-stu-id="7b802-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="7b802-138">Пример.</span><span class="sxs-lookup"><span data-stu-id="7b802-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="7b802-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="7b802-139">vary-by-header</span></span>

| <span data-ttu-id="7b802-140">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-140">Attribute Type</span></span> | <span data-ttu-id="7b802-141">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="7b802-142">Строка</span><span class="sxs-lookup"><span data-stu-id="7b802-142">String</span></span>         | <span data-ttu-id="7b802-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="7b802-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="7b802-144">`vary-by-header` принимает список разделенных запятыми значений заголовков, запускающих обновление кэша при их изменении.</span><span class="sxs-lookup"><span data-stu-id="7b802-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="7b802-145">Следующий пример показывает отслеживание значения заголовка `User-Agent`.</span><span class="sxs-lookup"><span data-stu-id="7b802-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="7b802-146">Содержимое будет кэшироваться для каждого отдельного заголовка `User-Agent`, представленного на веб-сервере:</span><span class="sxs-lookup"><span data-stu-id="7b802-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="7b802-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="7b802-147">vary-by-query</span></span>

| <span data-ttu-id="7b802-148">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-148">Attribute Type</span></span> | <span data-ttu-id="7b802-149">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="7b802-150">Строка</span><span class="sxs-lookup"><span data-stu-id="7b802-150">String</span></span>         | <span data-ttu-id="7b802-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="7b802-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="7b802-152">`vary-by-query` принимает список разделенных запятыми <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> в строке запроса (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), запускающих обновление кэша при изменении значения любого указанного ключа.</span><span class="sxs-lookup"><span data-stu-id="7b802-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="7b802-153">Следующий пример показывает отслеживание значений `Make` и `Model`.</span><span class="sxs-lookup"><span data-stu-id="7b802-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="7b802-154">Содержимое будет кэшироваться для каждого отдельного заголовка `Make` и `Model`, представленного на веб-сервере:</span><span class="sxs-lookup"><span data-stu-id="7b802-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="7b802-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="7b802-155">vary-by-route</span></span>

| <span data-ttu-id="7b802-156">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-156">Attribute Type</span></span> | <span data-ttu-id="7b802-157">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="7b802-158">Строка</span><span class="sxs-lookup"><span data-stu-id="7b802-158">String</span></span>         | <span data-ttu-id="7b802-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="7b802-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="7b802-160">`vary-by-route` принимает список разделенных запятыми имен параметров маршрута, запускающих обновление кэша при изменении значения параметра данных маршрута.</span><span class="sxs-lookup"><span data-stu-id="7b802-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="7b802-161">Пример.</span><span class="sxs-lookup"><span data-stu-id="7b802-161">Example:</span></span>

<span data-ttu-id="7b802-162">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b802-162">*Startup.cs*:</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="7b802-163">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7b802-163">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-cookie"></a><span data-ttu-id="7b802-164">vary-by-cookie</span><span class="sxs-lookup"><span data-stu-id="7b802-164">vary-by-cookie</span></span>

| <span data-ttu-id="7b802-165">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-165">Attribute Type</span></span> | <span data-ttu-id="7b802-166">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="7b802-167">Строка</span><span class="sxs-lookup"><span data-stu-id="7b802-167">String</span></span>         | <span data-ttu-id="7b802-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="7b802-168">`.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor`</span></span> |

<span data-ttu-id="7b802-169">`vary-by-cookie` принимает список разделенных запятыми имен cookie, запускающих обновление кэша при изменении их значений.</span><span class="sxs-lookup"><span data-stu-id="7b802-169">`vary-by-cookie` accepts a comma-delimited list of cookie names that trigger a cache refresh when the cookie values change.</span></span>

<span data-ttu-id="7b802-170">В следующем примере показан мониторинг файла cookie, связанного с ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="7b802-170">The following example monitors the cookie associated with ASP.NET Core Identity.</span></span> <span data-ttu-id="7b802-171">При проверке подлинности пользователя изменение Identity файла cookie инициирует обновление кэша:</span><span class="sxs-lookup"><span data-stu-id="7b802-171">When a user is authenticated, a change in the Identity cookie triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="7b802-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="7b802-172">vary-by-user</span></span>

| <span data-ttu-id="7b802-173">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-173">Attribute Type</span></span>  | <span data-ttu-id="7b802-174">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-174">Examples</span></span>        | <span data-ttu-id="7b802-175">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7b802-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="7b802-176">Логическое</span><span class="sxs-lookup"><span data-stu-id="7b802-176">Boolean</span></span>         | <span data-ttu-id="7b802-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="7b802-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="7b802-178">`vary-by-user` указывает, следует ли сбрасывать кэш при изменении вошедшего в систему пользователя (или участника контекста).</span><span class="sxs-lookup"><span data-stu-id="7b802-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="7b802-179">Текущий пользователь также известен как участник контекста запроса, и его можно просмотреть в Razor представлении, обратившись к `@User.Identity.Name` .</span><span class="sxs-lookup"><span data-stu-id="7b802-179">The current user is also known as the Request Context Principal and can be viewed in a Razor view by referencing `@User.Identity.Name`.</span></span>

<span data-ttu-id="7b802-180">В следующем примере отслеживается текущий вошедший пользователь для активации обновления кэша:</span><span class="sxs-lookup"><span data-stu-id="7b802-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="7b802-181">При использовании этого атрибута содержимое сохраняется в кэше в течение цикла входа и выхода.</span><span class="sxs-lookup"><span data-stu-id="7b802-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="7b802-182">Если присвоено значение `true`, цикл проверки подлинности делает недействительным кэш для прошедшего проверку подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="7b802-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="7b802-183">Кэш становится недействительным, так как при проверке подлинности создается уникальное значение файла cookie.</span><span class="sxs-lookup"><span data-stu-id="7b802-183">The cache is invalidated because a new unique cookie value is generated when a user is authenticated.</span></span> <span data-ttu-id="7b802-184">Кэш сохраняется в состоянии анонимности, когда значение cookie не существует или срок его действия истек.</span><span class="sxs-lookup"><span data-stu-id="7b802-184">Cache is maintained for the anonymous state when no cookie is present or the cookie has expired.</span></span> <span data-ttu-id="7b802-185">Если пользователь **не** прошел проверку подлинности, кэш сохраняется.</span><span class="sxs-lookup"><span data-stu-id="7b802-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="7b802-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="7b802-186">vary-by</span></span>

| <span data-ttu-id="7b802-187">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-187">Attribute Type</span></span> | <span data-ttu-id="7b802-188">Пример</span><span class="sxs-lookup"><span data-stu-id="7b802-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="7b802-189">Строка</span><span class="sxs-lookup"><span data-stu-id="7b802-189">String</span></span>         | `@Model` |

<span data-ttu-id="7b802-190">`vary-by` позволяет настраивать, какие данные кэшируются.</span><span class="sxs-lookup"><span data-stu-id="7b802-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="7b802-191">Содержимое вспомогательной функции тегов кэша обновляется при изменении объекта, на который ссылается строковое значение атрибута.</span><span class="sxs-lookup"><span data-stu-id="7b802-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="7b802-192">Часто этому атрибуту назначается объединенная строка значений модели.</span><span class="sxs-lookup"><span data-stu-id="7b802-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="7b802-193">По сути, это приводит к ситуации, когда обновление любого из объединенных значений приводит к сбросу кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="7b802-194">В следующем примере предполагается, что метод контроллера, визуализирующий представление, суммирует целочисленные значения двух параметров маршрута (`myParam1` и `myParam2`) и возвращает сумму как одно свойство модели.</span><span class="sxs-lookup"><span data-stu-id="7b802-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="7b802-195">При изменении этой суммы содержимое вспомогательной функции тегов кэша визуализируется и кэшируется заново.</span><span class="sxs-lookup"><span data-stu-id="7b802-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="7b802-196">Действие:</span><span class="sxs-lookup"><span data-stu-id="7b802-196">Action:</span></span>

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

<span data-ttu-id="7b802-197">*Index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="7b802-197">*Index.cshtml*:</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="7b802-198">priority</span><span class="sxs-lookup"><span data-stu-id="7b802-198">priority</span></span>

| <span data-ttu-id="7b802-199">Тип атрибута</span><span class="sxs-lookup"><span data-stu-id="7b802-199">Attribute Type</span></span>      | <span data-ttu-id="7b802-200">Примеры</span><span class="sxs-lookup"><span data-stu-id="7b802-200">Examples</span></span>                               | <span data-ttu-id="7b802-201">Значение по умолчанию</span><span class="sxs-lookup"><span data-stu-id="7b802-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="7b802-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="7b802-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="7b802-203">`priority` предоставляет встроенному поставщику кэша инструкции по удалению кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="7b802-204">При нехватке памяти веб-сервер будет первыми удалять записи кэша с приоритетом `Low`.</span><span class="sxs-lookup"><span data-stu-id="7b802-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="7b802-205">Пример.</span><span class="sxs-lookup"><span data-stu-id="7b802-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="7b802-206">Атрибут `priority` не гарантирует определенный уровень периода удержания кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="7b802-207">`CacheItemPriority` носит лишь рекомендательный характер.</span><span class="sxs-lookup"><span data-stu-id="7b802-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="7b802-208">Установка для этого атрибута значения `NeverRemove` не гарантирует постоянное хранение элементов кэша.</span><span class="sxs-lookup"><span data-stu-id="7b802-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="7b802-209">Дополнительные сведения см. в разделе [Дополнительные ресурсы](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="7b802-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="7b802-210">Вспомогательная функция тегов кэша зависит от [службы кэширования в памяти](xref:performance/caching/memory).</span><span class="sxs-lookup"><span data-stu-id="7b802-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="7b802-211">Вспомогательная функция тегов кэша добавляет эту службу, если она не добавлена.</span><span class="sxs-lookup"><span data-stu-id="7b802-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7b802-212">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7b802-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
