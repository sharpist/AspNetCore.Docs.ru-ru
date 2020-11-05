---
title: 'Глобализация и локализация в ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Узнайте, как сделать компоненты :::no-loc(Razor)::: доступными для пользователей с различными языковыми и региональными параметрами.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/globalization-localization
ms.openlocfilehash: f8f261f25d854a9bf36ad3299f4af392d5c4fafd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055884"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="e6fcd-103">Глобализация и локализация в ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="e6fcd-103">ASP.NET Core :::no-loc(Blazor)::: globalization and localization</span></span>

<span data-ttu-id="e6fcd-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="e6fcd-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e6fcd-105">Компоненты :::no-loc(Razor)::: можно сделать доступными для пользователей с различными языковыми и региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-105">:::no-loc(Razor)::: components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="e6fcd-106">Доступны следующие сценарии глобализации и локализации .NET:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="e6fcd-107">система ресурсов NET;</span><span class="sxs-lookup"><span data-stu-id="e6fcd-107">.NET's resources system</span></span>
* <span data-ttu-id="e6fcd-108">форматирование чисел и дат в зависимости от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="e6fcd-109">В настоящее время поддерживается ограниченный набор сценариев локализации ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="e6fcd-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> и <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *поддерживаются* в приложениях :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="e6fcd-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> и локализация заметок к данным являются сценариями MVC ASP.NET Core и **не поддерживается** в приложениях :::no-loc(Blazor):::.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in :::no-loc(Blazor)::: apps.</span></span>

<span data-ttu-id="e6fcd-112">Для получения дополнительной информации см. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="e6fcd-113">Глобализация</span><span class="sxs-lookup"><span data-stu-id="e6fcd-113">Globalization</span></span>

