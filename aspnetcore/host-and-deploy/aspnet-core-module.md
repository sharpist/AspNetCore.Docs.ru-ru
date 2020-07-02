---
title: Модуль ASP.NET Core
author: rick-anderson
description: Сведения о настройке модуля ASP.NET Core для размещения приложений ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: b60fa81b3bf180cd2ba15f285df9474be42f95f8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400093"
---
# <a name="aspnet-core-module"></a>Модуль ASP.NET Core

Авторы: [Том Дайкстра](https://github.com/tdykstra) (Tom Dykstra), [Рик Штраль](https://github.com/RickStrahl) (Rick Strahl), [Крис Росс](https://github.com/Tratcher) (Chris Ross), [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson), [Сураб Ширхатти](https://twitter.com/sshirhatti) (Sourabh Shirhatti) и [Джастин Коталик](https://github.com/jkotalik) (Justin Kotalik)

::: moniker range=">= aspnetcore-3.0"

Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:

* Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).
* Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).

При размещении вне процесса модуль работает только с Kestrel. Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Модели размещения

### <a name="in-process-hosting-model"></a>Модель внутрипроцессного размещения

По умолчанию приложения ASP.NET Core используют модель внутрипроцессного размещения.

При внутрипроцессном размещении применимы следующие характеристики:

* Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`). Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:

  * Регистрация `IISHttpServer`.
  * Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.
  * Настройка перехвата ошибок запуска на узле.

* [Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.

* Совместное использование пула приложений среди приложений не поддерживается. Используйте один пул приложений для каждого приложения.

* При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения. Например, подключение websocket может задерживать завершение работы приложения.

* Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.

* Обнаружены отключения клиентов. При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).

* В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).

  Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Вызовите метод `SetCurrentDirectory`. Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.

* При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя. Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию. При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * [Развертывания веб-пакета (однофайлового)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) не поддерживаются.

### <a name="out-of-process-hosting-model"></a>Модель размещения вне процесса

Чтобы настроить приложение для внепроцессного размещения, задайте для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` в файле проекта (*CSPROJ*):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Для внутрипроцессного размещения указано значение `InProcess`, которое является значением по умолчанию.

Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.

Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).

Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:

* Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.
* Настройка перехвата ошибок запуска на узле.

### <a name="hosting-model-changes"></a>Изменения модели размещения

Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.

Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express. Следующий запрос для приложения порождает новый процесс IIS Express.

### <a name="process-name"></a>Имя процесса

`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).

Многие собственные модули, такие как проверка подлинности Windows, остаются активными. Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.

Дополнительные возможности модуля ASP.NET Core:

* Задание переменных среды для рабочего процесса.
* Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.
* Переадресация токенов проверки подлинности Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Как установить и использовать модуль ASP.NET Core

Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Конфигурация с помощью файла web.config

Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.

Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.

Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`. Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.

Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Атрибуты элемента aspNetCore

| Атрибут | Описание | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `hostingModel` | <p>Необязательный строковый атрибут.</p><p>Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</p><p>&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</p><p>Параметр `processesPerApplication` не рекомендуется. Этот атрибут будет удален в будущем выпуске.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100`&dagger; |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p><p>Не поддерживается для внутрипроцессного размещения.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</p><p>Не применяется к внутрипроцессному размещению. Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</p><p>Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59. Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</p><p>Значение 0 (ноль) **не** считается бесконечным временем ожидания.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, создаются модулем при создании файла журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Настройка переменных среды

Переменные среды для процесса можно указать в атрибуте `processPath`. Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`. Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.

