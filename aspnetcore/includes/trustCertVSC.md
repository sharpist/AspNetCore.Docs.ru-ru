* <span data-ttu-id="6aa6d-101">Настройте доверие сертификату разработки HTTPS с помощью следующей команды:</span><span class="sxs-lookup"><span data-stu-id="6aa6d-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="6aa6d-102">Предыдущая команда не работает в Linux.</span><span class="sxs-lookup"><span data-stu-id="6aa6d-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="6aa6d-103">Сведения о настройке доверия к сертификату см. в документации по вашему дистрибутиву Linux.</span><span class="sxs-lookup"><span data-stu-id="6aa6d-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="6aa6d-104">Приведенная выше команда отображает следующее диалоговое окно.</span><span class="sxs-lookup"><span data-stu-id="6aa6d-104">The preceding command displays the following dialog:</span></span>

  ![Диалоговое окно "Предупреждение о безопасности"](~/getting-started/_static/cert.png)

* <span data-ttu-id="6aa6d-106">Выберите **Да**, если согласны доверять сертификату разработки.</span><span class="sxs-lookup"><span data-stu-id="6aa6d-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="6aa6d-107">Дополнительные сведения см. в разделе [Настройка доверия к сертификату разработки HTTPS ASP.NET Core](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="6aa6d-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]