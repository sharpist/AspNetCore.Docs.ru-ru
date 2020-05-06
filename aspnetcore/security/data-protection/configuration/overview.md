---
title: Настройка защиты данных в ASP.NET Core
author: rick-anderson
description: Узнайте, как настроить защиту данных в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/overview
ms.openlocfilehash: d37fdc44bb9a603d85818fc72a7a07de67006366
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776816"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="3b3e4-103">Настройка защиты данных в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3b3e4-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="3b3e4-104">При инициализации системы защиты данных применяются [параметры по умолчанию](xref:security/data-protection/configuration/default-settings) , основанные на рабочей среде.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="3b3e4-105">Эти параметры обычно подходят для приложений, выполняющихся на одном компьютере.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="3b3e4-106">Существуют случаи, когда разработчику может потребоваться изменить параметры по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="3b3e4-107">Приложение распределено между несколькими компьютерами.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="3b3e4-108">По соображениям соответствия.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-108">For compliance reasons.</span></span>

<span data-ttu-id="3b3e4-109">В этих сценариях система защиты данных предоставляет богатый API настройки.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="3b3e4-110">Как и файлы конфигурации, кольцо ключа защиты данных следует защищать с помощью соответствующих разрешений.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="3b3e4-111">Вы можете выбрать шифрование неактивных ключей, но это не помешает злоумышленникам создавать новые ключи.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="3b3e4-112">Таким образом, безопасность приложения затронута.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="3b3e4-113">Доступ к расположению хранилища, настроенному с помощью защиты данных, должен иметь только само приложение, аналогично тому, как вы защищаете файлы конфигурации.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="3b3e4-114">Например, если вы решили сохранить ключ на диске, используйте разрешения файловой системы.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="3b3e4-115">Убедитесь, что только удостоверение, под которым выполняется веб-приложение, имеет доступ на чтение, запись и создание этого каталога.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="3b3e4-116">При использовании хранилища больших двоичных объектов Azure только веб-приложение должно иметь возможность чтения, записи или создания новых записей в хранилище больших двоичных объектов и т. д.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="3b3e4-117">Метод расширения [адддатапротектион](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) возвращает [идатапротектионбуилдер](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="3b3e4-118">`IDataProtectionBuilder`предоставляет методы расширения, которые можно объединить в цепочку для настройки параметров защиты данных.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3b3e4-119">Для расширений защиты данных, используемых в этой статье, требуются следующие пакеты NuGet:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="3b3e4-120">Microsoft. AspNetCore. в отношении защиты. AzureStorage</span><span class="sxs-lookup"><span data-stu-id="3b3e4-120">Microsoft.AspNetCore.DataProtection.AzureStorage</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/)
* [<span data-ttu-id="3b3e4-121">Microsoft. AspNetCore. в отношении защиты. AzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="3b3e4-121">Microsoft.AspNetCore.DataProtection.AzureKeyVault</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault/)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="3b3e4-122">протекткэйсвисазурекэйваулт</span><span class="sxs-lookup"><span data-stu-id="3b3e4-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="3b3e4-123">Чтобы сохранить ключи в [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), настройте систему с помощью [протекткэйсвисазурекэйваулт](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) в `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-123">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="3b3e4-124">Задайте место хранения ключевых звонков (например, [персисткэйстоазуреблобстораже](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-124">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="3b3e4-125">Расположение должно быть задано, так `ProtectKeysWithAzureKeyVault` как вызов реализует [иксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , который отключает автоматические параметры защиты данных, включая место хранения ключевых звонков.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-125">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="3b3e4-126">В предыдущем примере для сохранения ключа используется хранилище BLOB-объектов Azure.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-126">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="3b3e4-127">Дополнительные сведения см. в статье [поставщики хранилища ключей: служба хранилища Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-127">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="3b3e4-128">Вы также можете сохранить ключ в локальной системе с помощью [персисткэйстофилесистем](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-128">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="3b3e4-129">`keyIdentifier` — Это идентификатор ключа хранилища ключей, используемый для шифрования ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-129">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="3b3e4-130">Например, ключ, созданный в хранилище ключей с именем `dataprotection` , `contosokeyvault` имеет идентификатор `https://contosokeyvault.vault.azure.net/keys/dataprotection/`ключа.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-130">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="3b3e4-131">Укажите приложение с **ключом** **распаковки** и разрешениями на ключ для хранилища ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-131">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="3b3e4-132">`ProtectKeysWithAzureKeyVault`перегрузки</span><span class="sxs-lookup"><span data-stu-id="3b3e4-132">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="3b3e4-133">[Протекткэйсвисазурекэйваулт (идатапротектионбуилдер, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) позволяет использовать [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) для обеспечения возможности использования хранилища ключей системой защиты данных.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-133">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="3b3e4-134">[Протекткэйсвисазурекэйваулт (идатапротектионбуилдер, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) позволяет использовать протокол `ClientId` и [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) , чтобы система защиты данных использовала хранилище ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-134">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="3b3e4-135">[Протекткэйсвисазурекэйваулт (идатапротектионбуилдер, строка, строка, строка)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) позволяет использовать `ClientId` и `ClientSecret` , чтобы система защиты данных использовала хранилище ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-135">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="3b3e4-136">персисткэйстофилесистем</span><span class="sxs-lookup"><span data-stu-id="3b3e4-136">PersistKeysToFileSystem</span></span>

<span data-ttu-id="3b3e4-137">Чтобы хранить ключи в общем UNC-ресурсе, а не в расположении *% LocalAppData%* по умолчанию, настройте систему с помощью [персисткэйстофилесистем](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="3b3e4-137">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="3b3e4-138">Если изменить расположение сохраняемости ключей, система больше не шифрует неактивных ключей автоматически, так как не знает, является ли DPAPI подходящим механизмом шифрования.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-138">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="3b3e4-139">протекткэйсвис\*</span><span class="sxs-lookup"><span data-stu-id="3b3e4-139">ProtectKeysWith\*</span></span>

<span data-ttu-id="3b3e4-140">Вы можете настроить систему для защиты неактивных ключей, вызвав любой из API [конфигурации\* протекткэйсвис](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-140">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="3b3e4-141">Рассмотрим приведенный ниже пример, в котором ключи хранятся в общем UNC-ресурсе и шифруются с помощью определенного сертификата X. 509:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-141">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3b3e4-142">В ASP.NET Core 2,1 или более поздней версии можно предоставить [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) [протекткэйсвисцертификате](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), например сертификат, загруженный из файла:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-142">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="3b3e4-143">Дополнительные примеры и обсуждение встроенных механизмов шифрования ключей см. в разделе [неактивных ключей шифрования](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-143">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="3b3e4-144">унпротекткэйсвисаницертификате</span><span class="sxs-lookup"><span data-stu-id="3b3e4-144">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="3b3e4-145">В ASP.NET Core 2,1 или более поздней версии можно поворачивать сертификаты и расшифровывать ключи в неактивном виде, используя массив сертификатов [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) с [унпротекткэйсвисаницертификате](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="3b3e4-145">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="3b3e4-146">сетдефаулткэйлифетиме</span><span class="sxs-lookup"><span data-stu-id="3b3e4-146">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="3b3e4-147">Чтобы настроить систему на использование времени жизни ключа, равного 14 дням, вместо 90 дней по умолчанию, используйте [сетдефаулткэйлифетиме](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="3b3e4-147">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="3b3e4-148">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="3b3e4-148">SetApplicationName</span></span>

<span data-ttu-id="3b3e4-149">По умолчанию система защиты данных изолирует приложения друг от друга на основе их [корневых путей к содержимому](xref:fundamentals/index#content-root) , даже если они совместно используют один и тот же физический репозиторий ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-149">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="3b3e4-150">Это предотвращает понимание приложениями защищенных полезных данных друг друга.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-150">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="3b3e4-151">Для совместного использования защищенных полезных данных между приложениями:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-151">To share protected payloads among apps:</span></span>

* <span data-ttu-id="3b3e4-152">Настройте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> в каждом приложении одно и то же значение.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-152">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="3b3e4-153">Используйте одну и ту же версию стека API для защиты данных в приложениях.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-153">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="3b3e4-154">Выполните **одно** из следующих действий в файлах проекта приложений:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-154">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="3b3e4-155">Сослаться на ту же версию общей платформы через [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-155">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="3b3e4-156">Сослаться на ту же версию [пакета защиты данных](xref:security/data-protection/introduction#package-layout) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-156">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="3b3e4-157">дисаблеаутоматиккэйженератион</span><span class="sxs-lookup"><span data-stu-id="3b3e4-157">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="3b3e4-158">У вас может быть сценарий, в котором приложение не должно автоматически выполнять откат ключей (создание новых ключей) по мере их истечения.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-158">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="3b3e4-159">Одним из примеров могут быть приложения, настроенные в связи «первичная/вторичная», в которой только основное приложение отвечает на вопросы управления ключами, а дополнительные приложения просто имеют доступное только для чтения представление круга ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-159">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="3b3e4-160">Вторичные приложения можно настроить так, чтобы они обрабатывали ключевое кольцо как доступное только для <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>чтения, настроив систему с помощью:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-160">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="3b3e4-161">Изоляция отдельных приложений</span><span class="sxs-lookup"><span data-stu-id="3b3e4-161">Per-application isolation</span></span>

<span data-ttu-id="3b3e4-162">Когда система защиты данных предоставляется узлом ASP.NET Core, она автоматически изолирует приложения друг от друга, даже если эти приложения выполняются в той же учетной записи рабочего процесса и используют один и тот же материал основного ключа.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-162">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="3b3e4-163">Это в некоторой степени аналогично модификатору IsolateApps `<machineKey>` элемента System. Web.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-163">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="3b3e4-164">Механизм изоляции работает путем рассмотрения каждого приложения на локальном компьютере в качестве уникального клиента, поэтому <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> корневой каталог для любого конкретного приложения автоматически включает идентификатор приложения в качестве дискриминатора.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-164">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="3b3e4-165">Уникальный идентификатор приложения — это физический путь приложения:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-165">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="3b3e4-166">Для приложений, размещенных в службах IIS, уникальный идентификатор — это физический путь IIS приложения.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-166">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="3b3e4-167">Если приложение развернуто в среде веб-фермы, это значение является стабильным при условии, что среды IIS настроены одинаково на всех компьютерах в веб-ферме.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-167">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="3b3e4-168">Для автономных приложений, работающих на [сервере Kestrel](xref:fundamentals/servers/index#kestrel), уникальный идентификатор — это физический путь к приложению на диске.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-168">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="3b3e4-169">Уникальный идентификатор разрабатывается для того,&mdash;чтобы сбросить оба отдельных приложения и самого компьютера.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-169">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="3b3e4-170">Этот механизм изоляции предполагает, что приложения не являются вредоносными.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-170">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="3b3e4-171">Вредоносное приложение всегда может повлиять на любое другое приложение, выполняемое в той же учетной записи рабочего процесса.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-171">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="3b3e4-172">В общей среде размещения, в которой приложения взаимно не считаются доверенными, поставщик услуг размещения должен принять меры по обеспечению изоляции на уровне ОС между приложениями, включая разделение базовых хранилищ ключей приложений.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-172">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="3b3e4-173">Если система защиты данных не предоставляется узлом ASP.NET Core (например, при создании экземпляра с помощью `DataProtectionProvider` конкретного типа), по умолчанию отключена изоляция приложений.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-173">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="3b3e4-174">Если изоляция приложений отключена, все приложения, которые поддерживаются одним и тем же материалом для ключа, могут совместно использовать полезные данные, если они предоставляют соответствующие [цели](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-174">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="3b3e4-175">Чтобы обеспечить изоляцию приложений в этой среде, вызовите метод [SetApplicationName](#setapplicationname) объекта конфигурации и укажите уникальное имя для каждого приложения.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-175">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="3b3e4-176">Изменение алгоритмов с помощью Усекриптографикалгорисмс</span><span class="sxs-lookup"><span data-stu-id="3b3e4-176">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="3b3e4-177">Стек защиты данных позволяет изменить алгоритм по умолчанию, используемый созданными новыми ключами.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-177">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="3b3e4-178">Самый простой способ сделать это — вызвать [усекриптографикалгорисмс](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) из обратного вызова конфигурации:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-178">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="3b3e4-179">По умолчанию EncryptionAlgorithm имеет значение AES-256-CBC, а Валидатионалгорисм по умолчанию — HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-179">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="3b3e4-180">Политика по умолчанию может быть задана системным администратором с помощью [политики на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy), но явный вызов `UseCryptographicAlgorithms` переопределяет политику по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-180">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="3b3e4-181">Вызов `UseCryptographicAlgorithms` позволяет указать нужный алгоритм из предопределенного встроенного списка.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-181">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="3b3e4-182">Не нужно беспокоиться о реализации алгоритма.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-182">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="3b3e4-183">В приведенном выше сценарии система защиты данных пытается использовать реализацию CNG AES при запуске в Windows.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-183">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="3b3e4-184">В противном случае он возвращается к управляемому классу [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-184">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="3b3e4-185">Реализацию можно указать вручную с помощью вызова [усекустомкриптографикалгорисмс](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-185">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="3b3e4-186">Изменение алгоритмов не влияет на существующие ключи в кольце ключа.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-186">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="3b3e4-187">Он влияет только на вновь созданные ключи.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-187">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="3b3e4-188">Указание пользовательских управляемых алгоритмов</span><span class="sxs-lookup"><span data-stu-id="3b3e4-188">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3b3e4-189">Чтобы указать пользовательские управляемые алгоритмы, создайте экземпляр [манажедаусентикатеденкрипторконфигуратион](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) , указывающий на типы реализации:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-189">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3b3e4-190">Чтобы указать пользовательские управляемые алгоритмы, создайте экземпляр [манажедаусентикатеденкриптионсеттингс](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) , указывающий на типы реализации:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-190">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="3b3e4-191">Как правило \*, свойства типа должны указывать на конкретные, допускающие создание экземпляров (с помощью общедоступного конструктора без параметров) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) и [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), хотя в специальных возможностях `typeof(Aes)` системы некоторые значения, например для удобства.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-191">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="3b3e4-192">SymmetricAlgorithm должен иметь длину ключа ≥ 128 бит и размер блока ≥ 64 бит, и он должен поддерживать шифрование в режиме CBC с заполнением PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-192">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="3b3e4-193">KeyedHashAlgorithm должен иметь размер дайджеста >= 128 бит и должен поддерживать ключи длиной, равную длине дайджеста хэш-алгоритма.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-193">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="3b3e4-194">KeyedHashAlgorithm строго не обязательно должен быть HMAC.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-194">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="3b3e4-195">Указание пользовательских алгоритмов CNG Windows</span><span class="sxs-lookup"><span data-stu-id="3b3e4-195">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3b3e4-196">Чтобы указать пользовательский алгоритм CNG Windows с помощью шифрования в режиме CBC с проверкой HMAC, создайте экземпляр [кнгкбкаусентикатеденкрипторконфигуратион](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) , содержащий алгоритмную информацию:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-196">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3b3e4-197">Чтобы указать пользовательский алгоритм CNG Windows с помощью шифрования в режиме CBC с проверкой HMAC, создайте экземпляр [кнгкбкаусентикатеденкриптионсеттингс](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) , содержащий алгоритмную информацию:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-197">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3b3e4-198">Алгоритм блочного блочного шифра должен иметь длину ключа >= 128 бит, размер блока >= 64 бит и должен поддерживать шифрование в режиме CBC с использованием дополнений PKCS #7.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-198">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="3b3e4-199">Хэш-алгоритм должен иметь размер дайджеста >= 128 бит и должен поддерживать открытие с флагом флага\_HMAC\_\_в\_Handles.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-199">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="3b3e4-200">Для \*свойств поставщика можно задать значение null, чтобы использовать поставщик по умолчанию для указанного алгоритма.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-200">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="3b3e4-201">Дополнительные сведения см. в документации по [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-201">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="3b3e4-202">Чтобы указать пользовательский алгоритм CNG Windows, используя Галоис/шифрование в режиме счетчика с проверкой, создайте экземпляр [кнггкмаусентикатеденкрипторконфигуратион](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) , содержащий алгоритмную информацию:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-202">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="3b3e4-203">Чтобы указать пользовательский алгоритм CNG Windows, используя Галоис/шифрование в режиме счетчика с проверкой, создайте экземпляр [кнггкмаусентикатеденкриптионсеттингс](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) , содержащий алгоритмную информацию:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-203">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3b3e4-204">Алгоритм симметричного блочного шифра должен иметь длину ключа >= 128 бит, размер блока ровно 128 бит и должен поддерживать шифрование GCM.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-204">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="3b3e4-205">Чтобы использовать поставщик по умолчанию для указанного алгоритма, можно задать для свойства [енкриптионалгорисмпровидер](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) значение null.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-205">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="3b3e4-206">Дополнительные сведения см. в документации по [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-206">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="3b3e4-207">Указание других пользовательских алгоритмов</span><span class="sxs-lookup"><span data-stu-id="3b3e4-207">Specifying other custom algorithms</span></span>

<span data-ttu-id="3b3e4-208">Несмотря на то, что не предоставляется в качестве API первого класса, система защиты данных достаточно расширяема, чтобы можно было указать практически любой тип алгоритма.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-208">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="3b3e4-209">Например, можно хранить все ключи, содержащиеся в аппаратном модуле безопасности (HSM), и обеспечивать пользовательскую реализацию подпрограмм шифрования и расшифровки.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-209">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="3b3e4-210">Дополнительные сведения см. в статье [иаусентикатеденкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) в разделе [расширение криптографии Core](xref:security/data-protection/extensibility/core-crypto) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-210">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="3b3e4-211">Сохранение ключей при размещении в контейнере DOCKER</span><span class="sxs-lookup"><span data-stu-id="3b3e4-211">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="3b3e4-212">При размещении в контейнере [DOCKER](/dotnet/standard/microservices-architecture/container-docker-introduction/) ключи должны поддерживаться в одном из следующих:</span><span class="sxs-lookup"><span data-stu-id="3b3e4-212">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="3b3e4-213">Папка, которая является томом DOCKER, который сохраняется за пределами времени существования контейнера, например общего тома или тома, подключенного к узлу.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-213">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="3b3e4-214">Внешний поставщик, например [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) или [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-214">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="3b3e4-215">Сохранение ключей с помощью Redis</span><span class="sxs-lookup"><span data-stu-id="3b3e4-215">Persisting keys with Redis</span></span>

<span data-ttu-id="3b3e4-216">Для хранения ключей следует использовать только версии Redis, поддерживающие [сохраняемость данных Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) .</span><span class="sxs-lookup"><span data-stu-id="3b3e4-216">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="3b3e4-217">[Хранилище BLOB-объектов Azure](/azure/storage/blobs/storage-blobs-introduction) является постоянным и может использоваться для хранения ключей.</span><span class="sxs-lookup"><span data-stu-id="3b3e4-217">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="3b3e4-218">Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore/issues/13476).</span><span class="sxs-lookup"><span data-stu-id="3b3e4-218">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3b3e4-219">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="3b3e4-219">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