В следующем примере устанавливаются две переменные среды в *web.config*. `ASPNETCORE_ENVIRONMENT` настраивает в качестве среды приложения `Development`. Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения. `CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Вместо установки среды напрямую в *web.config* включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта. При этом подходе во время публикации проекта среда задается в файле *web.config*:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.

## <a name="app_offlinehtm"></a>App_offline.htm

Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов. Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.

Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*. Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.

При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения. Например, подключение websocket может задерживать завершение работы приложения.

## <a name="start-up-error-page"></a>Страница ошибок запуска

Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.

Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.

Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.

Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.

Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`. Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Создание и перенаправление журнала

Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`. Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).

Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки. Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.

Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.

Не используйте журнал stdout для ведения общего журнала приложений. Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов. Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).

При создании файла журнала автоматически добавляются отметка времени и расширение файла. Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей. Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.

Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ). После запуска все дополнительные журналы удаляются.

В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`. Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`. Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.

Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.

Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Расширенные журналы диагностики

Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики. Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Все папки, указанные в пути (*logs* в приведенном выше примере), создаются модулем при создании файла журнала. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).

Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.

Уровни (в порядке возрастания степени детализации):

* ОШИБКА
* ПРЕДУПРЕЖДЕНИЕ
* ИНФОРМАЦИЯ
* TRACE

Расположения (допускаются несколько расположений):

* CONSOLE
* EVENTLOG
* FILE

Параметры обработчика могут быть указаны с помощью переменных среды:

* `ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки. (По умолчанию: *aspnetcore debug.log*)
* `ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.

> [!WARNING]
> **Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы. Размер журнала не ограничен. Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.

См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).

## <a name="modify-the-stack-size"></a>Изменение размера стека

*Применяется только при использовании модели внутрипроцессного размещения.*

Настройте размер управляемого стека, задав для параметра `stackSize` значение в байтах в файле *web.config*. Размер по умолчанию — `1048576` байт (1 МБ).

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Конфигурация прокси-сервера использует протокол HTTP и токен связывания

*Применяется только к размещению вне процесса.*

Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP. Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.

Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника. Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`). Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер. ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды. Если значения токена не совпадают, запрос заносится в журнал и отклоняется. Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере. Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Модуль ASP.NET Core с общей конфигурацией IIS

Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**. Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.

При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.

1. Отключите общую конфигурацию IIS.
1. Запустите установщик.
1. Экспортируйте обновленный файл *applicationHost.config* в общую папку.
1. Повторно включите общую конфигурацию IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Версия модуля и журналы установщика хостинга Bundle

Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.

1. В системе размещения перейдите к папке *%windir%\System32\inetsrv*.
1. Найдите файл *aspnetcore.dll*.
1. Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.
1. Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.

Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Модуль, схемы и расположение файлов конфигурации

### <a name="module"></a>Module

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Схема

**Службы IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Параметр Configuration

**Службы IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для выполнения следующих задач:

