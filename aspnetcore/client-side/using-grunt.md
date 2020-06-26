---
title: Usar Grunt no ASP.NET Core
author: rick-anderson
description: Usar Grunt no ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/using-grunt
ms.openlocfilehash: fc871e22f9bd5a9c137008f1d87019542c45b5d2
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401760"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="3193c-103">Usar Grunt no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3193c-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="3193c-104">Grunt é um executor de tarefa JavaScript que automatiza o script minificação, compilação TypeScript, ferramentas de "pano" de qualidade de código, pré-processadores de CSS e praticamente qualquer tarefa repetitiva que precise fazer para dar suporte ao desenvolvimento de clientes.</span><span class="sxs-lookup"><span data-stu-id="3193c-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="3193c-105">O Grunt tem suporte total no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3193c-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="3193c-106">Este exemplo usa um projeto de ASP.NET Core vazio como ponto de partida, para mostrar como automatizar o processo de criação do cliente do zero.</span><span class="sxs-lookup"><span data-stu-id="3193c-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="3193c-107">O exemplo concluído limpa o diretório de implantação de destino, combina arquivos JavaScript, verifica a qualidade do código, condensa o conteúdo do arquivo JavaScript e implanta na raiz do seu aplicativo Web.</span><span class="sxs-lookup"><span data-stu-id="3193c-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="3193c-108">Usaremos os seguintes pacotes:</span><span class="sxs-lookup"><span data-stu-id="3193c-108">We will use the following packages:</span></span>

* <span data-ttu-id="3193c-109">**Grunt**: o pacote do executor de tarefa do Grunt.</span><span class="sxs-lookup"><span data-stu-id="3193c-109">**grunt**: The Grunt task runner package.</span></span>

* <span data-ttu-id="3193c-110">**Grunt-contrib-Clean**: um plug-in que remove arquivos ou diretórios.</span><span class="sxs-lookup"><span data-stu-id="3193c-110">**grunt-contrib-clean**: A plugin that removes files or directories.</span></span>

* <span data-ttu-id="3193c-111">**Grunt-contrib-jshint**: um plug-in que revisa a qualidade do código JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3193c-111">**grunt-contrib-jshint**: A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="3193c-112">**Grunt-contrib-Concat**: um plug-in que une arquivos em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="3193c-112">**grunt-contrib-concat**: A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="3193c-113">**Grunt-contrib-uglify**: um plug-in que minifies JavaScript para reduzir o tamanho.</span><span class="sxs-lookup"><span data-stu-id="3193c-113">**grunt-contrib-uglify**: A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="3193c-114">**Grunt-contrib-Watch**: um plug-in que observa a atividade do arquivo.</span><span class="sxs-lookup"><span data-stu-id="3193c-114">**grunt-contrib-watch**: A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="3193c-115">Preparando o aplicativo</span><span class="sxs-lookup"><span data-stu-id="3193c-115">Preparing the application</span></span>

