---
title: Учебник. Начало работы с Razor Pages в ASP.NET Core
author: rick-anderson
description: В этой серии учебников объясняется, как использовать Razor Pages в ASP.NET Core. Узнайте, как создать модель, сгенерировать код для Razor Pages, использовать Entity Framework Core и SQL Server для доступа к данным, добавлять функции поиска и проверки ввода, а также использовать возможность миграции для обновления модели.
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: db05b8243b91a936a6ed72b0445e0770d44df014
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634583"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>Учебник. Начало работы с Razor Pages в ASP.NET Core

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)

::: moniker range=">= aspnetcore-3.0"
В этом первом руководстве серии приводятся основные сведения о веб-приложении Razor Pages в ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Создание веб-приложения Razor Pages.
> * Запустите приложение.
> * Анализ файлов проекта.

Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Создание веб-приложения Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.
* Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.
  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)
* Присвойте проекту имя **RazorPagesMovie**. Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.
  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* Выберите в раскрывающемся списке пункт **ASP.NET Core 3.1**, затем — **Веб-приложение** и нажмите кнопку **Создать**.

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/3/npx.png)

  Создается следующий начальный проект:

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Перейдите в каталог `cd`, в котором находится проект.

* Выполните следующие команды:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.
  * Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.

* Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите ответ **Да**.

  Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

  ![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.

  ![Выбор шаблона веб-приложения macOS](razor-pages-start/_static/web_app_template_vsmac.png)

* В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:

  * Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
  * Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 3.x.

  Выберите **Далее**.

* Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.

  ![Имя проекта macOS](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Запуск приложения

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>Анализ файлов проекта

Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.

### <a name="pages-folder"></a>Папка Pages

Содержит страницы Razor и вспомогательные файлы. Каждая страница Razor — это пара файлов.

* Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.
* Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.

Имена вспомогательных файлов начинаются с символа подчеркивания. Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц. Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Папка wwwroot

Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы. Для получения дополнительной информации см. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Содержит данные конфигурации, например строки подключения. Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Содержит точку входа для программы. Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

содержит код, задающий поведение приложения. Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="next-steps"></a>Следующие шаги

Перейдите к следующему учебнику в серии:

> [!div class="step-by-step"]
> [Добавление модели](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

Это первый учебник из серии. В этой [серии](xref:tutorials/razor-pages/index) приводятся основные сведения о сборке веб-приложения Razor Pages в ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

Пройдя всю серию, вы получите приложение, которое управляет базой данных фильмов.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

В этом учебнике рассмотрены следующие задачи.

> [!div class="checklist"]
> * Создание веб-приложения Razor Pages.
> * Запустите приложение.
> * Анализ файлов проекта.

Пройдя это руководство, вы получите рабочее веб-приложение Razor Pages, сборка которого описана в последующих руководствах.

![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>Предварительные требования

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>Создание веб-приложения Razor Pages

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* В Visual Studio в меню **Файл** щелкните **Создать** > **Проект**.

* Создайте веб-приложение ASP.NET Core и нажмите кнопку **Далее**.

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2.1.png)

* Присвойте проекту имя **RazorPagesMovie**. Очень важно, чтобы проект имел имя *RazorPagesMovie*, так как пространства имен при копировании и вставке кода должны совпасть.

  ![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/config.png)

* Выберите в раскрывающемся списке пункт **ASP.NET Core 2.2**, затем — **Веб-приложение** и нажмите кнопку **Создать**.

![Новое веб-приложение ASP.NET Core](razor-pages-start/_static/np_2_2.2.png)

  Создается следующий начальный проект:

  ![обозреватель решений](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Откройте [Интегрированный терминал](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Перейдите в каталог `cd`, в котором находится проект.

* Выполните следующие команды:

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Команда `dotnet new` создает новый проект Razor Pages в папке *RazorPagesMovie*.
  * Команда `code` открывает папку *RazorPagesMovie* в текущем экземпляре Visual Studio Code.

* Когда значок строки состояния OmniSharp станет зеленым, появится диалоговое окно с предупреждением **В RazorPagesMovie отсутствуют необходимые ресурсы для сборки и отладки. Добавить их?** Выберите ответ **Да**.

  Каталог *.vscode*, содержащий файлы *launch.json* и *tasks.json*, добавляется в корневой каталог проекта.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

* Щелкните **Файл** > **Новое решение**.

![Новое решение macOS](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* В версии Visual Studio для Mac, предшествующей 8.6, последовательно выберите **.NET Core** > **Приложение** > **Веб-приложение** > **Далее**. В версии 8.6 или более поздней последовательно выберите **Интернет и консоль** > **Приложение** > **Веб-приложение** > **Далее**.

* В диалоговом окне **Настройка нового веб-приложения** сделайте следующее:

  * Убедитесь, что для параметра **Аутентификация** задано значение **Без аутентификации**.
  * Если отобразится запрос на выбор **целевой платформы**, выберите последнюю версию 2.x.

  Выберите **Далее**.

* Присвойте проекту имя **RazorPagesMovie** и нажмите **Создать**.

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Запуск приложения

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение. В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера. Localhost обслуживает только веб-запросы с локального компьютера. Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.

* На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.

  Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  На следующем рисунке показано приложение после принятия отслеживания:

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Нажмите клавиши **CTRL-F5**, чтобы выполнить запуск без отладчика.

  Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`. В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`. Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера. Localhost обслуживает только веб-запросы с локального компьютера.

* На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.

  Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2.png)

  На следующем рисунке показано приложение после принятия отслеживания:

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Нажмите клавиши **Cmd-Opt-F5**, чтобы выполнить запуск без отладчика.

  Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.

* На домашней странице нажмите **Принять**, чтобы согласиться на отслеживание.

  Это приложение не отслеживает персональные данные, но в шаблон проекта входит компонент согласия на случай, если приложение должно соответствовать [общему регламенту по защите данных (GDPR)](xref:security/gdpr) Европейского Союза.

  ![Домашняя или индексная страница](razor-pages-start/_static/homeGDPR2.2_safari.png)

  На следующем рисунке показано приложение после принятия отслеживания:

  ![Домашняя или индексная страница](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Анализ файлов проекта

Ниже приведен обзор основных папок и файлов проекта, с которыми вы будете работать в последующих учебниках.

### <a name="pages-folder"></a>Папка Pages

Содержит страницы Razor и вспомогательные файлы. Каждая страница Razor — это пара файлов.

* Файл *CSHTML*, содержащий HTML-разметку с кодом C# и синтаксисом Razor.
* Файл *. cshtml.cs*, содержащий код C#, который обрабатывает события страницы.

Имена вспомогательных файлов начинаются с символа подчеркивания. Например, файл *_Layout.cshtml* настраивает элементы пользовательского интерфейса, общие для всех страниц. Этот файл настраивает меню навигации в верхней части страницы и уведомление об авторских правах в нижней части страницы. Для получения дополнительной информации см. <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Папка wwwroot

Содержит статические файлы, такие как HTML-файлы, файлы JavaScript и CSS-файлы. Для получения дополнительной информации см. <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appSettings.json

Содержит данные конфигурации, например строки подключения. Для получения дополнительной информации см. <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Содержит точку входа для программы. Для получения дополнительной информации см. <xref:fundamentals/host/generic-host>.

### <a name="startupcs"></a>Startup.cs

Содержит код, который настраивает поведение приложения, например, требуется ли согласие для файлов cookie. Для получения дополнительной информации см. <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Версия руководства на YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Следующие шаги

Перейдите к следующему учебнику в серии:

> [!div class="step-by-step"]
> [Добавление модели](xref:tutorials/razor-pages/model)

::: moniker-end
