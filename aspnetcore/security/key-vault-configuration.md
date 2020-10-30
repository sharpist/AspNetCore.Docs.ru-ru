---
title: Поставщик конфигурации Azure Key Vault в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать поставщик конфигурации Azure Key Vault для настройки приложения с помощью пар "имя-значение", загружаемых во время выполнения.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/key-vault-configuration
ms.openlocfilehash: 10a949831c180f51bc6bb9b8294150a558f9343c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060135"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="d3d52-103">Поставщик конфигурации Azure Key Vault в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d3d52-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="d3d52-104">[Эндрю Стантон-медперсонала](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="d3d52-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3d52-105">В этом документе объясняется, как использовать поставщик конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложения из Azure Key Vault секреты.</span><span class="sxs-lookup"><span data-stu-id="d3d52-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="d3d52-106">Azure Key Vault — это облачная служба, которая помогает защитить криптографические ключи и секреты, используемые приложениями и службами.</span><span class="sxs-lookup"><span data-stu-id="d3d52-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="d3d52-107">Распространенные сценарии использования Azure Key Vault с приложениями ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d3d52-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="d3d52-108">Управление доступом к конфиденциальным данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d3d52-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="d3d52-109">Соблюдайте требования для FIPS 140-2 уровня 2 проверенных аппаратных модулей безопасности (HSM) при хранении данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d3d52-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="d3d52-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3d52-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="d3d52-111">Пакеты</span><span class="sxs-lookup"><span data-stu-id="d3d52-111">Packages</span></span>

