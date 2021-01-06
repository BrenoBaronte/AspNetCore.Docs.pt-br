---
title: Criar APIs da Web JSON do gRPC
author: jamesnk
description: Saiba como criar APIs HTTP JSON para serviços gRPCs.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: cb2855f0293a6bc800bb5758cd1a8400d4434a24
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96855450"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="7bf8d-103">Criar APIs da Web JSON do gRPC</span><span class="sxs-lookup"><span data-stu-id="7bf8d-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="7bf8d-104">Por [James Newton – King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7bf8d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7bf8d-105">a API HTTP gRPC é um projeto experimental, não um produto confirmado.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="7bf8d-106">Queremos:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-106">We want to:</span></span>
>
> * <span data-ttu-id="7bf8d-107">Teste que nossa abordagem para criar APIs Web JSON para serviços gRPCs funciona.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="7bf8d-108">Obtenha comentários sobre se essa abordagem é útil para os desenvolvedores do .NET.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="7bf8d-109">[Deixe comentários](https://github.com/grpc/grpc-dotnet/issues/167) para garantir que criamos algo que os desenvolvedores gostam e que são produtivos com o.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="7bf8d-110">o gRPC é uma maneira moderna de se comunicar entre aplicativos.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="7bf8d-111">o gRPC usa HTTP/2, transmissão, Protobuf e contratos de mensagem para criar serviços de alto desempenho e em tempo real.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="7bf8d-112">Uma limitação com o gRPC não é cada plataforma que pode usá-lo.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="7bf8d-113">Os navegadores não dão suporte total a HTTP/2, tornando REST e JSON a maneira primária de obter dados em aplicativos de navegador.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="7bf8d-114">Mesmo com os benefícios que o gRPC traz, REST e JSON têm um lugar importante em aplicativos modernos.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="7bf8d-115">A criação de APIs Web gRPC \***e** _ JSON adiciona sobrecarga indesejada ao desenvolvimento de aplicativos.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="7bf8d-116">Este documento discute como criar APIs Web JSON usando os serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="7bf8d-117">API HTTP gRPC</span><span class="sxs-lookup"><span data-stu-id="7bf8d-117">gRPC HTTP API</span></span>

<span data-ttu-id="7bf8d-118">a API HTTP gRPC é uma extensão experimental para ASP.NET Core que cria APIs JSON de RESTful para serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="7bf8d-119">Uma vez configurado, a API HTTP gRPC permite que os aplicativos chamem os serviços gRPC com conceitos HTTP familiares:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="7bf8d-120">_ Verbos HTTP</span><span class="sxs-lookup"><span data-stu-id="7bf8d-120">_ HTTP verbs</span></span>
* <span data-ttu-id="7bf8d-121">Associação de parâmetro de URL</span><span class="sxs-lookup"><span data-stu-id="7bf8d-121">URL parameter binding</span></span>
* <span data-ttu-id="7bf8d-122">Solicitações/respostas JSON</span><span class="sxs-lookup"><span data-stu-id="7bf8d-122">JSON requests/responses</span></span>

<span data-ttu-id="7bf8d-123">gRPC ainda pode ser usado para chamar serviços.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="7bf8d-124">Uso</span><span class="sxs-lookup"><span data-stu-id="7bf8d-124">Usage</span></span>

1. <span data-ttu-id="7bf8d-125">Adicione uma referência de pacote a [Microsoft. AspNetCore. Grpc. HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span><span class="sxs-lookup"><span data-stu-id="7bf8d-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="7bf8d-126">Registre serviços no *Startup.cs* com `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="7bf8d-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="7bf8d-127">Adicione arquivos [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) e [Google/API/Annotations. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) ao seu projeto.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="7bf8d-128">Anote os métodos gRPC em seus arquivos *. proto* com associações http e rotas:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="7bf8d-129">O `SayHello` método gRPC agora pode ser invocado como gRPC + Protobuf e como uma API http:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="7bf8d-130">Solicitação: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="7bf8d-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="7bf8d-131">Resposta: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="7bf8d-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="7bf8d-132">Os logs de servidor mostram que a chamada HTTP é executada por um serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="7bf8d-133">a API HTTP gRPC mapeia a solicitação HTTP de entrada para uma mensagem gRPC e, em seguida, converte a mensagem de resposta em JSON.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="7bf8d-134">Este é um exemplo básico.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-134">This is a basic example.</span></span> <span data-ttu-id="7bf8d-135">Consulte [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) para obter mais opções de personalização.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="enable-swaggeropenapi-support"></a><span data-ttu-id="7bf8d-136">Habilitar suporte a Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="7bf8d-136">Enable Swagger/OpenAPI support</span></span>

<span data-ttu-id="7bf8d-137">O Swagger (OpenAPI) é uma especificação independente de linguagem para descrever as APIs REST.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-137">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="7bf8d-138">a API HTTP gRPC pode ser integrada com [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) para gerar um ponto de extremidade do Swagger para serviços gRPC RESTful.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-138">gRPC HTTP API can integrate with [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) to generate a Swagger endpoint for RESTful gRPC services.</span></span> <span data-ttu-id="7bf8d-139">O ponto de extremidade do Swagger pode ser usado com a [interface do usuário do Swagger](https://swagger.io/swagger-ui/) e outras ferramentas.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-139">The Swagger endpoint can then be used with [Swagger UI](https://swagger.io/swagger-ui/) and other tooling.</span></span>

<span data-ttu-id="7bf8d-140">Para habilitar o Swagger com a API HTTP gRPC:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-140">To enable Swagger with gRPC HTTP API:</span></span>

1. <span data-ttu-id="7bf8d-141">Adicione uma referência de pacote a [Microsoft. AspNetCore. Grpc. Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span><span class="sxs-lookup"><span data-stu-id="7bf8d-141">Add a package reference to [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span></span>
2. <span data-ttu-id="7bf8d-142">Configure swashbuckle em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-142">Configure Swashbuckle in *Startup.cs*.</span></span> <span data-ttu-id="7bf8d-143">O `AddGrpcSwagger` método configura swashbuckle para incluir pontos de extremidade de API http gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-143">The `AddGrpcSwagger` method configures Swashbuckle to include gRPC HTTP API endpoints.</span></span>

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

<span data-ttu-id="7bf8d-144">Para confirmar se swashbuckle está gerando Swagger para os serviços gRPC RESTful, inicie o aplicativo e navegue até a página da interface do usuário do Swagger:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-144">To confirm that Swashbuckle is generating Swagger for the RESTful gRPC services, start the app and navigate to the Swagger UI page:</span></span>

![IU do Swagger](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="7bf8d-146">API HTTP do gRPC vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="7bf8d-146">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="7bf8d-147">Tanto a API HTTP do gRPC quanto o gRPC-Web permitem que os serviços gRPC sejam chamados de um navegador.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-147">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="7bf8d-148">No entanto, a maneira que cada um faz é diferente:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-148">However, the way each does this is different:</span></span>

* <span data-ttu-id="7bf8d-149">gRPC-Web permite que aplicativos de navegador chamem serviços gRPC do navegador com o gRPC-Web Client e Protobuf.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-149">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="7bf8d-150">o gRPC-Web exige que o aplicativo de navegador gere um cliente gRPC e tenha a vantagem de enviar mensagens de Protobuf pequenas e rápidas.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-150">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="7bf8d-151">a API HTTP gRPC permite que aplicativos de navegador chamem serviços gRPC como se fossem APIs RESTful com JSON.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-151">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="7bf8d-152">O aplicativo de navegador não precisa gerar um cliente gRPC nem saber nada sobre gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-152">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="7bf8d-153">Nenhum cliente gerado é criado para a API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-153">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="7bf8d-154">O `Greeter` serviço anterior pode ser chamado usando APIs JavaScript do navegador:</span><span class="sxs-lookup"><span data-stu-id="7bf8d-154">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="7bf8d-155">Status experimental</span><span class="sxs-lookup"><span data-stu-id="7bf8d-155">Experimental status</span></span>

<span data-ttu-id="7bf8d-156">a API HTTP gRPC é um experimento.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-156">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="7bf8d-157">Ele não está completo e não tem suporte.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-157">It is not complete and it is not supported.</span></span> <span data-ttu-id="7bf8d-158">Estamos interessados nessa tecnologia e a capacidade que ela permite que os desenvolvedores de aplicativos criem rapidamente serviços gRPC e JSON ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-158">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="7bf8d-159">Não há compromisso em concluir a API HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-159">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="7bf8d-160">Queremos medir o interesse do desenvolvedor na API HTTP do gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-160">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="7bf8d-161">Se a API HTTP gRPC é interessante para você, [forneça comentários](https://github.com/grpc/grpc-dotnet/issues/167).</span><span class="sxs-lookup"><span data-stu-id="7bf8d-161">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="7bf8d-162">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="7bf8d-162">grpc-gateway</span></span>

<span data-ttu-id="7bf8d-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) é outra tecnologia para criar APIs JSON do RESTful a partir dos serviços do grpc.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="7bf8d-164">Ele usa as mesmas anotações *. proto* para mapear conceitos http para serviços gRPCs.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-164">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="7bf8d-165">A maior diferença entre o grpc-gateway e a API HTTP gRPC é o grpc-gateway usa a geração de código para criar um servidor de proxy reverso.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-165">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="7bf8d-166">O proxy reverso converte as chamadas RESTful em gRPC e as envia para o serviço gRPC.</span><span class="sxs-lookup"><span data-stu-id="7bf8d-166">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="7bf8d-167">Para a instalação e o uso do grpc-gateway, consulte o [Leiame do grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="7bf8d-167">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7bf8d-168">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7bf8d-168">Additional resources</span></span>

* [<span data-ttu-id="7bf8d-169">documentação do Google. API. HttpRule</span><span class="sxs-lookup"><span data-stu-id="7bf8d-169">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
