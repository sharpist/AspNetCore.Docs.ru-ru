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
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="5dcf6-103">Безопасное хранение секретов приложений в разработке в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5dcf6-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5dcf6-104">[Рик Андерсон](https://twitter.com/RickAndMSFT), [Кирк Ларкин](https://twitter.com/serpent5), [Даниэль Рот](https://github.com/danroth27), и [Скотт Эдди](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5dcf6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5dcf6-105">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5dcf6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5dcf6-106">В этом документе разъясняется методы хранения и извлечения конфиденциальных данных при разработке приложения ASP.NET Core на машине разработки.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5dcf6-107">Никогда не храните пароли или другие конфиденциальные данные в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5dcf6-108">Производственные секреты не должны использоваться для разработки или тестирования.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5dcf6-109">Секреты не должны быть развернуты с приложением.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5dcf6-110">Вместо этого секреты должны быть доступны в производственной среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т.д. Вы можете хранить и защищать секреты тестирования и производства Azure с [помощью поставщика конфигураций Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="5dcf6-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5dcf6-111">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="5dcf6-111">Environment variables</span></span>

<span data-ttu-id="5dcf6-112">Переменные среды используются для предотвращения хранения секретов приложений в коде или в локальных файлах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5dcf6-113">Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5dcf6-114">Рассмотрим веб-приложение ASP.NET Core Core, в котором включена безопасность **индивидуальных учетных записей пользователей.**</span><span class="sxs-lookup"><span data-stu-id="5dcf6-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5dcf6-115">Строка подключения базы данных по умолчанию включена в файл `DefaultConnection` *appsettings.json* проекта с ключом.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5dcf6-116">Строка подключения по умолчанию предназначена для LocalDB, которая работает в пользовательском режиме и не требует пароля.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5dcf6-117">При развертывании `DefaultConnection` приложения ключевое значение может быть переопределено значением переменной среды.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5dcf6-118">Переменная среды может хранить полную строку соединения с конфиденциальными учетными данными.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5dcf6-119">Переменные среды обычно хранятся в простом, незашифрованном тексте.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5dcf6-120">Если машина или процесс скомпрометированы, переменные среды могут быть доступны ненадежным сторонам.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5dcf6-121">Могут потребоваться дополнительные меры по предотвращению разглашения секретов пользователей.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5dcf6-122">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="5dcf6-122">Secret Manager</span></span>

<span data-ttu-id="5dcf6-123">Инструмент Secret Manager хранит конфиденциальные данные во время разработки проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5dcf6-124">В этом контексте часть конфиденциальных данных является секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5dcf6-125">Секреты приложений хранятся в отдельном месте от дерева проекта.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5dcf6-126">Секреты приложения связаны с конкретным проектом или совместно используются несколькими проектами.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5dcf6-127">Секреты приложения не проверяются в исходном управлении.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5dcf6-128">Инструмент Secret Manager не шифрует сохраненные секреты и не должен рассматриваться как надежный магазин.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5dcf6-129">Это только для целей развития.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-129">It's for development purposes only.</span></span> <span data-ttu-id="5dcf6-130">Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5dcf6-131">Как работает инструмент Secret Manager</span><span class="sxs-lookup"><span data-stu-id="5dcf6-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="5dcf6-132">Инструмент Secret Manager абстрагирует детали реализации, например, где и как хранятся значения.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5dcf6-133">Вы можете использовать инструмент, не зная этих деталей реализации.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5dcf6-134">Значения хранятся в файле конфигурации JSON в защищенной системой папке профиля пользователя на локальной машине:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5dcf6-135">Windows</span><span class="sxs-lookup"><span data-stu-id="5dcf6-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="5dcf6-136">Путь файловой системы:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5dcf6-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5dcf6-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5dcf6-138">Путь файловой системы:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5dcf6-139">В предыдущих путях `<user_secrets_id>` файла замените `UserSecretsId` значение, указанное в файле *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5dcf6-140">Не пишите код, который зависит от местоположения или формата данных, сохраненных с помощью инструмента Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5dcf6-141">Эти детали реализации могут измениться.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-141">These implementation details may change.</span></span> <span data-ttu-id="5dcf6-142">Например, секретные значения не зашифрованы, но могут быть в будущем.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5dcf6-143">Включить секретное хранилище</span><span class="sxs-lookup"><span data-stu-id="5dcf6-143">Enable secret storage</span></span>

