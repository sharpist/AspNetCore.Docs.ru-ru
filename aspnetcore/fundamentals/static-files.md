---
title: Статические файлы в ASP.NET Core
author: rick-anderson
description: Узнайте, как в веб-приложениях ASP.NET Core обслуживать и защищать статические файлы, а также как настраивать ПО промежуточного слоя по размещению статических файлов.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: f7697260e6ab29d0e9ba955dfdf0c8c81e4e1130
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775496"
---
# <a name="static-files-in-aspnet-core"></a>Статические файлы в ASP.NET Core

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)

Статические файлы, такие как HTML, CSS, изображения и JavaScript, являются ресурсами, которые приложения ASP.NET Core предоставляют клиентам напрямую. Для обслуживания таких файлов требуется настроить некоторые параметры.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Обслуживание статических файлов

Статические файлы хранятся в [корневом каталоге документов](xref:fundamentals/index#web-root) проекта. Каталог по умолчанию — *{корневой_каталог_содержимого}/wwwroot*, но его можно изменить через метод [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_). Дополнительные сведения см. в разделах [Корневой каталог содержимого](xref:fundamentals/index#content-root) и [Корневой веб-каталог](xref:fundamentals/index#web-root).

Веб-узел приложения должен знать о расположении корневого каталога содержимого.

::: moniker range=">= aspnetcore-2.0"

Метод `WebHost.CreateDefaultBuilder` устанавливает текущий каталог в качестве корневого каталога содержимого:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Установите текущий каталог в качестве корневого каталога содержимого, вызвав [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) в методе `Program.Main`:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Статические файлы доступны по относительному пути от [корневого каталога документов](xref:fundamentals/index#web-root). Например, шаблон проекта **Web Application** содержит несколько папок в папке *wwwroot*:

* **wwwroot**
  * **css**
  * **images**
  * **js**

Формат URI для доступа к файлу во вложенной папке *images* следующий: *http://\<адрес_сервера>/images/\<имя_файла_изображения>* . Например, *http://localhost:9189/images/banner3.svg* .

::: moniker range=">= aspnetcore-2.1"

Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/). Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Если код предназначен для .NET Framework, добавьте в проект пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/). Если код предназначен для .NET Core, то этот пакет уже включен в метапакет [Microsoft.AspNetCore.All](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Добавьте пакет [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) в проект.

::: moniker-end

Настройте [ПО промежуточного слоя](xref:fundamentals/middleware/index), позволяющее обслуживать статические файлы.

### <a name="serve-files-inside-of-web-root"></a>Обслуживание файлов в корневом веб-каталоге

Вызовите метод [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) в `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Эта перегрузка метода `UseStaticFiles` не принимает параметров, она помечает файлы в [корневом каталоге документов](xref:fundamentals/index#web-root) как обслуживаемые. Следующая разметка ссылается на *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

В приведенном выше коде знак тильды `~/` указывает на [корневой каталог документов](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Обслуживание файлов вне корневого веб-каталога

Пусть имеется иерархия каталогов, в которой статические файлы обслуживаются вне [корневого каталога документов](xref:fundamentals/index#web-root):

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

В запросе можно получить доступ к файлу *banner1.svg*, настроив ПО промежуточного слоя для статических файлов следующим образом:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

В приведенном выше коде доступ к иерархии каталога *MyStaticFiles* представляется через сегмент URI *StaticFiles*. Запрос *http://\<адрес_сервера> /StaticFiles/images/banner1.svg* обслуживает файлы *banner1.svg*.

Следующая разметка ссылается на *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Установка заголовков HTTP-ответов

Для установки заголовков HTTP-ответов можно использовать объект [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions). Кроме настройки обслуживания статических файлов в [корневом каталоге документов](xref:fundamentals/index#web-root), следующий код также устанавливает заголовок `Cache-Control`:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Метод [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) содержится в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).

Файлы, к которым был предоставлен доступ, кэшируются в течение 10 минут (600 секунд) в среде разработки:

![Добавлены заголовки ответов, включая заголовок Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Авторизация статических файлов

ПО промежуточного слоя для статических файлов не предоставляет возможности авторизации. Все обслуживаемые им файлы, включая расположенные в *wwwroot*, находятся в открытом доступе. Для обслуживания файлов с авторизацией:

* Сохраните файлы в любом каталоге за пределами каталога *wwwroot*, к которому имеет доступ ПО промежуточного слоя для статических файлов.
* Обслуживайте их через метод действия, к которому применима авторизация. Возвращайте объект [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult):

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Просмотр каталогов

Просмотр каталогов позволяет пользователям веб-приложениям просматривать файлы и каталоги внутри определенного каталога. По соображениям безопасности просмотр каталогов отключен по умолчанию (см. раздел [Особенности](#considerations)). Разрешить просмотр каталогов можно вызвав метод [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) в `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Добавим необходимые службы путем вызова метода [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) из `Startup.ConfigureServices`:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Приведенный выше код разрешает просмотр папки *wwwroot/images* с помощью URL-адреса *http://\<адрес_сервера>/MyImages*, со ссылками на все файлы и папки:

![просмотр каталогов](static-files/_static/dir-browse.png)

Информацию об угрозе безопасности при включении просмотра каталогов см. в разделе [Особенности](#considerations).

Обратите внимание на два вызова метода `UseStaticFiles` в следующем примере. Первый вызов включает обслуживание статических файлов в папке *wwwroot*. Второй вызов включает просмотр каталогов в папке *wwwroot/images* с помощью URL-адреса *http://\<адрес_сервера>/MyImages*:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Обслуживание документа по умолчанию

Домашняя страница по умолчанию является для посетителей логической отправной точкой при посещении веб-сайта. Для обслуживания страницы по умолчанию без указания полного имени URI вызовите метод [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) из `Startup.Configure`:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> Для обслуживания файла по умолчанию метод `UseDefaultFiles`должен быть вызван до метода `UseStaticFiles`. Метод `UseDefaultFiles` фактически не обслуживает файл, а только перезаписывает URL-адрес. Для обслуживания файла включите ПО промежуточного слоя для статических файлов, вызвав метод `UseStaticFiles`.

При вызове метода `UseDefaultFiles` запросы к папке будут искать следующие файлы:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Первый найденный файл из списка будет обслужен, как будто был введен полный URI. URL-адрес в браузере будет соответствовать запрошенному URI.

Следующий код позволяет изменить имя файла по умолчанию на *mydefault.html*:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

<xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> объединяет функции `UseStaticFiles`, `UseDefaultFiles` и при необходимости `UseDirectoryBrowser`.

Следующий пример кода позволяет обслуживать статические файлы и файл по умолчанию. Просмотр каталогов отключен.

```csharp
app.UseFileServer();
```

Следующий код отличается от предыдущей перегрузки метода без параметров включением просмотра каталогов:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Пусть имеется следующая иерархия каталогов:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Следующий пример кода включает обслуживание статических файлов, файлы по умолчанию и просмотр каталога `MyStaticFiles`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

Метод `AddDirectoryBrowser` должен вызываться при значении `true` свойства `EnableDirectoryBrowsing`:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

При указанных выше коде и иерархии файлов URL-адреса будут разрешаться следующим образом:

| URI            |                             Ответ  |
| ------- | ------|
| *http://\<адрес_сервера>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<адрес_сервера>/StaticFiles*             |     MyStaticFiles/default.html |

Если в каталоге *MyStaticFiles* отсутствует файл с именем по умолчанию, то *http://\<адрес_сервера>/StaticFiles* возвращает список содержимого каталога с доступными для перехода ссылками:

![Список статических файлов](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> и <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> выполняют перенаправление на стороне клиента из `http://{SERVER ADDRESS}/StaticFiles` (без косой черты в конце) в `http://{SERVER ADDRESS}/StaticFiles/` (с косой чертой в конце). Относительные URL-адреса в каталоге *StaticFiles* считаются недопустимыми без косой черты в конце.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Класс [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) содержит свойство `Mappings`, которое служит для сопоставления расширений файлов и типов содержимого MIME. В следующем примере несколько расширений файлов регистрируются в известные типы MIME. Расширение *.rtf* заменяется, а *.mp4* удаляется.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

См. раздел [Типы содержимого MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Нестандартные типы содержимого

ПО промежуточного слоя для статических файлов воспринимает почти 400 известных типов содержимого файлов. Если пользователь запрашивает файл неизвестного типа, ПО промежуточного слоя статических файлов передает запрос следующему компоненту ПО промежуточного слоя в конвейере. Если ПО промежуточного слоя не удается обработать запрос, возвращается ответ *404 Не найдено*. Если просмотр каталогов разрешен, то в списке каталогов отображается ссылка на файл.

Следующий код включает обслуживание неизвестных типов и обслуживает неизвестные файлы как изображения:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

При выполнении вышеописанного кода ответ на запрос файла с неизвестным типом содержимого вернется в виде изображения.

> [!WARNING]
> Включение параметра [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) представляет угрозу безопасности. По умолчанию он отключен, и его использование не рекомендуется. Использование класса [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) является более безопасной альтернативой для обслуживания файлов с нестандартными расширениями.

## <a name="serve-files-from-multiple-locations"></a>Предоставление файлов из нескольких расположений

В качестве значений `UseStaticFiles` и `UseFileServer` по умолчанию используется поставщик файлов, указывающий на *wwwroot*. Вы можете предоставить дополнительные экземпляры `UseStaticFiles` и `UseFileServer` с другими поставщиками файлов для предоставления файлов из других расположений. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Особенности

> [!WARNING]
> Использование `UseDirectoryBrowser` и `UseStaticFiles` может привести к утечке конфиденциальной информации. Настоятельно рекомендуется отключать просмотр каталогов в рабочей среде. Тщательно проверьте, просмотр каких каталогов разрешен посредством `UseStaticFiles` или `UseDirectoryBrowser`. Весь каталог и его подкаталоги становятся общедоступными. Храните файлы, предназначенные для общего доступа, в выделенных каталогах, таких как *\<корневой_каталог_содержимого>/wwwroot*. Отделите эти файлы от представлений MVC, страниц Razor Pages (только для версии 2.x), файлов конфигурации и т. д.

* К URL-адресам содержимого, к которому предоставлен доступ методами `UseDirectoryBrowser` и `UseStaticFiles`, применяются те же требования по регистрозависимости и запрещенным символам, что и к базовой файловой системе. Например, в Windows не учитывается регистр, а в macOS и Linux &mdash; учитывается.

* Приложения ASP.NET Core, размещенные в IIS, используют [Модуль Core ASP.NET](xref:host-and-deploy/aspnet-core-module) для перенаправления всех запросов к приложению, включая запросы статических файлов. Обработчик статических файлов IIS не используется. Не существует возможности обработать запросы до того, как их обработает модуль.

* Выполните следующие шаги в диспетчере служб IIS для удаления обработчика статических файлов IIS на уровне сервера или веб-сайта:
    1. Перейдите к компоненту **Модули**.
    1. Выберите в списке модуль **StaticFileModule**.
    1. Нажмите кнопку **Удалить** в боковой панели **Действия**.

> [!WARNING]
> Если обработчик статических файлов IIS включен **и** модуль ASP.NET Core настроен неправильно, то статические файлы будут обслуживаться. Это может случиться, если, например, не был развернут файл *web.config*.

* Размещайте файлы с кодом (включая *CS* и *CSHTML*) за пределами [корневого каталога документов](xref:fundamentals/index#web-root) проекта приложения. Таким образом, в приложении создается логическое разделение между клиентским содержимым и серверным кодом. Это предотвращает утечку серверного кода.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [ПО промежуточного слоя](xref:fundamentals/middleware/index)
* [Введение в ASP.NET Core](xref:index)
