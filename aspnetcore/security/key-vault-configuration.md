---
title: Поставщик конфигурации клавиатуры Azure в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать поставщика конфигурации клавиатур Azure Key Vault для настройки приложения с использованием пар именных значений, загруженных во время выполнения.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228170"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="7783e-103">Поставщик конфигурации клавиатуры Azure в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7783e-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="7783e-104">[Эндрю Стэнтон-Медсестра](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7783e-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7783e-105">В этом документе объясняется, как использовать поставщика конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложений из секретов Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-105">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="7783e-106">Azure Key Vault — это облачный сервис, который помогает охранять криптографические ключи и секреты, используемые приложениями и службами.</span><span class="sxs-lookup"><span data-stu-id="7783e-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="7783e-107">Общие сценарии использования Azure Key Vault с ASP.NET основными приложениями включают:</span><span class="sxs-lookup"><span data-stu-id="7783e-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="7783e-108">Контроль доступа к конфиденциальным данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="7783e-109">Соответствие требованию fiPS 140-2 Уровень 2 проверенных модулей аппаратной безопасности (HSM) при хранении данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="7783e-110">[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(как скачать)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7783e-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="7783e-111">Пакеты</span><span class="sxs-lookup"><span data-stu-id="7783e-111">Packages</span></span>

