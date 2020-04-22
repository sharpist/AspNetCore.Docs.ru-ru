---
title: Безопасное хранение секретов приложений в разработке в ASP.NET Core
author: rick-anderson
description: Узнайте, как хранить и получать конфиденциальную информацию в качестве секретов приложений во время разработки приложения ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: c62c5e59ad0a72506fb72bda82aa821a4f1719c8
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791590"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Безопасное хранение секретов приложений в разработке в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Рик Андерсон](https://twitter.com/RickAndMSFT), [Кирк Ларкин](https://twitter.com/serpent5), [Даниэль Рот](https://github.com/danroth27), и [Скотт Эдди](https://github.com/scottaddie)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))

В этом документе разъясняется методы хранения и извлечения конфиденциальных данных при разработке приложения ASP.NET Core на машине разработки. Никогда не храните пароли или другие конфиденциальные данные в исходном коде. Производственные секреты не должны использоваться для разработки или тестирования. Секреты не должны быть развернуты с приложением. Вместо этого секреты должны быть доступны в производственной среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т.д. Вы можете хранить и защищать секреты тестирования и производства Azure с [помощью поставщика конфигураций Azure Key Vault.](xref:security/key-vault-configuration)

## <a name="environment-variables"></a>Переменные среды

Переменные среды используются для предотвращения хранения секретов приложений в коде или в локальных файлах конфигурации. Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.

Рассмотрим веб-приложение ASP.NET Core Core, в котором включена безопасность **индивидуальных учетных записей пользователей.** Строка подключения базы данных по умолчанию включена в файл `DefaultConnection` *appsettings.json* проекта с ключом. Строка подключения по умолчанию предназначена для LocalDB, которая работает в пользовательском режиме и не требует пароля. При развертывании `DefaultConnection` приложения ключевое значение может быть переопределено значением переменной среды. Переменная среды может хранить полную строку соединения с конфиденциальными учетными данными.

> [!WARNING]
> Переменные среды обычно хранятся в простом, незашифрованном тексте. Если машина или процесс скомпрометированы, переменные среды могут быть доступны ненадежным сторонам. Могут потребоваться дополнительные меры по предотвращению разглашения секретов пользователей.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Диспетчер секретов

Инструмент Secret Manager хранит конфиденциальные данные во время разработки проекта ASP.NET Core. В этом контексте часть конфиденциальных данных является секретом приложения. Секреты приложений хранятся в отдельном месте от дерева проекта. Секреты приложения связаны с конкретным проектом или совместно используются несколькими проектами. Секреты приложения не проверяются в исходном управлении.

> [!WARNING]
> Инструмент Secret Manager не шифрует сохраненные секреты и не должен рассматриваться как надежный магазин. Это только для целей развития. Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.

## <a name="how-the-secret-manager-tool-works"></a>Как работает инструмент Secret Manager

Инструмент Secret Manager абстрагирует детали реализации, например, где и как хранятся значения. Вы можете использовать инструмент, не зная этих деталей реализации. Значения хранятся в файле конфигурации JSON в защищенной системой папке профиля пользователя на локальной машине:

# <a name="windows"></a>[Windows](#tab/windows)

Путь файловой системы:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Путь файловой системы:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

В предыдущих путях `<user_secrets_id>` файла замените `UserSecretsId` значение, указанное в файле *.csproj.*

Не пишите код, который зависит от местоположения или формата данных, сохраненных с помощью инструмента Secret Manager. Эти детали реализации могут измениться. Например, секретные значения не зашифрованы, но могут быть в будущем.

## <a name="enable-secret-storage"></a>Включить секретное хранилище

Инструмент Secret Manager работает на настройках конфигурации, хранящихся в профиле пользователя.

Инструмент Secret Manager `init` включает команду в .NET Core SDK 3.0.100 или позже. Чтобы использовать секреты пользователя, запустите следующую команду в каталоге проекта:

```dotnetcli
dotnet user-secrets init
```

