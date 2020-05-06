---
title: Авторизация на основе представлений в ASP.NET Core MVC
author: rick-anderson
description: В этом документе показано, как внедрить и использовать службу авторизации в представлении ASP.NET Core Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775626"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Авторизация на основе представлений в ASP.NET Core MVC

Разработчик часто хочет показать, скрыть или иным образом изменить пользовательский интерфейс на основе удостоверения текущего пользователя. Доступ к службе авторизации можно получить в представлениях MVC с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection). Чтобы внедрить службу авторизации в Razor представление, используйте `@inject` директиву:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Если вы хотите, чтобы служба авторизации была в любом представлении, `@inject` поместите директиву в файл *_ViewImports. cshtml* каталога *views* . Дополнительные сведения: [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection).

Используйте внедренную службу авторизации для вызова `AuthorizeAsync` точно так же, как при [авторизации на основе ресурсов](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

В некоторых случаях ресурсом будет модель представления. Вызов `AuthorizeAsync` выполняется точно так же, как при [авторизации на основе ресурсов](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

В приведенном выше коде модель передается как ресурс, который следует учитывать при оценке политики.

> [!WARNING]
> Не полагайтесь на переключение видимости элементов пользовательского интерфейса приложения в качестве единственной проверки авторизации. Скрытие элемента пользовательского интерфейса может не полностью препятствовать доступу к связанному действию контроллера. Например, рассмотрим кнопку в предыдущем фрагменте кода. Пользователь может вызвать метод `Edit` действия, если ему известен относительный URL-адрес ресурса — */Document/Edit/1*. По этой причине метод `Edit` действия должен выполнять собственную проверку авторизации.
