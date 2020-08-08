---
title: Надежное хранение секретов приложений в разработке в ASP.NET Core
author: rick-anderson
description: Узнайте, как хранить и извлекать конфиденциальную информацию в виде секретов приложения во время разработки ASP.NET Core приложения.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/app-secrets
ms.openlocfilehash: 917e698d34a5d4b6c2c3f4737c08f1a590f5df1a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017952"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="da5a1-103">Надежное хранение секретов приложений в разработке в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da5a1-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="da5a1-104">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Kirk Ларкин](https://twitter.com/serpent5), [Даниэль Roth)](https://github.com/danroth27)и [Скотт Эдди (](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="da5a1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="da5a1-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da5a1-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="da5a1-106">В этом документе объясняются способы хранения и извлечения конфиденциальных данных во время разработки ASP.NET Core приложения на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="da5a1-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="da5a1-107">Никогда не храните пароли или другие конфиденциальные данные в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="da5a1-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="da5a1-108">Производственные секреты не должны использоваться для разработки или тестирования.</span><span class="sxs-lookup"><span data-stu-id="da5a1-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="da5a1-109">Секреты не должны развертываться вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="da5a1-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="da5a1-110">Вместо этого секреты должны быть доступны в рабочей среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т. д. Вы можете хранить и защищать тестовые и рабочие секреты Azure с помощью [поставщика конфигурации Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="da5a1-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="da5a1-111">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="da5a1-111">Environment variables</span></span>

<span data-ttu-id="da5a1-112">Переменные среды используются, чтобы избежать хранения секретов приложения в коде или в локальных файлах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="da5a1-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="da5a1-113">Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="da5a1-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="da5a1-114">Рассмотрим ASP.NET Core веб-приложение, в котором включена безопасность **отдельных учетных записей пользователей** .</span><span class="sxs-lookup"><span data-stu-id="da5a1-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="da5a1-115">Строка подключения к базе данных по умолчанию включается в *appsettings.js* проекта в файле с ключом `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="da5a1-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="da5a1-116">Строка подключения по умолчанию — для LocalDB, которая выполняется в пользовательском режиме и не требует пароля.</span><span class="sxs-lookup"><span data-stu-id="da5a1-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="da5a1-117">Во время развертывания приложения `DefaultConnection` значение ключа может быть переопределено значением переменной среды.</span><span class="sxs-lookup"><span data-stu-id="da5a1-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="da5a1-118">Переменная среды может хранить полную строку подключения с конфиденциальными учетными данными.</span><span class="sxs-lookup"><span data-stu-id="da5a1-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="da5a1-119">Переменные среды обычно хранятся в виде обычного незашифрованного текста.</span><span class="sxs-lookup"><span data-stu-id="da5a1-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="da5a1-120">Если компьютер или процесс скомпрометирован, недоверенные стороны могут получить доступ к переменным среды.</span><span class="sxs-lookup"><span data-stu-id="da5a1-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="da5a1-121">Могут потребоваться дополнительные меры для предотвращения раскрытия секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="da5a1-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="da5a1-122">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-122">Secret Manager</span></span>

