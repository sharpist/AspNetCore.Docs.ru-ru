---
Title: ' применение политики безопасности содержимого для ASP.NET Core Blazor ' author: Description: ' Узнайте, как использовать политику безопасности содержимого (CSP) с Blazor приложениями ASP.NET Core для защиты от атак с использованием межсайтовых сценариев (XSS).
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ИД пользователя "SignalR": 

---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Применение политики безопасности содержимого для ASP.NET CoreBlazor

[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)

[Межсайтовые сценарии (XSS)](xref:security/cross-site-scripting) — это уязвимость безопасности, при которой злоумышленник помещает один или несколько вредоносных клиентских скриптов в отображаемое содержимое приложения. Политика безопасности содержимого (CSP) помогает защититься от атак XSS путем формирования обозревателя допустимых:

* Источники загруженного содержимого, включая скрипты, таблицы стилей и изображения.
* Действия, выполняемые страницей, с указанием разрешенных целевых объектов URL-адресов форм.
* Подключаемые модули, которые могут быть загружены.

Чтобы применить CSP к приложению, разработчик указывает несколько *директив* безопасности содержимого CSP в одном или нескольких `Content-Security-Policy` заголовках или `<meta>` тегах.

Политики оцениваются браузером во время загрузки страницы. Браузер проверяет источники страницы и определяет, соответствуют ли они требованиям директив безопасности содержимого. Если директивы политики не выполняются для ресурса, браузер не загружает ресурс. Например, рассмотрим политику, которая не позволяет использовать сценарии сторонних разработчиков. Если страница содержит `<script>` тег с источником стороннего разработчика в `src` атрибуте, браузер предотвращает загрузку сценария.

CSP поддерживается в большинстве современных настольных и мобильных браузеров, включая Chrome, ребро, Firefox, Opera и Safari. Для приложений рекомендуется использовать CSP Blazor .

## <a name="policy-directives"></a>Директивы политики

