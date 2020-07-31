---
title: Настройка проверки подлинности Windows в ASP.NET Core
author: scottaddie
description: Узнайте, как настроить проверку подлинности Windows в ASP.NET Core для IIS и HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/windowsauth
ms.openlocfilehash: 8f6dc8620df04bcebe996119869ca2e498cffccc
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/26/2020
ms.locfileid: "87330692"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Настройка проверки подлинности Windows в ASP.NET Core

Автор: [Скотт Адди](https://twitter.com/Scott_Addie) (Scott Addie)

::: moniker range=">= aspnetcore-3.0"

Проверка подлинности Windows (также известная как согласование, Kerberos или проверка подлинности NTLM) может быть настроена для ASP.NET Core приложений, размещенных в [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel)или [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Проверка подлинности Windows (также известная как Negotiate, Kerberos или NTLM) может быть настроена для ASP.NET Core приложений, размещенных в [IIS](xref:host-and-deploy/iis/index) или [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

Проверка подлинности Windows полагается на операционную систему для проверки подлинности пользователей ASP.NET Core приложений. Проверку подлинности Windows можно использовать, когда сервер работает в корпоративной сети, используя удостоверения домена Active Directory или учетные записи Windows для идентификации пользователей. Проверка подлинности Windows лучше всего подходит для сред интрасети, где пользователи, клиентские приложения и веб-серверы принадлежат одному и тому же домену Windows.

> [!NOTE]
> Проверка подлинности Windows не поддерживается HTTP/2. Проблемы проверки подлинности могут отправляться в ответах HTTP/2, но перед проверкой подлинности клиент должен перейти на HTTP/1.1.

## <a name="proxy-and-load-balancer-scenarios"></a>Сценарии прокси-сервера и подсистемы балансировки нагрузки

Проверка подлинности Windows — это сценарий с отслеживанием состояния, который в основном используется в интрасети, где прокси-сервер или балансировщик нагрузки обычно не обрабатывал трафик между клиентами и серверами. Если используется прокси-сервер или балансировщик нагрузки, проверка подлинности Windows работает только в том случае, если прокси-сервер или балансировщик нагрузки:

* Обрабатывает проверку подлинности.
* Передает сведения о проверке подлинности пользователя в приложение (например, в заголовке запроса), которое действует на данные проверки подлинности.

Альтернативой проверки подлинности Windows в средах, где используются прокси-серверы и подсистемы балансировки нагрузки, — Active Directory Федеративные службы (ADFS) с OpenID Connect Connect (OIDC).

## <a name="iisiis-express"></a>IIS/IIS Express

Добавьте службы проверки подлинности, вызвав <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> пространство имен) в `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Параметры запуска (отладчик)

Настройка параметров запуска влияет только на *Свойства и launchSettings.jsв* файле для IIS Express и не НАСТРАИВАЕТ службы IIS для проверки подлинности Windows. Конфигурация сервера описывается в разделе [IIS](#iis) .

Шаблон **веб-приложения** , доступный через Visual Studio или .NET Core CLI можно настроить для поддержки проверки подлинности Windows, который автоматически обновляет *Свойства и launchSettings.jsв* файле.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Новый проект**

1. Создайте новый проект.
1. Выберите **Новое веб-приложение ASP.NET Core**. Выберите **Далее**.
1. Введите имя в поле **имя проекта** . Убедитесь, что для проекта правильно указано существующее **расположение** или укажите новое. Нажмите кнопку **создания**.
1. В разделе **Проверка подлинности**выберите **изменить** .
1. В окне **Изменение проверки подлинности** выберите **Проверка подлинности Windows**. Щелкните **ОК**.
1. Выберите **Веб-приложение**.
1. Нажмите кнопку **создания**.

Запустите приложение. Имя пользователя отображается в пользовательском интерфейсе отображаемого приложения.

**Существующий проект**

Свойства проекта включают проверку подлинности Windows и отключение анонимной проверки подлинности:

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите пункт **Свойства**.
1. Выберите вкладку **Отладка**.
1. Снимите флажок **включить анонимную проверку подлинности**.
1. Установите флажок **включить проверку подлинности Windows**.
1. Сохраните и закройте страницу свойств.

Кроме того, свойства можно настроить в `iisSettings` узле *launchSettings.jsв* файле:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-cli"></a>[Интерфейс командной строки .NET Core](#tab/netcore-cli)

**Новый проект**

Выполните команду [DotNet New](/dotnet/core/tools/dotnet-new) с `webapp` аргументом (ASP.NET Core веб-приложение) и `--auth Windows` параметром:

```dotnetcli
dotnet new webapp --auth Windows
```

**Существующий проект**

Обновите `iisSettings` узел *launchSettings.jsв* файле:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

При изменении существующего проекта убедитесь, что файл проекта содержит ссылку на пакет для пакета NuGet [Microsoft. AspNetCore. app метапакет](xref:fundamentals/metapackage-app) **или** [Microsoft. AspNetCore. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) .

### <a name="iis"></a>IIS

Службы IIS используют [модуль ASP.NET Core](xref:host-and-deploy/aspnet-core-module) для размещения ASP.NET Core приложений. Проверка подлинности Windows настраивается для служб IIS с помощью файла *web.config* . В следующих разделах показано, как выполнить следующие задачи:

* Укажите локальный файл *web.config* , который активирует проверку подлинности Windows на сервере при развертывании приложения.
* Используйте диспетчер служб IIS для настройки файла *web.config* ASP.NET Core приложения, которое уже развернуто на сервере.

Если вы еще не сделали этого, включите IIS для размещения ASP.NET Core приложений. Для получения дополнительной информации см. <xref:host-and-deploy/iis/index>.

Включите службу роли IIS для проверки подлинности Windows. Дополнительные сведения см. [в разделе Включение проверки подлинности Windows в службах РОЛЕЙ IIS (см. шаг 2)](xref:host-and-deploy/iis/index#iis-configuration).

По умолчанию по [промежуточного слоя интеграции IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) настроено на автоматическую проверку подлинности запросов. Дополнительные сведения см. [в разделе Host ASP.NET Core в Windows с IIS: параметры IIS (аутоматикаусентикатион)](xref:host-and-deploy/iis/index#iis-options).

Модуль ASP.NET Core настроен на пересылку маркера проверки подлинности Windows в приложение по умолчанию. Дополнительные сведения см. в разделе [Справочник по конфигурации модуля ASP.NET Core: атрибуты элемента aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Используйте **один** из следующих подходов:

* **Перед публикацией и развертыванием проекта** добавьте следующий файл *web.config* в корневой каталог проекта:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Когда проект публикуется пакет SDK для .NET Core (без `<IsTransformWebConfigDisabled>` свойства, установленного `true` в файле проекта), опубликованный файл *web.config* включает `<location><system.webServer><security><authentication>` раздел. Дополнительные сведения о `<IsTransformWebConfigDisabled>` свойстве см. в разделе <xref:host-and-deploy/iis/index#webconfig-file> .

* **После публикации и развертывания проекта** выполните настройку на стороне сервера с помощью диспетчера IIS.

  1. В диспетчере служб IIS выберите сайт IIS в узле **сайты** на боковой панели **подключения** .
  1. Дважды щелкните **Проверка подлинности** в области **IIS** .
  1. Выберите **Анонимная проверка подлинности**. На боковой панели **действия** выберите **Отключить** .
  1. Выберите параметр **Проверка подлинности Windows**. Выберите **включить** на боковой панели **действия** .

  Когда выполняются эти действия, Диспетчер IIS изменяет файл *web.config* приложения. `<system.webServer><security><authentication>`Узел добавляется с обновленными параметрами для `anonymousAuthentication` и `windowsAuthentication` :

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>`Раздел, добавленный в файл *web.config* диспетчером IIS, находится за пределами раздела приложения, `<location>` добавленного пакет SDK для .NET Core при публикации приложения. Так как раздел добавляется за пределы `<location>` узла, параметры наследуются любыми [вложенными приложениями](xref:host-and-deploy/iis/index#sub-applications) для текущего приложения. Чтобы предотвратить наследование, переместите добавленный `<security>` раздел в `<location><system.webServer>` раздел, указанный пакет SDK для .NET Core.

  Когда диспетчер IIS используется для добавления конфигурации IIS, он влияет только на файл *web.config* приложения на сервере. Последующее развертывание приложения может перезаписать параметры на сервере, если копия *web.config* сервера заменяется *web.configным* файлом проекта. Для управления параметрами используйте **один** из следующих подходов.

  * Используйте диспетчер IIS для сброса параметров в файле *web.config* после того, как файл будет перезаписан при развертывании.
  * Добавьте *файлweb.config* в приложение локально с параметрами.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

Пакет NuGet [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) можно использовать с [Kestrel](xref:fundamentals/servers/kestrel) для поддержки проверки подлинности Windows с помощью Negotiate и Kerberos в Windows, Linux и macOS.

> [!WARNING]
> Учетные данные могут быть сохранены в запросах к соединению. *Проверка подлинности Negotiate не должна использоваться с прокси, если только прокси-сервер не поддерживает сопоставление соединения 1:1 (постоянное подключение) с Kestrel.*

> [!NOTE]
> Обработчик согласования определяет, поддерживает ли базовый сервер встроенную проверку подлинности Windows и включен ли он. Если сервер поддерживает проверку подлинности Windows, но отключен, выдается сообщение об ошибке, предлагающее включить серверную реализацию. Если на сервере включена проверка подлинности Windows, обработчик согласования прозрачно пересылает его.

Добавьте службы проверки подлинности, вызвав <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> и <xref:Microsoft.Extensions.DependencyInjection.NegotiateExtensions.AddNegotiate*> в `Startup.ConfigureServices` :

 ```csharp
// using Microsoft.AspNetCore.Authentication.Negotiate;
// using Microsoft.Extensions.DependencyInjection;

services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Добавьте по промежуточного слоя проверки подлинности, вызвав <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> в `Startup.Configure` :

 ```csharp
app.UseAuthentication();
```

Дополнительные сведения о по промежуточного слоя см. в разделе <xref:fundamentals/middleware/index> .

Анонимные запросы разрешены. Используйте [ASP.NET Coreную авторизацию](xref:security/authorization/introduction) для запроса анонимных запросов на проверку подлинности.

### <a name="windows-environment-configuration"></a>Конфигурация среды Windows

Компонент [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) выполняет проверку подлинности пользователя в пользовательском режиме. Имена участников-служб (SPN) должны быть добавлены в учетную запись пользователя, запускающего службу, а не в учетную запись компьютера. Выполните `setspn -S HTTP/myservername.mydomain.com myuser` команду в административной командной консоли.

### <a name="linux-and-macos-environment-configuration"></a>Конфигурация среды Linux и macOS

Инструкции по присоединению компьютера Linux или macOS к домену Windows доступны в статье [подключение Azure Data Studio к SQL Server с помощью проверки подлинности Windows — Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) . Эти инструкции создают учетную запись компьютера для компьютера Linux в домене. Имена участников-служб должны быть добавлены в эту учетную запись компьютера.

> [!NOTE]
> При выполнении рекомендаций, приведенных в разделе [подключение Azure Data Studio к SQL Server с помощью проверки подлинности Windows — Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) , замените `python-software-properties` на `python3-software-properties` при необходимости.

После присоединения компьютера Linux или macOS к домену необходимо выполнить дополнительные действия, чтобы предоставить keytab- [файл](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) с именами участников-служб:

* На контроллере домена добавьте новые имена SPN веб-службы в учетную запись компьютера:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Используйте [ктпасс](/windows-server/administration/windows-commands/ktpass) для создания файла keytab:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Некоторые поля должны быть указаны в верхнем регистре, как указано.
* Скопируйте файл keytab на компьютер Linux или macOS.
* Выберите файл keytab с помощью переменной среды:`export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Вызовите `klist` , чтобы отобразить имена участников-служб, доступные в настоящее время для использования.

> [!NOTE]
> Файл keytab содержит учетные данные доступа к домену и должен быть защищен соответствующим образом.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) поддерживает проверку подлинности Windows в режиме ядра с помощью Negotiate, NTLM или обычной проверки подлинности.

Добавьте службы проверки подлинности, вызвав <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> ( <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> пространство имен) в `Startup.ConfigureServices` :

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Настройте веб-узел приложения для использования HTTP.sys с проверкой подлинности Windows (*Program.CS*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> находится в пространстве имен <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName>.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys делегирует задачи в проверку подлинности в режиме ядра с помощью протокола проверки подлинности Kerberos. Проверка подлинности в режиме пользователя не поддерживается с Kerberos и HTTP.sys. Необходимо использовать учетную запись компьютера для расшифровки маркера/билета Kerberos, полученного из Active Directory и переадресованного клиентом на сервер для проверки подлинности пользователя. Зарегистрируйте имя субъекта-службы (SPN) для узла, а не пользователя приложения.

> [!NOTE]
> HTTP.sys не поддерживается на сервере Nano Server версии 1709 или более поздней. Чтобы использовать проверку подлинности Windows и HTTP.sys с Nano Server, используйте [контейнер Server Core (Microsoft/windowsservercore)](https://hub.docker.com/r/microsoft/windowsservercore/). Дополнительные сведения о Server Core см. в разделе [что такое вариант установки Server Core в Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Авторизация пользователей

Состояние конфигурации анонимного доступа определяет способ `[Authorize]` `[AllowAnonymous]` использования атрибутов и в приложении. В следующих двух разделах объясняется, как управлять состояниями запрещенных и разрешенных конфигураций анонимного доступа.

### <a name="disallow-anonymous-access"></a>Запретить анонимный доступ

Если включена проверка подлинности Windows, а анонимный доступ отключен, `[Authorize]` `[AllowAnonymous]` атрибуты и не действуют. Если сайт IIS настроен для запрета анонимного доступа, запрос никогда не достигнет приложения. По этой причине `[AllowAnonymous]` атрибут неприменим.

### <a name="allow-anonymous-access"></a>Разрешить анонимный доступ

Если включена проверка подлинности Windows и анонимный доступ, `[Authorize]` Используйте `[AllowAnonymous]` атрибуты и. `[Authorize]`Атрибут позволяет защитить конечные точки приложения, требующие проверки подлинности. `[AllowAnonymous]`Атрибут переопределяет `[Authorize]` атрибут в приложениях, разрешающих анонимный доступ. Сведения об использовании атрибутов см. в разделе <xref:security/authorization/simple> .

> [!NOTE]
> По умолчанию пользователям, у которых отсутствует авторизация на доступ к странице, предоставляется пустой ответ HTTP 403. По [промежуточного слоя статускодепажес](xref:fundamentals/error-handling#usestatuscodepages) можно настроить для предоставления пользователям более качественного интерфейса "отказ в доступе".

## <a name="impersonation"></a>Олицетворение

ASP.NET Core не реализует олицетворение. Приложения запускаются с удостоверением приложения для всех запросов с использованием пула приложений или удостоверения процесса. Если приложение должно выполнить действие от имени пользователя, используйте [WindowsIdentity. рунимперсонатед](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) во встроенном по [промежуточного слоя терминала](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) в `Startup.Configure` . Выполните одно действие в этом контексте и закройте контекст.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated`не поддерживает асинхронные операции и не следует использовать для сложных сценариев. Например, не поддерживается или не рекомендуется заключать в оболочку все запросы или цепочки промежуточного слоя.

::: moniker range=">= aspnetcore-3.0"

Хотя пакет [Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) включает проверку подлинности в Windows, Linux и macOS, олицетворение поддерживается только в Windows.

::: moniker-end

## <a name="claims-transformations"></a>Преобразования утверждений

::: moniker range=">= aspnetcore-3.0"

При размещении с IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутренне для инициализации пользователя. Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию. Дополнительные сведения и пример кода, который активирует преобразования утверждений, см. в разделе <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

При размещении в режиме обработки IIS <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> не вызывается внутренним образом для инициализации пользователя. Таким образом, реализация <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, используемая для преобразования утверждений после каждой проверки подлинности, не активируется по умолчанию. Дополнительные сведения и пример кода, который активирует преобразования утверждений при размещении внутри процесса, см. в разделе <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model> .

::: moniker-end

## <a name="additional-resources"></a>Дополнительные ресурсы

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
