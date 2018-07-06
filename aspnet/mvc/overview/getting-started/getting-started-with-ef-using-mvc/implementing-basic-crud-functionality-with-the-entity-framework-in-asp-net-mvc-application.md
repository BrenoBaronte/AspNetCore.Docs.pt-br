---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
title: Implementando a funcionalidade CRUD básica com o Entity Framework no aplicativo ASP.NET MVC | Microsoft Docs
author: tdykstra
description: Aplicativo web de exemplo Contoso University demonstra como criar aplicativos ASP.NET MVC 5 usando o Entity Framework 6 Code First e o Visual Studio...
ms.author: aspnetcontent
ms.date: 03/09/2015
ms.assetid: a2f70ba4-83d1-4002-9255-24732726c4f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 20c4ec6835f65c267245c5b37cdd5648e9787c71
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37837695"
---
<a name="implementing-basic-crud-functionality-with-the-entity-framework-in-aspnet-mvc-application"></a>Implementando a funcionalidade CRUD básica com o Entity Framework no aplicativo ASP.NET MVC
====================
por [Tom Dykstra](https://github.com/tdykstra)

[Baixe o projeto concluído](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8) ou [baixar PDF](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20Entity%20Framework%206%20Code%20First%20using%20MVC%205.pdf)

> Aplicativo web de exemplo Contoso University demonstra como criar aplicativos ASP.NET MVC 5 usando o Entity Framework 6 Code First e o Visual Studio 2013. Para obter informações sobre a série de tutoriais, consulte [primeiro tutorial na série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


No tutorial anterior, você criou um aplicativo MVC que armazena e exibe os dados usando o Entity Framework e o LocalDB do SQL Server. Neste tutorial você examinará e personalizará o CRUD (criar, ler, atualizar e excluir) que o scaffolding do MVC cria automaticamente para você em controladores e modos de exibição de código.

> [!NOTE]
> É uma prática comum implementar o padrão de repositório para criar uma camada de abstração entre o controlador e a camada de acesso a dados. Para manter esses tutoriais simples e com foco no ensino de como usar o Entity Framework em si, eles não usam repositórios. Para obter informações sobre como implementar repositórios, consulte o [mapa de conteúdo de acesso do ASP.NET dados](../../../../whitepapers/aspnet-data-access-content-map.md).


Neste tutorial, você criará as seguintes páginas da web:

![Student_Details_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image1.png)

![Student_Edit_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image2.png)

![Student_delete_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image3.png)

## <a name="create-a-details-page"></a>Criar uma página de detalhes

O código gerado automaticamente para os alunos `Index` página deixada de fora a `Enrollments` propriedade, porque essa propriedade contém uma coleção. No `Details` página você exibirá o conteúdo da coleção em uma tabela HTML.

 Na *Controllers\StudentController.cs*, o método de ação para o `Details` exibir usa o [localizar](https://msdn.microsoft.com/library/gg696418(v=VS.103).aspx) método para recuperar uma única `Student` entidade. 

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample1.cs)]

O valor da chave é passado para o método como o `id` parâmetro e é proveniente *dados da rota* no **detalhes** hiperlink na página de índice.

### <a name="tip-route-data"></a>Dica: **rotear dados**

Dados de rota são dados que o associador de modelo encontrado em um segmento de URL especificado na tabela de roteamento. Por exemplo, a rota padrão especifica `controller`, `action`, e `id` segmentos:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample2.cs?highlight=3)]

Na URL a seguir, a rota padrão mapeia `Instructor` como o `controller`, `Index` como o `action` e 1 como o `id`; esses são valores de dados de rota.

`http://localhost:1230/Instructor/Index/1?courseID=2021`

"? courseID = 2021" é um valor de cadeia de caracteres de consulta. O associador de modelo também funcionará se você passar o `id` como um valor de cadeia de caracteres de consulta:

`http://localhost:1230/Instructor/Index?id=1&CourseID=2021`

As URLs são criadas por `ActionLink` instruções na exibição do Razor. No código a seguir, o `id` parâmetro corresponde à rota padrão e, portanto, `id` é adicionado aos dados de rota.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample3.cshtml)]

No código a seguir, `courseID` não corresponde a um parâmetro de rota padrão e, portanto, ele é adicionado como uma cadeia de caracteres de consulta.

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample4.cshtml)]