Предыдущая команда `UserSecretsId` добавляет элемент `PropertyGroup` в файл *.csproj.* По умолчанию внутренний `UserSecretsId` текст является GUID. Внутренний текст является произвольным, но уникальным для проекта.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

В Visual Studio нажмите правой кнопкой мыши на проект в Solution Explorer и выберите **«Управляйте секретами пользователя»** из контекстного меню. Этот жест `UserSecretsId` добавляет элемент, населенный GUID, в файл *.csproj.*

## <a name="set-a-secret"></a>Установить секрет

Определите секрет приложения, состоящий из ключа и его ценности. Секрет связан со `UserSecretsId` стоимостью проекта. Например, выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

В предыдущем примере толстая `Movies` кишка обозначает `ServiceApiKey` буквальный объект с свойством.

Инструмент Secret Manager может быть использован и в других каталогах. Используйте `--project` опцию для предоставления пути файловой системы, на котором существует файл *.csproj.* Пример:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Структура JSON уплощение в Визуальной студии

Visual Studio's **Manage User Secrets** жест открывает *secrets.json* файл в текстовом редакторе. Замените содержимое *secrets.json* парами с ключевымзначением, которые будут храниться. Пример:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Структура JSON сплющена после `dotnet user-secrets remove` модификаций через или `dotnet user-secrets set`. Например, `dotnet user-secrets remove "Movies:ConnectionString"` бег разрушает `Movies` объект буквально. Измененный файл напоминает следующее:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Установить несколько секретов

Пакет секретов можно установить, подав `set` JSON в команду. В следующем примере содержимое файла *input.json* передается в `set` команду.

# <a name="windows"></a>[Windows](#tab/windows)

Откройте командный корпус и выполните следующую команду:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Откройте командный корпус и выполните следующую команду:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Доступ к секрету

[API ASP.NET Core Configuration](xref:fundamentals/configuration/index) предоставляет доступ к секретам секретного менеджера.

Источник конфигурации пользовательских секретов автоматически добавляется <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> в режиме разработки, когда проект требует инициализировать новый экземпляр узла с преднастроенными по умолчанию. `CreateDefaultBuilder`звонки, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> когда <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>это:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Когда `CreateDefaultBuilder` не вызывается, добавить источник конфигурации <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>пользовательских секретов явно, позвонив. Звоните `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Секреты пользователя могут быть `Configuration` получены через API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Карта секреты POCO

Отображение всего объекта в буквальном смысле к POCO (простой класс .NET со свойствами) полезно для агрегирования связанных свойств.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Чтобы сопоставить предыдущие секреты с `Configuration` POCO, используйте функцию [связывания графика объекта](xref:fundamentals/configuration/index#bind-to-an-object-graph) API. Следующий код связывается `MovieSettings` с пользовательским POCO и получает доступ к значению `ServiceApiKey` свойства:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString` Секреты `Movies:ServiceApiKey` и секреты отображаются в соответствующих свойствах в: `MovieSettings`

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Замена строки секретами

