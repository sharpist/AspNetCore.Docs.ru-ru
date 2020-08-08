---
title: Вспомогательная функция тега среды в ASP.NET Core
author: pkellner
description: Определенная в ASP.NET Core вспомогательная функция тега среды, включая все свойства
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 01fa5409136557dcbf53dbdd051bf35803e8b71f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018654"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="8f629-103">Вспомогательная функция тега среды в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8f629-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="8f629-104">Авторы: [Питер Кельнер (Peter Kellner)](https://peterkellner.net) и [Хишам Бин Атея](https://twitter.com/hishambinateya) (Hisham Bin Ateya)</span><span class="sxs-lookup"><span data-stu-id="8f629-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="8f629-105">Вспомогательная функция тега среды условно визуализирует свое содержимое в соответствии с текущей [средой размещения](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="8f629-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="8f629-106">Единственный атрибут вспомогательной функции тега среды, `names`, — это разделенный запятыми список имен сред.</span><span class="sxs-lookup"><span data-stu-id="8f629-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="8f629-107">Если одно из указанных имен среды соответствует текущей среде, включенное содержимое подготавливается к просмотру.</span><span class="sxs-lookup"><span data-stu-id="8f629-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="8f629-108">Общие сведения о вспомогательных функциях тегов см. здесь: <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="8f629-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="8f629-109">Атрибуты вспомогательной функции тега среды</span><span class="sxs-lookup"><span data-stu-id="8f629-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="8f629-110">имена</span><span class="sxs-lookup"><span data-stu-id="8f629-110">names</span></span>

<span data-ttu-id="8f629-111">`names` принимает одно имя среды размещения или список разделенных запятыми имен сред размещения, которые запускают отрисовку включенного в функцию содержимого.</span><span class="sxs-lookup"><span data-stu-id="8f629-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="8f629-112">Значения среды сравниваются с текущим значением, возвращенным [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="8f629-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="8f629-113">При сравнении регистр не учитывается.</span><span class="sxs-lookup"><span data-stu-id="8f629-113">The comparison ignores case.</span></span>

<span data-ttu-id="8f629-114">В приведенном ниже примере используется вспомогательная функция тега среды.</span><span class="sxs-lookup"><span data-stu-id="8f629-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="8f629-115">Содержимое подготавливается к просмотру в том случае, если среда размещения является промежуточной или рабочей:</span><span class="sxs-lookup"><span data-stu-id="8f629-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="8f629-116">Атрибуты include и exclude</span><span class="sxs-lookup"><span data-stu-id="8f629-116">include and exclude attributes</span></span>

<span data-ttu-id="8f629-117">`include`& `exclude` элемент управления атрибутами подготовит содержимое, основанное на включенных или исключенных именах среды размещения.</span><span class="sxs-lookup"><span data-stu-id="8f629-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="8f629-118">include</span><span class="sxs-lookup"><span data-stu-id="8f629-118">include</span></span>

<span data-ttu-id="8f629-119">Свойство `include` ведет себя так же для атрибута `names`.</span><span class="sxs-lookup"><span data-stu-id="8f629-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="8f629-120">Среда, указанная в значении атрибута `include`, должна соответствовать среде размещения приложения ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) для отрисовки содержимого тега `<environment>`.</span><span class="sxs-lookup"><span data-stu-id="8f629-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="8f629-121">исключение</span><span class="sxs-lookup"><span data-stu-id="8f629-121">exclude</span></span>

<span data-ttu-id="8f629-122">В отличие от атрибута `include`, содержимое тега `<environment>` отрисовывается, когда среда размещения не соответствует среде, указанной в значении атрибута `exclude`.</span><span class="sxs-lookup"><span data-stu-id="8f629-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8f629-123">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="8f629-123">Additional resources</span></span>

* <xref:fundamentals/environments>