<span data-ttu-id="da5a1-123">Средство диспетчера секретов сохраняет конфиденциальные данные во время разработки проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da5a1-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="da5a1-124">В этом контексте фрагмент конфиденциальных данных является секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="da5a1-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="da5a1-125">Секреты приложения хранятся в отдельном месте из дерева проекта.</span><span class="sxs-lookup"><span data-stu-id="da5a1-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="da5a1-126">Секреты приложения связаны с конкретным проектом или совместно используются в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="da5a1-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="da5a1-127">Секреты приложения не возвращаются в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="da5a1-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="da5a1-128">Диспетчер секретов не шифрует сохраненные секреты и не должен рассматриваться как доверенное хранилище.</span><span class="sxs-lookup"><span data-stu-id="da5a1-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="da5a1-129">Это только для целей разработки.</span><span class="sxs-lookup"><span data-stu-id="da5a1-129">It's for development purposes only.</span></span> <span data-ttu-id="da5a1-130">Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="da5a1-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="da5a1-131">Как работает средство диспетчера секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="da5a1-132">Диспетчер секретов абстрагирует детали реализации, например, где и как хранятся значения.</span><span class="sxs-lookup"><span data-stu-id="da5a1-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="da5a1-133">Вы можете использовать это средство, не зная сведений о реализации.</span><span class="sxs-lookup"><span data-stu-id="da5a1-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="da5a1-134">Значения хранятся в файле конфигурации JSON в папке профиля пользователя, защищенной системой, на локальном компьютере:</span><span class="sxs-lookup"><span data-stu-id="da5a1-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="da5a1-135">Windows</span><span class="sxs-lookup"><span data-stu-id="da5a1-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="da5a1-136">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="da5a1-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="da5a1-137">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="da5a1-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="da5a1-138">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="da5a1-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="da5a1-139">В приведенных выше путях файлов замените `<user_secrets_id>` значением, `UserSecretsId` указанным в файле *CSPROJ* .</span><span class="sxs-lookup"><span data-stu-id="da5a1-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="da5a1-140">Не записывайте код, который зависит от расположения или формата данных, сохраненных с помощью диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="da5a1-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="da5a1-141">Эти сведения о реализации могут измениться.</span><span class="sxs-lookup"><span data-stu-id="da5a1-141">These implementation details may change.</span></span> <span data-ttu-id="da5a1-142">Например, секретные значения не шифруются, но могут быть в будущем.</span><span class="sxs-lookup"><span data-stu-id="da5a1-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="da5a1-143">Включить хранилище секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-143">Enable secret storage</span></span>

<span data-ttu-id="da5a1-144">Диспетчер секретов работает с параметрами конфигурации конкретного проекта, хранящимися в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="da5a1-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="da5a1-145">Средство диспетчера секретов включает `init` команду в пакет SDK для .NET Core 3.0.100 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="da5a1-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="da5a1-146">Чтобы использовать секреты пользователя, выполните следующую команду в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="da5a1-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="da5a1-147">Предыдущая команда добавляет `UserSecretsId` элемент в `PropertyGroup` файл *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="da5a1-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="da5a1-148">По умолчанию внутренний текст `UserSecretsId` является идентификатором GUID.</span><span class="sxs-lookup"><span data-stu-id="da5a1-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="da5a1-149">Внутренний текст является произвольным, но уникален для проекта.</span><span class="sxs-lookup"><span data-stu-id="da5a1-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="da5a1-150">В Visual Studio щелкните правой кнопкой мыши проект в обозреватель решений и выберите в контекстном меню пункт **Управление секретами пользователя** .</span><span class="sxs-lookup"><span data-stu-id="da5a1-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="da5a1-151">Этот жест добавляет `UserSecretsId` элемент, заполненный идентификатором GUID, в *CSPROJ* -файл.</span><span class="sxs-lookup"><span data-stu-id="da5a1-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="da5a1-152">Задать секрет</span><span class="sxs-lookup"><span data-stu-id="da5a1-152">Set a secret</span></span>

