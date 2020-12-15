---
title: Configuração de ASP.NET Core Blazor
author: guardrex
description: Saiba mais sobre a configuração de Blazor aplicativos, incluindo configurações de aplicativo, autenticação e configuração de log.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485961"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="62517-103">Configuração de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="62517-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="62517-104">Este tópico aplica-se a Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="62517-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="62517-105">Para obter diretrizes gerais sobre a configuração do aplicativo ASP.NET Core, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="62517-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="62517-106">Blazor WebAssembly carrega a configuração dos seguintes arquivos de configurações de aplicativo por padrão:</span><span class="sxs-lookup"><span data-stu-id="62517-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="62517-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="62517-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="62517-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, em que o `{ENVIRONMENT}` espaço reservado é o [ambiente de tempo de execução](xref:fundamentals/environments)do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62517-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="62517-109">Outros provedores de configuração registrados pelo aplicativo também podem fornecer configuração, mas nem todos os provedores ou recursos de provedor são apropriados para Blazor WebAssembly aplicativos:</span><span class="sxs-lookup"><span data-stu-id="62517-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="62517-110">[Azure Key Vault provedor de configuração](xref:security/key-vault-configuration): o provedor não tem suporte para identidade gerenciada e ID do aplicativo (ID do cliente) com cenários de segredo do cliente.</span><span class="sxs-lookup"><span data-stu-id="62517-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="62517-111">A ID do aplicativo com um segredo do cliente não é recomendada para nenhum aplicativo ASP.NET Core, especialmente Blazor WebAssembly aplicativos porque o segredo do cliente não pode ser protegido do lado do cliente para acessar o serviço Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="62517-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="62517-112">[Provedor de configuração de Azure app](/azure/azure-app-configuration/quickstart-aspnet-core-app): o provedor não é apropriado para Blazor WebAssembly aplicativos porque os Blazor WebAssembly aplicativos não são executados em um servidor no Azure.</span><span class="sxs-lookup"><span data-stu-id="62517-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="62517-113">A configuração em um Blazor WebAssembly aplicativo é visível para os usuários.</span><span class="sxs-lookup"><span data-stu-id="62517-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="62517-114">**Não armazene segredos do aplicativo, credenciais ou quaisquer outros dados confidenciais na configuração de um Blazor WebAssembly aplicativo.**</span><span class="sxs-lookup"><span data-stu-id="62517-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="62517-115">Para obter mais informações sobre provedores de configuração, consulte <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="62517-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="62517-116">Configuração de configurações do aplicativo</span><span class="sxs-lookup"><span data-stu-id="62517-116">App settings configuration</span></span>

<span data-ttu-id="62517-117">A configuração em arquivos de configurações do aplicativo é carregada por padrão.</span><span class="sxs-lookup"><span data-stu-id="62517-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="62517-118">No exemplo a seguir, um valor de configuração de interface do usuário é armazenado em um arquivo de configurações do aplicativo e carregado pela Blazor estrutura automaticamente.</span><span class="sxs-lookup"><span data-stu-id="62517-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="62517-119">O valor é lido por um componente.</span><span class="sxs-lookup"><span data-stu-id="62517-119">The value is read by a component.</span></span>

<span data-ttu-id="62517-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="62517-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="62517-121">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="62517-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="62517-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="62517-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="62517-123">Para ler outros arquivos de configuração da `wwwroot` pasta para a configuração, use um <xref:System.Net.Http.HttpClient> para obter o conteúdo do arquivo.</span><span class="sxs-lookup"><span data-stu-id="62517-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="62517-124">O exemplo a seguir lê um arquivo de configuração ( `cars.json` ) na configuração do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62517-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="62517-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="62517-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="62517-126">Adicione o namespace para <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="62517-127">No `Program.Main` `Program.cs` , modifique o registro de <xref:System.Net.Http.HttpClient> serviço existente para usar o cliente do para ler o arquivo:</span><span class="sxs-lookup"><span data-stu-id="62517-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="62517-128">Provedor de configuração personalizada com EF Core</span><span class="sxs-lookup"><span data-stu-id="62517-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="62517-129">O provedor de configuração personalizada com EF Core demonstrado no <xref:fundamentals/configuration/index#custom-configuration-provider> funciona com Blazor WebAssembly aplicativos.</span><span class="sxs-lookup"><span data-stu-id="62517-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="62517-130">As cadeias de caracteres de conexão de banco de dados e os bancos de dados carregados com Blazor WebAssembly aplicativos não são seguros e não devem ser usados para armazená-los</span><span class="sxs-lookup"><span data-stu-id="62517-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="62517-131">Adicione referências de pacote para [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) e [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) ao arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62517-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="62517-132">Adicione as classes de configuração de EF Core descritas em <xref:fundamentals/configuration/index#custom-configuration-provider> .</span><span class="sxs-lookup"><span data-stu-id="62517-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="62517-133">Adicionar namespaces para <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> e <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="62517-134">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="62517-135">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="62517-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="62517-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="62517-136">`Pages/EFCoreConfig.razor`:</span></span>

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="62517-137">Fonte de configuração de memória</span><span class="sxs-lookup"><span data-stu-id="62517-137">Memory Configuration Source</span></span>

