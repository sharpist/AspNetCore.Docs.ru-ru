---
title: Настройка защиты данных в ASP.NET Core
author: rick-anderson
description: Узнайте, как настроить защиту данных в ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 09/04/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 620aab1e47caf7539b2c0e1deca060c7eafa786f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052751"
---
# <a name="configure-aspnet-core-data-protection"></a>Настройка защиты данных в ASP.NET Core

При инициализации системы защиты данных применяются [параметры по умолчанию](xref:security/data-protection/configuration/default-settings) , основанные на рабочей среде. Эти параметры обычно подходят для приложений, выполняющихся на одном компьютере. Существуют случаи, когда разработчику может потребоваться изменить параметры по умолчанию.

* Приложение распределено между несколькими компьютерами.
* По соображениям соответствия.

В этих сценариях система защиты данных предоставляет богатый API настройки.

> [!WARNING]
> Как и файлы конфигурации, кольцо ключа защиты данных следует защищать с помощью соответствующих разрешений. Вы можете выбрать шифрование неактивных ключей, но это не помешает злоумышленникам создавать новые ключи. Таким образом, безопасность приложения затронута. Доступ к расположению хранилища, настроенному с помощью защиты данных, должен иметь только само приложение, аналогично тому, как вы защищаете файлы конфигурации. Например, если вы решили сохранить ключ на диске, используйте разрешения файловой системы. Убедитесь, что только удостоверение, под которым выполняется веб-приложение, имеет доступ на чтение, запись и создание этого каталога. При использовании хранилища больших двоичных объектов Azure только веб-приложение должно иметь возможность чтения, записи или создания новых записей в хранилище больших двоичных объектов и т. д.
>
> Метод расширения [адддатапротектион](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) возвращает [идатапротектионбуилдер](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder` предоставляет методы расширения, которые можно объединить в цепочку для настройки параметров защиты данных.

::: moniker range=">= aspnetcore-3.0"

Для расширений защиты данных, используемых в этой статье, требуются следующие пакеты NuGet:

* [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>протекткэйсвисазурекэйваулт

Чтобы сохранить ключи в [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), настройте систему с помощью [протекткэйсвисазурекэйваулт](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) в `Startup` классе. `blobUriWithSasToken` полный URI, где должен храниться файл ключа. URI должен содержать маркер SAS в качестве параметра строки запроса:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Задайте место хранения ключевых звонков (например, [персисткэйстоазуреблобстораже](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Расположение должно быть задано, так как вызов `ProtectKeysWithAzureKeyVault` реализует [иксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , который отключает автоматические параметры защиты данных, включая место хранения ключевых звонков. В предыдущем примере для сохранения ключа используется хранилище BLOB-объектов Azure. Дополнительные сведения см. в статье [поставщики хранилища ключей: служба хранилища Azure](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Вы также можете сохранить ключ в локальной системе с помощью [персисткэйстофилесистем](xref:security/data-protection/implementation/key-storage-providers#file-system).

`keyIdentifier`— Это идентификатор ключа хранилища ключей, используемый для шифрования ключей. Например, ключ, созданный в хранилище ключей с именем `dataprotection` , `contosokeyvault` имеет идентификатор ключа `https://contosokeyvault.vault.azure.net/keys/dataprotection/` . Укажите приложение с **ключом** **распаковки** и разрешениями на ключ для хранилища ключей.

`ProtectKeysWithAzureKeyVault` перегрузки

* [Протекткэйсвисазурекэйваулт (идатапротектионбуилдер, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) позволяет использовать [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) для обеспечения возможности использования хранилища ключей системой защиты данных.
* [Протекткэйсвисазурекэйваулт (идатапротектионбуилдер, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) позволяет использовать протокол `ClientId` и [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) , чтобы система защиты данных использовала хранилище ключей.
* [Протекткэйсвисазурекэйваулт (идатапротектионбуилдер, строка, строка, строка)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) позволяет использовать `ClientId` и, `ClientSecret` чтобы система защиты данных использовала хранилище ключей.

Если приложение использует предыдущие пакеты Azure ( [`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) и [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault) ) и сочетание Azure Key Vault и хранилища Azure для хранения и защиты ключей, создается <xref:System.UriFormatException?displayProperty=nameWithType> исключение, если BLOB-объект для хранилища ключей не существует. Большой двоичный объект может быть создан вручную перед запуском приложения в портал Azure или с помощью следующей процедуры:

1. Удалите вызов для `ProtectKeysWithAzureKeyVault` первого запуска, чтобы создать BLOB-объект на месте.
1. Добавьте вызов для `ProtectKeysWithAzureKeyVault` последующих запусков.

`ProtectKeysWithAzureKeyVault`Рекомендуется удалить для первого запуска, так как он обеспечивает создание файла с правильной схемой и значениями на месте. 

Мы рекомендуем обновить пакеты [Azure. Extensions. AspNetCore. данные защиты. BLOB](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) и [Azure. Extensions. AspNetCore. данные защиты. Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) , так как указанный API автоматически создает большой двоичный объект, если он не существует.

```csharp
var storageAccount = CloudStorageAccount.Parse("<storage account connection string">);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("<key store container name>");

var azureServiceTokenProvider = new AzureServiceTokenProvider();
var keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(
        azureServiceTokenProvider.KeyVaultTokenCallback));

services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage(container, "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(keyVaultClient, "<keyIdentifier>");
```

::: moniker-end

## <a name="persistkeystofilesystem"></a>персисткэйстофилесистем

Чтобы хранить ключи в общем UNC-ресурсе, а не в расположении *% LocalAppData%* по умолчанию, настройте систему с помощью [персисткэйстофилесистем](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Если изменить расположение сохраняемости ключей, система больше не шифрует неактивных ключей автоматически, так как не знает, является ли DPAPI подходящим механизмом шифрования.

## <a name="protectkeyswith"></a>протекткэйсвис\*

Вы можете настроить систему для защиты неактивных ключей, вызвав любой из API [конфигурации \* протекткэйсвис](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) . Рассмотрим приведенный ниже пример, в котором ключи хранятся в общем UNC-ресурсе и шифруются с помощью определенного сертификата X. 509:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

В ASP.NET Core 2,1 или более поздней версии можно предоставить [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) [протекткэйсвисцертификате](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), например сертификат, загруженный из файла:

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

Дополнительные примеры и обсуждение встроенных механизмов шифрования ключей см. в разделе [неактивных ключей шифрования](xref:security/data-protection/implementation/key-encryption-at-rest) .

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>унпротекткэйсвисаницертификате

В ASP.NET Core 2,1 или более поздней версии можно поворачивать сертификаты и расшифровывать ключи в неактивном виде, используя массив сертификатов [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) с [унпротекткэйсвисаницертификате](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

## <a name="setdefaultkeylifetime"></a>сетдефаулткэйлифетиме

Чтобы настроить систему на использование времени жизни ключа, равного 14 дням, вместо 90 дней по умолчанию, используйте [сетдефаулткэйлифетиме](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

По умолчанию система защиты данных изолирует приложения друг от друга на основе их [корневых путей к содержимому](xref:fundamentals/index#content-root) , даже если они совместно используют один и тот же физический репозиторий ключей. Это предотвращает понимание приложениями защищенных полезных данных друг друга.

Для совместного использования защищенных полезных данных между приложениями:

* Настройте <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> в каждом приложении одно и то же значение.
* Используйте одну и ту же версию стека API для защиты данных в приложениях. Выполните **одно** из следующих действий в файлах проекта приложений:
  * Сослаться на ту же версию общей платформы через [метапакет Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).
  * Сослаться на ту же версию [пакета защиты данных](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>дисаблеаутоматиккэйженератион

У вас может быть сценарий, в котором приложение не должно автоматически выполнять откат ключей (создание новых ключей) по мере их истечения. Одним из примеров могут быть приложения, настроенные в связи «первичная/вторичная», в которой только основное приложение отвечает на вопросы управления ключами, а дополнительные приложения просто имеют доступное только для чтения представление круга ключей. Вторичные приложения можно настроить так, чтобы они обрабатывали ключевое кольцо как доступное только для чтения, настроив систему с помощью <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Изоляция отдельных приложений

Когда система защиты данных предоставляется узлом ASP.NET Core, она автоматически изолирует приложения друг от друга, даже если эти приложения выполняются в той же учетной записи рабочего процесса и используют один и тот же материал основного ключа. Это в некоторой степени аналогично модификатору IsolateApps элемента System. Web `<machineKey>` .

Механизм изоляции работает путем рассмотрения каждого приложения на локальном компьютере в качестве уникального клиента, поэтому <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> корневой каталог для любого конкретного приложения автоматически включает идентификатор приложения в качестве дискриминатора. Уникальный идентификатор приложения — это физический путь приложения:

* Для приложений, размещенных в службах IIS, уникальный идентификатор — это физический путь IIS приложения. Если приложение развернуто в среде веб-фермы, это значение является стабильным при условии, что среды IIS настроены одинаково на всех компьютерах в веб-ферме.
* Для автономных приложений, работающих на [сервере Kestrel](xref:fundamentals/servers/index#kestrel), уникальный идентификатор — это физический путь к приложению на диске.

Уникальный идентификатор разрабатывается для того, чтобы сбросить &mdash; оба отдельных приложения и самого компьютера.

Этот механизм изоляции предполагает, что приложения не являются вредоносными. Вредоносное приложение всегда может повлиять на любое другое приложение, выполняемое в той же учетной записи рабочего процесса. В общей среде размещения, в которой приложения взаимно не считаются доверенными, поставщик услуг размещения должен принять меры по обеспечению изоляции на уровне ОС между приложениями, включая разделение базовых хранилищ ключей приложений.

Если система защиты данных не предоставляется узлом ASP.NET Core (например, при создании экземпляра с помощью `DataProtectionProvider` конкретного типа), по умолчанию отключена изоляция приложений. Если изоляция приложений отключена, все приложения, которые поддерживаются одним и тем же материалом для ключа, могут совместно использовать полезные данные, если они предоставляют соответствующие [цели](xref:security/data-protection/consumer-apis/purpose-strings). Чтобы обеспечить изоляцию приложений в этой среде, вызовите метод [SetApplicationName](#setapplicationname) объекта конфигурации и укажите уникальное имя для каждого приложения.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Изменение алгоритмов с помощью Усекриптографикалгорисмс

Стек защиты данных позволяет изменить алгоритм по умолчанию, используемый созданными новыми ключами. Самый простой способ сделать это — вызвать [усекриптографикалгорисмс](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) из обратного вызова конфигурации:

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

По умолчанию EncryptionAlgorithm имеет значение AES-256-CBC, а Валидатионалгорисм по умолчанию — HMACSHA256. Политика по умолчанию может быть задана системным администратором с помощью [политики на уровне компьютера](xref:security/data-protection/configuration/machine-wide-policy), но явный вызов `UseCryptographicAlgorithms` переопределяет политику по умолчанию.

Вызов `UseCryptographicAlgorithms` позволяет указать нужный алгоритм из предопределенного встроенного списка. Не нужно беспокоиться о реализации алгоритма. В приведенном выше сценарии система защиты данных пытается использовать реализацию CNG AES при запуске в Windows. В противном случае он возвращается к управляемому классу [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) .

Реализацию можно указать вручную с помощью вызова [усекустомкриптографикалгорисмс](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Изменение алгоритмов не влияет на существующие ключи в кольце ключа. Он влияет только на вновь созданные ключи.

### <a name="specifying-custom-managed-algorithms"></a>Указание пользовательских управляемых алгоритмов

::: moniker range=">= aspnetcore-2.0"

Чтобы указать пользовательские управляемые алгоритмы, создайте экземпляр [манажедаусентикатеденкрипторконфигуратион](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) , указывающий на типы реализации:

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

Чтобы указать пользовательские управляемые алгоритмы, создайте экземпляр [манажедаусентикатеденкриптионсеттингс](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) , указывающий на типы реализации:

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

Как правило, \* свойства типа должны указывать на конкретные, допускающие создание экземпляров (с помощью общедоступного конструктора без параметров) [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) и [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), хотя в специальных возможностях системы некоторые значения, например `typeof(Aes)` для удобства.

> [!NOTE]
> SymmetricAlgorithm должен иметь длину ключа ≥ 128 бит и размер блока ≥ 64 бит, и он должен поддерживать шифрование в режиме CBC с заполнением PKCS #7. KeyedHashAlgorithm должен иметь размер дайджеста >= 128 бит и должен поддерживать ключи длиной, равную длине дайджеста хэш-алгоритма. KeyedHashAlgorithm строго не обязательно должен быть HMAC.

### <a name="specifying-custom-windows-cng-algorithms"></a>Указание пользовательских алгоритмов CNG Windows

::: moniker range=">= aspnetcore-2.0"

Чтобы указать пользовательский алгоритм CNG Windows с помощью шифрования в режиме CBC с проверкой HMAC, создайте экземпляр [кнгкбкаусентикатеденкрипторконфигуратион](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) , содержащий алгоритмную информацию:

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

Чтобы указать пользовательский алгоритм CNG Windows с помощью шифрования в режиме CBC с проверкой HMAC, создайте экземпляр [кнгкбкаусентикатеденкриптионсеттингс](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) , содержащий алгоритмную информацию:

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
> Алгоритм блочного блочного шифра должен иметь длину ключа >= 128 бит, размер блока >= 64 бит и должен поддерживать шифрование в режиме CBC с использованием дополнений PKCS #7. Хэш-алгоритм должен иметь размер дайджеста >= 128 бит и должен поддерживать открытие с \_ \_ \_ \_ флагом флага HMAC в Handles. Для \* свойств поставщика можно задать значение null, чтобы использовать поставщик по умолчанию для указанного алгоритма. Дополнительные сведения см. в документации по [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .

::: moniker range=">= aspnetcore-2.0"

Чтобы указать пользовательский алгоритм CNG Windows, используя Галоис/шифрование в режиме счетчика с проверкой, создайте экземпляр [кнггкмаусентикатеденкрипторконфигуратион](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) , содержащий алгоритмную информацию:

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

Чтобы указать пользовательский алгоритм CNG Windows, используя Галоис/шифрование в режиме счетчика с проверкой, создайте экземпляр [кнггкмаусентикатеденкриптионсеттингс](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) , содержащий алгоритмную информацию:

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
> Алгоритм симметричного блочного шифра должен иметь длину ключа >= 128 бит, размер блока ровно 128 бит и должен поддерживать шифрование GCM. Чтобы использовать поставщик по умолчанию для указанного алгоритма, можно задать для свойства [енкриптионалгорисмпровидер](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) значение null. Дополнительные сведения см. в документации по [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .

### <a name="specifying-other-custom-algorithms"></a>Указание других пользовательских алгоритмов

Несмотря на то, что не предоставляется в качестве API первого класса, система защиты данных достаточно расширяема, чтобы можно было указать практически любой тип алгоритма. Например, можно хранить все ключи, содержащиеся в аппаратном модуле безопасности (HSM), и обеспечивать пользовательскую реализацию подпрограмм шифрования и расшифровки. Дополнительные сведения см. в статье [иаусентикатеденкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) в разделе [расширение криптографии Core](xref:security/data-protection/extensibility/core-crypto) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Сохранение ключей при размещении в контейнере DOCKER

При размещении в контейнере [DOCKER](/dotnet/standard/microservices-architecture/container-docker-introduction/) ключи должны поддерживаться в одном из следующих:

* Папка, которая является томом DOCKER, который сохраняется за пределами времени существования контейнера, например общего тома или тома, подключенного к узлу.
* Внешний поставщик, например [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) или [Redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Сохранение ключей с помощью Redis

Для хранения ключей следует использовать только версии Redis, поддерживающие [сохраняемость данных Redis](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) . [Хранилище BLOB-объектов Azure](/azure/storage/blobs/storage-blobs-introduction) является постоянным и может использоваться для хранения ключей. Дополнительные сведения см. в [этой статье об ошибке на GitHub](https://github.com/dotnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
