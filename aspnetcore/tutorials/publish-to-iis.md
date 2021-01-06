---
title: Публикация приложения ASP.NET Core в службах IIS
author: rick-anderson
description: Узнайте, как разместить приложение ASP.NET Core на сервере служб IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753157"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>Публикация приложения ASP.NET Core в службах IIS

В этом руководстве описывается, как разместить приложение ASP.NET Core на сервере служб IIS.

В руководстве рассматриваются следующие действия:

> [!div class="checklist"]
> * установка пакета размещения .NET Core в Windows Server;
> * создание сайта служб IIS в диспетчере служб IIS;
> * развертывание приложения ASP.NET Core.

## <a name="prerequisites"></a>Предварительные требования

* [Пакет SDK для .NET Core](/dotnet/core/sdk), установленный на компьютере разработки.
* Сервер Windows Server с настроенной ролью **Веб-сервер (IIS)** . Если сервер не настроен для размещения веб-сайтов со службами IIS, следуйте указаниям в разделе *Настройка служб IIS* статьи <xref:host-and-deploy/iis/index#iis-configuration>, а затем вернитесь к этому руководству.

> [!WARNING]
> **Принципы настройки служб IIS и обеспечения безопасности веб-сайта не рассматриваются в этом руководстве.** Перед размещением рабочих приложений в службах IIS ознакомьтесь с руководством по службам IIS в [документации по Microsoft IIS](https://www.iis.net/) и [статьей о размещении ASP.NET Core с помощью служб IIS](xref:host-and-deploy/iis/index).
>
> Важные сценарии размещения служб IIS, не рассматриваемые в этом руководстве:
>
> * [Создание куста реестра для защиты данных ASP.NET Core](xref:host-and-deploy/iis/advanced#data-protection)
> * [Настройка списка управления доступом (ACL) для пула приложений](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * Чтобы сосредоточиться на принципах развертывания посредством служб IIS, в этом руководстве приложение развертывается без настройки протокола HTTPS в службах IIS. Дополнительные сведения о размещении приложения с поддержкой протокола HTTPS см. в статьях, посвященных безопасности, в разделе [Дополнительные ресурсы](#additional-resources) этой статьи. Дополнительные рекомендации по размещению приложений ASP.NET Core приведены в статье <xref:host-and-deploy/iis/index>.

## <a name="install-the-net-core-hosting-bundle"></a>Установка пакета размещения .NET Core

Установите *пакет размещения .NET Core* на сервере служб IIS. В составе пакета устанавливаются среда выполнения .NET Core, библиотека .NET Core и [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module). Модуль позволяет запускать приложения ASP.NET Core под управлением IIS.

Скачайте установщик по следующей ссылке:

[Текущий установщик пакета размещения .NET Core (прямая загрузка)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. Запустите установщик на сервере служб IIS.

1. Перезагрузите сервер или в командой оболочке выполните команду `net stop was /y`, а затем — `net start w3svc`.

## <a name="create-the-iis-site"></a>Создание сайта IIS

1. На сервере служб IIS создайте папку, в которой будут храниться файлы и папки опубликованного приложения. На следующем этапе путь к папке предоставляется IIS как физический путь к приложению. Дополнительные сведения о папке развертывания и структуре файлов приложения см. в статье <xref:host-and-deploy/directory-structure>.

1. В окне диспетчера IIS на панели **Подключения** разверните узел сервера. Щелкните правой кнопкой мыши папку **Сайты**. В контекстном меню выберите пункт **Добавить веб-сайт**.

1. Укажите **Имя сайта** и задайте **Физический путь** к созданной папке развертывания приложения. Укажите конфигурацию **привязки** и нажмите кнопку **ОК**, чтобы создать веб-сайт.

   > [!WARNING]
   > **Не используйте** привязки с подстановочными знаками (`http://*:80/` и `http://+:80`) на верхнем уровне. Это может создать уязвимость и поставить ваше приложение под угрозу. Сюда относятся и строгие, и нестрогие подстановочные знаки. Вместо этого используйте имена узлов в явном виде. Привязки с подстановочными знаками на уровне дочерних доменов (например `*.mysub.com`) не создают таких угроз безопасности, если вы полностью контролируете родительский домен (в отличие от варианта `*.com`, создающего уязвимость). Дополнительные сведения см. в документе [rfc7230, раздел 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Убедитесь в том, что удостоверение модели процесса имеет соответствующие разрешения.

   При изменении удостоверения по умолчанию для пула приложений (**Модель процесса** >  **Identity** ) с `ApplicationPoolIdentity` на другое убедитесь в том, что у нового удостоверения есть соответствующие разрешения для доступа к папке приложения, базе данных и другим необходимым ресурсам. Например, пулу приложений требуются права на чтение и запись в папках, в которых приложение считывает и записывает файлы.

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>Создание приложения Razor Pages ASP.NET Core

Следуйте указаниям из руководства <xref:getting-started>, чтобы создать приложение Razor Pages.

## <a name="publish-and-deploy-the-app"></a>Публикация и развертывание приложения

*Публикация приложения* означает создание скомпилированного приложения, которое можно разместить на сервере. *Развертывание приложения* означает перемещение опубликованного приложения в систему размещения. Публикация обеспечивается [пакетом SDK для .NET Core](/dotnet/core/sdk), а развертывание может производиться различными способами. В этом руководстве применяется развертывание с помощью *папок*, которое состоит из следующих этапов:
 
* Приложение публикуется в папке.
* Содержимое папки перемещается в папку на сайте IIS (**физический путь** к сайту в диспетчере IIS).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите **Опубликовать**.
1. В диалоговом оке **Выберите целевой объект публикации** выберите вариант публикации **Папка**.
1. Укажите путь к **папке или общей папке**.
   * Если вы создали для сайта IIS папку, доступную на компьютере разработки в качестве сетевой папки, укажите путь к общей папке. Для публикации в общей папке текущий пользователь должен иметь доступ на запись.
   * Если выполнить развертывание непосредственно в папке сайта IIS на сервере IIS невозможно, опубликуйте приложение в папке на съемном носителе и физически переместите опубликованное приложение в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS. Переместите содержимое папки `bin/Release/{TARGET FRAMEWORK}/publish` в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.
1. Нажмите кнопку **Опубликовать**.

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

1. В командной оболочке опубликуйте приложение в конфигурации выпуска, выполнив команду [dotnet publish](/dotnet/core/tools/dotnet-publish):

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. Переместите содержимое папки `bin/Release/{TARGET FRAMEWORK}/publish` в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.

# <a name="visual-studio-for-mac"></a>[Visual Studio для Mac](#tab/visual-studio-mac)

1. В **обозревателе решений** щелкните правой кнопкой мыши проект и выберите пункты **Опубликовать** > **Опубликовать в папку**.
1. Укажите путь в поле **Выберите папку**.
   * Если вы создали для сайта IIS папку, доступную на компьютере разработки в качестве сетевой папки, укажите путь к общей папке. Для публикации в общей папке текущий пользователь должен иметь доступ на запись.
   * Если выполнить развертывание непосредственно в папке сайта IIS на сервере IIS невозможно, опубликуйте приложение в папке на съемном носителе и физически переместите опубликованное приложение в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS. Переместите содержимое папки `bin/Release/{TARGET FRAMEWORK}/publish` в папку сайта IIS на сервере, которая является **физическим путем** сайта в диспетчере IIS.
1. Нажмите кнопку **Опубликовать**.

---

## <a name="browse-the-website"></a>Обзор веб-сайта

Приложение будет доступно в браузере после получения первого запроса. Выполните запрос к приложению в привязке конечной точки, созданной в диспетчере служб IIS для сайта.

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как:

> [!div class="checklist"]
> * установка пакета размещения .NET Core в Windows Server;
> * создание сайта служб IIS в диспетчере служб IIS;
> * развертывание приложения ASP.NET Core.

Дополнительные сведения о размещении приложений ASP.NET Core в службах IIS см. в обзорной статье о службах IIS:

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>Дополнительные ресурсы

### <a name="articles-in-the-aspnet-core-documentation-set"></a>Статьи в наборе документации по ASP.NET Core

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>Статьи, относящиеся к развертыванию приложений ASP.NET Core

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [Публикация веб-приложения в папку с помощью Visual Studio для Mac](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>Статьи о конфигурации HTTPS служб IIS

* [Настройка SSL в диспетчере служб IIS](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [Настройка SSL в службах IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>Статьи о службах IIS и Windows Server

* [Официальный веб-сайт Microsoft IIS](https://www.iis.net/)
* [Библиотека технического содержимого по Windows Server](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>Ресурсы развертывания для администраторов IIS

* [Документация по службам IIS](/iis)
* [Начало работы с диспетчером IIS в IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Развертывание приложений .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

