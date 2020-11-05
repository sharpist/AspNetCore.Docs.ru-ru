---
title: Службы gRPC на языке C#
author: juntaoluo
description: Ознакомьтесь с основными понятиями при написании служб gRPC с помощью C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061019"
---
# <a name="grpc-services-with-c"></a>Службы gRPC на языке C\#

Этот документ описывает понятия, необходимые для написания приложений [gRPC](https://grpc.io/docs/guides/) на C#. Рассматриваемые здесь темы применимы к приложениям gRPC как на основе [C-core](https://grpc.io/blog/grpc-stacks), так и на основе ASP.NET Core.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>Файл PROTO

Для разработки API в gRPC используется подход, при котором сначала создается контракт. Буферы протоколов (protobuf) по умолчанию используются в качестве языка IDL. Файл *\*.proto* содержит следующее:

* Определение службы gRPC.
* Сообщения, передаваемые между клиентами и серверами.

Дополнительные сведения о синтаксисе файлов protobuf см. в разделе <xref:grpc/protobuf>.

Например, рассмотрим файл *greet.proto* , используемый в разделе о [начале работы со службами gRPC](xref:tutorials/grpc/grpc-start):

* Определяет службу `Greeter`.
* Служба `Greeter` определяет вызов `SayHello`.
* `SayHello` отправляет сообщение `HelloRequest` и получает сообщение `HelloReply`:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Добавление файла PROTO в приложение C\#

Файл *\*.proto* включается в проект путем его добавления в группу элементов `<Protobuf>`.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

По умолчанию ссылка на `<Protobuf>` создает конкретный клиент и базовый класс службы. С помощью атрибута `GrpcServices` элемента ссылки можно ограничить создание ресурсов на C#. Допустимые значения `GrpcServices`:

* `Both` (по умолчанию, если значение не задано)
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>Средства C# для работы с файлами с расширением .proto

Для создания ресурсов C# из *\*.proto* требуется пакет инструментов [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/). Создаваемые ресурсы (файлы):

* создаются по мере необходимости при каждой сборке проекта;
* не добавляются в проект или не возвращаются в систему управления версиями;
* представляют собой артефакт сборки, находящийся в каталоге *obj*.

Этот пакет требуется как для серверных, так и для клиентских проектов. Метапакет `Grpc.AspNetCore` содержит ссылку на `Grpc.Tools`. Серверные проекты могут добавлять `Grpc.AspNetCore` с помощью диспетчера пакетов в Visual Studio или путем добавления `<PackageReference>` в файл проекта:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Клиентские проекты должны непосредственно ссылаться на `Grpc.Tools` вместе с другими пакетами, необходимыми для использования клиента gRPC. Этот пакет инструментов не требуется во время выполнения, поэтому зависимость помечается как `PrivateAssets="All"`:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Создаваемые ресурсы C#

Пакет инструментов создает типы C#, которые представляют сообщения, определенные во включаемых файлах *\*.proto*.

Для ресурсов на стороне сервера создается абстрактный базовый тип службы. Этот базовый тип содержит определения всех вызовов gRPC, содержащихся в файле *.proto*. Создайте конкретную реализацию службы, производную от этого базового типа, и реализуйте логику для вызовов gRPC. Для `greet.proto` в описанном выше примере создается абстрактный тип `GreeterBase`, содержащий виртуальный метод `SayHello`. Конкретная реализация `GreeterService` переопределяет метод и реализует логику, обрабатывающую вызов gRPC.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Для клиентских ресурсов создается конкретный тип клиента. Вызовы gRPC в файле *.proto* преобразуются в методы конкретного типа, которые могут быть вызваны. Для `greet.proto` в описанном выше примере создается конкретный тип `GreeterClient`. Вызовите `GreeterClient.SayHelloAsync`, чтобы инициировать вызов gRPC на сервере.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

По умолчанию серверные и клиентские ресурсы создаются для каждого файла *\*.proto* , включаемого в группу элементов `<Protobuf>`. Чтобы гарантировать, что в серверном проекте создаются только серверные ресурсы, атрибуту `GrpcServices` присваивается значение `Server`.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Аналогичным образом атрибуту присваивается значение `Client` в клиентских проектах.

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