<span data-ttu-id="3193c-116">Para começar, configure um novo aplicativo Web vazio e adicione arquivos de exemplo do TypeScript.</span><span class="sxs-lookup"><span data-stu-id="3193c-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="3193c-117">Os arquivos TypeScript são compilados automaticamente em JavaScript usando as configurações padrão do Visual Studio e serão nosso material bruto para processar usando Grunt.</span><span class="sxs-lookup"><span data-stu-id="3193c-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="3193c-118">No Visual Studio, crie um novo `ASP.NET Web Application` .</span><span class="sxs-lookup"><span data-stu-id="3193c-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="3193c-119">Na caixa de diálogo **novo projeto ASP.net** , selecione o modelo ASP.NET Core **vazio** e clique no botão OK.</span><span class="sxs-lookup"><span data-stu-id="3193c-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="3193c-120">Na Gerenciador de Soluções, examine a estrutura do projeto.</span><span class="sxs-lookup"><span data-stu-id="3193c-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="3193c-121">A `\src` pasta inclui os `wwwroot` nós e vazios `Dependencies` .</span><span class="sxs-lookup"><span data-stu-id="3193c-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![solução Web vazia](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="3193c-123">Adicione uma nova pasta chamada `TypeScript` ao diretório do projeto.</span><span class="sxs-lookup"><span data-stu-id="3193c-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="3193c-124">Antes de adicionar qualquer arquivo, verifique se o Visual Studio tem a opção ' Compilar ao salvar ' para arquivos TypeScript verificados.</span><span class="sxs-lookup"><span data-stu-id="3193c-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="3193c-125">Navegue até **ferramentas**  >  **Opções**  >  **Editor de texto**  >  **Typescript**  >  **projeto**typescript:</span><span class="sxs-lookup"><span data-stu-id="3193c-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project**:</span></span>

    ![opções de configuração da compilação automática de arquivos TypeScript](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="3193c-127">Clique com o botão direito do mouse no `TypeScript` diretório e selecione **Adicionar > novo item** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="3193c-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="3193c-128">Selecione o item de **arquivo JavaScript** e nomeie o arquivo *preferências. TS* (Observe a \* extensão. TS).</span><span class="sxs-lookup"><span data-stu-id="3193c-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="3193c-129">Copie a linha do código TypeScript abaixo para o arquivo (quando você salvar, um novo arquivo de *Tastes.js* aparecerá com a origem do JavaScript).</span><span class="sxs-lookup"><span data-stu-id="3193c-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="3193c-130">Adicione um segundo arquivo ao diretório **TypeScript** e nomeie-o `Food.ts` .</span><span class="sxs-lookup"><span data-stu-id="3193c-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="3193c-131">Copie o código abaixo para o arquivo.</span><span class="sxs-lookup"><span data-stu-id="3193c-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="3193c-132">Configurando o NPM</span><span class="sxs-lookup"><span data-stu-id="3193c-132">Configuring NPM</span></span>

<span data-ttu-id="3193c-133">Em seguida, configure NPM para baixar Grunt e Grunt-Tasks.</span><span class="sxs-lookup"><span data-stu-id="3193c-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="3193c-134">Na Gerenciador de Soluções, clique com o botão direito do mouse no projeto e selecione **adicionar > novo item** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="3193c-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="3193c-135">Selecione o item de **arquivo de configuração NPM** , deixe o nome padrão, *package.jsem*, e clique no botão **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="3193c-135">Select the **NPM configuration file** item, leave the default name, *package.json*, and click the **Add** button.</span></span>

2. <span data-ttu-id="3193c-136">Na *package.jsno* arquivo, dentro das `devDependencies` chaves do objeto, digite "Grunt".</span><span class="sxs-lookup"><span data-stu-id="3193c-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="3193c-137">Selecione `grunt` na lista do IntelliSense e pressione a tecla Enter.</span><span class="sxs-lookup"><span data-stu-id="3193c-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="3193c-138">O Visual Studio irá citar o nome do pacote Grunt e adicionará dois-pontos.</span><span class="sxs-lookup"><span data-stu-id="3193c-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="3193c-139">À direita dos dois-pontos, selecione a versão estável mais recente do pacote na parte superior da lista do IntelliSense (pressione `Ctrl-Space` se o IntelliSense não aparece).</span><span class="sxs-lookup"><span data-stu-id="3193c-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![Grunt IntelliSense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="3193c-141">O NPM usa o [controle de versão semântico](https://semver.org/) para organizar dependências.</span><span class="sxs-lookup"><span data-stu-id="3193c-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="3193c-142">O controle de versão semântico, também conhecido como SemVer, identifica pacotes com o esquema de numeração \<major> . \<minor> . \<patch> . O IntelliSense simplifica o controle de versão semântico mostrando apenas algumas opções comuns.</span><span class="sxs-lookup"><span data-stu-id="3193c-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="3193c-143">O item superior na lista do IntelliSense (0.4.5 no exemplo acima) é considerado a versão estável mais recente do pacote.</span><span class="sxs-lookup"><span data-stu-id="3193c-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="3193c-144">O símbolo de acento circunflexo (^) corresponde à versão principal mais recente e o til (~) corresponde à versão secundária mais recente.</span><span class="sxs-lookup"><span data-stu-id="3193c-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="3193c-145">Consulte a [referência do analisador de versão do NPM semver](https://www.npmjs.com/package/semver) como um guia para o expressividade completo que o semver fornece.</span><span class="sxs-lookup"><span data-stu-id="3193c-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="3193c-146">Adicione mais dependências para carregar Grunt-contrib- \* Packages para *Clean*, *jshint*, *concat*, *uglify*e *Watch* , conforme mostrado no exemplo a seguir.</span><span class="sxs-lookup"><span data-stu-id="3193c-146">Add more dependencies to load grunt-contrib-\* packages for *clean*, *jshint*, *concat*, *uglify*, and *watch* as shown in the example below.</span></span> <span data-ttu-id="3193c-147">As versões não precisam corresponder ao exemplo.</span><span class="sxs-lookup"><span data-stu-id="3193c-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="3193c-148">Salve o arquivo *package.json*.</span><span class="sxs-lookup"><span data-stu-id="3193c-148">Save the *package.json* file.</span></span>

<span data-ttu-id="3193c-149">Os pacotes para cada `devDependencies` item serão baixados, juntamente com todos os arquivos que cada pacote exigir.</span><span class="sxs-lookup"><span data-stu-id="3193c-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="3193c-150">Você pode encontrar os arquivos de pacote no diretório *node_modules* habilitando o botão **Mostrar todos os arquivos** em **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="3193c-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![node_modules Grunt](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="3193c-152">Se necessário, você pode restaurar manualmente as dependências no **Gerenciador de soluções** clicando com o botão direito do mouse em `Dependencies\NPM` e selecionando a opção de menu **restaurar pacotes** .</span><span class="sxs-lookup"><span data-stu-id="3193c-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![restaurar pacotes](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="3193c-154">Configurando o Grunt</span><span class="sxs-lookup"><span data-stu-id="3193c-154">Configuring Grunt</span></span>

<span data-ttu-id="3193c-155">O Grunt é configurado usando um manifesto chamado *Gruntfile.js* que define, carrega e registra tarefas que podem ser executadas manualmente ou configuradas para serem executadas automaticamente com base em eventos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3193c-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="3193c-156">Clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo item**.</span><span class="sxs-lookup"><span data-stu-id="3193c-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="3193c-157">Selecione o modelo de item de **arquivo JavaScript** , altere o nome para *Gruntfile.js*e clique no botão **Adicionar** .</span><span class="sxs-lookup"><span data-stu-id="3193c-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js*, and click the **Add** button.</span></span>

1. <span data-ttu-id="3193c-158">Adicione o código a seguir a *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="3193c-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="3193c-159">A `initConfig` função define as opções para cada pacote e o restante do módulo carrega e registra as tarefas.</span><span class="sxs-lookup"><span data-stu-id="3193c-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="3193c-160">Dentro da `initConfig` função, adicione opções para a `clean` tarefa, conforme mostrado no exemplo *Gruntfile.js* abaixo.</span><span class="sxs-lookup"><span data-stu-id="3193c-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="3193c-161">A `clean` tarefa aceita uma matriz de cadeias de caracteres de diretório.</span><span class="sxs-lookup"><span data-stu-id="3193c-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="3193c-162">Essa tarefa remove arquivos de *wwwroot/lib* e remove todo o diretório */Temp* .</span><span class="sxs-lookup"><span data-stu-id="3193c-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="3193c-163">Abaixo da `initConfig` função, adicione uma chamada para `grunt.loadNpmTasks` .</span><span class="sxs-lookup"><span data-stu-id="3193c-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="3193c-164">Isso fará com que a tarefa seja executável do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3193c-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="3193c-165">Salve *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="3193c-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="3193c-166">O arquivo deve ser semelhante à captura de tela abaixo.</span><span class="sxs-lookup"><span data-stu-id="3193c-166">The file should look something like the screenshot below.</span></span>

    ![gruntfile inicial](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="3193c-168">Clique com o botão direito do mouse em *Gruntfile.js* e selecione **Task Runner Explorer** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="3193c-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="3193c-169">A janela **Gerenciador de executores de tarefas** será aberta.</span><span class="sxs-lookup"><span data-stu-id="3193c-169">The **Task Runner Explorer** window will open.</span></span>

    ![menu do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="3193c-171">Verifique se `clean` o mostra em **tarefas** no **Task Runner Explorer**.</span><span class="sxs-lookup"><span data-stu-id="3193c-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![lista de tarefas do Gerenciador de executor de tarefas](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="3193c-173">Clique com o botão direito do mouse na tarefa limpar e selecione **executar** no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="3193c-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="3193c-174">Uma janela de comando exibe o progresso da tarefa.</span><span class="sxs-lookup"><span data-stu-id="3193c-174">A command window displays progress of the task.</span></span>

    ![Gerenciador do executor de tarefas executar limpar tarefa](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="3193c-176">Não há arquivos ou diretórios para limpar ainda.</span><span class="sxs-lookup"><span data-stu-id="3193c-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="3193c-177">Se desejar, você pode criá-los manualmente no Gerenciador de Soluções e executar a tarefa de limpeza como um teste.</span><span class="sxs-lookup"><span data-stu-id="3193c-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="3193c-178">Na `initConfig` função, adicione uma entrada para `concat` usar o código abaixo.</span><span class="sxs-lookup"><span data-stu-id="3193c-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="3193c-179">A `src` matriz de Propriedades lista os arquivos a serem combinados, na ordem em que eles devem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="3193c-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="3193c-180">A `dest` Propriedade atribui o caminho para o arquivo combinado que é produzido.</span><span class="sxs-lookup"><span data-stu-id="3193c-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="3193c-181">A `all` propriedade no código acima é o nome de um destino.</span><span class="sxs-lookup"><span data-stu-id="3193c-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="3193c-182">Os destinos são usados em algumas tarefas do Grunt para permitir vários ambientes de compilação.</span><span class="sxs-lookup"><span data-stu-id="3193c-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="3193c-183">Você pode exibir os destinos internos usando o IntelliSense ou atribuir o seu próprio.</span><span class="sxs-lookup"><span data-stu-id="3193c-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="3193c-184">Adicione a `jshint` tarefa usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3193c-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="3193c-185">O `code-quality` utilitário jshint é executado em todos os arquivos JavaScript encontrados no diretório *Temp* .</span><span class="sxs-lookup"><span data-stu-id="3193c-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="3193c-186">A opção "-W069" é um erro produzido por jshint quando o JavaScript usa a sintaxe de colchete para atribuir uma propriedade em vez de uma notação de ponto, ou seja, `Tastes["Sweet"]` em vez de `Tastes.Sweet` .</span><span class="sxs-lookup"><span data-stu-id="3193c-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="3193c-187">A opção desativa o aviso para permitir que o restante do processo continue.</span><span class="sxs-lookup"><span data-stu-id="3193c-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="3193c-188">Adicione a `uglify` tarefa usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3193c-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="3193c-189">A tarefa minifies o arquivo de *combined.js* encontrado no diretório temp e cria o arquivo de resultado em wwwroot/lib seguindo a Convenção de nomenclatura padrão \* \<file name\>.min.js\*.</span><span class="sxs-lookup"><span data-stu-id="3193c-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="3193c-190">Sob a chamada para `grunt.loadNpmTasks` essas cargas `grunt-contrib-clean` , inclua a mesma chamada para jshint, Concat e uglify usando o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3193c-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="3193c-191">Salve *Gruntfile.js*.</span><span class="sxs-lookup"><span data-stu-id="3193c-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="3193c-192">O arquivo deve ser semelhante ao exemplo abaixo.</span><span class="sxs-lookup"><span data-stu-id="3193c-192">The file should look something like the example below.</span></span>

    ![exemplo de arquivo Grunt completo](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="3193c-194">Observe que a lista de tarefas do **Gerenciador de executores de tarefas** inclui `clean` `concat` tarefas, `jshint` e `uglify` .</span><span class="sxs-lookup"><span data-stu-id="3193c-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="3193c-195">Execute cada tarefa na ordem e observe os resultados em **Gerenciador de soluções**.</span><span class="sxs-lookup"><span data-stu-id="3193c-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="3193c-196">Cada tarefa deve ser executada sem erros.</span><span class="sxs-lookup"><span data-stu-id="3193c-196">Each task should run without errors.</span></span>

    ![Explorador do executor de tarefas executar cada tarefa](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="3193c-198">A tarefa Concat cria um novo arquivo de *combined.js* e o coloca no diretório temporário.</span><span class="sxs-lookup"><span data-stu-id="3193c-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="3193c-199">A `jshint` tarefa simplesmente é executada e não produz a saída.</span><span class="sxs-lookup"><span data-stu-id="3193c-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="3193c-200">A `uglify` tarefa cria um novo arquivo de *combined.min.js* e o coloca em *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="3193c-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="3193c-201">Ao concluir, a solução deve ser semelhante à captura de tela abaixo:</span><span class="sxs-lookup"><span data-stu-id="3193c-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![Gerenciador de soluções após todas as tarefas](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="3193c-203">Para obter mais informações sobre as opções para cada pacote, visite [https://www.npmjs.com/](https://www.npmjs.com/) e pesquise o nome do pacote na caixa de pesquisa na página principal.</span><span class="sxs-lookup"><span data-stu-id="3193c-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="3193c-204">Por exemplo, você pode pesquisar o pacote Grunt-contrib-Clean para obter um link de documentação que explica todos os seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="3193c-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="3193c-205">Todos juntos agora</span><span class="sxs-lookup"><span data-stu-id="3193c-205">All together now</span></span>

<span data-ttu-id="3193c-206">Use o `registerTask()` método Grunt para executar uma série de tarefas em uma sequência específica.</span><span class="sxs-lookup"><span data-stu-id="3193c-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="3193c-207">Por exemplo, para executar as etapas de exemplo acima no pedido Clean-> Concat-> jshint-> uglify, adicione o código abaixo ao módulo.</span><span class="sxs-lookup"><span data-stu-id="3193c-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="3193c-208">O código deve ser adicionado ao mesmo nível que as chamadas loadNpmTasks (), fora de initConfig.</span><span class="sxs-lookup"><span data-stu-id="3193c-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="3193c-209">A nova tarefa aparece no Gerenciador do executor de tarefas em tarefas de alias.</span><span class="sxs-lookup"><span data-stu-id="3193c-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="3193c-210">Você pode clicar com o botão direito do mouse e executá-lo da mesma forma que faria com outras tarefas.</span><span class="sxs-lookup"><span data-stu-id="3193c-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="3193c-211">A `all` tarefa será executada `clean` , `concat` `jshint` e `uglify` , em ordem.</span><span class="sxs-lookup"><span data-stu-id="3193c-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![tarefas de Grunt de alias](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="3193c-213">Monitorando alterações</span><span class="sxs-lookup"><span data-stu-id="3193c-213">Watching for changes</span></span>

<span data-ttu-id="3193c-214">Uma `watch` tarefa fica atento aos arquivos e diretórios.</span><span class="sxs-lookup"><span data-stu-id="3193c-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="3193c-215">O Watch acionará as tarefas automaticamente se detectar alterações.</span><span class="sxs-lookup"><span data-stu-id="3193c-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="3193c-216">Adicione o código abaixo a initConfig para observar alterações em \* arquivos. js no diretório TypeScript.</span><span class="sxs-lookup"><span data-stu-id="3193c-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="3193c-217">Se um arquivo JavaScript for alterado, `watch` o executará a `all` tarefa.</span><span class="sxs-lookup"><span data-stu-id="3193c-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="3193c-218">Adicione uma chamada para `loadNpmTasks()` para mostrar a `watch` tarefa no Task Runner Explorer.</span><span class="sxs-lookup"><span data-stu-id="3193c-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="3193c-219">Clique com o botão direito do mouse na tarefa de inspeção no Gerenciador do executor de tarefa e selecione executar no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="3193c-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="3193c-220">A janela de comando que mostra a tarefa de inspeção em execução exibirá um "aguardando..." Mensagem.</span><span class="sxs-lookup"><span data-stu-id="3193c-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="3193c-221">Abra um dos arquivos TypeScript, adicione um espaço e, em seguida, salve o arquivo.</span><span class="sxs-lookup"><span data-stu-id="3193c-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="3193c-222">Isso disparará a tarefa de inspeção e disparará as outras tarefas a serem executadas na ordem.</span><span class="sxs-lookup"><span data-stu-id="3193c-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="3193c-223">A captura de tela abaixo mostra uma execução de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3193c-223">The screenshot below shows a sample run.</span></span>

![saída de tarefas em execução](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="3193c-225">Associação a eventos do Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3193c-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="3193c-226">A menos que você queira iniciar manualmente suas tarefas sempre que trabalhar no Visual Studio, associe tarefas a **antes da compilação**, **após a compilação**, **limpeza**e eventos **abertos do projeto** .</span><span class="sxs-lookup"><span data-stu-id="3193c-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build**, **After Build**, **Clean**, and **Project Open** events.</span></span>

<span data-ttu-id="3193c-227">Associe `watch` para que ele seja executado toda vez que o Visual Studio for aberto.</span><span class="sxs-lookup"><span data-stu-id="3193c-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="3193c-228">No Gerenciador do executor de tarefas, clique com o botão direito do **Bindings**mouse na tarefa de inspeção e selecione  >  **projeto** de associações abrir no menu de contexto.</span><span class="sxs-lookup"><span data-stu-id="3193c-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![associar uma tarefa à abertura do projeto](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="3193c-230">Descarregue e recarregue o projeto.</span><span class="sxs-lookup"><span data-stu-id="3193c-230">Unload and reload the project.</span></span> <span data-ttu-id="3193c-231">Quando o projeto é carregado novamente, a tarefa de inspeção começa a ser executada automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3193c-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="3193c-232">Resumo</span><span class="sxs-lookup"><span data-stu-id="3193c-232">Summary</span></span>

<span data-ttu-id="3193c-233">O Grunt é um executor de tarefas poderoso que pode ser usado para automatizar a maioria das tarefas de compilação do cliente.</span><span class="sxs-lookup"><span data-stu-id="3193c-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="3193c-234">O Grunt aproveita o NPM para fornecer seus pacotes e recursos de integração de ferramentas com o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3193c-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="3193c-235">O Task Runner Explorer do Visual Studio detecta alterações nos arquivos de configuração e fornece uma interface conveniente para executar tarefas, exibir tarefas em execução e associar tarefas a eventos do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3193c-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