<span data-ttu-id="5dcf6-144">Инструмент Secret Manager работает на настройках конфигурации, хранящихся в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5dcf6-145">Инструмент Secret Manager `init` включает команду в .NET Core SDK 3.0.100 или позже.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="5dcf6-146">Чтобы использовать секреты пользователя, запустите следующую команду в каталоге проекта:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="5dcf6-147">Предыдущая команда `UserSecretsId` добавляет элемент `PropertyGroup` в файл *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5dcf6-148">По умолчанию внутренний `UserSecretsId` текст является GUID.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="5dcf6-149">Внутренний текст является произвольным, но уникальным для проекта.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="5dcf6-150">В Visual Studio нажмите правой кнопкой мыши на проект в Solution Explorer и выберите **«Управляйте секретами пользователя»** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5dcf6-151">Этот жест `UserSecretsId` добавляет элемент, населенный GUID, в файл *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5dcf6-152">Установить секрет</span><span class="sxs-lookup"><span data-stu-id="5dcf6-152">Set a secret</span></span>

<span data-ttu-id="5dcf6-153">Определите секрет приложения, состоящий из ключа и его ценности.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5dcf6-154">Секрет связан со `UserSecretsId` стоимостью проекта.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5dcf6-155">Например, выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5dcf6-156">В предыдущем примере толстая `Movies` кишка обозначает `ServiceApiKey` буквальный объект с свойством.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5dcf6-157">Инструмент Secret Manager может быть использован и в других каталогах.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5dcf6-158">Используйте `--project` опцию для предоставления пути файловой системы, на котором существует файл *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5dcf6-159">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5dcf6-160">Структура JSON уплощение в Визуальной студии</span><span class="sxs-lookup"><span data-stu-id="5dcf6-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5dcf6-161">Visual Studio's **Manage User Secrets** жест открывает *secrets.json* файл в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5dcf6-162">Замените содержимое *secrets.json* парами с ключевымзначением, которые будут храниться.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5dcf6-163">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5dcf6-164">Структура JSON сплющена после `dotnet user-secrets remove` модификаций через или `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5dcf6-165">Например, `dotnet user-secrets remove "Movies:ConnectionString"` бег разрушает `Movies` объект буквально.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5dcf6-166">Измененный файл напоминает следующее:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5dcf6-167">Установить несколько секретов</span><span class="sxs-lookup"><span data-stu-id="5dcf6-167">Set multiple secrets</span></span>

<span data-ttu-id="5dcf6-168">Пакет секретов можно установить, подав `set` JSON в команду.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5dcf6-169">В следующем примере содержимое файла *input.json* передается в `set` команду.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5dcf6-170">Windows</span><span class="sxs-lookup"><span data-stu-id="5dcf6-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="5dcf6-171">Откройте командный корпус и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5dcf6-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5dcf6-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5dcf6-173">Откройте командный корпус и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5dcf6-174">Доступ к секрету</span><span class="sxs-lookup"><span data-stu-id="5dcf6-174">Access a secret</span></span>

<span data-ttu-id="5dcf6-175">[API ASP.NET Core Configuration](xref:fundamentals/configuration/index) предоставляет доступ к секретам секретного менеджера.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5dcf6-176">Источник конфигурации пользовательских секретов автоматически добавляется <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> в режиме разработки, когда проект требует инициализировать новый экземпляр узла с преднастроенными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5dcf6-177">`CreateDefaultBuilder`звонки, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> когда <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>это:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="5dcf6-178">Когда `CreateDefaultBuilder` не вызывается, добавить источник конфигурации <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>пользовательских секретов явно, позвонив.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="5dcf6-179">Звоните `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="5dcf6-180">Секреты пользователя могут быть `Configuration` получены через API:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5dcf6-181">Карта секреты POCO</span><span class="sxs-lookup"><span data-stu-id="5dcf6-181">Map secrets to a POCO</span></span>

