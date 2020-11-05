---
title: Файл web.config
author: rick-anderson
description: Узнайте, что содержит файл web.config и как настроить различные параметры модуля ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057288"
---
# <a name="webconfig-file"></a>Файл `web.config`

`web.config` — это файл, который считывается службами IIS и [модулем ASP.NET Core](xref:host-and-deploy/aspnet-core-module) для настройки приложения, размещенного в службах IIS.

## <a name="webconfig-file-location"></a>Расположение файла `web.config`

Для корректной настройки [модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module) необходимо наличие файла `web.config` в [корневой папке содержимого](xref:fundamentals/index#content-root) развертываемого приложения (как правило, это основной путь приложения). Это расположение соответствует физическому пути веб-сайта, указанному в службах IIS. Файл `web.config` требуется в корне приложения, чтобы разрешить публикацию нескольких приложений с помощью веб-развертывания.

По физическому пути приложения находятся файлы с конфиденциальной информацией, например, `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (комментарии к XML-документации) и `{ASSEMBLY}.deps.json`, где заполнитель `{ASSEMBLY}` представляет собой имя сборки. Когда файл `web.config` присутствует и сайт запускается нормально, службы IIS не обрабатывают запросы к этим файлам. Если файл `web.config` отсутствует, неправильно именован или не может настроить нормальный запуск сайта, службы IIS могут свободно передавать содержимое этих конфиденциальных файлов.

**Файл `web.config` должен постоянно находиться в развертывании, у этого файла должно быть правильное имя, и файл должен быть в состоянии настроить нормальный запуск сайта. Никогда не удаляйте файл `web.config` из развертывания в рабочей среде.**

Если в проекте нет файла `web.config`, он создается с соответствующими аргументами `processPath` и `arguments` для настройки модуля ASP.NET Core и переносится в [опубликованные выходные данные](xref:host-and-deploy/directory-structure).

Если в проекте есть файл `web.config`, он преобразуется с соответствующими аргументами `processPath` и `arguments` для настройки модуля ASP.NET Core и переносится в опубликованные выходные данные. Преобразование не изменяет параметры конфигурации служб IIS, включенные в файл.

Файл `web.config` может содержать дополнительные параметры конфигурации IIS, управляющие активными модулями IIS. Сведения о модулях IIS, которые могут обрабатывать запросы к приложениям ASP.NET Core, см. в статье [Модули IIS](xref:host-and-deploy/iis/modules).

Создание, преобразование и публикация файла `web.config` обрабатываются целевым объектом MSBuild (`_TransformWebConfig`) при публикации проекта. Этот целевой объект присутствует в целевых веб-пакетах SDK (`Microsoft.NET.Sdk.Web`). Пакет SDK задается в начале файла проекта:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Чтобы веб-пакет SDK не преобразовывал файл `web.config`, используйте свойство `<IsTransformWebConfigDisabled>` в файле проекта:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Если пакет SDK не преобразует файл, аргументы `processPath` и `arguments` нужно задать вручную. Для получения дополнительной информации см. <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Настройка модуля ASP.NET Core с помощью `web.config`

Модуль ASP.NET Core настроен с помощью раздела `aspNetCore` узла `system.webServer` файла `web.config` на веб-сайте.

Следующий файл `web.config` публикуется для [зависимого от платформы развертывания](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) и настраивает модуль ASP.NET Core для обработки запросов к веб-сайту.

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

Следующий файл `web.config` опубликован для [автономного развертывания](/dotnet/articles/core/deploying/#self-contained-deployments-scd).

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

Значение `false` свойства <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A> указывает, что параметры, заданные в элементе [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location), не наследуются приложениями, которые находятся во вложенном каталоге приложения.

Когда приложение развернуто в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), путь `stdoutLogFile` задан как `\\?\%home%\LogFiles\stdout`. Путь сохраняет журналы stdout в папке `LogFiles`, расположение которой автоматически создается службой.

Сведения о конфигурации дочерних приложений IIS см. здесь: <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Атрибуты элемента `aspNetCore`

| attribute | Описание | Значение по умолчанию |
| --------- | ----------- | :-----: |
| `arguments` | <p>Необязательный строковый атрибут.</p><p>Аргументы для исполняемого файла, указанного в атрибуте `processPath`.</p> | |
| `disableStartUpErrorPage` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, страница **502.5 — ошибка процесса** подавляется и страница в файле `web.config` с кодом состояния 502 имеет более высокий приоритет.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, маркер безопасности отправляется дочернему процессу, прослушивающему порт `%ASPNETCORE_PORT%`, как заголовок MS-ASPNETCORE-WINAUTHTOKEN каждого запроса. Этот процесс вызывает CloseHandle по этому маркеру безопасности каждого запроса.</p> | `true` |
| `hostingModel` | <p>Необязательный строковый атрибут.</p><p>Указывает модель размещения — внутри процесса (`InProcess`/`inprocess`) или вне процесса (`OutOfProcess`/`outofprocess`).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Необязательный целочисленный атрибут.</p><p>Указывает число экземпляров процесса, заданное в параметре `processPath`, которое может появиться для каждого приложения.</p><p>&dagger;Для внутрипроцессного размещения существует ограничение: `1`.</p><p>Параметр `processesPerApplication` не рекомендуется. Этот атрибут будет удален в будущем выпуске.</p> | По умолчанию: `1`<br>Минимум: `1`<br>Максимум: `100`&dagger; |
| `processPath` | <p>Обязательный строковый атрибут.</p><p>Путь к исполняемому файлу, который запускает процесс прослушивания HTTP-запросов. Поддерживаются относительные пути. Если путь начинается с `.`, то начало пути считается относительно корневого каталога веб-сайта.</p> | |
| `rapidFailsPerMinute` | <p>Необязательный целочисленный атрибут.</p><p>Указывает количество сбоев за минуту, которыми может завершиться процесс, указанный в `processPath`. Если этот предел превышен, модуль останавливает запуск процесса на оставшуюся часть минуты.</p><p>Не поддерживается для внутрипроцессного размещения.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `100` |
| `requestTimeout` | <p>Необязательный атрибут timespan.</p><p>Указывает продолжительность, на протяжении которой модуль ASP.NET Core ожидает ответа от процесса, прослушивающего порт %ASPNETCORE_PORT%.</p><p>В версиях модуля ASP.NET Core, поставляемых с выпуском ASP.NET Core 2.1 или новее, атрибут `requestTimeout` указывается в часах, минутах и секундах.</p><p>Не применяется к внутрипроцессному размещению. Для внутрипроцессного размещения модуль ожидает, пока приложение не обработает запрос.</p><p>Допустимые значения для сегментов минут и секунд в строках находятся в диапазоне 0–59. Значение `60` для минут и секунд приведет к ошибке *500 — внутренняя ошибка сервера*.</p> | По умолчанию: `00:02:00`<br>Минимум: `00:00:00`<br>Максимум: `360:00:00` |
| `shutdownTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Длительность ожидания модуля в секундах, пока произойдет правильное выключение исполняемого файла при обнаружении файла `app_offline.htm`.</p> | По умолчанию: `10`<br>Минимум: `0`<br>Максимум: `600` |
| `startupTimeLimit` | <p>Необязательный целочисленный атрибут.</p><p>Время в секундах, которое модуль ожидает, пока запустится процесс прослушивания порта исполняемого файла. Если этот предел превышен, модуль завершает процесс.</p><p>*Внутрипроцессное* размещение. Процесс **не** перезапускается, и параметр `rapidFailsPerMinute` **не** используется.</p><p>Размещение *вне процесса*. Модуль пытается перезапустить процесс при получении нового запроса и будет продолжать пытаться перезапустить процесс для последующих входящих запросов, если не удается запустить приложение определенное в атрибуте `rapidFailsPerMinute` количество раз за последнюю минуту.</p><p>Значение 0 (ноль) **не** считается бесконечным временем ожидания.</p> | По умолчанию: `120`<br>Минимум: `0`<br>Максимум: `3600` |
| `stdoutLogEnabled` | <p>Дополнительный логический атрибут.</p><p>Если значение равно true, `stdout` и `stderr` для процесса, указанного в атрибуте `processPath`, перенаправляются к файлу, заданному в атрибуте `stdoutLogFile`.</p> | `false` |
| `stdoutLogFile` | <p>Необязательный строковый атрибут.</p><p>Указывает относительный или абсолютный путь к файлу, для которого регистрируются `stdout` и `stderr` из процесса, указанного в `processPath`. Относительные пути задаются относительно корневого каталога веб-сайта. Любой путь, начинающийся с `.`, относится к корневому каталогу веб-сайта, а все остальные пути рассматриваются как абсолютные пути. Все папки, указанные в пути, создаются модулем при создании файла журнала. С помощью разделителей подчеркивания метка времени, идентификатор процесса и расширение файла ( `.log`) добавляются к последнему сегменту пути журнала `stdoutLogFile`. Если в качестве значения задано значение `.\logs\stdout`, например, журнал stdout сохраняется как `stdout_20180205194132_1934.log` в папке `logs` с датой 5 февраля 2018 г. в 19:41:32 с идентификатором процесса 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Настройка переменных среды

Переменные среды для процесса можно указать в атрибуте `processPath`. Укажите переменную среды с дочерним элементом `<environmentVariable>` элемента коллекции `<environmentVariables>`. Переменные среды, установленные в этом разделе, имеют приоритет над переменными системной среды.

В приведенном ниже примере устанавливаются две переменные среды в `web.config`. `ASPNETCORE_ENVIRONMENT` настраивает среду приложения для `Development`. Разработчик может временно задать это значение в файле `web.config`, чтобы принудительно загрузить [Страницу исключений для разработчиков](xref:fundamentals/error-handling) при отладке исключения приложения. `CONFIG_DIR` — пример пользовательской переменной среды, где разработчик написал код, который считывает значение при запуске, чтобы сформировать путь для загрузки файла конфигурации приложения.

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
> Вместо установки среды напрямую в `web.config` можно включить свойство `<EnvironmentName>` в [профиль публикации (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) или файл проекта. При этом подходе во время публикации проекта среда задается в файле `web.config`:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Установите только переменную среды `ASPNETCORE_ENVIRONMENT` для `Development` на серверах промежуточных процессов и тестирования, которые недоступны для ненадежных сетей, таких как Интернет.

## <a name="configuration-of-iis-with-webconfig"></a>Настройка служб IIS с помощью `web.config`

Конфигурация IIS зависит от `<system.webServer>` раздела `web.config` для сценариев IIS, предназначенных для работы приложений ASP.NET Core с помощью модуля ASP.NET Core. Например, конфигурация IIS работает для динамического сжатия. Если в службах IIS на уровне сервера настроено динамическое сжатие, элемент `<urlCompression>` в файле `web.config` приложения может отключить это сжатие для приложения ASP.NET Core.

Дополнительные сведения см. в следующих разделах:

* [Справочник по настройке для `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Сведения о настройке переменных среды для отдельных приложений, выполняющихся в изолированных пулах приложений (такая возможность поддерживается в службах IIS, начиная с версии 10.0), см. в разделе *Команда `AppCmd.exe`* статьи [Переменные среды `<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) в справочной документации по службам IIS.

## <a name="configuration-sections-of-webconfig"></a>Разделы конфигурации `web.config`

Разделы конфигурации приложений ASP.NET 4.x в файле `web.config` не используются для конфигурации приложений ASP.NET Core.

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Для настройки приложений ASP.NET Core используются другие поставщики конфигураций. Дополнительные сведения см. в разделе [Конфигурация](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Преобразование web.config

Если вам необходимо преобразовать `web.config` при публикации, обратитесь к разделу <xref:host-and-deploy/iis/transform-webconfig>. Вам может потребоваться преобразовать `web.config` при публикации, чтобы задать переменные среды на основе конфигурации, профиля или среды.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Службы IIS\<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