<span data-ttu-id="da5a1-153">Определите секрет приложения, состоящий из ключа и его значения.</span><span class="sxs-lookup"><span data-stu-id="da5a1-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="da5a1-154">Секрет связан со `UserSecretsId` значением проекта.</span><span class="sxs-lookup"><span data-stu-id="da5a1-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="da5a1-155">Например, выполните следующую команду из каталога, в котором существует CSPROJ файл *.*</span><span class="sxs-lookup"><span data-stu-id="da5a1-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="da5a1-156">В предыдущем примере двоеточие обозначает, что `Movies` является объектным литералом со `ServiceApiKey` свойством.</span><span class="sxs-lookup"><span data-stu-id="da5a1-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="da5a1-157">Диспетчер секретов можно использовать и в других каталогах.</span><span class="sxs-lookup"><span data-stu-id="da5a1-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="da5a1-158">Используйте `--project` параметр, чтобы указать путь файловой системы, в котором существует файл *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="da5a1-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="da5a1-159">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="da5a1-160">Сведение структуры JSON в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da5a1-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="da5a1-161">Жест **управления пользовательскими секретами** в Visual Studio открывает *secrets.jsдля* файла в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="da5a1-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="da5a1-162">Замените содержимое *secrets.jsна* пары "ключ-значение", которые необходимо сохранить.</span><span class="sxs-lookup"><span data-stu-id="da5a1-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="da5a1-163">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="da5a1-164">Структура JSON преобразуется в плоскую структуру после изменений с помощью `dotnet user-secrets remove` или `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="da5a1-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="da5a1-165">Например, запуск `dotnet user-secrets remove "Movies:ConnectionString"` сворачивает `Movies` объектный литерал.</span><span class="sxs-lookup"><span data-stu-id="da5a1-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="da5a1-166">Измененный файл выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="da5a1-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="da5a1-167">Задание нескольких секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-167">Set multiple secrets</span></span>

<span data-ttu-id="da5a1-168">Пакет секретов можно задать с помощью конвейера `set` командной строки.</span><span class="sxs-lookup"><span data-stu-id="da5a1-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="da5a1-169">В следующем примере *input.js* содержимое файла передается `set` команде.</span><span class="sxs-lookup"><span data-stu-id="da5a1-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="da5a1-170">Windows</span><span class="sxs-lookup"><span data-stu-id="da5a1-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="da5a1-171">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="da5a1-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="da5a1-172">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="da5a1-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="da5a1-173">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="da5a1-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="da5a1-174">Доступ к секрету</span><span class="sxs-lookup"><span data-stu-id="da5a1-174">Access a secret</span></span>

<span data-ttu-id="da5a1-175">[API настройки ASP.NET Core](xref:fundamentals/configuration/index) предоставляет доступ к секретам диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="da5a1-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="da5a1-176">Источник конфигурации секреты пользователя автоматически добавляется в режим разработки при вызове проекта <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> для инициализации нового экземпляра узла с предварительно настроенными настройками по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="da5a1-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="da5a1-177">`CreateDefaultBuilder`вызывает <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> , если <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> имеет значение <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="da5a1-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="da5a1-178">Если `CreateDefaultBuilder` метод не вызывается, добавьте источник конфигурации user Secret явным образом, вызвав <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="da5a1-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="da5a1-179">Вызывайте `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="da5a1-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="da5a1-180">Секреты пользователя можно получить с помощью `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="da5a1-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="da5a1-181">Преобразование секретов в POCO</span><span class="sxs-lookup"><span data-stu-id="da5a1-181">Map secrets to a POCO</span></span>

