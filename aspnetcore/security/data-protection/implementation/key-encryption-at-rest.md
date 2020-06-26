---
title: Шифрование неактивных ключей в Windows и Azure с помощью ASP.NET Core
author: rick-anderson
description: Сведения о реализации ASP.NET Core шифрования ключа защиты данных при хранении.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: eaa855d48437f80ab912780205530843a9c56476
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399703"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="7ebfb-103">Шифрование неактивных ключей в Windows и Azure с помощью ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ebfb-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="7ebfb-104">Система защиты данных [по умолчанию использует механизм обнаружения,](xref:security/data-protection/configuration/default-settings) чтобы определить способ шифрования неактивных криптографических ключей.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="7ebfb-105">Разработчик может переопределить механизм обнаружения и вручную указать способ шифрования неактивных ключей.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="7ebfb-106">Если указать явное [Расположение сохраняемости ключа](xref:security/data-protection/implementation/key-storage-providers), система защиты данных отменяет регистрацию шифрования ключа по умолчанию в механизме RESTful.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="7ebfb-107">Следовательно, ключи больше не шифруются.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="7ebfb-108">Рекомендуется [указать явный механизм шифрования ключей](xref:security/data-protection/implementation/key-encryption-at-rest) для рабочих развертываний.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="7ebfb-109">В этом разделе описаны варианты механизма шифрования с неактивных шифрованием.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="7ebfb-110">Хранилище ключей Azure;</span><span class="sxs-lookup"><span data-stu-id="7ebfb-110">Azure Key Vault</span></span>

<span data-ttu-id="7ebfb-111">Чтобы сохранить ключи в [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), настройте систему с помощью [протекткэйсвисазурекэйваулт](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) в `Startup` классе:</span><span class="sxs-lookup"><span data-stu-id="7ebfb-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="7ebfb-112">Дополнительные сведения см. в статье [настройка ASP.NET Core Data Protection: протекткэйсвисазурекэйваулт](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="7ebfb-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="7ebfb-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="7ebfb-113">Windows DPAPI</span></span>

<span data-ttu-id="7ebfb-114">**Применяется только к развертываниям Windows.**</span><span class="sxs-lookup"><span data-stu-id="7ebfb-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="7ebfb-115">При использовании Windows DPAPI материал ключа шифруется с помощью [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) перед сохранением в хранилище.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="7ebfb-116">DPAPI — это подходящий механизм шифрования для данных, которые никогда не читаются за пределами текущего компьютера (хотя эти ключи можно переключать на Active Directory; см. раздел [DPAPI и перемещаемые профили](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="7ebfb-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="7ebfb-117">Чтобы настроить шифрование неактивных ключей DPAPI, вызовите один из методов расширения [протекткэйсвисдпапи](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) :</span><span class="sxs-lookup"><span data-stu-id="7ebfb-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="7ebfb-118">Если `ProtectKeysWithDpapi` вызывается без параметров, только текущая учетная запись пользователя Windows может расшифровать сохраненный кольцо ключа.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="7ebfb-119">При необходимости можно указать, что любая учетная запись пользователя на компьютере (а не только текущая учетная запись пользователя) сможет расшифровать ключевое кольцо:</span><span class="sxs-lookup"><span data-stu-id="7ebfb-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="7ebfb-120">Сертификат X.509</span><span class="sxs-lookup"><span data-stu-id="7ebfb-120">X.509 certificate</span></span>

<span data-ttu-id="7ebfb-121">Если приложение распределено между несколькими компьютерами, может быть удобно распространять общий сертификат X. 509 на компьютерах и настраивать размещенные приложения для использования сертификата для шифрования неактивных ключей:</span><span class="sxs-lookup"><span data-stu-id="7ebfb-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="7ebfb-122">Из-за ограничений .NET Framework поддерживаются только сертификаты с закрытыми ключами CAPI.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="7ebfb-123">Возможные решения для этих ограничений см. в приведенном ниже содержимом.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="7ebfb-124">Windows DPAPI — NG</span><span class="sxs-lookup"><span data-stu-id="7ebfb-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="7ebfb-125">**Этот механизм доступен только в Windows 8, Windows Server 2012 и более поздних версиях.**</span><span class="sxs-lookup"><span data-stu-id="7ebfb-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="7ebfb-126">Начиная с Windows 8, ОС Windows поддерживает DPAPI-NG (также называется CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="7ebfb-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="7ebfb-127">Дополнительные сведения см. в разделе [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="7ebfb-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="7ebfb-128">Участник кодируется как правило дескриптора защиты.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="7ebfb-129">В следующем примере, который вызывает [протекткэйсвисдпапинг](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), только присоединенный к домену пользователь с указанным SID может расшифровать ключевое кольцо:</span><span class="sxs-lookup"><span data-stu-id="7ebfb-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="7ebfb-130">Также существует перегрузка без параметров `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="7ebfb-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="7ebfb-131">Используйте этот удобный метод, чтобы указать правило "SID = {CURRENT_ACCOUNT_SID}", где *CURRENT_ACCOUNT_SID* является идентификатором безопасности текущей учетной записи пользователя Windows:</span><span class="sxs-lookup"><span data-stu-id="7ebfb-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="7ebfb-132">В этом сценарии контроллер домена AD отвечает за распространение ключей шифрования, используемых операциями DPAPI-NG.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="7ebfb-133">Целевой пользователь может расшифровать зашифрованные полезные данные с любого компьютера, присоединенного к домену (при условии, что процесс выполняется с удостоверением).</span><span class="sxs-lookup"><span data-stu-id="7ebfb-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="7ebfb-134">Шифрование на основе сертификата с помощью Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="7ebfb-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="7ebfb-135">Если приложение выполняется в Windows 8.1/Windows Server 2012 R2 или более поздней версии, можно использовать Windows DPAPI-NG для шифрования на основе сертификата.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="7ebfb-136">Используйте строку дескриптора правила "сертификат = Хашид: отпечаток", где *отпечаток* — это шестнадцатеричный отпечаток сертификата SHA1, закодированный в сертификате:</span><span class="sxs-lookup"><span data-stu-id="7ebfb-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="7ebfb-137">Любое приложение, указываемое в этом репозитории, должно выполняться на Windows 8.1/Windows Server 2012 R2 или более поздней версии для расшифровки ключей.</span><span class="sxs-lookup"><span data-stu-id="7ebfb-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="7ebfb-138">Шифрование с пользовательским ключом</span><span class="sxs-lookup"><span data-stu-id="7ebfb-138">Custom key encryption</span></span>

<span data-ttu-id="7ebfb-139">Если встроенные механизмы не подходят, разработчик может указать собственный механизм шифрования ключей, предоставив пользовательский [иксмленкриптор](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="7ebfb-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