<span data-ttu-id="5dcf6-182">Отображение всего объекта в буквальном смысле к POCO (простой класс .NET со свойствами) полезно для агрегирования связанных свойств.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-183">Чтобы сопоставить предыдущие секреты с `Configuration` POCO, используйте функцию [связывания графика объекта](xref:fundamentals/configuration/index#bind-to-an-object-graph) API.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5dcf6-184">Следующий код связывается `MovieSettings` с пользовательским POCO и получает доступ к значению `ServiceApiKey` свойства:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5dcf6-185">`Movies:ConnectionString` Секреты `Movies:ServiceApiKey` и секреты отображаются в соответствующих свойствах в: `MovieSettings`</span><span class="sxs-lookup"><span data-stu-id="5dcf6-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5dcf6-186">Замена строки секретами</span><span class="sxs-lookup"><span data-stu-id="5dcf6-186">String replacement with secrets</span></span>

<span data-ttu-id="5dcf6-187">Хранение паролей в простом тексте является небезопасным.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5dcf6-188">Например, строка подключения к базе данных, хранящаяся в *appsettings.json,* может включать пароль для указанного пользователя:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5dcf6-189">Более безопасный подход заключается в хранении пароля в качестве секрета.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5dcf6-190">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5dcf6-191">Удалите пару `Password` значения ключа из строки соединения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5dcf6-192">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5dcf6-193">Значение секрета может быть установлено <xref:System.Data.SqlClient.SqlConnectionStringBuilder> на <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойстве объекта для завершения строки соединения:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5dcf6-194">Перечислите секреты</span><span class="sxs-lookup"><span data-stu-id="5dcf6-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-195">Выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5dcf6-196">Появится следующий результат:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5dcf6-197">В предыдущем примере толстая кишка в ключевых именах обозначает иерархию объектов в *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5dcf6-198">Удалить один секрет</span><span class="sxs-lookup"><span data-stu-id="5dcf6-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-199">Выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5dcf6-200">Файл *secrets.json* приложения был изменен, чтобы удалить пару ключей, связанную с ключом: `MoviesConnectionString`</span><span class="sxs-lookup"><span data-stu-id="5dcf6-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5dcf6-201">`dotnet user-secrets list`отображает следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5dcf6-202">Удалить все секреты</span><span class="sxs-lookup"><span data-stu-id="5dcf6-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-203">Выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5dcf6-204">Все секреты пользователя приложения были удалены из файла *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5dcf6-205">Запуск `dotnet user-secrets list` отображает следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5dcf6-206">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5dcf6-206">Additional resources</span></span>

