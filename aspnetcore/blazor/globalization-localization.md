---
title: Глобализация и локализация в ASP.NET Core Blazor
author: guardrex
description: Узнайте, как сделать компоненты Razor доступными для пользователей с различными языковыми и региональными параметрами.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 1b0db66b23c0caffc6b7c4e4af723c020609612a
ms.sourcegitcommit: d5d45d84fe488427d418de770000f7df44a08370
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81539665"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="9419e-103">Глобализация и локализация в ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9419e-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="9419e-104">Авторы: [Люк Латэм](https://github.com/guardrex) (Luke Latham) и [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth)</span><span class="sxs-lookup"><span data-stu-id="9419e-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9419e-105">Компоненты Razor можно сделать доступными для пользователей с различными языковыми и региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="9419e-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="9419e-106">Доступны следующие сценарии глобализации и локализации .NET:</span><span class="sxs-lookup"><span data-stu-id="9419e-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="9419e-107">система ресурсов NET;</span><span class="sxs-lookup"><span data-stu-id="9419e-107">.NET's resources system</span></span>
* <span data-ttu-id="9419e-108">форматирование чисел и дат в зависимости от языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="9419e-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="9419e-109">В настоящее время поддерживается ограниченный набор сценариев локализации ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9419e-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="9419e-110">`IStringLocalizer<>` *поддерживается* в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="9419e-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="9419e-111">`IHtmlLocalizer<>`, `IViewLocalizer<>` и локализация заметок к данным являются сценариями MVC ASP.NET Core и **не поддерживается** в приложениях Blazor.</span><span class="sxs-lookup"><span data-stu-id="9419e-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="9419e-112">Для получения дополнительной информации см. <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="9419e-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="9419e-113">Глобализация</span><span class="sxs-lookup"><span data-stu-id="9419e-113">Globalization</span></span>

<span data-ttu-id="9419e-114">Функция `@bind` в Blazor выполняет форматирование и синтаксический анализ значений, отображаемых на основе текущего языка и региональных параметров пользователя.</span><span class="sxs-lookup"><span data-stu-id="9419e-114">Blazor's `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="9419e-115">Доступ к текущему языку и региональным параметрам можно получить из свойства <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="9419e-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="9419e-116">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) используется для следующих типов полей (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="9419e-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="9419e-117">Предыдущие типы полей:</span><span class="sxs-lookup"><span data-stu-id="9419e-117">The preceding field types:</span></span>

* <span data-ttu-id="9419e-118">отображаются с использованием соответствующих правил форматирования браузера;</span><span class="sxs-lookup"><span data-stu-id="9419e-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="9419e-119">не могут содержать текст в свободной форме;</span><span class="sxs-lookup"><span data-stu-id="9419e-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="9419e-120">предоставляют характеристики взаимодействия с пользователем в зависимости от реализации браузера.</span><span class="sxs-lookup"><span data-stu-id="9419e-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="9419e-121">Следующие типы полей имеют особые требования к форматированию, которые в настоящее время не поддерживаются в Blazor, так как они не поддерживаются всеми основными браузерами.</span><span class="sxs-lookup"><span data-stu-id="9419e-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="9419e-122">`@bind` поддерживает параметр `@bind:culture` для предоставления <xref:System.Globalization.CultureInfo?displayProperty=fullName> для анализа и форматирования значения.</span><span class="sxs-lookup"><span data-stu-id="9419e-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="9419e-123">Указание языка и региональных параметров не рекомендуется при использовании типов полей `date` и `number`.</span><span class="sxs-lookup"><span data-stu-id="9419e-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="9419e-124">Типы `date` и `number` имеют встроенную поддержку Blazor, которая обеспечивает поддержку языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="9419e-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="9419e-125">Локализация</span><span class="sxs-lookup"><span data-stu-id="9419e-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="9419e-126"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9419e-126"> WebAssembly</span></span>