Как минимум, укажите следующие директивы и источники для Blazor приложений. При необходимости добавьте дополнительные директивы и источники. В разделе [применение политики](#apply-the-policy) этой статьи используются следующие директивы, где предоставляются примеры политик безопасности для Blazor сборки и Blazor сервера.

* [base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): ограничивают URL-адреса `<base>` тегов страницы. Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.
* [Блокировать все — смешанное содержимое](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): предотвращает загрузку смешанного содержимого HTTP и HTTPS.
* [Default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): указывает резерв для исходных директив, которые не задаются явно политикой. Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Указывает допустимые источники для изображений.
  * Укажите `data:` , чтобы разрешается загрузка изображений с `data:` URL-адресов.
  * Укажите `https:` , чтобы Разрешите загрузку образов из конечных точек HTTPS.
* [Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Указывает допустимые источники для `<object>` `<embed>` тегов, и `<applet>` . Укажите `none` , чтобы запретить все источники URL-адресов.
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Указывает допустимые источники для скриптов.
  * Укажите `https://stackpath.bootstrapcdn.com/` источник узла для скриптов начальной загрузки.
  * Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.
  * В Blazor приложении сборки:
    * Укажите следующие хэши, чтобы предоставить требуемые Blazor Встроенные скрипты сборки для загрузки:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Укажите, `unsafe-eval` чтобы использовать `eval()` методы и для создания кода из строк.
  * В Blazor серверном приложении укажите `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` хэш для встроенного скрипта, который выполняет обнаружение отката для таблиц стилей.
* [Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Указывает допустимые источники таблиц стилей.
  * Укажите `https://stackpath.bootstrapcdn.com/` источник для таблиц начальной загрузки.
  * Укажите `self` , чтобы указать, что источник приложения, включая схему и номер порта, является допустимым источником.
  * Укажите `unsafe-inline` , чтобы разрешить использование встроенных стилей. Встроенное объявление требуется для пользовательского интерфейса в Blazor серверных приложениях для повторного подключения клиента и сервера после первоначального запроса. В будущем выпуске встроенные стили могут быть удалены, поэтому `unsafe-inline` они больше не нужны.
* [Обновление — небезопасные запросы](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): указывает, что URL-адреса содержимого из небезопасных (http) источников должны быть безопасно получены по протоколу HTTPS.

Предыдущие директивы поддерживаются всеми браузерами, кроме Microsoft Internet Explorer.

Для получения хэшей SHA для дополнительных встроенных скриптов:

* Примените CSP, показанный в разделе [применение политики](#apply-the-policy) .
* Доступ к консоли средств разработчика в браузере при локальном запуске приложения. Браузер вычисляет и отображает хэши для заблокированных скриптов при наличии заголовка или `meta` ТЕГА CSP.
* Скопируйте хэши, предоставленные браузером, в `script-src` источники. Используйте одинарные кавычки вокруг каждого хэша.

Таблицу поддержки браузера уровня политики безопасности содержимого 2 см. в разделе [можно использовать: уровень политики безопасности содержимого уровня 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Применение политики

`<meta>`Примените политику с помощью тега:

* Присвойте `http-equiv` атрибуту значение `Content-Security-Policy` .
* Поместите директивы в `content` значение атрибута. Разделяйте директивы точкой с запятой ( `;` ).
* Всегда помещайте `meta` тег в `<head>` содержимое.

В следующих разделах показаны примеры политик для Blazor сборки и Blazor сервера. Эти примеры имеют версию, описанную в этой статье для каждого выпуска Blazor . Чтобы использовать версию, соответствующую выпуску, выберите версию документа с помощью средства выбора версии для раскрывающегося списка **версий** на этой веб-странице.

### <a name="blazor-webassembly"></a>Blazor WebAssembly

В `<head>` содержимом страницы узла *wwwroot/index.HTML* примените директивы, описанные в разделе [директивы политики](#policy-directives) .

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

### <a name="blazor-server"></a>Blazor Server

В `<head>` содержимом страницы узла *pages/_Host. cshtml* примените директивы, описанные в разделе [директивы политики](#policy-directives) .

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

## <a name="meta-tag-limitations"></a>Ограничения мета тегов

`<meta>`Политика тегов не поддерживает следующие директивы:

* [кадр — предки](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [отчет — для](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [URI отчета](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [Обезвреженные](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Для поддержки предыдущих директив используйте заголовок с именем `Content-Security-Policy` . Строка директивы — это значение заголовка.

## <a name="test-a-policy-and-receive-violation-reports"></a>Тестирование политики и получение отчетов о нарушениях

Тестирование помогает убедиться в том, что сценарии сторонних разработчиков случайно не блокируются при создании начальной политики.

Чтобы протестировать политику в течение определенного периода времени без применения директив политики, задайте `<meta>` `http-equiv` для атрибута тега или заголовка политики на основе заголовков значение `Content-Security-Policy-Report-Only` . Отчеты о сбоях отправляются как документы JSON по указанному URL-адресу. Дополнительные сведения см. в статье [MDN Web документация: Content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Сведения о нарушениях, когда политика активна, см. в следующих статьях:

* [отчет — для](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [URI отчета](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Хотя `report-uri` больше не рекомендуется использовать, обе директивы следует использовать до тех пор, пока `report-to` не будет поддерживаться всеми основными браузерами. Не используйте монопольное использование `report-uri` , так как поддержка `report-uri` может быть удалена *в любое время* из браузеров. Удалите поддержку `report-uri` в политиках, если `report-to` она полностью поддерживается. Сведения об отслеживании внедрения см. `report-to` [в разделе можно использовать: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Протестируйте и обновляйте политику приложения в каждом выпуске.

## <a name="troubleshoot"></a>Диагностика

* Ошибки отображаются в консоли средств разработчика браузера. Браузеры предоставляют следующие сведения:
  * Элементы, которые не соответствуют политике.
  * Изменение политики для разрешения заблокированного элемента.
* Политика полностью эффективна, только если браузер клиента поддерживает все включенные директивы. Сведения о текущей матрице поддержки браузера см. в разделе [можно использовать: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Веб-документы MDN: безопасность содержимого — политика](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Уровень политики безопасности содержимого 2](https://www.w3.org/TR/CSP2/)
* [Оценщик Google CSP](https://csp-evaluator.withgoogle.com/)
