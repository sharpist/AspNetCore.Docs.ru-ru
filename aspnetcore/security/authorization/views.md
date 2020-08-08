---
title: Авторизация на основе представлений в ASP.NET Core MVC
author: rick-anderson
description: В этом документе показано, как внедрить и использовать службу авторизации в Razor представлении ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: a9576a48ad6badc5130d89940e4112e69eada1b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021943"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="70e48-103">Авторизация на основе представлений в ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="70e48-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="70e48-104">Разработчик часто хочет показать, скрыть или иным образом изменить пользовательский интерфейс на основе удостоверения текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="70e48-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="70e48-105">Доступ к службе авторизации можно получить в представлениях MVC с помощью [внедрения зависимостей](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="70e48-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="70e48-106">Чтобы внедрить службу авторизации в Razor представление, используйте `@inject` директиву:</span><span class="sxs-lookup"><span data-stu-id="70e48-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="70e48-107">Если вы хотите, чтобы служба авторизации была в любом представлении, поместите `@inject` директиву в файл *_ViewImports. cshtml* каталога *views* .</span><span class="sxs-lookup"><span data-stu-id="70e48-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="70e48-108">Дополнительные сведения: [Внедрение зависимостей в представления](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="70e48-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="70e48-109">Используйте внедренную службу авторизации для вызова `AuthorizeAsync` точно так же, как при [авторизации на основе ресурсов](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="70e48-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="70e48-110">В некоторых случаях ресурсом будет модель представления.</span><span class="sxs-lookup"><span data-stu-id="70e48-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="70e48-111">Вызов `AuthorizeAsync` выполняется точно так же, как при [авторизации на основе ресурсов](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="70e48-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="70e48-112">В приведенном выше коде модель передается как ресурс, который следует учитывать при оценке политики.</span><span class="sxs-lookup"><span data-stu-id="70e48-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="70e48-113">Не полагайтесь на переключение видимости элементов пользовательского интерфейса приложения в качестве единственной проверки авторизации.</span><span class="sxs-lookup"><span data-stu-id="70e48-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="70e48-114">Скрытие элемента пользовательского интерфейса может не полностью препятствовать доступу к связанному действию контроллера.</span><span class="sxs-lookup"><span data-stu-id="70e48-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="70e48-115">Например, рассмотрим кнопку в предыдущем фрагменте кода.</span><span class="sxs-lookup"><span data-stu-id="70e48-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="70e48-116">Пользователь может вызвать `Edit` метод действия, если ему известен относительный URL-адрес ресурса — */Document/Edit/1*.</span><span class="sxs-lookup"><span data-stu-id="70e48-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="70e48-117">По этой причине `Edit` метод действия должен выполнять собственную проверку авторизации.</span><span class="sxs-lookup"><span data-stu-id="70e48-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