<span data-ttu-id="da5a1-182">Сопоставление целого литерала объекта с POCO (простой класс .NET со свойствами) полезен для статистической обработки связанных свойств.</span><span class="sxs-lookup"><span data-stu-id="da5a1-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-183">Чтобы связать предыдущие секреты с POCO, используйте `Configuration` функцию [привязки графа объектов](xref:fundamentals/configuration/index#bind-to-an-object-graph) API.</span><span class="sxs-lookup"><span data-stu-id="da5a1-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="da5a1-184">Следующий код привязывается к пользовательской `MovieSettings` POCO и обращается к `ServiceApiKey` значению свойства:</span><span class="sxs-lookup"><span data-stu-id="da5a1-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="da5a1-185">`Movies:ConnectionString`Секреты и `Movies:ServiceApiKey` сопоставлены с соответствующими свойствами в `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="da5a1-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="da5a1-186">Замена строк секретами</span><span class="sxs-lookup"><span data-stu-id="da5a1-186">String replacement with secrets</span></span>

<span data-ttu-id="da5a1-187">Хранение паролей в виде обычного текста является небезопасным.</span><span class="sxs-lookup"><span data-stu-id="da5a1-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="da5a1-188">Например, строка подключения к базе данных, хранимая в *appsettings.json* , может включать пароль для указанного пользователя:</span><span class="sxs-lookup"><span data-stu-id="da5a1-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="da5a1-189">Более безопасный подход заключается в хранении пароля в качестве секрета.</span><span class="sxs-lookup"><span data-stu-id="da5a1-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="da5a1-190">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="da5a1-191">Удалите `Password` пару "ключ-значение" из строки подключения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="da5a1-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="da5a1-192">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="da5a1-193">Значение секрета может быть задано для <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойства объекта, чтобы завершить строку подключения:</span><span class="sxs-lookup"><span data-stu-id="da5a1-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="da5a1-194">Вывод списка секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-195">Выполните следующую команду из каталога, в котором существует файл *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="da5a1-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="da5a1-196">Появится следующий результат:</span><span class="sxs-lookup"><span data-stu-id="da5a1-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="da5a1-197">В предыдущем примере двоеточие в именах ключей обозначает иерархию объектов в *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="da5a1-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="da5a1-198">Удаление одного секрета</span><span class="sxs-lookup"><span data-stu-id="da5a1-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-199">Выполните следующую команду из каталога, в котором существует файл *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="da5a1-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="da5a1-200">Файл *secrets.jsв* приложении изменен для удаления пары "ключ-значение", связанной с `MoviesConnectionString` ключом:</span><span class="sxs-lookup"><span data-stu-id="da5a1-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="da5a1-201">`dotnet user-secrets list`отображает следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="da5a1-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="da5a1-202">Удалить все секреты</span><span class="sxs-lookup"><span data-stu-id="da5a1-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-203">Выполните следующую команду из каталога, в котором существует файл *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="da5a1-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="da5a1-204">Все секреты пользователя для приложения удалены из *secrets.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="da5a1-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="da5a1-205">При выполнении `dotnet user-secrets list` отображается следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="da5a1-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="da5a1-206">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="da5a1-206">Additional resources</span></span>

* <span data-ttu-id="da5a1-207">Сведения о доступе к диспетчеру секретов из IIS см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="da5a1-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="da5a1-208">[Рик Андерсон (](https://twitter.com/RickAndMSFT), [Даниэль Roth)](https://github.com/danroth27)и [Скотт Эдди (](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="da5a1-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="da5a1-209">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="da5a1-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="da5a1-210">В этом документе объясняются способы хранения и извлечения конфиденциальных данных во время разработки ASP.NET Core приложения на компьютере разработки.</span><span class="sxs-lookup"><span data-stu-id="da5a1-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="da5a1-211">Никогда не храните пароли или другие конфиденциальные данные в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="da5a1-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="da5a1-212">Производственные секреты не должны использоваться для разработки или тестирования.</span><span class="sxs-lookup"><span data-stu-id="da5a1-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="da5a1-213">Секреты не должны развертываться вместе с приложением.</span><span class="sxs-lookup"><span data-stu-id="da5a1-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="da5a1-214">Вместо этого секреты должны быть доступны в рабочей среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т. д. Вы можете хранить и защищать тестовые и рабочие секреты Azure с помощью [поставщика конфигурации Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="da5a1-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="da5a1-215">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="da5a1-215">Environment variables</span></span>

<span data-ttu-id="da5a1-216">Переменные среды используются, чтобы избежать хранения секретов приложения в коде или в локальных файлах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="da5a1-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="da5a1-217">Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="da5a1-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="da5a1-218">Рассмотрим ASP.NET Core веб-приложение, в котором включена безопасность **отдельных учетных записей пользователей** .</span><span class="sxs-lookup"><span data-stu-id="da5a1-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="da5a1-219">Строка подключения к базе данных по умолчанию включается в *appsettings.js* проекта в файле с ключом `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="da5a1-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="da5a1-220">Строка подключения по умолчанию — для LocalDB, которая выполняется в пользовательском режиме и не требует пароля.</span><span class="sxs-lookup"><span data-stu-id="da5a1-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="da5a1-221">Во время развертывания приложения `DefaultConnection` значение ключа может быть переопределено значением переменной среды.</span><span class="sxs-lookup"><span data-stu-id="da5a1-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="da5a1-222">Переменная среды может хранить полную строку подключения с конфиденциальными учетными данными.</span><span class="sxs-lookup"><span data-stu-id="da5a1-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="da5a1-223">Переменные среды обычно хранятся в виде обычного незашифрованного текста.</span><span class="sxs-lookup"><span data-stu-id="da5a1-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="da5a1-224">Если компьютер или процесс скомпрометирован, недоверенные стороны могут получить доступ к переменным среды.</span><span class="sxs-lookup"><span data-stu-id="da5a1-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="da5a1-225">Могут потребоваться дополнительные меры для предотвращения раскрытия секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="da5a1-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="da5a1-226">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-226">Secret Manager</span></span>

<span data-ttu-id="da5a1-227">Средство диспетчера секретов сохраняет конфиденциальные данные во время разработки проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="da5a1-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="da5a1-228">В этом контексте фрагмент конфиденциальных данных является секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="da5a1-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="da5a1-229">Секреты приложения хранятся в отдельном месте из дерева проекта.</span><span class="sxs-lookup"><span data-stu-id="da5a1-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="da5a1-230">Секреты приложения связаны с конкретным проектом или совместно используются в нескольких проектах.</span><span class="sxs-lookup"><span data-stu-id="da5a1-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="da5a1-231">Секреты приложения не возвращаются в систему управления версиями.</span><span class="sxs-lookup"><span data-stu-id="da5a1-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="da5a1-232">Диспетчер секретов не шифрует сохраненные секреты и не должен рассматриваться как доверенное хранилище.</span><span class="sxs-lookup"><span data-stu-id="da5a1-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="da5a1-233">Это только для целей разработки.</span><span class="sxs-lookup"><span data-stu-id="da5a1-233">It's for development purposes only.</span></span> <span data-ttu-id="da5a1-234">Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="da5a1-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="da5a1-235">Как работает средство диспетчера секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="da5a1-236">Диспетчер секретов абстрагирует детали реализации, например, где и как хранятся значения.</span><span class="sxs-lookup"><span data-stu-id="da5a1-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="da5a1-237">Вы можете использовать это средство, не зная сведений о реализации.</span><span class="sxs-lookup"><span data-stu-id="da5a1-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="da5a1-238">Значения хранятся в файле конфигурации JSON в папке профиля пользователя, защищенной системой, на локальном компьютере:</span><span class="sxs-lookup"><span data-stu-id="da5a1-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="da5a1-239">Windows</span><span class="sxs-lookup"><span data-stu-id="da5a1-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="da5a1-240">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="da5a1-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="da5a1-241">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="da5a1-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="da5a1-242">Путь к файловой системе:</span><span class="sxs-lookup"><span data-stu-id="da5a1-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="da5a1-243">В приведенных выше путях файлов замените `<user_secrets_id>` значением, `UserSecretsId` указанным в файле *CSPROJ* .</span><span class="sxs-lookup"><span data-stu-id="da5a1-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="da5a1-244">Не записывайте код, который зависит от расположения или формата данных, сохраненных с помощью диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="da5a1-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="da5a1-245">Эти сведения о реализации могут измениться.</span><span class="sxs-lookup"><span data-stu-id="da5a1-245">These implementation details may change.</span></span> <span data-ttu-id="da5a1-246">Например, секретные значения не шифруются, но могут быть в будущем.</span><span class="sxs-lookup"><span data-stu-id="da5a1-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="da5a1-247">Включить хранилище секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-247">Enable secret storage</span></span>

<span data-ttu-id="da5a1-248">Диспетчер секретов работает с параметрами конфигурации конкретного проекта, хранящимися в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="da5a1-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="da5a1-249">Чтобы использовать секреты пользователя, определите `UserSecretsId` элемент в `PropertyGroup` файле *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="da5a1-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="da5a1-250">Внутренний текст `UserSecretsId` является произвольным, но уникален для проекта.</span><span class="sxs-lookup"><span data-stu-id="da5a1-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="da5a1-251">Разработчики обычно создают идентификатор GUID для `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="da5a1-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="da5a1-252">В Visual Studio щелкните правой кнопкой мыши проект в обозреватель решений и выберите в контекстном меню пункт **Управление секретами пользователя** .</span><span class="sxs-lookup"><span data-stu-id="da5a1-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="da5a1-253">Этот жест добавляет `UserSecretsId` элемент, заполненный идентификатором GUID, в *CSPROJ* -файл.</span><span class="sxs-lookup"><span data-stu-id="da5a1-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="da5a1-254">Задать секрет</span><span class="sxs-lookup"><span data-stu-id="da5a1-254">Set a secret</span></span>

<span data-ttu-id="da5a1-255">Определите секрет приложения, состоящий из ключа и его значения.</span><span class="sxs-lookup"><span data-stu-id="da5a1-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="da5a1-256">Секрет связан со `UserSecretsId` значением проекта.</span><span class="sxs-lookup"><span data-stu-id="da5a1-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="da5a1-257">Например, выполните следующую команду из каталога, в котором существует CSPROJ файл *.*</span><span class="sxs-lookup"><span data-stu-id="da5a1-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="da5a1-258">В предыдущем примере двоеточие обозначает, что `Movies` является объектным литералом со `ServiceApiKey` свойством.</span><span class="sxs-lookup"><span data-stu-id="da5a1-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="da5a1-259">Диспетчер секретов можно использовать и в других каталогах.</span><span class="sxs-lookup"><span data-stu-id="da5a1-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="da5a1-260">Используйте `--project` параметр, чтобы указать путь файловой системы, в котором существует файл *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="da5a1-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="da5a1-261">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="da5a1-262">Сведение структуры JSON в Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da5a1-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="da5a1-263">Жест **управления пользовательскими секретами** в Visual Studio открывает *secrets.jsдля* файла в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="da5a1-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="da5a1-264">Замените содержимое *secrets.jsна* пары "ключ-значение", которые необходимо сохранить.</span><span class="sxs-lookup"><span data-stu-id="da5a1-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="da5a1-265">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="da5a1-266">Структура JSON преобразуется в плоскую структуру после изменений с помощью `dotnet user-secrets remove` или `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="da5a1-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="da5a1-267">Например, запуск `dotnet user-secrets remove "Movies:ConnectionString"` сворачивает `Movies` объектный литерал.</span><span class="sxs-lookup"><span data-stu-id="da5a1-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="da5a1-268">Измененный файл выглядит следующим образом:</span><span class="sxs-lookup"><span data-stu-id="da5a1-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="da5a1-269">Задание нескольких секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-269">Set multiple secrets</span></span>

<span data-ttu-id="da5a1-270">Пакет секретов можно задать с помощью конвейера `set` командной строки.</span><span class="sxs-lookup"><span data-stu-id="da5a1-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="da5a1-271">В следующем примере *input.js* содержимое файла передается `set` команде.</span><span class="sxs-lookup"><span data-stu-id="da5a1-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="da5a1-272">Windows</span><span class="sxs-lookup"><span data-stu-id="da5a1-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="da5a1-273">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="da5a1-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="da5a1-274">Linux и macOS</span><span class="sxs-lookup"><span data-stu-id="da5a1-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="da5a1-275">Откройте командную оболочку и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="da5a1-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="da5a1-276">Доступ к секрету</span><span class="sxs-lookup"><span data-stu-id="da5a1-276">Access a secret</span></span>

<span data-ttu-id="da5a1-277">[API настройки ASP.NET Core](xref:fundamentals/configuration/index) предоставляет доступ к секретам диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="da5a1-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="da5a1-278">Если проект предназначен для .NET Framework, установите [Microsoft.Extensions.Configфигурации. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets)Пакет NuGet усерсекретс.</span><span class="sxs-lookup"><span data-stu-id="da5a1-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="da5a1-279">В ASP.NET Core 2,0 или более поздней версии источник конфигурации секреты пользователя автоматически добавляется в режим разработки, когда проект вызывает <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> инициализацию нового экземпляра узла с предварительно настроенными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="da5a1-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="da5a1-280">`CreateDefaultBuilder`вызывает <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> , если <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> имеет значение <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="da5a1-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="da5a1-281">Если `CreateDefaultBuilder` метод не вызывается, добавьте источник конфигурации секреты пользователя явным образом, вызвав <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> в `Startup` конструкторе.</span><span class="sxs-lookup"><span data-stu-id="da5a1-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="da5a1-282">Вызывайте `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="da5a1-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="da5a1-283">Секреты пользователя можно получить с помощью `Configuration` API:</span><span class="sxs-lookup"><span data-stu-id="da5a1-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="da5a1-284">Преобразование секретов в POCO</span><span class="sxs-lookup"><span data-stu-id="da5a1-284">Map secrets to a POCO</span></span>

<span data-ttu-id="da5a1-285">Сопоставление целого литерала объекта с POCO (простой класс .NET со свойствами) полезен для статистической обработки связанных свойств.</span><span class="sxs-lookup"><span data-stu-id="da5a1-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-286">Чтобы связать предыдущие секреты с POCO, используйте `Configuration` функцию [привязки графа объектов](xref:fundamentals/configuration/index#bind-to-an-object-graph) API.</span><span class="sxs-lookup"><span data-stu-id="da5a1-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="da5a1-287">Следующий код привязывается к пользовательской `MovieSettings` POCO и обращается к `ServiceApiKey` значению свойства:</span><span class="sxs-lookup"><span data-stu-id="da5a1-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="da5a1-288">`Movies:ConnectionString`Секреты и `Movies:ServiceApiKey` сопоставлены с соответствующими свойствами в `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="da5a1-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="da5a1-289">Замена строк секретами</span><span class="sxs-lookup"><span data-stu-id="da5a1-289">String replacement with secrets</span></span>

<span data-ttu-id="da5a1-290">Хранение паролей в виде обычного текста является небезопасным.</span><span class="sxs-lookup"><span data-stu-id="da5a1-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="da5a1-291">Например, строка подключения к базе данных, хранимая в *appsettings.json* , может включать пароль для указанного пользователя:</span><span class="sxs-lookup"><span data-stu-id="da5a1-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="da5a1-292">Более безопасный подход заключается в хранении пароля в качестве секрета.</span><span class="sxs-lookup"><span data-stu-id="da5a1-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="da5a1-293">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="da5a1-294">Удалите `Password` пару "ключ-значение" из строки подключения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="da5a1-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="da5a1-295">Например:</span><span class="sxs-lookup"><span data-stu-id="da5a1-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="da5a1-296">Значение секрета может быть задано для <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойства объекта, чтобы завершить строку подключения:</span><span class="sxs-lookup"><span data-stu-id="da5a1-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="da5a1-297">Вывод списка секретов</span><span class="sxs-lookup"><span data-stu-id="da5a1-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-298">Выполните следующую команду из каталога, в котором существует файл *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="da5a1-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="da5a1-299">Появится следующий результат:</span><span class="sxs-lookup"><span data-stu-id="da5a1-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="da5a1-300">В предыдущем примере двоеточие в именах ключей обозначает иерархию объектов в *secrets.js*.</span><span class="sxs-lookup"><span data-stu-id="da5a1-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="da5a1-301">Удаление одного секрета</span><span class="sxs-lookup"><span data-stu-id="da5a1-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-302">Выполните следующую команду из каталога, в котором существует файл *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="da5a1-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="da5a1-303">Файл *secrets.jsв* приложении изменен для удаления пары "ключ-значение", связанной с `MoviesConnectionString` ключом:</span><span class="sxs-lookup"><span data-stu-id="da5a1-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="da5a1-304">При выполнении `dotnet user-secrets list` отображается следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="da5a1-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="da5a1-305">Удалить все секреты</span><span class="sxs-lookup"><span data-stu-id="da5a1-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="da5a1-306">Выполните следующую команду из каталога, в котором существует файл *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="da5a1-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="da5a1-307">Все секреты пользователя для приложения удалены из *secrets.jsв* файле:</span><span class="sxs-lookup"><span data-stu-id="da5a1-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="da5a1-308">При выполнении `dotnet user-secrets list` отображается следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="da5a1-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="da5a1-309">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="da5a1-309">Additional resources</span></span>

* <span data-ttu-id="da5a1-310">Сведения о доступе к диспетчеру секретов из IIS см. в [этой статье](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="da5a1-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end