<span data-ttu-id="7783e-112">Добавьте ссылку на пакет [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="7783e-112">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="7783e-113">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="7783e-113">Sample app</span></span>

<span data-ttu-id="7783e-114">Пример приложения работает в любом из двух `#define` режимов, определяемых заявлением в верхней части *файла Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="7783e-114">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="7783e-115">`Certificate`&ndash; Демонстрирует использование идентификатора клиента Azure Key Vault и сертификата X.509 для доступа к секретам, хранящимся в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-115">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="7783e-116">Эта версия образца может быть запущена из любого места, развернута в службе приложений Azure или любого узла, способного обслуживать ASP.NET приложение Core.</span><span class="sxs-lookup"><span data-stu-id="7783e-116">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="7783e-117">`Managed`&ndash; Демонстрирует, как использовать [управляемые идентификаторы для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для аутентификации приложения в Хранилище ключей Azure с аутентификацией Azure AD без учетных данных, хранящихся в коде или конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-117">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="7783e-118">При использовании управляемых идентификационных данных для проверки подлинности идентификатор приложения Azure AD и пароль (Client Secret) не требуются.</span><span class="sxs-lookup"><span data-stu-id="7783e-118">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="7783e-119">Версия `Managed` образца должна быть развернута в Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-119">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="7783e-120">Следуйте инструкциям в разделе [«Использование управляемых идентификаторов» для ресурсов Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="7783e-120">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="7783e-121">Для получения дополнительной информации о том, как настроить`#define`образец приложения с помощью препроцессорных директив ( , см. <xref:index#preprocessor-directives-in-sample-code></span><span class="sxs-lookup"><span data-stu-id="7783e-121">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="7783e-122">Секретное хранилище в среде разработки</span><span class="sxs-lookup"><span data-stu-id="7783e-122">Secret storage in the Development environment</span></span>

<span data-ttu-id="7783e-123">Установите секреты локально с помощью [инструмента Secret Manager.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="7783e-123">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7783e-124">Когда пример приложения работает на локальной машине в среде разработки, секреты загружаются из местного магазина Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="7783e-124">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="7783e-125">Инструмент «Секретный `<UserSecretsId>` менеджер» требует свойства в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-125">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="7783e-126">Установите значение`{GUID}`свойства ( ) на любой уникальный GUID:</span><span class="sxs-lookup"><span data-stu-id="7783e-126">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="7783e-127">Секреты создаются как пары имено-ценности.</span><span class="sxs-lookup"><span data-stu-id="7783e-127">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="7783e-128">Иерархические значения (разделы конфигурации) используют a `:` (colon) в качестве сепаратора в ASP.NET ключевых имен [конфигурации Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="7783e-128">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="7783e-129">Секретный менеджер используется из командной оболочки, открытой для `{SECRET NAME}` [корня содержимого](xref:fundamentals/index#content-root)проекта, где находится имя и `{SECRET VALUE}` значение:</span><span class="sxs-lookup"><span data-stu-id="7783e-129">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="7783e-130">Выполните следующие команды в командной оболочке из [корня содержимого](xref:fundamentals/index#content-root) проекта, чтобы установить секреты для примера приложения:</span><span class="sxs-lookup"><span data-stu-id="7783e-130">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="7783e-131">Когда эти секреты хранятся в Хранилище ключей Azure в [секретном хранилище в производственной среде с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault, `_dev` суффикс изменяется на `_prod`.</span><span class="sxs-lookup"><span data-stu-id="7783e-131">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="7783e-132">Суффикс обеспечивает визуальный сигнал на выходе приложения с указанием источника значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-132">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="7783e-133">Секретное хранилище в производственной среде с Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7783e-133">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="7783e-134">Инструкции, предоставленные [quickstart: Установить и получить секрет из Azure Key Vault с помощью темы Azure CLI,](/azure/key-vault/quick-create-cli) суммируются здесь для создания Убежища ключей Azure и хранения секретов, используемых в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-134">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="7783e-135">Для более подробной информации обратитесь к теме.</span><span class="sxs-lookup"><span data-stu-id="7783e-135">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="7783e-136">Откройте оболочку облака Azure, используя любой из следующих методов [на портале Azure:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="7783e-136">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="7783e-137">Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом.</span><span class="sxs-lookup"><span data-stu-id="7783e-137">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="7783e-138">Используйте строку поиска "Azure CLI" в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="7783e-138">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="7783e-139">Откройте облачную оболочку в браузере с помощью кнопки **Launch Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="7783e-139">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="7783e-140">Выберите кнопку **Cloud Shell** в меню в правом верхнем углу портала Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-140">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="7783e-141">Для получения дополнительной информации смотрите [обзор Azure CLI](/cli/azure/) и [обзор облачной оболочки Azure.](/azure/cloud-shell/overview)</span><span class="sxs-lookup"><span data-stu-id="7783e-141">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="7783e-142">Если вы еще не проверены, вопийте команду. `az login`</span><span class="sxs-lookup"><span data-stu-id="7783e-142">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="7783e-143">Создайте группу ресурсов со `{RESOURCE GROUP NAME}` следующей командой, где находится `{LOCATION}` название группы ресурсов для новой группы ресурсов и является областью Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="7783e-143">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7783e-144">Создайте хранилище ключей в группе ресурсов `{KEY VAULT NAME}` со следующей командой, `{LOCATION}` где находится название нового хранилища ключей и является областью Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="7783e-144">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7783e-145">Создавайте секреты в хранилище ключей в виде пар именной ценности.</span><span class="sxs-lookup"><span data-stu-id="7783e-145">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="7783e-146">Секретные имена Azure Key Vault ограничены буквенно-цифровыми персонажами и тире.</span><span class="sxs-lookup"><span data-stu-id="7783e-146">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="7783e-147">Иерархические значения (разделы `--` конфигурации) используют (два тире) в качестве сепаратора.</span><span class="sxs-lookup"><span data-stu-id="7783e-147">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="7783e-148">Колоны, которые обычно используются для разграничения раздела из подключаемых ASP.NET [конфигурации Core,](xref:fundamentals/configuration/index)не допускаются в секретных именах хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-148">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="7783e-149">Таким образом, два тире используются и обмениваются на толстую кишку, когда секреты загружаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-149">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="7783e-150">Следующие секреты для использования с образцом приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-150">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="7783e-151">Значения включают `_prod` суффикс, чтобы отличить их от `_dev` значений суффикса, загруженных в среде разработки, от пользовательских секретов.</span><span class="sxs-lookup"><span data-stu-id="7783e-151">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="7783e-152">Замените `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем этапе:</span><span class="sxs-lookup"><span data-stu-id="7783e-152">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="7783e-153">Идентификатор приложений и сертификат X.509 для приложений, не хозяек, размещенных в Azure</span><span class="sxs-lookup"><span data-stu-id="7783e-153">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="7783e-154">Напередите Azure AD, Azure Key Vault и приложение для использования идентификатора приложений Active Directory Azure и сертификата X.509 для проверки подлинности хранилища ключей **при размещении приложения за пределами Azure.**</span><span class="sxs-lookup"><span data-stu-id="7783e-154">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="7783e-155">См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="7783e-155">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="7783e-156">Хотя использование идентификатора приложений и сертификата X.509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые идентификаторы для ресурсов Azure](#use-managed-identities-for-azure-resources) при хостинге приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-156">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="7783e-157">Управляемые идентификаторы не требуют хранения сертификата в приложении или в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="7783e-157">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="7783e-158">В примере приложения используется идентификатор приложения `#define` и сертификат X.509, `Certificate`когда заявление в верхней части *Program.cs* файла установлено.</span><span class="sxs-lookup"><span data-stu-id="7783e-158">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="7783e-159">Создайте сертификат PKCS-12 *(.pfx)* сертификат.</span><span class="sxs-lookup"><span data-stu-id="7783e-159">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="7783e-160">Варианты создания сертификатов включают [MakeCert на Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL.](https://www.openssl.org/)</span><span class="sxs-lookup"><span data-stu-id="7783e-160">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="7783e-161">Установите сертификат в личный магазин сертификатов текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="7783e-161">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="7783e-162">Маркировка ключа как экспортируемого не является обязательной.</span><span class="sxs-lookup"><span data-stu-id="7783e-162">Marking the key as exportable is optional.</span></span> <span data-ttu-id="7783e-163">Обратите внимание на отпечаток пальца сертификата, который используется позже в этом процессе.</span><span class="sxs-lookup"><span data-stu-id="7783e-163">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="7783e-164">Экспорт архива PKCS-12 (*.pfx*) сертификат в качестве DER-кодированного сертификата (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="7783e-164">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="7783e-165">Зарегистрируйте приложение с Помощью Azure AD **(регистрация приложений).**</span><span class="sxs-lookup"><span data-stu-id="7783e-165">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="7783e-166">Загрузите закодированный сертификат DER *(.cer)* в Azure AD:</span><span class="sxs-lookup"><span data-stu-id="7783e-166">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="7783e-167">Выберите приложение в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7783e-167">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="7783e-168">Перейдите к **сертификатам & секреты**.</span><span class="sxs-lookup"><span data-stu-id="7783e-168">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="7783e-169">Выберите **сертификат загрузки** для загрузки сертификата, который содержит общедоступный ключ.</span><span class="sxs-lookup"><span data-stu-id="7783e-169">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="7783e-170">Сертификат *.cer*, *.pem*или *.crt* является приемлемым.</span><span class="sxs-lookup"><span data-stu-id="7783e-170">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="7783e-171">Храните имя хранилища ключей, идентификатор приложения и отпечаток пальца сертификата в файле *appsettings.json* приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-171">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="7783e-172">Перейдите к **хранилищам ключей** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-172">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="7783e-173">Выберите хранилище ключей, созданное в [секретном хранилище в среде Production, с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-173">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="7783e-174">Выберите **политики доступа.**</span><span class="sxs-lookup"><span data-stu-id="7783e-174">Select **Access policies**.</span></span>
1. <span data-ttu-id="7783e-175">Выберите **политику добавления доступа.**</span><span class="sxs-lookup"><span data-stu-id="7783e-175">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="7783e-176">Открытые **секретные разрешения** и предоставить приложение с **получить** и **список** разрешений.</span><span class="sxs-lookup"><span data-stu-id="7783e-176">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="7783e-177">Выберите **основной принцип** и выберите зарегистрированное приложение по имени.</span><span class="sxs-lookup"><span data-stu-id="7783e-177">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="7783e-178">Нажмите кнопку **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="7783e-178">Select the **Select** button.</span></span>
1. <span data-ttu-id="7783e-179">Щелкните **ОК**.</span><span class="sxs-lookup"><span data-stu-id="7783e-179">Select **OK**.</span></span>
1. <span data-ttu-id="7783e-180">Щелкните **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7783e-180">Select **Save**.</span></span>
1. <span data-ttu-id="7783e-181">Разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="7783e-181">Deploy the app.</span></span>

<span data-ttu-id="7783e-182">Пример `Certificate` приложения получает значения конфигурации `IConfigurationRoot` с тем же именем, что и секретное имя:</span><span class="sxs-lookup"><span data-stu-id="7783e-182">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="7783e-183">Неиерархические значения: Значение для `SecretName` получено `config["SecretName"]`с .</span><span class="sxs-lookup"><span data-stu-id="7783e-183">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="7783e-184">Иерархические значения (разделы): Используйте `:` (колонию) обозначения или метод `GetSection` расширения.</span><span class="sxs-lookup"><span data-stu-id="7783e-184">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="7783e-185">Используйте любой из этих подходов для получения значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="7783e-185">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="7783e-186">Сертификат X.509 управляется ОС.</span><span class="sxs-lookup"><span data-stu-id="7783e-186">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="7783e-187">Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> значения, поставляемые файлом *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="7783e-187">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="7783e-188">Примеры значений:</span><span class="sxs-lookup"><span data-stu-id="7783e-188">Example values:</span></span>

* <span data-ttu-id="7783e-189">Имя хранилища ключа:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="7783e-189">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="7783e-190">Идентификатор приложения:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="7783e-190">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="7783e-191">Отпечаток пальца сертификата:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="7783e-191">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="7783e-192">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7783e-192">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="7783e-193">При запуске приложения на веб-странице отображаются загруженные секретные значения.</span><span class="sxs-lookup"><span data-stu-id="7783e-193">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7783e-194">В среде разработки секретные `_dev` значения загружаются суффиксом.</span><span class="sxs-lookup"><span data-stu-id="7783e-194">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="7783e-195">В производственной среде значения загружаются суффиксом. `_prod`</span><span class="sxs-lookup"><span data-stu-id="7783e-195">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="7783e-196">Используйте управляемые идентификаторы для ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="7783e-196">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="7783e-197">**Приложение, развернутое в Azure,** может воспользоваться [управляемыми идентификаторами для ресурсов Azure,](/azure/active-directory/managed-identities-azure-resources/overview)что позволяет приложению аутентифицировать сяддение с помощью Azure Key Vault с помощью аутентификации Azure AD без учетных данных (Идентификатор приложения и пароль/клиентсекрет), хранящийся в приложении.</span><span class="sxs-lookup"><span data-stu-id="7783e-197">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="7783e-198">Приложение образец использует управляемые идентификаторы для ресурсов Azure `Managed`при установке `#define` оператора в верхней части файла *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="7783e-198">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="7783e-199">Введите имя хранилища в файл *appsettings.json* приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-199">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="7783e-200">Пример приложения не требует идентификатора приложения и пароль `Managed` (Client Secret) при установке на версию, так что вы можете игнорировать эти настройки.</span><span class="sxs-lookup"><span data-stu-id="7783e-200">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="7783e-201">Приложение развернуто в Azure, и Azure проверяет подлинность приложения для доступа к Azure Key Vault только с помощью имени хранилища, хранящегося в файле *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="7783e-201">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="7783e-202">Развертывание примера приложения в службу приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-202">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="7783e-203">Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы.</span><span class="sxs-lookup"><span data-stu-id="7783e-203">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="7783e-204">Получите идентификатор объекта из развертывания для использования в следующей команде.</span><span class="sxs-lookup"><span data-stu-id="7783e-204">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="7783e-205">Идентификатор объекта отображается на портале Azure на панели **идентификационных** данных Службы app.</span><span class="sxs-lookup"><span data-stu-id="7783e-205">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="7783e-206">Используя Azure CLI и идентификатор объекта `list` `get` приложения, предоставьте приложение и разрешения на доступ к хранилищу ключей:</span><span class="sxs-lookup"><span data-stu-id="7783e-206">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="7783e-207">**Перезапустите приложение** с помощью Azure CLI, PowerShell или портала Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-207">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="7783e-208">Пример приложения:</span><span class="sxs-lookup"><span data-stu-id="7783e-208">The sample app:</span></span>

* <span data-ttu-id="7783e-209">Создает экземпляр `AzureServiceTokenProvider` класса без строки соединения.</span><span class="sxs-lookup"><span data-stu-id="7783e-209">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="7783e-210">Когда строка соединения не предоставлена, провайдер пытается получить токен доступа из управляемых идентификаторов для ресурсов Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-210">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="7783e-211">Новый <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается `AzureServiceTokenProvider` с обратным вызовом маркера экземпляра.</span><span class="sxs-lookup"><span data-stu-id="7783e-211">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="7783e-212">Экземпляр <xref:Microsoft.Azure.KeyVault.KeyVaultClient> используется с реализацией <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> по умолчанию, которая загружает все`--`секретные значения`:`и заменяет двойные капли () с толстой кишки ( ) в ключевых имен.</span><span class="sxs-lookup"><span data-stu-id="7783e-212">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="7783e-213">Значение примера имени хранилища ключей:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="7783e-213">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="7783e-214">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7783e-214">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="7783e-215">При запуске приложения на веб-странице отображаются загруженные секретные значения.</span><span class="sxs-lookup"><span data-stu-id="7783e-215">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7783e-216">В среде разработки секретные `_dev` значения имеют суффикс, потому что они предоставляются секретами пользователя.</span><span class="sxs-lookup"><span data-stu-id="7783e-216">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="7783e-217">В рабочей среде значения загружаются `_prod` суффиксом, поскольку они предоставляются Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-217">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="7783e-218">Если вы `Access denied` получили ошибку, подтвердите, что приложение зарегистрировано в Azure AD и предоставляет доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-218">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="7783e-219">Подтвердите, что вы перезапустили службу в Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-219">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="7783e-220">Для получения информации об использовании поставщика с управляемой идентификацией и конвейером Azure DevOps [см.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="7783e-220">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="7783e-221">Варианты настройки</span><span class="sxs-lookup"><span data-stu-id="7783e-221">Configuration options</span></span>

<span data-ttu-id="7783e-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>может <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>принять:</span><span class="sxs-lookup"><span data-stu-id="7783e-222"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> can accept an <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>:</span></span>

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| <span data-ttu-id="7783e-223">Свойство</span><span class="sxs-lookup"><span data-stu-id="7783e-223">Property</span></span>         | <span data-ttu-id="7783e-224">Описание</span><span class="sxs-lookup"><span data-stu-id="7783e-224">Description</span></span> |
| ---------------- | ----------- |
| `Client`         | <span data-ttu-id="7783e-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>использовать для извлечения значений.</span><span class="sxs-lookup"><span data-stu-id="7783e-225"><xref:Microsoft.Azure.KeyVault.KeyVaultClient> to use for retrieving values.</span></span> |
| `Manager`        | <span data-ttu-id="7783e-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>экземпляр, используемый для управления секретной загрузкой.</span><span class="sxs-lookup"><span data-stu-id="7783e-226"><xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="7783e-227">`Timespan`ждать между попытками опроса хранилища ключей для изменений.</span><span class="sxs-lookup"><span data-stu-id="7783e-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="7783e-228">Значение по `null` умолчанию (конфигурация не перезагружается).</span><span class="sxs-lookup"><span data-stu-id="7783e-228">The default value is `null` (configuration isn't reloaded).</span></span> |
| `Vault`          | <span data-ttu-id="7783e-229">Хранилище ключей URI.</span><span class="sxs-lookup"><span data-stu-id="7783e-229">Key vault URI.</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="7783e-230">Используйте префикс ключевого имени</span><span class="sxs-lookup"><span data-stu-id="7783e-230">Use a key name prefix</span></span>

<span data-ttu-id="7783e-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>обеспечивает <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>перегрузку, которая принимает реализацию, которая позволяет контролировать, как секреты хранилища ключей преобразуются в клавиши конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-231"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="7783e-232">Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, предоставляемого при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-232">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="7783e-233">Это позволяет, например, загружать секреты на основе версии приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-233">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="7783e-234">Не используйте префиксы на секретах хранилища ключей, чтобы разместить секреты для нескольких приложений в одном хранилище ключей или поместить экологические секреты (например, *секреты развития* по сравнению с *производственными* секретами) в одном хранилище.</span><span class="sxs-lookup"><span data-stu-id="7783e-234">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="7783e-235">Мы рекомендуем различным приложениям и средам разработки/производства использовать отдельные хранилища ключей для изоляции сред приложений для наивысшего уровня безопасности.</span><span class="sxs-lookup"><span data-stu-id="7783e-235">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="7783e-236">В следующем примере секрет устанавливается в хранилище ключей (и с использованием `5000-AppSecret` инструмента Secret Manager для среды разработки) для (периоды не допускаются в секретных именах хранилища ключей).</span><span class="sxs-lookup"><span data-stu-id="7783e-236">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="7783e-237">Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-237">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="7783e-238">Для другой версии приложения, 5.1.0.0, секрет добавляется в хранилище ключей (и с помощью инструмента Secret Manager) для `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="7783e-238">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="7783e-239">Каждая версия приложения загружает свою `AppSecret`версию секретное значение в свою конфигурацию, как, зачистки от версии, как он загружает секрет.</span><span class="sxs-lookup"><span data-stu-id="7783e-239">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="7783e-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>называется с <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>обычаем :</span><span class="sxs-lookup"><span data-stu-id="7783e-240"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="7783e-241">Реализация <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> реагирует на префиксы версии секретов для загрузки надлежащего секрета в конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="7783e-241">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="7783e-242">`Load`загружает секрет, когда его название начинается с префикса.</span><span class="sxs-lookup"><span data-stu-id="7783e-242">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="7783e-243">Другие секреты не загружаются.</span><span class="sxs-lookup"><span data-stu-id="7783e-243">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="7783e-244">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="7783e-244">`GetKey`:</span></span>
  * <span data-ttu-id="7783e-245">Удаляет префикс из секретного имени.</span><span class="sxs-lookup"><span data-stu-id="7783e-245">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="7783e-246">Заменяет два тире в `KeyDelimiter`любом названии с , который является делимитетом, используемым в конфигурации (обычно толстой кишки).</span><span class="sxs-lookup"><span data-stu-id="7783e-246">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="7783e-247">Azure Key Vault не разрешает толстую кишку в секретных именах.</span><span class="sxs-lookup"><span data-stu-id="7783e-247">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="7783e-248">Метод `Load` вызывается алгоритмом поставщика, который итерирует секреты хранилища, чтобы найти те, которые имеют префикс версии.</span><span class="sxs-lookup"><span data-stu-id="7783e-248">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="7783e-249">При обнаружении `Load`префикса версии `GetKey` алгоритм использует метод для возврата названия конфигурации секретного имени.</span><span class="sxs-lookup"><span data-stu-id="7783e-249">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="7783e-250">Он снимает приставку версии с имени секрета и возвращает остальную часть секретного имени для загрузки в парах значения имени конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-250">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="7783e-251">Когда этот подход реализован:</span><span class="sxs-lookup"><span data-stu-id="7783e-251">When this approach is implemented:</span></span>

1. <span data-ttu-id="7783e-252">Версия приложения указана в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-252">The app's version specified in the app's project file.</span></span> <span data-ttu-id="7783e-253">В следующем примере версия приложения установлена `5.0.0.0`на:</span><span class="sxs-lookup"><span data-stu-id="7783e-253">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="7783e-254">Подтвердите, что свойство `<UserSecretsId>` присутствует в `{GUID}` файле проекта приложения, где находится ГРАФИЧЕСКИй интерфейс, поставляемый пользователем:</span><span class="sxs-lookup"><span data-stu-id="7783e-254">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="7783e-255">Сохранить следующие секреты локально с [помощью инструмента Secret Manager:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="7783e-255">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="7783e-256">Секреты сохраняются в Хранилище ключей Azure, используя следующие команды Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="7783e-256">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="7783e-257">При запуске приложения загружаются секреты хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-257">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="7783e-258">Секрет строки `5000-AppSecret` совпадает с версией приложения, указанной в`5.0.0.0`файле проекта приложения ().</span><span class="sxs-lookup"><span data-stu-id="7783e-258">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="7783e-259">Версия, `5000` (с тире), удаляется из названия ключа.</span><span class="sxs-lookup"><span data-stu-id="7783e-259">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="7783e-260">На протяжении всего приложения, `AppSecret` чтение конфигурации с ключом загружает секретное значение.</span><span class="sxs-lookup"><span data-stu-id="7783e-260">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="7783e-261">Если версия приложения изменена в файле `5.1.0.0` проекта и приложение снова запущено, `5.1.0.0_secret_value_dev` возврат секретное `5.1.0.0_secret_value_prod` значение находится в среде разработки и в производстве.</span><span class="sxs-lookup"><span data-stu-id="7783e-261">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="7783e-262">Вы также можете <xref:Microsoft.Azure.KeyVault.KeyVaultClient> предоставить <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>свою собственную реализацию.</span><span class="sxs-lookup"><span data-stu-id="7783e-262">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="7783e-263">Пользовательский клиент разрешает совместное использование одного экземпляра клиента через приложение.</span><span class="sxs-lookup"><span data-stu-id="7783e-263">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="7783e-264">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="7783e-264">Bind an array to a class</span></span>

<span data-ttu-id="7783e-265">Поставщик способен считывать значения конфигурации в массив для привязки к массиву POCO.</span><span class="sxs-lookup"><span data-stu-id="7783e-265">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="7783e-266">При чтении из источника конфигурации,`:`который позволяет ключам содержать толстую кишку () сепараторы, цифровой сегмент ключа используется для различения ключей, которые составляют массив (`:0:`, `:1:`. &hellip; `:{n}:`</span><span class="sxs-lookup"><span data-stu-id="7783e-266">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="7783e-267">Для получения дополнительной информации [см. Конфигурация: Привязать массив к классу.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="7783e-267">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="7783e-268">Ключи Убежища ключей Azure не могут использовать толстую кишку в качестве сепаратора.</span><span class="sxs-lookup"><span data-stu-id="7783e-268">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="7783e-269">В подходе, описанном в`--`этой теме, используются двойные тире () в качестве сепаратора для иерархических значений (разделов).</span><span class="sxs-lookup"><span data-stu-id="7783e-269">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="7783e-270">Ключи array хранятся в Azure Key Vault с двойными`--0--` `--1--`тире и числовыми ключевыми сегментами (, ). &hellip; `--{n}--`</span><span class="sxs-lookup"><span data-stu-id="7783e-270">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="7783e-271">Изучите следующую конфигурацию поставщика журналов [Serilog,](https://serilog.net/) представленную файлом JSON.</span><span class="sxs-lookup"><span data-stu-id="7783e-271">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="7783e-272">Есть два объекта буквально определяется `WriteTo` в массиве, которые отражают два Serilog *раковины*, которые описывают назначения для выхода регистрации:</span><span class="sxs-lookup"><span data-stu-id="7783e-272">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="7783e-273">Конфигурация, отображаемый в предыдущем файле JSON, хранится в Azure Key Vault с помощью двойного тире ()`--`нотации и числовых сегментов:</span><span class="sxs-lookup"><span data-stu-id="7783e-273">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="7783e-274">Клавиши</span><span class="sxs-lookup"><span data-stu-id="7783e-274">Key</span></span> | <span data-ttu-id="7783e-275">Значение</span><span class="sxs-lookup"><span data-stu-id="7783e-275">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="7783e-276">Перезагрузить секреты</span><span class="sxs-lookup"><span data-stu-id="7783e-276">Reload secrets</span></span>

<span data-ttu-id="7783e-277">Секреты кэшируются до тех пор, пока `IConfigurationRoot.Reload()` не будет названо.</span><span class="sxs-lookup"><span data-stu-id="7783e-277">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="7783e-278">Просроченные, отключенные и обновленные секреты в хранилище `Reload` ключей не соблюдаются приложением до тех пор, пока не будут выполнены.</span><span class="sxs-lookup"><span data-stu-id="7783e-278">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="7783e-279">Инвалиды и просроченные секреты</span><span class="sxs-lookup"><span data-stu-id="7783e-279">Disabled and expired secrets</span></span>

<span data-ttu-id="7783e-280">Инвалиды и истек <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>секреты бросить .</span><span class="sxs-lookup"><span data-stu-id="7783e-280">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="7783e-281">Чтобы предотвратить бросок приложения, предоставьте конфигурацию с помощью другого поставщика конфигурации или обновите отключенный или просроченный секрет.</span><span class="sxs-lookup"><span data-stu-id="7783e-281">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7783e-282">Диагностика</span><span class="sxs-lookup"><span data-stu-id="7783e-282">Troubleshoot</span></span>

<span data-ttu-id="7783e-283">Когда приложение не загружает конфигурацию с помощью провайдера, сообщение об ошибке записывается [в ASP.NET инфраструктуры Core Logging.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="7783e-283">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="7783e-284">Следующие условия предотвратят загрузку конфигурации:</span><span class="sxs-lookup"><span data-stu-id="7783e-284">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="7783e-285">Приложение или сертификат не настроены правильно в Active Directory Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-285">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="7783e-286">Хранилище ключей не существует в Хранилище ключей Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-286">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="7783e-287">Приложение не имеет права на доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-287">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="7783e-288">Политика доступа не включает `Get` `List` и разрешения.</span><span class="sxs-lookup"><span data-stu-id="7783e-288">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="7783e-289">В хранилище ключей данные конфигурации (пара именных значений) неправильно называются, отсутствуют, отключены или истекли.</span><span class="sxs-lookup"><span data-stu-id="7783e-289">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="7783e-290">Приложение имеет неправильное имя`KeyVaultName`хранилища ключа ( ,`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), или Azure AD сертификат отпечатков пальцев ().</span><span class="sxs-lookup"><span data-stu-id="7783e-290">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="7783e-291">Ключ конфигурации (имя) неверен в приложении для значения, которое вы пытаетесь загрузить.</span><span class="sxs-lookup"><span data-stu-id="7783e-291">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="7783e-292">При добавлении политики доступа к приложению в хранилище ключей была создана политика, но кнопка **«Сохранить»** не была выбрана в uI **политик access.**</span><span class="sxs-lookup"><span data-stu-id="7783e-292">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7783e-293">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7783e-293">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="7783e-294">Microsoft Azure: Ключевое Убежище</span><span class="sxs-lookup"><span data-stu-id="7783e-294">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="7783e-295">Microsoft Azure: Документация по ключевым хранилищам</span><span class="sxs-lookup"><span data-stu-id="7783e-295">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="7783e-296">Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="7783e-296">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="7783e-297">Класс KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="7783e-297">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="7783e-298">Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET</span><span class="sxs-lookup"><span data-stu-id="7783e-298">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="7783e-299">Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)</span><span class="sxs-lookup"><span data-stu-id="7783e-299">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7783e-300">В этом документе объясняется, как использовать поставщика конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложений из секретов Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-300">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="7783e-301">Azure Key Vault — это облачный сервис, который помогает охранять криптографические ключи и секреты, используемые приложениями и службами.</span><span class="sxs-lookup"><span data-stu-id="7783e-301">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="7783e-302">Общие сценарии использования Azure Key Vault с ASP.NET основными приложениями включают:</span><span class="sxs-lookup"><span data-stu-id="7783e-302">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="7783e-303">Контроль доступа к конфиденциальным данным конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-303">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="7783e-304">Соответствие требованию fiPS 140-2 Уровень 2 проверенных модулей аппаратной безопасности (HSM) при хранении данных конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-304">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="7783e-305">[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(как скачать)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7783e-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="7783e-306">Пакеты</span><span class="sxs-lookup"><span data-stu-id="7783e-306">Packages</span></span>

<span data-ttu-id="7783e-307">Добавьте ссылку на пакет [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)</span><span class="sxs-lookup"><span data-stu-id="7783e-307">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="7783e-308">Пример приложения</span><span class="sxs-lookup"><span data-stu-id="7783e-308">Sample app</span></span>

<span data-ttu-id="7783e-309">Пример приложения работает в любом из двух `#define` режимов, определяемых заявлением в верхней части *файла Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="7783e-309">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="7783e-310">`Certificate`&ndash; Демонстрирует использование идентификатора клиента Azure Key Vault и сертификата X.509 для доступа к секретам, хранящимся в Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-310">`Certificate` &ndash; Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="7783e-311">Эта версия образца может быть запущена из любого места, развернута в службе приложений Azure или любого узла, способного обслуживать ASP.NET приложение Core.</span><span class="sxs-lookup"><span data-stu-id="7783e-311">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="7783e-312">`Managed`&ndash; Демонстрирует, как использовать [управляемые идентификаторы для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для аутентификации приложения в Хранилище ключей Azure с аутентификацией Azure AD без учетных данных, хранящихся в коде или конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-312">`Managed` &ndash; Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="7783e-313">При использовании управляемых идентификационных данных для проверки подлинности идентификатор приложения Azure AD и пароль (Client Secret) не требуются.</span><span class="sxs-lookup"><span data-stu-id="7783e-313">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="7783e-314">Версия `Managed` образца должна быть развернута в Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-314">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="7783e-315">Следуйте инструкциям в разделе [«Использование управляемых идентификаторов» для ресурсов Azure.](#use-managed-identities-for-azure-resources)</span><span class="sxs-lookup"><span data-stu-id="7783e-315">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="7783e-316">Для получения дополнительной информации о том, как настроить`#define`образец приложения с помощью препроцессорных директив ( , см. <xref:index#preprocessor-directives-in-sample-code></span><span class="sxs-lookup"><span data-stu-id="7783e-316">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="7783e-317">Секретное хранилище в среде разработки</span><span class="sxs-lookup"><span data-stu-id="7783e-317">Secret storage in the Development environment</span></span>

<span data-ttu-id="7783e-318">Установите секреты локально с помощью [инструмента Secret Manager.](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="7783e-318">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7783e-319">Когда пример приложения работает на локальной машине в среде разработки, секреты загружаются из местного магазина Secret Manager.</span><span class="sxs-lookup"><span data-stu-id="7783e-319">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local Secret Manager store.</span></span>

<span data-ttu-id="7783e-320">Инструмент «Секретный `<UserSecretsId>` менеджер» требует свойства в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-320">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="7783e-321">Установите значение`{GUID}`свойства ( ) на любой уникальный GUID:</span><span class="sxs-lookup"><span data-stu-id="7783e-321">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="7783e-322">Секреты создаются как пары имено-ценности.</span><span class="sxs-lookup"><span data-stu-id="7783e-322">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="7783e-323">Иерархические значения (разделы конфигурации) используют a `:` (colon) в качестве сепаратора в ASP.NET ключевых имен [конфигурации Core.](xref:fundamentals/configuration/index)</span><span class="sxs-lookup"><span data-stu-id="7783e-323">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="7783e-324">Секретный менеджер используется из командной оболочки, открытой для `{SECRET NAME}` [корня содержимого](xref:fundamentals/index#content-root)проекта, где находится имя и `{SECRET VALUE}` значение:</span><span class="sxs-lookup"><span data-stu-id="7783e-324">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="7783e-325">Выполните следующие команды в командной оболочке из [корня содержимого](xref:fundamentals/index#content-root) проекта, чтобы установить секреты для примера приложения:</span><span class="sxs-lookup"><span data-stu-id="7783e-325">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="7783e-326">Когда эти секреты хранятся в Хранилище ключей Azure в [секретном хранилище в производственной среде с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault, `_dev` суффикс изменяется на `_prod`.</span><span class="sxs-lookup"><span data-stu-id="7783e-326">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="7783e-327">Суффикс обеспечивает визуальный сигнал на выходе приложения с указанием источника значений конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-327">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="7783e-328">Секретное хранилище в производственной среде с Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="7783e-328">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="7783e-329">Инструкции, предоставленные [quickstart: Установить и получить секрет из Azure Key Vault с помощью темы Azure CLI,](/azure/key-vault/quick-create-cli) суммируются здесь для создания Убежища ключей Azure и хранения секретов, используемых в примере приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-329">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="7783e-330">Для более подробной информации обратитесь к теме.</span><span class="sxs-lookup"><span data-stu-id="7783e-330">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="7783e-331">Откройте оболочку облака Azure, используя любой из следующих методов [на портале Azure:](https://portal.azure.com/)</span><span class="sxs-lookup"><span data-stu-id="7783e-331">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="7783e-332">Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом.</span><span class="sxs-lookup"><span data-stu-id="7783e-332">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="7783e-333">Используйте строку поиска "Azure CLI" в текстовом поле.</span><span class="sxs-lookup"><span data-stu-id="7783e-333">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="7783e-334">Откройте облачную оболочку в браузере с помощью кнопки **Launch Cloud Shell.**</span><span class="sxs-lookup"><span data-stu-id="7783e-334">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="7783e-335">Выберите кнопку **Cloud Shell** в меню в правом верхнем углу портала Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-335">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="7783e-336">Для получения дополнительной информации смотрите [обзор Azure CLI](/cli/azure/) и [обзор облачной оболочки Azure.](/azure/cloud-shell/overview)</span><span class="sxs-lookup"><span data-stu-id="7783e-336">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="7783e-337">Если вы еще не проверены, вопийте команду. `az login`</span><span class="sxs-lookup"><span data-stu-id="7783e-337">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="7783e-338">Создайте группу ресурсов со `{RESOURCE GROUP NAME}` следующей командой, где находится `{LOCATION}` название группы ресурсов для новой группы ресурсов и является областью Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="7783e-338">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7783e-339">Создайте хранилище ключей в группе ресурсов `{KEY VAULT NAME}` со следующей командой, `{LOCATION}` где находится название нового хранилища ключей и является областью Azure (центр обработки данных):</span><span class="sxs-lookup"><span data-stu-id="7783e-339">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="7783e-340">Создавайте секреты в хранилище ключей в виде пар именной ценности.</span><span class="sxs-lookup"><span data-stu-id="7783e-340">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="7783e-341">Секретные имена Azure Key Vault ограничены буквенно-цифровыми персонажами и тире.</span><span class="sxs-lookup"><span data-stu-id="7783e-341">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="7783e-342">Иерархические значения (разделы `--` конфигурации) используют (два тире) в качестве сепаратора.</span><span class="sxs-lookup"><span data-stu-id="7783e-342">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="7783e-343">Колоны, которые обычно используются для разграничения раздела из подключаемых ASP.NET [конфигурации Core,](xref:fundamentals/configuration/index)не допускаются в секретных именах хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-343">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="7783e-344">Таким образом, два тире используются и обмениваются на толстую кишку, когда секреты загружаются в конфигурацию приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-344">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="7783e-345">Следующие секреты для использования с образцом приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-345">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="7783e-346">Значения включают `_prod` суффикс, чтобы отличить их от `_dev` значений суффикса, загруженных в среде разработки, от пользовательских секретов.</span><span class="sxs-lookup"><span data-stu-id="7783e-346">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="7783e-347">Замените `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем этапе:</span><span class="sxs-lookup"><span data-stu-id="7783e-347">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="7783e-348">Идентификатор приложений и сертификат X.509 для приложений, не хозяек, размещенных в Azure</span><span class="sxs-lookup"><span data-stu-id="7783e-348">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="7783e-349">Напередите Azure AD, Azure Key Vault и приложение для использования идентификатора приложений Active Directory Azure и сертификата X.509 для проверки подлинности хранилища ключей **при размещении приложения за пределами Azure.**</span><span class="sxs-lookup"><span data-stu-id="7783e-349">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="7783e-350">См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).</span><span class="sxs-lookup"><span data-stu-id="7783e-350">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="7783e-351">Хотя использование идентификатора приложений и сертификата X.509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые идентификаторы для ресурсов Azure](#use-managed-identities-for-azure-resources) при хостинге приложения в Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-351">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="7783e-352">Управляемые идентификаторы не требуют хранения сертификата в приложении или в среде разработки.</span><span class="sxs-lookup"><span data-stu-id="7783e-352">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="7783e-353">В примере приложения используется идентификатор приложения `#define` и сертификат X.509, `Certificate`когда заявление в верхней части *Program.cs* файла установлено.</span><span class="sxs-lookup"><span data-stu-id="7783e-353">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="7783e-354">Создайте сертификат PKCS-12 *(.pfx)* сертификат.</span><span class="sxs-lookup"><span data-stu-id="7783e-354">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="7783e-355">Варианты создания сертификатов включают [MakeCert на Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL.](https://www.openssl.org/)</span><span class="sxs-lookup"><span data-stu-id="7783e-355">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="7783e-356">Установите сертификат в личный магазин сертификатов текущего пользователя.</span><span class="sxs-lookup"><span data-stu-id="7783e-356">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="7783e-357">Маркировка ключа как экспортируемого не является обязательной.</span><span class="sxs-lookup"><span data-stu-id="7783e-357">Marking the key as exportable is optional.</span></span> <span data-ttu-id="7783e-358">Обратите внимание на отпечаток пальца сертификата, который используется позже в этом процессе.</span><span class="sxs-lookup"><span data-stu-id="7783e-358">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="7783e-359">Экспорт архива PKCS-12 (*.pfx*) сертификат в качестве DER-кодированного сертификата (*.cer*).</span><span class="sxs-lookup"><span data-stu-id="7783e-359">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="7783e-360">Зарегистрируйте приложение с Помощью Azure AD **(регистрация приложений).**</span><span class="sxs-lookup"><span data-stu-id="7783e-360">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="7783e-361">Загрузите закодированный сертификат DER *(.cer)* в Azure AD:</span><span class="sxs-lookup"><span data-stu-id="7783e-361">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="7783e-362">Выберите приложение в Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7783e-362">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="7783e-363">Перейдите к **сертификатам & секреты**.</span><span class="sxs-lookup"><span data-stu-id="7783e-363">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="7783e-364">Выберите **сертификат загрузки** для загрузки сертификата, который содержит общедоступный ключ.</span><span class="sxs-lookup"><span data-stu-id="7783e-364">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="7783e-365">Сертификат *.cer*, *.pem*или *.crt* является приемлемым.</span><span class="sxs-lookup"><span data-stu-id="7783e-365">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="7783e-366">Храните имя хранилища ключей, идентификатор приложения и отпечаток пальца сертификата в файле *appsettings.json* приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-366">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="7783e-367">Перейдите к **хранилищам ключей** на портале Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-367">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="7783e-368">Выберите хранилище ключей, созданное в [секретном хранилище в среде Production, с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-368">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="7783e-369">Выберите **политики доступа.**</span><span class="sxs-lookup"><span data-stu-id="7783e-369">Select **Access policies**.</span></span>
1. <span data-ttu-id="7783e-370">Выберите **политику добавления доступа.**</span><span class="sxs-lookup"><span data-stu-id="7783e-370">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="7783e-371">Открытые **секретные разрешения** и предоставить приложение с **получить** и **список** разрешений.</span><span class="sxs-lookup"><span data-stu-id="7783e-371">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="7783e-372">Выберите **основной принцип** и выберите зарегистрированное приложение по имени.</span><span class="sxs-lookup"><span data-stu-id="7783e-372">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="7783e-373">Нажмите кнопку **Выбрать**.</span><span class="sxs-lookup"><span data-stu-id="7783e-373">Select the **Select** button.</span></span>
1. <span data-ttu-id="7783e-374">Щелкните **ОК**.</span><span class="sxs-lookup"><span data-stu-id="7783e-374">Select **OK**.</span></span>
1. <span data-ttu-id="7783e-375">Щелкните **Сохранить**.</span><span class="sxs-lookup"><span data-stu-id="7783e-375">Select **Save**.</span></span>
1. <span data-ttu-id="7783e-376">Разверните приложение.</span><span class="sxs-lookup"><span data-stu-id="7783e-376">Deploy the app.</span></span>

<span data-ttu-id="7783e-377">Пример `Certificate` приложения получает значения конфигурации `IConfigurationRoot` с тем же именем, что и секретное имя:</span><span class="sxs-lookup"><span data-stu-id="7783e-377">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="7783e-378">Неиерархические значения: Значение для `SecretName` получено `config["SecretName"]`с .</span><span class="sxs-lookup"><span data-stu-id="7783e-378">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="7783e-379">Иерархические значения (разделы): Используйте `:` (колонию) обозначения или метод `GetSection` расширения.</span><span class="sxs-lookup"><span data-stu-id="7783e-379">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="7783e-380">Используйте любой из этих подходов для получения значения конфигурации:</span><span class="sxs-lookup"><span data-stu-id="7783e-380">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="7783e-381">Сертификат X.509 управляется ОС.</span><span class="sxs-lookup"><span data-stu-id="7783e-381">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="7783e-382">Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> значения, поставляемые файлом *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="7783e-382">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="7783e-383">Примеры значений:</span><span class="sxs-lookup"><span data-stu-id="7783e-383">Example values:</span></span>

* <span data-ttu-id="7783e-384">Имя хранилища ключа:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="7783e-384">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="7783e-385">Идентификатор приложения:`627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="7783e-385">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="7783e-386">Отпечаток пальца сертификата:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="7783e-386">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="7783e-387">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7783e-387">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="7783e-388">При запуске приложения на веб-странице отображаются загруженные секретные значения.</span><span class="sxs-lookup"><span data-stu-id="7783e-388">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7783e-389">В среде разработки секретные `_dev` значения загружаются суффиксом.</span><span class="sxs-lookup"><span data-stu-id="7783e-389">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="7783e-390">В производственной среде значения загружаются суффиксом. `_prod`</span><span class="sxs-lookup"><span data-stu-id="7783e-390">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="7783e-391">Используйте управляемые идентификаторы для ресурсов Azure</span><span class="sxs-lookup"><span data-stu-id="7783e-391">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="7783e-392">**Приложение, развернутое в Azure,** может воспользоваться [управляемыми идентификаторами для ресурсов Azure,](/azure/active-directory/managed-identities-azure-resources/overview)что позволяет приложению аутентифицировать сяддение с помощью Azure Key Vault с помощью аутентификации Azure AD без учетных данных (Идентификатор приложения и пароль/клиентсекрет), хранящийся в приложении.</span><span class="sxs-lookup"><span data-stu-id="7783e-392">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="7783e-393">Приложение образец использует управляемые идентификаторы для ресурсов Azure `Managed`при установке `#define` оператора в верхней части файла *Program.cs.*</span><span class="sxs-lookup"><span data-stu-id="7783e-393">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="7783e-394">Введите имя хранилища в файл *appsettings.json* приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-394">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="7783e-395">Пример приложения не требует идентификатора приложения и пароль `Managed` (Client Secret) при установке на версию, так что вы можете игнорировать эти настройки.</span><span class="sxs-lookup"><span data-stu-id="7783e-395">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="7783e-396">Приложение развернуто в Azure, и Azure проверяет подлинность приложения для доступа к Azure Key Vault только с помощью имени хранилища, хранящегося в файле *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="7783e-396">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="7783e-397">Развертывание примера приложения в службу приложений Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-397">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="7783e-398">Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы.</span><span class="sxs-lookup"><span data-stu-id="7783e-398">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="7783e-399">Получите идентификатор объекта из развертывания для использования в следующей команде.</span><span class="sxs-lookup"><span data-stu-id="7783e-399">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="7783e-400">Идентификатор объекта отображается на портале Azure на панели **идентификационных** данных Службы app.</span><span class="sxs-lookup"><span data-stu-id="7783e-400">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="7783e-401">Используя Azure CLI и идентификатор объекта `list` `get` приложения, предоставьте приложение и разрешения на доступ к хранилищу ключей:</span><span class="sxs-lookup"><span data-stu-id="7783e-401">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="7783e-402">**Перезапустите приложение** с помощью Azure CLI, PowerShell или портала Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-402">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="7783e-403">Пример приложения:</span><span class="sxs-lookup"><span data-stu-id="7783e-403">The sample app:</span></span>

* <span data-ttu-id="7783e-404">Создает экземпляр `AzureServiceTokenProvider` класса без строки соединения.</span><span class="sxs-lookup"><span data-stu-id="7783e-404">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="7783e-405">Когда строка соединения не предоставлена, провайдер пытается получить токен доступа из управляемых идентификаторов для ресурсов Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-405">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="7783e-406">Новый <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается `AzureServiceTokenProvider` с обратным вызовом маркера экземпляра.</span><span class="sxs-lookup"><span data-stu-id="7783e-406">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="7783e-407">Экземпляр <xref:Microsoft.Azure.KeyVault.KeyVaultClient> используется с реализацией <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> по умолчанию, которая загружает все`--`секретные значения`:`и заменяет двойные капли () с толстой кишки ( ) в ключевых имен.</span><span class="sxs-lookup"><span data-stu-id="7783e-407">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="7783e-408">Значение примера имени хранилища ключей:`contosovault`</span><span class="sxs-lookup"><span data-stu-id="7783e-408">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="7783e-409">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7783e-409">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="7783e-410">При запуске приложения на веб-странице отображаются загруженные секретные значения.</span><span class="sxs-lookup"><span data-stu-id="7783e-410">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="7783e-411">В среде разработки секретные `_dev` значения имеют суффикс, потому что они предоставляются секретами пользователя.</span><span class="sxs-lookup"><span data-stu-id="7783e-411">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="7783e-412">В рабочей среде значения загружаются `_prod` суффиксом, поскольку они предоставляются Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="7783e-412">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="7783e-413">Если вы `Access denied` получили ошибку, подтвердите, что приложение зарегистрировано в Azure AD и предоставляет доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-413">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="7783e-414">Подтвердите, что вы перезапустили службу в Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-414">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="7783e-415">Для получения информации об использовании поставщика с управляемой идентификацией и конвейером Azure DevOps [см.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)</span><span class="sxs-lookup"><span data-stu-id="7783e-415">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="7783e-416">Используйте префикс ключевого имени</span><span class="sxs-lookup"><span data-stu-id="7783e-416">Use a key name prefix</span></span>

<span data-ttu-id="7783e-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>обеспечивает <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>перегрузку, которая принимает реализацию, которая позволяет контролировать, как секреты хранилища ключей преобразуются в клавиши конфигурации.</span><span class="sxs-lookup"><span data-stu-id="7783e-417"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="7783e-418">Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, предоставляемого при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-418">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="7783e-419">Это позволяет, например, загружать секреты на основе версии приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-419">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="7783e-420">Не используйте префиксы на секретах хранилища ключей, чтобы разместить секреты для нескольких приложений в одном хранилище ключей или поместить экологические секреты (например, *секреты развития* по сравнению с *производственными* секретами) в одном хранилище.</span><span class="sxs-lookup"><span data-stu-id="7783e-420">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="7783e-421">Мы рекомендуем различным приложениям и средам разработки/производства использовать отдельные хранилища ключей для изоляции сред приложений для наивысшего уровня безопасности.</span><span class="sxs-lookup"><span data-stu-id="7783e-421">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="7783e-422">В следующем примере секрет устанавливается в хранилище ключей (и с использованием `5000-AppSecret` инструмента Secret Manager для среды разработки) для (периоды не допускаются в секретных именах хранилища ключей).</span><span class="sxs-lookup"><span data-stu-id="7783e-422">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="7783e-423">Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-423">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="7783e-424">Для другой версии приложения, 5.1.0.0, секрет добавляется в хранилище ключей (и с помощью инструмента Secret Manager) для `5100-AppSecret`.</span><span class="sxs-lookup"><span data-stu-id="7783e-424">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="7783e-425">Каждая версия приложения загружает свою `AppSecret`версию секретное значение в свою конфигурацию, как, зачистки от версии, как он загружает секрет.</span><span class="sxs-lookup"><span data-stu-id="7783e-425">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="7783e-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>называется с <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>обычаем :</span><span class="sxs-lookup"><span data-stu-id="7783e-426"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="7783e-427">Реализация <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> реагирует на префиксы версии секретов для загрузки надлежащего секрета в конфигурацию:</span><span class="sxs-lookup"><span data-stu-id="7783e-427">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="7783e-428">`Load`загружает секрет, когда его название начинается с префикса.</span><span class="sxs-lookup"><span data-stu-id="7783e-428">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="7783e-429">Другие секреты не загружаются.</span><span class="sxs-lookup"><span data-stu-id="7783e-429">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="7783e-430">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="7783e-430">`GetKey`:</span></span>
  * <span data-ttu-id="7783e-431">Удаляет префикс из секретного имени.</span><span class="sxs-lookup"><span data-stu-id="7783e-431">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="7783e-432">Заменяет два тире в `KeyDelimiter`любом названии с , который является делимитетом, используемым в конфигурации (обычно толстой кишки).</span><span class="sxs-lookup"><span data-stu-id="7783e-432">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="7783e-433">Azure Key Vault не разрешает толстую кишку в секретных именах.</span><span class="sxs-lookup"><span data-stu-id="7783e-433">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="7783e-434">Метод `Load` вызывается алгоритмом поставщика, который итерирует секреты хранилища, чтобы найти те, которые имеют префикс версии.</span><span class="sxs-lookup"><span data-stu-id="7783e-434">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="7783e-435">При обнаружении `Load`префикса версии `GetKey` алгоритм использует метод для возврата названия конфигурации секретного имени.</span><span class="sxs-lookup"><span data-stu-id="7783e-435">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="7783e-436">Он снимает приставку версии с имени секрета и возвращает остальную часть секретного имени для загрузки в парах значения имени конфигурации приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-436">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="7783e-437">Когда этот подход реализован:</span><span class="sxs-lookup"><span data-stu-id="7783e-437">When this approach is implemented:</span></span>

1. <span data-ttu-id="7783e-438">Версия приложения указана в файле проекта приложения.</span><span class="sxs-lookup"><span data-stu-id="7783e-438">The app's version specified in the app's project file.</span></span> <span data-ttu-id="7783e-439">В следующем примере версия приложения установлена `5.0.0.0`на:</span><span class="sxs-lookup"><span data-stu-id="7783e-439">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="7783e-440">Подтвердите, что свойство `<UserSecretsId>` присутствует в `{GUID}` файле проекта приложения, где находится ГРАФИЧЕСКИй интерфейс, поставляемый пользователем:</span><span class="sxs-lookup"><span data-stu-id="7783e-440">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="7783e-441">Сохранить следующие секреты локально с [помощью инструмента Secret Manager:](xref:security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="7783e-441">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="7783e-442">Секреты сохраняются в Хранилище ключей Azure, используя следующие команды Azure CLI:</span><span class="sxs-lookup"><span data-stu-id="7783e-442">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="7783e-443">При запуске приложения загружаются секреты хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-443">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="7783e-444">Секрет строки `5000-AppSecret` совпадает с версией приложения, указанной в`5.0.0.0`файле проекта приложения ().</span><span class="sxs-lookup"><span data-stu-id="7783e-444">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="7783e-445">Версия, `5000` (с тире), удаляется из названия ключа.</span><span class="sxs-lookup"><span data-stu-id="7783e-445">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="7783e-446">На протяжении всего приложения, `AppSecret` чтение конфигурации с ключом загружает секретное значение.</span><span class="sxs-lookup"><span data-stu-id="7783e-446">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="7783e-447">Если версия приложения изменена в файле `5.1.0.0` проекта и приложение снова запущено, `5.1.0.0_secret_value_dev` возврат секретное `5.1.0.0_secret_value_prod` значение находится в среде разработки и в производстве.</span><span class="sxs-lookup"><span data-stu-id="7783e-447">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="7783e-448">Вы также можете <xref:Microsoft.Azure.KeyVault.KeyVaultClient> предоставить <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>свою собственную реализацию.</span><span class="sxs-lookup"><span data-stu-id="7783e-448">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="7783e-449">Пользовательский клиент разрешает совместное использование одного экземпляра клиента через приложение.</span><span class="sxs-lookup"><span data-stu-id="7783e-449">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="7783e-450">Привязка массива к классу</span><span class="sxs-lookup"><span data-stu-id="7783e-450">Bind an array to a class</span></span>

<span data-ttu-id="7783e-451">Поставщик способен считывать значения конфигурации в массив для привязки к массиву POCO.</span><span class="sxs-lookup"><span data-stu-id="7783e-451">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="7783e-452">При чтении из источника конфигурации,`:`который позволяет ключам содержать толстую кишку () сепараторы, цифровой сегмент ключа используется для различения ключей, которые составляют массив (`:0:`, `:1:`. &hellip; `:{n}:`</span><span class="sxs-lookup"><span data-stu-id="7783e-452">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="7783e-453">Для получения дополнительной информации [см. Конфигурация: Привязать массив к классу.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)</span><span class="sxs-lookup"><span data-stu-id="7783e-453">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="7783e-454">Ключи Убежища ключей Azure не могут использовать толстую кишку в качестве сепаратора.</span><span class="sxs-lookup"><span data-stu-id="7783e-454">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="7783e-455">В подходе, описанном в`--`этой теме, используются двойные тире () в качестве сепаратора для иерархических значений (разделов).</span><span class="sxs-lookup"><span data-stu-id="7783e-455">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="7783e-456">Ключи array хранятся в Azure Key Vault с двойными`--0--` `--1--`тире и числовыми ключевыми сегментами (, ). &hellip; `--{n}--`</span><span class="sxs-lookup"><span data-stu-id="7783e-456">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="7783e-457">Изучите следующую конфигурацию поставщика журналов [Serilog,](https://serilog.net/) представленную файлом JSON.</span><span class="sxs-lookup"><span data-stu-id="7783e-457">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="7783e-458">Есть два объекта буквально определяется `WriteTo` в массиве, которые отражают два Serilog *раковины*, которые описывают назначения для выхода регистрации:</span><span class="sxs-lookup"><span data-stu-id="7783e-458">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

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

<span data-ttu-id="7783e-459">Конфигурация, отображаемый в предыдущем файле JSON, хранится в Azure Key Vault с помощью двойного тире ()`--`нотации и числовых сегментов:</span><span class="sxs-lookup"><span data-stu-id="7783e-459">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="7783e-460">Клавиши</span><span class="sxs-lookup"><span data-stu-id="7783e-460">Key</span></span> | <span data-ttu-id="7783e-461">Значение</span><span class="sxs-lookup"><span data-stu-id="7783e-461">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="7783e-462">Перезагрузить секреты</span><span class="sxs-lookup"><span data-stu-id="7783e-462">Reload secrets</span></span>

<span data-ttu-id="7783e-463">Секреты кэшируются до тех пор, пока `IConfigurationRoot.Reload()` не будет названо.</span><span class="sxs-lookup"><span data-stu-id="7783e-463">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="7783e-464">Просроченные, отключенные и обновленные секреты в хранилище `Reload` ключей не соблюдаются приложением до тех пор, пока не будут выполнены.</span><span class="sxs-lookup"><span data-stu-id="7783e-464">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="7783e-465">Инвалиды и просроченные секреты</span><span class="sxs-lookup"><span data-stu-id="7783e-465">Disabled and expired secrets</span></span>

<span data-ttu-id="7783e-466">Инвалиды и истек <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>секреты бросить .</span><span class="sxs-lookup"><span data-stu-id="7783e-466">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="7783e-467">Чтобы предотвратить бросок приложения, предоставьте конфигурацию с помощью другого поставщика конфигурации или обновите отключенный или просроченный секрет.</span><span class="sxs-lookup"><span data-stu-id="7783e-467">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7783e-468">Диагностика</span><span class="sxs-lookup"><span data-stu-id="7783e-468">Troubleshoot</span></span>

<span data-ttu-id="7783e-469">Когда приложение не загружает конфигурацию с помощью провайдера, сообщение об ошибке записывается [в ASP.NET инфраструктуры Core Logging.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="7783e-469">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="7783e-470">Следующие условия предотвратят загрузку конфигурации:</span><span class="sxs-lookup"><span data-stu-id="7783e-470">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="7783e-471">Приложение или сертификат не настроены правильно в Active Directory Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-471">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="7783e-472">Хранилище ключей не существует в Хранилище ключей Azure.</span><span class="sxs-lookup"><span data-stu-id="7783e-472">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="7783e-473">Приложение не имеет права на доступ к хранилищу ключей.</span><span class="sxs-lookup"><span data-stu-id="7783e-473">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="7783e-474">Политика доступа не включает `Get` `List` и разрешения.</span><span class="sxs-lookup"><span data-stu-id="7783e-474">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="7783e-475">В хранилище ключей данные конфигурации (пара именных значений) неправильно называются, отсутствуют, отключены или истекли.</span><span class="sxs-lookup"><span data-stu-id="7783e-475">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="7783e-476">Приложение имеет неправильное имя`KeyVaultName`хранилища ключа ( ,`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), или Azure AD сертификат отпечатков пальцев ().</span><span class="sxs-lookup"><span data-stu-id="7783e-476">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="7783e-477">Ключ конфигурации (имя) неверен в приложении для значения, которое вы пытаетесь загрузить.</span><span class="sxs-lookup"><span data-stu-id="7783e-477">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="7783e-478">При добавлении политики доступа к приложению в хранилище ключей была создана политика, но кнопка **«Сохранить»** не была выбрана в uI **политик access.**</span><span class="sxs-lookup"><span data-stu-id="7783e-478">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7783e-479">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="7783e-479">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="7783e-480">Microsoft Azure: Ключевое Убежище</span><span class="sxs-lookup"><span data-stu-id="7783e-480">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="7783e-481">Microsoft Azure: Документация по ключевым хранилищам</span><span class="sxs-lookup"><span data-stu-id="7783e-481">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="7783e-482">Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure</span><span class="sxs-lookup"><span data-stu-id="7783e-482">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="7783e-483">Класс KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="7783e-483">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="7783e-484">Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET</span><span class="sxs-lookup"><span data-stu-id="7783e-484">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="7783e-485">Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)</span><span class="sxs-lookup"><span data-stu-id="7783e-485">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

