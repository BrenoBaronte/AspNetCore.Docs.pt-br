---
title: Solucionar problemas do gRPC no .NET Core
author: jamesnk
description: Solucionar erros ao usar o gRPC no .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: 61d4d2204886f26b4ff55bc876825012809f1dfa
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253092"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="24c84-103">Solucionar problemas do gRPC no .NET Core</span><span class="sxs-lookup"><span data-stu-id="24c84-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="24c84-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="24c84-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="24c84-105">Este documento aborda problemas comumente encontrados ao desenvolver aplicativos gRPC no .NET.</span><span class="sxs-lookup"><span data-stu-id="24c84-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="24c84-106">Incompatibilidade entre a configuração de SSL/TLS do cliente e do serviço</span><span class="sxs-lookup"><span data-stu-id="24c84-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="24c84-107">O modelo gRPC e os exemplos usam o protocolo TLS para proteger os serviços [do](https://tools.ietf.org/html/rfc5246) gRPC por padrão.</span><span class="sxs-lookup"><span data-stu-id="24c84-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="24c84-108">Os clientes gRPC precisam usar uma conexão segura para chamar os serviços gRPC protegidos com êxito.</span><span class="sxs-lookup"><span data-stu-id="24c84-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="24c84-109">Você pode verificar se o serviço gRPC do ASP.NET Core está usando o TLS nos logs gravados no início do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24c84-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="24c84-110">O serviço escutará em um ponto de extremidade HTTPS:</span><span class="sxs-lookup"><span data-stu-id="24c84-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="24c84-111">O cliente .NET Core deve usar `https` no endereço do servidor para fazer chamadas com uma conexão segura:</span><span class="sxs-lookup"><span data-stu-id="24c84-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="24c84-112">Todas as implementações de cliente do gRPC dão suporte a TLS.</span><span class="sxs-lookup"><span data-stu-id="24c84-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="24c84-113">Os clientes gRPC de outras linguagens normalmente exigem o canal configurado com o `SslCredentials` .</span><span class="sxs-lookup"><span data-stu-id="24c84-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="24c84-114">`SslCredentials` Especifica o certificado que o cliente usará e deve ser usado em vez de credenciais não seguras.</span><span class="sxs-lookup"><span data-stu-id="24c84-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="24c84-115">Para obter exemplos de como configurar as diferentes implementações de cliente gRPC para usar o TLS, consulte [autenticação do gRPC](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="24c84-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="24c84-116">Chamar um serviço gRPC com um certificado não confiável/inválido</span><span class="sxs-lookup"><span data-stu-id="24c84-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="24c84-117">O cliente .NET gRPC requer que o serviço tenha um certificado confiável.</span><span class="sxs-lookup"><span data-stu-id="24c84-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="24c84-118">A seguinte mensagem de erro é retornada ao chamar um serviço gRPC sem um certificado confiável:</span><span class="sxs-lookup"><span data-stu-id="24c84-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="24c84-119">Exceção sem tratamento.</span><span class="sxs-lookup"><span data-stu-id="24c84-119">Unhandled exception.</span></span> <span data-ttu-id="24c84-120">Sistema .net. http. HttpRequestexception: não foi possível estabelecer a conexão SSL, consulte a exceção interna.</span><span class="sxs-lookup"><span data-stu-id="24c84-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="24c84-121">---> System.Security.Authentication.AuthenticationException: o certificado remoto é inválido de acordo com o procedimento de validação.</span><span class="sxs-lookup"><span data-stu-id="24c84-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="24c84-122">Você poderá ver esse erro se estiver testando seu aplicativo localmente e o ASP.NET Core certificado de desenvolvimento HTTPS não for confiável.</span><span class="sxs-lookup"><span data-stu-id="24c84-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="24c84-123">Para obter instruções sobre como corrigir esse problema, confira [Confiar no certificado de desenvolvimento HTTPS do ASP.NET Core no Windows e no macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="24c84-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="24c84-124">Se você estiver chamando um serviço gRPC em outro computador e não puder confiar no certificado, o cliente gRPC poderá ser configurado para ignorar o certificado inválido.</span><span class="sxs-lookup"><span data-stu-id="24c84-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="24c84-125">O código a seguir usa [HttpClientHandler. ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) para permitir chamadas sem um certificado confiável:</span><span class="sxs-lookup"><span data-stu-id="24c84-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="24c84-126">Certificados não confiáveis só devem ser usados durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24c84-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="24c84-127">Os aplicativos de produção sempre devem usar certificados válidos.</span><span class="sxs-lookup"><span data-stu-id="24c84-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="24c84-128">Chamar serviços gRPCs inseguros com o cliente .NET Core</span><span class="sxs-lookup"><span data-stu-id="24c84-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="24c84-129">Quando um aplicativo está usando o .NET Core 3. x, é necessária configuração adicional para chamar serviços gRPCs inseguros com o cliente .NET Core.</span><span class="sxs-lookup"><span data-stu-id="24c84-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="24c84-130">O cliente gRPC deve definir o `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` comutador para `true` e usar `http` no endereço do servidor:</span><span class="sxs-lookup"><span data-stu-id="24c84-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="24c84-131">Os aplicativos .NET 5 não precisam de configuração adicional, mas para chamar serviços gRPCs inseguros eles devem usar a `Grpc.Net.Client` versão 2.32.0 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="24c84-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="24c84-132">Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS</span><span class="sxs-lookup"><span data-stu-id="24c84-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="24c84-133">O Kestrel não dá suporte a HTTP/2 com TLS no macOS e versões mais antigas do Windows, como o Windows 7.</span><span class="sxs-lookup"><span data-stu-id="24c84-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="24c84-134">O modelo ASP.NET Core gRPC e os exemplos usam TLS por padrão.</span><span class="sxs-lookup"><span data-stu-id="24c84-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="24c84-135">Você verá a seguinte mensagem de erro ao tentar iniciar o servidor gRPC:</span><span class="sxs-lookup"><span data-stu-id="24c84-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="24c84-136">Não é possível associar ao https://localhost:5001 na interface de loopback IPv4: ' http/2 sobre TLS não tem suporte no MacOS devido ao suporte de ALPN ausente. '.</span><span class="sxs-lookup"><span data-stu-id="24c84-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="24c84-137">Para contornar esse problema, configure o Kestrel e o cliente gRPC para usar HTTP/2 *sem* TLS.</span><span class="sxs-lookup"><span data-stu-id="24c84-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="24c84-138">Você só deve fazer isso durante o desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="24c84-138">You should only do this during development.</span></span> <span data-ttu-id="24c84-139">Não usar o TLS fará com que as mensagens gRPC sejam enviadas sem criptografia.</span><span class="sxs-lookup"><span data-stu-id="24c84-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="24c84-140">Kestrel deve configurar um ponto de extremidade HTTP/2 sem TLS em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="24c84-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="24c84-141">Quando um ponto de extremidade HTTP/2 é configurado sem TLS, o [ListenerOptions](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) do ponto de extremidade deve ser definido como `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="24c84-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="24c84-142">`HttpProtocols.Http1AndHttp2` Não pode ser usado porque o TLS é necessário para negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="24c84-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="24c84-143">Sem o TLS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="24c84-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="24c84-144">Quando um ponto de extremidade HTTP/2 é configurado sem TLS, o [ListenerOptions](xref:fundamentals/servers/kestrel#listenoptionsprotocols) do ponto de extremidade deve ser definido como `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="24c84-144">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="24c84-145">`HttpProtocols.Http1AndHttp2` Não pode ser usado porque o TLS é necessário para negociar HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="24c84-145">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="24c84-146">Sem o TLS, todas as conexões com o ponto de extremidade padrão para HTTP/1.1 e chamadas gRPC falham.</span><span class="sxs-lookup"><span data-stu-id="24c84-146">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>
::: moniker-end

<span data-ttu-id="24c84-147">O cliente gRPC também deve ser configurado para não usar o TLS.</span><span class="sxs-lookup"><span data-stu-id="24c84-147">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="24c84-148">Para obter mais informações, consulte [chamar serviços gRPCs inseguros com o cliente .NET Core](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="24c84-148">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="24c84-149">O HTTP/2 sem TLS só deve ser usado durante o desenvolvimento do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="24c84-149">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="24c84-150">Os aplicativos de produção sempre devem usar a segurança de transporte.</span><span class="sxs-lookup"><span data-stu-id="24c84-150">Production apps should always use transport security.</span></span> <span data-ttu-id="24c84-151">Para obter mais informações, consulte [Security Considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="24c84-151">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="24c84-152">os ativos do C# gRPC não são gerados pelo código de arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="24c84-152">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="24c84-153">a geração de código gRPC de clientes concretos e classes base de serviço requer que os arquivos e as ferramentas do protobuf sejam referenciados de um projeto.</span><span class="sxs-lookup"><span data-stu-id="24c84-153">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="24c84-154">Você deve incluir:</span><span class="sxs-lookup"><span data-stu-id="24c84-154">You must include:</span></span>

* <span data-ttu-id="24c84-155">arquivos *. proto* que você deseja usar no `<Protobuf>` grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="24c84-155">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="24c84-156">[Arquivos *. proto* importados](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) devem ser referenciados pelo projeto.</span><span class="sxs-lookup"><span data-stu-id="24c84-156">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="24c84-157">Referência de pacote para o pacote de ferramentas do gRPC [gRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/).</span><span class="sxs-lookup"><span data-stu-id="24c84-157">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="24c84-158">Para obter mais informações sobre como gerar ativos do gRPC C#, consulte <xref:grpc/basics> .</span><span class="sxs-lookup"><span data-stu-id="24c84-158">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="24c84-159">Um ASP.NET Core aplicativo Web que hospeda serviços gRPCs precisa apenas da classe base de serviço gerada:</span><span class="sxs-lookup"><span data-stu-id="24c84-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="24c84-160">Um aplicativo cliente gRPC que faz chamadas gRPC precisa apenas do cliente concreto gerado:</span><span class="sxs-lookup"><span data-stu-id="24c84-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="24c84-161">Projetos do WPF não podem gerar ativos gRPC C# a partir de arquivos. proto</span><span class="sxs-lookup"><span data-stu-id="24c84-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="24c84-162">Os projetos do WPF têm um [problema conhecido](https://github.com/dotnet/wpf/issues/810) que impede a geração de código gRPC de funcionar corretamente.</span><span class="sxs-lookup"><span data-stu-id="24c84-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="24c84-163">Quaisquer tipos de gRPC gerados em um projeto do WPF referenciando `Grpc.Tools` e *. proto* arquivos criarão erros de compilação quando usados:</span><span class="sxs-lookup"><span data-stu-id="24c84-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="24c84-164">erro CS0246: não foi possível encontrar o nome do namespace ou tipo ' MyGrpcServices ' (está faltando uma diretiva using ou uma referência de assembly?)</span><span class="sxs-lookup"><span data-stu-id="24c84-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="24c84-165">Você pode solucionar esse problema:</span><span class="sxs-lookup"><span data-stu-id="24c84-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="24c84-166">Crie um novo projeto de biblioteca de classes do .NET Core.</span><span class="sxs-lookup"><span data-stu-id="24c84-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="24c84-167">No novo projeto, adicione referências para habilitar a [geração de código C# a partir de arquivos *\* . proto*](xref:grpc/basics#generated-c-assets):</span><span class="sxs-lookup"><span data-stu-id="24c84-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="24c84-168">Adicione uma referência de pacote ao pacote [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .</span><span class="sxs-lookup"><span data-stu-id="24c84-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="24c84-169">Adicione arquivos *\* . proto* ao `<Protobuf>` grupo de itens.</span><span class="sxs-lookup"><span data-stu-id="24c84-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="24c84-170">No aplicativo do WPF, adicione uma referência ao novo projeto.</span><span class="sxs-lookup"><span data-stu-id="24c84-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="24c84-171">O aplicativo WPF pode usar os tipos gerados gRPC do novo projeto de biblioteca de classes.</span><span class="sxs-lookup"><span data-stu-id="24c84-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