1. Abra *Views\Student\Details.cshtml*. Cada campo é exibido usando um `DisplayFor` auxiliares, conforme mostrado no exemplo a seguir:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample5.cshtml)]
2. Após o `EnrollmentDate` campo e imediatamente antes do fechamento `</dl>` marca, adicione o código realçado para exibir uma lista de registros, conforme mostrado no exemplo a seguir:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample6.cshtml?highlight=8-29)]

    Se o recuo do código estiver incorreto depois de colar o código, pressione CTRL-K-D para corrigi-lo.

    Esse código percorre as entidades na propriedade de navegação `Enrollments`. Para cada `Enrollment` entidade na propriedade, ele exibe o título do curso e a classificação. O título do curso é recuperado do `Course` entidade que é armazenada em de `Course` propriedade de navegação do `Enrollments` entidade. Todos esses dados são recuperados do banco de dados automaticamente quando for necessário. (Em outras palavras, você está usando o carregamento lento aqui. Você não especificou *o carregamento adiantado* para o `Courses` propriedade de navegação, portanto, os registros não foram recuperados na mesma consulta que obteve os alunos. Em vez disso, na primeira vez que você tentar acessar o `Enrollments` propriedade de navegação, uma nova consulta é enviada para o banco de dados para recuperar os dados. Você pode ler mais sobre o carregamento lento e o carregamento adiantado na [lendo dados relacionados](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial posteriormente nessa série.)
3. Execute a página, selecionando o **alunos** guia e clicando em um **detalhes** link para Alexander Carson. (Se você pressionar CTRL + F5 enquanto o arquivo details. cshtml é aberto, você receberá um erro HTTP 400 porque o Visual Studio tenta executar a página de detalhes, mas ele não foi atingido de um link que especifica o aluno ser exibido. Nesse caso, apenas remover "Aluno/detalhes" da URL e tente novamente, ou feche o navegador, clique com botão direito no projeto e clique em **modo de exibição**e, em seguida, clique em **exibir no navegador**.)

    Você verá a lista de cursos e notas do aluno selecionado:

    ![Student_Details_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image4.png)

## <a name="update-the-create-page"></a>Atualizar a página criar

1. Na *Controllers\StudentController.cs*, substitua o `HttpPost``Create` método de ação com o código a seguir para adicionar um `try-catch` bloquear e remover `ID` do [atributo Bind](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) para o método gerado por scaffolding:

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample7.cs?highlight=3,5-6,13-18)]

    Esse código adiciona o `Student` entidade criada pelo associador de modelo ASP.NET MVC para o `Students` entidade definida e, em seguida, salva as alterações no banco de dados. (*Associador de modelo* refere-se a funcionalidade do ASP.NET MVC que torna mais fácil para você trabalhar com os dados enviados por um formulário; um associador de modelo converte postado formulário valores para tipos CLR e passa para o método de ação em parâmetros. In this case, o associador de modelo cria uma instância de um `Student` os valores de entidade usando a propriedade do `Form` coleção.)

    Você removeu `ID` da associação de atributo porque `ID` é o valor de chave primária do SQL Server será definido automaticamente quando a linha é inserida. Entrada do usuário não define o `ID` valor.

    <a id="overpost"></a>

    ### <a name="security-warning---the-validateantiforgerytoken-attribute-helps-prevent-cross-site-request-forgerysecurityxsrfcsrf-prevention-in-aspnet-mvc-and-web-pagesmd-attacks-it-requires-a-corresponding-htmlantiforgerytoken-statement-in-the-view-which-youll-see-later"></a>Aviso de segurança - a `ValidateAntiForgeryToken` atributo ajuda a impedir [falsificação de solicitação intersite](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) ataques. Ele requer um correspondente `Html.AntiForgeryToken()` instrução no modo de exibição, você verá mais tarde.

    O `Bind` atributo é uma maneira de proteger contra *overposting* em cenários de criação. Por exemplo, suponha que o `Student` entidade inclui um `Secret` propriedade que você não deseja que essa página da web para definir.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample8.cs?highlight=7)]

    Mesmo se você não tiver um `Secret` campo na página da web, um hacker poderia usar uma ferramenta como [fiddler](http://fiddler2.com/home), ou escrever um JavaScript para postar um `Secret` valor de formulário. Sem o [vincular](https://msdn.microsoft.com/library/system.web.mvc.bindattribute(v=vs.108).aspx) atributo limitando os campos que o associador de modelos usa quando cria uma `Student` instância<em>,</em> associador de modelos seleciona esse `Secret` valor de formulário e usá-lo para criar o `Student` instância de entidade. Em seguida, seja qual for o valor que o invasor especificou para o campo de formulário `Secret`, ele é atualizado no banco de dados. A imagem a seguir mostra o fiddler ferramenta adicionando o `Secret` campo (com o valor "OverPost") para os valores de formulário postados.

    ![](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image5.png)  

    Em seguida, o valor "OverPost" é adicionado com êxito à propriedade `Secret` da linha inserida, embora você nunca desejou que a página da Web pudesse definir essa propriedade.

    É uma prática recomendada de segurança para usar o `Include` parâmetro com o `Bind` atributo *lista branca* campos. Também é possível usar o `Exclude` parâmetro para *blacklist* campos que deseja excluir. O motivo `Include` é mais seguro é que quando você adiciona uma nova propriedade à entidade, o novo campo não será automaticamente protegido por um `Exclude` lista.

    Você pode impedir o excesso de postagem em cenários de edição é ler a entidade do banco de dados primeiro e, em seguida, chamando `TryUpdateModel`, passando uma lista explícita de propriedades permitidas. Que é o método usado nestes tutoriais.

    Uma maneira alternativa para evitar o excesso de postagem preferido por muitos desenvolvedores é usar modelos de exibição em vez de classes de entidade com ligação de modelo. Inclua apenas as propriedades que você deseja atualizar no modelo de exibição. Quando o associador de modelos MVC tiver terminado, copie as propriedades do modelo de exibição para a instância da entidade, opcionalmente usando uma ferramenta como [AutoMapper](http://automapper.org/). Use o banco de dados. Entrada na instância de entidade para definir seu estado como não alterado e, em seguida, definir Property("PropertyName"). IsModified como true em cada propriedade de entidade que está incluída no modelo de exibição. Esse método funciona nos cenários de edição e criação.

    Diferente de `Bind` atributo, o `try-catch` bloco é a única alteração que você fez o código gerado por scaffolding. Se uma exceção que é derivada de [DataException](https://msdn.microsoft.com/library/system.data.dataexception.aspx) é capturada enquanto as alterações estão sendo salvas, será exibida uma mensagem de erro genérica. [DataException](https://msdn.microsoft.com/library/system.data.dataexception.aspx) exceções às vezes são causadas por algo externo para o aplicativo em vez de um erro de programação, portanto, o usuário é aconselhado a tentar novamente. Embora não implementado nesta amostra, um aplicativo de qualidade de produção registrará a exceção em log. Para obter mais informações, consulte a seção **Log para informações** em [Monitoramento e telemetria (criando aplicativos de nuvem do mundo real com o Azure)](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log).

    O código na *Views\Student\Create.cshtml* é semelhante ao que você viu na *details. cshtml*, exceto pelo fato `EditorFor` e `ValidationMessageFor` auxiliares são usados para cada campo em vez de `DisplayFor`. Aqui está o código relevante:

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample9.cshtml)]

    *Create.chstml* também inclui `@Html.AntiForgeryToken()`, que funciona com o `ValidateAntiForgeryToken` atributo no controlador para ajudar a evitar [falsificação de solicitação intersite](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md) ataques.

    Nenhuma alteração é necessária no *Create. cshtml*.
2. Execute a página, selecionando o **alunos** guia e clicando em **criar novo**.
3. Insira nomes e uma data inválida e clique em **criar** para ver a mensagem de erro.

    ![Students_Create_page_error_message](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image6.png)

    Essa é a validação do lado do servidor que você obtém por padrão; em um tutorial posterior, você verá como adicionar atributos que gerarão o código para a validação do lado do cliente também. O seguinte código realçado mostra a verificação de validação no modelo de **criar** método.

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample10.cs?highlight=1)]
4. Altere a data para um valor válido e clique em **Criar** para ver o novo aluno ser exibido na página **Índice**.

    ![Students_Index_page_with_new_student](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image7.png)

