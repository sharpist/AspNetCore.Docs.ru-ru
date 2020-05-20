# <a name="visual-studio"></a>[<span data-ttu-id="a74ed-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a74ed-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a74ed-102">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="a74ed-102">Create a new project.</span></span>
1. <span data-ttu-id="a74ed-103">Выберите **службу рабочей роли**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-103">Select **Worker Service**.</span></span> <span data-ttu-id="a74ed-104">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-104">Select **Next**.</span></span>
1. <span data-ttu-id="a74ed-105">В поле **Имя проекта** укажите имя проекта или оставьте имя по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="a74ed-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="a74ed-106">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-106">Select **Create**.</span></span>
1. <span data-ttu-id="a74ed-107">В диалоговом окне **Создать службу рабочей роли** выберите **Создать**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a74ed-108">Visual Studio для Mac</span><span class="sxs-lookup"><span data-stu-id="a74ed-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="a74ed-109">Создайте новый проект.</span><span class="sxs-lookup"><span data-stu-id="a74ed-109">Create a new project.</span></span>
1. <span data-ttu-id="a74ed-110">В разделе **.NET Core** на боковой панели выберите **Приложение**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="a74ed-111">В разделе **ASP.NET Core** выберите **Рабочая роль**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="a74ed-112">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-112">Select **Next**.</span></span>
1. <span data-ttu-id="a74ed-113">Для параметра **Требуемая версия .NET Framework** выберите **.NET Core 3.0** или более поздней версии.</span><span class="sxs-lookup"><span data-stu-id="a74ed-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="a74ed-114">Выберите **Далее**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-114">Select **Next**.</span></span>
1. <span data-ttu-id="a74ed-115">Введите имя в поле **Имя проекта**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="a74ed-116">Нажмите кнопку **Создать**.</span><span class="sxs-lookup"><span data-stu-id="a74ed-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a74ed-117">Интерфейс командной строки .NET Core</span><span class="sxs-lookup"><span data-stu-id="a74ed-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a74ed-118">Используйте шаблон службы рабочей роли (`worker`) с командой [dotnet new](/dotnet/core/tools/dotnet-new) из командной оболочки.</span><span class="sxs-lookup"><span data-stu-id="a74ed-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="a74ed-119">В приведенном ниже примере создается приложение службы рабочей роли с именем `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="a74ed-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="a74ed-120">Папка для приложения `ContosoWorker` создается автоматически при выполнении команды.</span><span class="sxs-lookup"><span data-stu-id="a74ed-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
