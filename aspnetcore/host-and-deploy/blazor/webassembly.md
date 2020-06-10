---
title: "title: 'Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core' author: guardrex description: Узнайте, как размещать и развертывать приложение Blazor с помощью ASP.NET Core, сетей доставки содержимого (CDN), файловых серверов и GitHub Pages."
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.автор: riande ms.custom: mvc ms.дата: 28.05.2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/28/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/blazor/webassembly
ms.openlocfilehash: 09f74edaa3d1cb0d51e0ce8d0209383885b81f5f
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239390"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a>'Blazor'

'Identity'

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid: host-and-deploy/blazor/webassembly

Размещение и развертывание Blazor WebAssembly с помощью ASP.NET Core

* Авторы: [Люк Лэтем](https://github.com/guardrex) (Luke Latham), [Рэйнер Стропек](https://www.timecockpit.com) (Rainer Stropek), [Дэниэл Рот](https://github.com/danroth27) (Daniel Roth), [Бен Адамс](https://twitter.com/ben_a_adams) (Ben Adams) и [Сафиа Абдалла](https://safia.rocks) (Safia Abdalla). С [моделью размещения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):
* Приложение Blazor, его зависимости и среда выполнения .NET скачиваются в браузере параллельно. Приложение выполняется непосредственно в потоке пользовательского интерфейса браузера.

## <a name="precompression"></a>Поддерживаются следующие стратегии развертывания:

Приложение Blazor обслуживается приложением ASP.NET Core. Эта стратегия описана в разделе [Размещенное развертывание с использованием ASP.NET Core](#hosted-deployment-with-aspnet-core).

* Приложение Blazor помещается на статическом веб-сервере или службе, где .NET не используется, чтобы обслуживать приложение Blazor.
* Эта стратегия рассматривается в разделе [Автономное развертывание](#standalone-deployment), где приводятся сведения о размещении приложения Blazor WebAssembly в качестве дочернего приложения IIS.

Предварительное сжатие

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

При публикации приложения Blazor WebAssembly выходные данные предварительно сжимаются, чтобы уменьшить размер приложения и не сжимать среду выполнения.

## <a name="rewrite-urls-for-correct-routing"></a>Используются следующие алгоритмы сжатия:

[Brotli](https://tools.ietf.org/html/rfc7932) (высший уровень) [Gzip](https://tools.ietf.org/html/rfc1952)

* Чтобы отключить сжатие, добавьте свойство `BlazorEnableCompression` MSBuild в файл проекта приложения и задайте для него значение `false`.
* Сведения о конфигурации сжатия *web.config* IIS см. в разделе [IIS: алгоритмы сжатия Brotli и Gzip](#brotli-and-gzip-compression).

Переопределение URL-адресов для маршрутизации

1. Маршрутизация запросов к компонентам страниц в приложении Blazor WebAssembly не настолько проста, как маршрутизация запросов к приложению, размещенному на сервере Blazor.
1. Рассмотрим приложение Blazor WebAssembly с двумя компонентами:
1. *Main.razor*: загружается в корне приложения и содержит ссылку на компонент `About` (`href="About"`).
1. *About.razor* — компонент `About`.

При запросе документа приложения по умолчанию в адресной строке браузера (например, `https://www.contoso.com/`): Браузер отправляет запрос. Возвращается страница по умолчанию; обычно это *index.html*.

Страница *index.html* обеспечивает начальную загрузку приложения. Загружается маршрутизатор Blazor, и компонент Razor `Main` преобразовывается для просмотра.

На странице Main возможность выбора ссылки на компонент `About` доступна на клиентском компьютере, так как маршрутизатор Blazor не разрешает браузеру сделать запрос в Интернете к `www.contoso.com` для `About` и сам обрабатывает отображенный компонент `About`. Все запросы внутренних конечных точек *в пределах приложения Blazor WebAssembly* будут работать так же: Запросы не активируют браузерные запросы к ресурсам, размещенным на сервере в Интернете.

Маршрутизатор обрабатывает запросы внутренним образом. Если запрос `www.contoso.com/About` сделан с помощью адресной строки браузера, он завершится ошибкой.

## <a name="hosted-deployment-with-aspnet-core"></a>Такой ресурс не существует на интернет-узле приложения, поэтому возвращается ответ *404 — Not Found* (не найдено).

Поскольку браузеры выполняют запросы к интернет-узлам для страниц на стороне клиента, веб-серверам и службам размещения необходимо переопределять все запросы к ресурсам, физически находящимся не на сервере, в случае страницы *index.html*.

Когда *index.html* возвращается, маршрутизатор Blazor приложения берет на себя управление и отвечает необходимым ресурсом. При развертывании на сервере IIS можно использовать модуль переопределения URL-адресов с опубликованным файлом приложения *web.config*. Дополнительные сведения см. в описании [IIS](#iis). Размещенное развертывание с использованием ASP.NET Core

*Размещенное развертывание*  обслуживает приложение Blazor WebAssembly для браузеров из [приложения ASP.NET Core](xref:index), выполняющегося на веб-сервере.

Клиентское приложение Blazor WebAssembly публикуется в папку */bin/Release/{Целевая_платформа}/publish/wwwroot* серверного приложения вместе со всеми прочими статическими веб-ресурсами серверного приложения.

## <a name="standalone-deployment"></a>Оба приложения развертываются вместе.

Веб-сервер, позволяющий размещать приложения ASP.NET Core, является обязательным. Для размещаемого развертывания Visual Studio включает шаблон проекта **приложения Blazor WebAssembly** (шаблон `blazorwasm` при использовании команды [dotnet new](/dotnet/core/tools/dotnet-new)) с выбранным параметром **Hosted** (`-ho|--hosted` при использовании команды `dotnet new`).

Дополнительные сведения о размещении приложения ASP.NET Core и его развертывании см. в разделе <xref:host-and-deploy/index>.

### <a name="azure-app-service"></a>Подробнее о развертывании в службе приложений Azure см. в <xref:tutorials/publish-to-azure-webapp-using-vs>.

Автономное развертывание

*Автономное развертывание* обрабатывает приложение Blazor WebAssembly как набор статических файлов, которые будут запрашиваться непосредственно клиентами. Любой статический файловый сервер способен обслуживать приложение Blazor. Ресурсы автономного развертывания публикуются в папку *bin/Release/{Целевая_платформа}publish/wwwroot*.

### <a name="iis"></a>Служба приложений Azure

Приложения Blazor WebAssembly можно развернуть в службах приложений Azure в Windows. При этом приложение размещается в службах [IIS](#iis). Развертывание автономного приложения Blazor WebAssembly в Службе приложений Azure для Linux в настоящее время не поддерживается.

Сейчас образ сервера Linux для размещения приложения недоступен. Ведется работа над реализацией этого сценария.

#### <a name="webconfig"></a>IIS

IIS можно использовать как полнофункциональный статический файловый сервер для приложений Blazor.

* Чтобы настроить IIS для размещения Blazor, перейдите к разделу [Развертывание статического веб-сайта на IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).
  * Опубликованные ресурсы создаются в папке */bin/Release/{целевая_платформа}/publish*.
  * Разместить содержимое папки *publish* на веб-сервере или в службе размещения.
  * web.config.
  * При публикации проекта Blazor создается файл *web.config* со следующей конфигурацией IIS:
  * Типы MIME заданы для следующих расширений файлов:
* *.dll*: `application/octet-stream`;
  * `application/octet-stream`
  * `application/wasm`
* *.json*: `application/json`;
  * *.wasm*: `application/wasm`;
  * *.woff*: `application/font-woff`;
  
#### <a name="use-a-custom-webconfig"></a>*.woff2*: `application/font-woff`.

Сжатие HTTP включено для следующих типов MIME:

#### <a name="install-the-url-rewrite-module"></a>Задаются правила модуля переопределения URL-адресов:

Предоставление подкаталога для размещения статических ресурсов приложения (*wwwroot/{Запрошенный_путь}* ). Создание резервной маршрутизации для одностраничного приложения, которая позволяет перенаправлять запросы, относящиеся к нефайловым ресурсам, к документу приложения по умолчанию в папке статических ресурсов (*wwwroot/index.html*). Использование пользовательского файла web.config Чтобы применить пользовательский файл *web.config*, поместите пользовательский файл *web.config* в корневую папку проекта и опубликуйте проект.

1. Установка модуля переопределения URL-адресов [Модуль переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite) нужен, чтобы переопределять URL-адреса. Модуль не устанавливается по умолчанию и недоступен для установки как компонент службы роли веб-сервера (IIS).
1. Модуль необходимо скачать с веб-сайта IIS. Для его установки используйте установщик веб-платформы. Локально перейдите на [страницу скачивания модуля переопределения URL-адресов](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). В англоязычной версии выберите **WebPI** для скачивания установщика веб-платформы.

#### <a name="configure-the-website"></a>Для других языков выберите подходящую архитектуру сервера (x86 или x64) для скачивания установщика.

Скопируйте установщик на сервер. Запустите установщик.

* Нажмите кнопку **Установить** и примите условия лицензионного соглашения.
* Перезагрузка сервера после завершения установки не требуется.

#### <a name="host-as-an-iis-sub-app"></a>Настройка веб-сайта

Установите для веб сайта **физический путь** к папке приложения.

* Папка содержит:

  Файл *web.config*, который используется службами IIS для настройки веб-сайта, включая необходимые правила перенаправления и типы содержимого файлов.

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* Папку статических ресурсов приложения.

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

Размещение в качестве дочернего приложения IIS Если автономное приложение размещено как дочернее приложение IIS, выполните одно из следующих действий:

#### <a name="brotli-and-gzip-compression"></a>Отключите наследуемый обработчик модуля ASP.NET Core.

Удалите обработчик в опубликованном файле *web.config* приложения Blazor, добавив раздел `<handlers>` в файл: Отключите наследование раздела `<system.webServer>` от корневого (родительского) приложения, используя `<location>` со значением `false` для `inheritInChildApplications`:

#### <a name="troubleshooting"></a>Удаление обработчика или отключение наследования выполняется вместе с [настройкой базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path).

Задайте базовый путь приложения в файле *index.html* приложения как псевдоним IIS, используемый при настройке дочернего приложения в IIS. Алгоритмы сжатия Brotli и Gzip С помощью файла *web.config* можно настроить IIS для обслуживания ресурсов Blazor, сжатых с помощью алгоритмов Brotli или gzip.

Пример конфигурации см. в файле [web.config](webassembly/_samples/web.config?raw=true).

### <a name="azure-storage"></a>Устранение неполадок

Если получено сообщение *500 — Internal Server Error* (внутренняя ошибка сервера), а диспетчер служб IIS возвращает ошибки при попытке получить доступ к конфигурации веб сайта, убедитесь, что установлен модуль переопределения URL-адресов. Если модуль не установлен, IIS не может проанализировать файл *web.config*.

Это предотвращает загрузку конфигурации веб сайта диспетчером служб IIS и передачу статических файлов Blazor c веб-сайта.

* Дополнительные сведения об устранении неполадок развертывания в службах IIS см. в разделе <xref:test/troubleshoot-azure-iis>.
* Хранилище Azure Размещение статических файлов [службы хранилища Azure](/azure/storage/) предусматривает размещение бессерверного приложения Blazor. Поддерживаются пользовательские доменные имена, сеть доставки содержимого Azure (CDN) и HTTPS. Если служба BLOB-объектов настроена для размещения статических веб-сайтов в учетной записи хранения:

Задайте для параметра **Имя документа индекса** значение `index.html`.

* Задайте для параметра **Путь к документу с ошибкой** значение `index.html`.
* Компоненты Razor и другие конечные точки, не являющиеся файлами, не имеют физических путей в статическом содержимом, хранящемся в службе BLOB-объектов.

  При получении запроса для одного из этих ресурсов, который должен обрабатываться маршрутизатором Blazor, ошибка *404 - Объект не найден*, создаваемая службой BLOB-объектов, перенаправляет запрос, используя **путь к документу с ошибкой**.
  
  1. При этом возвращается большой двоичный объект *index.html*, и маршрутизатор Blazor загружает и обрабатывает путь.
  1. Если файлы не загружаются во время выполнения из-за недопустимых типов MIME в заголовках файлов `Content-Type`, выполните одно из следующих действий:

Настройте средства для установки правильных типов MIME (заголовков `Content-Type`) при развертывании файлов.

### <a name="nginx"></a>Измените типы MIME (заголовки `Content-Type`) для файлов после развертывания приложения.

В Обозревателе службы хранилища (портал Azure) для каждого файла сделайте следующее:

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Щелкните правой кнопкой мыши файл и выберите **Свойства**.

### <a name="nginx-in-docker"></a>Укажите значение для параметра **ContentType** и нажмите кнопку **Сохранить**.

См. подробнее о [размещении статических веб-сайтов в службе хранилища Azure](/azure/storage/blobs/storage-blob-static-website). Nginx

Следующий файл *nginx.conf* упрощен для демонстрации того, как настроить Nginx для отправки файла *Index.html*, когда не удается найти соответствующий файл на диске.

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a>Дополнительные сведения о конфигурации веб-сервера Nginx в рабочей среде см. в разделе [Создание файлов конфигурации NGINX Plus и NGINX](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

Nginx в Docker

1. Чтобы разместить Blazor в Docker с помощью Nginx, настройте в Dockerfile образ с Nginx на основе Alpine. Измените Dockerfile, скопировав файл *nginx.config* в контейнер.

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. Добавьте одну строку в Dockerfile, как показано в следующем примере:

1. Apache

1. Чтобы развернуть приложение Blazor WebAssembly в CentOS 7 или более поздней версии, выполните следующие действия:

Создайте файл конфигурации Apache.

### <a name="github-pages"></a>В следующем примере показан упрощенный файл конфигурации (*blazorapp.config*):

Поместите файл конфигурации Apache в каталог `/etc/httpd/conf.d/`. Это каталог конфигурации Apache по умолчанию в CentOS 7. Поместите файлы приложения в каталог `/var/www/blazorapp` (расположение, указанное в параметре `DocumentRoot` в файле конфигурации). Перезапустите службу Apache.

Дополнительные сведения см. в разделах [mod_mime](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) и [mod_deflate](https://httpd.apache.org/docs/current/mod/mod_deflate.html). GitHub Pages

## <a name="host-configuration-values"></a>Чтобы обеспечить переопределение URL-адресов, добавьте файл *404.html* со сценарием, который производит перенаправление на страницу *index.html*.

Пример реализации, предоставленный сообществом, см. в разделе [Одностраничные приложения для страниц GitHub](https://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages на сайте GitHub](https://github.com/rafrex/spa-github-pages#readme)).

### <a name="content-root"></a>Пример с использованием подхода сообщества можно увидеть в разделе [blazor-demo/blazor-demo.github.io на сайте GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([активный сайт](https://blazor-demo.github.io/)).

При использовании сайта проекта вместо сайта организации следует добавить или обновить тег `<base>` в файле *index.html*. Задайте для имени репозитория GitHub значение атрибута `href` с косой чертой в конце (например, `my-repository/`).

* Значения конфигурации узла [Приложения Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) могут принимать следующие значения конфигурации узла в качестве аргументов командной строки во время выполнения в среде разработки.

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* Корневой каталог содержимого Аргумент `--contentroot` задает абсолютный путь к каталогу, где находятся файлы содержимого приложения ([корневой каталог содержимого](xref:fundamentals/index#content-root)).

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* В следующих примерах `/content-root-path` — это путь к корневому каталогу содержимого приложения. Передайте этот аргумент при локальном запуске приложения в командной строке.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Перейдите в каталог приложения и выполните следующую команду:

Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**. Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`. В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.

> [!IMPORTANT]
> Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*. Базовый путь

* Аргумент `--pathbase` задает базовый путь приложения для локального выполнения с некорневым относительным путем URL (в `<base>` тег `href` задан с путем, отличным от `/` для промежуточной и рабочей сред). В следующих примерах `/relative-URL-path` — это базовый путь приложения.

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* Дополнительные сведения см. в описании [базового пути приложения](xref:host-and-deploy/blazor/index#app-base-path). В отличие от пути, заданного через `href` в теге `<base>`, не ставьте косую черту (`/`) при передаче значения аргумента `--pathbase`.

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* Если основной путь приложения задан в теге `<base>` как `<base href="/CoolApp/">` (включая косую черту в конце), передайте значение аргумента командной строки как `--pathbase=/CoolApp` (без косой черты в конце). Передайте этот аргумент при локальном запуске приложения в командной строке.

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a>Перейдите в каталог приложения и выполните следующую команду:

Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**.

* Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`. В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*. URL-адреса

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Аргумент `--urls` задает IP-адреса или адреса узлов с портами и протоколами, по которым ожидаются запросы. Передайте этот аргумент при локальном запуске приложения в командной строке.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="configure-the-linker"></a>Перейдите в каталог приложения и выполните следующую команду:

Добавьте запись в файл приложения *launchSettings.json* в профиле **IIS Express**. Этот параметр используется при запуске приложения с помощью отладчика Visual Studio и из командной строки вместе с `dotnet run`.

## <a name="custom-boot-resource-loading"></a>В Visual Studio укажите аргумент в меню **Свойства** > **Отладка** > **Аргументы приложения**.

Задание аргумента на странице свойств Visual Studio добавляет его в файл *launchSettings.json*. Настройка компоновщика

* Blazor выполняет компоновку для промежуточного языка (IL) в каждой сборке выпуска, чтобы удалить ненужный промежуточный язык из выходных сборок.
* Для получения дополнительной информации см. <xref:host-and-deploy/blazor/configure-linker>.
* Загрузка пользовательских ресурсов загрузки

Приложение Blazor WebAssembly можно инициализировать с помощью функции `loadBootResource`, чтобы переопределить встроенный механизм загрузки ресурсов загрузки.

| Используйте `loadBootResource` в следующих сценариях.    | Разрешить пользователям загружать статические ресурсы, такие как данные о часовом поясе или *dotnet.wasm*, из сети CDN. |
| ------------ | ----------- |
| `type`       | Загрузить сжатые сборки с помощью HTTP-запроса и распаковать их на клиенте для узлов, которые не поддерживают получение сжатого содержимого с сервера. Создать псевдоним ресурсов, выбрав другое имя путем перенаправления каждого запроса `fetch` на новое имя. |
| `name`       | Параметры `loadBootResource` приведены в следующей таблице. |
| `defaultUri` | Параметр |
| `integrity`  | Описание |

Тип ресурса.

* Допустимые типы: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata` Имя ресурса.

  * Относительный или абсолютный URI ресурса.
  * Строка целостности, представляющая ожидаемое содержимое в ответе.
  * `loadBootResource` возвращает любой из следующих элементов для переопределения процесса загрузки.

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* Строка URI. В следующем примере (*wwwroot/index.html*) следующие файлы обслуживаются из сети CDN в `https://my-awesome-cdn.com/`.

  *dotnet.\*.js*
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* *dotnet.wasm*

Данные часового пояса `Promise<Response>`.

Передайте параметр `integrity` в заголовке для сохранения поведения проверки целостности по умолчанию. В следующем примере (*wwwroot/index.html*) в исходящие запросы добавляется пользовательский заголовок HTTP и передается параметр `integrity` в вызов `fetch`.

## <a name="change-the-filename-extension-of-dll-files"></a>`null`/`undefined`, что приводит к поведению загрузки по умолчанию.

Внешние источники должны возвращать необходимые заголовки CORS для браузеров, чтобы разрешить загрузку ресурсов между источниками.

По умолчанию CDN обычно предоставляют необходимые заголовки. Необходимо указать только типы для пользовательских поведений.

Типы, не указанные в `loadBootResource`, загружаются платформой в соответствии с поведением при загрузке по умолчанию.

Изменение расширения DLL-файлов.

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

Если вам необходимо изменить расширения опубликованных *DLL*-файлов приложения, следуйте указаниям в этом разделе.

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

После публикации приложения используйте скрипт оболочки или конвейер сборки DevOps для переименования *DLL*-файлов для использования другого расширения файла.

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

Указывайте на *DLL*-файлы в каталоге *wwwroot* опубликованных выходных данных приложения (например, *{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot*).

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
В следующих примерах *DLL*-файлы переименованы для использования расширения файла *BIN*.

Windows:

* Если ресурсы рабочей роли также используются, добавьте следующую команду: В Linux или macOS.
* Если ресурсы рабочей роли также используются, добавьте следующую команду:

Чтобы использовать расширение файла, отличное от *BIN*, замените *BIN* в предыдущих командах. Для сжатых файлов *blazor.boot.json.gz* и *blazor.boot.json.br* используйте один из следующих подходов. Удалите сжатые файлы *blazor.boot.json.gz* и *blazor.boot.json.br*.

Сжатие при таком подходе отключается.

Выполните повторное сжатие обновленного файла *blazor.boot.json*.

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

Приведенные выше рекомендации также применимы при использовании ресурсов рабочей роли.

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

Удалите или повторно распакуйте файлы *wwwroot/service-worker-assets.js.br* и *wwwroot/service-worker-assets.js.gz*,

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

иначе проверка целостности файлов в браузере завершится ошибкой.
 