<span data-ttu-id="9419e-127">Приложения Blazor WebAssembly задают язык и региональные параметры с помощью [предпочитаемого языка пользователя](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span><span class="sxs-lookup"><span data-stu-id="9419e-127">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="9419e-128">Чтобы явно настроить язык и региональные параметры, задайте `CultureInfo.DefaultThreadCurrentCulture` и `CultureInfo.DefaultThreadCurrentUICulture` в `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="9419e-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="9419e-129">По умолчанию конфигурация компоновщика Blazor для приложений Blazor WebAssembly исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов.</span><span class="sxs-lookup"><span data-stu-id="9419e-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="9419e-130">Дополнительные сведения и рекомендации по управлению поведением компоновщика см. в разделе <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="9419e-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="9419e-131">Хотя язык и региональные параметры, выбираемые Blazor по умолчанию, могут быть достаточными для большинства пользователей, рекомендуется предоставить пользователям возможность указать предпочитаемый языковой стандарт.</span><span class="sxs-lookup"><span data-stu-id="9419e-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="9419e-132">Пример приложения Blazor WebAssembly с выбором языка и региональных параметров см. в описании примера приложения локализации [LocSample](https://github.com/pranavkm/LocSample).</span><span class="sxs-lookup"><span data-stu-id="9419e-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="opno-locblazor-server"></a><span data-ttu-id="9419e-133">Сервер Blazor</span><span class="sxs-lookup"><span data-stu-id="9419e-133">Blazor Server</span></span>

<span data-ttu-id="9419e-134">Приложения Blazor Server локализуются с помощью [промежуточного слоя локализации](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="9419e-134">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="9419e-135">ПО промежуточного слоя выбирает соответствующие языки и региональные параметры для пользователей, запрашивающих ресурсы из приложения.</span><span class="sxs-lookup"><span data-stu-id="9419e-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="9419e-136">Язык и региональные параметры можно задать с помощью одного из следующих подходов.</span><span class="sxs-lookup"><span data-stu-id="9419e-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="9419e-137">Файлы "cookie"</span><span class="sxs-lookup"><span data-stu-id="9419e-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="9419e-138">Предоставление пользовательского интерфейса для выбора языка и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="9419e-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="9419e-139">Дополнительные сведения и примеры см. в разделе <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="9419e-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="9419e-140">Файлы cookie</span><span class="sxs-lookup"><span data-stu-id="9419e-140">Cookies</span></span>

<span data-ttu-id="9419e-141">Файл cookie локализации может запоминать язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="9419e-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="9419e-142">Файл cookie создается методом `OnGet` на странице host приложения (*Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="9419e-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="9419e-143">ПО промежуточного слоя локализации считывает файл cookie при последующих запросах, чтобы задать язык и региональные параметры пользователя.</span><span class="sxs-lookup"><span data-stu-id="9419e-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="9419e-144">Использование файла cookie гарантирует, что соединение WebSocket сможет правильно распространить языковые и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="9419e-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="9419e-145">Если схема локализации основана на URL-адресе или строке запроса, она может не поддерживать работу с WebSockets и не сможет сохранить язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="9419e-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="9419e-146">Поэтому рекомендуемым подходом для локализации является использование файла cookie языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="9419e-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="9419e-147">Если язык и региональные параметры сохраняются в файле cookie с локализацией, то можно использовать любой метод для назначения языка и региональных параметров.</span><span class="sxs-lookup"><span data-stu-id="9419e-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="9419e-148">Если у приложения уже есть установленная схема локализации для ASP.NET Core на стороне сервера, продолжайте использовать существующую инфраструктуру локализации приложения и задавайте файл cookie языка и региональных параметров для локализации в схеме приложения.</span><span class="sxs-lookup"><span data-stu-id="9419e-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="9419e-149">В следующем примере показано, как задать текущий язык и региональные параметры в файле cookie, который можно прочитать в ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="9419e-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="9419e-150">Создайте файл *Pages/_Host.cshtml.cs* со следующим содержимым в приложении Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="9419e-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="9419e-151">Локализация обрабатывается приложением в следующей последовательности событий.</span><span class="sxs-lookup"><span data-stu-id="9419e-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="9419e-152">Браузер отправляет первоначальный HTTP-запрос в приложение.</span><span class="sxs-lookup"><span data-stu-id="9419e-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="9419e-153">Язык и региональные параметры назначаются в ПО промежуточного слоя локализации.</span><span class="sxs-lookup"><span data-stu-id="9419e-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="9419e-154">Метод `OnGet` в файле *_Host.cshtml.cs* сохраняет язык и региональные параметры в файле cookie как часть ответа.</span><span class="sxs-lookup"><span data-stu-id="9419e-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="9419e-155">Браузер открывает соединение WebSocket для создания интерактивного сеанса Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="9419e-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="9419e-156">ПО промежуточного слоя для локализации считывает файл cookie и назначает язык и региональные параметры.</span><span class="sxs-lookup"><span data-stu-id="9419e-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="9419e-157">Сеанс Blazor Server начинается с нужными языком и региональными параметрами.</span><span class="sxs-lookup"><span data-stu-id="9419e-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="9419e-158">Предоставление пользовательского интерфейса для выбора языка и региональных параметров</span><span class="sxs-lookup"><span data-stu-id="9419e-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="9419e-159">Чтобы предоставить пользовательский интерфейс, позволяющий пользователю выбрать язык и региональные параметры, рекомендуется *подход на основе перенаправления*.</span><span class="sxs-lookup"><span data-stu-id="9419e-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="9419e-160">Процесс аналогичен тому, что происходит в веб-приложении, когда пользователь пытается получить доступ к защищенному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="9419e-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="9419e-161">Пользователь перенаправляется на страницу входа, а затем обратно к исходному ресурсу.</span><span class="sxs-lookup"><span data-stu-id="9419e-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="9419e-162">Приложение сохраняет выбранный язык и региональные параметры пользователя с помощью перенаправления к контроллеру.</span><span class="sxs-lookup"><span data-stu-id="9419e-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="9419e-163">Контроллер устанавливает выбранный язык и региональные параметры пользователя в файл cookie и перенаправляет пользователя обратно в исходный URI.</span><span class="sxs-lookup"><span data-stu-id="9419e-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="9419e-164">Установите конечную точку HTTP на сервере, чтобы задать выбранный язык и региональные параметры пользователя в файле cookie, и выполните перенаправление обратно в исходный URI:</span><span class="sxs-lookup"><span data-stu-id="9419e-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="9419e-165">Для предотвращения атак с открытым перенаправлением используйте результат действия `LocalRedirect`.</span><span class="sxs-lookup"><span data-stu-id="9419e-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="9419e-166">Для получения дополнительной информации см. <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="9419e-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="9419e-167">В следующем компоненте показан пример выполнения начального перенаправления, когда пользователь выбирает язык и региональные параметры:</span><span class="sxs-lookup"><span data-stu-id="9419e-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="9419e-168">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="9419e-168">Additional resources</span></span>

* <xref:fundamentals/localization>
