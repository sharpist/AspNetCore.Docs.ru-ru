---
title: Новые возможности в ASP.NET Core 3.1
author: rick-anderson
description: Сведения о новых возможностях в ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 92804d168381526100ddb8a368f71d201bd4cad9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407672"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="50bd4-103">Новые возможности в ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="50bd4-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="50bd4-104">В этой статье описываются наиболее важные изменения в ASP.NET Core 3.1 со ссылками на соответствующую документацию.</span><span class="sxs-lookup"><span data-stu-id="50bd4-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="50bd4-105">Поддержка разделяемых классов для компонентов Razor</span><span class="sxs-lookup"><span data-stu-id="50bd4-105">Partial class support for Razor components</span></span>

<span data-ttu-id="50bd4-106">Компоненты Razor теперь создаются как разделяемые классы.</span><span class="sxs-lookup"><span data-stu-id="50bd4-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="50bd4-107">Код компонента Razor можно написать как файл кода программной части, определенный как разделяемый класс, вместо того чтобы определять весь код компонента в одном файле.</span><span class="sxs-lookup"><span data-stu-id="50bd4-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="50bd4-108">Дополнительные сведения см. в разделе [Поддержка разделяемых классов](xref:blazor/components/index#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="50bd4-108">For more information, see [Partial class support](xref:blazor/components/index#partial-class-support).</span></span>

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a><span data-ttu-id="50bd4-109">Вспомогательная функция тега компонента Blazor и передача параметров в компоненты верхнего уровня</span><span class="sxs-lookup"><span data-stu-id="50bd4-109">Blazor Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="50bd4-110">В Blazor с ASP.NET Core 3.0 компоненты отрисовывались как страницы и представления с помощью вспомогательной функции HTML (`Html.RenderComponentAsync`).</span><span class="sxs-lookup"><span data-stu-id="50bd4-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="50bd4-111">В ASP.NET Core 3.1 компонент отрисовывается из страницы или представления с помощью новой вспомогательной функции тега компонента:</span><span class="sxs-lookup"><span data-stu-id="50bd4-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="50bd4-112">Вспомогательная функция HTML по-прежнему поддерживается в ASP.NET Core 3.1, но рекомендуется использовать вспомогательную функцию тега компонента.</span><span class="sxs-lookup"><span data-stu-id="50bd4-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="50bd4-113">Приложения Blazor Server теперь могут передавать параметры в компоненты верхнего уровня во время первоначальной отрисовки.</span><span class="sxs-lookup"><span data-stu-id="50bd4-113">Blazor Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="50bd4-114">Ранее параметры можно было передавать в компонент верхнего уровня только с помощью [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="50bd4-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="50bd4-115">В этом выпуске поддерживается как [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server), так и [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered).</span><span class="sxs-lookup"><span data-stu-id="50bd4-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="50bd4-116">Все указанные значения параметров сериализуются как JSON и включаются в исходный ответ.</span><span class="sxs-lookup"><span data-stu-id="50bd4-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="50bd4-117">Например, компонент `Counter` может предварительно отрисовываться со значением приращения (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="50bd4-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="50bd4-118">Дополнительные сведения см. в разделе [Интеграция компонентов в Razor Pages и приложения MVC](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span><span class="sxs-lookup"><span data-stu-id="50bd4-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="50bd4-119">Поддержка общих очередей в HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="50bd4-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="50bd4-120">[HTTP.sys](xref:fundamentals/servers/httpsys) поддерживает создание анонимных очередей запросов.</span><span class="sxs-lookup"><span data-stu-id="50bd4-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="50bd4-121">В ASP.NET Core 3.1 добавлена возможность создания именованной очереди запросов HTTP.sys или присоединения к существующей очереди.</span><span class="sxs-lookup"><span data-stu-id="50bd4-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="50bd4-122">Создание именованной очереди запросов HTTP.sys или присоединение к ней обеспечивает сценарии, в которых процесс контроллера HTTP.Sys, которому принадлежит очередь, не зависит от процесса прослушивателя.</span><span class="sxs-lookup"><span data-stu-id="50bd4-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="50bd4-123">Такая независимость позволяет сохранять существующие соединения и находящиеся в очереди запросы при перезапуске процесса прослушивателя:</span><span class="sxs-lookup"><span data-stu-id="50bd4-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="50bd4-124">Критические изменения для файлов cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="50bd4-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="50bd4-125">Поведение файлов cookie SameSite изменилось в соответствии с предстоящими изменениями в браузерах.</span><span class="sxs-lookup"><span data-stu-id="50bd4-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="50bd4-126">Это может повлиять на сценарии проверки подлинности, такие как AzureAd, OpenIdConnect или WsFederation.</span><span class="sxs-lookup"><span data-stu-id="50bd4-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="50bd4-127">Для получения дополнительной информации см. <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="50bd4-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a><span data-ttu-id="50bd4-128">Запрет выполнения действий по умолчанию для событий в приложениях Blazor</span><span class="sxs-lookup"><span data-stu-id="50bd4-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="50bd4-129">Используйте атрибут директивы `@on{EVENT}:preventDefault`, чтобы предотвратить выполнение действия по умолчанию для события.</span><span class="sxs-lookup"><span data-stu-id="50bd4-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="50bd4-130">В следующем примере запрещается действие по умолчанию, отображающее символ клавиши в текстовом поле:</span><span class="sxs-lookup"><span data-stu-id="50bd4-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="50bd4-131">Дополнительные сведения см. в разделе [Запрет действий по умолчанию](xref:blazor/components/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="50bd4-131">For more information, see [Prevent default actions](xref:blazor/components/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-blazor-apps"></a><span data-ttu-id="50bd4-132">Остановка распространения событий в приложениях Blazor</span><span class="sxs-lookup"><span data-stu-id="50bd4-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="50bd4-133">Используйте атрибут директивы `@on{EVENT}:stopPropagation`, чтобы остановить распространение событий.</span><span class="sxs-lookup"><span data-stu-id="50bd4-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="50bd4-134">В следующем примере при установке флажка события щелчка мышью из дочернего элемента `<div>` перестают распространяться в родительский элемент `<div>`:</span><span class="sxs-lookup"><span data-stu-id="50bd4-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="50bd4-135">Дополнительные сведения см. в разделе [Отключение распространения событий](xref:blazor/components/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="50bd4-135">For more information, see [Stop event propagation](xref:blazor/components/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-blazor-app-development"></a><span data-ttu-id="50bd4-136">Подробные сведения об ошибках во время разработки приложений Blazor</span><span class="sxs-lookup"><span data-stu-id="50bd4-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="50bd4-137">Если во время разработки приложение Blazor работает неправильно, подробные сведения об ошибках в приложении могут помочь в устранении неполадок.</span><span class="sxs-lookup"><span data-stu-id="50bd4-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="50bd4-138">При возникновении ошибки в приложении Blazor в нижней части экрана отображается золотистая полоска.</span><span class="sxs-lookup"><span data-stu-id="50bd4-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="50bd4-139">Во время разработки из этой полоски можно перейти в консоль браузера, где можно просмотреть исключение.</span><span class="sxs-lookup"><span data-stu-id="50bd4-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="50bd4-140">В рабочей среде эта полоска уведомляет пользователя о том, что произошла ошибка, и рекомендует обновить содержимое окна браузера.</span><span class="sxs-lookup"><span data-stu-id="50bd4-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="50bd4-141">Дополнительные сведения см. в разделе [Подробные сведения об ошибках во время разработки](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="50bd4-141">For more information, see [Detailed errors during development](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).</span></span>