Хранение паролей в простом тексте является небезопасным. Например, строка подключения к базе данных, хранящаяся в *appsettings.json,* может включать пароль для указанного пользователя:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Более безопасный подход заключается в хранении пароля в качестве секрета. Пример:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Удалите пару `Password` значения ключа из строки соединения в *appsettings.json*. Пример:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Значение секрета может быть установлено <xref:System.Data.SqlClient.SqlConnectionStringBuilder> на <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойстве объекта для завершения строки соединения:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Перечислите секреты

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets list
```

Появится следующий результат:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

В предыдущем примере толстая кишка в ключевых именах обозначает иерархию объектов в *secrets.json*.

## <a name="remove-a-single-secret"></a>Удалить один секрет

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Файл *secrets.json* приложения был изменен, чтобы удалить пару ключей, связанную с ключом: `MoviesConnectionString`

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`отображает следующее сообщение:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Удалить все секреты

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets clear
```

Все секреты пользователя приложения были удалены из файла *secrets.json:*

```json
{}
```

Запуск `dotnet user-secrets list` отображает следующее сообщение:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* Ознакомьтесь с [этой проблемой](https://github.com/dotnet/AspNetCore.Docs/issues/16328) для получения информации о доступе к Секретному менеджеру из IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Рик Андерсон](https://twitter.com/RickAndMSFT), [Даниэль Рот](https://github.com/danroth27), и Скотт [Эдди](https://github.com/scottaddie)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))

В этом документе разъясняется методы хранения и извлечения конфиденциальных данных при разработке приложения ASP.NET Core на машине разработки. Никогда не храните пароли или другие конфиденциальные данные в исходном коде. Производственные секреты не должны использоваться для разработки или тестирования. Секреты не должны быть развернуты с приложением. Вместо этого секреты должны быть доступны в производственной среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т.д. Вы можете хранить и защищать секреты тестирования и производства Azure с [помощью поставщика конфигураций Azure Key Vault.](xref:security/key-vault-configuration)

## <a name="environment-variables"></a>Переменные среды

Переменные среды используются для предотвращения хранения секретов приложений в коде или в локальных файлах конфигурации. Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.

Рассмотрим веб-приложение ASP.NET Core Core, в котором включена безопасность **индивидуальных учетных записей пользователей.** Строка подключения базы данных по умолчанию включена в файл `DefaultConnection` *appsettings.json* проекта с ключом. Строка подключения по умолчанию предназначена для LocalDB, которая работает в пользовательском режиме и не требует пароля. При развертывании `DefaultConnection` приложения ключевое значение может быть переопределено значением переменной среды. Переменная среды может хранить полную строку соединения с конфиденциальными учетными данными.

> [!WARNING]
> Переменные среды обычно хранятся в простом, незашифрованном тексте. Если машина или процесс скомпрометированы, переменные среды могут быть доступны ненадежным сторонам. Могут потребоваться дополнительные меры по предотвращению разглашения секретов пользователей.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Диспетчер секретов

Инструмент Secret Manager хранит конфиденциальные данные во время разработки проекта ASP.NET Core. В этом контексте часть конфиденциальных данных является секретом приложения. Секреты приложений хранятся в отдельном месте от дерева проекта. Секреты приложения связаны с конкретным проектом или совместно используются несколькими проектами. Секреты приложения не проверяются в исходном управлении.

> [!WARNING]
> Инструмент Secret Manager не шифрует сохраненные секреты и не должен рассматриваться как надежный магазин. Это только для целей развития. Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.

## <a name="how-the-secret-manager-tool-works"></a>Как работает инструмент Secret Manager

Инструмент Secret Manager абстрагирует детали реализации, например, где и как хранятся значения. Вы можете использовать инструмент, не зная этих деталей реализации. Значения хранятся в файле конфигурации JSON в защищенной системой папке профиля пользователя на локальной машине:

# <a name="windows"></a>[Windows](#tab/windows)

Путь файловой системы:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Путь файловой системы:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

В предыдущих путях `<user_secrets_id>` файла замените `UserSecretsId` значение, указанное в файле *.csproj.*

Не пишите код, который зависит от местоположения или формата данных, сохраненных с помощью инструмента Secret Manager. Эти детали реализации могут измениться. Например, секретные значения не зашифрованы, но могут быть в будущем.

## <a name="enable-secret-storage"></a>Включить секретное хранилище

Инструмент Secret Manager работает на настройках конфигурации, хранящихся в профиле пользователя.

Чтобы использовать секреты `UserSecretsId` пользователя, `PropertyGroup` определите элемент в файле *.csproj.* Внутренний `UserSecretsId` текст является произвольным, но является уникальным для проекта. Разработчики обычно генерируют `UserSecretsId`GUID для .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> В Visual Studio нажмите правой кнопкой мыши на проект в Solution Explorer и выберите **«Управляйте секретами пользователя»** из контекстного меню. Этот жест `UserSecretsId` добавляет элемент, населенный GUID, в файл *.csproj.*

## <a name="set-a-secret"></a>Установить секрет

Определите секрет приложения, состоящий из ключа и его ценности. Секрет связан со `UserSecretsId` стоимостью проекта. Например, выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

В предыдущем примере толстая `Movies` кишка обозначает `ServiceApiKey` буквальный объект с свойством.

Инструмент Secret Manager может быть использован и в других каталогах. Используйте `--project` опцию для предоставления пути файловой системы, на котором существует файл *.csproj.* Пример:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Структура JSON уплощение в Визуальной студии

Visual Studio's **Manage User Secrets** жест открывает *secrets.json* файл в текстовом редакторе. Замените содержимое *secrets.json* парами с ключевымзначением, которые будут храниться. Пример:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Структура JSON сплющена после `dotnet user-secrets remove` модификаций через или `dotnet user-secrets set`. Например, `dotnet user-secrets remove "Movies:ConnectionString"` бег разрушает `Movies` объект буквально. Измененный файл напоминает следующее:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Установить несколько секретов

Пакет секретов можно установить, подав `set` JSON в команду. В следующем примере содержимое файла *input.json* передается в `set` команду.

# <a name="windows"></a>[Windows](#tab/windows)

Откройте командный корпус и выполните следующую команду:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux / macOS](#tab/linux+macos)

Откройте командный корпус и выполните следующую команду:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Доступ к секрету

[API ASP.NET Core Configuration](xref:fundamentals/configuration/index) предоставляет доступ к секретам секретного менеджера.

Если ваш проект нацелен на платформу .NET, установите пакет [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.

В ASP.NET Core 2.0 или позже, источник конфигурации пользовательских <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> секретов автоматически добавляется в режиме разработки, когда проект требует инициализировать новый экземпляр узла с предварительно настроенными по умолчанию. `CreateDefaultBuilder`звонки, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> когда <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>это:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Когда `CreateDefaultBuilder` не вызывается, добавить источник конфигурации <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> пользовательских `Startup` секретов явно, позвонив в конструкторе. Звоните `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Секреты пользователя могут быть `Configuration` получены через API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Карта секреты POCO

