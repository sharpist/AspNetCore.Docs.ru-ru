---
title: Надежное хранение секретов приложений в разработке в ASP.NET Core
author: rick-anderson
description: Узнайте, как хранить и извлекать конфиденциальную информацию в виде секретов приложения во время разработки ASP.NET Core приложения.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: a12262d182ce84a326086935627b55d2edc4885e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407009"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Надежное хранение секретов приложений в разработке в ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Kirk Ларкин](https://twitter.com/serpent5), [Даниэль Roth)](https://github.com/danroth27)и [Скотт Эдди (](https://github.com/scottaddie)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))

В этом документе объясняются способы хранения и извлечения конфиденциальных данных во время разработки ASP.NET Core приложения на компьютере разработки. Никогда не храните пароли или другие конфиденциальные данные в исходном коде. Производственные секреты не должны использоваться для разработки или тестирования. Секреты не должны развертываться вместе с приложением. Вместо этого секреты должны быть доступны в рабочей среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т. д. Вы можете хранить и защищать тестовые и рабочие секреты Azure с помощью [поставщика конфигурации Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Переменные среды

Переменные среды используются, чтобы избежать хранения секретов приложения в коде или в локальных файлах конфигурации. Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.

Рассмотрим ASP.NET Core веб-приложение, в котором включена безопасность **отдельных учетных записей пользователей** . Строка подключения к базе данных по умолчанию включается в *appsettings.js* проекта в файле с ключом `DefaultConnection` . Строка подключения по умолчанию — для LocalDB, которая выполняется в пользовательском режиме и не требует пароля. Во время развертывания приложения `DefaultConnection` значение ключа может быть переопределено значением переменной среды. Переменная среды может хранить полную строку подключения с конфиденциальными учетными данными.

> [!WARNING]
> Переменные среды обычно хранятся в виде обычного незашифрованного текста. Если компьютер или процесс скомпрометирован, недоверенные стороны могут получить доступ к переменным среды. Могут потребоваться дополнительные меры для предотвращения раскрытия секретов пользователя.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Диспетчер секретов

Средство диспетчера секретов сохраняет конфиденциальные данные во время разработки проекта ASP.NET Core. В этом контексте фрагмент конфиденциальных данных является секретом приложения. Секреты приложения хранятся в отдельном месте из дерева проекта. Секреты приложения связаны с конкретным проектом или совместно используются в нескольких проектах. Секреты приложения не возвращаются в систему управления версиями.

> [!WARNING]
> Диспетчер секретов не шифрует сохраненные секреты и не должен рассматриваться как доверенное хранилище. Это только для целей разработки. Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.

## <a name="how-the-secret-manager-tool-works"></a>Как работает средство диспетчера секретов

Диспетчер секретов абстрагирует детали реализации, например, где и как хранятся значения. Вы можете использовать это средство, не зная сведений о реализации. Значения хранятся в файле конфигурации JSON в папке профиля пользователя, защищенной системой, на локальном компьютере:

# <a name="windows"></a>[Windows](#tab/windows)

Путь к файловой системе:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux и macOS](#tab/linux+macos)

Путь к файловой системе:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

В приведенных выше путях файлов замените `<user_secrets_id>` значением, `UserSecretsId` указанным в файле *CSPROJ* .

Не записывайте код, который зависит от расположения или формата данных, сохраненных с помощью диспетчера секретов. Эти сведения о реализации могут измениться. Например, секретные значения не шифруются, но могут быть в будущем.

## <a name="enable-secret-storage"></a>Включить хранилище секретов

Диспетчер секретов работает с параметрами конфигурации конкретного проекта, хранящимися в профиле пользователя.

Средство диспетчера секретов включает `init` команду в пакет SDK для .NET Core 3.0.100 или более поздней версии. Чтобы использовать секреты пользователя, выполните следующую команду в каталоге проекта:

```dotnetcli
dotnet user-secrets init
```

Предыдущая команда добавляет `UserSecretsId` элемент в `PropertyGroup` файл *. csproj* . По умолчанию внутренний текст `UserSecretsId` является идентификатором GUID. Внутренний текст является произвольным, но уникален для проекта.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

В Visual Studio щелкните правой кнопкой мыши проект в обозреватель решений и выберите в контекстном меню пункт **Управление секретами пользователя** . Этот жест добавляет `UserSecretsId` элемент, заполненный идентификатором GUID, в *CSPROJ* -файл.

## <a name="set-a-secret"></a>Задать секрет

Определите секрет приложения, состоящий из ключа и его значения. Секрет связан со `UserSecretsId` значением проекта. Например, выполните следующую команду из каталога, в котором существует CSPROJ файл *.*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

В предыдущем примере двоеточие обозначает, что `Movies` является объектным литералом со `ServiceApiKey` свойством.

Диспетчер секретов можно использовать и в других каталогах. Используйте `--project` параметр, чтобы указать путь файловой системы, в котором существует файл *. csproj* . Пример.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Сведение структуры JSON в Visual Studio

Жест **управления пользовательскими секретами** в Visual Studio открывает *secrets.jsдля* файла в текстовом редакторе. Замените содержимое *secrets.jsна* пары "ключ-значение", которые необходимо сохранить. Пример.

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Структура JSON преобразуется в плоскую структуру после изменений с помощью `dotnet user-secrets remove` или `dotnet user-secrets set` . Например, запуск `dotnet user-secrets remove "Movies:ConnectionString"` сворачивает `Movies` объектный литерал. Измененный файл выглядит следующим образом:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Задание нескольких секретов

Пакет секретов можно задать с помощью конвейера `set` командной строки. В следующем примере *input.js* содержимое файла передается `set` команде.

# <a name="windows"></a>[Windows](#tab/windows)

Откройте командную оболочку и выполните следующую команду:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux и macOS](#tab/linux+macos)

Откройте командную оболочку и выполните следующую команду:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Доступ к секрету

[API настройки ASP.NET Core](xref:fundamentals/configuration/index) предоставляет доступ к секретам диспетчера секретов.

Источник конфигурации секреты пользователя автоматически добавляется в режим разработки при вызове проекта <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> для инициализации нового экземпляра узла с предварительно настроенными настройками по умолчанию. `CreateDefaultBuilder`вызывает <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> , если <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> имеет значение <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Если `CreateDefaultBuilder` метод не вызывается, добавьте источник конфигурации user Secret явным образом, вызвав <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> . Вызывайте `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Секреты пользователя можно получить с помощью `Configuration` API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Преобразование секретов в POCO

Сопоставление целого литерала объекта с POCO (простой класс .NET со свойствами) полезен для статистической обработки связанных свойств.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Чтобы связать предыдущие секреты с POCO, используйте `Configuration` функцию [привязки графа объектов](xref:fundamentals/configuration/index#bind-to-an-object-graph) API. Следующий код привязывается к пользовательской `MovieSettings` POCO и обращается к `ServiceApiKey` значению свойства:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`Секреты и `Movies:ServiceApiKey` сопоставлены с соответствующими свойствами в `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Замена строк секретами

Хранение паролей в виде обычного текста является небезопасным. Например, строка подключения к базе данных, хранимая в *appsettings.json* , может включать пароль для указанного пользователя:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Более безопасный подход заключается в хранении пароля в качестве секрета. Пример.

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Удалите `Password` пару "ключ-значение" из строки подключения в *appsettings.json*. Пример.

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

Значение секрета может быть задано для <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойства объекта, чтобы завершить строку подключения:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Вывод списка секретов

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполните следующую команду из каталога, в котором существует файл *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Появится следующий результат:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

В предыдущем примере двоеточие в именах ключей обозначает иерархию объектов в *secrets.js*.

## <a name="remove-a-single-secret"></a>Удаление одного секрета

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполните следующую команду из каталога, в котором существует файл *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Файл *secrets.jsв* приложении изменен для удаления пары "ключ-значение", связанной с `MoviesConnectionString` ключом:

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

Выполните следующую команду из каталога, в котором существует файл *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Все секреты пользователя для приложения удалены из *secrets.jsв* файле:

```json
{}
```

При выполнении `dotnet user-secrets list` отображается следующее сообщение:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* Сведения о доступе к диспетчеру секретов из IIS см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Даниэль Roth)](https://github.com/danroth27)и [Скотт Эдди (](https://github.com/scottaddie)

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))

В этом документе объясняются способы хранения и извлечения конфиденциальных данных во время разработки ASP.NET Core приложения на компьютере разработки. Никогда не храните пароли или другие конфиденциальные данные в исходном коде. Производственные секреты не должны использоваться для разработки или тестирования. Секреты не должны развертываться вместе с приложением. Вместо этого секреты должны быть доступны в рабочей среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т. д. Вы можете хранить и защищать тестовые и рабочие секреты Azure с помощью [поставщика конфигурации Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Переменные среды

Переменные среды используются, чтобы избежать хранения секретов приложения в коде или в локальных файлах конфигурации. Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.

Рассмотрим ASP.NET Core веб-приложение, в котором включена безопасность **отдельных учетных записей пользователей** . Строка подключения к базе данных по умолчанию включается в *appsettings.js* проекта в файле с ключом `DefaultConnection` . Строка подключения по умолчанию — для LocalDB, которая выполняется в пользовательском режиме и не требует пароля. Во время развертывания приложения `DefaultConnection` значение ключа может быть переопределено значением переменной среды. Переменная среды может хранить полную строку подключения с конфиденциальными учетными данными.

> [!WARNING]
> Переменные среды обычно хранятся в виде обычного незашифрованного текста. Если компьютер или процесс скомпрометирован, недоверенные стороны могут получить доступ к переменным среды. Могут потребоваться дополнительные меры для предотвращения раскрытия секретов пользователя.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Диспетчер секретов

Средство диспетчера секретов сохраняет конфиденциальные данные во время разработки проекта ASP.NET Core. В этом контексте фрагмент конфиденциальных данных является секретом приложения. Секреты приложения хранятся в отдельном месте из дерева проекта. Секреты приложения связаны с конкретным проектом или совместно используются в нескольких проектах. Секреты приложения не возвращаются в систему управления версиями.

> [!WARNING]
> Диспетчер секретов не шифрует сохраненные секреты и не должен рассматриваться как доверенное хранилище. Это только для целей разработки. Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.

## <a name="how-the-secret-manager-tool-works"></a>Как работает средство диспетчера секретов

Диспетчер секретов абстрагирует детали реализации, например, где и как хранятся значения. Вы можете использовать это средство, не зная сведений о реализации. Значения хранятся в файле конфигурации JSON в папке профиля пользователя, защищенной системой, на локальном компьютере:

# <a name="windows"></a>[Windows](#tab/windows)

Путь к файловой системе:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux и macOS](#tab/linux+macos)

Путь к файловой системе:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

В приведенных выше путях файлов замените `<user_secrets_id>` значением, `UserSecretsId` указанным в файле *CSPROJ* .

Не записывайте код, который зависит от расположения или формата данных, сохраненных с помощью диспетчера секретов. Эти сведения о реализации могут измениться. Например, секретные значения не шифруются, но могут быть в будущем.

## <a name="enable-secret-storage"></a>Включить хранилище секретов

Диспетчер секретов работает с параметрами конфигурации конкретного проекта, хранящимися в профиле пользователя.

Чтобы использовать секреты пользователя, определите `UserSecretsId` элемент в `PropertyGroup` файле *. csproj* . Внутренний текст `UserSecretsId` является произвольным, но уникален для проекта. Разработчики обычно создают идентификатор GUID для `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> В Visual Studio щелкните правой кнопкой мыши проект в обозреватель решений и выберите в контекстном меню пункт **Управление секретами пользователя** . Этот жест добавляет `UserSecretsId` элемент, заполненный идентификатором GUID, в *CSPROJ* -файл.

## <a name="set-a-secret"></a>Задать секрет

Определите секрет приложения, состоящий из ключа и его значения. Секрет связан со `UserSecretsId` значением проекта. Например, выполните следующую команду из каталога, в котором существует CSPROJ файл *.*

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

В предыдущем примере двоеточие обозначает, что `Movies` является объектным литералом со `ServiceApiKey` свойством.

Диспетчер секретов можно использовать и в других каталогах. Используйте `--project` параметр, чтобы указать путь файловой системы, в котором существует файл *. csproj* . Пример.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Сведение структуры JSON в Visual Studio

Жест **управления пользовательскими секретами** в Visual Studio открывает *secrets.jsдля* файла в текстовом редакторе. Замените содержимое *secrets.jsна* пары "ключ-значение", которые необходимо сохранить. Пример.

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

Структура JSON преобразуется в плоскую структуру после изменений с помощью `dotnet user-secrets remove` или `dotnet user-secrets set` . Например, запуск `dotnet user-secrets remove "Movies:ConnectionString"` сворачивает `Movies` объектный литерал. Измененный файл выглядит следующим образом:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Задание нескольких секретов

Пакет секретов можно задать с помощью конвейера `set` командной строки. В следующем примере *input.js* содержимое файла передается `set` команде.

# <a name="windows"></a>[Windows](#tab/windows)

Откройте командную оболочку и выполните следующую команду:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux и macOS](#tab/linux+macos)

Откройте командную оболочку и выполните следующую команду:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Доступ к секрету

[API настройки ASP.NET Core](xref:fundamentals/configuration/index) предоставляет доступ к секретам диспетчера секретов.

Если проект предназначен для .NET Framework, установите [Microsoft.Extensions.Configфигурации. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)Пакет NuGet усерсекретс.

В ASP.NET Core 2,0 или более поздней версии источник конфигурации секреты пользователя автоматически добавляется в режим разработки, когда проект вызывает <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> инициализацию нового экземпляра узла с предварительно настроенными по умолчанию. `CreateDefaultBuilder`вызывает <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> , если <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> имеет значение <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Если `CreateDefaultBuilder` метод не вызывается, добавьте источник конфигурации секреты пользователя явным образом, вызвав <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> в `Startup` конструкторе. Вызывайте `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Секреты пользователя можно получить с помощью `Configuration` API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Преобразование секретов в POCO

Сопоставление целого литерала объекта с POCO (простой класс .NET со свойствами) полезен для статистической обработки связанных свойств.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Чтобы связать предыдущие секреты с POCO, используйте `Configuration` функцию [привязки графа объектов](xref:fundamentals/configuration/index#bind-to-an-object-graph) API. Следующий код привязывается к пользовательской `MovieSettings` POCO и обращается к `ServiceApiKey` значению свойства:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

`Movies:ConnectionString`Секреты и `Movies:ServiceApiKey` сопоставлены с соответствующими свойствами в `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Замена строк секретами

Хранение паролей в виде обычного текста является небезопасным. Например, строка подключения к базе данных, хранимая в *appsettings.json* , может включать пароль для указанного пользователя:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Более безопасный подход заключается в хранении пароля в качестве секрета. Пример.

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Удалите `Password` пару "ключ-значение" из строки подключения в *appsettings.json*. Пример.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

Значение секрета может быть задано для <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойства объекта, чтобы завершить строку подключения:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Вывод списка секретов

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполните следующую команду из каталога, в котором существует файл *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Появится следующий результат:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

В предыдущем примере двоеточие в именах ключей обозначает иерархию объектов в *secrets.js*.

## <a name="remove-a-single-secret"></a>Удаление одного секрета

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполните следующую команду из каталога, в котором существует файл *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Файл *secrets.jsв* приложении изменен для удаления пары "ключ-значение", связанной с `MoviesConnectionString` ключом:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

При выполнении `dotnet user-secrets list` отображается следующее сообщение:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Удалить все секреты

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Выполните следующую команду из каталога, в котором существует файл *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Все секреты пользователя для приложения удалены из *secrets.jsв* файле:

```json
{}
```

При выполнении `dotnet user-secrets list` отображается следующее сообщение:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Дополнительные ресурсы

* Сведения о доступе к диспетчеру секретов из IIS см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end