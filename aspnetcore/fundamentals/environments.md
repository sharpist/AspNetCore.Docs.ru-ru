---
title: Использование нескольких сред в ASP.NET Core
author: rick-anderson
description: Сведения об управлении поведением приложений в разных средах в приложениях ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/1/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/environments
ms.openlocfilehash: 977d222ed61fa914bd4ffb70e192ef19d4da5c33
ms.sourcegitcommit: 6fb27ea41a92f6d0e91dfd0eba905d2ac1a707f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2020
ms.locfileid: "87330664"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Использование нескольких сред в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Авторы: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson) и [Кирк Ларкин](https://twitter.com/serpent5) (Kirk Larkin)

ASP.NET Core настраивает поведение приложения в зависимости от среды выполнения с помощью переменной среды.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Среды

Чтобы определить среду выполнения, ASP.NET Core считывает данные из следующих переменных среды:

1. [DOTNET_ENVIRONMENT](xref:fundamentals/configuration/index#default-host-configuration).
1. `ASPNETCORE_ENVIRONMENT` при вызове <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*>. Шаблоны по умолчанию для веб-приложений ASP.NET Core вызывают `ConfigureWebHostDefaults`. Значение `ASPNETCORE_ENVIRONMENT` переопределяет `DOTNET_ENVIRONMENT`.

Переменной `IHostEnvironment.EnvironmentName` можно присвоить любое значение, но платформа предоставляет следующие значения:

* <xref:Microsoft.Extensions.Hosting.Environments.Development>: на локальном компьютере в файле [launchSettings.json](#lsj) для `ASPNETCORE_ENVIRONMENT` задается значение `Development`.
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <xref:Microsoft.Extensions.Hosting.Environments.Production>: значение по умолчанию, если `DOTNET_ENVIRONMENT` и `ASPNETCORE_ENVIRONMENT` не заданы.

В приведенном ниже коде

* Вызывает [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage), если `ASPNETCORE_ENVIRONMENT` имеет значение `Development`.
* Позволяет вызвать [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler), если переменная `ASPNETCORE_ENVIRONMENT` имеет значение `Staging`, `Production` или `Staging_2`.
* Позволяет внедрить <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в `Startup.Configure`. Этот подход удобен, когда для приложения требуется просто скорректировать `Startup.Configure` для нескольких сред с минимальными различиями в коде для каждой среды.
* Это аналогично коду, созданному на основе шаблонов ASP.NET Core.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Startup.cs?name=snippet)]

[Вспомогательная функция тега среды](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) использует значение [IHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName) для включения или исключения разметки в элементе:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

[Страница со сведениями](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) в [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) включает предыдущую разметку и отображает значение `IWebHostEnvironment.EnvironmentName`.

В ОС Windows и macOS регистр символов в переменных среды и их значениях не учитывается. В ОС Linux в переменных среды и их значениях **регистр символов по умолчанию учитывается**.

### <a name="create-environmentssample"></a>Создание EnvironmentsSample

[Пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample), используемый в этом документе, основан на проекте Razor Pages с именем *EnvironmentsSample*.

Следующий код позволяет создать и запустить веб-приложение с именем *EnvironmentsSample*:

```bash
dotnet new webapp -o EnvironmentsSample
cd EnvironmentsSample
dotnet run --verbosity normal
```

При запуске приложения отображаются некоторые из следующих выходных данных:

```bash
Using launch settings from c:\tmp\EnvironmentsSample\Properties\launchSettings.json
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: c:\tmp\EnvironmentsSample
```

<a name="lsj"></a>

### <a name="development-and-launchsettingsjson"></a>Разработка и launchSettings.json

В среде разработки могут быть включены функции, которые не должны быть доступны в рабочей среде. Например, шаблоны ASP.NET Core включают в среде разработки [страницу со сведениями об исключении для разработчика](xref:fundamentals/error-handling#developer-exception-page).

Среду для локального компьютера разработки можно задать в файле *Properties\launchSettings.json* проекта. Значения среды, заданные в файле *launchSettings.json*, переопределяют значения, заданные в системной среде.

Файл *launchSettings.json*:

* используется только на локальном компьютере разработки;
* не развернут;
* содержит параметры профиля.

В следующем фрагменте JSON показан файл *launchSettings.json* для веб-проекта ASP.NET Core с именем *EnvironmentsSample*, созданный с помощью Visual Studio или `dotnet new`:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettingsCopy.json)]

Предыдущая разметка содержит два профиля:

* `IIS Express`. Профиль по умолчанию, используемый при запуске приложения из Visual Studio. Ключ `"commandName"` имеет значение `"IISExpress"`, поэтому [IISExpress](/iis/extensions/introduction-to-iis-express/iis-express-overview) является веб-сервером. Профиль запуска можно задать для проекта или любого другого профиля. Например, на приведенном ниже изображении при выборе имени проекта запускается [веб-сервер Kestrel](xref:fundamentals/servers/kestrel).

  ![Запуск IIS Express из меню](environments/_static/iisx2.png)
* `EnvironmentsSample`. Имя профиля — это имя проекта. Этот профиль используется по умолчанию при запуске приложения с помощью `dotnet run`.  Ключ `"commandName"` имеет значение `"Project"`, поэтому запускается [веб-сервер Kestrel](xref:fundamentals/servers/kestrel).

Значение `commandName` может определять запускаемый веб-сервер. `commandName` может иметь одно из следующих значений:

* `IISExpress`: запуск IIS Express.
* `IIS`: веб-сервер не запущен. Службы IIS должны быть доступны.
* `Project`: запуск Kestrel.

Вкладка **Отладка** в свойствах проекта Visual Studio предоставляет графический пользовательский интерфейс для изменения файла *launchSettings.json*. Для вступления в силу изменений, внесенных в профили проекта, может потребоваться перезапуск веб-сервера. Чтобы сервер Kestrel обнаружил изменения, внесенные в среду, его необходимо перезапустить.

![Задание переменных среды в свойствах проекта](environments/_static/project-properties-debug.png)

Следующий файл *launchSettings.json* содержит несколько профилей:

[!code-json[](environments/3.1sample/EnvironmentsSample/Properties/launchSettings.json)]

Профили можно выбирать:

* В пользовательском интерфейсе Visual Studio.
* С помощью команды [`dotnet run`](/dotnet/core/tools/dotnet-run) в командной оболочке. При этом для параметра `--launch-profile` нужно задать имя профиля. *Этот подход поддерживает только профили Kestrel.*

  ```dotnetcli
  dotnet run --launch-profile "SampleApp"
  ```

> [!WARNING]
> В файле *launchSettings.json* не должны храниться секреты. Для хранения секретов во время разработки в локальной среде можно использовать [средство Secret Manager](xref:security/app-secrets).

При использовании [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json*. В следующем примере задается несколько [переменных среды для значений конфигурации узла](xref:fundamentals/host/web-host#host-configuration-values):

[!code-json[](environments/3.1sample/EnvironmentsSample/.vscode/launch.json?range=4-10,32-38)]

Файл *.vscode/launch.json* используется только Visual Studio Code.

### <a name="production"></a>Рабочие

Конфигурация рабочей среды должна обеспечивать максимальный уровень безопасности, [производительности](xref:performance/performance-best-practices) и надежности приложений. Некоторые общие параметры, отличные от разработки:

* [Кэширование](xref:performance/caching/memory).
* ресурсы на стороне клиента объединяются в пакеты, уплотняются и могут предоставляться из сети CDN;
* страницы с сообщениями об ошибках диагностики отключены;
* включены страницы с понятными пользователям сообщениями об ошибках;
* включены средства [ведения журналов](xref:fundamentals/logging/index) и мониторинга в рабочей среде (например, с использованием [Application Insights](/azure/application-insights/app-insights-asp-net-core)).

## <a name="set-the-environment"></a>Указание среды

Часто бывает полезным указать определенную среду для тестирования с переменной среды или параметром платформы. Если среда не указана, по умолчанию используется среда `Production`, в которой большинство функций отладки отключено. Способ указания среды зависит от операционной системы.

При создании узла среду приложения определяет последний параметр среды, считанный приложением. Среду приложения невозможно изменить во время его выполнения.

На [странице со сведениями](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample/EnvironmentsSample/Pages/About.cshtml) в [примере кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/3.1sample) отображается значение `IWebHostEnvironment.EnvironmentName`.

### <a name="azure-app-service"></a>Служба приложений Azure

Чтобы установить среду в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), выполните следующие действия:

1. Выберите приложение из колонки **Службы приложений**.
1. В группе **Параметры** выберите колонку **Конфигурация**.
1. На вкладке **Параметры приложения** выберите **Новый параметр приложения**.
1. В окне **Добавить или изменить параметр приложения** в поле **Имя** введите `ASPNETCORE_ENVIRONMENT`. В поле **Значение** укажите среду (например, `Staging`).
1. Установите флажок **Параметр слота развертывания**, если нужно, чтобы параметр среды оставался в текущем слоте при замене слота развертывания. Дополнительные сведения см. в статье [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) (Настройка промежуточных сред в Службе приложений Azure) в документации по Azure.
1. Нажмите кнопку **ОК**, чтобы закрыть окно **Добавить или изменить параметр приложения**.
1. Нажмите кнопку **Сохранить** в верхней части колонки **Конфигурация**.

Служба приложений Azure автоматически перезапускает приложение после добавления, изменения или удаления параметра приложения на портале Azure.

### <a name="windows"></a>Windows

Значения среды в файле *launchSettings.json* переопределяют значения, заданные в системной среде.

Если приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), то, чтобы задать переменную `ASPNETCORE_ENVIRONMENT` для текущего сеанса, используйте следующие команды:

**Командная строка**

```console
set ASPNETCORE_ENVIRONMENT=Staging
dotnet run --no-launch-profile
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Staging"
dotnet run --no-launch-profile
```

Предыдущая команда позволяет задать `ASPNETCORE_ENVIRONMENT` только для процессов, запускаемых из этого командного окна.

Чтобы задать это значение в Windows на глобальном уровне, используйте один из следующих подходов.

* Последовательно откройте **Панель управления** > **Система** > **Дополнительные параметры системы** и добавьте или измените значение `ASPNETCORE_ENVIRONMENT`:

  ![Дополнительные параметры системы](environments/_static/systemsetting_environment.png)

  ![Переменная среды ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Откройте командную строку администратора и выполните команду `setx` либо откройте командную строку администратора PowerShell и используйте `[Environment]::SetEnvironmentVariable`:

  **Командная строка**

  ```console
  setx ASPNETCORE_ENVIRONMENT Staging /M
  ```

  Параметр `/M` указывает на установку переменной среды на уровне системы. Если параметр `/M` не используется, переменная среды задается для учетной записи пользователя.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Staging", "Machine")
  ```

  Значение параметра `Machine` указывает на установку переменной среды на уровне системы. При изменении значения параметра на `User` переменная среды задается для учетной записи пользователя.

Если переменная среды `ASPNETCORE_ENVIRONMENT` задана глобально, она действует для `dotnet run` в любом окне командной строки, открываемом после установки значения. Значения среды в файле *launchSettings.json* переопределяют значения, заданные в системной среде.

**web.config**

Сведения об установке переменной среды `ASPNETCORE_ENVIRONMENT` в файле *web.config* см. в разделе *Настройка переменной среды* в <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Файл проекта или профиль публикации**

**Для развертываний Windows IIS:** Включите свойство `<EnvironmentName>` в [профиле публикации (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) или файле проекта. При этом подходе во время публикации проекта среда задается в файле *web.config*:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Пул приложений IIS**

Чтобы задать переменную среды `ASPNETCORE_ENVIRONMENT` для приложения, выполняющегося в изолированном пуле приложений (такая возможность поддерживается в службах IIS 10.0 и более поздних версий), см. подраздел, посвященный команде *AppCmd.exe*, в разделе [Переменные среды &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Если переменная среды `ASPNETCORE_ENVIRONMENT` задана для пула приложений, ее значение переопределяет значение на уровне системы.

При размещении приложения в службах IIS и добавлении или изменении переменной среды `ASPNETCORE_ENVIRONMENT` используйте один из следующих подходов по применению нового значения в приложении.

* Из командной строки выполните команду `net stop was /y`, за которой следует `net start w3svc`.
* Перезапустите сервер.

#### <a name="macos"></a>macOS

Задать текущую среду в macOS можно в командной строке при запуске приложения:

```bash
ASPNETCORE_ENVIRONMENT=Staging dotnet run
```

Также можно задать среду с помощью команды `export` до запуска приложения:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

Переменные среды на уровне компьютера задаются в файле *BASHRC* или *BASH_PROFILE*. Измените файл в любом текстовом редакторе. Добавьте следующий оператор:

```bash
export ASPNETCORE_ENVIRONMENT=Staging
```

#### <a name="linux"></a>Linux

В дистрибутивах Linux используйте команду `export` в командной строке для значений переменных на уровне сеанса или в файле *BASH_PROFILE* для значений среды на уровне компьютера.

### <a name="set-the-environment-in-code"></a>Указание среды в коде

Вызовите <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> при создании узла. См. раздел <xref:fundamentals/host/generic-host#environmentname>.

### <a name="configuration-by-environment"></a>Конфигурация для разных сред

Для загрузки конфигурации среды см. <xref:fundamentals/configuration/index#json-configuration-provider>.

## <a name="environment-based-startup-class-and-methods"></a>Класс Startup и его методы для разных сред

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a>Внедрение IWebHostEnvironment в класс Startup

Внедрите <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> в конструктор `Startup`. Этот подход удобен, когда для приложения требуется настроить `Startup` всего для нескольких сред с минимальными различиями в коде для каждой среды.

В следующем примере:

* Среда хранится в поле `_env`.
* `_env` используется в `ConfigureServices` и `Configure` для применения конфигурации запуска на основе среды приложения.

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupInject.cs?name=snippet&highlight=3-11)]

### <a name="startup-class-conventions"></a>Соглашения о классе Startup

При запуске приложения ASP.NET Core [класс Startup](xref:fundamentals/startup) выполняет его начальную загрузку. Приложение может определять несколько классов `Startup` для различных сред. Подходящий класс `Startup` выбирается во время выполнения. Класс, у которого суффикс имени соответствует текущей среде, получает приоритет. Если соответствующий класс `Startup{EnvironmentName}` не найден, используется класс `Startup`. Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды. Для обычных приложений такой подход не нужно использовать.

Чтобы реализовать классы `Startup` на основе среды, создайте классы `Startup{EnvironmentName}` и резервный класс `Startup`:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupClassConventions.cs?name=snippet)]

Используйте перегрузку [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), которая принимает имя сборки:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/Program.cs?name=snippet)]

### <a name="startup-method-conventions"></a>Соглашения о методах Startup

Методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) и [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) поддерживают версии для конкретных сред в формате `Configure<EnvironmentName>` и `Configure<EnvironmentName>Services`. Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды:

[!code-csharp[](environments/3.1sample/EnvironmentsSample/StartupMethodConventions.cs?name=snippet)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* <xref:blazor/fundamentals/environments>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core настраивает поведение приложения в зависимости от среды выполнения с помощью переменной среды.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Среды

ASP.NET Core считывает переменную среды `ASPNETCORE_ENVIRONMENT` при запуске приложения и сохраняет ее значение в свойстве [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName). Переменной `ASPNETCORE_ENVIRONMENT` можно присвоить любое значение, но платформа предоставляет три значения:

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (по умолчанию)

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Предыдущий код:

* Вызывает [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage), если `ASPNETCORE_ENVIRONMENT` имеет значение `Development`.
* Вызывает [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler), если переменная `ASPNETCORE_ENVIRONMENT` имеет одно из следующих значений:

  * `Staging`
  * `Production`
  * `Staging_2`

[Вспомогательная функция тега среды](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) использует значение `IHostingEnvironment.EnvironmentName` для включения или исключения разметки в элементе:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

В ОС Windows и macOS регистр символов в переменных среды и их значениях не учитывается. В ОС Linux в переменных среды и их значениях регистр символов по умолчанию учитывается.

### <a name="development"></a>Разработка

В среде разработки могут быть включены функции, которые не должны быть доступны в рабочей среде. Например, шаблоны ASP.NET Core включают в среде разработки [страницу со сведениями об исключении для разработчика](xref:fundamentals/error-handling#developer-exception-page).

Среду для локального компьютера разработки можно задать в файле *Properties\launchSettings.json* проекта. Значения среды, заданные в файле *launchSettings.json*, переопределяют значения, заданные в системной среде.

В следующем коде JSON показаны три профиля из файла *launchSettings.json*:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> Свойство `applicationUrl` в *launchSettings.json* может задать список URL-адресов сервера. Для разделения URL-адресов в списке используется точка с запятой:
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

Когда приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), используется первый профиль с атрибутом `"commandName": "Project"`. Значение `commandName` определяет запускаемый веб-сервер. `commandName` может иметь одно из следующих значений:

* `IISExpress`
* `IIS`
* `Project` (запускается Kestrel)

Когда приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), выполняются указанные ниже действия:

* Считывается файл *launchSettings.json*, если он доступен. Параметры `environmentVariables` в файле *launchSettings.json* переопределяют переменные среды.
* Отображается среда размещения.

Ниже представлены выходные данные приложения, запущенного с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run):

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Вкладка **Отладка** в свойствах проекта Visual Studio предоставляет графический пользовательский интерфейс для изменения файла *launchSettings.json*.

![Задание переменных среды в свойствах проекта](environments/_static/project-properties-debug.png)

Для вступления в силу изменений, внесенных в профили проекта, может потребоваться перезапуск веб-сервера. Чтобы сервер Kestrel обнаружил изменения, внесенные в среду, его необходимо перезапустить.

> [!WARNING]
> В файле *launchSettings.json* не должны храниться секреты. Для хранения секретов во время разработки в локальной среде можно использовать [средство Secret Manager](xref:security/app-secrets).

При использовании [Visual Studio Code](https://code.visualstudio.com/) переменные среды можно задавать в файле *.vscode/launch.json*. В следующем примере показано, как присвоить среде значение `Development`:

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

Файл *.vscode/launch.json* в проекте не читается при запуске приложения с помощью команды `dotnet run` так же, как файл *Properties/launchSettings.json*. При запуске приложения в среде разработки без файла *launchSettings.json* укажите среду с помощью переменной среды или задайте аргумент командной строки как команду `dotnet run`.

### <a name="production"></a>Рабочие

Конфигурация рабочей среды должна обеспечивать максимальный уровень безопасности, производительности и надежности приложений. Некоторые общие параметры, отличные от разработки:

* кэширование;
* ресурсы на стороне клиента объединяются в пакеты, уплотняются и могут предоставляться из сети CDN;
* страницы с сообщениями об ошибках диагностики отключены;
* включены страницы с понятными пользователям сообщениями об ошибках;
* включены средства ведения журналов и мониторинга в рабочей среде, например [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Указание среды

Часто бывает полезным указать определенную среду для тестирования с переменной среды или параметром платформы. Если среда не указана, по умолчанию используется среда `Production`, в которой большинство функций отладки отключено. Способ указания среды зависит от операционной системы.

При создании узла среду приложения определяет последний параметр среды, считанный приложением. Среду приложения невозможно изменить во время его выполнения.

### <a name="environment-variable-or-platform-setting"></a>Переменная среды или параметр платформы

#### <a name="azure-app-service"></a>Служба приложений Azure

Чтобы установить среду в [службе приложений Azure](https://azure.microsoft.com/services/app-service/), выполните следующие действия:

1. Выберите приложение из колонки **Службы приложений**.
1. В группе **Параметры** выберите колонку **Конфигурация**.
1. На вкладке **Параметры приложения** выберите **Новый параметр приложения**.
1. В окне **Добавить или изменить параметр приложения** в поле **Имя** введите `ASPNETCORE_ENVIRONMENT`. В поле **Значение** укажите среду (например, `Staging`).
1. Установите флажок **Параметр слота развертывания**, если нужно, чтобы параметр среды оставался в текущем слоте при замене слота развертывания. Дополнительные сведения см. в статье [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) (Настройка промежуточных сред в Службе приложений Azure) в документации по Azure.
1. Нажмите кнопку **ОК**, чтобы закрыть окно **Добавить или изменить параметр приложения**.
1. Нажмите кнопку **Сохранить** в верхней части колонки **Конфигурация**.

Служба приложений Azure автоматически перезапускает приложение после добавления, изменения или удаления параметра приложения (переменной среды) на портале Azure.

#### <a name="windows"></a>Windows

Если приложение запускается с помощью команды [dotnet run](/dotnet/core/tools/dotnet-run), то, чтобы задать переменную `ASPNETCORE_ENVIRONMENT` для текущего сеанса, используйте следующие команды:

**Командная строка**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Эти команды действуют только для текущего окна. Когда окно закрывается, для параметра `ASPNETCORE_ENVIRONMENT` восстанавливается значение по умолчанию или значение, заданное на компьютере.

Чтобы задать это значение в Windows на глобальном уровне, используйте один из следующих подходов.

* Последовательно откройте **Панель управления** > **Система** > **Дополнительные параметры системы** и добавьте или измените значение `ASPNETCORE_ENVIRONMENT`:

  ![Дополнительные параметры системы](environments/_static/systemsetting_environment.png)

  ![Переменная среды ASPNET Core](environments/_static/windows_aspnetcore_environment.png)

* Откройте командную строку администратора и выполните команду `setx` либо откройте командную строку администратора PowerShell и используйте `[Environment]::SetEnvironmentVariable`:

  **Командная строка**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Параметр `/M` указывает на установку переменной среды на уровне системы. Если параметр `/M` не используется, переменная среды задается для учетной записи пользователя.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Значение параметра `Machine` указывает на установку переменной среды на уровне системы. При изменении значения параметра на `User` переменная среды задается для учетной записи пользователя.

Если переменная среды `ASPNETCORE_ENVIRONMENT` задана глобально, она действует для `dotnet run` в любом окне командной строки, открываемом после установки значения.

**web.config**

Сведения об установке переменной среды `ASPNETCORE_ENVIRONMENT` в файле *web.config* см. в разделе *Настройка переменной среды* в <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

**Файл проекта или профиль публикации**

**Для развертываний Windows IIS:** Включите свойство `<EnvironmentName>` в [профиле публикации (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) или файле проекта. При этом подходе во время публикации проекта среда задается в файле *web.config*:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

**Пул приложений IIS**

Чтобы задать переменную среды `ASPNETCORE_ENVIRONMENT` для приложения, выполняющегося в изолированном пуле приложений (такая возможность поддерживается в службах IIS 10.0 и более поздних версий), см. подраздел, посвященный команде *AppCmd.exe*, в разделе [Переменные среды &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Если переменная среды `ASPNETCORE_ENVIRONMENT` задана для пула приложений, ее значение переопределяет значение на уровне системы.

> [!IMPORTANT]
> При размещении приложения в службах IIS и добавлении или изменении переменной среды `ASPNETCORE_ENVIRONMENT` используйте один из следующих подходов по применению нового значения в приложении.
>
> * Из командной строки выполните команду `net stop was /y`, за которой следует `net start w3svc`.
> * Перезапустите сервер.

#### <a name="macos"></a>macOS

Задать текущую среду в macOS можно в командной строке при запуске приложения:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Также можно задать среду с помощью команды `export` до запуска приложения:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Переменные среды на уровне компьютера задаются в файле *BASHRC* или *BASH_PROFILE*. Измените файл в любом текстовом редакторе. Добавьте следующий оператор:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a>Linux

В дистрибутивах Linux используйте команду `export` в командной строке для значений переменных на уровне сеанса или в файле *BASH_PROFILE* для значений среды на уровне компьютера.

### <a name="set-the-environment-in-code"></a>Указание среды в коде

Вызовите <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> при создании узла. См. раздел <xref:fundamentals/host/web-host#environment>.

### <a name="configuration-by-environment"></a>Конфигурация для разных сред

Для загрузки конфигурации среды мы рекомендуем:

* Файлы *appsettings* (*appsettings.{Environment}.json*). См. раздел <xref:fundamentals/configuration/index#json-configuration-provider>.
* Переменные среды (заданные в каждой системе, где размещено приложение). См. разделы <xref:fundamentals/host/web-host#environment> и <xref:security/app-secrets#environment-variables>.
* Менеджер секретов (только в среде разработки). См. раздел <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Класс Startup и его методы для разных сред

### <a name="inject-ihostingenvironment-into-startupconfigure"></a>Внедрение IHostingEnvironment в Startup.Configure

Внедрите <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> в `Startup.Configure`. Этот подход удобен, когда для приложения требуется просто настроить `Startup.Configure` всего для нескольких сред с минимальными различиями в коде для каждой среды.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a>Внедрение IHostingEnvironment в класс Startup

Внедрите <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> в конструктор `Startup` и назначьте службу полю для использования в рамках всего класса `Startup`. Этот подход удобен, когда для приложения требуется настроить запуск всего для нескольких сред с минимальными различиями в коде для каждой среды.

В следующем примере:

* Среда хранится в поле `_env`.
* `_env` используется в `ConfigureServices` и `Configure` для применения конфигурации запуска на основе среды приложения.

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a>Соглашения о классе Startup

При запуске приложения ASP.NET Core [класс Startup](xref:fundamentals/startup) выполняет его начальную загрузку. Приложение может определять отдельные классы `Startup` для различных сред (например, `StartupDevelopment`). Подходящий класс `Startup` выбирается во время выполнения. Класс, у которого суффикс имени соответствует текущей среде, получает приоритет. Если соответствующий класс `Startup{EnvironmentName}` не найден, используется класс `Startup`. Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды.

Чтобы реализовать классы `Startup` на основе среды, создайте класс `Startup{EnvironmentName}` для каждой используемой среды и резервный класс `Startup`:

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

Используйте перегрузку [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), которая принимает имя сборки:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a>Соглашения о методах Startup

Методы [Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) и [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) поддерживают версии для конкретных сред в формате `Configure<EnvironmentName>` и `Configure<EnvironmentName>Services`. Этот подход удобен, когда для приложения требуется настроить запуск для нескольких сред с многочисленными различиями в коде для каждой среды.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