* <span data-ttu-id="5dcf6-207">Ознакомьтесь с [этой проблемой](https://github.com/dotnet/AspNetCore.Docs/issues/16328) для получения информации о доступе к Секретному менеджеру из IIS.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5dcf6-208">[Рик Андерсон](https://twitter.com/RickAndMSFT), [Даниэль Рот](https://github.com/danroth27), и Скотт [Эдди](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="5dcf6-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="5dcf6-209">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5dcf6-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5dcf6-210">В этом документе разъясняется методы хранения и извлечения конфиденциальных данных при разработке приложения ASP.NET Core на машине разработки.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="5dcf6-211">Никогда не храните пароли или другие конфиденциальные данные в исходном коде.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="5dcf6-212">Производственные секреты не должны использоваться для разработки или тестирования.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="5dcf6-213">Секреты не должны быть развернуты с приложением.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="5dcf6-214">Вместо этого секреты должны быть доступны в производственной среде с помощью контролируемых средств, таких как переменные среды, Azure Key Vault и т.д. Вы можете хранить и защищать секреты тестирования и производства Azure с [помощью поставщика конфигураций Azure Key Vault.](xref:security/key-vault-configuration)</span><span class="sxs-lookup"><span data-stu-id="5dcf6-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="5dcf6-215">Переменные среды</span><span class="sxs-lookup"><span data-stu-id="5dcf6-215">Environment variables</span></span>

<span data-ttu-id="5dcf6-216">Переменные среды используются для предотвращения хранения секретов приложений в коде или в локальных файлах конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="5dcf6-217">Переменные среды переопределяют значения конфигурации для всех ранее указанных источников конфигурации.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="5dcf6-218">Рассмотрим веб-приложение ASP.NET Core Core, в котором включена безопасность **индивидуальных учетных записей пользователей.**</span><span class="sxs-lookup"><span data-stu-id="5dcf6-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="5dcf6-219">Строка подключения базы данных по умолчанию включена в файл `DefaultConnection` *appsettings.json* проекта с ключом.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="5dcf6-220">Строка подключения по умолчанию предназначена для LocalDB, которая работает в пользовательском режиме и не требует пароля.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="5dcf6-221">При развертывании `DefaultConnection` приложения ключевое значение может быть переопределено значением переменной среды.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="5dcf6-222">Переменная среды может хранить полную строку соединения с конфиденциальными учетными данными.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="5dcf6-223">Переменные среды обычно хранятся в простом, незашифрованном тексте.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="5dcf6-224">Если машина или процесс скомпрометированы, переменные среды могут быть доступны ненадежным сторонам.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="5dcf6-225">Могут потребоваться дополнительные меры по предотвращению разглашения секретов пользователей.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="5dcf6-226">Диспетчер секретов</span><span class="sxs-lookup"><span data-stu-id="5dcf6-226">Secret Manager</span></span>

<span data-ttu-id="5dcf6-227">Инструмент Secret Manager хранит конфиденциальные данные во время разработки проекта ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="5dcf6-228">В этом контексте часть конфиденциальных данных является секретом приложения.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="5dcf6-229">Секреты приложений хранятся в отдельном месте от дерева проекта.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="5dcf6-230">Секреты приложения связаны с конкретным проектом или совместно используются несколькими проектами.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="5dcf6-231">Секреты приложения не проверяются в исходном управлении.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="5dcf6-232">Инструмент Secret Manager не шифрует сохраненные секреты и не должен рассматриваться как надежный магазин.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="5dcf6-233">Это только для целей развития.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-233">It's for development purposes only.</span></span> <span data-ttu-id="5dcf6-234">Ключи и значения хранятся в файле конфигурации JSON в каталоге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="5dcf6-235">Как работает инструмент Secret Manager</span><span class="sxs-lookup"><span data-stu-id="5dcf6-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="5dcf6-236">Инструмент Secret Manager абстрагирует детали реализации, например, где и как хранятся значения.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="5dcf6-237">Вы можете использовать инструмент, не зная этих деталей реализации.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="5dcf6-238">Значения хранятся в файле конфигурации JSON в защищенной системой папке профиля пользователя на локальной машине:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="5dcf6-239">Windows</span><span class="sxs-lookup"><span data-stu-id="5dcf6-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="5dcf6-240">Путь файловой системы:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="5dcf6-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5dcf6-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5dcf6-242">Путь файловой системы:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="5dcf6-243">В предыдущих путях `<user_secrets_id>` файла замените `UserSecretsId` значение, указанное в файле *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="5dcf6-244">Не пишите код, который зависит от местоположения или формата данных, сохраненных с помощью инструмента Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="5dcf6-245">Эти детали реализации могут измениться.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-245">These implementation details may change.</span></span> <span data-ttu-id="5dcf6-246">Например, секретные значения не зашифрованы, но могут быть в будущем.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="5dcf6-247">Включить секретное хранилище</span><span class="sxs-lookup"><span data-stu-id="5dcf6-247">Enable secret storage</span></span>

<span data-ttu-id="5dcf6-248">Инструмент Secret Manager работает на настройках конфигурации, хранящихся в профиле пользователя.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="5dcf6-249">Чтобы использовать секреты `UserSecretsId` пользователя, `PropertyGroup` определите элемент в файле *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="5dcf6-250">Внутренний `UserSecretsId` текст является произвольным, но является уникальным для проекта.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="5dcf6-251">Разработчики обычно генерируют `UserSecretsId`GUID для .</span><span class="sxs-lookup"><span data-stu-id="5dcf6-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="5dcf6-252">В Visual Studio нажмите правой кнопкой мыши на проект в Solution Explorer и выберите **«Управляйте секретами пользователя»** из контекстного меню.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="5dcf6-253">Этот жест `UserSecretsId` добавляет элемент, населенный GUID, в файл *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="5dcf6-254">Установить секрет</span><span class="sxs-lookup"><span data-stu-id="5dcf6-254">Set a secret</span></span>

<span data-ttu-id="5dcf6-255">Определите секрет приложения, состоящий из ключа и его ценности.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="5dcf6-256">Секрет связан со `UserSecretsId` стоимостью проекта.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="5dcf6-257">Например, выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="5dcf6-258">В предыдущем примере толстая `Movies` кишка обозначает `ServiceApiKey` буквальный объект с свойством.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="5dcf6-259">Инструмент Secret Manager может быть использован и в других каталогах.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="5dcf6-260">Используйте `--project` опцию для предоставления пути файловой системы, на котором существует файл *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="5dcf6-261">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="5dcf6-262">Структура JSON уплощение в Визуальной студии</span><span class="sxs-lookup"><span data-stu-id="5dcf6-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="5dcf6-263">Visual Studio's **Manage User Secrets** жест открывает *secrets.json* файл в текстовом редакторе.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="5dcf6-264">Замените содержимое *secrets.json* парами с ключевымзначением, которые будут храниться.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="5dcf6-265">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5dcf6-266">Структура JSON сплющена после `dotnet user-secrets remove` модификаций через или `dotnet user-secrets set`.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="5dcf6-267">Например, `dotnet user-secrets remove "Movies:ConnectionString"` бег разрушает `Movies` объект буквально.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="5dcf6-268">Измененный файл напоминает следующее:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="5dcf6-269">Установить несколько секретов</span><span class="sxs-lookup"><span data-stu-id="5dcf6-269">Set multiple secrets</span></span>

<span data-ttu-id="5dcf6-270">Пакет секретов можно установить, подав `set` JSON в команду.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="5dcf6-271">В следующем примере содержимое файла *input.json* передается в `set` команду.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="5dcf6-272">Windows</span><span class="sxs-lookup"><span data-stu-id="5dcf6-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="5dcf6-273">Откройте командный корпус и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="5dcf6-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="5dcf6-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="5dcf6-275">Откройте командный корпус и выполните следующую команду:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="5dcf6-276">Доступ к секрету</span><span class="sxs-lookup"><span data-stu-id="5dcf6-276">Access a secret</span></span>

<span data-ttu-id="5dcf6-277">[API ASP.NET Core Configuration](xref:fundamentals/configuration/index) предоставляет доступ к секретам секретного менеджера.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="5dcf6-278">Если ваш проект нацелен на платформу .NET, установите пакет [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="5dcf6-279">В ASP.NET Core 2.0 или позже, источник конфигурации пользовательских <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> секретов автоматически добавляется в режиме разработки, когда проект требует инициализировать новый экземпляр узла с предварительно настроенными по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="5dcf6-280">`CreateDefaultBuilder`звонки, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> когда <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>это:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="5dcf6-281">Когда `CreateDefaultBuilder` не вызывается, добавить источник конфигурации <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> пользовательских `Startup` секретов явно, позвонив в конструкторе.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="5dcf6-282">Звоните `AddUserSecrets` только при запуске приложения в среде разработки, как показано в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="5dcf6-283">Секреты пользователя могут быть `Configuration` получены через API:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="5dcf6-284">Карта секреты POCO</span><span class="sxs-lookup"><span data-stu-id="5dcf6-284">Map secrets to a POCO</span></span>

<span data-ttu-id="5dcf6-285">Отображение всего объекта в буквальном смысле к POCO (простой класс .NET со свойствами) полезно для агрегирования связанных свойств.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-286">Чтобы сопоставить предыдущие секреты с `Configuration` POCO, используйте функцию [связывания графика объекта](xref:fundamentals/configuration/index#bind-to-an-object-graph) API.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="5dcf6-287">Следующий код связывается `MovieSettings` с пользовательским POCO и получает доступ к значению `ServiceApiKey` свойства:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="5dcf6-288">`Movies:ConnectionString` Секреты `Movies:ServiceApiKey` и секреты отображаются в соответствующих свойствах в: `MovieSettings`</span><span class="sxs-lookup"><span data-stu-id="5dcf6-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="5dcf6-289">Замена строки секретами</span><span class="sxs-lookup"><span data-stu-id="5dcf6-289">String replacement with secrets</span></span>

<span data-ttu-id="5dcf6-290">Хранение паролей в простом тексте является небезопасным.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="5dcf6-291">Например, строка подключения к базе данных, хранящаяся в *appsettings.json,* может включать пароль для указанного пользователя:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="5dcf6-292">Более безопасный подход заключается в хранении пароля в качестве секрета.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="5dcf6-293">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="5dcf6-294">Удалите пару `Password` значения ключа из строки соединения в *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="5dcf6-295">Пример:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="5dcf6-296">Значение секрета может быть установлено <xref:System.Data.SqlClient.SqlConnectionStringBuilder> на <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> свойстве объекта для завершения строки соединения:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="5dcf6-297">Перечислите секреты</span><span class="sxs-lookup"><span data-stu-id="5dcf6-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-298">Выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="5dcf6-299">Появится следующий результат:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="5dcf6-300">В предыдущем примере толстая кишка в ключевых именах обозначает иерархию объектов в *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="5dcf6-301">Удалить один секрет</span><span class="sxs-lookup"><span data-stu-id="5dcf6-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-302">Выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="5dcf6-303">Файл *secrets.json* приложения был изменен, чтобы удалить пару ключей, связанную с ключом: `MoviesConnectionString`</span><span class="sxs-lookup"><span data-stu-id="5dcf6-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="5dcf6-304">Запуск `dotnet user-secrets list` отображает следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="5dcf6-305">Удалить все секреты</span><span class="sxs-lookup"><span data-stu-id="5dcf6-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="5dcf6-306">Выполнить следующую команду из каталога, в котором существует файл *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="5dcf6-307">Все секреты пользователя приложения были удалены из файла *secrets.json:*</span><span class="sxs-lookup"><span data-stu-id="5dcf6-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="5dcf6-308">Запуск `dotnet user-secrets list` отображает следующее сообщение:</span><span class="sxs-lookup"><span data-stu-id="5dcf6-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="5dcf6-309">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="5dcf6-309">Additional resources</span></span>

* <span data-ttu-id="5dcf6-310">Ознакомьтесь с [этой проблемой](https://github.com/dotnet/AspNetCore.Docs/issues/16328) для получения информации о доступе к Секретному менеджеру из IIS.</span><span class="sxs-lookup"><span data-stu-id="5dcf6-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end