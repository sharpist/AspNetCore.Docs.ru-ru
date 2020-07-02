---
title: Создание клиента и сервера gRPC .NET Core в ASP.NET Core
author: juntaoluo
description: В этом учебнике объясняется, как создать службу и клиента gRPC в ASP.NET Core. Узнайте, как создать проект службы gRPC, изменить файл proto и добавить дуплексный режим потоковой передачи вызовов.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: c2ec543bd73c0c15c65358c95def0109c295a0f8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403473"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Учебник. Создание клиента и сервера gRPC в ASP.NET Core

Автор: [John Luo](https://github.com/juntaoluo) (Джон Луо)

В этом руководстве показано, как создать клиент [gRPC](https://grpc.io/docs/guides/) в .NET Core и сервер gRPC ASP.NET Core.

В итоге вы получите клиент gRPC, который взаимодействует со службой Greeter gRPC.

[Просмотреть или скачать пример кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([описание скачивания](xref:index#how-to-download-a-sample)).

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Создание сервера gRPC.
> * Создание клиента gRPC.
> * Тестирование службы клиента gRPC с помощью службы Greeter gRPC.

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a>Создание службы gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Запустите Visual Studio и щелкните **Создать проект** или в меню **Файл** в Visual Studio выберите **Создать** > **Проект**.
* В диалоговом окне **Создание проекта** выберите **Служба gRPC** и нажмите кнопку **Далее**:

  ![Диалоговое окно создания проекта](~/tutorials/grpc/grpc-start/static/cnp.png)

* Присвойте проекту имя **GrpcGreeter**. Для проекта необходимо установить имя *GrpcGreeter*, чтобы при копировании и вставке кода совпадали пространства имен.
* Выберите **Создать**.
* В диалоговом окне **Создать службу gRPC** сделайте следующее:
  * Шаблон **gRPC Service** выбран.
  * Выберите **Создать**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться проект.
* Выполните следующие команды:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * С помощью команды `dotnet new` в папке *GrpcGreeter* создается служба gRPC.
  * С помощью команды `code` папка *GrpcGreeter* открывается в новом экземпляре Visual Studio Code.

  Появится диалоговое окно с предупреждением **Required assets to build and debug are missing from 'GrpcGreeter'. Добавить их?**
* Выберите ответ **Да**.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Из терминала выполните следующие команды:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Эти команды позволяют создать службу gRPC с помощью [.NET Core CLI](/dotnet/core/tools/dotnet).

### <a name="open-the-project"></a>Открытие проекта

В Visual Studio выберите **Файл** > **Открыть** и щелкните файл *GrpcGreeter.csproj*.

---

### <a name="run-the-service"></a>Запуск службы

  [!INCLUDE[](~/includes/run-the-app.md)]

В журналах отобразится служба, которая ожидает передачи данных через `https://localhost:5001`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Шаблон gRPC настроен для использования [протокола TLS](https://tools.ietf.org/html/rfc5246). Клиенты gRPC должны использовать протокол HTTPS для обращения к серверу.
>
> macOS не поддерживает ASP.NET Core gRPC с TLS. Для успешного запуска служб gRPC в macOS требуется дополнительная настройка. Дополнительные сведения см. в статье [Не удается запустить приложение ASP.NET Core gRPC в macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Анализ файлов проекта

Файлы проекта *GrpcGreeter*:

* *greet.proto*: Файл *Protos/greet.proto* определяет службу gRPC `Greeter` и используется для создания ресурсов сервера gRPC. Дополнительные сведения см. в разделе [Введение в gRPC](xref:grpc/index).
* Папка *Services* содержит реализацию службы `Greeter`.
* *appSettings.json*: содержит данные конфигурации, такие как протокол, используемый в Kestrel. Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.
* *Program.cs*: содержит точку входа для службы gRPC. Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.
* *Startup.cs*: содержит код, задающий поведение приложения. Дополнительные сведения: [Запуск приложения](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Создание клиента gRPC в консольном приложении .NET

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Откройте второй экземпляр Visual Studio и щелкните **Создать проект**.
* В диалоговом окне **Создание проекта** выберите **Консольное приложение (.NET Core)** и щелкните **Далее**.
* В текстовое поле **Имя проекта** введите **GrpcGreeterClient** и щелкните **Создать**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Смените каталог (`cd`) на папку, в которой будет содержаться проект.
* Выполните следующие команды:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

Чтобы создать консольное приложение с именем *GrpcGreeterClient*, см. руководство по [созданию полного решения .NET Core в macOS с помощью Visual Studio для Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).

---

### <a name="add-required-packages"></a>Добавление необходимых пакетов

Для клиентского проекта gRPC требуются следующие пакеты:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), который содержит клиент .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), который содержит API сообщений protobuf для C#;
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), который содержит поддержку инструментов C# для файлов protobuf. Пакет инструментов не требуется во время выполнения, поэтому зависимость помечается `PrivateAssets="All"`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Установка пакетов с помощью консоли диспетчера пакетов (PMC) или управления пакетами NuGet.

#### <a name="pmc-option-to-install-packages"></a>Установка пакетов с помощью консоли диспетчера пакетов

* В Visual Studio выберите пункты меню **Сервис** > **Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**.
* В **консоли диспетчера пакетов** выполните команду `cd GrpcGreeterClient`, чтобы перейти в папку с файлами *GrpcGreeterClient.csproj*.
* Выполните следующие команды:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Установка пакетов с помощью раздела управления пакетами NuGet

* Щелкните правой кнопкой мыши проект в **обозревателе решений** > **Управление пакетами NuGet**
* Выберите вкладку **Обзор**.
* В поле поиска введите **Grpc.Net.Client**.
* Выберите пакет **Grpc.Net.Client** на вкладке **Обзор** и нажмите кнопку **Установить**.
* Повторите все шаги для `Google.Protobuf` и `Grpc.Tools`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Во **встроенном терминале** выполните следующие команды:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните правой кнопкой мыши папку **Пакеты** на **панели решения** > **Добавление пакетов**
* В поле поиска введите **Grpc.Net.Client**.
* В области результатов выберите пакет **Grpc.Net.Client** и нажмите кнопку **Добавить пакет**
* Повторите все шаги для `Google.Protobuf` и `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Добавление greet.proto

* Создайте папку *Protos* в клиентском проекте gRPC.
* Скопируйте файл *Protos\greet.proto* из службы Greeter gRPC в проект клиента gRPC.
* Измените файл проекта *GrpcGreeterClient.csproj*:

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Щелкните проект правой кнопкой мыши и выберите **Изменить файл проекта**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Выберите файл *GrpcGreeterClient.csproj*.

  # <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

  Щелкните проект правой кнопкой мыши и выберите **Сервис** > **Изменить файл**.

  ---

* Добавьте группу элементов с элементом `<Protobuf>`, ссылающимся на файл *greet.proto*:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Создание клиента Greeter

Скомпилируйте проект, чтобы создать типы в пространстве имен `GrpcGreeter`. Типы `GrpcGreeter` создаются автоматически в процессе сборки.

Добавьте в файл *Program.cs* клиента gRPC следующий код:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

файл *Program.cs* содержит точку входа и логику для клиента gRPC.

Клиент Greeter создается следующим образом:

* Создание экземпляра `GrpcChannel` со сведениями для создания подключения к службе gRPC.
* Использование `GrpcChannel` для создания клиента Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Клиент Greeter вызывает асинхронный метод `SayHello`. Отображается результат вызова `SayHello`:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Тестирование клиента gRPC с помощью службы Greeter gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В службе Greeter нажмите `Ctrl+F5` для запуска сервера без отладчика.
* В проекте `GrpcGreeterClient` нажмите `Ctrl+F5` для запуска клиента без отладчика.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Запустите службу Greeter.
* Запустите клиент.


# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Запустите службу Greeter.
* Запустите клиент.

---

Клиент отправляет приветствие в службу с сообщением, в котором содержится его имя *GreeterClient*. Служба отправляет сообщение "Hello GreeterClient" в качестве ответа. Ответ "Hello GreeterClient" отображается в командной строке:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Служба gRPC записывает сведения об успешном вызове в журналы, что отображается в командной строке:

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> Чтобы применить код, описанный в этой статье, требуется сертификат разработки HTTPS ASP.NET Core для защиты службы gRPC. Если .NET gRPC возвращает сообщение `The remote certificate is invalid according to the validation procedure.` или `The SSL connection could not be established.`, это значит, что сертификат разработки не является доверенным. Чтобы исправить эту проблему, см. [Вызов службы gRPC с использованием ненадежного или недействительного сертификата](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Следующие шаги

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
