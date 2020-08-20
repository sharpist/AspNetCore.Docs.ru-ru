---
title: Вопросы безопасности применительно к gRPC для ASP.NET Core
author: jamesnk
description: Сведения о вопросах безопасности применительно к gRPC для ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: ca03a129281377af9271d56e2400d1a238358554
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632659"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a><span data-ttu-id="4bbe5-103">Вопросы безопасности применительно к gRPC для ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bbe5-103">Security considerations in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="4bbe5-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="4bbe5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4bbe5-105">В этой статье представлены сведения о защите gRPC в .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-105">This article provides information on securing gRPC with .NET Core.</span></span>

## <a name="transport-security"></a><span data-ttu-id="4bbe5-106">Защита транспорта</span><span class="sxs-lookup"><span data-stu-id="4bbe5-106">Transport security</span></span>

<span data-ttu-id="4bbe5-107">Сообщения gRPC отправляются и принимаются по протоколу HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-107">gRPC messages are sent and received using HTTP/2.</span></span> <span data-ttu-id="4bbe5-108">Примите во внимание следующие рекомендации.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-108">We recommend:</span></span>

* <span data-ttu-id="4bbe5-109">Для защиты сообщений в рабочих приложениях gRPC следует использовать [протокол TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="4bbe5-109">[Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) be used to secure messages in production gRPC apps.</span></span>
* <span data-ttu-id="4bbe5-110">Службы gRPC должны ожидать передачи данных и отвечать только через защищенные порты.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-110">gRPC services should only listen and respond over secured ports.</span></span>

<span data-ttu-id="4bbe5-111">В Kestrel следует настроить протокол TLS.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-111">TLS is configured in Kestrel.</span></span> <span data-ttu-id="4bbe5-112">Дополнительные сведения о настройке конечных точек Kestrel см. в статье [Конфигурация конечной точки Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="4bbe5-112">For more information on configuring Kestrel endpoints, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="exceptions"></a><span data-ttu-id="4bbe5-113">Исключения</span><span class="sxs-lookup"><span data-stu-id="4bbe5-113">Exceptions</span></span>

<span data-ttu-id="4bbe5-114">Сообщения об исключениях, как правило, считаются конфиденциальными данными, которые не следует раскрывать клиенту.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-114">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="4bbe5-115">По умолчанию gRPC не отправляет сведения об исключении, возникшем в службе gRPC, клиенту.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-115">By default, gRPC doesn't send the details of an exception thrown by a gRPC service to the client.</span></span> <span data-ttu-id="4bbe5-116">Вместо этого клиент получает общее сообщение с указанием произошедшей ошибки.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-116">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="4bbe5-117">Доставку сообщений об исключениях клиенту можно переопределить (например, при разработке или тестировании) с помощью [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span><span class="sxs-lookup"><span data-stu-id="4bbe5-117">Exception message delivery to the client can be overridden (for example, in development or test) with [EnableDetailedErrors](xref:grpc/configuration#configure-services-options).</span></span> <span data-ttu-id="4bbe5-118">В рабочих приложениях сообщения об исключениях не должны предоставляться клиенту.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-118">Exception messages shouldn't be exposed to the client in production apps.</span></span>

## <a name="message-size-limits"></a><span data-ttu-id="4bbe5-119">Ограничения на размер сообщений</span><span class="sxs-lookup"><span data-stu-id="4bbe5-119">Message size limits</span></span>

<span data-ttu-id="4bbe5-120">Входящие сообщения для клиентов и служб gRPC загружаются в память.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-120">Incoming messages to gRPC clients and services are loaded into memory.</span></span> <span data-ttu-id="4bbe5-121">Ограничения на размер сообщений позволяют предотвратить чрезмерное потребление ресурсов системой gRPC.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-121">Message size limits are a mechanism to help prevent gRPC from consuming excessive resources.</span></span>

<span data-ttu-id="4bbe5-122">В gRPC используются ограничения на размер отдельных входящих и исходящих сообщений.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-122">gRPC uses per-message size limits to manage incoming and outgoing messages.</span></span> <span data-ttu-id="4bbe5-123">По умолчанию максимальный размер входящего сообщения в gRPC составляет 4 МБ.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-123">By default, gRPC limits incoming messages to 4 MB.</span></span> <span data-ttu-id="4bbe5-124">На размер исходящих сообщений ограничений нет.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-124">There is no limit on outgoing messages.</span></span>

<span data-ttu-id="4bbe5-125">На сервере можно настроить ограничения на размер сообщений для всех служб приложения gRPC с помощью `AddGrpc`:</span><span class="sxs-lookup"><span data-stu-id="4bbe5-125">On the server, gRPC message limits can be configured for all services in an app with `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

<span data-ttu-id="4bbe5-126">С помощью `AddServiceOptions<TService>` можно также настроить ограничения для отдельной службы.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-126">Limits can also be configured for an individual service using `AddServiceOptions<TService>`.</span></span> <span data-ttu-id="4bbe5-127">Дополнительные сведения о настройке ограничений на размер сообщений см. в статье [Настройка gRPC](xref:grpc/configuration).</span><span class="sxs-lookup"><span data-stu-id="4bbe5-127">For more information on configuring message size limits, see [gRPC configuration](xref:grpc/configuration).</span></span>

## <a name="client-certificate-validation"></a><span data-ttu-id="4bbe5-128">Проверка сертификата клиента</span><span class="sxs-lookup"><span data-stu-id="4bbe5-128">Client certificate validation</span></span>

<span data-ttu-id="4bbe5-129">[Сертификаты клиента](https://tools.ietf.org/html/rfc5246#section-7.4.4) изначально проверяются при установке соединения.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-129">[Client certificates](https://tools.ietf.org/html/rfc5246#section-7.4.4) are initially validated when the connection is established.</span></span> <span data-ttu-id="4bbe5-130">По умолчанию Kestrel не выполняет дополнительную проверку сертификата клиента для подключения.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-130">By default, Kestrel doesn't perform additional validation of a connection's client certificate.</span></span>

<span data-ttu-id="4bbe5-131">Рекомендуется, чтобы службы gRPC, защищенные с помощью сертификатов клиента, использовали пакет [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth).</span><span class="sxs-lookup"><span data-stu-id="4bbe5-131">We recommend that gRPC services secured by client certificates use the [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) package.</span></span> <span data-ttu-id="4bbe5-132">При проверке подлинности на основе сертификата в ASP.NET Core выполняются дополнительные проверки сертификата клиента, включая следующие:</span><span class="sxs-lookup"><span data-stu-id="4bbe5-132">ASP.NET Core certification authentication will perform additional validation on a client certificate, including:</span></span>

* <span data-ttu-id="4bbe5-133">сертификат имеет допустимое значение расширенного использования ключа (EKU);</span><span class="sxs-lookup"><span data-stu-id="4bbe5-133">Certificate has a valid extended key use (EKU)</span></span>
* <span data-ttu-id="4bbe5-134">период действия сертификата не истек;</span><span class="sxs-lookup"><span data-stu-id="4bbe5-134">Is within its validity period</span></span>
* <span data-ttu-id="4bbe5-135">проверка отзыва сертификата.</span><span class="sxs-lookup"><span data-stu-id="4bbe5-135">Check certificate revocation</span></span>
