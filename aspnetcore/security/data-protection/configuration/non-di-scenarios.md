---
title: Сценарии без поддержки внедрения зависимостей для защиты данных в ASP.NET Core
author: rick-anderson
description: Узнайте, как поддерживать сценарии защиты данных, в которых вы не можете использовать службу, предоставляемую внедрением зависимостей, или не хотите.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 633548eabe38508ff0cc76ae880220e1dac9b414
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020123"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Сценарии без поддержки внедрения зависимостей для защиты данных в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

ASP.NET Core система защиты данных обычно [добавляется в контейнер службы](xref:security/data-protection/consumer-apis/overview) и используется зависимыми компонентами посредством внедрения зависимостей (DI). Однако бывают случаи, когда это нецелесообразно или нежелательно, особенно при импорте системы в существующее приложение.

Для поддержки этих сценариев пакет [Microsoft. AspNetCore. Data Protection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) предоставляет конкретный тип, [датапротектионпровидер](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), который обеспечивает простой способ использования защиты данных, не полагаясь на Di. `DataProtectionProvider`Тип реализует [идатапротектионпровидер](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). `DataProtectionProvider`Только для создания экземпляра [DirectoryInfo](/dotnet/api/system.io.directoryinfo) необходимо указать, где должны храниться криптографические ключи поставщика, как показано в следующем примере кода:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

По умолчанию `DataProtectionProvider` конкретный тип не шифрует материал необработанного ключа перед его сохранением в файловой системе. Это предназначено для поддержки сценариев, в которых разработчик указывает на общую сетевую папку, и система защиты данных не может автоматически вычислить подходящий механизм шифрования ключей с недоступностью.

Кроме того, `DataProtectionProvider` конкретный тип не [изолирует приложения](xref:security/data-protection/configuration/overview#per-application-isolation) по умолчанию. Все приложения, использующие один и тот же каталог ключей, могут использовать полезные данные при условии соответствия их [параметров цели](xref:security/data-protection/consumer-apis/purpose-strings) .

Конструктор [датапротектионпровидер](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) принимает необязательный обратный вызов конфигурации, который можно использовать для настройки поведения системы. В примере ниже показано восстановление изоляции с помощью явного вызова [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). В примере также демонстрируется настройка системы для автоматического шифрования сохраняемых ключей с помощью Windows DPAPI. Если каталог указывает на общую папку UNC, может потребоваться распространить общий сертификат на всех соответствующих компьютерах и настроить систему на использование шифрования на основе сертификата с помощью вызова [протекткэйсвисцертификате](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Экземпляры `DataProtectionProvider` конкретного типа затратны на создание. Если приложение поддерживает несколько экземпляров этого типа и все они используют один и тот же каталог хранилища ключей, производительность приложений может снизиться. Если используется `DataProtectionProvider` тип, рекомендуется создать этот тип один раз и использовать его как можно больше. `DataProtectionProvider`Тип и все экземпляры [идатапротектор](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) , созданные из него, являются потокобезопасными для нескольких вызывающих объектов.
