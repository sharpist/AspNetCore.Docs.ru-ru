---
title: Применение политики безопасности содержимого для ASP.NET CoreBlazor
author: guardrex
description: Узнайте, как использовать политику безопасности содержимого (CSP) с ASP.NET Core Blazor приложениями для защиты от атак с использованием межсайтовых сценариев (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8615b199373ca856c252b9f843e3635770367e4a
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106394"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="ae447-103">Применение политики безопасности содержимого для ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="ae447-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ae447-104">[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ae447-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ae447-105">[Межсайтовые сценарии (XSS)](xref:security/cross-site-scripting) — это уязвимость безопасности, при которой злоумышленник помещает один или несколько вредоносных клиентских скриптов в отображаемое содержимое приложения.</span><span class="sxs-lookup"><span data-stu-id="ae447-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="ae447-106">Политика безопасности содержимого (CSP) помогает защититься от атак XSS путем формирования обозревателя допустимых:</span><span class="sxs-lookup"><span data-stu-id="ae447-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="ae447-107">Источники загруженного содержимого, включая скрипты, таблицы стилей и изображения.</span><span class="sxs-lookup"><span data-stu-id="ae447-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="ae447-108">Действия, выполняемые страницей, с указанием разрешенных целевых объектов URL-адресов форм.</span><span class="sxs-lookup"><span data-stu-id="ae447-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="ae447-109">Подключаемые модули, которые могут быть загружены.</span><span class="sxs-lookup"><span data-stu-id="ae447-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="ae447-110">Чтобы применить CSP к приложению, разработчик указывает несколько *директив* безопасности содержимого CSP в одном или нескольких `Content-Security-Policy` заголовках или `<meta>` тегах.</span><span class="sxs-lookup"><span data-stu-id="ae447-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="ae447-111">Политики оцениваются браузером во время загрузки страницы.</span><span class="sxs-lookup"><span data-stu-id="ae447-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="ae447-112">Браузер проверяет источники страницы и определяет, соответствуют ли они требованиям директив безопасности содержимого.</span><span class="sxs-lookup"><span data-stu-id="ae447-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="ae447-113">Если директивы политики не выполняются для ресурса, браузер не загружает ресурс.</span><span class="sxs-lookup"><span data-stu-id="ae447-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="ae447-114">Например, рассмотрим политику, которая не позволяет использовать сценарии сторонних разработчиков.</span><span class="sxs-lookup"><span data-stu-id="ae447-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="ae447-115">Если страница содержит `<script>` тег с источником стороннего разработчика в `src` атрибуте, браузер предотвращает загрузку сценария.</span><span class="sxs-lookup"><span data-stu-id="ae447-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="ae447-116">CSP поддерживается в большинстве современных настольных и мобильных браузеров, включая Chrome, ребро, Firefox, Opera и Safari.</span><span class="sxs-lookup"><span data-stu-id="ae447-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="ae447-117">Для приложений рекомендуется использовать CSP Blazor .</span><span class="sxs-lookup"><span data-stu-id="ae447-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="ae447-118">Директивы политики</span><span class="sxs-lookup"><span data-stu-id="ae447-118">Policy directives</span></span>

<span data-ttu-id="ae447-119">Как минимум, укажите следующие директивы и источники для Blazor приложений.</span><span class="sxs-lookup"><span data-stu-id="ae447-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="ae447-120">При необходимости добавьте дополнительные директивы и источники.</span><span class="sxs-lookup"><span data-stu-id="ae447-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="ae447-121">В разделе [применение политики](#apply-the-policy) этой статьи используются следующие директивы, где предоставляются примеры политик безопасности для Blazor сборки и Blazor сервера.</span><span class="sxs-lookup"><span data-stu-id="ae447-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="ae447-122">[base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ограничивают URL-адреса `<base>` тегов страницы.</span><span class="sxs-lookup"><span data-stu-id="ae447-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="ae447-123">Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="ae447-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="ae447-124">[Блокировать все — смешанное содержимое](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): предотвращает загрузку смешанного содержимого HTTP и HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ae447-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="ae447-125">[Default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): указывает резерв для исходных директив, которые не задаются явно политикой.</span><span class="sxs-lookup"><span data-stu-id="ae447-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="ae447-126">Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="ae447-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="ae447-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Указывает допустимые источники для изображений.</span><span class="sxs-lookup"><span data-stu-id="ae447-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="ae447-128">Укажите `data:` , чтобы разрешается загрузка изображений с `data:` URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="ae447-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="ae447-129">Укажите `https:` , чтобы Разрешите загрузку образов из конечных точек HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ae447-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="ae447-130">[Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Указывает допустимые источники для `<object>` `<embed>` тегов, и `<applet>` .</span><span class="sxs-lookup"><span data-stu-id="ae447-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="ae447-131">Укажите `none` , чтобы запретить все источники URL-адресов.</span><span class="sxs-lookup"><span data-stu-id="ae447-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="ae447-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Указывает допустимые источники для скриптов.</span><span class="sxs-lookup"><span data-stu-id="ae447-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="ae447-133">Укажите `https://stackpath.bootstrapcdn.com/` источник узла для скриптов начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="ae447-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="ae447-134">Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="ae447-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="ae447-135">В Blazor приложении сборки:</span><span class="sxs-lookup"><span data-stu-id="ae447-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="ae447-136">Укажите следующие хэши, чтобы предоставить требуемые Blazor Встроенные скрипты сборки для загрузки:</span><span class="sxs-lookup"><span data-stu-id="ae447-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="ae447-137">Укажите, `unsafe-eval` чтобы использовать `eval()` методы и для создания кода из строк.</span><span class="sxs-lookup"><span data-stu-id="ae447-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="ae447-138">В Blazor серверном приложении укажите `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` хэш для встроенного скрипта, который выполняет обнаружение отката для таблиц стилей.</span><span class="sxs-lookup"><span data-stu-id="ae447-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="ae447-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Указывает допустимые источники таблиц стилей.</span><span class="sxs-lookup"><span data-stu-id="ae447-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="ae447-140">Укажите `https://stackpath.bootstrapcdn.com/` источник для таблиц начальной загрузки.</span><span class="sxs-lookup"><span data-stu-id="ae447-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="ae447-141">Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.</span><span class="sxs-lookup"><span data-stu-id="ae447-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="ae447-142">Укажите `unsafe-inline` , чтобы разрешить использование встроенных стилей.</span><span class="sxs-lookup"><span data-stu-id="ae447-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="ae447-143">Встроенное объявление требуется для пользовательского интерфейса в Blazor серверных приложениях для повторного подключения клиента и сервера после первоначального запроса.</span><span class="sxs-lookup"><span data-stu-id="ae447-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="ae447-144">В будущем выпуске встроенные стили могут быть удалены, поэтому `unsafe-inline` они больше не нужны.</span><span class="sxs-lookup"><span data-stu-id="ae447-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="ae447-145">[Обновление — небезопасные запросы](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): указывает, что URL-адреса содержимого из небезопасных (http) источников должны быть безопасно получены по протоколу HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ae447-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="ae447-146">Предыдущие директивы поддерживаются всеми браузерами, кроме Microsoft Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="ae447-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="ae447-147">Для получения хэшей SHA для дополнительных встроенных скриптов:</span><span class="sxs-lookup"><span data-stu-id="ae447-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="ae447-148">Примените CSP, показанный в разделе [применение политики](#apply-the-policy) .</span><span class="sxs-lookup"><span data-stu-id="ae447-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="ae447-149">Доступ к консоли средств разработчика в браузере при локальном запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="ae447-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="ae447-150">Браузер вычисляет и отображает хэши для заблокированных скриптов при наличии заголовка или `meta` ТЕГА CSP.</span><span class="sxs-lookup"><span data-stu-id="ae447-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="ae447-151">Скопируйте хэши, предоставленные браузером, в `script-src` источники.</span><span class="sxs-lookup"><span data-stu-id="ae447-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="ae447-152">Используйте одинарные кавычки вокруг каждого хэша.</span><span class="sxs-lookup"><span data-stu-id="ae447-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="ae447-153">Таблицу поддержки браузера уровня политики безопасности содержимого 2 см. в разделе [можно использовать: уровень политики безопасности содержимого уровня 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="ae447-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="ae447-154">Применение политики</span><span class="sxs-lookup"><span data-stu-id="ae447-154">Apply the policy</span></span>

<span data-ttu-id="ae447-155">`<meta>`Примените политику с помощью тега:</span><span class="sxs-lookup"><span data-stu-id="ae447-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="ae447-156">Присвойте `http-equiv` атрибуту значение `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="ae447-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="ae447-157">Поместите директивы в `content` значение атрибута.</span><span class="sxs-lookup"><span data-stu-id="ae447-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="ae447-158">Разделяйте директивы точкой с запятой ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="ae447-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="ae447-159">Всегда помещайте `meta` тег в `<head>` содержимое.</span><span class="sxs-lookup"><span data-stu-id="ae447-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="ae447-160">В следующих разделах показаны примеры политик для Blazor сборки и Blazor сервера.</span><span class="sxs-lookup"><span data-stu-id="ae447-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="ae447-161">Эти примеры имеют версию, описанную в этой статье для каждого выпуска Blazor .</span><span class="sxs-lookup"><span data-stu-id="ae447-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="ae447-162">Чтобы использовать версию, соответствующую выпуску, выберите версию документа с помощью средства выбора версии для раскрывающегося списка **версий** на этой веб-странице.</span><span class="sxs-lookup"><span data-stu-id="ae447-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="ae447-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ae447-163"> WebAssembly</span></span>

<span data-ttu-id="ae447-164">В `<head>` содержимом страницы узла *wwwroot/index.HTML* примените директивы, описанные в разделе [директивы политики](#policy-directives) .</span><span class="sxs-lookup"><span data-stu-id="ae447-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a><span data-ttu-id="ae447-165">Сервер Blazor</span><span class="sxs-lookup"><span data-stu-id="ae447-165">Blazor Server</span></span>

<span data-ttu-id="ae447-166">В `<head>` содержимом страницы узла *pages/_Host. cshtml* примените директивы, описанные в разделе [директивы политики](#policy-directives) .</span><span class="sxs-lookup"><span data-stu-id="ae447-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="ae447-167">Ограничения мета тегов</span><span class="sxs-lookup"><span data-stu-id="ae447-167">Meta tag limitations</span></span>

<span data-ttu-id="ae447-168">`<meta>`Политика тегов не поддерживает следующие директивы:</span><span class="sxs-lookup"><span data-stu-id="ae447-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="ae447-169">кадр — предки</span><span class="sxs-lookup"><span data-stu-id="ae447-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="ae447-170">отчет — для</span><span class="sxs-lookup"><span data-stu-id="ae447-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="ae447-171">URI отчета</span><span class="sxs-lookup"><span data-stu-id="ae447-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="ae447-172">Обезвреженные</span><span class="sxs-lookup"><span data-stu-id="ae447-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="ae447-173">Для поддержки предыдущих директив используйте заголовок с именем `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="ae447-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="ae447-174">Строка директивы — это значение заголовка.</span><span class="sxs-lookup"><span data-stu-id="ae447-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="ae447-175">Тестирование политики и получение отчетов о нарушениях</span><span class="sxs-lookup"><span data-stu-id="ae447-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="ae447-176">Тестирование помогает убедиться в том, что сценарии сторонних разработчиков случайно не блокируются при создании начальной политики.</span><span class="sxs-lookup"><span data-stu-id="ae447-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="ae447-177">Чтобы протестировать политику в течение определенного периода времени без применения директив политики, задайте `<meta>` `http-equiv` для атрибута тега или заголовка политики на основе заголовков значение `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="ae447-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="ae447-178">Отчеты о сбоях отправляются как документы JSON по указанному URL-адресу.</span><span class="sxs-lookup"><span data-stu-id="ae447-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="ae447-179">Дополнительные сведения см. в статье [MDN Web документация: Content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="ae447-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="ae447-180">Сведения о нарушениях, когда политика активна, см. в следующих статьях:</span><span class="sxs-lookup"><span data-stu-id="ae447-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="ae447-181">отчет — для</span><span class="sxs-lookup"><span data-stu-id="ae447-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="ae447-182">URI отчета</span><span class="sxs-lookup"><span data-stu-id="ae447-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="ae447-183">Хотя `report-uri` больше не рекомендуется использовать, обе директивы следует использовать до тех пор, пока `report-to` не будет поддерживаться всеми основными браузерами.</span><span class="sxs-lookup"><span data-stu-id="ae447-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="ae447-184">Не используйте монопольное использование `report-uri` , так как поддержка `report-uri` может быть удалена *в любое время* из браузеров.</span><span class="sxs-lookup"><span data-stu-id="ae447-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="ae447-185">Удалите поддержку `report-uri` в политиках, если `report-to` она полностью поддерживается.</span><span class="sxs-lookup"><span data-stu-id="ae447-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="ae447-186">Сведения об отслеживании внедрения см. `report-to` [в разделе можно использовать: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="ae447-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="ae447-187">Протестируйте и обновляйте политику приложения в каждом выпуске.</span><span class="sxs-lookup"><span data-stu-id="ae447-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ae447-188">Диагностика</span><span class="sxs-lookup"><span data-stu-id="ae447-188">Troubleshoot</span></span>

* <span data-ttu-id="ae447-189">Ошибки отображаются в консоли средств разработчика браузера.</span><span class="sxs-lookup"><span data-stu-id="ae447-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="ae447-190">Браузеры предоставляют следующие сведения:</span><span class="sxs-lookup"><span data-stu-id="ae447-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="ae447-191">Элементы, которые не соответствуют политике.</span><span class="sxs-lookup"><span data-stu-id="ae447-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="ae447-192">Изменение политики для разрешения заблокированного элемента.</span><span class="sxs-lookup"><span data-stu-id="ae447-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="ae447-193">Политика полностью эффективна, только если браузер клиента поддерживает все включенные директивы.</span><span class="sxs-lookup"><span data-stu-id="ae447-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="ae447-194">Сведения о текущей матрице поддержки браузера см. в разделе [можно использовать: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="ae447-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ae447-195">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ae447-195">Additional resources</span></span>

* [<span data-ttu-id="ae447-196">Веб-документы MDN: безопасность содержимого — политика</span><span class="sxs-lookup"><span data-stu-id="ae447-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="ae447-197">Уровень политики безопасности содержимого 2</span><span class="sxs-lookup"><span data-stu-id="ae447-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="ae447-198">Оценщик Google CSP</span><span class="sxs-lookup"><span data-stu-id="ae447-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
