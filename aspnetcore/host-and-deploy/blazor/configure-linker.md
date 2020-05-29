---
<span data-ttu-id="154bc-101">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-103">'Blazor'</span></span>
- <span data-ttu-id="154bc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-104">'Identity'</span></span>
- <span data-ttu-id="154bc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-106">'Razor'</span></span>
- <span data-ttu-id="154bc-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="154bc-108">Настройка компоновщика для ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="154bc-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="154bc-109">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="154bc-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="154bc-110"> WebAssembly выполняет компоновку [промежуточного языка (IL)](/dotnet/standard/managed-code#intermediate-language--execution) во время сборки, чтобы затем удалить ненужный IL из выходных сборок приложения.</span><span class="sxs-lookup"><span data-stu-id="154bc-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="154bc-111">Компоновщик отключен при сборке в конфигурации отладки.</span><span class="sxs-lookup"><span data-stu-id="154bc-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="154bc-112">Для включения компоновщика приложения должны быть построены в конфигурации выпуска.</span><span class="sxs-lookup"><span data-stu-id="154bc-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="154bc-113">Мы рекомендуем создавать выпуск при развертывании приложений Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="154bc-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="154bc-114">Компоновка приложения оптимизируется в зависимости от размера, но это может иметь негативные последствия.</span><span class="sxs-lookup"><span data-stu-id="154bc-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="154bc-115">Приложения, использующие отражение или связанные динамические функции, могут прерываться при усечении, так как компоновщик не знает об этом динамическом поведении и не может определить, какие типы необходимы для отражения во время выполнения.</span><span class="sxs-lookup"><span data-stu-id="154bc-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="154bc-116">Чтобы обрезать такие приложения, компоновщик должен быть уведомлен о любых типах, необходимых для отражения в коде и в пакетах или платформах, от которых зависит приложение.</span><span class="sxs-lookup"><span data-stu-id="154bc-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="154bc-117">Чтобы обеспечить правильную работу обрезанного приложения после его развертывания, важно часто тестировать сборки выпуска приложения при разработке.</span><span class="sxs-lookup"><span data-stu-id="154bc-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="154bc-118">Компоновку приложений Blazor можно настроить с помощью следующих функций MSBuild:</span><span class="sxs-lookup"><span data-stu-id="154bc-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="154bc-119">настройка компоновки глобально с помощью [свойства MSBuild](#control-linking-with-an-msbuild-property);</span><span class="sxs-lookup"><span data-stu-id="154bc-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="154bc-120">управлять компоновкой каждой сборки с помощью [файла конфигурации](#control-linking-with-a-configuration-file).</span><span class="sxs-lookup"><span data-stu-id="154bc-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="154bc-121">Управление компоновкой с помощью свойства MSBuild</span><span class="sxs-lookup"><span data-stu-id="154bc-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="154bc-122">Компоновка включается, когда приложение собирается в конфигурации `Release`.</span><span class="sxs-lookup"><span data-stu-id="154bc-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="154bc-123">Чтобы изменить это поведение, настройте свойство MSBuild `BlazorWebAssemblyEnableLinking` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="154bc-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="154bc-124">Управление компоновкой с помощью файла конфигурации</span><span class="sxs-lookup"><span data-stu-id="154bc-124">Control linking with a configuration file</span></span>

<span data-ttu-id="154bc-125">Чтобы управлять компоновкой каждой сборки, нужно предоставить XML-файл конфигурации и указать его как элемент MSBuild в файле проекта.</span><span class="sxs-lookup"><span data-stu-id="154bc-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="154bc-126">*LinkerConfig.xml*:</span><span class="sxs-lookup"><span data-stu-id="154bc-126">*LinkerConfig.xml*:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

<span data-ttu-id="154bc-127">Дополнительные сведения см. в разделе [Форматы данных (репозиторий GitHub для mono/компоновщика)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="154bc-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="154bc-128">Добавление файла конфигурации компоновщика XML в библиотеку</span><span class="sxs-lookup"><span data-stu-id="154bc-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="154bc-129">Чтобы настроить компоновщик для определенной библиотеки, добавьте файл конфигурации компоновщика XML в библиотеку в качестве внедренного ресурса.</span><span class="sxs-lookup"><span data-stu-id="154bc-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="154bc-130">Имя внедренного ресурса должно совпадать с именем сборки.</span><span class="sxs-lookup"><span data-stu-id="154bc-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="154bc-131">В следующем примере файл *LinkerConfig.xml* указан как внедренный ресурс, имя которого совпадает с именем сборки библиотеки.</span><span class="sxs-lookup"><span data-stu-id="154bc-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="154bc-132">Настройка компоновщика для интернационализации</span><span class="sxs-lookup"><span data-stu-id="154bc-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="154bc-133">По умолчанию конфигурация компоновщика Blazor для приложений Blazor WebAssembly исключает сведения об интернационализации, кроме явно запрошенных языковых стандартов.</span><span class="sxs-lookup"><span data-stu-id="154bc-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="154bc-134">Удаление этих сборок уменьшает размер приложения.</span><span class="sxs-lookup"><span data-stu-id="154bc-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="154bc-135">Чтобы указать, какие сборки I18N необходимо оставить, задайте свойство MSBuild `<BlazorWebAssemblyI18NAssemblies>` в файле проекта:</span><span class="sxs-lookup"><span data-stu-id="154bc-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="154bc-136">Значение региона</span><span class="sxs-lookup"><span data-stu-id="154bc-136">Region Value</span></span>     | <span data-ttu-id="154bc-137">Сборка для одного региона</span><span class="sxs-lookup"><span data-stu-id="154bc-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="154bc-138">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-140">'Blazor'</span></span>
- <span data-ttu-id="154bc-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-141">'Identity'</span></span>
- <span data-ttu-id="154bc-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-143">'Razor'</span></span>
- <span data-ttu-id="154bc-144">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-145">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-147">'Blazor'</span></span>
- <span data-ttu-id="154bc-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-148">'Identity'</span></span>
- <span data-ttu-id="154bc-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-150">'Razor'</span></span>
- <span data-ttu-id="154bc-151">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-152">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-154">'Blazor'</span></span>
- <span data-ttu-id="154bc-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-155">'Identity'</span></span>
- <span data-ttu-id="154bc-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-157">'Razor'</span></span>
- <span data-ttu-id="154bc-158">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-159">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-161">'Blazor'</span></span>
- <span data-ttu-id="154bc-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-162">'Identity'</span></span>
- <span data-ttu-id="154bc-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-164">'Razor'</span></span>
- <span data-ttu-id="154bc-165">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-166">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-168">'Blazor'</span></span>
- <span data-ttu-id="154bc-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-169">'Identity'</span></span>
- <span data-ttu-id="154bc-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-171">'Razor'</span></span>
- <span data-ttu-id="154bc-172">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-173">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-175">'Blazor'</span></span>
- <span data-ttu-id="154bc-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-176">'Identity'</span></span>
- <span data-ttu-id="154bc-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-178">'Razor'</span></span>
- <span data-ttu-id="154bc-179">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-179">'SignalR' uid:</span></span> 

<span data-ttu-id="154bc-180">-------- | --- название: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-182">'Blazor'</span></span>
- <span data-ttu-id="154bc-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-183">'Identity'</span></span>
- <span data-ttu-id="154bc-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-185">'Razor'</span></span>
- <span data-ttu-id="154bc-186">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-187">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-189">'Blazor'</span></span>
- <span data-ttu-id="154bc-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-190">'Identity'</span></span>
- <span data-ttu-id="154bc-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-192">'Razor'</span></span>
- <span data-ttu-id="154bc-193">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-194">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-196">'Blazor'</span></span>
- <span data-ttu-id="154bc-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-197">'Identity'</span></span>
- <span data-ttu-id="154bc-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-199">'Razor'</span></span>
- <span data-ttu-id="154bc-200">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-201">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-203">'Blazor'</span></span>
- <span data-ttu-id="154bc-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-204">'Identity'</span></span>
- <span data-ttu-id="154bc-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-206">'Razor'</span></span>
- <span data-ttu-id="154bc-207">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-208">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-210">'Blazor'</span></span>
- <span data-ttu-id="154bc-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-211">'Identity'</span></span>
- <span data-ttu-id="154bc-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-213">'Razor'</span></span>
- <span data-ttu-id="154bc-214">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-215">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-217">'Blazor'</span></span>
- <span data-ttu-id="154bc-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-218">'Identity'</span></span>
- <span data-ttu-id="154bc-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-220">'Razor'</span></span>
- <span data-ttu-id="154bc-221">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-222">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-224">'Blazor'</span></span>
- <span data-ttu-id="154bc-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-225">'Identity'</span></span>
- <span data-ttu-id="154bc-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-227">'Razor'</span></span>
- <span data-ttu-id="154bc-228">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-229">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-231">'Blazor'</span></span>
- <span data-ttu-id="154bc-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-232">'Identity'</span></span>
- <span data-ttu-id="154bc-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-234">'Razor'</span></span>
- <span data-ttu-id="154bc-235">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="154bc-236">title: 'Настройка компоновщика для ASP.NET Core Blazor' author: description: 'Узнайте, как управлять компоновщиком для промежуточного языка (IL) при создании приложения Blazor.'</span><span class="sxs-lookup"><span data-stu-id="154bc-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="154bc-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="154bc-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="154bc-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="154bc-238">'Blazor'</span></span>
- <span data-ttu-id="154bc-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="154bc-239">'Identity'</span></span>
- <span data-ttu-id="154bc-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="154bc-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="154bc-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="154bc-241">'Razor'</span></span>
- <span data-ttu-id="154bc-242">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="154bc-242">'SignalR' uid:</span></span> 

<span data-ttu-id="154bc-243">------------ | | `all`            | Включены все сборки | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (по умолчанию) | Нет                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span><span class="sxs-lookup"><span data-stu-id="154bc-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="154bc-244">Для разделения нескольких значений используйте запятую (например, `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="154bc-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="154bc-245">Дополнительные сведения см. на странице [I18N: библиотека платформы интернационализации Pnetlib (репозиторий mono/mono GitHub)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="154bc-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="154bc-246">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="154bc-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
