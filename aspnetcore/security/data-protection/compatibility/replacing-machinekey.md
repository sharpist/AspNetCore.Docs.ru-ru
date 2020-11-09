---
title: Замените ASP.NET machineKey в ASP.NET Core
author: rick-anderson
description: Узнайте, как заменить machineKey в ASP.NET, чтобы позволить использовать новую и безопасную систему защиты данных.
ms.author: riande
ms.date: 04/06/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053063"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="544ef-103">Замените ASP.NET machineKey в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="544ef-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="544ef-104">Реализация `<machineKey>` элемента в ASP.NET [является заменяемой](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="544ef-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="544ef-105">Это позволяет направлять большинство вызовов ASP.NET шифрования через механизм защиты данных с заменой, включая новую систему защиты данных.</span><span class="sxs-lookup"><span data-stu-id="544ef-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="544ef-106">Установка пакета</span><span class="sxs-lookup"><span data-stu-id="544ef-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="544ef-107">Новую систему защиты данных можно установить только в существующее приложение ASP.NET, предназначенное для .NET 4.5.1 или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="544ef-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="544ef-108">Установка завершится ошибкой, если приложение предназначено для .NET 4,5 или более ранней версии.</span><span class="sxs-lookup"><span data-stu-id="544ef-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="544ef-109">Чтобы установить новую систему защиты данных в существующий проект ASP.NET 4.5.1 +, установите пакет Microsoft.AspNetCore.DataProtection.SysТемвеб.</span><span class="sxs-lookup"><span data-stu-id="544ef-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="544ef-110">При этом будет создан экземпляр системы защиты данных с использованием параметров [конфигурации по умолчанию](xref:security/data-protection/configuration/default-settings) .</span><span class="sxs-lookup"><span data-stu-id="544ef-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="544ef-111">При установке пакета он вставляет строку в *Web.config* , которая указывает ASP.NET использовать ее для [большинства криптографических операций](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), включая проверку подлинности с помощью форм, состояние представления и вызовы machineKey. Protect.</span><span class="sxs-lookup"><span data-stu-id="544ef-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="544ef-112">Вставленная строка считывается следующим образом.</span><span class="sxs-lookup"><span data-stu-id="544ef-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="544ef-113">Вы можете определить, активна ли новая система защиты данных, проверив такие поля `__VIEWSTATE` , как, которые должны начинаться с "CfDJ8", как показано в примере ниже.</span><span class="sxs-lookup"><span data-stu-id="544ef-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="544ef-114">"CfDJ8" — это представление в формате Base64 заголовка Magic "09 F0 C9 F0", определяющего полезную нагрузку, защищенную системой защиты данных.</span><span class="sxs-lookup"><span data-stu-id="544ef-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="544ef-115">Конфигурация пакета</span><span class="sxs-lookup"><span data-stu-id="544ef-115">Package configuration</span></span>

<span data-ttu-id="544ef-116">Создается экземпляр системы защиты данных с конфигурацией нулевой установки по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="544ef-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="544ef-117">Однако поскольку ключи по умолчанию сохраняются в локальной файловой системе, это не будет работать для приложений, развернутых в ферме.</span><span class="sxs-lookup"><span data-stu-id="544ef-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="544ef-118">Чтобы устранить эту проблему, можно предоставить конфигурацию, создав тип, который подклассировать Датапротектионстартуп и переопределяющий его метод ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="544ef-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="544ef-119">Ниже приведен пример настраиваемого типа запуска защиты данных, который настроил, где хранятся ключи, и как они шифруются при хранении.</span><span class="sxs-lookup"><span data-stu-id="544ef-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="544ef-120">Он также переопределяет политику изоляции приложений по умолчанию, предоставляя собственное имя приложения.</span><span class="sxs-lookup"><span data-stu-id="544ef-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="544ef-121">Можно также использовать `<machineKey applicationName="my-app" ... />` вместо явного вызова SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="544ef-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="544ef-122">Это удобный механизм, позволяющий не заставлять разработчику создавать Датапротектионстартуп-производный тип, если все, что нужно настроить, задает имя приложения.</span><span class="sxs-lookup"><span data-stu-id="544ef-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="544ef-123">Чтобы включить эту пользовательскую конфигурацию, вернитесь к Web.config и найдите `<appSettings>` элемент, который пакет установки добавляет в файл конфигурации.</span><span class="sxs-lookup"><span data-stu-id="544ef-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="544ef-124">Он будет выглядеть, как в следующей разметке:</span><span class="sxs-lookup"><span data-stu-id="544ef-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="544ef-125">Введите пустое значение с указанием имени сборки только что созданного типа Датапротектионстартуп.</span><span class="sxs-lookup"><span data-stu-id="544ef-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="544ef-126">Если имя приложения — Датапротектиондемо, это будет выглядеть, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="544ef-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="544ef-127">Недавно настроенная система защиты данных теперь готова к использованию внутри приложения.</span><span class="sxs-lookup"><span data-stu-id="544ef-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
