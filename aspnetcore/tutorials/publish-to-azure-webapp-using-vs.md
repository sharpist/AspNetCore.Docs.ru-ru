---
title: Публикация приложения ASP.NET Core в Azure с помощью Visual Studio
author: rick-anderson
description: Сведения о публикации приложения ASP.NET Core в службе приложений Azure с помощью Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777089"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Публикация приложения ASP.NET Core в Azure с помощью Visual Studio

Автор: [Рик Андерсон](https://twitter.com/RickAndMSFT) (Rick Anderson)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


См. сведения о [публикации веб-приложения в Службе приложений Azure с помощью Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019).

Сведения об устранении проблем развертывания службы приложений см. в статье <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Настройка

* Создайте [бесплатную учетную запись Azure](https://azure.microsoft.com/free/dotnet/), если у вас ее нет. 

## <a name="create-a-web-app"></a>Создание веб-приложения

На начальной странице Visual Studio последовательно выберите **Файл > Создать > Проект…**

![меню "Файл"](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

В диалоговом окне **Создание проекта** выполните следующие действия.

* В левой области выберите **.NET Core**.
* В центральной области выберите **Веб-приложение ASP.NET Core**.
* Нажмите кнопку **ОК**.

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj.png)

В диалоговом окне **Создание веб-приложения ASP.NET Core** сделайте следующее.

* Выберите **Веб-приложение**.
* Выберите **Изменить способ проверки подлинности**.

![Диалоговое окно создания нового проекта](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Появится диалоговое окно **Изменение способа проверки подлинности**. 

* Выберите **Учетные записи отдельных пользователей**.
* Нажмите кнопку **ОК**, чтобы вернуться на страницу **Создание веб-приложения ASP.NET Core**, а затем снова нажмите кнопку **ОК**.

![Диалоговое окно "Новый способ веб-проверки подлинности ASP.NET Core"](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio создает решение.

## <a name="run-the-app"></a>Запуск приложения

* Нажмите клавиши CTRL+F5, чтобы запустить проект.
* Протестируйте ссылки **О программе** и **Контакт**.

![Веб-приложение откроется в localhost.](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Регистрация пользователя

* Выберите **Зарегистрироваться** и зарегистрируйте нового пользователя. Можно использовать вымышленный адрес электронной почты. После отправки на странице отображается следующая ошибка.

    *"Внутренняя ошибка сервера: не удалось выполнить операцию базы данных при обработке запроса. Исключение SQL: невозможно открыть базу данных. Применение имеющихся миграций для контекста базы данных приложения, возможно, позволит решить проблему."*
* Выберите **Применить миграции** и после обновления страницы перезагрузите ее.

![Внутренняя ошибка сервера: не удалось выполнить операцию базы данных при обработке запроса. Исключение SQL: невозможно открыть базу данных. Чтобы решить эту проблему, можно применить существующие миграции для контекста базы данных приложения.](publish-to-azure-webapp-using-vs/_static/mig.png)

Приложение отобразит адрес электронной почты, который использовался для регистрации нового пользователя, и ссылку **Выйти**.

![Веб-приложение откроется в Microsoft Edge. Ссылка "Зарегистрировать" заменяется текстом "Hello email@domain.com!"](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Развертывание приложения в Azure

В обозревателе решений щелкните правой кнопкой мыши проект и выберите команду **Опубликовать…**

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

В диалоговом окне **Публикация**:

* Выберите **Служба приложений Microsoft Azure**.
* Выберите значок шестеренки и затем пункт **Создать профиль**.
* Выберите **Создать профиль**.

![Диалоговое окно публикации](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Создание ресурсов Azure

Отображается диалоговое окно **Создание приложения службы**:

* Введите свою подписку.
* Заполняются поля ввода **Имя приложения**, **Группа ресурсов** и **План службы приложений**. Вы можете сохранить эти имена или изменить их.

![Диалоговое окно "Служба приложений"](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Выберите вкладку **Службы**, чтобы создать базу данных.

* Выберите зеленый значок **+** , чтобы создать базу данных SQL.

![Новая база данных SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Щелкните **Создать…** в диалоговом окне **Настройка базы данных SQL**, чтобы создать базу данных.

![Новый сервер и база данных SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

Появится диалоговое окно **Настройка SQL Server**.

* Введите имя пользователя и пароль администратора, а затем нажмите кнопку **ОК**. Вы можете сохранить **Имя сервера** по умолчанию. 

> [!NOTE]
> "admin" запрещено использовать в качестве имени пользователя администратора.

![Диалоговое окно "Настройка SQL Server"](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Нажмите кнопку **ОК**.

Visual Studio вернет диалоговое окно **Создание службы приложений**.

* Нажмите кнопку **Создать** в диалоговом окне **Создание службы приложений**.

![Диалоговое окно "Настройка базы данных SQL"](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio создает веб-приложение и SQL Server в Azure. Это может занять несколько минут. Сведения о созданных ресурсах см. в разделе [Дополнительные ресурсы](#additional-resources).

После завершения развертывания выберите **Параметры**:

![Диалоговое окно "Настройка SQL Server"](publish-to-azure-webapp-using-vs/_static/set.png)

На странице **Параметры** диалогового окна **Публикация**

* Разверните раздел **Базы данных** и установите флажок **Использовать эту строку подключения во время выполнения**.
* Разверните раздел **Миграции Entity Framework** и установите флажок **Использовать эту миграцию при публикации**.

* Нажмите кнопку **Сохранить**. Visual Studio вернется в диалоговое окно **Публикация**. 

![Диалоговое окно публикации: панель параметров](publish-to-azure-webapp-using-vs/_static/pubs.png)

Нажмите кнопку **Опубликовать**. Visual Studio публикует приложение в Azure. По завершении развертывания приложение открывается в браузере.

### <a name="test-your-app-in-azure"></a>Тестирование приложения в Azure

* Протестируйте ссылки **О программе** и **Контакт**.

* Регистрация нового пользователя

![Веб-приложение, открытое в Microsoft Edge в службе приложений Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Обновление приложения

* Измените страницу ** Pages/About.cshtmlRazor и ее содержимое. Например, вы можете изменить абзац на "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Щелкните правой кнопкой мыши проект и снова выберите пункт **Опубликовать...** .

![Открытое контекстное меню с выделенной ссылкой "Опубликовать"](publish-to-azure-webapp-using-vs/_static/pub.png)

* После публикации приложения убедитесь, что внесенные изменения доступны в Azure.

![Убедитесь, что задача завершена.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Очистка

Завершив тестирование приложения, перейдите на [портал Azure](https://portal.azure.com/) и удалите приложение.

* Выберите пункт **Группы ресурсов**, а затем созданную группу ресурсов.

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
