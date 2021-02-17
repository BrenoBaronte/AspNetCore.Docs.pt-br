---
title: Criar serviços de back-end para aplicativos móveis nativos com o ASP.NET Core
author: rick-anderson
description: Saiba como criar serviços de back-end usando o ASP.NET Core MVC para dar suporte a aplicativos móveis nativos.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564037"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a>Criar serviços de back-end para aplicativos móveis nativos com o ASP.NET Core

Por [James Montemagno](https://twitter.com/JamesMontemagno)

Os aplicativos móveis podem se comunicar com os serviços de back-end do ASP.NET Core. Para obter instruções sobre como conectar os serviços Web locais dos simuladores do iOS e dos emuladores do Android, confira [Conectar-se aos Serviços Web Locais em simuladores do iOS e emuladores do Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).

[Exibir ou baixar o código de exemplo dos serviços de back-end](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a>Exemplo do aplicativo móvel nativo

Este tutorial demonstra como criar serviços de back-end usando o ASP.NET Core para dar suporte a aplicativos móveis nativos. Ele usa o [aplicativo Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) como seu cliente nativo, que inclui clientes nativos separados para Android, Ios e Windows. Siga o tutorial com links para criar o aplicativo nativo (e instale as ferramentas do Xamarin gratuitas necessárias), além de baixar a solução de exemplo do Xamarin. O exemplo do Xamarin inclui um ASP.NET Core projeto de serviços de API Web, que o aplicativo ASP.NET Core deste artigo substitui (sem alterações exigidas pelo cliente).

![Aplicativo ToDoRest em execução em um smartphone Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a>Recursos

O [aplicativo TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) dá suporte à listagem, adição, exclusão e atualização de itens de To-Do. Cada item tem uma ID, um Nome, Observações e uma propriedade que indica se ele já foi Concluído.

A exibição principal dos itens, conforme mostrado acima, lista o nome de cada item e indica se ele foi concluído com uma marca de seleção.

Tocar no ícone `+` abre uma caixa de diálogo de adição de itens:

![Caixa de diálogo de adição de itens](native-mobile-backend/_static/todo-android-new-item.png)

Tocar em um item na tela da lista principal abre uma caixa de diálogo de edição, na qual o Nome do item, Observações e configurações de Concluído podem ser modificados, ou o item pode ser excluído:

![Caixa de diálogo de edição de itens](native-mobile-backend/_static/todo-android-edit-item.png)

Para testá-lo em relação ao aplicativo ASP.NET Core criado na próxima seção em execução no computador, atualize a constante do aplicativo [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .

Os emuladores do Android não são executados no computador local e usam um IP de loopback (10.0.2.2) para se comunicar com o computador local. Aproveite o [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) para detectar em qual operação o sistema está sendo executado para usar a URL correta.

Navegue até o [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) projeto e abra o [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) arquivo. O arquivo *Constants.cs* contém a configuração a seguir.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

Opcionalmente, você pode implantar o serviço Web em um serviço de nuvem, como o Azure, e atualizar o `RestUrl` .

## <a name="creating-the-aspnet-core-project"></a>Criando o projeto ASP.NET Core

Crie um novo aplicativo Web do ASP.NET Core no Visual Studio. Escolha o modelo de API Web. Nomeie o projeto *TodoAPI*.

![Caixa de diálogo nova do aplicativo Web ASP.NET com modelo de projeto de Web API selecionado](native-mobile-backend/_static/web-api-template.png)

O aplicativo deve responder a todas as solicitações feitas à porta 5000, incluindo o tráfego HTTP de texto não criptografado para nosso cliente móvel. Atualize *Startup.cs* para que <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> não seja executado no desenvolvimento:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> Execute o aplicativo diretamente, em vez de por trás de IIS Express. O IIS Express ignora as solicitações não locais por padrão. Execute [dotnet executado](/dotnet/core/tools/dotnet-run) em um prompt de comando ou escolha o perfil de nome do aplicativo no menu suspenso de destino de depuração na barra de ferramentas do Visual Studio.

Adicione uma classe de modelo para representar itens pendentes. Marque os campos obrigatórios com o `[Required]` atributo:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

Os métodos da API exigem alguma maneira de trabalhar com dados. Use a mesma interface `ITodoRepository` nos usos de exemplo originais do Xamarin:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

Para esta amostra, a implementação apenas usa uma coleção particular de itens:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

Configure a implementação em *Startup.cs*:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a>Criando o controlador

Adicione um novo controlador ao projeto, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs). Ele deve herdar de <xref:Microsoft.AspNetCore.Mvc.ControllerBase> . Adicione um atributo `Route` para indicar que o controlador manipulará as solicitações feitas para caminhos que começam com `api/todoitems`. O token `[controller]` na rota é substituído pelo nome do controlador (com a omissão do sufixo `Controller`) e é especialmente útil para rotas globais. Saiba mais sobre o [roteamento](../fundamentals/routing.md).

O controlador requer um `ITodoRepository` para a função; solicite uma instância desse tipo usando o construtor do controlador. No runtime, esta instância será fornecida com suporte do framework para[injeção de dependência](../fundamentals/dependency-injection.md).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

Essa API é compatível com quatro verbos HTTP diferentes para executar operações CRUD (Criar, Ler, Atualizar, Excluir) na fonte de dados. A mais simples delas é a operação Read, que corresponde a uma solicitação HTTP GET.

### <a name="reading-items"></a>Lendo itens

A solicitação de uma lista de itens é feita com uma solicitação GET ao método `List`. O atributo `[HttpGet]` no método `List` indica que esta ação só deve lidar com as solicitações GET. A rota para esta ação é a rota especificada no controlador. Você não precisa necessariamente usar o nome da ação como parte da rota. Você precisa garantir que cada ação tem uma rota exclusiva e não ambígua. Os atributos de roteamento podem ser aplicados nos níveis de método e controlador para criar rotas específicas.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

O `List` método retorna um código de resposta de 200 OK e todos os itens de tarefas pendentes, serializados como JSON.

Você pode testar o novo método de API usando uma variedade de ferramentas, como [Postman](https://www.getpostman.com/docs/). Veja abaixo:

![Console Postman mostrando uma solicitação GET para todoitems e corpo da resposta mostrando JSON para três itens retornados](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a>Criando itens

Por convenção, a criação de novos itens de dados é mapeada para o verbo HTTP POST. O `Create` método tem um `[HttpPost]` atributo aplicado a ele e aceita uma `TodoItem` instância. Como o `item` argumento é passado no corpo da postagem, esse parâmetro especifica o `[FromBody]` atributo.

Dentro do método, o item é verificado quanto à validade e existência anterior no armazenamento de dados e, se nenhum problema ocorrer, ele será adicionado usando o repositório. A verificação de `ModelState.IsValid` executa a [validação do modelo](../mvc/models/validation.md) e deve ser feita em todos os métodos de API que aceitam a entrada do usuário.

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

O exemplo usa um `enum` que contém códigos de erro que são passados para o cliente móvel:

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

Teste a adição de novos itens usando Postman escolhendo o verbo POST fornecendo o novo objeto no formato JSON no corpo da solicitação. Você também deve adicionar um cabeçalho de solicitação que especifica um `Content-Type` de `application/json`.

![Console Postman mostrando um POST e resposta](native-mobile-backend/_static/postman-post.png)

O método retorna o item recém-criado na resposta.

### <a name="updating-items"></a>Atualizando itens

A modificação de registros é feita com as solicitações HTTP PUT. Além desta mudança, o método `Edit` é quase idêntico ao `Create`. Observe que, se o registro não for encontrado, a ação `Edit` retornará uma resposta `NotFound` (404).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

Para testar com Postman, altere o verbo para PUT. Especifique os dados do objeto atualizado no corpo da solicitação.

![Console Postman mostrando um PUT e resposta](native-mobile-backend/_static/postman-put.png)

Este método retornará uma resposta `NoContent` (204) quando obtiver êxito, para manter a consistência com a API já existente.

### <a name="deleting-items"></a>Excluindo itens

A exclusão de registros é feita por meio da criação de solicitações de exclusão para o serviço e por meio do envio do ID do item a ser excluído. Assim como as atualizações, as solicitações de itens que não existem receberão respostas `NotFound`. Caso contrário, uma solicitação bem-sucedida receberá uma resposta `NoContent` (204).

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

Observe que, ao testar a funcionalidade de exclusão, nada é necessário no Corpo da solicitação.

![Console do Postman mostrando um DELETE e uma resposta](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a>Evitar excesso de postagem

Atualmente, o aplicativo de exemplo expõe o `TodoItem` objeto inteiro. Os aplicativos de produção normalmente limitam os dados que são inseridos e retornados usando um subconjunto do modelo. Há várias razões por trás disso e a segurança é uma importante. O subconjunto de um modelo é geralmente conhecido como um objeto Transferência de Dados (DTO), um modelo de entrada ou um modelo de exibição. O **dto** é usado neste artigo.

Um DTO pode ser usado para:

* Evitar sobrepostos.
* Oculte as propriedades que os clientes não devem exibir.
* Omita algumas propriedades para reduzir o tamanho da carga.
* Mesclar grafos de objeto que contêm objetos aninhados. Os gráficos de objeto achatados podem ser mais convenientes para os clientes.

Para demonstrar a abordagem do DTO, consulte [evitar excesso de postagem](xref:tutorials/first-web-api#prevent-over-posting)

## <a name="common-web-api-conventions"></a>Convenções de Web API comuns

À medida que você desenvolve serviços de back-end para seu aplicativo, desejará criar um conjunto consistente de convenções ou políticas para lidar com preocupações paralelas. Por exemplo, no serviço mostrado acima, as solicitações de registros específicos que não foram encontrados receberam uma resposta `NotFound`, em vez de uma resposta `BadRequest`. Da mesma forma, os comandos feitos para esse serviço que passaram tipos associados a um modelo sempre verificaram `ModelState.IsValid` e retornaram um `BadRequest` para tipos de modelo inválidos.

Depois de identificar uma diretiva comum para suas APIs, você geralmente pode encapsulá-la em um [filtro](../mvc/controllers/filters.md). Saiba mais sobre [como encapsular políticas comuns da API em aplicativos ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).

## <a name="additional-resources"></a>Recursos adicionais

- [Xamarin. Forms: autenticação do serviço Web](/xamarin/xamarin-forms/data-cloud/authentication/)
- [Xamarin. Forms: consumir um serviço Web RESTful](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [Microsoft Learn: consumir serviços Web REST em aplicativos Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn: criar uma API Web com ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
