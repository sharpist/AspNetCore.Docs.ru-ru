---
title: Шифрование неактивных ключей в Windows и Azure с помощью ASP.NET Core
author: rick-anderson
description: Сведения о реализации ASP.NET Core шифрования ключа защиты данных при хранении.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: c927c926212aeb1263d15fd3fdc753c377b2e305
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153570"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>Шифрование неактивных ключей в Windows и Azure с помощью ASP.NET Core

Система защиты данных [по умолчанию использует механизм обнаружения,](xref:security/data-protection/configuration/default-settings) чтобы определить способ шифрования неактивных криптографических ключей. Разработчик может переопределить механизм обнаружения и вручную указать способ шифрования неактивных ключей.

> [!WARNING]
> Если указать явное [Расположение сохраняемости ключа](xref:security/data-protection/implementation/key-storage-providers), система защиты данных отменяет регистрацию шифрования ключа по умолчанию в механизме RESTful. Следовательно, ключи больше не шифруются. Рекомендуется [указать явный механизм шифрования ключей](xref:security/data-protection/implementation/key-encryption-at-rest) для рабочих развертываний. В этом разделе описаны варианты механизма шифрования с неактивных шифрованием.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Чтобы сохранить ключи в [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), настройте систему с помощью [протекткэйсвисазурекэйваулт](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) в `Startup` классе:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Дополнительные сведения см. в статье [настройка ASP.NET Core Data Protection: протекткэйсвисазурекэйваулт](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Применяется только к развертываниям Windows.**

При использовании Windows DPAPI материал ключа шифруется с помощью [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) перед сохранением в хранилище. DPAPI — это подходящий механизм шифрования для данных, которые никогда не читаются за пределами текущего компьютера (хотя эти ключи можно переключать на Active Directory; см. раздел [DPAPI и перемещаемые профили](https://support.microsoft.com/kb/309408/#6)). Чтобы настроить шифрование неактивных ключей DPAPI, вызовите один из методов расширения [протекткэйсвисдпапи](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Если `ProtectKeysWithDpapi` вызывается без параметров, только текущая учетная запись пользователя Windows может расшифровать сохраненный кольцо ключа. При необходимости можно указать, что любая учетная запись пользователя на компьютере (а не только текущая учетная запись пользователя) сможет расшифровать ключевое кольцо:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>Сертификат X.509

Если приложение распределено между несколькими компьютерами, может быть удобно распространять общий сертификат X. 509 на компьютерах и настраивать размещенные приложения для использования сертификата для шифрования неактивных ключей:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Из-за ограничений .NET Framework поддерживаются только сертификаты с закрытыми ключами CAPI. Возможные решения для этих ограничений см. в приведенном ниже содержимом.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI — NG

**Этот механизм доступен только в Windows 8, Windows Server 2012 и более поздних версиях.**

Начиная с Windows 8, ОС Windows поддерживает DPAPI-NG (также называется CNG DPAPI). Дополнительные сведения см. в разделе [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

Участник кодируется как правило дескриптора защиты. В следующем примере, который вызывает [протекткэйсвисдпапинг](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), только присоединенный к домену пользователь с указанным SID может расшифровать ключевое кольцо:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Также существует перегрузка без параметров `ProtectKeysWithDpapiNG` . Используйте этот удобный метод, чтобы указать правило "SID = {CURRENT_ACCOUNT_SID}", где *CURRENT_ACCOUNT_SID* является идентификатором безопасности текущей учетной записи пользователя Windows:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

В этом сценарии контроллер домена AD отвечает за распространение ключей шифрования, используемых операциями DPAPI-NG. Целевой пользователь может расшифровать зашифрованные полезные данные с любого компьютера, присоединенного к домену (при условии, что процесс выполняется с удостоверением).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Шифрование на основе сертификата с помощью Windows DPAPI-NG

Если приложение выполняется в Windows 8.1/Windows Server 2012 R2 или более поздней версии, можно использовать Windows DPAPI-NG для шифрования на основе сертификата. Используйте строку дескриптора правила "сертификат = Хашид: отпечаток", где *отпечаток* — это шестнадцатеричный отпечаток сертификата SHA1, закодированный в сертификате:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Любое приложение, указываемое в этом репозитории, должно выполняться на Windows 8.1/Windows Server 2012 R2 или более поздней версии для расшифровки ключей.

## <a name="custom-key-encryption"></a>Шифрование с пользовательским ключом

Если встроенные механизмы не подходят, разработчик может указать собственный механизм шифрования ключей, предоставив пользовательский [иксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).