<span data-ttu-id="e6fcd-114">Функция [`@bind`](xref:mvc/views/razor#bind) в :::no-loc(Blazor)::: выполняет форматирование и синтаксический анализ значений, отображаемых на основе текущего языка и региональных параметров пользователя.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-114">:::no-loc(Blazor):::'s [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="e6fcd-115">Доступ к текущему языку и региональным параметрам можно получить из свойства <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="e6fcd-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> используется для следующих типов полей (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="e6fcd-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="e6fcd-117">Предыдущие типы полей:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-117">The preceding field types:</span></span>

* <span data-ttu-id="e6fcd-118">отображаются с использованием соответствующих правил форматирования браузера;</span><span class="sxs-lookup"><span data-stu-id="e6fcd-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="e6fcd-119">не могут содержать текст в свободной форме;</span><span class="sxs-lookup"><span data-stu-id="e6fcd-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="e6fcd-120">предоставляют характеристики взаимодействия с пользователем в зависимости от реализации браузера.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="e6fcd-121">Следующие типы полей имеют особые требования к форматированию, которые в настоящее время не поддерживаются в :::no-loc(Blazor):::, так как они не поддерживаются всеми основными браузерами.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-121">The following field types have specific formatting requirements and aren't currently supported by :::no-loc(Blazor)::: because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="e6fcd-122">[`@bind`](xref:mvc/views/razor#bind) поддерживает параметр `@bind:culture`, чтобы предоставлять <xref:System.Globalization.CultureInfo?displayProperty=fullName> для анализа и форматирования значения.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="e6fcd-123">Указание языка и региональных параметров не рекомендуется при использовании типов полей `date` и `number`.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="e6fcd-124">Типы `date` и `number` имеют встроенную поддержку :::no-loc(Blazor):::, которая обеспечивает поддержку языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-124">`date` and `number` have built-in :::no-loc(Blazor)::: support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="e6fcd-125">Локализация</span><span class="sxs-lookup"><span data-stu-id="e6fcd-125">Localization</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="e6fcd-126">Приложения :::no-loc(Blazor WebAssembly)::: задают язык и региональные параметры с помощью [предпочитаемого языка пользователя](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span><span class="sxs-lookup"><span data-stu-id="e6fcd-126">:::no-loc(Blazor WebAssembly)::: apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="e6fcd-127">Чтобы явно настроить язык и региональные параметры, задайте <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> и <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e6fcd-128">По умолчанию :::no-loc(Blazor WebAssembly)::: содержит минимальные ресурсы глобализации, необходимые для отображения значений, таких как дата и валюта, на языке пользователя и с его региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-128">By default, :::no-loc(Blazor WebAssembly)::: carries minimal globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="e6fcd-129">Для приложений, которые должны поддерживать динамическое изменение языка и региональных параметров, нужно настроить параметр `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-129">Applications that must support dynamically changing the culture should configure `:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData` in the project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>true</:::no-loc(Blazor):::WebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

<span data-ttu-id="e6fcd-130">Вы также можете задать параметры, передаваемыми в `:::no-loc(Blazor):::.start`, чтобы приложение :::no-loc(Blazor WebAssembly)::: запускалось с определенными региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-130">:::no-loc(Blazor WebAssembly)::: can also be configured to launch using a specific application culture using options passed to `:::no-loc(Blazor):::.start`.</span></span> <span data-ttu-id="e6fcd-131">Например, в примере ниже показано, что приложение настроено для запуска с использованием языка и региональных параметров `en-GB`:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-131">For instance, the sample below shows an app configured to launch using the `en-GB` culture:</span></span>

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  :::no-loc(Blazor):::.start({
    applicationCulture: 'en-GB'
  });
</script>
```

<span data-ttu-id="e6fcd-132">Значение параметра `applicationCulture` должно соответствовать [языковому тегу в формате BCP-47](https://tools.ietf.org/html/bcp47).</span><span class="sxs-lookup"><span data-stu-id="e6fcd-132">The value for `applicationCulture` should conform to the [BCP-47 language tag format](https://tools.ietf.org/html/bcp47).</span></span>

<span data-ttu-id="e6fcd-133">Если приложению не требуется локализация, можно настроить поддержку инвариантных языка и региональных параметров, основанных на языке и региональных параметрах `en-US`:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-133">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="e6fcd-134">По умолчанию конфигурация компоновщика промежуточного языка (IL) для приложений :::no-loc(Blazor WebAssembly)::: исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-134">By default, the Intermediate Language (IL) Linker configuration for :::no-loc(Blazor WebAssembly)::: apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="e6fcd-135">Для получения дополнительной информации см. <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-135">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="e6fcd-136">Хотя язык и региональные параметры, выбираемые :::no-loc(Blazor)::: по умолчанию, могут быть достаточными для большинства пользователей, рекомендуется предоставить пользователям возможность указать предпочитаемый языковой стандарт.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-136">While the culture that :::no-loc(Blazor)::: selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="e6fcd-137">Пример приложения :::no-loc(Blazor WebAssembly)::: с выбором языка и региональных параметров см. в описании примера приложения локализации [`LocSample`](https://github.com/pranavkm/LocSample).</span><span class="sxs-lookup"><span data-stu-id="e6fcd-137">For a :::no-loc(Blazor WebAssembly)::: sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="e6fcd-138">Приложения :::no-loc(Blazor Server)::: локализуются с помощью [промежуточного слоя локализации](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="e6fcd-138">:::no-loc(Blazor Server)::: apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="e6fcd-139">ПО промежуточного слоя выбирает соответствующие языки и региональные параметры для пользователей, запрашивающих ресурсы из приложения.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-139">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="e6fcd-140">Язык и региональные параметры можно задать с помощью одного из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-140">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="e6fcd-141">[Файлы :::no-loc(Cookie):::](#:::no-loc(cookie):::s)</span><span class="sxs-lookup"><span data-stu-id="e6fcd-141">[:::no-loc(Cookie):::s](#:::no-loc(cookie):::s)</span></span>
* [<span data-ttu-id="e6fcd-142">Предоставление пользовательского интерфейса для выбора языка и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="e6fcd-142">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="e6fcd-143">Дополнительные сведения и примеры см. в разделе <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-143">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="e6fcd-144">:::no-loc(Cookie):::s</span><span class="sxs-lookup"><span data-stu-id="e6fcd-144">:::no-loc(Cookie):::s</span></span>

<span data-ttu-id="e6fcd-145">Файл :::no-loc(cookie)::: локализации может запоминать язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-145">A localization culture :::no-loc(cookie)::: can persist the user's culture.</span></span> <span data-ttu-id="e6fcd-146">ПО промежуточного слоя локализации считывает файл :::no-loc(cookie)::: при последующих запросах, чтобы задать язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-146">The Localization Middleware reads the :::no-loc(cookie)::: on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="e6fcd-147">Использование файла :::no-loc(cookie)::: гарантирует, что соединение WebSocket сможет правильно распространить языковые и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-147">Use of a :::no-loc(cookie)::: ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="e6fcd-148">Если схема локализации основана на URL-адресе или строке запроса, она может не поддерживать работу с WebSockets и не сможет сохранить язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-148">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="e6fcd-149">Поэтому рекомендуемым подходом для локализации является использование файла :::no-loc(cookie)::: языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-149">Therefore, use of a localization culture :::no-loc(cookie)::: is the recommended approach.</span></span>

<span data-ttu-id="e6fcd-150">Если язык и региональные параметры сохраняются в файле :::no-loc(cookie)::: с локализацией, то можно использовать любой метод для назначения языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-150">Any technique can be used to assign a culture if the culture is persisted in a localization :::no-loc(cookie):::.</span></span> <span data-ttu-id="e6fcd-151">Если у приложения уже есть установленная схема локализации для ASP.NET Core на стороне сервера, продолжайте использовать существующую инфраструктуру локализации приложения и задавайте файл :::no-loc(cookie)::: языка и региональных параметров для локализации в схеме приложения.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-151">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture :::no-loc(cookie)::: within the app's scheme.</span></span>

<span data-ttu-id="e6fcd-152">В следующем примере показано, как задать текущий язык и региональные параметры в файле :::no-loc(cookie):::, который можно прочитать в ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-152">The following example shows how to set the current culture in a :::no-loc(cookie)::: that can be read by the Localization Middleware.</span></span> <span data-ttu-id="e6fcd-153">Создайте :::no-loc(Razor)::: выражение в файле `Pages/_Host.cshtml` непосредственно в открывающем теге `<body>`:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-153">Create a :::no-loc(Razor)::: expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.:::no-loc(Cookie):::s.Append(
            :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
            :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="e6fcd-154">Локализация обрабатывается приложением в следующей последовательности событий.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-154">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="e6fcd-155">Браузер отправляет первоначальный HTTP-запрос в приложение.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-155">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="e6fcd-156">Язык и региональные параметры назначаются в ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-156">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="e6fcd-157">Выражение :::no-loc(Razor)::: на странице `_Host` (`_Host.cshtml`) сохраняет язык и региональные параметры в файле :::no-loc(cookie)::: как часть ответа.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-157">The :::no-loc(Razor)::: expression in the `_Host` page (`_Host.cshtml`) persists the culture in a :::no-loc(cookie)::: as part of the response.</span></span>
1. <span data-ttu-id="e6fcd-158">Браузер открывает соединение WebSocket для создания интерактивного сеанса :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-158">The browser opens a WebSocket connection to create an interactive :::no-loc(Blazor Server)::: session.</span></span>
1. <span data-ttu-id="e6fcd-159">ПО промежуточного слоя для локализации считывает файл :::no-loc(cookie)::: и назначает язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-159">The Localization Middleware reads the :::no-loc(cookie)::: and assigns the culture.</span></span>
1. <span data-ttu-id="e6fcd-160">Сеанс :::no-loc(Blazor Server)::: начинается с нужными языком и региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-160">The :::no-loc(Blazor Server)::: session begins with the correct culture.</span></span>

<span data-ttu-id="e6fcd-161">При работе с <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page> используйте свойство <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context>:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-161">When working with a <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page>, use the <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.:::no-loc(Razor):::Page.Context> property:</span></span>

```razor
@{
    this.Context.Response.:::no-loc(Cookie):::s.Append(
        :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
        :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="e6fcd-162">Предоставление пользовательского интерфейса для выбора языка и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="e6fcd-162">Provide UI to choose the culture</span></span>

<span data-ttu-id="e6fcd-163">Чтобы предоставить пользовательский интерфейс, позволяющий пользователю выбрать язык и региональные параметры, рекомендуется *подход на основе перенаправления*.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-163">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="e6fcd-164">Процесс аналогичен тому, что происходит в веб-приложении, когда пользователь пытается получить доступ к защищенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-164">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="e6fcd-165">Пользователь перенаправляется на страницу входа, а затем обратно к исходному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-165">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="e6fcd-166">Приложение сохраняет выбранный язык и региональные параметры пользователя с помощью перенаправления к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-166">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="e6fcd-167">Контроллер устанавливает выбранный язык и региональные параметры пользователя в файл :::no-loc(cookie)::: и перенаправляет пользователя обратно в исходный URI.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-167">The controller sets the user's selected culture into a :::no-loc(cookie)::: and redirects the user back to the original URI.</span></span>

<span data-ttu-id="e6fcd-168">Установите конечную точку HTTP на сервере, чтобы задать выбранный язык и региональные параметры пользователя в файле :::no-loc(cookie):::, и выполните перенаправление обратно в исходный URI:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-168">Establish an HTTP endpoint on the server to set the user's selected culture in a :::no-loc(cookie)::: and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.:::no-loc(Cookie):::s.Append(
                :::no-loc(Cookie):::RequestCultureProvider.Default:::no-loc(Cookie):::Name,
                :::no-loc(Cookie):::RequestCultureProvider.Make:::no-loc(Cookie):::Value(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="e6fcd-169">Для предотвращения атак с открытым перенаправлением используйте результат действия <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-169">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="e6fcd-170">Для получения дополнительной информации см. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="e6fcd-170">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="e6fcd-171">Если приложение не настроено для обработки действий контроллера:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-171">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="e6fcd-172">Добавьте службы MVC в коллекцию служб в `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-172">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="e6fcd-173">Добавьте маршрутизацию конечных точек контроллера в `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-173">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.Map:::no-loc(Blazor):::Hub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="e6fcd-174">В следующем компоненте показан пример выполнения начального перенаправления, когда пользователь выбирает язык и региональные параметры:</span><span class="sxs-lookup"><span data-stu-id="e6fcd-174">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="e6fcd-175">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="e6fcd-175">Additional resources</span></span>

* <xref:fundamentals/localization>
