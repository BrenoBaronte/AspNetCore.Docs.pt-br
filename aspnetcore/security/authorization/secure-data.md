---
title: Criar um aplicativo ASP.NET Core com dados de usuário protegidos por autorização
author: rick-anderson
description: Saiba como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Inclui HTTPS, autenticação, segurança, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051935"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização

Por [Rick Anderson](https://twitter.com/RickAndMSFT) e [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

Consulte [este PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram. Há três grupos de segurança:

* **Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.
* **Os gerentes** podem aprovar ou rejeitar dados de contato. Somente contatos aprovados são visíveis para os usuários.
* **Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.

As imagens neste documento não correspondem exatamente aos modelos mais recentes.

Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado. Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos. Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** . Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:

![Exibição do gerente de um contato](secure-data/_static/manager.png)

Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.

Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

O administrador tem todos os privilégios. Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.

O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

O exemplo contém os seguintes manipuladores de autorização:

* `ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.
* `ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.
* `ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial é avançado. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticação](xref:security/authentication/identity)
* [Confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)
* [Autorização](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>O aplicativo iniciador e concluído

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.

### <a name="the-starter-app"></a>O aplicativo inicial

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.

## <a name="secure-user-data"></a>Proteger dados do usuário

As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro. Talvez você ache útil fazer referência ao projeto concluído.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular os dados de contato ao usuário

Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários. Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados. O `Status` campo determina se um contato é visível por usuários gerais.

Crie uma nova migração e atualize o banco de dados:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Adicionar serviços de função a Identity

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Exigir usuários autenticados

Defina a política de autenticação de fallback para exigir que os usuários sejam autenticados:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

O código realçado anterior define a [política de autenticação de fallback](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy). A política de autenticação de fallback exige que * *_todos_* os usuários sejam autenticados, exceto por Razor páginas, controladores ou métodos de ação com um atributo de autenticação. Por exemplo, Razor páginas, controladores ou métodos de ação com `[AllowAnonymous]` ou `[Authorize(PolicyName="MyPolicy")]` usam o atributo de autenticação aplicado em vez da política de autenticação de fallback.

A política de autenticação de fallback:

_ É aplicado a todas as solicitações que não especificam explicitamente uma política de autenticação. Para solicitações servidas pelo roteamento de ponto de extremidade, isso inclui qualquer ponto de extremidade que não especifica um atributo de autorização. Para solicitações servidas por outro middleware após o middleware de autorização, como [arquivos estáticos](xref:fundamentals/static-files), isso aplicaria a política a todas as solicitações.

Definir a política de autenticação de fallback para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente. Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.

A <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> classe também contém <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . O `DefaultPolicy` é a política usada com o `[Authorize]` atributo quando nenhuma política é especificada. `[Authorize]` Não contém uma política nomeada, ao contrário de `[Authorize(PolicyName="MyPolicy")]` .

Para obter mais informações sobre políticas, consulte <xref:security/authorization/policies> .

Uma maneira alternativa para os controladores e Razor páginas do MVC exigir que todos os usuários sejam autenticados é adicionar um filtro de autorização:

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

O código anterior usa um filtro de autorização, a definição da política de fallback usa o roteamento de ponto de extremidade. Definir a política de fallback é a maneira preferida de exigir que todos os usuários sejam autenticados.

Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às `Index` páginas e `Privacy` para que os usuários anônimos possam obter informações sobre o site antes de se registrarem:

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Configurar a conta de teste

A `SeedData` classe cria duas contas: administrador e Gerenciador. Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas. Defina a senha do diretório do projeto (o diretório que contém *Program.cs* ):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.

Atualize `Main` para usar a senha de teste:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Criar as contas de teste e atualizar os contatos

Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Adicione a ID de usuário do administrador e `ContactStatus` aos contatos. Faça um dos contatos "enviado" e um "rejeitado". Adicione a ID de usuário e o status a todos os contatos. Apenas um contato é mostrado:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Criar manipuladores de autorização de administrador, gerente e proprietário

Crie uma `ContactIsOwnerAuthorizationHandler` classe na pasta *Authorization* . O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato. Manipuladores de autorização geralmente:

* Retornar `context.Succeed` quando os requisitos forem atendidos.
* Retornar `Task.CompletedTask` quando os requisitos não forem atendidos. `Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.

Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados. `ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Criar um manipulador de autorização de gerente

Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* . O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente. Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Criar um manipulador de autorização de administrador

Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* . O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador. O administrador pode realizar todas as operações.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrar os manipuladores de autorização

Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core. Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection). Adicione o seguinte código ao final de `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons. Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.

## <a name="support-authorization"></a>Autorização de suporte

Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.

### <a name="review-the-contact-operations-requirements-class"></a>Examinar a classe de requisitos de operações de contato

Examine a `ContactOperations` classe. Essa classe contém os requisitos aos quais o aplicativo dá suporte:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Criar uma classe base para as páginas de contatos Razor

Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor . A classe base coloca o código de inicialização em um local:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

O código anterior:

* Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.
* Adiciona o Identity `UserManager` serviço.
* Adicione a `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Atualizar o CREATEMODEL

Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Atualize o `CreateModel.OnPostAsync` método para:

* Adicione a ID de usuário ao `Contact` modelo.
* Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Atualizar o IndexModel

Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Atualizar o EditModel

Adicione um manipulador de autorização para verificar se o usuário possui o contato. Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente. O aplicativo não tem acesso ao recurso quando os atributos são avaliados. A autorização baseada em recursos deve ser imperativa. As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador. Você acessa com frequência o recurso passando a chave de recurso.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Atualizar o DeleteModel

Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injetar o serviço de autorização nas exibições

Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.

Insira o serviço de autorização no arquivo *pages/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

A marcação anterior adiciona várias `using` instruções.

Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo. Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos. Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem. A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.

### <a name="update-details"></a>Atualizar detalhes

Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Atualize o modelo de página de detalhes:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Adicionar ou remover um usuário de uma função

Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:

* Removendo privilégios de um usuário. Por exemplo, ativar mudo de um usuário em um aplicativo de chat.
* Adicionando privilégios a um usuário.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Diferenças entre desafio e proíba

Esse aplicativo define a política padrão para [exigir usuários autenticados](#rau). O código a seguir permite usuários anônimos. Os usuários anônimos têm permissão para mostrar as diferenças entre o desafio versus proíba.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

No código anterior:

* Quando o usuário **não** é autenticado, um `ChallengeResult` é retornado. Quando um `ChallengeResult` é retornado, o usuário é redirecionado para a página de entrada.
* Quando o usuário é autenticado, mas não autorizado, um `ForbidResult` é retornado. Quando um `ForbidResult` é retornado, o usuário é redirecionado para a página acesso negado.

## <a name="test-the-completed-app"></a>Testar o aplicativo concluído

Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:

* Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas. Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.
* Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Se o aplicativo tiver contatos:

* Exclua todos os registros na `Contact` tabela.
* Reinicie o aplicativo para propagar o banco de dados.

Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate). Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ). Entre em cada navegador com um usuário diferente. Verifique as seguintes operações:

* Os usuários registrados podem exibir todos os dados de contato aprovados.
* Os usuários registrados podem editar/excluir seus próprios dados.
* Os gerentes podem aprovar/rejeitar dados de contato. A `Details` exibição mostra os botões **aprovar** e **rejeitar** .
* Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.

| Usuário                | Propagado pelo aplicativo | Opções                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Edite/exclua os próprios dados.                |
| manager@contoso.com | Yes               | Aprovar/rejeitar e editar/excluir os próprios dados. |
| admin@contoso.com   | Yes               | Aprovar/rejeitar e editar/excluir todos os dados. |

Crie um contato no navegador do administrador. Copie a URL para excluir e editar do contato do administrador. Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.

## <a name="create-the-starter-app"></a>Criar o aplicativo inicial

* Criar um Razor aplicativo de páginas chamado "ContactManager"
  * Crie o aplicativo com **contas de usuário individuais** .
  * Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.
  * `-uld` Especifica o LocalDB em vez do SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Adicionar *modelos/Contact. cs* :

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold o `Contact` modelo.
* Crie uma migração inicial e atualize o banco de dados:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Se você tiver um bug com o `dotnet aspnet-codegenerator razorpage` comando, consulte [este problema do GitHub](https://github.com/aspnet/Scaffolding/issues/984).

* Atualize a âncora **ContactManager** no arquivo *pages/Shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testar o aplicativo Criando, editando e excluindo um contato

### <a name="seed-the-database"></a>Propagar o banco de dados

Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) à pasta de *dados* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Chamada `SeedData.Initialize` de `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Teste se o aplicativo propagau o banco de dados. Se houver linhas no BD de contato, o método de semente não será executado.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Este tutorial mostra como criar um aplicativo Web ASP.NET Core com dados de usuário protegidos por autorização. Ele exibe uma lista de contatos que usuários autenticados (registrados) criaram. Há três grupos de segurança:

* **Os usuários registrados** podem exibir todos os dados aprovados e podem editar/excluir seus próprios dados.
* **Os gerentes** podem aprovar ou rejeitar dados de contato. Somente contatos aprovados são visíveis para os usuários.
* **Os administradores** podem aprovar/rejeitar e editar/excluir todos os dados.

Na imagem a seguir, o usuário Rick ( `rick@example.com` ) está conectado. Rick só pode exibir contatos aprovados e **Editar** / **excluir** / **criar novos** links para seus contatos. Somente o último registro, criado por Rick, exibe links de **edição** e **exclusão** . Outros usuários não verão o último registro até que um gerente ou Administrador altere o status para "aprovado".

![Captura de tela mostrando Rick conectado](secure-data/_static/rick.png)

Na imagem a seguir, `manager@contoso.com` está conectado e na função do gerente:

![Captura de tela mostrando a manager@contoso.com entrada](secure-data/_static/manager1.png)

A imagem a seguir mostra a exibição de detalhes dos gerentes de um contato:

![Exibição do gerente de um contato](secure-data/_static/manager.png)

Os botões **aprovar** e **rejeitar** são exibidos apenas para gerentes e administradores.

Na imagem a seguir, `admin@contoso.com` está conectado e na função do administrador:

![Captura de tela mostrando a admin@contoso.com entrada](secure-data/_static/admin.png)

O administrador tem todos os privilégios. Ela pode ler/editar/excluir qualquer contato e alterar o status dos contatos.

O aplicativo foi criado por [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) o seguinte `Contact` modelo:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

O exemplo contém os seguintes manipuladores de autorização:

* `ContactIsOwnerAuthorizationHandler`: Garante que um usuário só possa editar seus dados.
* `ContactManagerAuthorizationHandler`: Permite que os gerentes aprovem ou rejeitem contatos.
* `ContactAdministratorsAuthorizationHandler`: Permite que os administradores aprovem ou rejeitem contatos e editem/excluam contatos.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial é avançado. Você deve estar familiarizado com:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Autenticação](xref:security/authentication/identity)
* [Confirmação de conta e recuperação de senha](xref:security/authentication/accconfirm)
* [Autorização](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>O aplicativo iniciador e concluído

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) . [Teste](#test-the-completed-app) o aplicativo concluído para que você se familiarize com seus recursos de segurança.

### <a name="the-starter-app"></a>O aplicativo inicial

[Baixe](xref:index#how-to-download-a-sample) o aplicativo [inicial](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Execute o aplicativo, toque no link do **ContactManager** e verifique se você pode criar, editar e excluir um contato.

## <a name="secure-user-data"></a>Proteger dados do usuário

As seções a seguir têm todas as principais etapas para criar o aplicativo de dados de usuário seguro. Talvez você ache útil fazer referência ao projeto concluído.

### <a name="tie-the-contact-data-to-the-user"></a>Vincular os dados de contato ao usuário

Use a [Identity](xref:security/authentication/identity) ID de usuário ASP.net para garantir que os usuários possam editar seus dados, mas não outros dados de usuários. Adicione `OwnerID` e `ContactStatus` ao `Contact` modelo:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` é a ID do usuário da `AspNetUser` tabela no banco de [Identity](xref:security/authentication/identity) dados. O `Status` campo determina se um contato é visível por usuários gerais.

Crie uma nova migração e atualize o banco de dados:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Adicionar serviços de função a Identity

Acrescente [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) para adicionar serviços de função:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Exigir usuários autenticados

Defina a política de autenticação padrão para exigir que os usuários sejam autenticados:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Você pode recusar a autenticação na Razor página, controlador ou nível de método de ação com o `[AllowAnonymous]` atributo. Definir a política de autenticação padrão para exigir que os usuários sejam autenticados protege Razor páginas e controladores adicionados recentemente. Ter a autenticação exigida por padrão é mais seguro do que depender de novos controladores e Razor páginas para incluir o `[Authorize]` atributo.

Adicione [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) às páginas de índice, sobre e de contato para que os usuários anônimos possam obter informações sobre o site antes de se registrarem.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Configurar a conta de teste

A `SeedData` classe cria duas contas: administrador e Gerenciador. Use a [ferramenta Gerenciador de segredo](xref:security/app-secrets) para definir uma senha para essas contas. Defina a senha do diretório do projeto (o diretório que contém *Program.cs* ):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Se uma senha forte não for especificada, uma exceção será lançada quando `SeedData.Initialize` for chamado.

Atualize `Main` para usar a senha de teste:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Criar as contas de teste e atualizar os contatos

Atualize o `Initialize` método na `SeedData` classe para criar as contas de teste:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Adicione a ID de usuário do administrador e `ContactStatus` aos contatos. Faça um dos contatos "enviado" e um "rejeitado". Adicione a ID de usuário e o status a todos os contatos. Apenas um contato é mostrado:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Criar manipuladores de autorização de administrador, gerente e proprietário

Crie uma pasta de *autorização* e crie uma `ContactIsOwnerAuthorizationHandler` classe nela. O `ContactIsOwnerAuthorizationHandler` verifica se o usuário que está atuando em um recurso possui o recurso.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

O `ContactIsOwnerAuthorizationHandler` contexto de chamadas [. Com sucesso](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) se o usuário autenticado atual for o proprietário do contato. Manipuladores de autorização geralmente:

* Retornar `context.Succeed` quando os requisitos forem atendidos.
* Retornar `Task.CompletedTask` quando os requisitos não forem atendidos. `Task.CompletedTask` Não é êxito ou falha &mdash; permite que outros manipuladores de autorização sejam executados.

Se você precisar falhar explicitamente, o contexto de retorno será retornado [. Falha](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

O aplicativo permite aos proprietários de contato editar/Excluir/criar seus próprios dados. `ContactIsOwnerAuthorizationHandler` Não precisa verificar a operação passada no parâmetro de requisito.

### <a name="create-a-manager-authorization-handler"></a>Criar um manipulador de autorização de gerente

Crie uma `ContactManagerAuthorizationHandler` classe na pasta *Authorization* . O `ContactManagerAuthorizationHandler` verifica se o usuário que está atuando no recurso é um gerente. Somente os gerentes podem aprovar ou rejeitar alterações de conteúdo (novas ou alteradas).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Criar um manipulador de autorização de administrador

Crie uma `ContactAdministratorsAuthorizationHandler` classe na pasta *Authorization* . O `ContactAdministratorsAuthorizationHandler` verifica se o usuário que está atuando no recurso é um administrador. O administrador pode realizar todas as operações.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrar os manipuladores de autorização

Os serviços que usam Entity Framework Core devem ser registrados para [injeção de dependência](xref:fundamentals/dependency-injection) usando [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). O `ContactIsOwnerAuthorizationHandler` usa ASP.NET Core [Identity](xref:security/authentication/identity) , que se baseia em Entity Framework Core. Registre os manipuladores com a coleção de serviços para que fiquem disponíveis para o `ContactsController` através de [injeção de dependência](xref:fundamentals/dependency-injection). Adicione o seguinte código ao final de `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` e `ContactManagerAuthorizationHandler` são adicionados como singletons. Eles são singletons porque não usam o EF e todas as informações necessárias estão no `Context` parâmetro do `HandleRequirementAsync` método.

## <a name="support-authorization"></a>Autorização de suporte

Nesta seção, você atualizará as Razor páginas e adicionará uma classe de requisitos de operações.

### <a name="review-the-contact-operations-requirements-class"></a>Examinar a classe de requisitos de operações de contato

Examine a `ContactOperations` classe. Essa classe contém os requisitos aos quais o aplicativo dá suporte:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Criar uma classe base para as páginas de contatos Razor

Crie uma classe base que contenha os serviços usados nas páginas de contatos Razor . A classe base coloca o código de inicialização em um local:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

O código anterior:

* Adiciona o `IAuthorizationService` serviço para acessar os manipuladores de autorização.
* Adiciona o Identity `UserManager` serviço.
* Adicione a `ApplicationDbContext`.

### <a name="update-the-createmodel"></a>Atualizar o CREATEMODEL

Atualize o Construtor criar modelo de página para usar a `DI_BasePageModel` classe base:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Atualize o `CreateModel.OnPostAsync` método para:

* Adicione a ID de usuário ao `Contact` modelo.
* Chame o manipulador de autorização para verificar se o usuário tem permissão para criar contatos.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Atualizar o IndexModel

Atualize o `OnGetAsync` método para que somente os contatos aprovados sejam mostrados para usuários gerais:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Atualizar o EditModel

Adicione um manipulador de autorização para verificar se o usuário possui o contato. Como a autorização de recursos está sendo validada, o `[Authorize]` atributo não é suficiente. O aplicativo não tem acesso ao recurso quando os atributos são avaliados. A autorização baseada em recursos deve ser imperativa. As verificações devem ser executadas depois que o aplicativo tem acesso ao recurso, seja carregando-o no modelo de página ou carregando-o dentro do próprio manipulador. Você acessa com frequência o recurso passando a chave de recurso.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Atualizar o DeleteModel

Atualize o modelo de página de exclusão para usar o manipulador de autorização para verificar se o usuário tem a permissão Excluir no contato.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Injetar o serviço de autorização nas exibições

Atualmente, a interface do usuário mostra links de edição e exclusão de contatos que o usuário não pode modificar.

Insira o serviço de autorização no arquivo *views/_ViewImports. cshtml* para que ele esteja disponível para todas as exibições:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

A marcação anterior adiciona várias `using` instruções.

Atualize os links **Editar** e **excluir** em *pages/Contacts/index. cshtml* para que eles sejam renderizados apenas para usuários com as permissões apropriadas:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ocultar links de usuários que não têm permissão para alterar dados não protege o aplicativo. Ocultar links torna o aplicativo mais amigável exibindo apenas links válidos. Os usuários podem invadir as URLs geradas para invocar operações de edição e exclusão nos dados que não possuem. A Razor página ou o controlador deve impor verificações de acesso para proteger os dados.

### <a name="update-details"></a>Atualizar detalhes

Atualize a exibição de detalhes para que os gerentes possam aprovar ou rejeitar contatos:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Atualize o modelo de página de detalhes:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Adicionar ou remover um usuário de uma função

Consulte [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) para obter informações sobre:

* Removendo privilégios de um usuário. Por exemplo, ativar mudo de um usuário em um aplicativo de chat.
* Adicionando privilégios a um usuário.

## <a name="test-the-completed-app"></a>Testar o aplicativo concluído

Se você ainda não definiu uma senha para contas de usuário propagadas, use a [ferramenta Gerenciador de segredo](xref:security/app-secrets#secret-manager) para definir uma senha:

* Escolha uma senha forte: Use oito ou mais caracteres e, pelo menos, um caractere, número e símbolo em letras maiúsculas. Por exemplo, `Passw0rd!` atende aos requisitos de senha forte.
* Execute o seguinte comando na pasta do projeto, em que `<PW>` é a senha:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Remover e atualizar o banco de dados

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Reinicie o aplicativo para propagar o banco de dados.

Uma maneira fácil de testar o aplicativo concluído é iniciar três navegadores diferentes (ou sessões Incognito/InPrivate). Em um navegador, registre um novo usuário (por exemplo, `test@example.com` ). Entre em cada navegador com um usuário diferente. Verifique as seguintes operações:

* Os usuários registrados podem exibir todos os dados de contato aprovados.
* Os usuários registrados podem editar/excluir seus próprios dados.
* Os gerentes podem aprovar/rejeitar dados de contato. A `Details` exibição mostra os botões **aprovar** e **rejeitar** .
* Os administradores podem aprovar/rejeitar e editar/excluir todos os dados.

| Usuário                | Propagado pelo aplicativo | Opções                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | Edite/exclua os próprios dados.                |
| manager@contoso.com | Yes               | Aprovar/rejeitar e editar/excluir os próprios dados. |
| admin@contoso.com   | Yes               | Aprovar/rejeitar e editar/excluir todos os dados. |

Crie um contato no navegador do administrador. Copie a URL para excluir e editar do contato do administrador. Cole esses links no navegador do usuário de teste para verificar se o usuário de teste não pode executar essas operações.

## <a name="create-the-starter-app"></a>Criar o aplicativo inicial

* Criar um Razor aplicativo de páginas chamado "ContactManager"
  * Crie o aplicativo com **contas de usuário individuais** .
  * Nomeie-o como "ContactManager" para que o namespace corresponda ao namespace usado no exemplo.
  * `-uld` Especifica o LocalDB em vez do SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Adicionar *modelos/Contact. cs* :

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold o `Contact` modelo.
* Crie uma migração inicial e atualize o banco de dados:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Atualize a âncora **ContactManager** no arquivo *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testar o aplicativo Criando, editando e excluindo um contato

### <a name="seed-the-database"></a>Propagar o banco de dados

Adicione a classe [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) à pasta de *dados* .

Chamada `SeedData.Initialize` de `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Teste se o aplicativo propagau o banco de dados. Se houver linhas no BD de contato, o método de semente não será executado.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Recursos adicionais

* [Criar um aplicativo Web .NET Core e do Banco de Dados SQL no Serviço de Aplicativo do Azure](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core laboratório de autorização](https://github.com/blowdart/AspNetAuthorizationWorkshop). Este laboratório apresenta mais detalhes sobre os recursos de segurança apresentados neste tutorial.
* <xref:security/authorization/introduction>
* [Autorização baseada em política personalizada](xref:security/authorization/policies)
