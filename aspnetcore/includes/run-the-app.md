# <a name="visual-studio"></a>[<span data-ttu-id="e8a99-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e8a99-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e8a99-102">Нажмите клавиши CTRL+F5, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="e8a99-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="e8a99-103">Visual Studio запускает [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), а затем приложение.</span><span class="sxs-lookup"><span data-stu-id="e8a99-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="e8a99-104">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="e8a99-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="e8a99-105">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="e8a99-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="e8a99-106">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="e8a99-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="e8a99-107">Когда Visual Studio создает веб-проект, для веб-сервера используется случайный порт.</span><span class="sxs-lookup"><span data-stu-id="e8a99-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="e8a99-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e8a99-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="e8a99-109">Нажмите клавиши **CTRL-F5**, чтобы выполнить запуск без отладчика.</span><span class="sxs-lookup"><span data-stu-id="e8a99-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="e8a99-110">Visual Studio Code запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e8a99-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="e8a99-111">В адресной строке указывается `localhost:port#`, а не что-либо типа `example.com`.</span><span class="sxs-lookup"><span data-stu-id="e8a99-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="e8a99-112">Это связано с тем, что `localhost` — стандартное имя узла для локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="e8a99-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="e8a99-113">Localhost обслуживает только веб-запросы с локального компьютера.</span><span class="sxs-lookup"><span data-stu-id="e8a99-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="e8a99-114">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="e8a99-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="e8a99-115">Чтобы выполнить запуск без отладчика, нажмите клавиши **OPT+CMD+RETURN** в Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e8a99-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="e8a99-116">Вы также можете в строке меню выбрать **Запуск > Запуск без отладки**.</span><span class="sxs-lookup"><span data-stu-id="e8a99-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="e8a99-117">Visual Studio запускает [Kestrel](xref:fundamentals/servers/kestrel), открывает браузер и переходит к `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="e8a99-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---
