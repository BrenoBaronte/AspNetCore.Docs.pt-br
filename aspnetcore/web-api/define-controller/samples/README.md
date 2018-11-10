# <a name="aspnet-core-web-api-controller-sample"></a>Exemplo de Controlador da API Web ASP.NET Core

Este aplicativo de exemplo consiste nos seguintes projetos:

- **WebApiSample.Api.22*: um projeto ASP.NET Core 2.2 que tem .NET Core 2.2 como destino.
- **WebApiSample.Api.21**: um projeto ASP.NET Core 2.1 que tem .NET Core 2.1 como destino.
- **WebApiSample.Api.Pre21**: um projeto ASP.NET Core 2.0 que tem .NET Core 2.0 como destino.
- **WebApiSample.DataAccess**: uma biblioteca de classes .NET Standard 2.0 que atua como uma camada de acesso a dados para os projetos da API Web 2.

Este exemplo mostra variações da criação do controlador da API Web:

- [Derivar a classe do ControllerBase](https://docs.microsoft.com/aspnet/core/web-api#derive-class-from-controllerbase)
- [Anotar classe com o ApiControllerAttribute](https://docs.microsoft.com/aspnet/core/web-api#annotate-class-with-apicontrollerattribute)
