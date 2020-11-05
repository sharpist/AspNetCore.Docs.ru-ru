---
title: Публикация приложения ASP.NET Core в Azure с помощью Visual Studio
author: rick-anderson
description: Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060226"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Публикация приложения ASP.NET Core в Azure с помощью Visual Studio

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


См. сведения о [публикации веб-приложения в Службе приложений Azure с помощью Visual Studio для Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019).

Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Настройка

* Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет. 

## <a name="create-a-web-app"></a>Создание веб-приложения

На начальной странице Visual Studio последовательно выберите **Файл > Создать > Проект…**

![меню "Файл"](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

В диалоговом окне **Создание проекта** выполните следующие действия.

* Выберите **Новое веб-приложение ASP.NET Core**.
* Выберите **Далее**.

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj.png)

В диалоговом окне **Создание веб-приложения ASP.NET Core** сделайте следующее.

* Выберите **Веб-приложение**.
* Выберите **Изменить** в разделе способа проверки подлинности.

![Диалоговое окно нового веб-приложения ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Появится диалоговое окно **Изменение способа проверки подлинности**. 

* Выберите **Учетные записи отдельных пользователей**.
* Нажмите кнопку **ОК** , чтобы вернуться на страницу **Создать веб-приложение ASP.NET Core** , и щелкните **Создать**.

![Диалоговое окно "Новый способ веб-проверки подлинности ASP.NET Core"](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio создает решение.

## <a name="run-the-app"></a>Запуск приложения

* Нажмите клавиши CTRL+F5, чтобы запустить проект.
* Прочтите, что написано по ссылке **Конфиденциальность**.

![Веб-приложение откроется в localhost.](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Регистрация пользователя

* Выберите **Зарегистрироваться** и зарегистрируйте нового пользователя. Можно использовать вымышленный адрес электронной почты. После отправки на странице отображается следующая ошибка.

    *Не удалось выполнить операцию базы данных при обработке запроса. Применение имеющихся миграций для контекста базы данных приложения, возможно, позволит решить проблему."*
* Выберите **Применить миграции** и после обновления страницы перезагрузите ее.

![не удалось выполнить операцию базы данных при обработке запроса. Чтобы решить эту проблему, можно применить существующие миграции для контекста базы данных приложения.](publish-to-azure-webapp-using-vs/_static/mig.png)

Приложение отобразит адрес электронной почты, который использовался для регистрации нового пользователя, и ссылку **Выйти**.

![Веб-приложение откроется в Microsoft Edge. Ссылка "Зарегистрировать" заменяется текстом "Hello user1@example.com!"](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Развертывание приложения в Azure

В обозревателе решений щелкните правой кнопкой мыши проект и выберите команду **Опубликовать…**

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

В диалоговом окне **Публикация** :

* Выберите **Azure**.
* Выберите **Далее**.

![Диалоговое окно публикации](publish-to-azure-webapp-using-vs/_static/maas1.png)

В диалоговом окне **Публикация** :

* Выберите **Служба приложений Azure (Linux)** .
* Выберите **Далее**.

![Диалоговое окно публикации: выбор службы Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

В диалоговом окне **Публикация** выберите вариант **Create a new Azure App Service...** (Создать экземпляр Службы приложений Azure).

![Диалоговое окно публикации: выбор экземпляра службы Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

Отображается диалоговое окно **Создание приложения службы** :

* Заполняются поля ввода **Имя приложения** , **Группа ресурсов** и **План службы приложений**. Вы можете сохранить эти имена или изменить их.
* Выберите **Создать**.

![Диалоговое окно "Создание службы приложений"](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Публикация**.

* Созданный экземпляр выбирается автоматически.
* Нажмите кнопку **Готово**.

![Диалоговое окно публикации: выбор экземпляра Службы приложений](publish-to-azure-webapp-using-vs/_static/select_as.png)

Затем отобразится страница **Сводка** для профиля публикации. Программа Visual Studio обнаружила, что для этого приложения требуется база данных SQL Server, и предлагает вам настроить ее. Нажмите кнопку **Настроить**.

![Страница со сводными данными о профиле публикации: настройка зависимости SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

Откроется диалоговое окно **Configure dependency** (Настройка зависимостей).

* Выберите **База данных SQL Azure**.
* Выберите **Далее**.

![Диалоговое окно для настройки зависимости SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

В диалоговом окне **Configure Azure SQL Database** (Настройка Базы данных SQL Azure) выберите **Создать базу данных SQL**.

![Диалоговое окно настройки Базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql2.png)

Появится окно **Create Azure SQL Database** (Создание базы данных SQL Azure).

* В нем уже заполнены поля **Имя базы данных** , **Группа ресурсов** , **Сервер базы данных** и **План службы приложений**. Вы можете сохранить эти значения или изменить их.
* Заполните поля **Database administrator username** (Имя администратора базы данных) и **Пароль администратора базы данных** для выбранного параметра **Сервер базы данных** . (Обратите внимание, что у используемой учетной записи должны быть разрешения на создание базы данных SQL Azure).
* Выберите **Создать**.

![Диалоговое окно создания базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Как только создание завершится, диалоговое окно автоматически закроется и снова активируется окно **Configure Azure SQL Database** (Настройка базы данных SQL Azure).

* Созданный экземпляр выбирается автоматически.
* Выберите **Далее**.

![Диалоговое окно настройки Базы данных SQL Azure](publish-to-azure-webapp-using-vs/_static/sql_select.png)

На следующем шаге в диалоговом окне **Configure Azure SQL Database** (Настройка Базы данных SQL Azure) сделайте следующее:

* Заполните поля **Database connection user name** (Имя пользователя для подключения к базе данных) и **Database connection password** (Пароль для подключения к базе данных). Эти сведения ваше приложение будет использовать при подключении к базе данных во время выполнения. Рекомендуется не указывать такое же имя администратора и пароль, которые вы использовали на предыдущем шаге.
* Нажмите кнопку **Готово**.

![Диалоговое окно с настройками Базы данных SQL Azure, сведения о строке подключения](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

На странице **Сводка** для профиля публикации выберите **Параметры**.

![Страница со сводными данными о профиле публикации: выбор параметров](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

На странице **Параметры** диалогового окна **Публикация**

* Разверните раздел **Базы данных** и установите флажок **Использовать эту строку подключения во время выполнения**.
* Разверните раздел **Миграции Entity Framework** и установите флажок **Использовать эту миграцию при публикации**.

* Нажмите кнопку **Сохранить**. Visual Studio вернется в диалоговое окно **Публикация**. 

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Нажмите кнопку **Опубликовать**. Visual Studio публикует приложение в Azure. По завершении развертывания приложение открывается в браузере.

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Обновление приложения

* Измените страницу Razor *Pages/Index.cshtml* и ее содержимое. Например, вы можете изменить абзац на "Hello ASP.NET Core!":

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* На странице **Сводка** для профиля публикации снова нажмите кнопку **Опубликовать**.

![Страница со сводными данными о профиле публикации](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* После публикации приложения убедитесь, что внесенные изменения доступны в Azure.

![Убедитесь, что задача завершена.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Очистка

Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.

* Выберите пункт **Группы ресурсов** , а затем созданную группу ресурсов.

![Портал Azure: "Группы ресурсов" в меню боковой панели](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* На странице **Группы ресурсов** выберите **Удалить**.

![Портал Azure: страница "Группы ресурсов"](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Введите имя группы ресурсов и выберите **Удалить**. Ваше приложение и все ресурсы, созданные при работе с этим руководством, удалены из Azure.

### <a name="next-steps"></a>Следующие шаги

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Дополнительные ресурсы

* Сведения о Visual Studio Code см. в разделе [Профили публикации](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [служба приложений Azure](/azure/app-service/app-service-web-overview);
* [Группа ресурсов Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [База данных SQL Azure](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>