<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Отключить проверку учетной записи по умолчанию

При использовании шаблонов по умолчанию пользователь перенаправляется к, `Account.RegisterConfirmation` где можно выбрать ссылку для подтверждения учетной записи. Значение по умолчанию `Account.RegisterConfirmation` используется ***только*** для тестирования, автоматическая проверка учетной записи должна быть отключена в рабочем приложении.

Чтобы запросить подтвержденную учетную запись и предотвратить немедленный вход при регистрации, установите `DisplayConfirmAccountLink = false` в */Ареас/идентити/Пажес/аккаунт/регистерконфирматион.кштмл.КС*:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]