Отображение всего объекта в буквальном смысле к POCO (простой класс .NET со свойствами) полезно для агрегирования связанных свойств.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Чтобы сопоставить предыдущие секреты с `Configuration` POCO, используйте функцию [связывания графика объекта](xref:fundamentals/configuration/index#bind-to-an-object-graph) API. Следующий код связывается `MovieSettings` с пользовательским POCO и получает доступ к значению `ServiceApiKey` свойства:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString` Секреты `Movies:ServiceApiKey` и секреты отображаются в соответствующих свойствах в: `MovieSettings`

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Замена строки секретами

Хранение паролей в простом тексте является небезопасным. Например, строка подключения к базе данных, хранящаяся в *appsettings.json,* может включать пароль для указанного пользователя:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Более безопасный подход заключается в хранении пароля в качестве секрета. Пример:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Удалите пару `Password` значения ключа из строки соединения в *appsettings.json*. Пример:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Значение секрета может быть установлено <xref:System.Data.SqlClient.SqlConnectionStringBuilder> на <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойстве объекта для завершения строки соединения:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Перечислите секреты

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets list
```

Появится следующий результат:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

В предыдущем примере толстая кишка в ключевых именах обозначает иерархию объектов в *secrets.json*.

## <a name="remove-a-single-secret"></a>Удалить один секрет

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Файл *secrets.json* приложения был изменен, чтобы удалить пару ключей, связанную с ключом: `MoviesConnectionString`

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Запуск `dotnet user-secrets list` отображает следующее сообщение:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Удалить все секреты

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполнить следующую команду из каталога, в котором существует файл *.csproj:*

```dotnetcli
dotnet user-secrets clear
```

Все секреты пользователя приложения были удалены из файла *secrets.json:*

```json
{}
```

Запуск `dotnet user-secrets list` отображает следующее сообщение:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* Ознакомьтесь с [этой проблемой](https://github.com/dotnet/AspNetCore.Docs/issues/16328) для получения информации о доступе к Секретному менеджеру из IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end