## <a name="update-the-edit-httppost-method"></a>Atualize o método HttpPost Edit

Na *Controllers\StudentController.cs*, o `HttpGet` `Edit` método (aquele sem o `HttpPost` atributo) usa o `Find` método para recuperar o selecionado `Student` entidade, como você viu no `Details` método. Não é necessário alterar esse método.

No entanto, substituir os `HttpPost` `Edit` método de ação com o código a seguir:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample11.cs)]

Essas alterações implementam uma prática recomendada de segurança para impedir [excesso de postagem](#overpost), o scaffolder gerado um `Bind` de atributos e adicionados a entidade criada pelo associador de modelos ao conjunto de entidades com um sinalizador modificado. Código não é recomendado porque o `Bind` atributo limpa os dados pré-existentes nos campos não listados no `Include` parâmetro. No futuro, o scaffolder de controlador MVC será atualizado para que ele não gera `Bind` atributos para métodos de edição.

O novo código lê a entidade existente e chama [TryUpdateModel](https://msdn.microsoft.com/library/system.web.mvc.controller.tryupdatemodel(v=vs.118).aspx) atualizar campos de entrada do usuário nos dados de formulário postados. Conjuntos de controle automático de alterações do Entity Framework a [modificado](https://msdn.microsoft.com/library/system.data.entitystate.aspx) sinalizador na entidade. Quando o [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) método é chamado, o `Modified` sinalizador faz com que o Entity Framework criar instruções SQL para atualizar a linha de banco de dados. [Conflitos de simultaneidade](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md) são ignorados, e todas as colunas da linha de banco de dados são atualizadas, incluindo aqueles que o usuário não tiver sido alterado. (Um tutorial posterior mostra como lidar com conflitos de simultaneidade, e se você quiser apenas campos individuais a serem atualizados no banco de dados, você pode definir a entidade como não alterado e definir individuais campos a serem modificadas.)

Como prática recomendada para evitar o excesso de postagem, os campos que você deseja que sejam atualizáveis pela página de edição estão na lista de permissões no `TryUpdateModel` parâmetros. Atualmente, não há nenhum campo extra que está sendo protegido, mas listar os campos que você deseja que o associador de modelos associe garante que, se você adicionar campos ao modelo de dados no futuro, eles serão automaticamente protegidos até que você adicione-os aqui de forma explícita.

Como resultado dessas alterações, a assinatura do método do método HttpPost Edit é o mesmo que o método de edição HttpGet; Portanto, você já renomeou o método EditPost.

> [!TIP] 
> 
> **Estados da entidade e os métodos de SaveChanges e anexar**
> 
> O contexto de banco de dados controla se as entidades em memória estão em sincronia com suas linhas correspondentes no banco de dados, e essas informações determinam o que acontece quando você chama o método `SaveChanges`. Por exemplo, quando você passa uma nova entidade para o [Add](https://msdn.microsoft.com/library/system.data.entity.dbset.add(v=vs.103).aspx) método, o que o estado da entidade é definido como `Added`. Em seguida, quando você chama o [SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx) método, o contexto de banco de dados emite um SQL `INSERT` comando.
> 
> Uma entidade pode estar em um dos[estados a seguir](https://msdn.microsoft.com/library/system.data.entitystate.aspx):
> 
> - `Added`. A entidade ainda não existe no banco de dados. O `SaveChanges` método deve emitir uma `INSERT` instrução.
> - `Unchanged`. Nada precisa ser feito com essa entidade pelo método `SaveChanges`. Ao ler uma entidade do banco de dados, a entidade começa com esse status.
> - `Modified`. Alguns ou todos os valores de propriedade da entidade foram modificados. O `SaveChanges` método deve emitir uma `UPDATE` instrução.
> - `Deleted`. A entidade foi marcada para exclusão. O `SaveChanges` método deve emitir uma `DELETE` instrução.
> - `Detached`. A entidade não está sendo controlada pelo contexto de banco de dados.
> 
> Em um aplicativo da área de trabalho, em geral, as alterações de estado são definidas automaticamente. Em um tipo de área de trabalho do aplicativo, você lê uma entidade e fazer alterações em alguns de seus valores de propriedade. Isso faz com que seu estado da entidade seja alterado automaticamente para `Modified`. Em seguida, quando você chama `SaveChanges`, o Entity Framework gera um SQL `UPDATE` instrução que atualiza apenas as propriedades reais que você alterou.
> 
> A natureza desconectada do aplicativos web não permite que essa sequência contínua. O [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx) que lê uma entidade é descartada depois que uma página é renderizada. Quando o `HttpPost` `Edit` método de ação é chamado, uma nova solicitação é feita e você tem uma nova instância das [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx), para que você precise definir manualmente o estado da entidade para `Modified.` e em seguida, quando você chama `SaveChanges`, o Entity Framework atualiza todas as colunas da linha de banco de dados, porque o contexto não tem como saber quais propriedades foram alteradas.
> 
> Se você quiser que o SQL `Update` instrução para atualizar somente os campos que o usuário realmente foram alterado, você pode salvar os valores originais de alguma forma (como campos ocultos) para que eles estejam disponíveis quando o `HttpPost` `Edit` método é chamado. Em seguida, você pode criar uma `Student` entidade usando os valores originais, chamada a `Attach` método com a versão original da entidade, atualize os valores da entidade para os novos valores e, em seguida, chame `SaveChanges.` para obter mais informações, consulte [ Estados da entidade e SaveChanges](https://msdn.microsoft.com/data/jj592676) e [dados locais](https://msdn.microsoft.com/data/jj592872) no MSDN Data Developer Center.


O código HTML e Razor no *Views\Student\Edit.cshtml* é semelhante ao que você viu na *Create. cshtml*, e nenhuma alteração é necessária.

Execute a página, selecionando o **alunos** guia e, em seguida, clicando em um **editar** hiperlink.

![Student_Edit_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image8.png)

Altere alguns dos dados e clique em **Salvar**. Você verá os dados alterados na página de índice.

![Students_Index_page_after_edit](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image9.png)

## <a name="updating-the-delete-page"></a>Atualizando a página Excluir

No *Controllers\StudentController.cs*, o código de modelo para o `HttpGet` `Delete` método usa o `Find` método para recuperar o selecionados `Student` entidade, como você viu no `Details` e `Edit` métodos. No entanto, para implementar uma mensagem de erro personalizada quando a chamada a `SaveChanges` falhar, você adicionará uma funcionalidade a esse método e à sua exibição correspondente.

Como você viu para operações de atualização e criação, as operações de exclusão exigem dois métodos de ação. O método que é chamado em resposta a uma solicitação GET mostra uma exibição que dá ao usuário a oportunidade de aprovar ou cancelar a operação de exclusão. Se o usuário aprová-la, uma solicitação POST será criada. Quando isso acontece, o `HttpPost` `Delete` método é chamado e, em seguida, esse método, na verdade, executa a operação de exclusão.

Você adicionará uma `try-catch` bloco para o `HttpPost` `Delete` método para tratar os erros que podem ocorrer quando o banco de dados é atualizado. Se ocorrer um erro, o `HttpPost` `Delete` chamadas de método de `HttpGet` `Delete` método, passando um parâmetro que indica que ocorreu um erro. O `HttpGet Delete` método, em seguida, exibe novamente a página de confirmação, junto com a mensagem de erro, dando ao usuário uma oportunidade de cancelar ou tentar novamente.

1. Substitua os `HttpGet` `Delete` método de ação com o código a seguir, que gerencia o relatório de erros: 

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample12.cs?highlight=1,7-10)]

    Este código aceita uma [parâmetro opcional](https://msdn.microsoft.com/library/dd264739.aspx) que indica se o método foi chamado após uma falha ao salvar as alterações. Esse parâmetro é `false` quando o `HttpGet` `Delete` método for chamado sem uma falha anterior. Quando ele é chamado o `HttpPost` `Delete` método em resposta a um erro de atualização de banco de dados, o parâmetro é `true` e uma mensagem de erro é passada para o modo de exibição.
2. Substitua os `HttpPost` `Delete` método de ação (chamado `DeleteConfirmed`) com o código a seguir, que executa a operação de exclusão real e captura os erros de atualização do banco de dados.

     [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample13.cs)]

     Esse código recupera a entidade selecionada, em seguida, chama o [remova](https://msdn.microsoft.com/library/system.data.entity.dbset.remove(v=vs.103).aspx) método para definir o status da entidade `Deleted`. Quando `SaveChanges` é chamado, um SQL `DELETE` comando é gerado. Você também alterou o nome do método de ação de `DeleteConfirmed` para `Delete`. O código gerado por scaffolding chamado a `HttpPost` `Delete` método `DeleteConfirmed` para dar o `HttpPost` método uma assinatura exclusiva. (O CLR exige métodos sobrecarregados tenham diferentes parâmetros de método). Agora que as assinaturas são exclusivas, você pode continuar com a convenção do MVC e usar o mesmo nome para o `HttpPost` e `HttpGet` excluir métodos.

     Se a melhoria do desempenho de um aplicativo de alto volume for uma prioridade, você poderia evitar uma consulta SQL desnecessária para recuperar a linha, substituindo as linhas de código que chamem o `Find` e `Remove` métodos com o código a seguir:

     [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample14.cs)]

     Esse código instancia uma `Student` entidade usando apenas o valor de chave primária e, em seguida, define o estado da entidade para `Deleted`. Isso é tudo o que o Entity Framework precisa para excluir a entidade.

     Conforme observado, o `HttpGet` `Delete` método não exclui os dados. Executando uma operação de exclusão em resposta a um GET de solicitação (ou para fato, a execução de qualquer operação de edição, criação ou qualquer outra operação que altera os dados) cria um risco à segurança. Para obter mais informações, consulte [ASP.NET MVC dica n º 46 — não use Excluir Links porque eles criaram buracos na segurança](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx) no blog de Stephen Walther.
3. Na *Views\Student\Delete.cshtml*, adicione uma mensagem de erro entre o `h2` título e o `h3` título, conforme mostrado no exemplo a seguir:

     [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample15.cshtml?highlight=2)]

     Execute a página, selecionando o **alunos** guia e clicando em um **excluir** hiperlink:

     ![Student_Delete_page](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image10.png)
4. Clique em **Excluir**. A página Índice será exibida sem o aluno excluído. (Você verá um exemplo de código na ação de manipulação de erros do [tutorial de simultaneidade](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md).)

## <a name="closing-database-connections"></a>Fechando conexões de banco de dados

Para fechar conexões de banco de dados e liberar os recursos que eles contêm assim que possível, descarte a instância de contexto quando você terminar com ele. Isto é porque o código gerado por scaffolding fornece um [Dispose](https://msdn.microsoft.com/library/system.idisposable.dispose(v=vs.110).aspx) método no final do `StudentController` classe *StudentController.cs*, conforme mostrado no exemplo a seguir:

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample16.cs)]

A base `Controller` já classe implementa o `IDisposable` interface, portanto, esse código simplesmente adiciona uma substituição para o `Dispose(bool)` método para descartar explicitamente a instância do contexto.

<a id="transactions"></a>
## <a name="handling-transactions"></a>Manipulando transações

Por padrão, o Entity Framework implementa transações de forma implícita. Em cenários em que você pode fazer alterações em várias linhas ou tabelas e, em seguida, chama `SaveChanges`, automaticamente, o Entity Framework torna-se de que todas as suas alterações ter êxito ou falhar. Se algumas alterações forem feitas pela primeira vez e, em seguida, ocorrer um erro, essas alterações serão revertidas automaticamente. Para cenários em que você precisa de mais controle – por exemplo, se você quiser incluir operações feitas fora do Entity Framework em uma transação –, consulte [trabalhando com transações](https://msdn.microsoft.com/data/dn456843) no MSDN.

## <a name="summary"></a>Resumo

Agora você tem um conjunto completo de páginas que executam operações CRUD simples para `Student` entidades. Você usou os auxiliares de MVC para gerar os elementos de interface do usuário para campos de dados. Para obter mais informações sobre os auxiliares de MVC, consulte [auxiliares de HTML usando um formato de renderização](https://msdn.microsoft.com/library/dd410596(v=VS.98).aspx) (a página é para o MVC 3, mas ainda é relevante para o MVC 5).

No próximo tutorial, você expandirá a funcionalidade da página de índice, adicionando a classificação e paginação.

Deixe comentários sobre como você gostou neste tutorial e o que poderíamos melhorar. Você também pode solicitar novos tópicos em [Mostrar-Me como com código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

Links para outros recursos do Entity Framework pode ser encontrado na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

> [!div class="step-by-step"]
> [Anterior](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)
> [Próximo](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)