<span data-ttu-id="d3d52-112">Добавьте ссылку на пакет в [Microsoft.Extensions.Configфигурации. Пакет AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="d3d52-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="d3d52-113">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="d3d52-113">Sample app</span></span>

<span data-ttu-id="d3d52-114">Пример приложения выполняется в одном из двух режимов, определяемых `#define` инструкцией в верхней части файла *Program.CS* :</span><span class="sxs-lookup"><span data-stu-id="d3d52-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="d3d52-115">`Certificate`: Демонстрируется использование идентификатора клиента Azure Key Vault и сертификата X. 509 для доступа к секретам, хранящимся в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d3d52-115">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="d3d52-116">Эту версию примера можно запустить из любого расположения, развернуть в службе приложений Azure или на любом узле, способном обслуживать ASP.NET Coreное приложение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="d3d52-117">`Managed`: Демонстрируется использование [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для проверки подлинности приложения в Azure Key Vault с проверкой подлинности Azure AD без учетных данных, хранящихся в коде или конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-117">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="d3d52-118">При использовании управляемых удостоверений для проверки подлинности не требуется идентификатор и пароль приложения Azure AD (секрет клиента).</span><span class="sxs-lookup"><span data-stu-id="d3d52-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="d3d52-119">`Managed`Версия примера должна быть развернута в Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="d3d52-120">Следуйте указаниям в разделе [Использование управляемых удостоверений для ресурсов Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="d3d52-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="d3d52-121">Дополнительные сведения о настройке примера приложения с помощью директив препроцессора ( `#define` ) см. в разделе <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="d3d52-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="d3d52-122">Секретное хранилище в среде разработки</span><span class="sxs-lookup"><span data-stu-id="d3d52-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="d3d52-123">Локальное задание секретов с помощью [средства диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d3d52-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d3d52-124">Когда пример приложения выполняется на локальном компьютере в среде разработки, секреты загружаются из локального хранилища диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="d3d52-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="d3d52-125">Для средства диспетчера секретов требуется `<UserSecretsId>` свойство в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="d3d52-126">Задайте для свойства значение ( `{GUID}` ) любой уникальный GUID:</span><span class="sxs-lookup"><span data-stu-id="d3d52-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="d3d52-127">Секреты создаются как пары "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="d3d52-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="d3d52-128">Иерархические значения (разделы конфигурации) используют `:` (двоеточие) в качестве разделителя в ASP.NET Core именах ключей [конфигурации](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="d3d52-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="d3d52-129">Диспетчер секретов используется из командной оболочки, открытой в [корне содержимого](xref:fundamentals/index#content-root)проекта, где — это `{SECRET NAME}` имя, а `{SECRET VALUE}` — значение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="d3d52-130">Выполните следующие команды в командной оболочке из [корневого каталога содержимого](xref:fundamentals/index#content-root) проекта, чтобы задать секреты для примера приложения:</span><span class="sxs-lookup"><span data-stu-id="d3d52-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="d3d52-131">Если эти секреты хранятся в Azure Key Vault в [хранилище секретов в рабочей среде с Azure Key Vaultным](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом, `_dev` суффикс изменяется на `_prod` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="d3d52-132">Суффикс предоставляет визуальную подсказку в выходных данных приложения, указывающую источник значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d3d52-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="d3d52-133">Секретное хранилище в рабочей среде с Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="d3d52-134">Инструкции, приведенные в [кратком руководстве по установке и извлечению секретов из Azure Key Vault с помощью Azure CLI](/azure/key-vault/quick-create-cli) разделе, приведены здесь для создания Azure Key Vault и хранения секретов, используемых в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="d3d52-135">Дополнительные сведения см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="d3d52-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="d3d52-136">Откройте Azure Cloud Shell одним из следующих способов в [портал Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="d3d52-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="d3d52-137">Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом.</span><span class="sxs-lookup"><span data-stu-id="d3d52-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="d3d52-138">Используйте строку поиска "Azure CLI" в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="d3d52-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="d3d52-139">Откройте Cloud Shell в браузере с помощью кнопки **запустить Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="d3d52-140">Нажмите кнопку **Cloud Shell** в меню в правом верхнем углу портал Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="d3d52-141">Дополнительные сведения см. в разделе [Azure CLI](/cli/azure/) и [Обзор Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="d3d52-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="d3d52-142">Если вы еще не прошли проверку подлинности, выполните вход с помощью `az login` команды.</span><span class="sxs-lookup"><span data-stu-id="d3d52-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="d3d52-143">Создайте группу ресурсов с помощью следующей команды, где `{RESOURCE GROUP NAME}` — это имя группы ресурсов для новой группы ресурсов, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="d3d52-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d3d52-144">Создайте хранилище ключей в группе ресурсов с помощью следующей команды, где `{KEY VAULT NAME}` — это имя нового хранилища ключей, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="d3d52-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d3d52-145">Создайте секреты в хранилище ключей в виде пар "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="d3d52-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="d3d52-146">Azure Key Vault имена секретов ограничены буквенно-цифровыми символами и тире.</span><span class="sxs-lookup"><span data-stu-id="d3d52-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="d3d52-147">Иерархические значения (разделы конфигурации) используют `--` (два тире) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d3d52-148">Двоеточия, которые обычно используются для разделения раздела из подраздела в [ASP.NET Core конфигурации](xref:fundamentals/configuration/index), не допускаются в именах секретов хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="d3d52-149">Поэтому при загрузке секретов в конфигурацию приложения используются два дефиса и они переключаются на двоеточие.</span><span class="sxs-lookup"><span data-stu-id="d3d52-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="d3d52-150">Следующие секреты предназначены для использования с примером приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="d3d52-151">Эти значения включают `_prod` суффикс, чтобы отличить `_dev` значения суффиксов, загруженных в среде разработки, от секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="d3d52-152">Замените на `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем шаге:</span><span class="sxs-lookup"><span data-stu-id="d3d52-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="d3d52-153">Использование идентификатора приложения и сертификата X. 509 для приложений, не размещенных в Azure</span><span class="sxs-lookup"><span data-stu-id="d3d52-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="d3d52-154">Настройте Azure AD, Azure Key Vault и приложение для использования идентификатора Azure Active Directory приложения и сертификата X. 509 для проверки подлинности в хранилище ключей, **Если приложение размещено за пределами Azure** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="d3d52-155">См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="d3d52-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="d3d52-156">Хотя использование идентификатора приложения и сертификата X. 509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые удостоверения для ресурсов Azure](#use-managed-identities-for-azure-resources) при размещении приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="d3d52-157">Для управляемых удостоверений не требуется хранить сертификат в приложении или в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="d3d52-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="d3d52-158">В примере приложения используется идентификатор приложения и сертификат X. 509, если `#define` инструкция в верхней части файла *Program.CS* имеет значение `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="d3d52-159">Создайте архивный сертификат PKCS # 12 ( *PFX* -файл).</span><span class="sxs-lookup"><span data-stu-id="d3d52-159">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="d3d52-160">Для создания сертификатов можно использовать [MakeCert в Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="d3d52-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="d3d52-161">Установите сертификат в личное хранилище сертификатов текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="d3d52-162">Пометка ключа как экспортируемого необязательна.</span><span class="sxs-lookup"><span data-stu-id="d3d52-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="d3d52-163">Запишите отпечаток сертификата, который будет использоваться далее в этом процессе.</span><span class="sxs-lookup"><span data-stu-id="d3d52-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="d3d52-164">Экспортируйте архивный сертификат PKCS # 12 ( *PFX* ) как сертификат в формате DER ( *CER* ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-164">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="d3d52-165">Зарегистрируйте приложение в Azure AD ( **Регистрация приложений** ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-165">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="d3d52-166">Отправьте сертификат в кодировке DER ( *CER* ) в Azure AD:</span><span class="sxs-lookup"><span data-stu-id="d3d52-166">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="d3d52-167">Выберите приложение в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="d3d52-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="d3d52-168">Перейдите к разделу **сертификаты & секреты** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-168">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="d3d52-169">Выберите **отправить сертификат** , чтобы отправить сертификат, который содержит открытый ключ.</span><span class="sxs-lookup"><span data-stu-id="d3d52-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="d3d52-170">Сертификат *. cer* , *. pem* или *. CRT* является приемлемым.</span><span class="sxs-lookup"><span data-stu-id="d3d52-170">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="d3d52-171">Сохраните имя хранилища ключей, идентификатор приложения и отпечаток сертификата в *:::no-loc(appsettings.json):::* файле приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *:::no-loc(appsettings.json):::* file.</span></span>
1. <span data-ttu-id="d3d52-172">Перейдите к **разделу хранилища ключей** в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="d3d52-173">Выберите хранилище ключей, созданное в [хранилище секретов в рабочей среде с помощью Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) разделе.</span><span class="sxs-lookup"><span data-stu-id="d3d52-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="d3d52-174">Выберите **Политики доступа** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-174">Select **Access policies** .</span></span>
1. <span data-ttu-id="d3d52-175">Нажмите **Добавить политику доступа** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-175">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="d3d52-176">Откройте **разрешения для секрета** и предоставьте приложению разрешения **Get** и **List** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="d3d52-177">Щелкните **выбрать субъект** и выберите зарегистрированное приложение по имени.</span><span class="sxs-lookup"><span data-stu-id="d3d52-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="d3d52-178">Нажмите кнопку **Выбрать** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="d3d52-179">Щелкните **ОК** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-179">Select **OK** .</span></span>
1. <span data-ttu-id="d3d52-180">Щелкните **Сохранить** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-180">Select **Save** .</span></span>
1. <span data-ttu-id="d3d52-181">Разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-181">Deploy the app.</span></span>

<span data-ttu-id="d3d52-182">`Certificate`Пример приложения получает значения конфигурации из `IConfigurationRoot` с тем же именем, что и имя секрета:</span><span class="sxs-lookup"><span data-stu-id="d3d52-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="d3d52-183">Неиерархические значения. значение для берется `SecretName` `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="d3d52-184">Иерархические значения (разделы): используйте `:` нотацию (двоеточие) или `GetSection` метод расширения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="d3d52-185">Используйте любой из этих подходов для получения значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="d3d52-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="d3d52-186">Сертификат X. 509 управляется ОС.</span><span class="sxs-lookup"><span data-stu-id="d3d52-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="d3d52-187">Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> со значениями, предоставленными *:::no-loc(appsettings.json):::* файлом:</span><span class="sxs-lookup"><span data-stu-id="d3d52-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="d3d52-188">Примеры значений</span><span class="sxs-lookup"><span data-stu-id="d3d52-188">Example values:</span></span>

* <span data-ttu-id="d3d52-189">Имя хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d3d52-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="d3d52-190">Идентификатор приложения: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="d3d52-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="d3d52-191">Отпечаток сертификата: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="d3d52-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="d3d52-192">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="d3d52-192">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/:::no-loc(appsettings.json):::?highlight=10-12)]

<span data-ttu-id="d3d52-193">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d3d52-194">В среде разработки значения секрета загружаются с `_dev` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="d3d52-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="d3d52-195">В рабочей среде значения загружаются с `_prod` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="d3d52-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="d3d52-196">Использование управляемых удостоверений для ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="d3d52-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="d3d52-197">**Приложение, развернутое в Azure** , может использовать преимущества [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview), что позволяет приложению проходить проверку подлинности в Azure Key Vault с помощью проверки подлинности Azure AD без учетных данных (идентификатор приложения и пароль/секрет клиента), хранимых в приложении.</span><span class="sxs-lookup"><span data-stu-id="d3d52-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="d3d52-198">Пример приложения использует управляемые удостоверения для ресурсов Azure, когда `#define` инструкция в верхней части файла *Program.CS* имеет значение `Managed` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="d3d52-199">Введите имя хранилища в *:::no-loc(appsettings.json):::* файл приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-199">Enter the vault name into the app's *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="d3d52-200">В примере приложения не требуется идентификатор приложения и пароль (секрет клиента), если задана `Managed` версия, поэтому эти записи конфигурации можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="d3d52-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="d3d52-201">Приложение развертывается в Azure, а Azure выполняет проверку подлинности приложения для доступа к Azure Key Vault только с использованием имени хранилища, хранящегося в *:::no-loc(appsettings.json):::* файле.</span><span class="sxs-lookup"><span data-stu-id="d3d52-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="d3d52-202">Разверните пример приложения в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="d3d52-203">Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы.</span><span class="sxs-lookup"><span data-stu-id="d3d52-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="d3d52-204">Получите идентификатор объекта из развертывания для использования в следующей команде.</span><span class="sxs-lookup"><span data-stu-id="d3d52-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="d3d52-205">Идентификатор объекта отображается в портал Azure на **:::no-loc(Identity):::** панели службы приложений.</span><span class="sxs-lookup"><span data-stu-id="d3d52-205">The Object ID is shown in the Azure portal on the **:::no-loc(Identity):::** panel of the App Service.</span></span>

<span data-ttu-id="d3d52-206">Используя Azure CLI и идентификатор объекта приложения, укажите приложение с `list` `get` разрешениями и для доступа к хранилищу ключей:</span><span class="sxs-lookup"><span data-stu-id="d3d52-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="d3d52-207">**Перезапустите приложение** с помощью Azure CLI, PowerShell или портал Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="d3d52-208">Пример приложения:</span><span class="sxs-lookup"><span data-stu-id="d3d52-208">The sample app:</span></span>

* <span data-ttu-id="d3d52-209">Создает экземпляр `AzureServiceTokenProvider` класса без строки подключения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="d3d52-210">Если строка подключения не указана, поставщик пытается получить маркер доступа из управляемых удостоверений для ресурсов Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="d3d52-211">Новый объект <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается с помощью `AzureServiceTokenProvider` обратного вызова токена экземпляра.</span><span class="sxs-lookup"><span data-stu-id="d3d52-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="d3d52-212"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Экземпляр используется с реализацией по умолчанию <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , которая загружает все секретные значения и заменяет двойные тире ( `--` ) на двоеточие ( `:` ) в именах ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="d3d52-213">Пример значения имени хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d3d52-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="d3d52-214">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="d3d52-214">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="d3d52-215">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d3d52-216">В среде разработки значения секрета имеют суффикс, `_dev` так как они предоставляются секретами пользователя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="d3d52-217">В рабочей среде значения загружаются с `_prod` суффиксом, так как они предоставляются Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d3d52-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="d3d52-218">Если появится `Access denied` сообщение об ошибке, убедитесь, что приложение зарегистрировано в Azure AD и предоставил доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="d3d52-219">Убедитесь, что вы перезапустите службу в Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="d3d52-220">Сведения об использовании поставщика с управляемым удостоверением и конвейером Azure DevOps см. в статье [Создание подключения Azure Resource Manager службы к виртуальной машине с помощью управляемого удостоверения службы](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="d3d52-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="d3d52-221">Варианты настройки</span><span class="sxs-lookup"><span data-stu-id="d3d52-221">Configuration options</span></span>

<span data-ttu-id="d3d52-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> может принимать <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions> :</span><span class="sxs-lookup"><span data-stu-id="d3d52-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="d3d52-223">Свойство.</span><span class="sxs-lookup"><span data-stu-id="d3d52-223">Property</span></span>         | <span data-ttu-id="d3d52-224">Описание</span><span class="sxs-lookup"><span data-stu-id="d3d52-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="d3d52-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> для использования при извлечении значений.</span><span class="sxs-lookup"><span data-stu-id="d3d52-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="d3d52-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> экземпляр, используемый для управления загрузкой секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="d3d52-227">`Timespan` ожидание между попытками опроса хранилища ключей на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="d3d52-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="d3d52-228">Значение по умолчанию — `null` (конфигурация не перегружается).</span><span class="sxs-lookup"><span data-stu-id="d3d52-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="d3d52-229">URI хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="d3d52-230">Использование префикса имени ключа</span><span class="sxs-lookup"><span data-stu-id="d3d52-230">Use a key name prefix</span></span>

<span data-ttu-id="d3d52-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> предоставляет перегрузку, которая принимает реализацию <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , которая позволяет управлять преобразованием секретов хранилища ключей в конфигурационные ключи.</span><span class="sxs-lookup"><span data-stu-id="d3d52-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="d3d52-232">Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, которое вы задают при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="d3d52-233">Это позволяет, например, загружать секреты на основе версии приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="d3d52-234">Не используйте префиксы в секретах хранилища ключей, чтобы размещать секреты для нескольких приложений в одном хранилище ключей или для размещения секретов среды (например, для *разработки* и *рабочих* секретов) в одном хранилище.</span><span class="sxs-lookup"><span data-stu-id="d3d52-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="d3d52-235">Мы рекомендуем использовать отдельные хранилища ключей для различных приложений и сред разработки и рабочей среды, чтобы изолировать среды приложений для обеспечения наивысшего уровня безопасности.</span><span class="sxs-lookup"><span data-stu-id="d3d52-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="d3d52-236">В следующем примере в хранилище ключей устанавливается секрет (и используется средство диспетчера секретов для среды разработки) `5000-AppSecret` (точки не допускаются в именах секретов хранилища ключей).</span><span class="sxs-lookup"><span data-stu-id="d3d52-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="d3d52-237">Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="d3d52-238">Для другой версии приложения 5.1.0.0 секрет добавляется в хранилище ключей (и с помощью средства диспетчера секретов) для `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="d3d52-239">Каждая версия приложения загружает значение секрета в своей конфигурации в виде `AppSecret` , отключая версию при загрузке секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="d3d52-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> вызывается с настраиваемым <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="d3d52-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="d3d52-241"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Реализация реагирует на префиксы версий секретов, чтобы загрузить правильный секрет в конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="d3d52-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="d3d52-242">`Load` загружает секрет, если его имя начинается с префикса.</span><span class="sxs-lookup"><span data-stu-id="d3d52-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="d3d52-243">Другие секреты не загружены.</span><span class="sxs-lookup"><span data-stu-id="d3d52-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="d3d52-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="d3d52-244">`GetKey`:</span></span>
  * <span data-ttu-id="d3d52-245">Удаляет префикс из имени секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="d3d52-246">Заменяет два тире в любом имени с помощью `KeyDelimiter` , которое является разделителем, используемым в конфигурации (обычно это двоеточие).</span><span class="sxs-lookup"><span data-stu-id="d3d52-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="d3d52-247">Azure Key Vault не допускает двоеточия в именах секретов.</span><span class="sxs-lookup"><span data-stu-id="d3d52-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="d3d52-248">`Load`Метод вызывается алгоритмом поставщика, который выполняет перебор секретов хранилища для поиска тех, у которых есть префикс версии.</span><span class="sxs-lookup"><span data-stu-id="d3d52-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="d3d52-249">При обнаружении префикса версии с `Load` алгоритм использует `GetKey` метод, чтобы вернуть имя конфигурации для имени секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="d3d52-250">Он отделяет префикс версии от имени секрета и возвращает оставшуюся часть имени секрета для загрузки в пары "имя-значение" конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="d3d52-251">При реализации этого подхода:</span><span class="sxs-lookup"><span data-stu-id="d3d52-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="d3d52-252">Версия приложения, указанная в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="d3d52-253">В следующем примере для версии приложения задано значение `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="d3d52-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="d3d52-254">Убедитесь, что `<UserSecretsId>` свойство имеется в файле проекта приложения, где `{GUID}` является идентификатором GUID, предоставляемым пользователем:</span><span class="sxs-lookup"><span data-stu-id="d3d52-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="d3d52-255">Сохраните следующие секреты локально с помощью [средства диспетчера секретов](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="d3d52-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="d3d52-256">Секреты сохраняются в Azure Key Vault с помощью следующих Azure CLI команд:</span><span class="sxs-lookup"><span data-stu-id="d3d52-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="d3d52-257">При запуске приложения загружаются секреты хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="d3d52-258">Строковый секрет для `5000-AppSecret` совпадает с версией приложения, указанной в файле проекта приложения ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="d3d52-259">Версия `5000` (с тире) удаляется из имени ключа.</span><span class="sxs-lookup"><span data-stu-id="d3d52-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="d3d52-260">Во всем приложении при чтении конфигурации с ключом `AppSecret` загружается секретное значение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="d3d52-261">Если версия приложения изменена в файле проекта на, `5.1.0.0` а приложение запускается снова, возвращается значение секрета `5.1.0.0_secret_value_dev` в среде разработки и `5.1.0.0_secret_value_prod` в рабочем окружении.</span><span class="sxs-lookup"><span data-stu-id="d3d52-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="d3d52-262">Можно также предоставить собственную <xref:Microsoft.Azure.KeyVault.KeyVaultClient> реализацию для <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="d3d52-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="d3d52-263">Настраиваемый клиент позволяет совместно использовать один экземпляр клиента в приложении.</span><span class="sxs-lookup"><span data-stu-id="d3d52-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d3d52-264">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="d3d52-264">Bind an array to a class</span></span>

<span data-ttu-id="d3d52-265">Поставщик может считывать значения конфигурации в массив для привязки к массиву POCO.</span><span class="sxs-lookup"><span data-stu-id="d3d52-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="d3d52-266">При чтении из источника конфигурации, позволяющего ключам содержать разделители в виде двоеточия ( `:` ), для различения ключей, составляющих массив ( `:0:` , `:1:` ,), используется числовой сегмент ключа &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="d3d52-267">Дополнительные сведения см. в разделе [Configuration: привязка массива к классу](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="d3d52-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="d3d52-268">Ключи Azure Key Vault не могут использовать двоеточие в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="d3d52-269">Подход, описанный в этом разделе, использует двойные тире ( `--` ) в качестве разделителя для иерархических значений (разделов).</span><span class="sxs-lookup"><span data-stu-id="d3d52-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="d3d52-270">Ключи массивов хранятся в Azure Key Vault с двойными тире и числовыми сегментами ключей ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="d3d52-271">Изучите следующую конфигурацию поставщика ведения журнала [Serilog](https://serilog.net/) , предоставляемую файлом JSON.</span><span class="sxs-lookup"><span data-stu-id="d3d52-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="d3d52-272">В массиве определены два объектных литерала, `WriteTo` отражающих два *приемника* Serilog, которые описывают назначения для вывода журнала:</span><span class="sxs-lookup"><span data-stu-id="d3d52-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="d3d52-273">Конфигурация, показанная в предыдущем JSON-файле, хранится в Azure Key Vault с использованием нотации двойного тире ( `--` ) и числовых сегментов.</span><span class="sxs-lookup"><span data-stu-id="d3d52-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="d3d52-274">Ключ</span><span class="sxs-lookup"><span data-stu-id="d3d52-274">Key</span></span> | <span data-ttu-id="d3d52-275">Значение</span><span class="sxs-lookup"><span data-stu-id="d3d52-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="d3d52-276">Перезагрузка секретов</span><span class="sxs-lookup"><span data-stu-id="d3d52-276">Reload secrets</span></span>

<span data-ttu-id="d3d52-277">Секреты кэшируются до `IConfigurationRoot.Reload()` вызова метода.</span><span class="sxs-lookup"><span data-stu-id="d3d52-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="d3d52-278">Срок действия истек, отключен и обновленные секреты в хранилище ключей не учитываются приложением до тех пор, пока `Reload` не будет выполнено.</span><span class="sxs-lookup"><span data-stu-id="d3d52-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="d3d52-279">Отключенные секреты и секреты с истекшим сроком действия</span><span class="sxs-lookup"><span data-stu-id="d3d52-279">Disabled and expired secrets</span></span>

<span data-ttu-id="d3d52-280">При отключенных и истекших секретах создается исключение <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="d3d52-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="d3d52-281">Чтобы предотвратить создание приложения, укажите конфигурацию с помощью другого поставщика конфигурации или обновите закрытый или истекший секрет.</span><span class="sxs-lookup"><span data-stu-id="d3d52-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d3d52-282">Диагностика</span><span class="sxs-lookup"><span data-stu-id="d3d52-282">Troubleshoot</span></span>

<span data-ttu-id="d3d52-283">Если приложению не удается загрузить конфигурацию с помощью поставщика, в [инфраструктуру ведения журнала ASP.NET Core](xref:fundamentals/logging/index)записывается сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="d3d52-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d3d52-284">При следующих условиях загрузка конфигурации будет запрещена:</span><span class="sxs-lookup"><span data-stu-id="d3d52-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="d3d52-285">Приложение или сертификат неправильно настроены в Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d3d52-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="d3d52-286">Хранилище ключей не существует в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d3d52-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="d3d52-287">Приложение не имеет прав доступа к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="d3d52-288">Политика доступа не включает и не имеет `Get` `List` разрешений.</span><span class="sxs-lookup"><span data-stu-id="d3d52-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="d3d52-289">В хранилище ключей данные конфигурации (пара "имя-значение") неправильно именованы, отсутствуют, отключены или устарели.</span><span class="sxs-lookup"><span data-stu-id="d3d52-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="d3d52-290">Приложение имеет неправильное имя хранилища ключей ( `KeyVaultName` ), идентификатор приложения Azure AD ( `AzureADApplicationId` ) или отпечаток сертификата Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="d3d52-291">Неверный ключ конфигурации (имя) в приложении для загружаемого значения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="d3d52-292">При добавлении политики доступа для приложения в хранилище ключей была создана политика, но кнопка **сохранить** не была выбрана в пользовательском интерфейсе **политик доступа** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3d52-293">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d3d52-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="d3d52-294">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="d3d52-295">Microsoft Azure: Документация по Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="d3d52-296">Создание и перенос ключей, защищенных АППАРАТным модулем безопасности, для Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="d3d52-297">Класс KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="d3d52-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="d3d52-298">Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET</span><span class="sxs-lookup"><span data-stu-id="d3d52-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="d3d52-299">Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)</span><span class="sxs-lookup"><span data-stu-id="d3d52-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3d52-300">В этом документе объясняется, как использовать поставщик конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложения из Azure Key Vault секреты.</span><span class="sxs-lookup"><span data-stu-id="d3d52-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="d3d52-301">Azure Key Vault — это облачная служба, которая помогает защитить криптографические ключи и секреты, используемые приложениями и службами.</span><span class="sxs-lookup"><span data-stu-id="d3d52-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="d3d52-302">Распространенные сценарии использования Azure Key Vault с приложениями ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d3d52-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="d3d52-303">Управление доступом к конфиденциальным данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d3d52-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="d3d52-304">Соблюдайте требования для FIPS 140-2 уровня 2 проверенных аппаратных модулей безопасности (HSM) при хранении данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d3d52-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="d3d52-305">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d3d52-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="d3d52-306">Пакеты</span><span class="sxs-lookup"><span data-stu-id="d3d52-306">Packages</span></span>

<span data-ttu-id="d3d52-307">Добавьте ссылку на пакет в [Microsoft.Extensions.Configфигурации. Пакет AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="d3d52-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="d3d52-308">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="d3d52-308">Sample app</span></span>

<span data-ttu-id="d3d52-309">Пример приложения выполняется в одном из двух режимов, определяемых `#define` инструкцией в верхней части файла *Program.CS* :</span><span class="sxs-lookup"><span data-stu-id="d3d52-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="d3d52-310">`Certificate`: Демонстрируется использование идентификатора клиента Azure Key Vault и сертификата X. 509 для доступа к секретам, хранящимся в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d3d52-310">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="d3d52-311">Эту версию примера можно запустить из любого расположения, развернуть в службе приложений Azure или на любом узле, способном обслуживать ASP.NET Coreное приложение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="d3d52-312">`Managed`: Демонстрируется использование [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для проверки подлинности приложения в Azure Key Vault с проверкой подлинности Azure AD без учетных данных, хранящихся в коде или конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-312">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="d3d52-313">При использовании управляемых удостоверений для проверки подлинности не требуется идентификатор и пароль приложения Azure AD (секрет клиента).</span><span class="sxs-lookup"><span data-stu-id="d3d52-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="d3d52-314">`Managed`Версия примера должна быть развернута в Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="d3d52-315">Следуйте указаниям в разделе [Использование управляемых удостоверений для ресурсов Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="d3d52-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="d3d52-316">Дополнительные сведения о настройке примера приложения с помощью директив препроцессора ( `#define` ) см. в разделе <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="d3d52-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="d3d52-317">Секретное хранилище в среде разработки</span><span class="sxs-lookup"><span data-stu-id="d3d52-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="d3d52-318">Локальное задание секретов с помощью [средства диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d3d52-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d3d52-319">Когда пример приложения выполняется на локальном компьютере в среде разработки, секреты загружаются из локального хранилища диспетчера секретов.</span><span class="sxs-lookup"><span data-stu-id="d3d52-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="d3d52-320">Для средства диспетчера секретов требуется `<UserSecretsId>` свойство в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="d3d52-321">Задайте для свойства значение ( `{GUID}` ) любой уникальный GUID:</span><span class="sxs-lookup"><span data-stu-id="d3d52-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="d3d52-322">Секреты создаются как пары "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="d3d52-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="d3d52-323">Иерархические значения (разделы конфигурации) используют `:` (двоеточие) в качестве разделителя в ASP.NET Core именах ключей [конфигурации](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="d3d52-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="d3d52-324">Диспетчер секретов используется из командной оболочки, открытой в [корне содержимого](xref:fundamentals/index#content-root)проекта, где — это `{SECRET NAME}` имя, а `{SECRET VALUE}` — значение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="d3d52-325">Выполните следующие команды в командной оболочке из [корневого каталога содержимого](xref:fundamentals/index#content-root) проекта, чтобы задать секреты для примера приложения:</span><span class="sxs-lookup"><span data-stu-id="d3d52-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="d3d52-326">Если эти секреты хранятся в Azure Key Vault в [хранилище секретов в рабочей среде с Azure Key Vaultным](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом, `_dev` суффикс изменяется на `_prod` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="d3d52-327">Суффикс предоставляет визуальную подсказку в выходных данных приложения, указывающую источник значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="d3d52-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="d3d52-328">Секретное хранилище в рабочей среде с Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="d3d52-329">Инструкции, приведенные в [кратком руководстве по установке и извлечению секретов из Azure Key Vault с помощью Azure CLI](/azure/key-vault/quick-create-cli) разделе, приведены здесь для создания Azure Key Vault и хранения секретов, используемых в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="d3d52-330">Дополнительные сведения см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="d3d52-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="d3d52-331">Откройте Azure Cloud Shell одним из следующих способов в [портал Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="d3d52-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="d3d52-332">Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом.</span><span class="sxs-lookup"><span data-stu-id="d3d52-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="d3d52-333">Используйте строку поиска "Azure CLI" в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="d3d52-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="d3d52-334">Откройте Cloud Shell в браузере с помощью кнопки **запустить Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="d3d52-335">Нажмите кнопку **Cloud Shell** в меню в правом верхнем углу портал Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="d3d52-336">Дополнительные сведения см. в разделе [Azure CLI](/cli/azure/) и [Обзор Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="d3d52-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="d3d52-337">Если вы еще не прошли проверку подлинности, выполните вход с помощью `az login` команды.</span><span class="sxs-lookup"><span data-stu-id="d3d52-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="d3d52-338">Создайте группу ресурсов с помощью следующей команды, где `{RESOURCE GROUP NAME}` — это имя группы ресурсов для новой группы ресурсов, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="d3d52-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d3d52-339">Создайте хранилище ключей в группе ресурсов с помощью следующей команды, где `{KEY VAULT NAME}` — это имя нового хранилища ключей, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="d3d52-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="d3d52-340">Создайте секреты в хранилище ключей в виде пар "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="d3d52-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="d3d52-341">Azure Key Vault имена секретов ограничены буквенно-цифровыми символами и тире.</span><span class="sxs-lookup"><span data-stu-id="d3d52-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="d3d52-342">Иерархические значения (разделы конфигурации) используют `--` (два тире) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="d3d52-343">Двоеточия, которые обычно используются для разделения раздела из подраздела в [ASP.NET Core конфигурации](xref:fundamentals/configuration/index), не допускаются в именах секретов хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="d3d52-344">Поэтому при загрузке секретов в конфигурацию приложения используются два дефиса и они переключаются на двоеточие.</span><span class="sxs-lookup"><span data-stu-id="d3d52-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="d3d52-345">Следующие секреты предназначены для использования с примером приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="d3d52-346">Эти значения включают `_prod` суффикс, чтобы отличить `_dev` значения суффиксов, загруженных в среде разработки, от секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="d3d52-347">Замените на `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем шаге:</span><span class="sxs-lookup"><span data-stu-id="d3d52-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="d3d52-348">Использование идентификатора приложения и сертификата X. 509 для приложений, не размещенных в Azure</span><span class="sxs-lookup"><span data-stu-id="d3d52-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="d3d52-349">Настройте Azure AD, Azure Key Vault и приложение для использования идентификатора Azure Active Directory приложения и сертификата X. 509 для проверки подлинности в хранилище ключей, **Если приложение размещено за пределами Azure** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure** .</span></span> <span data-ttu-id="d3d52-350">См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="d3d52-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="d3d52-351">Хотя использование идентификатора приложения и сертификата X. 509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые удостоверения для ресурсов Azure](#use-managed-identities-for-azure-resources) при размещении приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="d3d52-352">Для управляемых удостоверений не требуется хранить сертификат в приложении или в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="d3d52-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="d3d52-353">В примере приложения используется идентификатор приложения и сертификат X. 509, если `#define` инструкция в верхней части файла *Program.CS* имеет значение `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="d3d52-354">Создайте архивный сертификат PKCS # 12 ( *PFX* -файл).</span><span class="sxs-lookup"><span data-stu-id="d3d52-354">Create a PKCS#12 archive ( *.pfx* ) certificate.</span></span> <span data-ttu-id="d3d52-355">Для создания сертификатов можно использовать [MakeCert в Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="d3d52-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="d3d52-356">Установите сертификат в личное хранилище сертификатов текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="d3d52-357">Пометка ключа как экспортируемого необязательна.</span><span class="sxs-lookup"><span data-stu-id="d3d52-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="d3d52-358">Запишите отпечаток сертификата, который будет использоваться далее в этом процессе.</span><span class="sxs-lookup"><span data-stu-id="d3d52-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="d3d52-359">Экспортируйте архивный сертификат PKCS # 12 ( *PFX* ) как сертификат в формате DER ( *CER* ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-359">Export the PKCS#12 archive ( *.pfx* ) certificate as a DER-encoded certificate ( *.cer* ).</span></span>
1. <span data-ttu-id="d3d52-360">Зарегистрируйте приложение в Azure AD ( **Регистрация приложений** ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-360">Register the app with Azure AD ( **App registrations** ).</span></span>
1. <span data-ttu-id="d3d52-361">Отправьте сертификат в кодировке DER ( *CER* ) в Azure AD:</span><span class="sxs-lookup"><span data-stu-id="d3d52-361">Upload the DER-encoded certificate ( *.cer* ) to Azure AD:</span></span>
   1. <span data-ttu-id="d3d52-362">Выберите приложение в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="d3d52-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="d3d52-363">Перейдите к разделу **сертификаты & секреты** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-363">Navigate to **Certificates & secrets** .</span></span>
   1. <span data-ttu-id="d3d52-364">Выберите **отправить сертификат** , чтобы отправить сертификат, который содержит открытый ключ.</span><span class="sxs-lookup"><span data-stu-id="d3d52-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="d3d52-365">Сертификат *. cer* , *. pem* или *. CRT* является приемлемым.</span><span class="sxs-lookup"><span data-stu-id="d3d52-365">A *.cer* , *.pem* , or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="d3d52-366">Сохраните имя хранилища ключей, идентификатор приложения и отпечаток сертификата в *:::no-loc(appsettings.json):::* файле приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *:::no-loc(appsettings.json):::* file.</span></span>
1. <span data-ttu-id="d3d52-367">Перейдите к **разделу хранилища ключей** в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="d3d52-368">Выберите хранилище ключей, созданное в [хранилище секретов в рабочей среде с помощью Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) разделе.</span><span class="sxs-lookup"><span data-stu-id="d3d52-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="d3d52-369">Выберите **Политики доступа** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-369">Select **Access policies** .</span></span>
1. <span data-ttu-id="d3d52-370">Нажмите **Добавить политику доступа** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-370">Select **Add Access Policy** .</span></span>
1. <span data-ttu-id="d3d52-371">Откройте **разрешения для секрета** и предоставьте приложению разрешения **Get** и **List** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="d3d52-372">Щелкните **выбрать субъект** и выберите зарегистрированное приложение по имени.</span><span class="sxs-lookup"><span data-stu-id="d3d52-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="d3d52-373">Нажмите кнопку **Выбрать** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="d3d52-374">Щелкните **ОК** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-374">Select **OK** .</span></span>
1. <span data-ttu-id="d3d52-375">Щелкните **Сохранить** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-375">Select **Save** .</span></span>
1. <span data-ttu-id="d3d52-376">Разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-376">Deploy the app.</span></span>

<span data-ttu-id="d3d52-377">`Certificate`Пример приложения получает значения конфигурации из `IConfigurationRoot` с тем же именем, что и имя секрета:</span><span class="sxs-lookup"><span data-stu-id="d3d52-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="d3d52-378">Неиерархические значения. значение для берется `SecretName` `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="d3d52-379">Иерархические значения (разделы): используйте `:` нотацию (двоеточие) или `GetSection` метод расширения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="d3d52-380">Используйте любой из этих подходов для получения значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="d3d52-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="d3d52-381">Сертификат X. 509 управляется ОС.</span><span class="sxs-lookup"><span data-stu-id="d3d52-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="d3d52-382">Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> со значениями, предоставленными *:::no-loc(appsettings.json):::* файлом:</span><span class="sxs-lookup"><span data-stu-id="d3d52-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *:::no-loc(appsettings.json):::* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="d3d52-383">Примеры значений</span><span class="sxs-lookup"><span data-stu-id="d3d52-383">Example values:</span></span>

* <span data-ttu-id="d3d52-384">Имя хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d3d52-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="d3d52-385">Идентификатор приложения: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="d3d52-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="d3d52-386">Отпечаток сертификата: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="d3d52-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="d3d52-387">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="d3d52-387">*:::no-loc(appsettings.json):::* :</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/:::no-loc(appsettings.json):::?highlight=10-12)]

<span data-ttu-id="d3d52-388">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d3d52-389">В среде разработки значения секрета загружаются с `_dev` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="d3d52-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="d3d52-390">В рабочей среде значения загружаются с `_prod` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="d3d52-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="d3d52-391">Использование управляемых удостоверений для ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="d3d52-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="d3d52-392">**Приложение, развернутое в Azure** , может использовать преимущества [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview), что позволяет приложению проходить проверку подлинности в Azure Key Vault с помощью проверки подлинности Azure AD без учетных данных (идентификатор приложения и пароль/секрет клиента), хранимых в приложении.</span><span class="sxs-lookup"><span data-stu-id="d3d52-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="d3d52-393">Пример приложения использует управляемые удостоверения для ресурсов Azure, когда `#define` инструкция в верхней части файла *Program.CS* имеет значение `Managed` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="d3d52-394">Введите имя хранилища в *:::no-loc(appsettings.json):::* файл приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-394">Enter the vault name into the app's *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="d3d52-395">В примере приложения не требуется идентификатор приложения и пароль (секрет клиента), если задана `Managed` версия, поэтому эти записи конфигурации можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="d3d52-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="d3d52-396">Приложение развертывается в Azure, а Azure выполняет проверку подлинности приложения для доступа к Azure Key Vault только с использованием имени хранилища, хранящегося в *:::no-loc(appsettings.json):::* файле.</span><span class="sxs-lookup"><span data-stu-id="d3d52-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *:::no-loc(appsettings.json):::* file.</span></span>

<span data-ttu-id="d3d52-397">Разверните пример приложения в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="d3d52-398">Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы.</span><span class="sxs-lookup"><span data-stu-id="d3d52-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="d3d52-399">Получите идентификатор объекта из развертывания для использования в следующей команде.</span><span class="sxs-lookup"><span data-stu-id="d3d52-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="d3d52-400">Идентификатор объекта отображается в портал Azure на **:::no-loc(Identity):::** панели службы приложений.</span><span class="sxs-lookup"><span data-stu-id="d3d52-400">The Object ID is shown in the Azure portal on the **:::no-loc(Identity):::** panel of the App Service.</span></span>

<span data-ttu-id="d3d52-401">Используя Azure CLI и идентификатор объекта приложения, укажите приложение с `list` `get` разрешениями и для доступа к хранилищу ключей:</span><span class="sxs-lookup"><span data-stu-id="d3d52-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="d3d52-402">**Перезапустите приложение** с помощью Azure CLI, PowerShell или портал Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="d3d52-403">Пример приложения:</span><span class="sxs-lookup"><span data-stu-id="d3d52-403">The sample app:</span></span>

* <span data-ttu-id="d3d52-404">Создает экземпляр `AzureServiceTokenProvider` класса без строки подключения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="d3d52-405">Если строка подключения не указана, поставщик пытается получить маркер доступа из управляемых удостоверений для ресурсов Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="d3d52-406">Новый объект <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается с помощью `AzureServiceTokenProvider` обратного вызова токена экземпляра.</span><span class="sxs-lookup"><span data-stu-id="d3d52-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="d3d52-407"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Экземпляр используется с реализацией по умолчанию <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , которая загружает все секретные значения и заменяет двойные тире ( `--` ) на двоеточие ( `:` ) в именах ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="d3d52-408">Пример значения имени хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="d3d52-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="d3d52-409">*:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="d3d52-409">*:::no-loc(appsettings.json):::* :</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="d3d52-410">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="d3d52-411">В среде разработки значения секрета имеют суффикс, `_dev` так как они предоставляются секретами пользователя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="d3d52-412">В рабочей среде значения загружаются с `_prod` суффиксом, так как они предоставляются Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d3d52-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="d3d52-413">Если появится `Access denied` сообщение об ошибке, убедитесь, что приложение зарегистрировано в Azure AD и предоставил доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="d3d52-414">Убедитесь, что вы перезапустите службу в Azure.</span><span class="sxs-lookup"><span data-stu-id="d3d52-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="d3d52-415">Сведения об использовании поставщика с управляемым удостоверением и конвейером Azure DevOps см. в статье [Создание подключения Azure Resource Manager службы к виртуальной машине с помощью управляемого удостоверения службы](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="d3d52-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="d3d52-416">Использование префикса имени ключа</span><span class="sxs-lookup"><span data-stu-id="d3d52-416">Use a key name prefix</span></span>

<span data-ttu-id="d3d52-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> предоставляет перегрузку, которая принимает реализацию <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , которая позволяет управлять преобразованием секретов хранилища ключей в конфигурационные ключи.</span><span class="sxs-lookup"><span data-stu-id="d3d52-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="d3d52-418">Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, которое вы задают при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="d3d52-419">Это позволяет, например, загружать секреты на основе версии приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="d3d52-420">Не используйте префиксы в секретах хранилища ключей, чтобы размещать секреты для нескольких приложений в одном хранилище ключей или для размещения секретов среды (например, для *разработки* и *рабочих* секретов) в одном хранилище.</span><span class="sxs-lookup"><span data-stu-id="d3d52-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="d3d52-421">Мы рекомендуем использовать отдельные хранилища ключей для различных приложений и сред разработки и рабочей среды, чтобы изолировать среды приложений для обеспечения наивысшего уровня безопасности.</span><span class="sxs-lookup"><span data-stu-id="d3d52-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="d3d52-422">В следующем примере в хранилище ключей устанавливается секрет (и используется средство диспетчера секретов для среды разработки) `5000-AppSecret` (точки не допускаются в именах секретов хранилища ключей).</span><span class="sxs-lookup"><span data-stu-id="d3d52-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="d3d52-423">Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="d3d52-424">Для другой версии приложения 5.1.0.0 секрет добавляется в хранилище ключей (и с помощью средства диспетчера секретов) для `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="d3d52-425">Каждая версия приложения загружает значение секрета в своей конфигурации в виде `AppSecret` , отключая версию при загрузке секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="d3d52-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> вызывается с настраиваемым <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="d3d52-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="d3d52-427"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Реализация реагирует на префиксы версий секретов, чтобы загрузить правильный секрет в конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="d3d52-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="d3d52-428">`Load` загружает секрет, если его имя начинается с префикса.</span><span class="sxs-lookup"><span data-stu-id="d3d52-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="d3d52-429">Другие секреты не загружены.</span><span class="sxs-lookup"><span data-stu-id="d3d52-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="d3d52-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="d3d52-430">`GetKey`:</span></span>
  * <span data-ttu-id="d3d52-431">Удаляет префикс из имени секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="d3d52-432">Заменяет два тире в любом имени с помощью `KeyDelimiter` , которое является разделителем, используемым в конфигурации (обычно это двоеточие).</span><span class="sxs-lookup"><span data-stu-id="d3d52-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="d3d52-433">Azure Key Vault не допускает двоеточия в именах секретов.</span><span class="sxs-lookup"><span data-stu-id="d3d52-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="d3d52-434">`Load`Метод вызывается алгоритмом поставщика, который выполняет перебор секретов хранилища для поиска тех, у которых есть префикс версии.</span><span class="sxs-lookup"><span data-stu-id="d3d52-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="d3d52-435">При обнаружении префикса версии с `Load` алгоритм использует `GetKey` метод, чтобы вернуть имя конфигурации для имени секрета.</span><span class="sxs-lookup"><span data-stu-id="d3d52-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="d3d52-436">Он отделяет префикс версии от имени секрета и возвращает оставшуюся часть имени секрета для загрузки в пары "имя-значение" конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="d3d52-437">При реализации этого подхода:</span><span class="sxs-lookup"><span data-stu-id="d3d52-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="d3d52-438">Версия приложения, указанная в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="d3d52-439">В следующем примере для версии приложения задано значение `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="d3d52-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="d3d52-440">Убедитесь, что `<UserSecretsId>` свойство имеется в файле проекта приложения, где `{GUID}` является идентификатором GUID, предоставляемым пользователем:</span><span class="sxs-lookup"><span data-stu-id="d3d52-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="d3d52-441">Сохраните следующие секреты локально с помощью [средства диспетчера секретов](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="d3d52-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="d3d52-442">Секреты сохраняются в Azure Key Vault с помощью следующих Azure CLI команд:</span><span class="sxs-lookup"><span data-stu-id="d3d52-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="d3d52-443">При запуске приложения загружаются секреты хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="d3d52-444">Строковый секрет для `5000-AppSecret` совпадает с версией приложения, указанной в файле проекта приложения ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="d3d52-445">Версия `5000` (с тире) удаляется из имени ключа.</span><span class="sxs-lookup"><span data-stu-id="d3d52-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="d3d52-446">Во всем приложении при чтении конфигурации с ключом `AppSecret` загружается секретное значение.</span><span class="sxs-lookup"><span data-stu-id="d3d52-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="d3d52-447">Если версия приложения изменена в файле проекта на, `5.1.0.0` а приложение запускается снова, возвращается значение секрета `5.1.0.0_secret_value_dev` в среде разработки и `5.1.0.0_secret_value_prod` в рабочем окружении.</span><span class="sxs-lookup"><span data-stu-id="d3d52-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="d3d52-448">Можно также предоставить собственную <xref:Microsoft.Azure.KeyVault.KeyVaultClient> реализацию для <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="d3d52-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="d3d52-449">Настраиваемый клиент позволяет совместно использовать один экземпляр клиента в приложении.</span><span class="sxs-lookup"><span data-stu-id="d3d52-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="d3d52-450">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="d3d52-450">Bind an array to a class</span></span>

<span data-ttu-id="d3d52-451">Поставщик может считывать значения конфигурации в массив для привязки к массиву POCO.</span><span class="sxs-lookup"><span data-stu-id="d3d52-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="d3d52-452">При чтении из источника конфигурации, позволяющего ключам содержать разделители в виде двоеточия ( `:` ), для различения ключей, составляющих массив ( `:0:` , `:1:` ,), используется числовой сегмент ключа &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="d3d52-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="d3d52-453">Дополнительные сведения см. в разделе [Configuration: привязка массива к классу](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="d3d52-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="d3d52-454">Ключи Azure Key Vault не могут использовать двоеточие в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="d3d52-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="d3d52-455">Подход, описанный в этом разделе, использует двойные тире ( `--` ) в качестве разделителя для иерархических значений (разделов).</span><span class="sxs-lookup"><span data-stu-id="d3d52-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="d3d52-456">Ключи массивов хранятся в Azure Key Vault с двойными тире и числовыми сегментами ключей ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="d3d52-457">Изучите следующую конфигурацию поставщика ведения журнала [Serilog](https://serilog.net/) , предоставляемую файлом JSON.</span><span class="sxs-lookup"><span data-stu-id="d3d52-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="d3d52-458">В массиве определены два объектных литерала, `WriteTo` отражающих два *приемника* Serilog, которые описывают назначения для вывода журнала:</span><span class="sxs-lookup"><span data-stu-id="d3d52-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks* , which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="d3d52-459">Конфигурация, показанная в предыдущем JSON-файле, хранится в Azure Key Vault с использованием нотации двойного тире ( `--` ) и числовых сегментов.</span><span class="sxs-lookup"><span data-stu-id="d3d52-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="d3d52-460">Ключ</span><span class="sxs-lookup"><span data-stu-id="d3d52-460">Key</span></span> | <span data-ttu-id="d3d52-461">Значение</span><span class="sxs-lookup"><span data-stu-id="d3d52-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="d3d52-462">Перезагрузка секретов</span><span class="sxs-lookup"><span data-stu-id="d3d52-462">Reload secrets</span></span>

<span data-ttu-id="d3d52-463">Секреты кэшируются до `IConfigurationRoot.Reload()` вызова метода.</span><span class="sxs-lookup"><span data-stu-id="d3d52-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="d3d52-464">Срок действия истек, отключен и обновленные секреты в хранилище ключей не учитываются приложением до тех пор, пока `Reload` не будет выполнено.</span><span class="sxs-lookup"><span data-stu-id="d3d52-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="d3d52-465">Отключенные секреты и секреты с истекшим сроком действия</span><span class="sxs-lookup"><span data-stu-id="d3d52-465">Disabled and expired secrets</span></span>

<span data-ttu-id="d3d52-466">При отключенных и истекших секретах создается исключение <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="d3d52-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="d3d52-467">Чтобы предотвратить создание приложения, укажите конфигурацию с помощью другого поставщика конфигурации или обновите закрытый или истекший секрет.</span><span class="sxs-lookup"><span data-stu-id="d3d52-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="d3d52-468">Диагностика</span><span class="sxs-lookup"><span data-stu-id="d3d52-468">Troubleshoot</span></span>

<span data-ttu-id="d3d52-469">Если приложению не удается загрузить конфигурацию с помощью поставщика, в [инфраструктуру ведения журнала ASP.NET Core](xref:fundamentals/logging/index)записывается сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="d3d52-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="d3d52-470">При следующих условиях загрузка конфигурации будет запрещена:</span><span class="sxs-lookup"><span data-stu-id="d3d52-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="d3d52-471">Приложение или сертификат неправильно настроены в Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d3d52-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="d3d52-472">Хранилище ключей не существует в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="d3d52-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="d3d52-473">Приложение не имеет прав доступа к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="d3d52-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="d3d52-474">Политика доступа не включает и не имеет `Get` `List` разрешений.</span><span class="sxs-lookup"><span data-stu-id="d3d52-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="d3d52-475">В хранилище ключей данные конфигурации (пара "имя-значение") неправильно именованы, отсутствуют, отключены или устарели.</span><span class="sxs-lookup"><span data-stu-id="d3d52-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="d3d52-476">Приложение имеет неправильное имя хранилища ключей ( `KeyVaultName` ), идентификатор приложения Azure AD ( `AzureADApplicationId` ) или отпечаток сертификата Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="d3d52-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="d3d52-477">Неверный ключ конфигурации (имя) в приложении для загружаемого значения.</span><span class="sxs-lookup"><span data-stu-id="d3d52-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="d3d52-478">При добавлении политики доступа для приложения в хранилище ключей была создана политика, но кнопка **сохранить** не была выбрана в пользовательском интерфейсе **политик доступа** .</span><span class="sxs-lookup"><span data-stu-id="d3d52-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3d52-479">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="d3d52-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="d3d52-480">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="d3d52-481">Microsoft Azure: Документация по Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="d3d52-482">Создание и перенос ключей, защищенных АППАРАТным модулем безопасности, для Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="d3d52-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="d3d52-483">Класс KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="d3d52-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="d3d52-484">Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET</span><span class="sxs-lookup"><span data-stu-id="d3d52-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="d3d52-485">Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)</span><span class="sxs-lookup"><span data-stu-id="d3d52-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

