---
title: Поставщик конфигурации Azure Key Vault в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать поставщик конфигурации Azure Key Vault для настройки приложения с помощью пар "имя-значение", загружаемых во время выполнения.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
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
uid: security/key-vault-configuration
ms.openlocfilehash: 4b035fe59b8576eb387ddce67943386ccab55492
ms.sourcegitcommit: 8dfcd2b4be936950c228b4d98430622a04254cd7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/26/2020
ms.locfileid: "97792078"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="ea709-103">Поставщик конфигурации Azure Key Vault в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ea709-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="ea709-104">[Эндрю Стантон-медперсонала](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="ea709-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ea709-105">В этом документе объясняется, как использовать поставщик конфигурации [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложения из Azure Key Vault секретов.</span><span class="sxs-lookup"><span data-stu-id="ea709-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="ea709-106">Azure Key Vault — это облачная служба, которая помогает защитить криптографические ключи и секреты, используемые приложениями и службами.</span><span class="sxs-lookup"><span data-stu-id="ea709-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="ea709-107">Распространенные сценарии использования Azure Key Vault с приложениями ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ea709-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="ea709-108">Управление доступом к конфиденциальным данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ea709-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="ea709-109">Соблюдайте требования для FIPS 140-2 уровня 2 проверенных аппаратных модулей безопасности (HSM) при хранении данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ea709-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="ea709-110">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea709-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="ea709-111">Пакеты</span><span class="sxs-lookup"><span data-stu-id="ea709-111">Packages</span></span>

<span data-ttu-id="ea709-112">Добавьте ссылки на пакеты для следующих пакетов:</span><span class="sxs-lookup"><span data-stu-id="ea709-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="ea709-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span><span class="sxs-lookup"><span data-stu-id="ea709-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="ea709-114">[Службы.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="ea709-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="ea709-115">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="ea709-115">Sample app</span></span>

<span data-ttu-id="ea709-116">Пример приложения выполняется в одном из двух режимов, определяемых `#define` инструкцией в верхней части файла *Program.CS* :</span><span class="sxs-lookup"><span data-stu-id="ea709-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="ea709-117">`Certificate`: Демонстрируется использование идентификатора клиента Azure Key Vault и сертификата X. 509 для доступа к секретам, хранящимся в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ea709-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="ea709-118">Эту версию примера можно запустить из любого расположения, развернуть в службе приложений Azure или на любом узле, способном обслуживать ASP.NET Coreное приложение.</span><span class="sxs-lookup"><span data-stu-id="ea709-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="ea709-119">`Managed`: Демонстрируется использование [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для проверки подлинности приложения в Azure Key Vault с проверкой подлинности Azure AD без учетных данных, хранящихся в коде или конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="ea709-120">При использовании управляемых удостоверений для проверки подлинности не требуется идентификатор и пароль приложения Azure AD (секрет клиента).</span><span class="sxs-lookup"><span data-stu-id="ea709-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="ea709-121">`Managed`Версия примера должна быть развернута в Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="ea709-122">Следуйте указаниям в разделе [Использование управляемых удостоверений для ресурсов Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="ea709-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="ea709-123">Дополнительные сведения о настройке примера приложения с помощью директив препроцессора ( `#define` ) см. в разделе <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="ea709-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="ea709-124">Секретное хранилище в среде разработки</span><span class="sxs-lookup"><span data-stu-id="ea709-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="ea709-125">Локальное задание секретов с помощью [средства диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ea709-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ea709-126">Когда пример приложения выполняется на локальном компьютере в среде разработки, секреты загружаются из локального пользовательского хранилища секретов.</span><span class="sxs-lookup"><span data-stu-id="ea709-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="ea709-127">Для средства диспетчера секретов требуется `<UserSecretsId>` свойство в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="ea709-128">Задайте для свойства значение ( `{GUID}` ) любой уникальный GUID:</span><span class="sxs-lookup"><span data-stu-id="ea709-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="ea709-129">Секреты создаются как пары "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="ea709-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="ea709-130">Иерархические значения (разделы конфигурации) используют `:` (двоеточие) в качестве разделителя в ASP.NET Core именах ключей [конфигурации](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="ea709-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="ea709-131">Диспетчер секретов используется из командной оболочки, открытой в [корне содержимого](xref:fundamentals/index#content-root)проекта, где — это `{SECRET NAME}` имя, а `{SECRET VALUE}` — значение.</span><span class="sxs-lookup"><span data-stu-id="ea709-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="ea709-132">Выполните следующие команды в командной оболочке из [корневого каталога содержимого](xref:fundamentals/index#content-root) проекта, чтобы задать секреты для примера приложения:</span><span class="sxs-lookup"><span data-stu-id="ea709-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="ea709-133">Если эти секреты хранятся в Azure Key Vault в [хранилище секретов в рабочей среде с Azure Key Vaultным](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом, `_dev` суффикс изменяется на `_prod` .</span><span class="sxs-lookup"><span data-stu-id="ea709-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="ea709-134">Суффикс предоставляет визуальную подсказку в выходных данных приложения, указывающую источник значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ea709-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="ea709-135">Секретное хранилище в рабочей среде с Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="ea709-136">Инструкции, приведенные в [кратком руководстве по установке и извлечению секретов из Azure Key Vault с помощью Azure CLI](/azure/key-vault/quick-create-cli) разделе, приведены здесь для создания Azure Key Vault и хранения секретов, используемых в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="ea709-137">Дополнительные сведения см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="ea709-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="ea709-138">Откройте Azure Cloud Shell одним из следующих способов в [портал Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="ea709-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="ea709-139">Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом.</span><span class="sxs-lookup"><span data-stu-id="ea709-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="ea709-140">Используйте строку поиска "Azure CLI" в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="ea709-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="ea709-141">Откройте Cloud Shell в браузере с помощью кнопки **запустить Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="ea709-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="ea709-142">Нажмите кнопку **Cloud Shell** в меню в правом верхнем углу портал Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="ea709-143">Дополнительные сведения см. в разделе [Azure CLI](/cli/azure/) и [Обзор Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="ea709-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="ea709-144">Если вы еще не прошли проверку подлинности, выполните вход с помощью `az login` команды.</span><span class="sxs-lookup"><span data-stu-id="ea709-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="ea709-145">Создайте группу ресурсов с помощью следующей команды, где `{RESOURCE GROUP NAME}` — это имя группы ресурсов для новой группы ресурсов, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="ea709-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="ea709-146">Создайте хранилище ключей в группе ресурсов с помощью следующей команды, где `{KEY VAULT NAME}` — это имя нового хранилища ключей, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="ea709-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="ea709-147">Создайте секреты в хранилище ключей в виде пар "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="ea709-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="ea709-148">Azure Key Vault имена секретов ограничены буквенно-цифровыми символами и тире.</span><span class="sxs-lookup"><span data-stu-id="ea709-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="ea709-149">Иерархические значения (разделы конфигурации) используют `--` (два тире) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="ea709-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="ea709-150">Двоеточия, которые обычно используются для разделения раздела из подраздела в [ASP.NET Core конфигурации](xref:fundamentals/configuration/index), не допускаются в именах секретов хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="ea709-151">Поэтому при загрузке секретов в конфигурацию приложения используются два дефиса и они переключаются на двоеточие.</span><span class="sxs-lookup"><span data-stu-id="ea709-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="ea709-152">Следующие секреты предназначены для использования с примером приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="ea709-153">Эти значения включают `_prod` суффикс, чтобы отличить `_dev` значения суффиксов, загруженных в среде разработки, от секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="ea709-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="ea709-154">Замените на `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем шаге:</span><span class="sxs-lookup"><span data-stu-id="ea709-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="ea709-155">Использование идентификатора приложения и сертификата X. 509 для приложений, не размещенных в Azure</span><span class="sxs-lookup"><span data-stu-id="ea709-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="ea709-156">Настройте Azure AD, Azure Key Vault и приложение для использования идентификатора Azure Active Directory приложения и сертификата X. 509 для проверки подлинности в хранилище ключей, **Если приложение размещено за пределами Azure**.</span><span class="sxs-lookup"><span data-stu-id="ea709-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="ea709-157">См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="ea709-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="ea709-158">Хотя использование идентификатора приложения и сертификата X. 509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые удостоверения для ресурсов Azure](#use-managed-identities-for-azure-resources) при размещении приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="ea709-159">Для управляемых удостоверений не требуется хранить сертификат в приложении или в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="ea709-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="ea709-160">В примере приложения используется идентификатор приложения и сертификат X. 509, если `#define` инструкция в верхней части файла *Program.CS* имеет значение `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="ea709-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="ea709-161">Создайте архивный сертификат PKCS # 12 (*PFX*-файл).</span><span class="sxs-lookup"><span data-stu-id="ea709-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="ea709-162">Для создания сертификатов можно использовать [MakeCert в Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="ea709-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="ea709-163">Установите сертификат в личное хранилище сертификатов текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="ea709-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="ea709-164">Пометка ключа как экспортируемого необязательна.</span><span class="sxs-lookup"><span data-stu-id="ea709-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="ea709-165">Запишите отпечаток сертификата, который будет использоваться далее в этом процессе.</span><span class="sxs-lookup"><span data-stu-id="ea709-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="ea709-166">Экспортируйте архивный сертификат PKCS # 12 (*PFX*) как сертификат в формате DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="ea709-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="ea709-167">Зарегистрируйте приложение в Azure AD (**Регистрация приложений**).</span><span class="sxs-lookup"><span data-stu-id="ea709-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="ea709-168">Отправьте сертификат в кодировке DER (*CER*) в Azure AD:</span><span class="sxs-lookup"><span data-stu-id="ea709-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="ea709-169">Выберите приложение в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="ea709-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="ea709-170">Перейдите к разделу **сертификаты & секреты**.</span><span class="sxs-lookup"><span data-stu-id="ea709-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="ea709-171">Выберите **отправить сертификат** , чтобы отправить сертификат, который содержит открытый ключ.</span><span class="sxs-lookup"><span data-stu-id="ea709-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="ea709-172">Сертификат *. cer*, *. pem* или *. CRT* является приемлемым.</span><span class="sxs-lookup"><span data-stu-id="ea709-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="ea709-173">Сохраните имя хранилища ключей, идентификатор приложения и отпечаток сертификата в *appsettings.json* файле приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="ea709-174">Перейдите к **разделу хранилища ключей** в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="ea709-175">Выберите хранилище ключей, созданное в [хранилище секретов в рабочей среде с помощью Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) разделе.</span><span class="sxs-lookup"><span data-stu-id="ea709-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="ea709-176">Выберите **Политики доступа**.</span><span class="sxs-lookup"><span data-stu-id="ea709-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="ea709-177">Нажмите **Добавить политику доступа**.</span><span class="sxs-lookup"><span data-stu-id="ea709-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="ea709-178">Откройте **разрешения для секрета** и предоставьте приложению разрешения **Get** и **List** .</span><span class="sxs-lookup"><span data-stu-id="ea709-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="ea709-179">Щелкните **выбрать субъект** и выберите зарегистрированное приложение по имени.</span><span class="sxs-lookup"><span data-stu-id="ea709-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="ea709-180">Нажмите кнопку **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="ea709-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="ea709-181">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ea709-181">Select **OK**.</span></span>
1. <span data-ttu-id="ea709-182">Щелкните **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="ea709-182">Select **Save**.</span></span>
1. <span data-ttu-id="ea709-183">Разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="ea709-183">Deploy the app.</span></span>

<span data-ttu-id="ea709-184">`Certificate`Пример приложения получает значения конфигурации из `IConfigurationRoot` с тем же именем, что и имя секрета:</span><span class="sxs-lookup"><span data-stu-id="ea709-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="ea709-185">Неиерархические значения. значение для берется `SecretName` `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="ea709-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="ea709-186">Иерархические значения (разделы): используйте `:` нотацию (двоеточие) или `GetSection` метод расширения.</span><span class="sxs-lookup"><span data-stu-id="ea709-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="ea709-187">Используйте любой из этих подходов для получения значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ea709-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="ea709-188">Сертификат X. 509 управляется ОС.</span><span class="sxs-lookup"><span data-stu-id="ea709-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="ea709-189">Приложение вызывает **AddAzureKeyVault** со значениями, предоставленными *appsettings.json* файлом:</span><span class="sxs-lookup"><span data-stu-id="ea709-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="ea709-190">Примеры значений</span><span class="sxs-lookup"><span data-stu-id="ea709-190">Example values:</span></span>

* <span data-ttu-id="ea709-191">Имя хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="ea709-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="ea709-192">Идентификатор приложения: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="ea709-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="ea709-193">Отпечаток сертификата: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="ea709-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="ea709-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea709-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="ea709-195">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="ea709-196">В среде разработки значения секрета загружаются с `_dev` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="ea709-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="ea709-197">В рабочей среде значения загружаются с `_prod` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="ea709-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="ea709-198">Использование управляемых удостоверений для ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="ea709-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="ea709-199">**Приложение, развернутое в Azure** , может использовать преимущества [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview), что позволяет приложению проходить проверку подлинности в Azure Key Vault с помощью проверки подлинности Azure AD без учетных данных (идентификатор приложения и пароль/секрет клиента), хранимых в приложении.</span><span class="sxs-lookup"><span data-stu-id="ea709-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="ea709-200">Пример приложения использует управляемые удостоверения для ресурсов Azure, когда `#define` инструкция в верхней части файла *Program.CS* имеет значение `Managed` .</span><span class="sxs-lookup"><span data-stu-id="ea709-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="ea709-201">Введите имя хранилища в *appsettings.json* файл приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="ea709-202">В примере приложения не требуется идентификатор приложения и пароль (секрет клиента), если задана `Managed` версия, поэтому эти записи конфигурации можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="ea709-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="ea709-203">Приложение развертывается в Azure, а Azure выполняет проверку подлинности приложения для доступа к Azure Key Vault только с использованием имени хранилища, хранящегося в *appsettings.json* файле.</span><span class="sxs-lookup"><span data-stu-id="ea709-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="ea709-204">Разверните пример приложения в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="ea709-205">Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы.</span><span class="sxs-lookup"><span data-stu-id="ea709-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="ea709-206">Получите идентификатор объекта из развертывания для использования в следующей команде.</span><span class="sxs-lookup"><span data-stu-id="ea709-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="ea709-207">Идентификатор объекта отображается в портал Azure на **Identity** панели службы приложений.</span><span class="sxs-lookup"><span data-stu-id="ea709-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="ea709-208">Используя Azure CLI и идентификатор объекта приложения, укажите приложение с `list` `get` разрешениями и для доступа к хранилищу ключей:</span><span class="sxs-lookup"><span data-stu-id="ea709-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="ea709-209">**Перезапустите приложение** с помощью Azure CLI, PowerShell или портал Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="ea709-210">Пример приложения:</span><span class="sxs-lookup"><span data-stu-id="ea709-210">The sample app:</span></span>

* <span data-ttu-id="ea709-211">Создает экземпляр `DefaultAzureCredential` класса, учетные данные пытаются получить маркер доступа из среды для ресурсов Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="ea709-212">Новый объект [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) создается с помощью `DefaultAzureCredential` экземпляра.</span><span class="sxs-lookup"><span data-stu-id="ea709-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="ea709-213">`Azure.Security.KeyVault.Secrets.Secrets`Экземпляр используется с реализацией по умолчанию `Azure.Extensions.AspNetCore.Configuration.Secrets` , которая загружает все секретные значения и заменяет двойные тире ( `--` ) на двоеточие ( `:` ) в именах ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="ea709-214">Пример значения имени хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="ea709-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="ea709-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea709-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="ea709-216">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="ea709-217">В среде разработки значения секрета имеют суффикс, `_dev` так как они предоставляются секретами пользователя.</span><span class="sxs-lookup"><span data-stu-id="ea709-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="ea709-218">В рабочей среде значения загружаются с `_prod` суффиксом, так как они предоставляются Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ea709-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="ea709-219">Если появится `Access denied` сообщение об ошибке, убедитесь, что приложение зарегистрировано в Azure AD и предоставил доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="ea709-220">Убедитесь, что вы перезапустите службу в Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="ea709-221">Сведения об использовании поставщика с управляемым удостоверением и конвейером Azure DevOps см. в статье [Создание подключения Azure Resource Manager службы к виртуальной машине с помощью управляемого удостоверения службы](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="ea709-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="ea709-222">Варианты настройки</span><span class="sxs-lookup"><span data-stu-id="ea709-222">Configuration options</span></span>

<span data-ttu-id="ea709-223">AddAzureKeyVault может принимать Азурекэйваултконфигуратионоптионс:</span><span class="sxs-lookup"><span data-stu-id="ea709-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="ea709-224">Свойство</span><span class="sxs-lookup"><span data-stu-id="ea709-224">Property</span></span>         | <span data-ttu-id="ea709-225">Описание</span><span class="sxs-lookup"><span data-stu-id="ea709-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="ea709-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` экземпляр, используемый для управления загрузкой секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="ea709-227">`Timespan` ожидание между попытками опроса хранилища ключей на предмет изменений.</span><span class="sxs-lookup"><span data-stu-id="ea709-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="ea709-228">Значение по умолчанию — `null` (конфигурация не перегружается).</span><span class="sxs-lookup"><span data-stu-id="ea709-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="ea709-229">Использование префикса имени ключа</span><span class="sxs-lookup"><span data-stu-id="ea709-229">Use a key name prefix</span></span>

<span data-ttu-id="ea709-230">AddAzureKeyVault предоставляет перегрузку, которая принимает реализацию `Azure.Extensions.AspNetCore.Configuration.Secrets` , которая позволяет управлять преобразованием секретов хранилища ключей в конфигурационные ключи.</span><span class="sxs-lookup"><span data-stu-id="ea709-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="ea709-231">Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, которое вы задают при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="ea709-232">Это позволяет, например, загружать секреты на основе версии приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="ea709-233">Не используйте префиксы в секретах хранилища ключей, чтобы размещать секреты для нескольких приложений в одном хранилище ключей или для размещения секретов среды (например, для *разработки* и *рабочих* секретов) в одном хранилище.</span><span class="sxs-lookup"><span data-stu-id="ea709-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="ea709-234">Мы рекомендуем использовать отдельные хранилища ключей для различных приложений и сред разработки и рабочей среды, чтобы изолировать среды приложений для обеспечения наивысшего уровня безопасности.</span><span class="sxs-lookup"><span data-stu-id="ea709-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="ea709-235">В следующем примере в хранилище ключей устанавливается секрет (и используется средство диспетчера секретов для среды разработки) `5000-AppSecret` (точки не допускаются в именах секретов хранилища ключей).</span><span class="sxs-lookup"><span data-stu-id="ea709-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="ea709-236">Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="ea709-237">Для другой версии приложения 5.1.0.0 секрет добавляется в хранилище ключей (и с помощью средства диспетчера секретов) для `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="ea709-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="ea709-238">Каждая версия приложения загружает значение секрета в своей конфигурации в виде `AppSecret` , отключая версию при загрузке секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="ea709-239">AddAzureKeyVault вызывается с настраиваемым `Azure.Extensions.AspNetCore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="ea709-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="ea709-240">`Azure.Extensions.AspNetCore.Configuration.Secrets`Реализация реагирует на префиксы версий секретов, чтобы загрузить правильный секрет в конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="ea709-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="ea709-241">`Load` загружает секрет, если его имя начинается с префикса.</span><span class="sxs-lookup"><span data-stu-id="ea709-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="ea709-242">Другие секреты не загружены.</span><span class="sxs-lookup"><span data-stu-id="ea709-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="ea709-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="ea709-243">`GetKey`:</span></span>
  * <span data-ttu-id="ea709-244">Удаляет префикс из имени секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="ea709-245">Заменяет два тире в любом имени с помощью `KeyDelimiter` , которое является разделителем, используемым в конфигурации (обычно это двоеточие).</span><span class="sxs-lookup"><span data-stu-id="ea709-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="ea709-246">Azure Key Vault не допускает двоеточия в именах секретов.</span><span class="sxs-lookup"><span data-stu-id="ea709-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="ea709-247">`Load`Метод вызывается алгоритмом поставщика, который выполняет перебор секретов хранилища для поиска тех, у которых есть префикс версии.</span><span class="sxs-lookup"><span data-stu-id="ea709-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="ea709-248">При обнаружении префикса версии с `Load` алгоритм использует `GetKey` метод, чтобы вернуть имя конфигурации для имени секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="ea709-249">Он отделяет префикс версии от имени секрета и возвращает оставшуюся часть имени секрета для загрузки в пары "имя-значение" конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="ea709-250">При реализации этого подхода:</span><span class="sxs-lookup"><span data-stu-id="ea709-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="ea709-251">Версия приложения, указанная в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="ea709-252">В следующем примере для версии приложения задано значение `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="ea709-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ea709-253">Убедитесь, что `<UserSecretsId>` свойство имеется в файле проекта приложения, где `{GUID}` является идентификатором GUID, предоставляемым пользователем:</span><span class="sxs-lookup"><span data-stu-id="ea709-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="ea709-254">Сохраните следующие секреты локально с помощью [средства диспетчера секретов](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="ea709-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="ea709-255">Секреты сохраняются в Azure Key Vault с помощью следующих Azure CLI команд:</span><span class="sxs-lookup"><span data-stu-id="ea709-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="ea709-256">При запуске приложения загружаются секреты хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="ea709-257">Строковый секрет для `5000-AppSecret` совпадает с версией приложения, указанной в файле проекта приложения ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="ea709-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="ea709-258">Версия `5000` (с тире) удаляется из имени ключа.</span><span class="sxs-lookup"><span data-stu-id="ea709-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="ea709-259">Во всем приложении при чтении конфигурации с ключом `AppSecret` загружается секретное значение.</span><span class="sxs-lookup"><span data-stu-id="ea709-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="ea709-260">Если версия приложения изменена в файле проекта на, `5.1.0.0` а приложение запускается снова, возвращается значение секрета `5.1.0.0_secret_value_dev` в среде разработки и `5.1.0.0_secret_value_prod` в рабочем окружении.</span><span class="sxs-lookup"><span data-stu-id="ea709-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="ea709-261">Вы также можете предоставить собственную <xref:Azure.Security.KeyVault.Secrets.SecretClient> реализацию для AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="ea709-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="ea709-262">Настраиваемый клиент позволяет совместно использовать один экземпляр клиента в приложении.</span><span class="sxs-lookup"><span data-stu-id="ea709-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="ea709-263">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="ea709-263">Bind an array to a class</span></span>

<span data-ttu-id="ea709-264">Поставщик может считывать значения конфигурации в массив для привязки к массиву POCO.</span><span class="sxs-lookup"><span data-stu-id="ea709-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="ea709-265">При чтении из источника конфигурации, позволяющего ключам содержать разделители в виде двоеточия ( `:` ), для различения ключей, составляющих массив ( `:0:` , `:1:` ,), используется числовой сегмент ключа &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="ea709-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="ea709-266">Дополнительные сведения см. в разделе [Configuration: привязка массива к классу](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="ea709-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="ea709-267">Ключи Azure Key Vault не могут использовать двоеточие в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="ea709-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="ea709-268">Подход, описанный в этом разделе, использует двойные тире ( `--` ) в качестве разделителя для иерархических значений (разделов).</span><span class="sxs-lookup"><span data-stu-id="ea709-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="ea709-269">Ключи массивов хранятся в Azure Key Vault с двойными тире и числовыми сегментами ключей ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="ea709-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="ea709-270">Изучите следующую конфигурацию поставщика ведения журнала [Serilog](https://serilog.net/) , предоставляемую файлом JSON.</span><span class="sxs-lookup"><span data-stu-id="ea709-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="ea709-271">В массиве определены два объектных литерала, `WriteTo` отражающих два *приемника* Serilog, которые описывают назначения для вывода журнала:</span><span class="sxs-lookup"><span data-stu-id="ea709-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="ea709-272">Конфигурация, показанная в предыдущем JSON-файле, хранится в Azure Key Vault с использованием нотации двойного тире ( `--` ) и числовых сегментов.</span><span class="sxs-lookup"><span data-stu-id="ea709-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="ea709-273">Ключ</span><span class="sxs-lookup"><span data-stu-id="ea709-273">Key</span></span> | <span data-ttu-id="ea709-274">Значение</span><span class="sxs-lookup"><span data-stu-id="ea709-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="ea709-275">Перезагрузка секретов</span><span class="sxs-lookup"><span data-stu-id="ea709-275">Reload secrets</span></span>

<span data-ttu-id="ea709-276">Секреты кэшируются до `IConfigurationRoot.Reload()` вызова метода.</span><span class="sxs-lookup"><span data-stu-id="ea709-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="ea709-277">Срок действия истек, отключен и обновленные секреты в хранилище ключей не учитываются приложением до тех пор, пока `Reload` не будет выполнено.</span><span class="sxs-lookup"><span data-stu-id="ea709-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="ea709-278">Отключенные секреты и секреты с истекшим сроком действия</span><span class="sxs-lookup"><span data-stu-id="ea709-278">Disabled and expired secrets</span></span>

<span data-ttu-id="ea709-279">При отключенных и истекших секретах создается исключение <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="ea709-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="ea709-280">Чтобы предотвратить создание приложения, укажите конфигурацию с помощью другого поставщика конфигурации или обновите закрытый или истекший секрет.</span><span class="sxs-lookup"><span data-stu-id="ea709-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ea709-281">Диагностика</span><span class="sxs-lookup"><span data-stu-id="ea709-281">Troubleshoot</span></span>

<span data-ttu-id="ea709-282">Если приложению не удается загрузить конфигурацию с помощью поставщика, в [инфраструктуру ведения журнала ASP.NET Core](xref:fundamentals/logging/index)записывается сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="ea709-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ea709-283">При следующих условиях загрузка конфигурации будет запрещена:</span><span class="sxs-lookup"><span data-stu-id="ea709-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="ea709-284">Приложение или сертификат неправильно настроены в Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ea709-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="ea709-285">Хранилище ключей не существует в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ea709-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="ea709-286">Приложение не имеет прав доступа к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="ea709-287">Политика доступа не включает и не имеет `Get` `List` разрешений.</span><span class="sxs-lookup"><span data-stu-id="ea709-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="ea709-288">В хранилище ключей данные конфигурации (пара "имя-значение") неправильно именованы, отсутствуют, отключены или устарели.</span><span class="sxs-lookup"><span data-stu-id="ea709-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="ea709-289">Приложение имеет неправильное имя хранилища ключей ( `KeyVaultName` ), идентификатор приложения Azure AD ( `AzureADApplicationId` ) или отпечаток сертификата Azure AD ( `AzureADCertThumbprint` ) или Azure AD директорид ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="ea709-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="ea709-290">Неверный ключ конфигурации (имя) в приложении для загружаемого значения.</span><span class="sxs-lookup"><span data-stu-id="ea709-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="ea709-291">При добавлении политики доступа для приложения в хранилище ключей была создана политика, но кнопка **сохранить** не была выбрана в пользовательском интерфейсе **политик доступа** .</span><span class="sxs-lookup"><span data-stu-id="ea709-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea709-292">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ea709-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="ea709-293">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="ea709-294">Microsoft Azure: Документация по Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="ea709-295">Создание и перенос ключей, защищенных АППАРАТным модулем безопасности, для Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="ea709-296">Класс KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="ea709-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="ea709-297">Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET</span><span class="sxs-lookup"><span data-stu-id="ea709-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="ea709-298">Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)</span><span class="sxs-lookup"><span data-stu-id="ea709-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ea709-299">В этом документе объясняется, как использовать поставщик конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложения из Azure Key Vault секреты.</span><span class="sxs-lookup"><span data-stu-id="ea709-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="ea709-300">Azure Key Vault — это облачная служба, которая помогает защитить криптографические ключи и секреты, используемые приложениями и службами.</span><span class="sxs-lookup"><span data-stu-id="ea709-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="ea709-301">Распространенные сценарии использования Azure Key Vault с приложениями ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ea709-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="ea709-302">Управление доступом к конфиденциальным данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ea709-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="ea709-303">Соблюдайте требования для FIPS 140-2 уровня 2 проверенных аппаратных модулей безопасности (HSM) при хранении данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ea709-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="ea709-304">[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([как скачивать](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ea709-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="ea709-305">Пакеты</span><span class="sxs-lookup"><span data-stu-id="ea709-305">Packages</span></span>

<span data-ttu-id="ea709-306">Добавьте ссылку на пакет в [Microsoft.Extensions.Configфигурации. Пакет AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="ea709-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="ea709-307">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="ea709-307">Sample app</span></span>

<span data-ttu-id="ea709-308">Пример приложения выполняется в одном из двух режимов, определяемых `#define` инструкцией в верхней части файла *Program.CS* :</span><span class="sxs-lookup"><span data-stu-id="ea709-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="ea709-309">`Certificate`: Демонстрируется использование идентификатора клиента Azure Key Vault и сертификата X. 509 для доступа к секретам, хранящимся в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ea709-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="ea709-310">Эту версию примера можно запустить из любого расположения, развернуть в службе приложений Azure или на любом узле, способном обслуживать ASP.NET Coreное приложение.</span><span class="sxs-lookup"><span data-stu-id="ea709-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="ea709-311">`Managed`: Демонстрируется использование [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для проверки подлинности приложения в Azure Key Vault с проверкой подлинности Azure AD без учетных данных, хранящихся в коде или конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="ea709-312">При использовании управляемых удостоверений для проверки подлинности не требуется идентификатор и пароль приложения Azure AD (секрет клиента).</span><span class="sxs-lookup"><span data-stu-id="ea709-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="ea709-313">`Managed`Версия примера должна быть развернута в Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="ea709-314">Следуйте указаниям в разделе [Использование управляемых удостоверений для ресурсов Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="ea709-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="ea709-315">Дополнительные сведения о настройке примера приложения с помощью директив препроцессора ( `#define` ) см. в разделе <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="ea709-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="ea709-316">Секретное хранилище в среде разработки</span><span class="sxs-lookup"><span data-stu-id="ea709-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="ea709-317">Локальное задание секретов с помощью [средства диспетчера секретов](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ea709-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ea709-318">Когда пример приложения выполняется на локальном компьютере в среде разработки, секреты загружаются из локального пользовательского хранилища секретов.</span><span class="sxs-lookup"><span data-stu-id="ea709-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="ea709-319">Для средства диспетчера секретов требуется `<UserSecretsId>` свойство в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="ea709-320">Задайте для свойства значение ( `{GUID}` ) любой уникальный GUID:</span><span class="sxs-lookup"><span data-stu-id="ea709-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="ea709-321">Секреты создаются как пары "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="ea709-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="ea709-322">Иерархические значения (разделы конфигурации) используют `:` (двоеточие) в качестве разделителя в ASP.NET Core именах ключей [конфигурации](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="ea709-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="ea709-323">Диспетчер секретов используется из командной оболочки, открытой в [корне содержимого](xref:fundamentals/index#content-root)проекта, где — это `{SECRET NAME}` имя, а `{SECRET VALUE}` — значение.</span><span class="sxs-lookup"><span data-stu-id="ea709-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="ea709-324">Выполните следующие команды в командной оболочке из [корневого каталога содержимого](xref:fundamentals/index#content-root) проекта, чтобы задать секреты для примера приложения:</span><span class="sxs-lookup"><span data-stu-id="ea709-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="ea709-325">Если эти секреты хранятся в Azure Key Vault в [хранилище секретов в рабочей среде с Azure Key Vaultным](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом, `_dev` суффикс изменяется на `_prod` .</span><span class="sxs-lookup"><span data-stu-id="ea709-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="ea709-326">Суффикс предоставляет визуальную подсказку в выходных данных приложения, указывающую источник значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="ea709-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="ea709-327">Секретное хранилище в рабочей среде с Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="ea709-328">Инструкции, приведенные в [кратком руководстве по установке и извлечению секретов из Azure Key Vault с помощью Azure CLI](/azure/key-vault/quick-create-cli) разделе, приведены здесь для создания Azure Key Vault и хранения секретов, используемых в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="ea709-329">Дополнительные сведения см. в разделе.</span><span class="sxs-lookup"><span data-stu-id="ea709-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="ea709-330">Откройте Azure Cloud Shell одним из следующих способов в [портал Azure](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="ea709-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="ea709-331">Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом.</span><span class="sxs-lookup"><span data-stu-id="ea709-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="ea709-332">Используйте строку поиска "Azure CLI" в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="ea709-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="ea709-333">Откройте Cloud Shell в браузере с помощью кнопки **запустить Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="ea709-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="ea709-334">Нажмите кнопку **Cloud Shell** в меню в правом верхнем углу портал Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="ea709-335">Дополнительные сведения см. в разделе [Azure CLI](/cli/azure/) и [Обзор Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="ea709-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="ea709-336">Если вы еще не прошли проверку подлинности, выполните вход с помощью `az login` команды.</span><span class="sxs-lookup"><span data-stu-id="ea709-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="ea709-337">Создайте группу ресурсов с помощью следующей команды, где `{RESOURCE GROUP NAME}` — это имя группы ресурсов для новой группы ресурсов, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="ea709-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="ea709-338">Создайте хранилище ключей в группе ресурсов с помощью следующей команды, где `{KEY VAULT NAME}` — это имя нового хранилища ключей, а `{LOCATION}` — регион Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="ea709-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="ea709-339">Создайте секреты в хранилище ключей в виде пар "имя-значение".</span><span class="sxs-lookup"><span data-stu-id="ea709-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="ea709-340">Azure Key Vault имена секретов ограничены буквенно-цифровыми символами и тире.</span><span class="sxs-lookup"><span data-stu-id="ea709-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="ea709-341">Иерархические значения (разделы конфигурации) используют `--` (два тире) в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="ea709-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="ea709-342">Двоеточия, которые обычно используются для разделения раздела из подраздела в [ASP.NET Core конфигурации](xref:fundamentals/configuration/index), не допускаются в именах секретов хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="ea709-343">Поэтому при загрузке секретов в конфигурацию приложения используются два дефиса и они переключаются на двоеточие.</span><span class="sxs-lookup"><span data-stu-id="ea709-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="ea709-344">Следующие секреты предназначены для использования с примером приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="ea709-345">Эти значения включают `_prod` суффикс, чтобы отличить `_dev` значения суффиксов, загруженных в среде разработки, от секретов пользователя.</span><span class="sxs-lookup"><span data-stu-id="ea709-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="ea709-346">Замените на `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем шаге:</span><span class="sxs-lookup"><span data-stu-id="ea709-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="ea709-347">Использование идентификатора приложения и сертификата X. 509 для приложений, не размещенных в Azure</span><span class="sxs-lookup"><span data-stu-id="ea709-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="ea709-348">Настройте Azure AD, Azure Key Vault и приложение для использования идентификатора Azure Active Directory приложения и сертификата X. 509 для проверки подлинности в хранилище ключей, **Если приложение размещено за пределами Azure**.</span><span class="sxs-lookup"><span data-stu-id="ea709-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="ea709-349">См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="ea709-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="ea709-350">Хотя использование идентификатора приложения и сертификата X. 509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые удостоверения для ресурсов Azure](#use-managed-identities-for-azure-resources) при размещении приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="ea709-351">Для управляемых удостоверений не требуется хранить сертификат в приложении или в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="ea709-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="ea709-352">В примере приложения используется идентификатор приложения и сертификат X. 509, если `#define` инструкция в верхней части файла *Program.CS* имеет значение `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="ea709-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="ea709-353">Создайте архивный сертификат PKCS # 12 (*PFX*-файл).</span><span class="sxs-lookup"><span data-stu-id="ea709-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="ea709-354">Для создания сертификатов можно использовать [MakeCert в Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="ea709-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="ea709-355">Установите сертификат в личное хранилище сертификатов текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="ea709-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="ea709-356">Пометка ключа как экспортируемого необязательна.</span><span class="sxs-lookup"><span data-stu-id="ea709-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="ea709-357">Запишите отпечаток сертификата, который будет использоваться далее в этом процессе.</span><span class="sxs-lookup"><span data-stu-id="ea709-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="ea709-358">Экспортируйте архивный сертификат PKCS # 12 (*PFX*) как сертификат в формате DER (*CER*).</span><span class="sxs-lookup"><span data-stu-id="ea709-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="ea709-359">Зарегистрируйте приложение в Azure AD (**Регистрация приложений**).</span><span class="sxs-lookup"><span data-stu-id="ea709-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="ea709-360">Отправьте сертификат в кодировке DER (*CER*) в Azure AD:</span><span class="sxs-lookup"><span data-stu-id="ea709-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="ea709-361">Выберите приложение в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="ea709-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="ea709-362">Перейдите к разделу **сертификаты & секреты**.</span><span class="sxs-lookup"><span data-stu-id="ea709-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="ea709-363">Выберите **отправить сертификат** , чтобы отправить сертификат, который содержит открытый ключ.</span><span class="sxs-lookup"><span data-stu-id="ea709-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="ea709-364">Сертификат *. cer*, *. pem* или *. CRT* является приемлемым.</span><span class="sxs-lookup"><span data-stu-id="ea709-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="ea709-365">Сохраните имя хранилища ключей, идентификатор приложения и отпечаток сертификата в *appsettings.json* файле приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="ea709-366">Перейдите к **разделу хранилища ключей** в портал Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="ea709-367">Выберите хранилище ключей, созданное в [хранилище секретов в рабочей среде с помощью Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) разделе.</span><span class="sxs-lookup"><span data-stu-id="ea709-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="ea709-368">Выберите **Политики доступа**.</span><span class="sxs-lookup"><span data-stu-id="ea709-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="ea709-369">Нажмите **Добавить политику доступа**.</span><span class="sxs-lookup"><span data-stu-id="ea709-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="ea709-370">Откройте **разрешения для секрета** и предоставьте приложению разрешения **Get** и **List** .</span><span class="sxs-lookup"><span data-stu-id="ea709-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="ea709-371">Щелкните **выбрать субъект** и выберите зарегистрированное приложение по имени.</span><span class="sxs-lookup"><span data-stu-id="ea709-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="ea709-372">Нажмите кнопку **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="ea709-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="ea709-373">Нажмите кнопку **ОК**.</span><span class="sxs-lookup"><span data-stu-id="ea709-373">Select **OK**.</span></span>
1. <span data-ttu-id="ea709-374">Щелкните **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="ea709-374">Select **Save**.</span></span>
1. <span data-ttu-id="ea709-375">Разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="ea709-375">Deploy the app.</span></span>

<span data-ttu-id="ea709-376">`Certificate`Пример приложения получает значения конфигурации из `IConfigurationRoot` с тем же именем, что и имя секрета:</span><span class="sxs-lookup"><span data-stu-id="ea709-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="ea709-377">Неиерархические значения. значение для берется `SecretName` `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="ea709-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="ea709-378">Иерархические значения (разделы): используйте `:` нотацию (двоеточие) или `GetSection` метод расширения.</span><span class="sxs-lookup"><span data-stu-id="ea709-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="ea709-379">Используйте любой из этих подходов для получения значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="ea709-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="ea709-380">Сертификат X. 509 управляется ОС.</span><span class="sxs-lookup"><span data-stu-id="ea709-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="ea709-381">Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> со значениями, предоставленными *appsettings.json* файлом:</span><span class="sxs-lookup"><span data-stu-id="ea709-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="ea709-382">Примеры значений</span><span class="sxs-lookup"><span data-stu-id="ea709-382">Example values:</span></span>

* <span data-ttu-id="ea709-383">Имя хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="ea709-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="ea709-384">Идентификатор приложения: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="ea709-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="ea709-385">Отпечаток сертификата: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="ea709-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="ea709-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea709-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="ea709-387">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="ea709-388">В среде разработки значения секрета загружаются с `_dev` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="ea709-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="ea709-389">В рабочей среде значения загружаются с `_prod` суффиксом.</span><span class="sxs-lookup"><span data-stu-id="ea709-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="ea709-390">Использование управляемых удостоверений для ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="ea709-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="ea709-391">**Приложение, развернутое в Azure** , может использовать преимущества [управляемых удостоверений для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview), что позволяет приложению проходить проверку подлинности в Azure Key Vault с помощью проверки подлинности Azure AD без учетных данных (идентификатор приложения и пароль/секрет клиента), хранимых в приложении.</span><span class="sxs-lookup"><span data-stu-id="ea709-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="ea709-392">Пример приложения использует управляемые удостоверения для ресурсов Azure, когда `#define` инструкция в верхней части файла *Program.CS* имеет значение `Managed` .</span><span class="sxs-lookup"><span data-stu-id="ea709-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="ea709-393">Введите имя хранилища в *appsettings.json* файл приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="ea709-394">В примере приложения не требуется идентификатор приложения и пароль (секрет клиента), если задана `Managed` версия, поэтому эти записи конфигурации можно игнорировать.</span><span class="sxs-lookup"><span data-stu-id="ea709-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="ea709-395">Приложение развертывается в Azure, а Azure выполняет проверку подлинности приложения для доступа к Azure Key Vault только с использованием имени хранилища, хранящегося в *appsettings.json* файле.</span><span class="sxs-lookup"><span data-stu-id="ea709-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="ea709-396">Разверните пример приложения в службе приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="ea709-397">Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы.</span><span class="sxs-lookup"><span data-stu-id="ea709-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="ea709-398">Получите идентификатор объекта из развертывания для использования в следующей команде.</span><span class="sxs-lookup"><span data-stu-id="ea709-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="ea709-399">Идентификатор объекта отображается в портал Azure на **Identity** панели службы приложений.</span><span class="sxs-lookup"><span data-stu-id="ea709-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="ea709-400">Используя Azure CLI и идентификатор объекта приложения, укажите приложение с `list` `get` разрешениями и для доступа к хранилищу ключей:</span><span class="sxs-lookup"><span data-stu-id="ea709-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="ea709-401">**Перезапустите приложение** с помощью Azure CLI, PowerShell или портал Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="ea709-402">Пример приложения:</span><span class="sxs-lookup"><span data-stu-id="ea709-402">The sample app:</span></span>

* <span data-ttu-id="ea709-403">Создает экземпляр `AzureServiceTokenProvider` класса без строки подключения.</span><span class="sxs-lookup"><span data-stu-id="ea709-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="ea709-404">Если строка подключения не указана, поставщик пытается получить маркер доступа из управляемых удостоверений для ресурсов Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="ea709-405">Новый объект <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается с помощью `AzureServiceTokenProvider` обратного вызова токена экземпляра.</span><span class="sxs-lookup"><span data-stu-id="ea709-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="ea709-406"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Экземпляр используется с реализацией по умолчанию <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , которая загружает все секретные значения и заменяет двойные тире ( `--` ) на двоеточие ( `:` ) в именах ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="ea709-407">Пример значения имени хранилища ключей: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="ea709-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="ea709-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ea709-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="ea709-409">При запуске приложения на веб-странице отображаются загруженные значения секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="ea709-410">В среде разработки значения секрета имеют суффикс, `_dev` так как они предоставляются секретами пользователя.</span><span class="sxs-lookup"><span data-stu-id="ea709-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="ea709-411">В рабочей среде значения загружаются с `_prod` суффиксом, так как они предоставляются Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ea709-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="ea709-412">Если появится `Access denied` сообщение об ошибке, убедитесь, что приложение зарегистрировано в Azure AD и предоставил доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="ea709-413">Убедитесь, что вы перезапустите службу в Azure.</span><span class="sxs-lookup"><span data-stu-id="ea709-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="ea709-414">Сведения об использовании поставщика с управляемым удостоверением и конвейером Azure DevOps см. в статье [Создание подключения Azure Resource Manager службы к виртуальной машине с помощью управляемого удостоверения службы](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="ea709-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="ea709-415">Использование префикса имени ключа</span><span class="sxs-lookup"><span data-stu-id="ea709-415">Use a key name prefix</span></span>

<span data-ttu-id="ea709-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> предоставляет перегрузку, которая принимает реализацию <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , которая позволяет управлять преобразованием секретов хранилища ключей в конфигурационные ключи.</span><span class="sxs-lookup"><span data-stu-id="ea709-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="ea709-417">Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, которое вы задают при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="ea709-418">Это позволяет, например, загружать секреты на основе версии приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="ea709-419">Не используйте префиксы в секретах хранилища ключей, чтобы размещать секреты для нескольких приложений в одном хранилище ключей или для размещения секретов среды (например, для *разработки* и *рабочих* секретов) в одном хранилище.</span><span class="sxs-lookup"><span data-stu-id="ea709-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="ea709-420">Мы рекомендуем использовать отдельные хранилища ключей для различных приложений и сред разработки и рабочей среды, чтобы изолировать среды приложений для обеспечения наивысшего уровня безопасности.</span><span class="sxs-lookup"><span data-stu-id="ea709-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="ea709-421">В следующем примере в хранилище ключей устанавливается секрет (и используется средство диспетчера секретов для среды разработки) `5000-AppSecret` (точки не допускаются в именах секретов хранилища ключей).</span><span class="sxs-lookup"><span data-stu-id="ea709-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="ea709-422">Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="ea709-423">Для другой версии приложения 5.1.0.0 секрет добавляется в хранилище ключей (и с помощью средства диспетчера секретов) для `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="ea709-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="ea709-424">Каждая версия приложения загружает значение секрета в своей конфигурации в виде `AppSecret` , отключая версию при загрузке секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="ea709-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> вызывается с настраиваемым <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="ea709-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="ea709-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>Реализация реагирует на префиксы версий секретов, чтобы загрузить правильный секрет в конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="ea709-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="ea709-427">`Load` загружает секрет, если его имя начинается с префикса.</span><span class="sxs-lookup"><span data-stu-id="ea709-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="ea709-428">Другие секреты не загружены.</span><span class="sxs-lookup"><span data-stu-id="ea709-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="ea709-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="ea709-429">`GetKey`:</span></span>
  * <span data-ttu-id="ea709-430">Удаляет префикс из имени секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="ea709-431">Заменяет два тире в любом имени с помощью `KeyDelimiter` , которое является разделителем, используемым в конфигурации (обычно это двоеточие).</span><span class="sxs-lookup"><span data-stu-id="ea709-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="ea709-432">Azure Key Vault не допускает двоеточия в именах секретов.</span><span class="sxs-lookup"><span data-stu-id="ea709-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="ea709-433">`Load`Метод вызывается алгоритмом поставщика, который выполняет перебор секретов хранилища для поиска тех, у которых есть префикс версии.</span><span class="sxs-lookup"><span data-stu-id="ea709-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="ea709-434">При обнаружении префикса версии с `Load` алгоритм использует `GetKey` метод, чтобы вернуть имя конфигурации для имени секрета.</span><span class="sxs-lookup"><span data-stu-id="ea709-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="ea709-435">Он отделяет префикс версии от имени секрета и возвращает оставшуюся часть имени секрета для загрузки в пары "имя-значение" конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="ea709-436">При реализации этого подхода:</span><span class="sxs-lookup"><span data-stu-id="ea709-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="ea709-437">Версия приложения, указанная в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="ea709-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="ea709-438">В следующем примере для версии приложения задано значение `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="ea709-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ea709-439">Убедитесь, что `<UserSecretsId>` свойство имеется в файле проекта приложения, где `{GUID}` является идентификатором GUID, предоставляемым пользователем:</span><span class="sxs-lookup"><span data-stu-id="ea709-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="ea709-440">Сохраните следующие секреты локально с помощью [средства диспетчера секретов](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="ea709-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="ea709-441">Секреты сохраняются в Azure Key Vault с помощью следующих Azure CLI команд:</span><span class="sxs-lookup"><span data-stu-id="ea709-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="ea709-442">При запуске приложения загружаются секреты хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="ea709-443">Строковый секрет для `5000-AppSecret` совпадает с версией приложения, указанной в файле проекта приложения ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="ea709-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="ea709-444">Версия `5000` (с тире) удаляется из имени ключа.</span><span class="sxs-lookup"><span data-stu-id="ea709-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="ea709-445">Во всем приложении при чтении конфигурации с ключом `AppSecret` загружается секретное значение.</span><span class="sxs-lookup"><span data-stu-id="ea709-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="ea709-446">Если версия приложения изменена в файле проекта на, `5.1.0.0` а приложение запускается снова, возвращается значение секрета `5.1.0.0_secret_value_dev` в среде разработки и `5.1.0.0_secret_value_prod` в рабочем окружении.</span><span class="sxs-lookup"><span data-stu-id="ea709-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="ea709-447">Можно также предоставить собственную <xref:Microsoft.Azure.KeyVault.KeyVaultClient> реализацию для <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="ea709-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="ea709-448">Настраиваемый клиент позволяет совместно использовать один экземпляр клиента в приложении.</span><span class="sxs-lookup"><span data-stu-id="ea709-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="ea709-449">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="ea709-449">Bind an array to a class</span></span>

<span data-ttu-id="ea709-450">Поставщик может считывать значения конфигурации в массив для привязки к массиву POCO.</span><span class="sxs-lookup"><span data-stu-id="ea709-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="ea709-451">При чтении из источника конфигурации, позволяющего ключам содержать разделители в виде двоеточия ( `:` ), для различения ключей, составляющих массив ( `:0:` , `:1:` ,), используется числовой сегмент ключа &hellip; `:{n}:` .</span><span class="sxs-lookup"><span data-stu-id="ea709-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="ea709-452">Дополнительные сведения см. в разделе [Configuration: привязка массива к классу](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="ea709-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="ea709-453">Ключи Azure Key Vault не могут использовать двоеточие в качестве разделителя.</span><span class="sxs-lookup"><span data-stu-id="ea709-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="ea709-454">Подход, описанный в этом разделе, использует двойные тире ( `--` ) в качестве разделителя для иерархических значений (разделов).</span><span class="sxs-lookup"><span data-stu-id="ea709-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="ea709-455">Ключи массивов хранятся в Azure Key Vault с двойными тире и числовыми сегментами ключей ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="ea709-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="ea709-456">Изучите следующую конфигурацию поставщика ведения журнала [Serilog](https://serilog.net/) , предоставляемую файлом JSON.</span><span class="sxs-lookup"><span data-stu-id="ea709-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="ea709-457">В массиве определены два объектных литерала, `WriteTo` отражающих два *приемника* Serilog, которые описывают назначения для вывода журнала:</span><span class="sxs-lookup"><span data-stu-id="ea709-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="ea709-458">Конфигурация, показанная в предыдущем JSON-файле, хранится в Azure Key Vault с использованием нотации двойного тире ( `--` ) и числовых сегментов.</span><span class="sxs-lookup"><span data-stu-id="ea709-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="ea709-459">Ключ</span><span class="sxs-lookup"><span data-stu-id="ea709-459">Key</span></span> | <span data-ttu-id="ea709-460">Значение</span><span class="sxs-lookup"><span data-stu-id="ea709-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="ea709-461">Перезагрузка секретов</span><span class="sxs-lookup"><span data-stu-id="ea709-461">Reload secrets</span></span>

<span data-ttu-id="ea709-462">Секреты кэшируются до `IConfigurationRoot.Reload()` вызова метода.</span><span class="sxs-lookup"><span data-stu-id="ea709-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="ea709-463">Срок действия истек, отключен и обновленные секреты в хранилище ключей не учитываются приложением до тех пор, пока `Reload` не будет выполнено.</span><span class="sxs-lookup"><span data-stu-id="ea709-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="ea709-464">Отключенные секреты и секреты с истекшим сроком действия</span><span class="sxs-lookup"><span data-stu-id="ea709-464">Disabled and expired secrets</span></span>

<span data-ttu-id="ea709-465">При отключенных и истекших секретах создается исключение <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="ea709-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="ea709-466">Чтобы предотвратить создание приложения, укажите конфигурацию с помощью другого поставщика конфигурации или обновите закрытый или истекший секрет.</span><span class="sxs-lookup"><span data-stu-id="ea709-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ea709-467">Диагностика</span><span class="sxs-lookup"><span data-stu-id="ea709-467">Troubleshoot</span></span>

<span data-ttu-id="ea709-468">Если приложению не удается загрузить конфигурацию с помощью поставщика, в [инфраструктуру ведения журнала ASP.NET Core](xref:fundamentals/logging/index)записывается сообщение об ошибке.</span><span class="sxs-lookup"><span data-stu-id="ea709-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ea709-469">При следующих условиях загрузка конфигурации будет запрещена:</span><span class="sxs-lookup"><span data-stu-id="ea709-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="ea709-470">Приложение или сертификат неправильно настроены в Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="ea709-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="ea709-471">Хранилище ключей не существует в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="ea709-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="ea709-472">Приложение не имеет прав доступа к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="ea709-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="ea709-473">Политика доступа не включает и не имеет `Get` `List` разрешений.</span><span class="sxs-lookup"><span data-stu-id="ea709-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="ea709-474">В хранилище ключей данные конфигурации (пара "имя-значение") неправильно именованы, отсутствуют, отключены или устарели.</span><span class="sxs-lookup"><span data-stu-id="ea709-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="ea709-475">Приложение имеет неправильное имя хранилища ключей ( `KeyVaultName` ), идентификатор приложения Azure AD ( `AzureADApplicationId` ) или отпечаток сертификата Azure AD ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="ea709-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="ea709-476">Неверный ключ конфигурации (имя) в приложении для загружаемого значения.</span><span class="sxs-lookup"><span data-stu-id="ea709-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="ea709-477">При добавлении политики доступа для приложения в хранилище ключей была создана политика, но кнопка **сохранить** не была выбрана в пользовательском интерфейсе **политик доступа** .</span><span class="sxs-lookup"><span data-stu-id="ea709-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea709-478">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="ea709-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="ea709-479">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="ea709-480">Microsoft Azure: Документация по Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="ea709-481">Создание и перенос ключей, защищенных АППАРАТным модулем безопасности, для Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="ea709-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="ea709-482">Класс KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="ea709-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="ea709-483">Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET</span><span class="sxs-lookup"><span data-stu-id="ea709-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="ea709-484">Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)</span><span class="sxs-lookup"><span data-stu-id="ea709-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
