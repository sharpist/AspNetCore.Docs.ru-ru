Компонент (*app. Razor*) похож на компонент, `App` обнаруженный в приложениях блазор Server. `App`

* `CascadingAuthenticationState` Компонент управляет предоставлением `AuthenticationState` для остального приложения.
* `AuthorizeRouteView` Компонент гарантирует, что текущий пользователь имеет право доступа к определенной странице или иным образом визуализирует `RedirectToLogin` компонент.
* `RedirectToLogin` Компонент управляет перенаправлением неавторизованных пользователей на страницу входа.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```
