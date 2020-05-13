---
title: Модули IIS с ASP.NET Core
author: rick-anderson
description: Сведения об обнаружении активных и неактивных модулей IIS для приложения ASP.NET Core и управлении модулями IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 7262b9ea18e4cf6acd278d087fcc44262f8f9c80
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775951"
---
# <a name="iis-modules-with-aspnet-core"></a>Модули IIS с ASP.NET Core

Некоторые собственные модули IIS и все управляемые модули IIS не могут применяться для обработки запросов в приложениях ASP.NET Core. Во многих случаях сценарии, которые обрабатывают собственные и управляемые модули IIS, можно переложить на ASP.NET Core.

## <a name="native-modules"></a>Собственные модули

В этой таблице перечислены собственные модули IIS, которые работают с приложениями ASP.NET Core и модулем ASP.NET Core.

| Module | Доступность для приложений ASP.NET Core | Вариант ASP.NET Core |
| --- | :---: | --- |
| **Анонимная аутентификация**<br>`AnonymousAuthenticationModule`                                  | Да | |
| **Обычная проверка подлинности**<br>`BasicAuthenticationModule`                                          | Да | |
| **Аутентификация с сопоставлением сертификата клиента**<br>`CertificateMappingAuthenticationModule`      | Да | |
| **CGI**<br>`CgiModule`                                                                           | Нет  | |
| **Проверка конфигурации**<br>`ConfigurationValidationModule`                                  | Да | |
| **Ошибки HTTP**<br>`CustomErrorModule`                                                           | Нет  | [ПО промежуточного слоя для страниц кода состояния](xref:fundamentals/error-handling#usestatuscodepages) |
| **Настраиваемое ведение журнала**<br>`CustomLoggingModule`                                                      | Да | |
| **Документ по умолчанию**<br>`DefaultDocumentModule`                                                  | Нет  | [ПО промежуточного слоя для файлов по умолчанию](xref:fundamentals/static-files#serve-a-default-document) |
| **Дайджест-проверка подлинности**<br>`DigestAuthenticationModule`                                        | Да | |
| **Просмотр каталогов**<br>`DirectoryListingModule`                                               | Нет  | [ПО промежуточного слоя для просмотра каталогов](xref:fundamentals/static-files#enable-directory-browsing) |
| **Динамическое сжатие**<br>`DynamicCompressionModule`                                            | Да | [ПО промежуточного слоя для сжатия ответов](xref:performance/response-compression) |
| **Трассировка неудачно завершенных запросов**<br>`FailedRequestsTracingModule`                                     | Да | [Ведение журналов ASP.NET Core](xref:fundamentals/logging/index#tracesource-provider) |
| **Кэширование файлов**<br>`FileCacheModule`                                                            | Нет  | [ПО промежуточного слоя для кэширования ответов](xref:performance/caching/middleware) |
| **Кэширование HTTP**<br>`HttpCacheModule`                                                            | Нет  | [ПО промежуточного слоя для кэширования ответов](xref:performance/caching/middleware) |
| **Ведение журнала HTTP**<br>`HttpLoggingModule`                                                          | Да | [Ведение журналов ASP.NET Core](xref:fundamentals/logging/index) |
| **Перенаправление HTTP**<br>`HttpRedirectionModule`                                                  | Да | [ПО промежуточного слоя для переопределения URL-адресов](xref:fundamentals/url-rewriting) |
| **Трассировка HTTP**<br>`TracingModule`                                                              | Да | |
| **Аутентификация IIS с сопоставлением сертификата клиента**<br>`IISCertificateMappingAuthenticationModule` | Да | |
| **Ограничения IP-адресов и доменов**<br>`IpRestrictionModule`                                          | Да | |
| **Фильтры ISAPI**<br>`IsapiFilterModule`                                                         | Да | [ПО промежуточного слоя](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | Да | [ПО промежуточного слоя](xref:fundamentals/middleware/index) |
| **Поддержка протоколов**<br>`ProtocolSupportModule`                                                  | Да | |
| **Фильтрация запросов**<br>`RequestFilteringModule`                                                | Да | [ПО промежуточного слоя для переопределения URL-адресов`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Монитор запросов**<br>`RequestMonitorModule`                                                    | Да | |
| **Переопределение URL-адресов**&#8224;<br>`RewriteModule`                                                      | Да | [ПО промежуточного слоя для переопределения URL-адресов](xref:fundamentals/url-rewriting) |
| **Включения на стороне сервера**<br>`ServerSideIncludeModule`                                            | Нет  | |
| **Статическое сжатие**<br>`StaticCompressionModule`                                              | Нет  | [ПО промежуточного слоя для сжатия ответов](xref:performance/response-compression) |
| **Статическое содержимое**<br>`StaticFileModule`                                                         | Нет  | [ПО промежуточного слоя для статических файлов](xref:fundamentals/static-files) |
| **Кэшировании маркеров**<br>`TokenCacheModule`                                                          | Да | |
| **Кэширование URI**<br>`UriCacheModule`                                                              | Да | |
| **Авторизация URL-адреса**<br>`UrlAuthorizationModule`                                                | Да | [Идентификация ASP.NET Core](xref:security/authentication/identity) |
| **Проверка подлинности Windows**<br>`WindowsAuthenticationModule`                                      | Да | |

&#8224;В модуле переопределения URL-адресов типы сопоставления `isFile` и `isDirectory` не работают с приложениями ASP.NET Core из-за изменений в [структуре каталогов](xref:host-and-deploy/directory-structure).

## <a name="managed-modules"></a>Управляемые модули

Управляемые модули *не* работают с размещенными приложениями ASP.NET Core, если для пула приложения указана версия среды CLR .NET **Без управляемого кода**. Для некоторых случаев ASP.NET Core предлагает альтернативное ПО промежуточного слоя.

| Module                  | Вариант ASP.NET Core |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [ПО промежуточного слоя для аутентификации по файлам cookie](xref:security/authentication/cookie) |
| OutputCache             | [ПО промежуточного слоя для кэширования ответов](xref:performance/caching/middleware) |
| Профиль                 | |
| RoleManager             | |
| ScriptModule-4.0        | |
| Сеанс                 | [ПО промежуточного слоя для сеансов](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [ПО промежуточного слоя для переопределения URL-адресов](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>Изменения в приложении диспетчера IIS

Если для настройки параметров применяется диспетчер IIS, изменяется файл *web.config* для приложения. Если вы развертываете приложение с файлом *web.config*, все внесенные через диспетчер IIS изменения будут перезаписаны значениями из развертываемого файла *web.config*. Если вы вносите изменения в файл *web.config* на сервере, сразу же скопируйте обновленный файл *web.config* с сервера в локальный проект.

## <a name="disabling-iis-modules"></a>Отключение модулей IIS

Если на уровне сервера настроен модуль IIS, который нужно отключить для конкретного приложения, это можно сделать добавлением параметров в файл *web.config*. Здесь вы можете сохранить модуль, отключив его с помощью соответствующего параметра конфигурации (если поддерживается), либо полностью удалить модуль из приложения.

### <a name="module-deactivation"></a>Отключение модуля

Многие модули поддерживают параметр конфигурации, позволяющий отключить их без удаления модуля из приложения. Это самый простой и быстрый способ отключить отдельный модуль. Например, можно отключить модуль перенаправления HTTP с помощью элемента `<httpRedirect>` в файле *web.config*:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Дополнительные сведения об отключении модулей с помощью параметров конфигурации вы найдете по ссылкам в разделе о *дочерних элементах* в документации, посвященной [IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Удаление модуля

Если вы решите удалить модуль с помощью параметра в файле *web.config*, первым делом разблокируйте этот модуль и раздел `<modules>` в файле *web.config*:

1. Снятие блокировки модуля на уровне сервера. Выберите сервер IIS на боковой панели **Подключения** диспетчера IIS. Откройте элемент **Модули** в области **IIS**. Выберите нужный модуль из списка. На боковой панели **Действия** справа выберите **Разблокировать**. Если для модуля указано действие записи **Блокировать**, модуль уже разблокирован и ничего делать не нужно. Разблокируйте все модули, которые вы намерены удалить из файла *web.config*.

2. Разверните приложение без раздела `<modules>` в файле *web.config*. Если вы развертываете приложение, для которого в файле*web.config* есть раздел `<modules>`, но этот раздел не был ранее разблокирован в диспетчере IIS, Configuration Manager создаст исключение при попытке разблокировать этот раздел. Поэтому приложение нужно развертывать без раздела `<modules>`.

3. Разблокируйте раздел `<modules>` файла *web.config*. На боковой панели **Подключения** выберите веб-сайт в разделе **Сайты**. В области **Управление** откройте **Редактор конфигураций**. С помощью элементов навигации выберите раздел `system.webServer/modules`. На боковой панели **Действия** справа выберите действие **Разблокировать** для этого раздела. Если для раздела модуля указано действие записи **Блокировать раздел**, раздел модуля уже разблокирован и ничего делать не нужно.

4. Верните раздел `<modules>` в файл *web.config*, указав в нем элемент `<remove>` для удаления модуля из приложения. Добавьте несколько элементов `<remove>`, чтобы удалить несколько модулей. При любых изменениях в файле *web.config* на сервере сразу же вносите такие же изменения в файл *web.config* для проекта на локальном компьютере. Такой способ удаления модуля никак не влияет на использование модуля в других приложениях на этом сервере.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Чтобы добавить или удалить модули для IIS Express с помощью файла *web.config*, измените файл *applicationHost.config* для разблокировки раздела `<modules>`.

1. Откройте файл *{КОРЕНЬ ПРИЛОЖЕНИЯ}\\.vs\config\applicationhost.config*.

1. Найдите элемент `<section>` для модулей IIS и измените значение атрибута `overrideModeDefault` с `Deny` на `Allow`.

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Найдите раздел `<location path="" overrideMode="Allow"><system.webServer><modules>`. Для модулей, которые требуется удалить, измените значение `lockItem` с `true` на `false`. В следующем примере показана разблокировка модуля CGI.

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. После разблокировки раздела `<modules>` и отдельных модулей вы можете добавлять или удалять модули IIS с помощью файла *web.config* приложения для запуска приложения в IIS Express.

Также модуль IIS можно удалить с помощью *Appcmd.exe*. Для этого включите в команду `MODULE_NAME` и `APPLICATION_NAME`:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Например, так можно удалить `DynamicCompressionModule` с веб-сайта по умолчанию:

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Минимальный набор модулей

Для запуска приложения ASP.NET Core нужны только два модуля: модуль анонимной аутентификации и модуль ASP.NET Core.

Модуль кэширования URI (`UriCacheModule`) позволяет IIS кэшировать конфигурацию веб-сайта на уровне URL-адресов. Без этого модуля IIS придется заново считывать и анализировать конфигурацию при каждом новом запросе, даже если один URL-адрес запрашивается несколько раз подряд. Синтаксический анализ конфигурации при каждом запросе значительно снижает производительность системы. *Поэтому мы настоятельно рекомендуем включать модуль кэширования URI во все развертывания ASP.NET Core, хотя он и не является строго обязательным для запуска размещенного приложения ASP.NET Core.*

Модуль кэширования HTTP (`HttpCacheModule`) реализует кэш вывода служб IIS, а также логику кэширования элементов в кэше HTTP.sys. Без этого модуля содержимое не кэшируется в режиме ядра и все профили кэша игнорируются. Удаление модуля кэширования HTTP обычно крайне негативно влияет на производительность системы и потребление ресурсов. *Поэтому мы настоятельно рекомендуем включать модуль кэширования HTTP во все развертывания ASP.NET Core, хотя он и не является строго обязательным для запуска размещенного приложения ASP.NET Core.*

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Введение в архитектуры служб IIS. Модули в службах IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Общие сведения о модулях IIS](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Настройка ролей и модулей в IIS 7.0](https://technet.microsoft.com/library/cc627313.aspx)
* [Службы IIS \<system.webServer>](/iis/configuration/system.webServer/)
