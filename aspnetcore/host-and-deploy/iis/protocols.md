---
title: Использование ASP.NET Core с HTTP/2 в службах IIS
author: rick-anderson
description: Сведения об использовании возможностей HTTP/2 со службами IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057418"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Использование ASP.NET Core с HTTP/2 в службах IIS

Автор [Джастин Коталик (Justin Kotalik)](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html) поддерживается в ASP.NET Core для следующих сценариев развертывания IIS:

* Windows Server 2016 или более поздней версии либо Windows 10 или более поздней версии
* Службы IIS 10 или более поздней версии
* Подключение TLS 1.2 или более поздней версии.
* Размещение [вне процесса](xref:host-and-deploy/iis/index#out-of-process-hosting-model). Подключения к пограничным серверам, открытых для общего доступа, выполняются по протоколу HTTP/2, а подключения к [серверу Kestrel](xref:fundamentals/servers/kestrel) через обратный прокси-сервер — по HTTP/1.1.

При внутрипроцессном развертывании и установленном подключении HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/2`. При внепроцессном развертывании и установленном подключении HTTP/2 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) возвращает `HTTP/1.1`.

Дополнительные сведения о внутрипроцессной и внепроцессной моделях размещения см. в статье <xref:host-and-deploy/aspnet-core-module>.

Протокол HTTP/2 включен по умолчанию для соединений по HTTPS/TLS. Если не удается установить подключение HTTP/2, применяется резервный вариант HTTP/1.1. Дополнительные сведения о настройке HTTP/2 для развертываний IIS см. в статье [об HTTP/2 в IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Расширенные возможности HTTP/2 для поддержки gRPC

Дополнительные возможности HTTP/2 в службах IIS поддерживают gRPC, включая трейлеры ответов и отправку кадров сброса.

Требования для выполнения gRPC в службах IIS

* Внутрипроцессное размещение.
* Сборка 20300.1000 или более поздняя ОС Windows 10. Могут требоваться сборки программы предварительной оценки Windows.
* Подключение TLS 1.2 или более поздней версии.

### <a name="trailers"></a>Трейлеры

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
