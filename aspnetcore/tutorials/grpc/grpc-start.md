---
title: Criar um cliente e um servidor gRPC do .NET Core no ASP.NET Core
author: juntaoluo
description: Este tutorial mostra como criar um serviço gRPC e um cliente gRPC no ASP.NET Core. Saiba como criar um projeto de serviço gRPC, editar um arquivo pronto e adicionar uma chamada de streaming duplex.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424250"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Tutorial: criar um cliente gRPC e um servidor no ASP.NET Core

Por [John Luo](https://github.com/juntaoluo)

Este tutorial mostra como criar um cliente [gRPC](https://grpc.io/docs/guides/) do .NET Core e um servidor gRPC do ASP.NET Core.

No final, você terá um cliente gRPC que se comunica com o serviço de Boas-vindas do gRPC.

[Exiba ou baixe o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([como baixar](xref:index#how-to-download-a-sample)).

Neste tutorial, você:

> [!div class="checklist"]
> * Criará um servidor gRPC.
> * Criará um cliente gRPC.
> * Testará o serviço do cliente gRPC com o serviço Greeter do gRPC.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a>Criar um serviço gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Inicie o Visual Studio e selecione **Criar um projeto**. Como alternativa, no menu **Arquivo** do Visual Studio, selecione **Novo** > **Projeto**.
* Na caixa de diálogo **criar um novo projeto** , selecione **serviço GRPC** e selecione **Avançar**:

  ![Caixa de diálogo criar um novo projeto](~/tutorials/grpc/grpc-start/static/cnp.png)

* Nomeie o projeto **GrpcGreeter**. É importante nomear o projeto *GrpcGreeter* de modo que os namespaces façam a correspondência quando você copiar e colar o código.
* Selecione **Criar**.
* Na caixa de diálogo **Criar um novo serviço gRPC**:
  * O modelo de **Serviço gRPC** está selecionado.
  * Selecione **Criar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute os comandos a seguir:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * O comando `dotnet new` cria um novo serviço gRPC na pasta *GrpcGreeter*.
  * O comando `code` abre a pasta *GrpcGreeter* em uma nova instância do Visual Studio Code.

  Uma caixa de diálogo aparece com os **ativos necessários para compilação e depuração estão ausentes em ' GrpcGreeter '. Adicioná-los?**
* Selecione **Sim** na barra superior.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Em um terminal, execute os seguintes comandos:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Os comandos anteriores usam a [CLI do .NET Core](/dotnet/core/tools/dotnet) para criar um serviço gRPC.

### <a name="open-the-project"></a>Abrir o projeto

No Visual Studio, selecione **arquivo**  >  **abrir**e, em seguida, selecione o arquivo *GrpcGreeter. csproj* .

---

### <a name="run-the-service"></a>Executar o serviço

  [!INCLUDE[](~/includes/run-the-app.md)]

Os logs mostram o serviço escutando em `https://localhost:5001`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> O modelo gRPC é configurado para usar [o protocolo TLS](https://tools.ietf.org/html/rfc5246). Os clientes gRPC precisam usar HTTPS para chamar o servidor.
>
> O macOS não é compatível com gRPC do ASP.NET Core com TLS. É necessária uma configuração adicional para executar com êxito os serviços gRPC no macOS. Para obter mais informações, confira [Não é possível iniciar o aplicativo ASP.NET Core gRPC no macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Examinar os arquivos de projeto

Arquivos de projeto *GrpcGreeter*:

* *Greet. proto*: o arquivo *Protos/Greet. proto* define o `Greeter` gRPC e é usado para gerar os ativos do servidor gRPC. Para obter mais informações, confira [Introdução ao gRPC](xref:grpc/index).
* Pasta de *Serviços* : contém a implementação do `Greeter` serviço.
* *appSettings.jsem*: contém dados de configuração, como o protocolo usado pelo Kestrel. Para obter mais informações, consulte <xref:fundamentals/configuration/index>.
* *Program.cs*: contém o ponto de entrada para o serviço gRPC. Para obter mais informações, consulte <xref:fundamentals/host/generic-host>.
* *Startup.cs*: contém o código que configura o comportamento do aplicativo. Para obter mais informações, veja [Inicialização do aplicativo](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Criar o cliente gRPC em um aplicativo de console .NET

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Abra uma segunda instância do Visual Studio e selecione **Criar um novo projeto**.
* Na caixa de diálogo **Criar um novo projeto**, selecione **Aplicativo de Console (.NET Core)** e **Avançar**.
* Na caixa de texto **nome do projeto** , digite **GrpcGreeterClient** e selecione **criar**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Abra o [terminal integrado](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Altere os diretórios (`cd`) para uma pasta que conterá o projeto.
* Execute os comandos a seguir:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

Siga as instruções em [Compilar uma solução completa do .NET Core no macOS usando o Visual Studio para Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) para criar um aplicativo de console com o nome *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Adicionar os pacotes necessários

O projeto cliente gRPC requer os seguintes pacotes:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), que contém o cliente do .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), que contém APIs de mensagem protobuf para C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), que contém o suporte a ferramentas C# para arquivos protobuf. O pacote de ferramentas não é necessário em runtime e, portanto, a dependência é marcada com `PrivateAssets="All"`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Instalar os pacotes usando o PMC (Console do Gerenciador de Pacotes) ou Gerenciar Pacotes NuGet.

#### <a name="pmc-option-to-install-packages"></a>Opção do PMC para instalar pacotes

* No Visual Studio, selecione **ferramentas**  >  **Gerenciador de pacotes NuGet**  >  **console do Gerenciador de pacotes**
* Na janela do **Console do Gerenciador de Pacotes**, execute `cd GrpcGreeterClient` para alterar os diretórios para a pasta que contém os arquivos *GrpcGreeterClient.csproj*.
* Execute os comandos a seguir:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Opção Gerenciar Pacotes NuGet para instalar pacotes

* Clique com o botão direito do mouse no projeto no **Gerenciador de soluções**  >  **gerenciar pacotes NuGet**
* Selecione a guia **Procurar**.
* Insira **Grpc.Net.Client** na caixa de pesquisa.
* Selecione o pacote **Grpc.Net.Client** na guia **Procurar** e selecione **Instalar**.
* Repita para `Google.Protobuf` e `Grpc.Tools`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Execute os comandos a seguir no **Terminal Integrado**:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Clique com o botão direito do mouse na pasta **Pacotes** em **Painel de Soluções** > **Adicionar Pacotes**
* Insira **Grpc.Net.Client** na caixa de pesquisa.
* Selecione o pacote **Grpc.Net.Client** no painel de resultados e selecione **Adicionar Pacote**
* Repita para `Google.Protobuf` e `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Adicionar greet.proto

* Crie uma pasta *Protos* no projeto do cliente gRPC.
* Copie o arquivo *Protos\greet.proto* do serviço de Boas-vindas do gRPC para o projeto de cliente gRPC.
* Atualize o namespace dentro do `greet.proto` arquivo para o namespace do projeto:

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* Edite o arquivo de projeto *GrpcGreeterClient.csproj*:

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Clique com o botão direito do mouse no projeto e selecione **Editar Arquivo de Projeto**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Selecione o arquivo *GrpcGreeterClient.csproj*.

  # <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

  Clique com o botão direito do mouse no projeto e selecione **ferramentas**  >  **Editar arquivo**.

  ---

* Adicione um grupo de itens com um elemento `<Protobuf>` que faça referência ao arquivo *greet.proto*:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Criar o cliente Greeter

Compile o projeto para criar os tipos no namespace `GrpcGreeter`. Os tipos `GrpcGreeter` são gerados automaticamente pelo processo de build.

Atualize o arquivo *Program.cs* do cliente gRPC com o seguinte código:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* contém o ponto de entrada e lógica para o cliente gRPC.

O cliente Greeter é criado da seguinte forma:

* Criando uma instância de `GrpcChannel` que contém as informações para criar a conexão com o serviço gRPC.
* Como usar o `GrpcChannel` para construir o cliente Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

O cliente Greeter chama o método `SayHello` assíncrono. O resultado da chamada `SayHello` é exibido:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testar o cliente gRPC com o serviço de Boas-vindas do gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* No serviço Greeter, pressione `Ctrl+F5` para iniciar o servidor sem o depurador.
* No projeto `GrpcGreeterClient`, pressione `Ctrl+F5` para iniciar o cliente sem o depurador.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Inicie o serviço Greeter.
* Inicie o cliente.


# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

* Inicie o serviço Greeter.
* Inicie o cliente.

---

O cliente envia uma saudação para o serviço com uma mensagem contendo seu nome, *GreeterClient*. O serviço envia a mensagem "Olá, GreeterClient" como uma resposta. A resposta "Olá, GreeterClient" é exibida no prompt de comando:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

O serviço gRPC registra os detalhes da chamada bem-sucedida nos logs gravados no prompt de comando:

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> O código neste artigo requer o certificado de desenvolvimento HTTPS do ASP.NET Core para proteger o serviço gRPC. Se o cliente .NET gRPC falhar com a mensagem `The remote certificate is invalid according to the validation procedure.` ou `The SSL connection could not be established.` , o certificado de desenvolvimento não será confiável. Para corrigir esse problema, consulte [chamar um serviço gRPC com um certificado não confiável/inválido](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Próximas etapas

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
