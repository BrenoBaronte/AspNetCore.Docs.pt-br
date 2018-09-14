---
title: Migrar do ASP.NET para o ASP.NET Core
author: isaac2004
description: Receba orientações para migrar os aplicativos existentes do ASP.NET MVC ou da API Web para o ASP.NET Core.web
ms.author: scaddie
ms.date: 08/27/2017
uid: migration/proper-to-2x/index
ms.openlocfilehash: 85f8ca30d4885476dea2da13c241483fc074c4b7
ms.sourcegitcommit: a742b55e4b8276a48b8b4394784554fecd883c84
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2018
ms.locfileid: "45538460"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a><span data-ttu-id="0fad0-103">Migrar do ASP.NET para o ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0fad0-103">Migrate from ASP.NET to ASP.NET Core</span></span>

<span data-ttu-id="0fad0-104">Por [Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="0fad0-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="0fad0-105">Este artigo serve como um guia de referência para migração de aplicativos ASP.NET para o ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0fad0-105">This article serves as a reference guide for migrating ASP.NET apps to ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0fad0-106">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="0fad0-106">Prerequisites</span></span>

[!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

## <a name="target-frameworks"></a><span data-ttu-id="0fad0-107">Frameworks de destino</span><span class="sxs-lookup"><span data-stu-id="0fad0-107">Target frameworks</span></span>

<span data-ttu-id="0fad0-108">Projetos do ASP.NET Core oferecem aos desenvolvedores a flexibilidade de direcionamento para o .NET Core, .NET Framework ou ambos.</span><span class="sxs-lookup"><span data-stu-id="0fad0-108">ASP.NET Core projects offer developers the flexibility of targeting .NET Core, .NET Framework, or both.</span></span> <span data-ttu-id="0fad0-109">Consulte [Escolhendo entre o .NET Core e .NET Framework para aplicativos de servidor](/dotnet/standard/choosing-core-framework-server) para determinar qual estrutura de destino é mais apropriada.</span><span class="sxs-lookup"><span data-stu-id="0fad0-109">See [Choosing between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) to determine which target framework is most appropriate.</span></span>

<span data-ttu-id="0fad0-110">Ao usar o .NET Framework como destino, projetos precisam fazer referência a pacotes NuGet individuais.</span><span class="sxs-lookup"><span data-stu-id="0fad0-110">When targeting .NET Framework, projects need to reference individual NuGet packages.</span></span>

<span data-ttu-id="0fad0-111">Usar o .NET Core como destino permite que você elimine várias referências de pacote explícitas, graças ao [metapacote](xref:fundamentals/metapackage) do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0fad0-111">Targeting .NET Core allows you to eliminate numerous explicit package references, thanks to the ASP.NET Core [metapackage](xref:fundamentals/metapackage).</span></span> <span data-ttu-id="0fad0-112">Instale o metapacote `Microsoft.AspNetCore.All` em seu projeto:</span><span class="sxs-lookup"><span data-stu-id="0fad0-112">Install the `Microsoft.AspNetCore.All` metapackage in your project:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.1.4" />
</ItemGroup>
```

<span data-ttu-id="0fad0-113">Quando o metapacote é usado, nenhum pacote referenciado no metapacote é implantado com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0fad0-113">When the metapackage is used, no packages referenced in the metapackage are deployed with the app.</span></span> <span data-ttu-id="0fad0-114">O repositório de tempo de execução do .NET Core inclui esses ativos e eles são pré-compilados para melhorar o desempenho.</span><span class="sxs-lookup"><span data-stu-id="0fad0-114">The .NET Core Runtime Store includes these assets, and they're precompiled to improve performance.</span></span> <span data-ttu-id="0fad0-115">Consulte [Metapacote do Microsoft.AspNetCore.All para ASP.NET Core 2.x](xref:fundamentals/metapackage) para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="0fad0-115">See [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.x](xref:fundamentals/metapackage) for more detail.</span></span>

## <a name="project-structure-differences"></a><span data-ttu-id="0fad0-116">Diferenças de estrutura do projeto</span><span class="sxs-lookup"><span data-stu-id="0fad0-116">Project structure differences</span></span>

<span data-ttu-id="0fad0-117">O formato de arquivo *.csproj* foi simplificado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0fad0-117">The *.csproj* file format has been simplified in ASP.NET Core.</span></span> <span data-ttu-id="0fad0-118">Algumas alterações importantes incluem:</span><span class="sxs-lookup"><span data-stu-id="0fad0-118">Some notable changes include:</span></span>

- <span data-ttu-id="0fad0-119">A inclusão explícita de arquivos não é necessária para que eles possam ser considerados como parte do projeto.</span><span class="sxs-lookup"><span data-stu-id="0fad0-119">Explicit inclusion of files isn't necessary for them to be considered part of the project.</span></span> <span data-ttu-id="0fad0-120">Isso reduz o risco de conflitos de mesclagem XML ao trabalhar em grandes equipes.</span><span class="sxs-lookup"><span data-stu-id="0fad0-120">This reduces the risk of XML merge conflicts when working on large teams.</span></span>
- <span data-ttu-id="0fad0-121">Não há referências baseadas em GUID a outros projetos, o que melhora a legibilidade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="0fad0-121">There are no GUID-based references to other projects, which improves file readability.</span></span>
- <span data-ttu-id="0fad0-122">O arquivo pode ser editado sem descarregá-lo no Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="0fad0-122">The file can be edited without unloading it in Visual Studio:</span></span>

    ![Opção de menu de contexto Editar CSPROJ no Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a><span data-ttu-id="0fad0-124">Substituição do arquivo Global.asax</span><span class="sxs-lookup"><span data-stu-id="0fad0-124">Global.asax file replacement</span></span>

<span data-ttu-id="0fad0-125">O ASP.NET Core introduziu um novo mecanismo para inicializar um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0fad0-125">ASP.NET Core introduced a new mechanism for bootstrapping an app.</span></span> <span data-ttu-id="0fad0-126">O ponto de entrada para aplicativos ASP.NET é o arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="0fad0-126">The entry point for ASP.NET applications is the *Global.asax* file.</span></span> <span data-ttu-id="0fad0-127">Tarefas como configuração de roteamento e registros de filtro e de área são tratadas no arquivo *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="0fad0-127">Tasks such as route configuration and filter and area registrations are handled in the *Global.asax* file.</span></span>

[!code-csharp[](samples/globalasax-sample.cs)]

<span data-ttu-id="0fad0-128">Essa abordagem associa o aplicativo e o servidor no qual ele é implantado de uma forma que interfere na implementação.</span><span class="sxs-lookup"><span data-stu-id="0fad0-128">This approach couples the application and the server to which it's deployed in a way that interferes with the implementation.</span></span> <span data-ttu-id="0fad0-129">Em um esforço para desassociar, a [OWIN](http://owin.org/) foi introduzida para fornecer uma maneira mais limpa de usar várias estruturas juntas.</span><span class="sxs-lookup"><span data-stu-id="0fad0-129">In an effort to decouple, [OWIN](http://owin.org/) was introduced to provide a cleaner way to use multiple frameworks together.</span></span> <span data-ttu-id="0fad0-130">A OWIN fornece um pipeline para adicionar somente os módulos necessários.</span><span class="sxs-lookup"><span data-stu-id="0fad0-130">OWIN provides a pipeline to add only the modules needed.</span></span> <span data-ttu-id="0fad0-131">O ambiente de hospedagem leva uma função [Startup](xref:fundamentals/startup) para configurar serviços e pipeline de solicitação do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0fad0-131">The hosting environment takes a [Startup](xref:fundamentals/startup) function to configure services and the app's request pipeline.</span></span> <span data-ttu-id="0fad0-132">`Startup` registra um conjunto de middleware com o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0fad0-132">`Startup` registers a set of middleware with the application.</span></span> <span data-ttu-id="0fad0-133">Para cada solicitação, o aplicativo chama cada um dos componentes de middleware com o ponteiro de cabeçalho de uma lista vinculada para um conjunto existente de manipuladores.</span><span class="sxs-lookup"><span data-stu-id="0fad0-133">For each request, the application calls each of the middleware components with the head pointer of a linked list to an existing set of handlers.</span></span> <span data-ttu-id="0fad0-134">Cada componente de middleware pode adicionar um ou mais manipuladores para a pipeline de tratamento de solicitação.</span><span class="sxs-lookup"><span data-stu-id="0fad0-134">Each middleware component can add one or more handlers to the request handling pipeline.</span></span> <span data-ttu-id="0fad0-135">Isso é feito retornando uma referência para o manipulador que é o novo cabeçalho da lista.</span><span class="sxs-lookup"><span data-stu-id="0fad0-135">This is accomplished by returning a reference to the handler that's the new head of the list.</span></span> <span data-ttu-id="0fad0-136">Cada manipulador é responsável por se lembrar do próximo manipulador na lista e por invocá-lo.</span><span class="sxs-lookup"><span data-stu-id="0fad0-136">Each handler is responsible for remembering and invoking the next handler in the list.</span></span> <span data-ttu-id="0fad0-137">Com o ASP.NET Core, o ponto de entrada para um aplicativo é `Startup` e você não tem mais uma dependência de *Global.asax*.</span><span class="sxs-lookup"><span data-stu-id="0fad0-137">With ASP.NET Core, the entry point to an application is `Startup`, and you no longer have a dependency on *Global.asax*.</span></span> <span data-ttu-id="0fad0-138">Ao usar a OWIN com o .NET Framework, use algo parecido com o seguinte como um pipeline:</span><span class="sxs-lookup"><span data-stu-id="0fad0-138">When using OWIN with .NET Framework, use something like the following as a pipeline:</span></span>

[!code-csharp[](samples/webapi-owin.cs)]

<span data-ttu-id="0fad0-139">Isso configura as rotas padrão e usa XmlSerialization em Json por padrão.</span><span class="sxs-lookup"><span data-stu-id="0fad0-139">This configures your default routes, and defaults to XmlSerialization over Json.</span></span> <span data-ttu-id="0fad0-140">Adicione outro Middleware para este pipeline conforme necessário (carregamento de serviços, definições de configuração, arquivos estáticos, etc.).</span><span class="sxs-lookup"><span data-stu-id="0fad0-140">Add other Middleware to this pipeline as needed (loading services, configuration settings, static files, etc.).</span></span>

<span data-ttu-id="0fad0-141">O ASP.NET Core usa uma abordagem semelhante, mas não depende de OWIN para manipular a entrada.</span><span class="sxs-lookup"><span data-stu-id="0fad0-141">ASP.NET Core uses a similar approach, but doesn't rely on OWIN to handle the entry.</span></span> <span data-ttu-id="0fad0-142">Em vez disso, isso é feito por meio do método `Main` de *Program.cs* (semelhante a aplicativos de console) e `Startup` é carregado por lá.</span><span class="sxs-lookup"><span data-stu-id="0fad0-142">Instead, that's done through the *Program.cs* `Main` method (similar to console applications) and `Startup` is loaded through there.</span></span>

[!code-csharp[](samples/program.cs)]

<span data-ttu-id="0fad0-143">`Startup` deve incluir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="0fad0-143">`Startup` must include a `Configure` method.</span></span> <span data-ttu-id="0fad0-144">Em `Configure`, adicione o middleware necessário ao pipeline.</span><span class="sxs-lookup"><span data-stu-id="0fad0-144">In `Configure`, add the necessary middleware to the pipeline.</span></span> <span data-ttu-id="0fad0-145">No exemplo a seguir (com base no modelo de site da Web padrão), vários métodos de extensão são usados para configurar o pipeline com suporte para:</span><span class="sxs-lookup"><span data-stu-id="0fad0-145">In the following example (from the default web site template), several extension methods are used to configure the pipeline with support for:</span></span>

* [<span data-ttu-id="0fad0-146">BrowserLink</span><span class="sxs-lookup"><span data-stu-id="0fad0-146">BrowserLink</span></span>](http://vswebessentials.com/features/browserlink)
* <span data-ttu-id="0fad0-147">Páginas de erro</span><span class="sxs-lookup"><span data-stu-id="0fad0-147">Error pages</span></span>
* <span data-ttu-id="0fad0-148">Arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="0fad0-148">Static files</span></span>
* <span data-ttu-id="0fad0-149">ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="0fad0-149">ASP.NET Core MVC</span></span>
* <span data-ttu-id="0fad0-150">Identidade</span><span class="sxs-lookup"><span data-stu-id="0fad0-150">Identity</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

<span data-ttu-id="0fad0-151">O host e o aplicativo foram separados, o que fornece a flexibilidade de mover para uma plataforma diferente no futuro.</span><span class="sxs-lookup"><span data-stu-id="0fad0-151">The host and application have been decoupled, which provides the flexibility of moving to a different platform in the future.</span></span>

> [!NOTE]
> <span data-ttu-id="0fad0-152">Para obter uma referência mais aprofundada sobre o Startup do ASP.NET Core e middleware, veja [Startup no ASP.NET Core](xref:fundamentals/startup)</span><span class="sxs-lookup"><span data-stu-id="0fad0-152">For a more in-depth reference to ASP.NET Core Startup and Middleware, see [Startup in ASP.NET Core](xref:fundamentals/startup)</span></span>

## <a name="store-configurations"></a><span data-ttu-id="0fad0-153">Armazenar configurações</span><span class="sxs-lookup"><span data-stu-id="0fad0-153">Store configurations</span></span>

<span data-ttu-id="0fad0-154">O ASP.NET dá suporte ao armazenamento de configurações.</span><span class="sxs-lookup"><span data-stu-id="0fad0-154">ASP.NET supports storing settings.</span></span> <span data-ttu-id="0fad0-155">Essas configurações são usadas, por exemplo, para dar suporte ao ambiente no qual os aplicativos foram implantados.</span><span class="sxs-lookup"><span data-stu-id="0fad0-155">These setting are used, for example, to support the environment to which the applications were deployed.</span></span> <span data-ttu-id="0fad0-156">Uma prática comum era armazenar todos os pares chave-valor personalizados na seção `<appSettings>` do arquivo *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="0fad0-156">A common practice was to store all custom key-value pairs in the `<appSettings>` section of the *Web.config* file:</span></span>

[!code-xml[](samples/webconfig-sample.xml)]

<span data-ttu-id="0fad0-157">Aplicativos leem essas configurações usando a coleção `ConfigurationManager.AppSettings` no namespace `System.Configuration`:</span><span class="sxs-lookup"><span data-stu-id="0fad0-157">Applications read these settings using the `ConfigurationManager.AppSettings` collection in the `System.Configuration` namespace:</span></span>

[!code-csharp[](samples/read-webconfig.cs)]

<span data-ttu-id="0fad0-158">O ASP.NET Core pode armazenar dados de configuração para o aplicativo em qualquer arquivo e carregá-los como parte da inicialização de middleware.</span><span class="sxs-lookup"><span data-stu-id="0fad0-158">ASP.NET Core can store configuration data for the application in any file and load them as part of middleware bootstrapping.</span></span> <span data-ttu-id="0fad0-159">O arquivo padrão usado em modelos de projeto é *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="0fad0-159">The default file used in the project templates is *appsettings.json*:</span></span>

[!code-json[](samples/appsettings-sample.json)]

<span data-ttu-id="0fad0-160">Carregar esse arquivo em uma instância de `IConfiguration` dentro de seu aplicativo é feito em *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0fad0-160">Loading this file into an instance of `IConfiguration` inside your application is done in *Startup.cs*:</span></span>

[!code-csharp[](samples/startup-builder.cs)]

<span data-ttu-id="0fad0-161">O aplicativo lê de `Configuration` para obter as configurações:</span><span class="sxs-lookup"><span data-stu-id="0fad0-161">The app reads from `Configuration` to get the settings:</span></span>

[!code-csharp[](samples/read-appsettings.cs)]

<span data-ttu-id="0fad0-162">Existem extensões para essa abordagem para tornar o processo mais robusto, tais como o uso de DI ([injeção de dependência](xref:fundamentals/dependency-injection)) para carregar um serviço com esses valores.</span><span class="sxs-lookup"><span data-stu-id="0fad0-162">There are extensions to this approach to make the process more robust, such as using [Dependency Injection](xref:fundamentals/dependency-injection) (DI) to load a service with these values.</span></span> <span data-ttu-id="0fad0-163">A abordagem de DI fornece um conjunto fortemente tipado de objetos de configuração.</span><span class="sxs-lookup"><span data-stu-id="0fad0-163">The DI approach provides a strongly-typed set of configuration objects.</span></span>

````csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
````

> [!NOTE]
> <span data-ttu-id="0fad0-164">Para uma referência mais detalhada sobre configuração do ASP.NET Core, veja [Configuração no ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="0fad0-164">For a more in-depth reference to ASP.NET Core configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

## <a name="native-dependency-injection"></a><span data-ttu-id="0fad0-165">Injeção de dependência nativa</span><span class="sxs-lookup"><span data-stu-id="0fad0-165">Native dependency injection</span></span>

<span data-ttu-id="0fad0-166">Uma meta importante ao criar aplicativos escalonáveis e grandes é o acoplamento flexível de componentes e serviços.</span><span class="sxs-lookup"><span data-stu-id="0fad0-166">An important goal when building large, scalable applications is the loose coupling of components and services.</span></span> <span data-ttu-id="0fad0-167">A [injeção de dependência](xref:fundamentals/dependency-injection) é uma técnica popular para conseguir isso e é também um componente nativo do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0fad0-167">[Dependency Injection](xref:fundamentals/dependency-injection) is a popular technique for achieving this, and it's a native component of ASP.NET Core.</span></span>

<span data-ttu-id="0fad0-168">Em aplicativos ASP.NET, os desenvolvedores contam com uma biblioteca de terceiros para implementar injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="0fad0-168">In ASP.NET apps, developers rely on a third-party library to implement Dependency Injection.</span></span> <span data-ttu-id="0fad0-169">Um biblioteca desse tipo é a [Unity](https://github.com/unitycontainer/unity), fornecida pelas Diretrizes da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0fad0-169">One such library is [Unity](https://github.com/unitycontainer/unity), provided by Microsoft Patterns & Practices.</span></span>

<span data-ttu-id="0fad0-170">Um exemplo de configuração da injeção de dependência com Unity é a implementação de `IDependencyResolver`, que encapsula uma `UnityContainer`:</span><span class="sxs-lookup"><span data-stu-id="0fad0-170">An example of setting up Dependency Injection with Unity is implementing `IDependencyResolver` that wraps a `UnityContainer`:</span></span>

[!code-csharp[](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample8.cs)]

<span data-ttu-id="0fad0-171">Crie uma instância de sua `UnityContainer`, registre seu serviço e defina o resolvedor de dependência de `HttpConfiguration` para a nova instância de `UnityResolver` para o contêiner:</span><span class="sxs-lookup"><span data-stu-id="0fad0-171">Create an instance of your `UnityContainer`, register your service, and set the dependency resolver of `HttpConfiguration` to the new instance of `UnityResolver` for your container:</span></span>

[!code-csharp[](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample9.cs)]

<span data-ttu-id="0fad0-172">Injete `IProductRepository` quando necessário:</span><span class="sxs-lookup"><span data-stu-id="0fad0-172">Inject `IProductRepository` where needed:</span></span>

[!code-csharp[](../../../aspnet/web-api/overview/advanced/dependency-injection/samples/sample5.cs)]

<span data-ttu-id="0fad0-173">Já que a injeção de dependência é parte do ASP.NET Core, você pode adicionar o serviço no método `ConfigureServices` de *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="0fad0-173">Because Dependency Injection is part of ASP.NET Core, you can add your service in the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](samples/configure-services.cs)]

<span data-ttu-id="0fad0-174">O repositório pode ser injetado em qualquer lugar, como ocorria com a Unity.</span><span class="sxs-lookup"><span data-stu-id="0fad0-174">The repository can be injected anywhere, as was true with Unity.</span></span>

> [!NOTE]
> <span data-ttu-id="0fad0-175">Para obter mais informações sobre injeção de dependência, confira [Injeção de dependência](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0fad0-175">For more information on dependency injection, see [Dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="0fad0-176">Fornecer arquivos estáticos</span><span class="sxs-lookup"><span data-stu-id="0fad0-176">Serve static files</span></span>

<span data-ttu-id="0fad0-177">Uma parte importante do desenvolvimento da Web é a capacidade de servir ativos estáticos, do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0fad0-177">An important part of web development is the ability to serve static, client-side assets.</span></span> <span data-ttu-id="0fad0-178">Os exemplos mais comuns de arquivos estáticos são HTML, CSS, Javascript e imagens.</span><span class="sxs-lookup"><span data-stu-id="0fad0-178">The most common examples of static files are HTML, CSS, Javascript, and images.</span></span> <span data-ttu-id="0fad0-179">Esses arquivos precisam ser salvos no local de publicação do aplicativo (ou CDN) e referenciados para que eles possam ser carregados por uma solicitação.</span><span class="sxs-lookup"><span data-stu-id="0fad0-179">These files need to be saved in the published location of the app (or CDN) and referenced so they can be loaded by a request.</span></span> <span data-ttu-id="0fad0-180">Esse processo foi alterado no ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0fad0-180">This process has changed in ASP.NET Core.</span></span>

<span data-ttu-id="0fad0-181">No ASP.NET, arquivos estáticos são armazenados em vários diretórios e referenciados nas exibições.</span><span class="sxs-lookup"><span data-stu-id="0fad0-181">In ASP.NET, static files are stored in various directories and referenced in the views.</span></span>

<span data-ttu-id="0fad0-182">No ASP.NET Core, arquivos estáticos são armazenados na "raiz da Web" (*&lt;raiz do conteúdo&gt;/wwwroot*), a menos que configurado de outra forma.</span><span class="sxs-lookup"><span data-stu-id="0fad0-182">In ASP.NET Core, static files are stored in the "web root" (*&lt;content root&gt;/wwwroot*), unless configured otherwise.</span></span> <span data-ttu-id="0fad0-183">Os arquivos são carregados no pipeline de solicitação invocando o método de extensão `UseStaticFiles` de `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="0fad0-183">The files are loaded into the request pipeline by invoking the `UseStaticFiles` extension method from `Startup.Configure`:</span></span>

[!code-csharp[](../../fundamentals/static-files/samples/1x/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> <span data-ttu-id="0fad0-184">Se você usar o .NET Framework como destino, instale o pacote NuGet `Microsoft.AspNetCore.StaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="0fad0-184">If targeting .NET Framework, install the NuGet package `Microsoft.AspNetCore.StaticFiles`.</span></span>

<span data-ttu-id="0fad0-185">Por exemplo, um ativo de imagem na pasta *wwwroot/imagens* está acessível para o navegador em um local como `http://<app>/images/<imageFileName>`.</span><span class="sxs-lookup"><span data-stu-id="0fad0-185">For example, an image asset in the *wwwroot/images* folder is accessible to the browser at a location such as `http://<app>/images/<imageFileName>`.</span></span>

> [!NOTE]
> <span data-ttu-id="0fad0-186">Para obter uma referência mais aprofundada sobre como servir arquivos estáticos no ASP.NET Core, veja [Arquivos estáticos](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="0fad0-186">For a more in-depth reference to serving static files in ASP.NET Core, see [Static files](xref:fundamentals/static-files).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0fad0-187">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0fad0-187">Additional resources</span></span>

- [<span data-ttu-id="0fad0-188">Fazendo a portabilidade de Bibliotecas para o .NET Core</span><span class="sxs-lookup"><span data-stu-id="0fad0-188">Porting Libraries to .NET Core</span></span>](/dotnet/core/porting/libraries)
