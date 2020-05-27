`App`Компонент (*app. Razor*) похож на `App` компонент, обнаруженный в приложениях блазор Server.

* <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>Компонент управляет предоставлением <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> для остального приложения.
* <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>Компонент гарантирует, что текущий пользователь имеет право доступа к определенной странице или иным образом визуализирует `RedirectToLogin` компонент.
* `RedirectToLogin`Компонент управляет перенаправлением неавторизованных пользователей на страницу входа.

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
