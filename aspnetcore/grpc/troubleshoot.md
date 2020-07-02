---
title: Устранение неполадок gRPC в .NET Core
author: jamesnk
description: Устранение ошибок при использовании gRPC в .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 05/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/troubleshoot
ms.openlocfilehash: 9a3e7269db04b79146686668879bf47f1a7dd6af
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400041"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="cc9d5-103">Устранение неполадок gRPC в .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc9d5-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="cc9d5-104">Автор: [Джеймс Ньютон-Кинг](https://twitter.com/jamesnk) (James Newton-King)</span><span class="sxs-lookup"><span data-stu-id="cc9d5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="cc9d5-105">В этом документе рассматриваются часто возникающие проблемы при разработке приложений gRPC на платформе .NET.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="cc9d5-106">Несоответствие конфигурации SSL/TLS клиента и службы</span><span class="sxs-lookup"><span data-stu-id="cc9d5-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="cc9d5-107">В шаблоне и образцах gRPC для защиты служб gRPC по умолчанию используется [протокол TLS](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="cc9d5-108">Клиенты gRPC должны использовать безопасное соединение для успешного вызова защищенных служб gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="cc9d5-109">Проверить, использует ли служба gRPC ASP.NET Core протокол TLS, можно в журналах, создаваемых при запуске приложения.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="cc9d5-110">Служба будет ожидать передачи данных через конечную точку HTTPS:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="cc9d5-111">Для выполнения вызовов через защищенное соединение клиент .NET Core должен использовать `https` в адресе сервера:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="cc9d5-112">Все реализации клиента gRPC поддерживают протокол TLS.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="cc9d5-113">Для клиентов gRPC на других языках обычно требуется канал, настроенный с помощью `SslCredentials`.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="cc9d5-114">В `SslCredentials` указывается сертификат, который будет использоваться клиентом вместо незащищенных учетных данных.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="cc9d5-115">Примеры настройки протокола TLS для различных реализаций клиента gRPC см. в статье, посвященной [проверке подлинности gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="cc9d5-116">Вызов службы gRPC с использованием ненадежного или недействительного сертификата</span><span class="sxs-lookup"><span data-stu-id="cc9d5-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="cc9d5-117">Клиент gRPC .NET требует, чтобы у службы был надежный сертификат.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="cc9d5-118">При вызове службы gRPC без надежного сертификата возвращается следующее сообщение об ошибке:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="cc9d5-119">Необработанное исключение.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-119">Unhandled exception.</span></span> <span data-ttu-id="cc9d5-120">System.Net.Http.HttpRequestException: не удалось установить подключение SSL. См. внутреннее исключение.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="cc9d5-121">---> System.Security.Authentication.AuthenticationException: Удаленный сертификат недопустим согласно процедуре проверки.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="cc9d5-122">Эта ошибка может возникать, если при локальном тестировании приложения сертификат разработки HTTPS ASP.NET Core не является надежным.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="cc9d5-123">См. сведения об устранении этой проблемы в руководстве по [настройке доверия к сертификату разработки HTTPS ASP.NET Core в Windows и macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="cc9d5-124">Если вы вызываете службу gRPC на другом компьютере и не можете установить доверие к сертификату, то клиент gRPC можно настроить так, чтобы недействительный сертификат игнорировался.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="cc9d5-125">В следующем коде с помощью [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) разрешаются вызовы без надежного сертификата:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="cc9d5-126">Ненадежные сертификаты следует использовать только во время разработки приложения.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="cc9d5-127">В рабочих приложениях всегда должны использоваться действительные сертификаты.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="cc9d5-128">Вызов незащищенных служб gRPC с помощью клиента .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc9d5-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="cc9d5-129">Для вызова незащищенных служб gRPC с помощью клиента .NET Core требуется дополнительная настройка.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-129">Additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="cc9d5-130">Клиент gRPC должен установить параметр `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` в значение `true` и использовать `http` в адресе сервера:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="cc9d5-131">Не удается запустить приложение gRPC ASP.NET Core в macOS</span><span class="sxs-lookup"><span data-stu-id="cc9d5-131">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="cc9d5-132">Kestrel не поддерживает HTTP/2 с TLS в macOS и старых версиях Windows, таких как Windows 7.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-132">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="cc9d5-133">В шаблоне и образцах gRPC ASP.NET Core по умолчанию используется протокол TLS.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="cc9d5-134">При попытке запустить сервер gRPC появится следующее сообщение об ошибке:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="cc9d5-135">Не удалось выполнить привязку к https://localhost:5001 в интерфейсе замыкания на себя IPv4: "'HTTP/2 через TLS не поддерживается в macOS из-за отсутствия поддержки ALPN."</span><span class="sxs-lookup"><span data-stu-id="cc9d5-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="cc9d5-136">Для решения этой проблемы настройте Kestrel и клиент gRPC так, чтобы они использовали HTTP/2 *без* TLS.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="cc9d5-137">Это следует делать только во время разработки.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-137">You should only do this during development.</span></span> <span data-ttu-id="cc9d5-138">Если протокол TLS не используется, сообщения gRPC передаются без шифрования.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="cc9d5-139">Для Kestrel должна быть настроена конечная точка HTTP/2 без TLS в файле *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-139">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="cc9d5-140">Если конечная точка HTTP/2 настроена без TLS, для конечной точки [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) должно быть установлено значение `HttpProtocols.Http2`.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-140">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="cc9d5-141">`HttpProtocols.Http1AndHttp2` использовать нельзя, так как протокол TLS необходим для согласования подключения по HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-141">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="cc9d5-142">Без TLS все подключения к конечной точке по умолчанию осуществляются по протоколу HTTP/1.1, а вызовы gRPC завершаются ошибкой.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-142">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="cc9d5-143">Клиент gRPC также должен быть настроен так, чтобы протокол TLS не использовался.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-143">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="cc9d5-144">Дополнительные сведения см. в разделе [Вызов незащищенных служб gRPC с помощью клиента .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-144">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="cc9d5-145">Протокол HTTP/2 без TLS следует использовать только во время разработки приложения.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-145">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="cc9d5-146">В рабочих приложениях всегда должна использоваться защита транспорта.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-146">Production apps should always use transport security.</span></span> <span data-ttu-id="cc9d5-147">Дополнительные сведения см. в статье [Вопросы безопасности в gRPC для ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-147">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="cc9d5-148">Код ресурсов gRPC на C# не создается из файлов PROTO</span><span class="sxs-lookup"><span data-stu-id="cc9d5-148">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="cc9d5-149">Для создания кода для конкретных клиентов и базовых классов службы gRPC необходимо, чтобы проект ссылался на файлы и инструментарий protobuf.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-149">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="cc9d5-150">Необходимо включить следующее:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-150">You must include:</span></span>

