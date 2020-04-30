---
title: Использование SignalR для ASP.NET Core с Blazor WebAssembly
author: guardrex
description: Создание приложения чата, которое использует SignalR для ASP.NET Core с Blazor WebAssembly.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 78c5fbb8b91b934bcb34525672e9e26b6a95290e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111153"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Использование SignalR для ASP.NET Core с Blazor WebAssembly

Авторы: [Дэниэл Рот (Daniel Roth)](https://github.com/danroth27) и [Люк Лэтем (Luke Latham)](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

В этом руководстве показано, как создать приложение, работающее в реальном времени, с помощью SignalR и Blazor WebAssembly. Вы научитесь:

> [!div class="checklist"]
> * создавать проект размещенного приложения Blazor WebAssembly;
> * Добавление клиентской библиотеки SignalR
> * добавлять концентратор SignalR;
> * добавлять службы и конечную точку SignalR для концентратора SignalR;
> * добавлять код компонента Razor для чата.

Когда вы выполните задачи из этого руководства, у вас будет работающее приложение чата.

[Просмотреть или скачать образец кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([как скачивать](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>Создание проекта размещенного приложения Blazor WebAssembly

Если вы не используете предварительную версию 2 Visual Studio 16.6 и более позднюю, установите шаблон [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly). В период действия предварительной версии Blazor WebAssembly будет использоваться предварительная версия пакета [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/). В командной оболочке выполните следующую команду:

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
```

Следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Создайте новый проект.

1. Выберите **Приложение Blazor** и нажмите кнопку **Далее**.

1. В поле **Имя проекта** введите BlazorSignalRApp. Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Выберите **Создать**.

1. Выберите шаблон **приложения Blazor WebAssembly**.

1. В разделе **Дополнительно** установите флажок **Размещенный проект ASP.NET Core**.

1. Выберите **Создать**.

> [!NOTE]
> Если вы обновили или установили новую версию Visual Studio, а шаблон Blazor WebAssembly не отображается в пользовательском интерфейсе VS, переустановите шаблон с помощью команды `dotnet new` (см. выше).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. В командной оболочке выполните следующую команду:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Откройте папку проекта приложения в Visual Studio Code.

1. Когда откроется диалоговое окно для добавления ресурсов создания и отладки приложения, выберите **Да**. Visual Studio Code автоматически добавит папку *.vscode* с файлами *launch.json* и *tasks.json*.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. В командной оболочке выполните следующую команду:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. В Visual Studio для Mac откройте проект. Для этого перейдите к его папке и откройте файл решения проекта ( *.sln*).

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

В командной оболочке выполните следующую команду:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>Добавление клиентской библиотеки SignalR

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **BlazorSignalRApp.Client** и выберите **Управление пакетами NuGet**.

1. Убедитесь, что в диалоговом окне **Управление пакетами NuGet** для параметра **Источник пакета** установлено значение *nuget.org*.

1. Выберите **Обзор** и введите в поле поиска Microsoft.AspNetCore.SignalR.Client.

1. В результатах поиска выберите пакет `Microsoft.AspNetCore.SignalR.Client` и щелкните **Установить**.

1. Если откроется диалоговое окно **Просмотр изменений**, нажмите кнопку **ОК**.

1. Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Я принимаю**, если принимаете условия.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Во **встроенном терминале** (**Просмотр** > **Терминал** на панели инструментов) выполните следующие команды:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. На боковой панели **Решение** щелкните правой кнопкой мыши проект **BlazorSignalRApp.Client** и выберите пункт **Управление пакетами NuGet**.

1. Убедитесь, что в диалоговом окне **Управление пакетами NuGet** в раскрывающемся меню источника установлено значение *nuget.org*.

1. Выберите **Обзор** и введите в поле поиска Microsoft.AspNetCore.SignalR.Client.

1. В результатах поиска установите флажок рядом с пакетом `Microsoft.AspNetCore.SignalR.Client` и выберите **Добавить пакет**.

1. Если откроется диалоговое окно **Принятие условий лицензионного соглашения**, выберите **Принять**, чтобы принять условия.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

В командной оболочке выполните следующие команды:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>Добавление концентратора SignalR

В проекте **BlazorSignalRApp.Server** создайте папку *Hubs* и добавьте следующий класс `ChatHub` (*Hubs/ChatHub.cs*):

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Добавьте службы и конечную точку для концентратора SignalR

1. В проекте **BlazorSignalRApp.Server** откройте файл *Startup.cs*.

1. Добавьте пространство имен для класса `ChatHub` в начало файла:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Добавьте службы SignalR и промежуточного ПО для компрессии ответа в `Startup.ConfigureServices`:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. В `Startup.Configure` между конечными точками для контроллера и отката на стороне клиента добавьте конечную точку для концентратора:

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>добавлять код компонента Razor для чата.

1. В проекте **BlazorSignalRApp.Client** откройте файл *Pages/Index.razor*.

1. Замените разметку следующим кодом:

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>Запуск приложения

1. Следуйте указаниям по выбору инструментов:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. В **обозревателе решений** выберите проект **BlazorSignalRApp.Server**. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Когда VS Code предлагает создать профиль запуска для серверного приложения ( *. vscode/Launch. JSON*), запись `program` выглядит примерно так, как показано ниже, чтобы указать сборку приложения (`{APPLICATION NAME}.Server.dll`):

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. Нажмите клавишу <kbd>F5</kbd>, чтобы запустить программу с отладкой, или <kbd>Ctrl</kbd>+<kbd>F5</kbd>, чтобы запустить ее без отладки.

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. На боковой панели **Решение** выберите проект **BlazorSignalRApp.Server**. Нажмите клавишу <kbd>⌘</kbd>+<kbd>↩</kbd>**, чтобы запустить приложение с отладкой, или <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, чтобы запустить приложение без отладки.

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli/)

1. В командной оболочке выполните следующие команды:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Скопируйте URL-адрес из адресной строки, откройте другой экземпляр или вкладку браузера и вставьте URL-адрес в адресную строку.

1. Выберите любой браузер, введите имя и сообщение и нажмите кнопку **Отправить**. Имя и сообщение отображаются на обеих страницах мгновенно:

   ![Пример приложения Blazor WebAssembly для SignalR в двух окнах браузера, где отображается обмен сообщениями.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Цитаты: *Звездный путь VI. Неоткрытая страна* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как:

> [!div class="checklist"]
> * создавать проект размещенного приложения Blazor WebAssembly;
> * Добавление клиентской библиотеки SignalR
> * добавлять концентратор SignalR;
> * добавлять службы и конечную точку SignalR для концентратора SignalR;
> * добавлять код компонента Razor для чата.

Дополнительные сведения о создании приложений Blazor см. в документации по Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:signalr/introduction>