<span data-ttu-id="62517-138">O exemplo a seguir usa um <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> no `Program.Main` para fornecer configuração adicional.</span><span class="sxs-lookup"><span data-stu-id="62517-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="62517-139">Adicione o namespace para <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="62517-140">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-140">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="62517-141">Injetar uma <xref:Microsoft.Extensions.Configuration.IConfiguration> instância em um componente para acessar os dados de configuração.</span><span class="sxs-lookup"><span data-stu-id="62517-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="62517-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="62517-142">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="62517-143">Obtenha uma seção da configuração em código C# com <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="62517-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="62517-144">O exemplo a seguir obtém a `wheels` seção para a configuração no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="62517-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="62517-145">Configuração de autenticação</span><span class="sxs-lookup"><span data-stu-id="62517-145">Authentication configuration</span></span>

<span data-ttu-id="62517-146">Forneça a configuração de autenticação em um arquivo de configurações do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62517-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="62517-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="62517-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="62517-148">Carregue a configuração de um Identity provedor com o <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="62517-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="62517-149">O exemplo a seguir carrega a configuração de um [provedor OIDC](xref:blazor/security/webassembly/standalone-with-authentication-library).</span><span class="sxs-lookup"><span data-stu-id="62517-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="62517-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="62517-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="62517-151">Configuração de log</span><span class="sxs-lookup"><span data-stu-id="62517-151">Logging configuration</span></span>

<span data-ttu-id="62517-152">Adicione uma referência de pacote para [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) o arquivo de projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="62517-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="62517-153">No arquivo de configurações do aplicativo, forneça a configuração de log.</span><span class="sxs-lookup"><span data-stu-id="62517-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="62517-154">A configuração de log é carregada no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="62517-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="62517-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="62517-155">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="62517-156">Adicione o namespace para <xref:Microsoft.Extensions.Logging?displayProperty=fullName> para `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="62517-157">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="62517-158">Configuração do host Builder</span><span class="sxs-lookup"><span data-stu-id="62517-158">Host builder configuration</span></span>

<span data-ttu-id="62517-159">Ler a configuração do host Builder de <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> no `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="62517-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="62517-160">Em `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="62517-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="62517-161">Configuração armazenada em cache</span><span class="sxs-lookup"><span data-stu-id="62517-161">Cached configuration</span></span>

<span data-ttu-id="62517-162">Os arquivos de configuração são armazenados em cache para uso offline.</span><span class="sxs-lookup"><span data-stu-id="62517-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="62517-163">Com [PWAs (aplicativos Web progressivos)](xref:blazor/progressive-web-app), você só pode atualizar arquivos de configuração ao criar uma nova implantação.</span><span class="sxs-lookup"><span data-stu-id="62517-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="62517-164">A edição de arquivos de configuração entre implantações não tem efeito porque:</span><span class="sxs-lookup"><span data-stu-id="62517-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="62517-165">Os usuários têm versões em cache dos arquivos que eles continuam a usar.</span><span class="sxs-lookup"><span data-stu-id="62517-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="62517-166">Os `service-worker.js` arquivos e do PWA `service-worker-assets.js` devem ser recriados na compilação, que sinaliza ao aplicativo no próximo online do usuário que o aplicativo foi reimplantado.</span><span class="sxs-lookup"><span data-stu-id="62517-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="62517-167">Para obter mais informações sobre como as atualizações em segundo plano são manipuladas pelo PWAs, consulte <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="62517-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
