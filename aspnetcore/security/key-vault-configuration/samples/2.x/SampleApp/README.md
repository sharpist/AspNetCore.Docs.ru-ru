# <a name="key-vault-configuration-provider-sample-app"></a>Пример приложения поставщика конфигурации Key Vault

В этом примере демонстрируется использование поставщика конфигурации Azure Key Vault.

Пример выполняется в одном из двух режимов, определяемых `#define` инструкцией, расположенной в верхней части файла *Program.CS* . Инструкции см. [в разделе Директивы препроцессора в образце кода](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code):

* `Certificate`: Демонстрируется использование идентификатора клиента Azure Key Vault и сертификата X. 509 для доступа к секретам, хранящимся в Azure Key Vault. Эту версию примера можно запустить из любого расположения, развернуть в службе приложений Azure или на любом узле, способном обслуживать ASP.NET Coreное приложение.
* `Managed`: Демонстрируется использование [управляемое удостоверение службы](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Azure для проверки подлинности приложения в Azure Key Vault с проверкой подлинности Azure AD без учетных данных в коде или конфигурации приложения. ИДЕНТИФИКАТОР и секрет клиента Azure AD не требуются для проверки подлинности приложения с помощью Azure Key Vault. Этот пример необходимо развернуть в службе приложений Azure, чтобы изучить управляемое удостоверение сцеарнио.

Дополнительные сведения см. в разделе [поставщик конфигурации Azure Key Vault](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration).