* <span data-ttu-id="cc9d5-151">нужные файлы *PROTO* в группе элементов `<Protobuf>`;</span><span class="sxs-lookup"><span data-stu-id="cc9d5-151">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="cc9d5-152">проект должен ссылаться на [импортированные файлы *PROTO*](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions);</span><span class="sxs-lookup"><span data-stu-id="cc9d5-152">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="cc9d5-153">ссылку на пакет инструментария gRPC [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-153">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="cc9d5-154">Дополнительные сведения о создании ресурсов gRPC на C# см. в статье <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-154">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="cc9d5-155">По умолчанию ссылка на `<Protobuf>` создает конкретный клиент и базовый класс службы.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-155">By default, a `<Protobuf>` reference generates a concrete client and a service base class.</span></span> <span data-ttu-id="cc9d5-156">С помощью атрибута `GrpcServices` элемента ссылки можно ограничить создание ресурсов на C#.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-156">The reference element's `GrpcServices` attribute can be used to limit C# asset generation.</span></span> <span data-ttu-id="cc9d5-157">Допустимые значения `GrpcServices`:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-157">Valid `GrpcServices` options are:</span></span>

* <span data-ttu-id="cc9d5-158">`Both` (по умолчанию, если значение не задано)</span><span class="sxs-lookup"><span data-stu-id="cc9d5-158">`Both` (default when not present)</span></span>
* `Server`
* `Client`
* `None`

<span data-ttu-id="cc9d5-159">Веб-приложение ASP.NET Core, в котором размещаются службы gRPC, требует создания только базового класса службы:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="cc9d5-160">Клиент gRPC, выполняющий вызовы gRPC, требует создания только конкретного клиента:</span><span class="sxs-lookup"><span data-stu-id="cc9d5-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="cc9d5-161">В проектах WPF не удается создать ресурсы gRPC на C# из файлов PROTO</span><span class="sxs-lookup"><span data-stu-id="cc9d5-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="cc9d5-162">В проектах WPF есть [известная проблема](https://github.com/dotnet/wpf/issues/810), из-за которой создание кода gRPC работает неправильно.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="cc9d5-163">При использовании любых типов gRPC, созданных в проектах WPF посредством ссылки на `Grpc.Tools` и файлы *PROTO*, будут возникать ошибки компиляции.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="cc9d5-164">Ошибка CS0246: Не удалось найти тип или имя пространства имен "MyGrpcServices" (возможно, отсутствует директива using или ссылка на сборку).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="cc9d5-165">Эту проблему можно решить, выполнив указанные ниже действия.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="cc9d5-166">Создайте проект библиотеки классов .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="cc9d5-167">В новом проекте добавьте ссылки, чтобы включить [создание кода C# из файлов *\*PROTO*](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="cc9d5-168">Добавьте ссылку на пакет [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="cc9d5-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="cc9d5-169">Добавьте файлы *\*.proto* в группу элементов `<Protobuf>`.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="cc9d5-170">В приложении WPF добавьте ссылку на новый проект.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="cc9d5-171">Приложение WPF может использовать созданные типы gRPC из нового проекта библиотеки классов.</span><span class="sxs-lookup"><span data-stu-id="cc9d5-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
