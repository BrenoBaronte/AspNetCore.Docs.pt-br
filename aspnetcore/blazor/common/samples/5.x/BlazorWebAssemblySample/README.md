# <a name="blazor-webassembly-sample-app"></a><span data-ttu-id="5b6d9-101">Aplicativo de exemplo Webassembly mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="5b6d9-101">Blazor WebAssembly Sample App</span></span>

<span data-ttu-id="5b6d9-102">Este exemplo ilustra o uso de cenários de mais claros descritos na documentação mais simples.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-102">This sample illustrates the use of Blazor scenarios described in the Blazor documentation.</span></span>

## <a name="call-web-api-example"></a><span data-ttu-id="5b6d9-103">Exemplo de API Web de chamada</span><span class="sxs-lookup"><span data-stu-id="5b6d9-103">Call web API example</span></span>

<span data-ttu-id="5b6d9-104">O exemplo de API Web requer uma API Web em execução com base no aplicativo de exemplo para o tópico <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">criar uma API Web com ASP.NET Core</a> , que, por padrão, usa a mesma porta HTTPS (5001) que o aplicativo de exemplo mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-104">The web API example requires a running web API based on the sample app for the <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Create a web API with ASP.NET Core</a> topic, which by default uses the same HTTPS port (5001) as the Blazor sample app.</span></span> <span data-ttu-id="5b6d9-105">Para usar ambos os aplicativos no mesmo computador ao mesmo tempo, altere a porta da API Web (por exemplo, use a porta 10000).</span><span class="sxs-lookup"><span data-stu-id="5b6d9-105">To use both apps on the same machine at the same time, change the port of the web API (for example, use port 10000).</span></span> <span data-ttu-id="5b6d9-106">O aplicativo de exemplo faz solicitações para a API da Web em `https://localhost:10000/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="5b6d9-106">The sample app makes requests to the web API at `https://localhost:10000/api/TodoItems`.</span></span> <span data-ttu-id="5b6d9-107">Se um endereço da API Web diferente for usado, atualize o `ServiceEndpoint` valor constante no bloco do componente Razor `@code` .</span><span class="sxs-lookup"><span data-stu-id="5b6d9-107">If a different web API address is used, update the `ServiceEndpoint` constant value in the Razor component's `@code` block.</span></span></p>

<span data-ttu-id="5b6d9-108">O aplicativo de exemplo faz uma solicitação de <a href="https://docs.microsoft.com/aspnet/core/security/cors">compartilhamento de recursos entre origens (CORS)</a> de `http://localhost:5000` ou `https://localhost:5001` para a API da Web.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-108">The sample app makes a <a href="https://docs.microsoft.com/aspnet/core/security/cors">cross-origin resource sharing (CORS)</a> request from `http://localhost:5000` or `https://localhost:5001` to the web API.</span></span> <span data-ttu-id="5b6d9-109">As credenciais (cookies/cabeçalhos de autorização) são permitidas.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-109">Credentials (authorization cookies/headers) are permitted.</span></span> <span data-ttu-id="5b6d9-110">Adicione a seguinte configuração de middleware CORS ao método da API Web `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="5b6d9-110">Add the following CORS middleware configuration to the web API's `Startup.Configure` method:</span></span></p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

<span data-ttu-id="5b6d9-111">Ajuste os domínios e as portas do `WithOrigins` conforme necessário para o aplicativo mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-111">Adjust the domains and ports of `WithOrigins` as needed for the Blazor app.</span></span>

<span data-ttu-id="5b6d9-112">A API da Web é configurada para CORS para permitir cookies/cabeçalhos de autorização e solicitações do código do cliente, mas a API da Web criada pelo tutorial não autoriza, de fato, solicitações.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-112">The web API is configured for CORS to permit authorization cookies/headers and requests from client code, but the web API as created by the tutorial doesn't actually authorize requests.</span></span> <span data-ttu-id="5b6d9-113">Consulte os <a href="https://docs.microsoft.com/aspnet/core/security/">artigos ASP.NET Core segurança e identidade</a> para obter diretrizes de implementação.</span><span class="sxs-lookup"><span data-stu-id="5b6d9-113">See the <a href="https://docs.microsoft.com/aspnet/core/security/">ASP.NET Core Security and Identity articles</a> for implementation guidance.</span></span>