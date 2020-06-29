Страница индекса (`wwwroot/index.html`) содержит сценарий, определяющий `AuthenticationService` в JavaScript. `AuthenticationService` обрабатывает низкоуровневые сведения о протоколе OIDC. Приложение внутренне вызывает методы, определенные в сценарии для выполнения операций проверки подлинности.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