* Размещение приложения ASP.NET Core внутри рабочего процесса IIS (`w3wp.exe`). Это так называемая [модель внутрипроцессного размещения](#in-process-hosting-model).
* Переадресация веб-запросов к серверной части приложения ASP.NET Core на [сервере Kestrel](xref:fundamentals/servers/kestrel). Это [модель размещения вне процесса](#out-of-process-hosting-model).

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

При размещении в процессе модуль использует реализацию внутрипроцессного сервера для IIS — HTTP-сервер IIS (`IISHttpServer`).

При размещении вне процесса модуль работает только с Kestrel. Модуль не работает с [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Модели размещения

### <a name="in-process-hosting-model"></a>Модель внутрипроцессного размещения

Чтобы настроить приложение для внутрипроцессного размещения, добавьте свойство `<AspNetCoreHostingModel>` к файлу проекта приложения со значением `InProcess` (размещение вне процесса имеет значение `OutOfProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Модель внутрипроцессного размещения не поддерживается для приложений ASP.NET Core, предназначенных для .NET Framework.

Значение `<AspNetCoreHostingModel>` не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.

Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.

При внутрипроцессном размещении применимы следующие характеристики:

* Вместо сервера [Kestrel](xref:fundamentals/servers/kestrel) используется HTTP-сервер IIS (`IISHttpServer`). Для внутрипроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> для выполнения следующих действий:

  * Регистрация `IISHttpServer`.
  * Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.
  * Настройка перехвата ошибок запуска на узле.

* [Атрибут requestTimeout](#attributes-of-the-aspnetcore-element) не применяется к внутрипроцессному размещению.

* Совместное использование пула приложений среди приложений не поддерживается. Используйте один пул приложений для каждого приложения.

* При использовании [веб-развертывания](/iis/publish/using-web-deploy/introduction-to-web-deploy) или размещении [файла app_offline.htm в развертывании](xref:host-and-deploy/iis/index#locked-deployment-files) вручную приложение может не завершить работу сразу при наличии открытого соединения. Например, подключение websocket может задерживать завершение работы приложения.

* Архитектура (разрядность) приложения и установленная среда выполнения (x64 или x86) должны соответствовать архитектуре пула приложений.

* Обнаружены отключения клиентов. При отключении клиента происходит отмена токена отмены [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*).

* В ASP.NET Core 2.2.1 и более ранних версий <xref:System.IO.Directory.GetCurrentDirectory*> возвращает рабочий каталог процесса, запущенного службами IIS, а не каталог приложения (например, *C:\Windows\System32\inetsrv* для *w3wp.exe*).

  Пример кода, который задает текущий каталог приложения, см. в разделе [Класс CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Вызовите метод `SetCurrentDirectory`. Последующие вызовы <xref:System.IO.Directory.GetCurrentDirectory*> возвращают каталог приложения.

* При размещении в процессе <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутри для инициализации пользователя. Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию. При преобразовании утверждений с реализацией <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> вызовите <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> для добавления служб проверки подлинности:

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a>Модель размещения вне процесса

Чтобы настроить приложение для размещения вне процесса, используйте один из следующих подходов в файле проекта.

* Не указывайте свойство `<AspNetCoreHostingModel>`. Если свойство `<AspNetCoreHostingModel>` отсутствует в файле, значение по умолчанию — `OutOfProcess`.
* Установите для свойства `<AspNetCoreHostingModel>` значение `OutOfProcess` (внутрипроцессное размещение имеет значение `InProcess`):

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Это значение не учитывает регистр, поэтому `inprocess` и `outofprocess` являются допустимыми значениями.

Сервер [Kestrel](xref:fundamentals/servers/kestrel) используется вместо HTTP-сервера IIS (`IISHttpServer`).

Для внепроцессной обработки [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) вызывает <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> для выполнения следующих действий:

* Настройка порта и базового пути, которые будет прослушивать сервер при выполнении за модулем ASP.NET Core.
* Настройка перехвата ошибок запуска на узле.

### <a name="hosting-model-changes"></a>Изменения модели размещения

Если параметр `hostingModel` изменяется в файле *web.config* (как описано в разделе [Конфигурация с помощью web.config](#configuration-with-webconfig)), модуль перезапускает рабочий процесс для служб IIS.

Для IIS Express модуль не перезапускает рабочий процесс, а запускает нормальное завершение работы текущего процесса IIS Express. Следующий запрос для приложения порождает новый процесс IIS Express.

### <a name="process-name"></a>Имя процесса

`Process.GetCurrentProcess().ProcessName` сообщает `w3wp`/`iisexpress` (внутри процесса) или `dotnet` (вне процесса).

Многие собственные модули, такие как проверка подлинности Windows, остаются активными. Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.

Дополнительные возможности модуля ASP.NET Core:

* Задание переменных среды для рабочего процесса.
* Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.
* Переадресация токенов проверки подлинности Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Как установить и использовать модуль ASP.NET Core

Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Конфигурация с помощью файла web.config

Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.

Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications*> указывает, что параметры, заданные в элементе [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.

Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`. Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.

Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Атрибуты элемента aspNetCore

| Атрибут | Описание | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `hostingModel` | <p>Необязательный строковый атрибут.</p><p>Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</p><p>&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</p><p>Параметр `processesPerApplication` не рекомендуется. Этот атрибут будет удален в будущем выпуске.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100`&dagger; |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p><p>Не поддерживается для внутрипроцессного размещения.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</p><p>Не применяется к внутрипроцессному размещению. Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</p><p>Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59. Значение **60** для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</p><p>Значение 0 (ноль) **не** считается бесконечным временем ожидания.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, создаются модулем при создании файла журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Настройка переменных среды

Переменные среды для процесса можно указать в атрибуте `processPath`. Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`. Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.

В следующем примере устанавливаются две переменные среды. `ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`. Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения. `CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Вместо установки среды напрямую в *web.config* включите свойство `<EnvironmentName>` в профиль публикации ([.pubxml](xref:host-and-deploy/visual-studio-publish-profiles)) или файл проекта. При этом подходе во время публикации проекта среда задается в файле *web.config*:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.

## <a name="app_offlinehtm"></a>App_offline.htm

Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов. Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.

Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*. Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.

При использовании модели размещения вне процесса приложение может не завершать работу немедленно при наличии открытого соединения. Например, подключение websocket может задерживать завершение работы приложения.

## <a name="start-up-error-page"></a>Страница ошибок запуска

Если при внутри- или внепроцессном размещении происходит сбой запуска приложения, открываются страницы пользовательских сообщений об ошибках.

Если модулю ASP.NET Core не удается найти внутри- или внепроцессный обработчик запросов, откроется страница кода состояния *500.0 — ошибка загрузки внутри- или внепроцессного обработчика запросов*.

Если в модели размещения внутри процесса модулю ASP.NET Core не удается запустить приложение, откроется страница кода состояния *500.30 — ошибка запуска*.

Если в модели размещения вне процесса модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*.

Чтобы подавить отображение этой странице и вернуться к странице IIS кода состояния 5xx по умолчанию, используйте атрибут `disableStartUpErrorPage`. Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Создание и перенаправление журнала

Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`. Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).

Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки. Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.

Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.

Не используйте журнал stdout для ведения общего журнала приложений. Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов. Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).

При создании файла журнала автоматически добавляются отметка времени и расширение файла. Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей. Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.

Если `stdoutLogEnabled` имеет значение false, возникающие при запуске приложения ошибки записываются и передаются в журнал событий (макс. 30 КБ). После запуска все дополнительные журналы удаляются.

В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`. Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`. Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.

Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Расширенные журналы диагностики

Модуль ASP.NET Core можно настроить. Он позволяет работать с расширенными журналами диагностики. Добавьте элемент `<handlerSettings>` в элемент `<aspNetCore>` в файле *web.config*. Задайте параметру `debugLevel` значение `TRACE`, чтобы обеспечить высокую точность диагностических сведений:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Папки, указанные в пути к значению `<handlerSetting>` (*logs* в приведенном выше примере), не создаются модулем автоматически и должны заранее существовать в развертывании. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).

Значения уровня отладки (`debugLevel`) могут включать уровень и расположение.

Уровни (в порядке возрастания степени детализации):

* ОШИБКА
* ПРЕДУПРЕЖДЕНИЕ
* ИНФОРМАЦИЯ
* TRACE

Расположения (допускаются несколько расположений):

* CONSOLE
* EVENTLOG
* FILE

Параметры обработчика могут быть указаны с помощью переменных среды:

* `ASPNETCORE_MODULE_DEBUG_FILE`. Путь к файлу журнала отладки. (По умолчанию: *aspnetcore debug.log*)
* `ASPNETCORE_MODULE_DEBUG`. Параметр уровня отладки.

> [!WARNING]
> **Не** оставляйте ведение журнала отладки включенным в развертывании дольше того времени, которое требуется для устранения проблемы. Размер журнала не ограничен. Если оставить журнал отладки включенным, он может исчерпать все доступное место на диске и привести к сбою сервера или службы приложений.

См. пример элемента `aspNetCore` в файле *web.config* в разделе [Конфигурация с помощью файла web.config](#configuration-with-webconfig).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Конфигурация прокси-сервера использует протокол HTTP и токен связывания

*Применяется только к размещению вне процесса.*

Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP. Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.

Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника. Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`). Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер. ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды. Если значения токена не совпадают, запрос заносится в журнал и отклоняется. Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере. Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Модуль ASP.NET Core с общей конфигурацией IIS

Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**. Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.

При использовании общей конфигурации IIS на том же компьютере, где установлены службы IIS, запустите установщик пакета размещения ASP.NET Core с параметром `OPT_NO_SHARED_CONFIG_CHECK` со значением `1`:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Если путь к общей конфигурации не на том же компьютере, где установлены службы IIS, выполните следующие действия.

1. Отключите общую конфигурацию IIS.
1. Запустите установщик.
1. Экспортируйте обновленный файл *applicationHost.config* в общую папку.
1. Повторно включите общую конфигурацию IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Версия модуля и журналы установщика хостинга Bundle

Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.

1. В системе размещения перейдите к папке *%windir%\System32\inetsrv*.
1. Найдите файл *aspnetcore.dll*.
1. Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.
1. Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.

Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Модуль, схемы и расположение файлов конфигурации

### <a name="module"></a>Module

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

* %ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

* %ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

* %ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll

### <a name="schema"></a>Схема

**Службы IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml

### <a name="configuration"></a>Параметр Configuration

**Службы IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Модуль ASP.NET Core имеет собственный модуль IIS, который подключается к конвейеру IIS для переадресации веб-запросов в серверные приложения ASP.NET Core.

Поддерживаемые версии Windows:

* Windows 7 и более поздние версии
* Windows Server 2008 R2 и более поздние версии

Модуль работает только с Kestrel. Модуль несовместим с [HTTP.sys](xref:fundamentals/servers/httpsys).

Так как приложения ASP.NET Core выполняются в процессе, отделенном от рабочего процесса IIS, этот модуль также обрабатывает управление процессами. Модуль запускает процесс для приложения ASP.NET Core при поступлении первого запроса и перезапускает приложение при сбое. Это, по сути, совпадает с поведением приложений ASP.NET 4.x, выполняемых внутрипроцессно в IIS и управляемых [службой активации процессов Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

На следующей схеме показана связь между IIS, модулем ASP.NET Core и приложением:

![Модуль ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

Запросы поступают из Интернета в драйвер HTTP.sys в режиме ядра. Драйвер направляет запросы к службам IIS на настроенный порт веб-сайта — обычно 80 (HTTP) или 443 (HTTPS). Модуль перенаправляет запросы Kestrel на случайный порт для приложения, отличающийся от порта 80 или 443.

Модуль задает порт с помощью переменной среды во время запуска, а [ПО промежуточного слоя для интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настраивает сервер для прослушивания `http://localhost:{port}`. Выполняются дополнительные проверки, и запросы не из модуля отклоняются. Модуль не поддерживает переадресацию по HTTPS, поэтому запросы переадресовываются по протоколу HTTP, даже если были получены IIS по протоколу HTTPS.

После того как Kestrel забирает запрос из модуля, запрос передается в конвейер ПО промежуточного слоя ASP.NET Core. Конвейер ПО промежуточного слоя обрабатывает запрос и передает его в качестве экземпляра `HttpContext` в логику приложения. ПО промежуточного слоя, добавленное интеграцией IIS, обновляет схему, удаленный IP-адрес и базовый путь для переадресации запроса в Kestrel. Отклик приложения передается обратно в службу IIS, которая отправляет его обратно в HTTP-клиент, инициировавший запрос.

Многие собственные модули, такие как проверка подлинности Windows, остаются активными. Дополнительные сведения о модулях IIS, активных с модулем ASP.NET Core, см. в разделе <xref:host-and-deploy/iis/modules>.

Дополнительные возможности модуля ASP.NET Core:

* Задание переменных среды для рабочего процесса.
* Внесение в журнал выходных данных stdout для хранилища файлов с целью устранения неполадок при запуске.
* Переадресация токенов проверки подлинности Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Как установить и использовать модуль ASP.NET Core

Инструкции о том, как установить модуль ASP.NET Core, см. в разделе [Установка пакета размещения .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Конфигурация с помощью файла web.config

Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла *web.config* на веб-сайте.

Следующий файл *web.config* публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Следующий файл *web.config* опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`. Путь сохраняет журналы stdout в папке *LogFiles*, расположение которой автоматически создается службой.

Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Атрибуты элемента aspNetCore

| Атрибут | Описание | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле *web.config* с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт %ASPNETCORE_PORT% как заголовок "MS-ASPNETCORE-WINAUTHTOKEN" каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре **processPath**, которое может появиться для каждого приложения.</p><p>Параметр `processesPerApplication` не рекомендуется. Этот атрибут будет удален в будущем выпуске.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100` |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в **processPath**. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла *app_offline.htm*.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте **rapidFailsPerMinute** количество раз за последнюю минуту.</p><p>Значение 0 (ноль) **не** считается бесконечным временем ожидания.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, **stdout** и **stderr** для процесса, указанного в атрибуте **processPath**, перенаправляются к файлу, заданному в атрибуте **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются **stdout** и **stderr** из процесса, указанного в **processPath**. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, должны существовать, чтобы модуль мог создать файл журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( *.log*) добавляются к последнему сегменту пути журнала **stdoutLogFile**. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как *stdout_20180205194132_1934.log* в папке *журналов* с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Настройка переменных среды

Переменные среды для процесса можно указать в атрибуте `processPath`. Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`.

> [!WARNING]
> Переменные среды, заданные в этом разделе, конфликтуют с переменными системной среды с такими же именами. Если переменная среды задана и в файле *web.config*, и на уровне системы в Windows, значение из файла *web.config* добавляется к значению переменной системной среды (например, `ASPNETCORE_ENVIRONMENT: Development;Development`), которая препятствует запуску приложения.

В следующем примере устанавливаются две переменные среды. `ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`. Разработчик может временно задать это значение в файле *web.config*, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения. `CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.

## <a name="app_offlinehtm"></a>App_offline.htm

Если в корневом каталоге приложения обнаружен файл с именем *app_offline.htm*, модуль ASP.NET Core пытается корректно закрыть приложение и прекратить обработку входящих запросов. Если приложение по-прежнему выполняется через количество секунд, определенное атрибутом `shutdownTimeLimit`, модуль ASP.NET Core завершает выполняющийся процесс.

Хотя файл *app_offline.htm* присутствует, модуль ASP.NET Core отвечает на запросы, отправляя назад содержимое файла *app_offline.htm*. Когда *app_offline.htm* файл удаляется, следующий запрос запускает приложение.

## <a name="start-up-error-page"></a>Страница ошибок запуска

Если модулю ASP.NET Core не удается запустить серверный процесс или начинается серверный процесс, но ему не удается прослушать настроенный порт, появится страница кода состояния *502.5 — ошибка процесса*. Чтобы подавить эту страницу и вернуться к странице IIS кода состояния 502 по умолчанию, используйте атрибут `disableStartUpErrorPage`. Дополнительные сведения о настройке пользовательских сообщений об ошибках см. в разделе [Ошибки HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

![Страница кода состояния "502.5 — ошибка процесса"](aspnet-core-module/_static/ANCM-502_5.png)

## <a name="log-creation-and-redirection"></a>Создание и перенаправление журнала

Модуль ASP.NET Core перенаправляет выходные потоки консоли stdout и stderr на диск, если заданы атрибуты `stdoutLogEnabled` и `stdoutLogFile` элемента `aspNetCore`. Все папки, указанные в пути `stdoutLogFile`, создаются модулем при создании файла журнала. Пул приложений должен иметь доступ на запись в папку, где записываются журналы (используйте атрибут `IIS AppPool\<app_pool_name>` для предоставления разрешения на запись).

Журналы не выполняют циклический сдвиг, пока не произойдет процесс перезапуска или перезагрузки. Администратор несет ответственность за ограничение дискового пространства, которое потребляют журналы.

Журнал stdout рекомендуется использовать только для устранения проблем с запуском приложений при размещении в IIS или при использовании [поддержки во время разработки для служб IIS в Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), а не при локальной отладке и выполнении приложения с использованием IIS Express.

Не используйте журнал stdout для ведения общего журнала приложений. Для обычного входа в приложение ASP.NET Core используйте библиотеку ведения журналов, которая ограничивает размер файла журнала и выполняет циклический сдвиг журналов. Дополнительные сведения см. в разделе [Сторонние поставщики ведения журналов](xref:fundamentals/logging/index#third-party-logging-providers).

При создании файла журнала автоматически добавляются отметка времени и расширение файла. Имя файла журнала составляется путем добавления метки времени, идентификатора процесса и расширения файла ( *.log*) к последнему сегменту атрибута пути `stdoutLogFile` (обычно *stdout*) с символами подчеркивания в качестве разделителей. Если атрибут пути `stdoutLogFile` заканчивается элементом *stdout*, журнал приложения с идентификатором 1934, созданный 5 февраля 2018 г. в 19:42:32, будет иметь имя *stdout_20180205194132_1934.log*.

В следующем примере элемент `aspNetCore` настраивает ведение журнала stdout по относительному пути `.\log\`. Убедитесь, что идентификатор пользователя AppPool имеет разрешение на запись по указанному пути.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

При публикации приложения для развертывания Службы приложений Azure веб-пакет SDK задает для `stdoutLogFile` значение `\\?\%home%\LogFiles\stdout`. Переменная среды `%home` предопределена для приложений, размещенных в Службе приложений Azure.

Сведения о создании правил фильтрации журналов см. в разделах [Конфигурация](xref:fundamentals/logging/index#log-filtering) и [Фильтрация журналов](xref:fundamentals/logging/index#log-filtering) в документации по ведению журнала ASP.NET Core.

Дополнительные сведения о форматах путей см. в разделе [Форматы путей к файлам в системах Windows](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Конфигурация прокси-сервера использует протокол HTTP и токен связывания

Прокси-сервер, созданный между модулем ASP.NET Core и Kestrel, использует протокол HTTP. Отсутствует риск перехвата трафика между модулем и Kestrel из расположения на сервере.

Токен связывания гарантирует, что полученные Kestrel запросы были переданы службами IIS, а не из какого-либо другого источника. Этот токен создается и задается модулем в переменную среды (`ASPNETCORE_TOKEN`). Он также задается в заголовке (`MS-ASPNETCORE-TOKEN`) каждого запроса, переданного через прокси-сервер. ПО промежуточного слоя IIS проверяет каждый получаемый запрос, чтобы убедиться, что заголовок с токеном связывания соответствует значению переменной среды. Если значения токена не совпадают, запрос заносится в журнал и отклоняется. Переменная среды с токеном связывания и трафик между модулем и Kestrel недоступны из расположения на сервере. Не зная значение токена связывания, злоумышленник не может отправлять запросы, обходящие проверку в ПО промежуточного слоя IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Модуль ASP.NET Core с общей конфигурацией IIS

Программа установки модуля ASP.NET Core запускается с правами учетной записи **TrustedInstaller**. Поскольку учетная запись локальной системы не имеет разрешения на изменение пути к общей папке, используемого общей конфигурацией IIS, установщик получает ошибку отказа в доступе при попытке настроить параметры модуля в файле *applicationHost.config* общей папки.

При использовании общей конфигурации IIS выполните следующие действия.

1. Отключите общую конфигурацию IIS.
1. Запустите установщик.
1. Экспортируйте обновленный файл *applicationHost.config* в общую папку.
1. Повторно включите общую конфигурацию IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Версия модуля и журналы установщика хостинга Bundle

Чтобы определить версию установщика модуля ASP.NET Core, выполните следующие действия.

1. В системе размещения перейдите к папке *%windir%\System32\inetsrv*.
1. Найдите файл *aspnetcore.dll*.
1. Щелкните правой кнопкой мыши файл и выберите **Свойства** из контекстного меню.
1. Выберите вкладку **Сведения**. **Версия файла** и **Версия продукта** дают представление об установленной версии модуля.

Журналы установщика хостинга Bundle для модуля находятся в папке *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. Этот файл имеет имя *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.

## <a name="module-schema-and-configuration-file-locations"></a>Модуль, схемы и расположение файлов конфигурации

### <a name="module"></a>Module

**IIS (x86/amd64):**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64):**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>Схема

**Службы IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Параметр Configuration

**Службы IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio: {КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config

Файлы можно найти путем поиска *aspnetcore* в файле *applicationHost.config*.

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [Справочные материалы по модулю ASP.NET Core (главная ветвь)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Используйте раскрывающийся список **Branch** (Ветвь), чтобы выбрать нужный выпуск (например, `release/3.1`).
* <xref:host-and-deploy/iis/modules>
