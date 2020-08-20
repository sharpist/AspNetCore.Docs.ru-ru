---
title: Профили публикации Visual Studio (.pubxml) для развертывания приложений ASP.NET Core
author: rick-anderson
description: Узнайте, как создавать профили публикации в Visual Studio и применять их для управления развертыванием приложений ASP.NET Core в разных целевых объектах.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
no-loc:
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: a7bbc3f3a0a0d5a320bd290d325f0c01390828b4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634167"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a>Профили публикации Visual Studio (.pubxml) для развертывания приложений ASP.NET Core

Авторы: [Саид Ибрагим Хашими](https://github.com/sayedihashimi) (Sayed Ibrahim Hashimi) и [Рик Андерсон](https://twitter.com/RickAndMSFT)

Этот документ посвящен использованию Visual Studio 2019 или более поздней версии для создания и применения профилей публикации. Профили публикации, созданные с помощью Visual Studio, можно применять в MSBuild и Visual Studio. Инструкции по публикации в Azure см. в разделе <xref:tutorials/publish-to-azure-webapp-using-vs>.

Команда `dotnet new mvc` создает файл проекта со следующим [элементом \<Project>](/visualstudio/msbuild/project-element-msbuild) на верхнем уровне:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

В описанном выше элементе `<Project>` атрибут `Sdk` импортирует [свойства](/visualstudio/msbuild/msbuild-properties) и [целевые объекты](/visualstudio/msbuild/msbuild-targets) для MSBuild из файлов *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\ SDK.props* и *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, соответственно. По умолчанию `$(MSBuildSDKsPath)` (в Visual Studio 2019 Enterprise) находится в папке *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.

`Microsoft.NET.Sdk.Web` ([Веб-пакет SDK](xref:razor-pages/web-sdk)) зависит от других пакетов SDK, включая `Microsoft.NET.Sdk` ([пакет SDK для .NET Core](/dotnet/core/project-sdk/msbuild-props)) и `Microsoft.NET.Sdk.Razor` ([пакет SDK для Razor](xref:razor-pages/sdk)). Импортируются свойства и целевые объекты MSBuild, связанные с каждым из зависимых пакетов SDK. Целевые объекты публикации импортируют подходящий набор целевых объектов в зависимости от используемых методов публикации.

Когда MSBuild или Visual Studio загружает проект, выполняются следующие обобщенные действия:

* сборка проекта;
* вычисление файлов для публикации;
* публикация файлов в месте назначения;

## <a name="compute-project-items"></a>вычисление элементов проекта.

После загрузки проекта вычисляются [элементы (файлы) проекта MSBuild](/visualstudio/msbuild/common-msbuild-project-items). Порядок обработки файла определяется типом элемента. По умолчанию файлы с расширением *.cs* включаются в список элементов `Compile`. Файлы в списке элементов `Compile` компилируются.

Список элементов `Content` содержит файлы, предназначенные для публикации, а также результаты сборки. По умолчанию в список элементов `Content` включаются файлы, соответствующие шаблонам `wwwroot\**`, `**\*.config` и `**\*.json`. Например, [стандартная маска](https://gruntjs.com/configuring-tasks#globbing-patterns) `wwwroot\**` соответствует всем файлам в папке *wwwroot* и всех ее вложенных папках.

::: moniker range=">= aspnetcore-3.0"

[Веб-пакет SDK](xref:razor-pages/web-sdk) импортирует [пакет SDK для Razor](xref:razor-pages/sdk). Это означает, что в список элементов `Content` дополнительно включаются файлы, соответствующие шаблонам `**\*.cshtml` и `**\*.razor`.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[Веб-пакет SDK](xref:razor-pages/web-sdk) импортирует [пакет SDK для Razor](xref:razor-pages/sdk). Это означает, что в список элементов `Content` дополнительно включаются файлы, соответствующие шаблону `**\*.cshtml`.

::: moniker-end

Чтобы явным образом добавить файл в список публикации, поместите его в *CSPROJ*-файл, как показано в разделе [включения файлов](#include-files).

При нажатии кнопки **Публикация** в Visual Studio или при публикации из командной строки происходит следующее:

* Вычисляются свойства и (или) элементы (файлы, требующие сборки).
* **Только Visual Studio**: пакеты NuGet восстанавливаются. (Восстановление выполняется пользователем в интерфейсе командной строки.)
* Выполняется сборка проекта.
* Вычисляются публикуемые элементы (файлы, требующие публикации).
* Публикуется проект (вычисляемые файлы копируются в место назначения публикации.)

Когда проект ASP.NET Core ссылается на `Microsoft.NET.Sdk.Web` в файле проекта, файл *app_offline.htm* помещается в корневой каталог веб-приложения. Если файл присутствует, модуль ASP.NET Core корректно завершает работу приложения и обслуживает файл *app_offline.htm* во время развертывания. Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).

## <a name="basic-command-line-publishing"></a>Простая публикация из командной строки

Публикация из командной строки работает на всех платформах, поддерживаемых .NET Core, и не требует наличия Visual Studio. В приведенных ниже примерах команда .NET Core CLI [dotnet publish](/dotnet/core/tools/dotnet-publish) выполняется из папки проекта (где хранится *CSPROJ*-файл). Если папка проекта не является текущим рабочим каталогом, явным образом передайте путь к файлу проекта. Пример:

```dotnetcli
dotnet publish C:\Webs\Web1
```

Для создания и публикации веб-приложения выполните следующие команды.

```dotnetcli
dotnet new mvc
dotnet publish
```

Команда `dotnet publish` создает подобные выходные данные:

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

По умолчанию папка публикации имеет формат *bin\Debug\\{МОНИКЕР ЦЕЛЕВОЙ ПЛАТФОРМЫ}\publish\\* . Например, *bin\Debug\netcoreapp2.2\publish\\* .

Следующая команда определяет сборку `Release` и папку для публикации.

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

Команда `dotnet publish` вызывает метод MSBuild, который, в свою очередь, вызывает целевой объект `Publish`. Все параметры, передаваемые в `dotnet publish`, передаются далее в MSBuild. Параметры `-c` и `-o` сопоставляются со свойствами MSBuild `Configuration` и `OutputPath` соответственно.

Свойства MSBuild можно передавать в любом из следующих форматов.

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

Например, следующая команда публикует сборку `Release` в общую сетевую папку. Общая сетевая папка указывается с помощью символов косой черты ( *//r8/* ) и работает на всех поддерживаемых платформах .NET Core.

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

Убедитесь, что приложение, публикуемое для развертывания, не запущено. Во время выполнения приложения файлы в папке *publish* блокируются. Заблокированные файлы скопировать нельзя, так что развертывание в этом случае не произойдет.

## <a name="publish-profiles"></a>Профили публикации

В этом разделе для создания профиля публикации используется Visual Studio 2019 или более поздней версии. После создания профиля публикацию можно выполнять из Visual Studio или из командной строки. Профили публикации упрощают процесс публикации. Вы можете создать любое количество профилей.

Создайте профиль публикации в Visual Studio, выбрав один из следующих методов:

* Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите **Опубликовать**.
* Выберите команду **Опубликовать {имя проекта}** в меню **Сборка**.

Откроется вкладка **Публикация** на странице возможностей приложения. Если в проекте нет профиля публикации, откроется страница **Выберите целевой объект публикации**. Вам будет предложено выбрать один из следующих целевых объектов публикации:

* Служба приложений Azure
* Служба приложений Azure в Linux
* Виртуальные машины Azure
* Папка
* IIS, FTP, веб-развертывание (для любого веб-сервера)
* Профиль импорта

Чтобы определить наиболее подходящий целевой объект публикации, см. раздел [Какие варианты публикации мне подойдут](/visualstudio/ide/not-in-toc/web-publish-options).

Если вы выбрали **Папка** в качестве целевого объекта, укажите путь к папке, в которой будут храниться опубликованные ресурсы. Путь к папке по умолчанию: *bin\\{КОНФИГУРАЦИЯ ПРОЕКТА}\\{МОНИКЕР ЦЕЛЕВОЙ ПЛАТФОРМЫ}\publish\\* . Например, *bin\Release\netcoreapp2.2\publish\\* . Нажмите кнопку **Создать профиль**, чтобы завершить процесс.

Когда профиль публикации будет создан, изменения отразятся на вкладке **Публикация**. Новый профиль появится в раскрывающемся списке. Под раскрывающимся списком щелкните **Создать новый профиль**, чтобы создать еще один профиль.

Инструмент публикации Visual Studio создает файл MSBuild *Properties/PublishProfiles/{имя_профиля}.pubxml* с описанием профиля публикации. *PUBXML*-файл:

* Содержит настройки конфигурации публикации и используется в процессе публикации.
* Может меняться для настройки процесса сборки и публикации.

Если публикация выполняется в целевой объект Azure, файл с расширением *.pubxml* содержит идентификатор подписки Azure. При таком типе целевого объекта мы не рекомендуем добавлять этот файл в систему управления версиями. Если публикация выполняется в целевой объект, не относящийся к Azure, файл с расширением *.pubxml* можно безопасно вернуть в систему.

Конфиденциальные данные (например, пароль публикации) шифруются на уровне пользователя или компьютера. Они сохраняются в файле *Properties/PublishProfiles/{имя_профиля}.pubxml.user*. Так как этот файл может содержать конфиденциальные данные, его не следует возвращать в систему управления версиями.

Общие сведения о публикации веб-приложения ASP.NET Core см. в статье <xref:host-and-deploy/index>. Открытый исходный код задач MSBuild и целевых объектов, необходимых для публикации веб-приложения ASP.NET Core, размещен в [репозитории dotnet/websdk](https://github.com/dotnet/websdk).

Следующие команды могут использовать профили публикации папки, MSDeploy и [Kudu](https://github.com/projectkudu/kudu/wiki). Так как в MSDeploy отсутствует кроссплатформенная поддержка, следующие параметры MSDeploy поддерживаются только в Windows.

**Папка (работает на всех платформах):**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

**MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

**Пакет MSDeploy:**

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

В предшествующих примерах:

* `dotnet publish` и `dotnet build`поддерживают API-интерфейсы Kudu для публикации в Azure с любой платформы. Публикация с помощью Visual Studio поддерживает API Kudu, но для кроссплатформенной публикации в Azure их поддерживает WebSDK.
* Не передавайте `DeployOnBuild` команде `dotnet publish`.

Дополнительные сведения см. в [документации по Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).

Добавьте в папку проекта *Properties/PublishProfiles* профиль публикации со следующим содержимым:

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a>Пример публикации папки

Для публикации с применением профиля *FolderProfile* используйте любую из указанных ниже команд.

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile`
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

Команда .NET Core CLI [dotnet build](/dotnet/core/tools/dotnet-build) вызывает `msbuild` для сборки и публикации. Команды `dotnet build` и `msbuild` эквивалентны, если они передаются в профиле папки. При прямом вызове `msbuild` на платформе Windows используется версия MSBuild для .NET Framework. Вызов `dotnet build` не в профиле папки:

* Вызывает `msbuild`, который использует MSDeploy.
* Приводит к сбою (даже при работе в Windows). Для публикации профилей, отличных от профиля папки, вызывайте `msbuild` напрямую.

Следующий профиль публикации папки был создан в Visual Studio и публикуется в общей сетевой папке:

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

В предшествующем примере:

* Свойство `<ExcludeApp_Data>` присутствует просто для того, чтобы удовлетворить потребность в схеме XML. Свойство `<ExcludeApp_Data>` не влияет на процесс публикации даже при наличии папки *App_Data* в корневом каталоге проекта. Папка *App_Data* не получает специальной обработки, как в проектах ASP.NET 4.x.
* Свойству `<LastUsedBuildConfiguration>` задано значение `Release`. При публикации из Visual Studio значение `<LastUsedBuildConfiguration>` указывается на основе значения, которое использовалось при запуске процесса публикации. `<LastUsedBuildConfiguration>` имеет особое значение и его не следует переопределять в импортируемом файле MSBuild. Тем не менее, это свойство можно переопределить в командной строке с помощью одного из следующих подходов.
  * С использованием .NET Core CLI:

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * С использованием MSBuild

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  Дополнительные сведения см. в статье [MSBuild: настройка свойства конфигурации](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a>Публикация конечной точки MSDeploy из командной строки

В следующем примере используется веб-приложение ASP.NET Core, созданное с помощью Visual Studio, с именем *AzureWebApp*. Профиль публикации приложений Azure добавляется с помощью Visual Studio. Дополнительные сведения о том, как создать профиль, см. в разделе [Профили публикации](#publish-profiles).

Чтобы развернуть приложение с помощью профиля публикации, выполните команду `msbuild` из **командной строки разработчика** Visual Studio. Командная строка доступна в папке *Visual Studio* в меню **Пуск** на панели задач Windows. Для удобства можно добавить командную строку в меню **Сервис** в Visual Studio. Дополнительные сведения см. в статье [Командная строка разработчика для Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).

MSBuild использует следующий синтаксис команд.

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* `{PATH}`. путь к файлу проекта приложения.
* `{PROFILE}`. имя профиля публикации.
* `{USERNAME}`. имя пользователя MSDeploy. `{USERNAME}` можно найти в профиле публикации.
* `{PASSWORD}`. пароль MSDeploy. Получите `{PASSWORD}` из файла *{PROFILE}.PublishSettings*. Файл с расширением *.PublishSettings* можно скачать одним из следующих способов:
  * **Обозреватель решений**: Выберите **Представление** > **Cloud Explorer**. Подключитесь к подписке Azure. Откройте **Службы приложений**. Щелкните правой кнопкой приложение. Выберите **Загрузить профиль публикации**.
  * Портал Azure: щелкните **Скачать профиль публикации** на панели **Обзор** веб-приложения.

В следующем примере используется профиль публикации с именем *AzureWebApp — веб-развертывание*:

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

Профиль публикации можно также использовать с командой .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) из командной оболочки Windows:

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> Команда `dotnet msbuild` доступна на всех платформах и может компилировать приложения ASP.NET Core в macOS и Linux. Однако MSBuild в macOS и Linux не может развертывать приложения в Azure или других конечных точках MSDeploy.

## <a name="set-the-environment"></a>Указание среды

Включите свойство `<EnvironmentName>` в профиле публикации (*PUBXML*) или файле проекта, чтобы задать [среду](xref:fundamentals/environments) приложения:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

Если вам нужно преобразовать *web.config* (например, задать переменные среды на основе конфигурации, профиля или среды), см. раздел <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="exclude-files"></a>Исключение файлов

При публикации веб-приложений ASP.NET Core включаются следующие ресурсы:

* Артефакты сборки
* Файлы и папки, соответствующие следующим стандартным маскам:
  * `**\*.config` (например, *web.config*);
  * `**\*.json` (например, *appsettings.json*).
  * `wwwroot\**`

MSBuild поддерживает [стандартные маски](https://gruntjs.com/configuring-tasks#globbing-patterns). Например, представленный ниже элемент `<Content>` запретит копирование текстовых файлов ( *.txt*) из папки *wwwroot\content* и всех ее подпапок.

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Предложенную выше разметку можно добавить в профиль публикации или в файл *CSPROJ*. При добавлении в файл *.csproj* правило добавляется во все профили публикации в проекте.

Следующий элемент `<MsDeploySkipRules>` исключает все файлы из папки *wwwroot\content*.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Элемент `<MsDeploySkipRules>` не удаляет *пропускаемые* целевые объекты с сайта развертывания. Целевые файлы и папки, указанные в элементе `<Content>`, будут удалены с сайта развертывания. Допустим, что в развернутом веб-приложении есть следующие файлы.

* *Views/Home/About1.cshtml*
* *Views/Home/About2.cshtml*
* *Views/Home/About3.cshtml*

Если вы добавите указанные ниже элементы `<MsDeploySkipRules>`, эти файлы не будут удалены с сайта развертывания.

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

Приведенные выше элементы `<MsDeploySkipRules>` запрещают развертывание *пропускаемых* файлов. Эти файлы не будут удалены, если они уже развернуты.

Следующий элемент `<Content>` удаляет целевые файлы на сайте развертывания.

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Применение указанного выше элемента `<Content>` при развертывании из командной строки дает следующий результат:

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a>Включаемые файлы

В следующих разделах описываются различные подходы для включения файла во время публикации. В разделе [Включение общего файла](#general-file-inclusion) используется элемент `DotNetPublishFiles`, предоставляемый файлом целевых объектов публикации в [веб-пакете SDK](xref:razor-pages/web-sdk). В разделе [Включение выборочного файла](#selective-file-inclusion) используется элемент `ResolvedFileToPublish`, предоставляемый файлом целевых объектов публикации в [пакете SDK для .NET Core](/dotnet/core/project-sdk/msbuild-props). Поскольку веб-пакет SDK зависит от пакета SDK для .NET Core, можно использовать любой из этих элементов в проекте ASP.NET Core.

### <a name="general-file-inclusion"></a>Включение общих файлов

Элемент `<ItemGroup>` в приведенном ниже примере демонстрирует копирование папки, расположенной за пределами папки проекта, в папку опубликованного сайта. Файлы, добавленные в следующий элемент разметки `<ItemGroup>`, включены по умолчанию.

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

Предыдущая разметка:

* может размещаться в файле *.csproj* или в профиле публикации. Если добавить ее в файл *CSPROJ*, она будет включаться в каждый профиль публикации этого проекта.
* Объявляет элемент `_CustomFiles` для хранения файлов, соответствующих стандартной маске атрибута `Include`. Папка *образов*, указанная в шаблоне, находится вне каталога проекта. [Зарезервированное свойство](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties) с именем `$(MSBuildProjectDirectory)` разрешается в абсолютный путь файла проекта.
* Предоставляет список файлов элементу `DotNetPublishFiles`. По умолчанию элемент `<DestinationRelativePath>` в элементе пуст. Значение по умолчанию переопределяется в разметке и использует [стандартные метаданные элементов](/visualstudio/msbuild/msbuild-well-known-item-metadata), например `%(RecursiveDir)`. Внутренний текст представляет папку *wwwroot/images* опубликованного сайта.

### <a name="selective-file-inclusion"></a>Включение выборочных файлов

Выделенная разметка в следующем примере показывает:

* Копирование файла, расположенного вне проекта, в папку *wwwroot* опубликованного сайта. Имя файла *ReadMe2.md* сохраняется.
* Исключение папки *wwwroot\Content*.
* Исключение *Views\Home\About2.cshtml*.

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

В предыдущем примере используется элемент `ResolvedFileToPublish`, поведение по умолчанию которого — всегда копировать файлы, предоставленные в атрибуте `Include` опубликованному сайту. Переопределите поведение по умолчанию, включив дочерний элемент `<CopyToPublishDirectory>` с внутренним текстом `Never` или `PreserveNewest`. Пример:

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

Дополнительные примеры развертывания см. в [файле README из репозитория веб-пакета SDK](https://github.com/dotnet/sdk/tree/master/src/WebSdk).

## <a name="run-a-target-before-or-after-publishing"></a>Выполнение целевого объекта до или после публикации

Встроенные целевые объекты `BeforePublish` и `AfterPublish` позволяют выполнить целевой объект до или после его публикации. Добавьте следующие элементы в профиль публикации, чтобы сохранять сообщения в консоли, возвращаемые до и после публикации:

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a>Публикация на сервере с использованием сертификата без доверия

Добавьте свойство `<AllowUntrustedCertificate>` со значением `True` в профиль публикации:

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a>Служба Kudu

Чтобы просмотреть файлы в развертывании веб-приложения службы приложений Azure, используйте [службу Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). Добавьте к имени веб-приложения маркер `scm`. Пример:

| URL-адрес                                    | Результат       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | Веб-приложение      |
| `http://mysite.scm.azurewebsites.net/` | Служба Kudu |

Для просмотра, редактирования, удаления и (или) добавления файлов выберите в меню пункт [Консоль отладки](https://github.com/projectkudu/kudu/wiki/Kudu-console).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Веб-развертывание](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) упрощает развертывание веб-приложений и веб-сайтов на серверах IIS.
* [Репозиторий веб-пакета SDK на GitHub](https://github.com/dotnet/websdk/issues) проблемы с файлами и возможности запросов для развертывания.
* [Публикация веб-приложения ASP.NET в виртуальную машину Azure из Visual Studio](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
