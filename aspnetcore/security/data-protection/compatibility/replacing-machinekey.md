---
title: Замените ASP.NET machineKey в ASP.NET Core
author: rick-anderson
description: Узнайте, как заменить machineKey в ASP.NET, чтобы позволить использовать новую и безопасную систему защиты данных.
ms.author: riande
ms.date: 04/06/2019
no-loc:
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 8cae0b8f1c4582e272061ff87868b32568dfe595
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625574"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Замените ASP.NET machineKey в ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

Реализация `<machineKey>` элемента в ASP.NET [является заменяемой](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Это позволяет направлять большинство вызовов ASP.NET шифрования через механизм защиты данных с заменой, включая новую систему защиты данных.

## <a name="package-installation"></a>Установка пакета

> [!NOTE]
> Новую систему защиты данных можно установить только в существующее приложение ASP.NET, предназначенное для .NET 4.5.1 или более поздней версии. Установка завершится ошибкой, если приложение предназначено для .NET 4,5 или более ранней версии.

Чтобы установить новую систему защиты данных в существующий проект ASP.NET 4.5.1 +, установите пакет Microsoft.AspNetCore.DataProtection.SysТемвеб. При этом будет создан экземпляр системы защиты данных с использованием параметров [конфигурации по умолчанию](xref:security/data-protection/configuration/default-settings) .

При установке пакета он вставляет строку в *Web.config* , которая указывает ASP.NET использовать ее для [большинства криптографических операций](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), включая проверку подлинности с помощью форм, состояние представления и вызовы machineKey. Protect. Вставленная строка считывается следующим образом.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Вы можете определить, активна ли новая система защиты данных, проверив такие поля `__VIEWSTATE` , как, которые должны начинаться с "CfDJ8", как показано в примере ниже. "CfDJ8" — это представление в формате Base64 заголовка Magic "09 F0 C9 F0", определяющего полезную нагрузку, защищенную системой защиты данных.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Конфигурация пакета

Создается экземпляр системы защиты данных с конфигурацией нулевой установки по умолчанию. Однако поскольку ключи по умолчанию сохраняются в локальной файловой системе, это не будет работать для приложений, развернутых в ферме. Чтобы устранить эту проблему, можно предоставить конфигурацию, создав тип, который подклассировать Датапротектионстартуп и переопределяющий его метод ConfigureServices.

Ниже приведен пример настраиваемого типа запуска защиты данных, который настроил, где хранятся ключи, и как они шифруются при хранении. Он также переопределяет политику изоляции приложений по умолчанию, предоставляя собственное имя приложения.

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
> Можно также использовать `<machineKey applicationName="my-app" ... />` вместо явного вызова SetApplicationName. Это удобный механизм, позволяющий не заставлять разработчику создавать Датапротектионстартуп-производный тип, если все, что нужно настроить, задает имя приложения.

Чтобы включить эту пользовательскую конфигурацию, вернитесь к Web.config и найдите `<appSettings>` элемент, который пакет установки добавляет в файл конфигурации. Он будет выглядеть, как в следующей разметке:

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

Введите пустое значение с указанием имени сборки только что созданного типа Датапротектионстартуп. Если имя приложения — Датапротектиондемо, это будет выглядеть, как показано ниже.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Недавно настроенная система защиты данных теперь готова к использованию внутри приложения.
