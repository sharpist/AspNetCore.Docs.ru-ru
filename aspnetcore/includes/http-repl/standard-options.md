* `-F|--no-formatting`

  <span data-ttu-id="8fd3c-101">Флаг, при установке которого форматирование HTTP-ответа не применяется.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="8fd3c-102">Задает заголовок HTTP-запроса.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-102">Sets an HTTP request header.</span></span> <span data-ttu-id="8fd3c-103">Поддерживаются следующие два формата значений:</span><span class="sxs-lookup"><span data-stu-id="8fd3c-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="8fd3c-104">Указывает файл, в который должен быть записан текст HTTP-ответа.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="8fd3c-105">Например, `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="8fd3c-106">Если файл не существует, он создается.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="8fd3c-107">Указывает файл, в который должны быть записаны заголовки HTTP-ответа.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="8fd3c-108">Например, `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="8fd3c-109">Если файл не существует, он создается.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="8fd3c-110">Флаг, при установке которого включается потоковая передача HTTP-ответа.</span><span class="sxs-lookup"><span data-stu-id="8fd3c-110">A flag whose presence enables streaming of the HTTP response.</span></span>
