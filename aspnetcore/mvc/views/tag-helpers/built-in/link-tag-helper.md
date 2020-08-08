---
title: Вспомогательная функция тега ссылки в ASP.NET Core
author: rick-anderson
ms.author: riande
description: Обнаруживайте атрибуты вспомогательной функции тега ссылки ASP.NET Core и роль, которую играет каждый атрибут в расширении поведения тега ссылки HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7487bc64f8e20f85ef95e5389409c0194ec31337
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018563"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="867cf-103">Вспомогательная функция тега ссылки в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="867cf-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="867cf-104">Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)</span><span class="sxs-lookup"><span data-stu-id="867cf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="867cf-105">[Вспомогательная функция тега ссылки](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) создает ссылку на первичный или резервный файл CSS.</span><span class="sxs-lookup"><span data-stu-id="867cf-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="867cf-106">Обычно первичный файл CSS находится в [сети доставки содержимого](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="867cf-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="867cf-107">Вспомогательная функция тега ссылки позволяет указать CDN для файла CSS и резервную копию, если CDN недоступен.</span><span class="sxs-lookup"><span data-stu-id="867cf-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="867cf-108">Вспомогательная функция тега ссылки обеспечивает высокую производительность CDN с надежностью локального размещения.</span><span class="sxs-lookup"><span data-stu-id="867cf-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="867cf-109">В следующей Razor разметке показан `head` элемент файла макета, созданного с помощью шаблона веб-приложения ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="867cf-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="867cf-110">Ниже приведен код HTML из предыдущего кода (в среде, не являющейся средой разработки):</span><span class="sxs-lookup"><span data-stu-id="867cf-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="867cf-111">В приведенном выше коде вспомогательная функция тега ссылки создала элемент `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` и следующий код JavaScript, который используется для проверки наличия запрошенного файла *bootstrap.min.css* в сети CDN.</span><span class="sxs-lookup"><span data-stu-id="867cf-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="867cf-112">В этом случае файл CSS был доступен, поэтому вспомогательная функция тега создала элемент `<link />` с файлом CSS CDN.</span><span class="sxs-lookup"><span data-stu-id="867cf-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="867cf-113">Часто используемые атрибуты вспомогательной функции тега ссылки</span><span class="sxs-lookup"><span data-stu-id="867cf-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="867cf-114">Все атрибуты, свойства и методы вспомогательной функции тега ссылки см. в разделе [LinkTagHelper Class](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) (Класс LinkTagHelper).</span><span class="sxs-lookup"><span data-stu-id="867cf-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="867cf-115">href</span><span class="sxs-lookup"><span data-stu-id="867cf-115">href</span></span>

<span data-ttu-id="867cf-116">Предпочтительный адрес связанного ресурса.</span><span class="sxs-lookup"><span data-stu-id="867cf-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="867cf-117">Адрес передается в созданный код HTML во всех случаях.</span><span class="sxs-lookup"><span data-stu-id="867cf-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="867cf-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="867cf-118">asp-fallback-href</span></span>

<span data-ttu-id="867cf-119">URL-адрес таблицы стилей CSS, к которой можно перейти в случае сбоя основного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="867cf-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="867cf-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="867cf-120">asp-fallback-test-class</span></span>

<span data-ttu-id="867cf-121">Имя класса, определенное в таблице стилей для использования в качестве теста резервного экземпляра.</span><span class="sxs-lookup"><span data-stu-id="867cf-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="867cf-122">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="867cf-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="867cf-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="867cf-123">asp-fallback-test-property</span></span>

<span data-ttu-id="867cf-124">Имя свойства CSS, используемое для теста резервного экземпляра.</span><span class="sxs-lookup"><span data-stu-id="867cf-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="867cf-125">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="867cf-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="867cf-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="867cf-126">asp-fallback-test-value</span></span>

<span data-ttu-id="867cf-127">Значение свойства CSS, используемое для теста резервного экземпляра.</span><span class="sxs-lookup"><span data-stu-id="867cf-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="867cf-128">Для получения дополнительной информации см. <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="867cf-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="867cf-129">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="867cf-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
