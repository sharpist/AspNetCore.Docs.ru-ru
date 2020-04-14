---
title: Поставщик конфигурации клавиатуры Azure в ASP.NET Core
author: rick-anderson
description: Узнайте, как использовать поставщика конфигурации клавиатур Azure Key Vault для настройки приложения с использованием пар именных значений, загруженных во время выполнения.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: security/key-vault-configuration
ms.openlocfilehash: d78584eaa3fcd50425698e4db581060b6ca845f8
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228170"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a>Поставщик конфигурации клавиатуры Azure в ASP.NET Core

[Эндрю Стэнтон-Медсестра](https://github.com/anurse)

::: moniker range=">= aspnetcore-3.0"

В этом документе объясняется, как использовать поставщика конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложений из секретов Azure Key Vault. Azure Key Vault — это облачный сервис, который помогает охранять криптографические ключи и секреты, используемые приложениями и службами. Общие сценарии использования Azure Key Vault с ASP.NET основными приложениями включают:

* Контроль доступа к конфиденциальным данным конфигурации.
* Соответствие требованию fiPS 140-2 Уровень 2 проверенных модулей аппаратной безопасности (HSM) при хранении данных конфигурации.

[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(как скачать)](xref:index#how-to-download-a-sample)

## <a name="packages"></a>Пакеты

Добавьте ссылку на пакет [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Пример приложения

Пример приложения работает в любом из двух `#define` режимов, определяемых заявлением в верхней части *файла Program.cs:*

* `Certificate`&ndash; Демонстрирует использование идентификатора клиента Azure Key Vault и сертификата X.509 для доступа к секретам, хранящимся в Azure Key Vault. Эта версия образца может быть запущена из любого места, развернута в службе приложений Azure или любого узла, способного обслуживать ASP.NET приложение Core.
* `Managed`&ndash; Демонстрирует, как использовать [управляемые идентификаторы для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для аутентификации приложения в Хранилище ключей Azure с аутентификацией Azure AD без учетных данных, хранящихся в коде или конфигурации приложения. При использовании управляемых идентификационных данных для проверки подлинности идентификатор приложения Azure AD и пароль (Client Secret) не требуются. Версия `Managed` образца должна быть развернута в Azure. Следуйте инструкциям в разделе [«Использование управляемых идентификаторов» для ресурсов Azure.](#use-managed-identities-for-azure-resources)

Для получения дополнительной информации о том, как настроить`#define`образец приложения с помощью препроцессорных директив ( , см. <xref:index#preprocessor-directives-in-sample-code>

## <a name="secret-storage-in-the-development-environment"></a>Секретное хранилище в среде разработки

Установите секреты локально с помощью [инструмента Secret Manager.](xref:security/app-secrets) Когда пример приложения работает на локальной машине в среде разработки, секреты загружаются из местного магазина Secret Manager.

Инструмент «Секретный `<UserSecretsId>` менеджер» требует свойства в файле проекта приложения. Установите значение`{GUID}`свойства ( ) на любой уникальный GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Секреты создаются как пары имено-ценности. Иерархические значения (разделы конфигурации) используют a `:` (colon) в качестве сепаратора в ASP.NET ключевых имен [конфигурации Core.](xref:fundamentals/configuration/index)

Секретный менеджер используется из командной оболочки, открытой для `{SECRET NAME}` [корня содержимого](xref:fundamentals/index#content-root)проекта, где находится имя и `{SECRET VALUE}` значение:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Выполните следующие команды в командной оболочке из [корня содержимого](xref:fundamentals/index#content-root) проекта, чтобы установить секреты для примера приложения:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Когда эти секреты хранятся в Хранилище ключей Azure в [секретном хранилище в производственной среде с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault, `_dev` суффикс изменяется на `_prod`. Суффикс обеспечивает визуальный сигнал на выходе приложения с указанием источника значений конфигурации.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Секретное хранилище в производственной среде с Azure Key Vault

Инструкции, предоставленные [quickstart: Установить и получить секрет из Azure Key Vault с помощью темы Azure CLI,](/azure/key-vault/quick-create-cli) суммируются здесь для создания Убежища ключей Azure и хранения секретов, используемых в примере приложения. Для более подробной информации обратитесь к теме.

1. Откройте оболочку облака Azure, используя любой из следующих методов [на портале Azure:](https://portal.azure.com/)

   * Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. Используйте строку поиска "Azure CLI" в текстовом поле.
   * Откройте облачную оболочку в браузере с помощью кнопки **Launch Cloud Shell.**
   * Выберите кнопку **Cloud Shell** в меню в правом верхнем углу портала Azure.

   Для получения дополнительной информации смотрите [обзор Azure CLI](/cli/azure/) и [обзор облачной оболочки Azure.](/azure/cloud-shell/overview)

1. Если вы еще не проверены, вопийте команду. `az login`

1. Создайте группу ресурсов со `{RESOURCE GROUP NAME}` следующей командой, где находится `{LOCATION}` название группы ресурсов для новой группы ресурсов и является областью Azure (центр обработки данных):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Создайте хранилище ключей в группе ресурсов `{KEY VAULT NAME}` со следующей командой, `{LOCATION}` где находится название нового хранилища ключей и является областью Azure (центр обработки данных):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Создавайте секреты в хранилище ключей в виде пар именной ценности.

   Секретные имена Azure Key Vault ограничены буквенно-цифровыми персонажами и тире. Иерархические значения (разделы `--` конфигурации) используют (два тире) в качестве сепаратора. Колоны, которые обычно используются для разграничения раздела из подключаемых ASP.NET [конфигурации Core,](xref:fundamentals/configuration/index)не допускаются в секретных именах хранилища ключей. Таким образом, два тире используются и обмениваются на толстую кишку, когда секреты загружаются в конфигурацию приложения.

   Следующие секреты для использования с образцом приложения. Значения включают `_prod` суффикс, чтобы отличить их от `_dev` значений суффикса, загруженных в среде разработки, от пользовательских секретов. Замените `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем этапе:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Идентификатор приложений и сертификат X.509 для приложений, не хозяек, размещенных в Azure

Напередите Azure AD, Azure Key Vault и приложение для использования идентификатора приложений Active Directory Azure и сертификата X.509 для проверки подлинности хранилища ключей **при размещении приложения за пределами Azure.** См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Хотя использование идентификатора приложений и сертификата X.509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые идентификаторы для ресурсов Azure](#use-managed-identities-for-azure-resources) при хостинге приложения в Azure. Управляемые идентификаторы не требуют хранения сертификата в приложении или в среде разработки.

В примере приложения используется идентификатор приложения `#define` и сертификат X.509, `Certificate`когда заявление в верхней части *Program.cs* файла установлено.

1. Создайте сертификат PKCS-12 *(.pfx)* сертификат. Варианты создания сертификатов включают [MakeCert на Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL.](https://www.openssl.org/)
1. Установите сертификат в личный магазин сертификатов текущего пользователя. Маркировка ключа как экспортируемого не является обязательной. Обратите внимание на отпечаток пальца сертификата, который используется позже в этом процессе.
1. Экспорт архива PKCS-12 (*.pfx*) сертификат в качестве DER-кодированного сертификата (*.cer*).
1. Зарегистрируйте приложение с Помощью Azure AD **(регистрация приложений).**
1. Загрузите закодированный сертификат DER *(.cer)* в Azure AD:
   1. Выберите приложение в Azure AD.
   1. Перейдите к **сертификатам & секреты**.
   1. Выберите **сертификат загрузки** для загрузки сертификата, который содержит общедоступный ключ. Сертификат *.cer*, *.pem*или *.crt* является приемлемым.
1. Храните имя хранилища ключей, идентификатор приложения и отпечаток пальца сертификата в файле *appsettings.json* приложения.
1. Перейдите к **хранилищам ключей** на портале Azure.
1. Выберите хранилище ключей, созданное в [секретном хранилище в среде Production, с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault.
1. Выберите **политики доступа.**
1. Выберите **политику добавления доступа.**
1. Открытые **секретные разрешения** и предоставить приложение с **получить** и **список** разрешений.
1. Выберите **основной принцип** и выберите зарегистрированное приложение по имени. Нажмите кнопку **Выбрать**.
1. Щелкните **ОК**.
1. Щелкните **Сохранить**.
1. Разверните приложение.

Пример `Certificate` приложения получает значения конфигурации `IConfigurationRoot` с тем же именем, что и секретное имя:

* Неиерархические значения: Значение для `SecretName` получено `config["SecretName"]`с .
* Иерархические значения (разделы): Используйте `:` (колонию) обозначения или метод `GetSection` расширения. Используйте любой из этих подходов для получения значения конфигурации:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Сертификат X.509 управляется ОС. Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> значения, поставляемые файлом *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Примеры значений:

* Имя хранилища ключа:`contosovault`
* Идентификатор приложения:`627e911e-43cc-61d4-992e-12db9c81b413`
* Отпечаток пальца сертификата:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

При запуске приложения на веб-странице отображаются загруженные секретные значения. В среде разработки секретные `_dev` значения загружаются суффиксом. В производственной среде значения загружаются суффиксом. `_prod`

## <a name="use-managed-identities-for-azure-resources"></a>Используйте управляемые идентификаторы для ресурсов Azure

**Приложение, развернутое в Azure,** может воспользоваться [управляемыми идентификаторами для ресурсов Azure,](/azure/active-directory/managed-identities-azure-resources/overview)что позволяет приложению аутентифицировать сяддение с помощью Azure Key Vault с помощью аутентификации Azure AD без учетных данных (Идентификатор приложения и пароль/клиентсекрет), хранящийся в приложении.

Приложение образец использует управляемые идентификаторы для ресурсов Azure `Managed`при установке `#define` оператора в верхней части файла *Program.cs.*

Введите имя хранилища в файл *appsettings.json* приложения. Пример приложения не требует идентификатора приложения и пароль `Managed` (Client Secret) при установке на версию, так что вы можете игнорировать эти настройки. Приложение развернуто в Azure, и Azure проверяет подлинность приложения для доступа к Azure Key Vault только с помощью имени хранилища, хранящегося в файле *appsettings.json.*

Развертывание примера приложения в службу приложений Azure.

Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы. Получите идентификатор объекта из развертывания для использования в следующей команде. Идентификатор объекта отображается на портале Azure на панели **идентификационных** данных Службы app.

Используя Azure CLI и идентификатор объекта `list` `get` приложения, предоставьте приложение и разрешения на доступ к хранилищу ключей:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Перезапустите приложение** с помощью Azure CLI, PowerShell или портала Azure.

Пример приложения:

* Создает экземпляр `AzureServiceTokenProvider` класса без строки соединения. Когда строка соединения не предоставлена, провайдер пытается получить токен доступа из управляемых идентификаторов для ресурсов Azure.
* Новый <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается `AzureServiceTokenProvider` с обратным вызовом маркера экземпляра.
* Экземпляр <xref:Microsoft.Azure.KeyVault.KeyVaultClient> используется с реализацией <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> по умолчанию, которая загружает все`--`секретные значения`:`и заменяет двойные капли () с толстой кишки ( ) в ключевых имен.

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Значение примера имени хранилища ключей:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

При запуске приложения на веб-странице отображаются загруженные секретные значения. В среде разработки секретные `_dev` значения имеют суффикс, потому что они предоставляются секретами пользователя. В рабочей среде значения загружаются `_prod` суффиксом, поскольку они предоставляются Azure Key Vault.

Если вы `Access denied` получили ошибку, подтвердите, что приложение зарегистрировано в Azure AD и предоставляет доступ к хранилищу ключей. Подтвердите, что вы перезапустили службу в Azure.

Для получения информации об использовании поставщика с управляемой идентификацией и конвейером Azure DevOps [см.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="configuration-options"></a>Варианты настройки

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>может <xref:Microsoft.Extensions.Configuration.AzureKeyVault.AzureKeyVaultConfigurationOptions>принять:

```csharp
config.AddAzureKeyVault(
    new AzureKeyVaultConfigurationOptions()
    {
        ...
    });
```

| Свойство         | Описание |
| ---------------- | ----------- |
| `Client`         | <xref:Microsoft.Azure.KeyVault.KeyVaultClient>использовать для извлечения значений. |
| `Manager`        | <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>экземпляр, используемый для управления секретной загрузкой. |
| `ReloadInterval` | `Timespan`ждать между попытками опроса хранилища ключей для изменений. Значение по `null` умолчанию (конфигурация не перезагружается). |
| `Vault`          | Хранилище ключей URI. |

## <a name="use-a-key-name-prefix"></a>Используйте префикс ключевого имени

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>обеспечивает <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>перегрузку, которая принимает реализацию, которая позволяет контролировать, как секреты хранилища ключей преобразуются в клавиши конфигурации. Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, предоставляемого при запуске приложения. Это позволяет, например, загружать секреты на основе версии приложения.

> [!WARNING]
> Не используйте префиксы на секретах хранилища ключей, чтобы разместить секреты для нескольких приложений в одном хранилище ключей или поместить экологические секреты (например, *секреты развития* по сравнению с *производственными* секретами) в одном хранилище. Мы рекомендуем различным приложениям и средам разработки/производства использовать отдельные хранилища ключей для изоляции сред приложений для наивысшего уровня безопасности.

В следующем примере секрет устанавливается в хранилище ключей (и с использованием `5000-AppSecret` инструмента Secret Manager для среды разработки) для (периоды не допускаются в секретных именах хранилища ключей). Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения. Для другой версии приложения, 5.1.0.0, секрет добавляется в хранилище ключей (и с помощью инструмента Secret Manager) для `5100-AppSecret`. Каждая версия приложения загружает свою `AppSecret`версию секретное значение в свою конфигурацию, как, зачистки от версии, как он загружает секрет.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>называется с <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>обычаем :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Реализация <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> реагирует на префиксы версии секретов для загрузки надлежащего секрета в конфигурацию:

* `Load`загружает секрет, когда его название начинается с префикса. Другие секреты не загружаются.
* `GetKey`:
  * Удаляет префикс из секретного имени.
  * Заменяет два тире в `KeyDelimiter`любом названии с , который является делимитетом, используемым в конфигурации (обычно толстой кишки). Azure Key Vault не разрешает толстую кишку в секретных именах.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Метод `Load` вызывается алгоритмом поставщика, который итерирует секреты хранилища, чтобы найти те, которые имеют префикс версии. При обнаружении `Load`префикса версии `GetKey` алгоритм использует метод для возврата названия конфигурации секретного имени. Он снимает приставку версии с имени секрета и возвращает остальную часть секретного имени для загрузки в парах значения имени конфигурации приложения.

Когда этот подход реализован:

1. Версия приложения указана в файле проекта приложения. В следующем примере версия приложения установлена `5.0.0.0`на:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Подтвердите, что свойство `<UserSecretsId>` присутствует в `{GUID}` файле проекта приложения, где находится ГРАФИЧЕСКИй интерфейс, поставляемый пользователем:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Сохранить следующие секреты локально с [помощью инструмента Secret Manager:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Секреты сохраняются в Хранилище ключей Azure, используя следующие команды Azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. При запуске приложения загружаются секреты хранилища ключей. Секрет строки `5000-AppSecret` совпадает с версией приложения, указанной в`5.0.0.0`файле проекта приложения ().

1. Версия, `5000` (с тире), удаляется из названия ключа. На протяжении всего приложения, `AppSecret` чтение конфигурации с ключом загружает секретное значение.

1. Если версия приложения изменена в файле `5.1.0.0` проекта и приложение снова запущено, `5.1.0.0_secret_value_dev` возврат секретное `5.1.0.0_secret_value_prod` значение находится в среде разработки и в производстве.

> [!NOTE]
> Вы также можете <xref:Microsoft.Azure.KeyVault.KeyVaultClient> предоставить <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>свою собственную реализацию. Пользовательский клиент разрешает совместное использование одного экземпляра клиента через приложение.

## <a name="bind-an-array-to-a-class"></a>Привязка массива к классу

Поставщик способен считывать значения конфигурации в массив для привязки к массиву POCO.

При чтении из источника конфигурации,`:`который позволяет ключам содержать толстую кишку () сепараторы, цифровой сегмент ключа используется для различения ключей, которые составляют массив (`:0:`, `:1:`. &hellip; `:{n}:` Для получения дополнительной информации [см. Конфигурация: Привязать массив к классу.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)

Ключи Убежища ключей Azure не могут использовать толстую кишку в качестве сепаратора. В подходе, описанном в`--`этой теме, используются двойные тире () в качестве сепаратора для иерархических значений (разделов). Ключи array хранятся в Azure Key Vault с двойными`--0--` `--1--`тире и числовыми ключевыми сегментами (, ). &hellip; `--{n}--`

Изучите следующую конфигурацию поставщика журналов [Serilog,](https://serilog.net/) представленную файлом JSON. Есть два объекта буквально определяется `WriteTo` в массиве, которые отражают два Serilog *раковины*, которые описывают назначения для выхода регистрации:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

Конфигурация, отображаемый в предыдущем файле JSON, хранится в Azure Key Vault с помощью двойного тире ()`--`нотации и числовых сегментов:

| Клавиши | Значение |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Перезагрузить секреты

Секреты кэшируются до тех пор, пока `IConfigurationRoot.Reload()` не будет названо. Просроченные, отключенные и обновленные секреты в хранилище `Reload` ключей не соблюдаются приложением до тех пор, пока не будут выполнены.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Инвалиды и просроченные секреты

Инвалиды и истек <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>секреты бросить . Чтобы предотвратить бросок приложения, предоставьте конфигурацию с помощью другого поставщика конфигурации или обновите отключенный или просроченный секрет.

## <a name="troubleshoot"></a>Диагностика

Когда приложение не загружает конфигурацию с помощью провайдера, сообщение об ошибке записывается [в ASP.NET инфраструктуры Core Logging.](xref:fundamentals/logging/index) Следующие условия предотвратят загрузку конфигурации:

* Приложение или сертификат не настроены правильно в Active Directory Azure.
* Хранилище ключей не существует в Хранилище ключей Azure.
* Приложение не имеет права на доступ к хранилищу ключей.
* Политика доступа не включает `Get` `List` и разрешения.
* В хранилище ключей данные конфигурации (пара именных значений) неправильно называются, отсутствуют, отключены или истекли.
* Приложение имеет неправильное имя`KeyVaultName`хранилища ключа ( ,`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), или Azure AD сертификат отпечатков пальцев ().
* Ключ конфигурации (имя) неверен в приложении для значения, которое вы пытаетесь загрузить.
* При добавлении политики доступа к приложению в хранилище ключей была создана политика, но кнопка **«Сохранить»** не была выбрана в uI **политик access.**

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Ключевое Убежище](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Документация по ключевым хранилищам](/azure/key-vault/)
* [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](/azure/key-vault/key-vault-hsm-protected-keys)
* [Класс KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET](/azure/key-vault/quick-create-net)
* [Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

В этом документе объясняется, как использовать поставщика конфигурации [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) для загрузки значений конфигурации приложений из секретов Azure Key Vault. Azure Key Vault — это облачный сервис, который помогает охранять криптографические ключи и секреты, используемые приложениями и службами. Общие сценарии использования Azure Key Vault с ASP.NET основными приложениями включают:

* Контроль доступа к конфиденциальным данным конфигурации.
* Соответствие требованию fiPS 140-2 Уровень 2 проверенных модулей аппаратной безопасности (HSM) при хранении данных конфигурации.

[Просмотр или загрузка образца кода](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) [(как скачать)](xref:index#how-to-download-a-sample)

## <a name="packages"></a>Пакеты

Добавьте ссылку на пакет [Microsoft.Extensions.Configuration.AzureKeyVault.](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/)

## <a name="sample-app"></a>Пример приложения

Пример приложения работает в любом из двух `#define` режимов, определяемых заявлением в верхней части *файла Program.cs:*

* `Certificate`&ndash; Демонстрирует использование идентификатора клиента Azure Key Vault и сертификата X.509 для доступа к секретам, хранящимся в Azure Key Vault. Эта версия образца может быть запущена из любого места, развернута в службе приложений Azure или любого узла, способного обслуживать ASP.NET приложение Core.
* `Managed`&ndash; Демонстрирует, как использовать [управляемые идентификаторы для ресурсов Azure](/azure/active-directory/managed-identities-azure-resources/overview) для аутентификации приложения в Хранилище ключей Azure с аутентификацией Azure AD без учетных данных, хранящихся в коде или конфигурации приложения. При использовании управляемых идентификационных данных для проверки подлинности идентификатор приложения Azure AD и пароль (Client Secret) не требуются. Версия `Managed` образца должна быть развернута в Azure. Следуйте инструкциям в разделе [«Использование управляемых идентификаторов» для ресурсов Azure.](#use-managed-identities-for-azure-resources)

Для получения дополнительной информации о том, как настроить`#define`образец приложения с помощью препроцессорных директив ( , см. <xref:index#preprocessor-directives-in-sample-code>

## <a name="secret-storage-in-the-development-environment"></a>Секретное хранилище в среде разработки

Установите секреты локально с помощью [инструмента Secret Manager.](xref:security/app-secrets) Когда пример приложения работает на локальной машине в среде разработки, секреты загружаются из местного магазина Secret Manager.

Инструмент «Секретный `<UserSecretsId>` менеджер» требует свойства в файле проекта приложения. Установите значение`{GUID}`свойства ( ) на любой уникальный GUID:

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

Секреты создаются как пары имено-ценности. Иерархические значения (разделы конфигурации) используют a `:` (colon) в качестве сепаратора в ASP.NET ключевых имен [конфигурации Core.](xref:fundamentals/configuration/index)

Секретный менеджер используется из командной оболочки, открытой для `{SECRET NAME}` [корня содержимого](xref:fundamentals/index#content-root)проекта, где находится имя и `{SECRET VALUE}` значение:

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

Выполните следующие команды в командной оболочке из [корня содержимого](xref:fundamentals/index#content-root) проекта, чтобы установить секреты для примера приложения:

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

Когда эти секреты хранятся в Хранилище ключей Azure в [секретном хранилище в производственной среде с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault, `_dev` суффикс изменяется на `_prod`. Суффикс обеспечивает визуальный сигнал на выходе приложения с указанием источника значений конфигурации.

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a>Секретное хранилище в производственной среде с Azure Key Vault

Инструкции, предоставленные [quickstart: Установить и получить секрет из Azure Key Vault с помощью темы Azure CLI,](/azure/key-vault/quick-create-cli) суммируются здесь для создания Убежища ключей Azure и хранения секретов, используемых в примере приложения. Для более подробной информации обратитесь к теме.

1. Откройте оболочку облака Azure, используя любой из следующих методов [на портале Azure:](https://portal.azure.com/)

   * Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. Используйте строку поиска "Azure CLI" в текстовом поле.
   * Откройте облачную оболочку в браузере с помощью кнопки **Launch Cloud Shell.**
   * Выберите кнопку **Cloud Shell** в меню в правом верхнем углу портала Azure.

   Для получения дополнительной информации смотрите [обзор Azure CLI](/cli/azure/) и [обзор облачной оболочки Azure.](/azure/cloud-shell/overview)

1. Если вы еще не проверены, вопийте команду. `az login`

1. Создайте группу ресурсов со `{RESOURCE GROUP NAME}` следующей командой, где находится `{LOCATION}` название группы ресурсов для новой группы ресурсов и является областью Azure (центр обработки данных):

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Создайте хранилище ключей в группе ресурсов `{KEY VAULT NAME}` со следующей командой, `{LOCATION}` где находится название нового хранилища ключей и является областью Azure (центр обработки данных):

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. Создавайте секреты в хранилище ключей в виде пар именной ценности.

   Секретные имена Azure Key Vault ограничены буквенно-цифровыми персонажами и тире. Иерархические значения (разделы `--` конфигурации) используют (два тире) в качестве сепаратора. Колоны, которые обычно используются для разграничения раздела из подключаемых ASP.NET [конфигурации Core,](xref:fundamentals/configuration/index)не допускаются в секретных именах хранилища ключей. Таким образом, два тире используются и обмениваются на толстую кишку, когда секреты загружаются в конфигурацию приложения.

   Следующие секреты для использования с образцом приложения. Значения включают `_prod` суффикс, чтобы отличить их от `_dev` значений суффикса, загруженных в среде разработки, от пользовательских секретов. Замените `{KEY VAULT NAME}` имя хранилища ключей, созданного на предыдущем этапе:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a>Идентификатор приложений и сертификат X.509 для приложений, не хозяек, размещенных в Azure

Напередите Azure AD, Azure Key Vault и приложение для использования идентификатора приложений Active Directory Azure и сертификата X.509 для проверки подлинности хранилища ключей **при размещении приложения за пределами Azure.** См. дополнительные сведения о [ключах, секретах и сертификатах](/azure/key-vault/about-keys-secrets-and-certificates).

> [!NOTE]
> Хотя использование идентификатора приложений и сертификата X.509 поддерживается для приложений, размещенных в Azure, мы рекомендуем использовать [управляемые идентификаторы для ресурсов Azure](#use-managed-identities-for-azure-resources) при хостинге приложения в Azure. Управляемые идентификаторы не требуют хранения сертификата в приложении или в среде разработки.

В примере приложения используется идентификатор приложения `#define` и сертификат X.509, `Certificate`когда заявление в верхней части *Program.cs* файла установлено.

1. Создайте сертификат PKCS-12 *(.pfx)* сертификат. Варианты создания сертификатов включают [MakeCert на Windows](/windows/desktop/seccrypto/makecert) и [OpenSSL.](https://www.openssl.org/)
1. Установите сертификат в личный магазин сертификатов текущего пользователя. Маркировка ключа как экспортируемого не является обязательной. Обратите внимание на отпечаток пальца сертификата, который используется позже в этом процессе.
1. Экспорт архива PKCS-12 (*.pfx*) сертификат в качестве DER-кодированного сертификата (*.cer*).
1. Зарегистрируйте приложение с Помощью Azure AD **(регистрация приложений).**
1. Загрузите закодированный сертификат DER *(.cer)* в Azure AD:
   1. Выберите приложение в Azure AD.
   1. Перейдите к **сертификатам & секреты**.
   1. Выберите **сертификат загрузки** для загрузки сертификата, который содержит общедоступный ключ. Сертификат *.cer*, *.pem*или *.crt* является приемлемым.
1. Храните имя хранилища ключей, идентификатор приложения и отпечаток пальца сертификата в файле *appsettings.json* приложения.
1. Перейдите к **хранилищам ключей** на портале Azure.
1. Выберите хранилище ключей, созданное в [секретном хранилище в среде Production, с](#secret-storage-in-the-production-environment-with-azure-key-vault) разделом Azure Key Vault.
1. Выберите **политики доступа.**
1. Выберите **политику добавления доступа.**
1. Открытые **секретные разрешения** и предоставить приложение с **получить** и **список** разрешений.
1. Выберите **основной принцип** и выберите зарегистрированное приложение по имени. Нажмите кнопку **Выбрать**.
1. Щелкните **ОК**.
1. Щелкните **Сохранить**.
1. Разверните приложение.

Пример `Certificate` приложения получает значения конфигурации `IConfigurationRoot` с тем же именем, что и секретное имя:

* Неиерархические значения: Значение для `SecretName` получено `config["SecretName"]`с .
* Иерархические значения (разделы): Используйте `:` (колонию) обозначения или метод `GetSection` расширения. Используйте любой из этих подходов для получения значения конфигурации:
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

Сертификат X.509 управляется ОС. Приложение вызывает <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> значения, поставляемые файлом *appsettings.json:*

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

Примеры значений:

* Имя хранилища ключа:`contosovault`
* Идентификатор приложения:`627e911e-43cc-61d4-992e-12db9c81b413`
* Отпечаток пальца сертификата:`fe14593dd66b2406c5269d742d04b6e1ab03adb1`

*appsettings.json*:

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

При запуске приложения на веб-странице отображаются загруженные секретные значения. В среде разработки секретные `_dev` значения загружаются суффиксом. В производственной среде значения загружаются суффиксом. `_prod`

## <a name="use-managed-identities-for-azure-resources"></a>Используйте управляемые идентификаторы для ресурсов Azure

**Приложение, развернутое в Azure,** может воспользоваться [управляемыми идентификаторами для ресурсов Azure,](/azure/active-directory/managed-identities-azure-resources/overview)что позволяет приложению аутентифицировать сяддение с помощью Azure Key Vault с помощью аутентификации Azure AD без учетных данных (Идентификатор приложения и пароль/клиентсекрет), хранящийся в приложении.

Приложение образец использует управляемые идентификаторы для ресурсов Azure `Managed`при установке `#define` оператора в верхней части файла *Program.cs.*

Введите имя хранилища в файл *appsettings.json* приложения. Пример приложения не требует идентификатора приложения и пароль `Managed` (Client Secret) при установке на версию, так что вы можете игнорировать эти настройки. Приложение развернуто в Azure, и Azure проверяет подлинность приложения для доступа к Azure Key Vault только с помощью имени хранилища, хранящегося в файле *appsettings.json.*

Развертывание примера приложения в службу приложений Azure.

Приложение, развернутое в службе приложений Azure, автоматически регистрируется в Azure AD при создании службы. Получите идентификатор объекта из развертывания для использования в следующей команде. Идентификатор объекта отображается на портале Azure на панели **идентификационных** данных Службы app.

Используя Azure CLI и идентификатор объекта `list` `get` приложения, предоставьте приложение и разрешения на доступ к хранилищу ключей:

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

**Перезапустите приложение** с помощью Azure CLI, PowerShell или портала Azure.

Пример приложения:

* Создает экземпляр `AzureServiceTokenProvider` класса без строки соединения. Когда строка соединения не предоставлена, провайдер пытается получить токен доступа из управляемых идентификаторов для ресурсов Azure.
* Новый <xref:Microsoft.Azure.KeyVault.KeyVaultClient> создается `AzureServiceTokenProvider` с обратным вызовом маркера экземпляра.
* Экземпляр <xref:Microsoft.Azure.KeyVault.KeyVaultClient> используется с реализацией <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> по умолчанию, которая загружает все`--`секретные значения`:`и заменяет двойные капли () с толстой кишки ( ) в ключевых имен.

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

Значение примера имени хранилища ключей:`contosovault`
    
*appsettings.json*:

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

При запуске приложения на веб-странице отображаются загруженные секретные значения. В среде разработки секретные `_dev` значения имеют суффикс, потому что они предоставляются секретами пользователя. В рабочей среде значения загружаются `_prod` суффиксом, поскольку они предоставляются Azure Key Vault.

Если вы `Access denied` получили ошибку, подтвердите, что приложение зарегистрировано в Azure AD и предоставляет доступ к хранилищу ключей. Подтвердите, что вы перезапустили службу в Azure.

Для получения информации об использовании поставщика с управляемой идентификацией и конвейером Azure DevOps [см.](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity)

## <a name="use-a-key-name-prefix"></a>Используйте префикс ключевого имени

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>обеспечивает <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>перегрузку, которая принимает реализацию, которая позволяет контролировать, как секреты хранилища ключей преобразуются в клавиши конфигурации. Например, можно реализовать интерфейс для загрузки секретных значений на основе значения префикса, предоставляемого при запуске приложения. Это позволяет, например, загружать секреты на основе версии приложения.

> [!WARNING]
> Не используйте префиксы на секретах хранилища ключей, чтобы разместить секреты для нескольких приложений в одном хранилище ключей или поместить экологические секреты (например, *секреты развития* по сравнению с *производственными* секретами) в одном хранилище. Мы рекомендуем различным приложениям и средам разработки/производства использовать отдельные хранилища ключей для изоляции сред приложений для наивысшего уровня безопасности.

В следующем примере секрет устанавливается в хранилище ключей (и с использованием `5000-AppSecret` инструмента Secret Manager для среды разработки) для (периоды не допускаются в секретных именах хранилища ключей). Этот секрет представляет секрет приложения для версии 5.0.0.0 приложения. Для другой версии приложения, 5.1.0.0, секрет добавляется в хранилище ключей (и с помощью инструмента Secret Manager) для `5100-AppSecret`. Каждая версия приложения загружает свою `AppSecret`версию секретное значение в свою конфигурацию, как, зачистки от версии, как он загружает секрет.

<xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>называется с <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>обычаем :

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

Реализация <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> реагирует на префиксы версии секретов для загрузки надлежащего секрета в конфигурацию:

* `Load`загружает секрет, когда его название начинается с префикса. Другие секреты не загружаются.
* `GetKey`:
  * Удаляет префикс из секретного имени.
  * Заменяет два тире в `KeyDelimiter`любом названии с , который является делимитетом, используемым в конфигурации (обычно толстой кишки). Azure Key Vault не разрешает толстую кишку в секретных именах.

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

Метод `Load` вызывается алгоритмом поставщика, который итерирует секреты хранилища, чтобы найти те, которые имеют префикс версии. При обнаружении `Load`префикса версии `GetKey` алгоритм использует метод для возврата названия конфигурации секретного имени. Он снимает приставку версии с имени секрета и возвращает остальную часть секретного имени для загрузки в парах значения имени конфигурации приложения.

Когда этот подход реализован:

1. Версия приложения указана в файле проекта приложения. В следующем примере версия приложения установлена `5.0.0.0`на:

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. Подтвердите, что свойство `<UserSecretsId>` присутствует в `{GUID}` файле проекта приложения, где находится ГРАФИЧЕСКИй интерфейс, поставляемый пользователем:

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   Сохранить следующие секреты локально с [помощью инструмента Secret Manager:](xref:security/app-secrets)

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. Секреты сохраняются в Хранилище ключей Azure, используя следующие команды Azure CLI:

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. При запуске приложения загружаются секреты хранилища ключей. Секрет строки `5000-AppSecret` совпадает с версией приложения, указанной в`5.0.0.0`файле проекта приложения ().

1. Версия, `5000` (с тире), удаляется из названия ключа. На протяжении всего приложения, `AppSecret` чтение конфигурации с ключом загружает секретное значение.

1. Если версия приложения изменена в файле `5.1.0.0` проекта и приложение снова запущено, `5.1.0.0_secret_value_dev` возврат секретное `5.1.0.0_secret_value_prod` значение находится в среде разработки и в производстве.

> [!NOTE]
> Вы также можете <xref:Microsoft.Azure.KeyVault.KeyVaultClient> предоставить <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>свою собственную реализацию. Пользовательский клиент разрешает совместное использование одного экземпляра клиента через приложение.

## <a name="bind-an-array-to-a-class"></a>Привязка массива к классу

Поставщик способен считывать значения конфигурации в массив для привязки к массиву POCO.

При чтении из источника конфигурации,`:`который позволяет ключам содержать толстую кишку () сепараторы, цифровой сегмент ключа используется для различения ключей, которые составляют массив (`:0:`, `:1:`. &hellip; `:{n}:` Для получения дополнительной информации [см. Конфигурация: Привязать массив к классу.](xref:fundamentals/configuration/index#bind-an-array-to-a-class)

Ключи Убежища ключей Azure не могут использовать толстую кишку в качестве сепаратора. В подходе, описанном в`--`этой теме, используются двойные тире () в качестве сепаратора для иерархических значений (разделов). Ключи array хранятся в Azure Key Vault с двойными`--0--` `--1--`тире и числовыми ключевыми сегментами (, ). &hellip; `--{n}--`

Изучите следующую конфигурацию поставщика журналов [Serilog,](https://serilog.net/) представленную файлом JSON. Есть два объекта буквально определяется `WriteTo` в массиве, которые отражают два Serilog *раковины*, которые описывают назначения для выхода регистрации:

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

Конфигурация, отображаемый в предыдущем файле JSON, хранится в Azure Key Vault с помощью двойного тире ()`--`нотации и числовых сегментов:

| Клавиши | Значение |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a>Перезагрузить секреты

Секреты кэшируются до тех пор, пока `IConfigurationRoot.Reload()` не будет названо. Просроченные, отключенные и обновленные секреты в хранилище `Reload` ключей не соблюдаются приложением до тех пор, пока не будут выполнены.

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a>Инвалиды и просроченные секреты

Инвалиды и истек <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>секреты бросить . Чтобы предотвратить бросок приложения, предоставьте конфигурацию с помощью другого поставщика конфигурации или обновите отключенный или просроченный секрет.

## <a name="troubleshoot"></a>Диагностика

Когда приложение не загружает конфигурацию с помощью провайдера, сообщение об ошибке записывается [в ASP.NET инфраструктуры Core Logging.](xref:fundamentals/logging/index) Следующие условия предотвратят загрузку конфигурации:

* Приложение или сертификат не настроены правильно в Active Directory Azure.
* Хранилище ключей не существует в Хранилище ключей Azure.
* Приложение не имеет права на доступ к хранилищу ключей.
* Политика доступа не включает `Get` `List` и разрешения.
* В хранилище ключей данные конфигурации (пара именных значений) неправильно называются, отсутствуют, отключены или истекли.
* Приложение имеет неправильное имя`KeyVaultName`хранилища ключа ( ,`AzureADApplicationId`Azure AD Application Id`AzureADCertThumbprint`( ), или Azure AD сертификат отпечатков пальцев ().
* Ключ конфигурации (имя) неверен в приложении для значения, которое вы пытаетесь загрузить.
* При добавлении политики доступа к приложению в хранилище ключей была создана политика, но кнопка **«Сохранить»** не была выбрана в uI **политик access.**

## <a name="additional-resources"></a>Дополнительные ресурсы

* <xref:fundamentals/configuration/index>
* [Microsoft Azure: Ключевое Убежище](https://azure.microsoft.com/services/key-vault/)
* [Microsoft Azure: Документация по ключевым хранилищам](/azure/key-vault/)
* [Создание ключей, защищенных аппаратным модулем безопасности, и их передача в хранилище ключей Azure](/azure/key-vault/key-vault-hsm-protected-keys)
* [Класс KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [Краткое руководство. Настройка и получение секрета из Azure Key Vault с помощью веб-приложения .NET](/azure/key-vault/quick-create-net)
* [Руководство. Использование Azure Key Vault с виртуальной машиной Linux в Azure (.NET)](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

