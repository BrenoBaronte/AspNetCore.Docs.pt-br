## <a name="troubleshoot"></a>Solucionar problemas

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a>Erros comuns

* Cliente não autorizado para AAD

  > info: falha na autorização de Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2]. Esses requisitos não foram atendidos: DenyAnonymousAuthorizationRequirement: requer um usuário autenticado.

  Erro de retorno de chamada de logon do AAD:

  * Erro: `unauthorized_client`
  * Descrição: `AADB2C90058: The provided application is not configured to allow public clients.`

  Para resolver o erro:

  1. No portal do Azure, acesse o [manifesto do aplicativo](/azure/active-directory/develop/reference-app-manifest).
  1. Defina o [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) atributo como `null` ou `true` .

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookies e dados do site

Cookies e dados do site podem persistir em atualizações de aplicativo e interferir no teste e na solução de problemas. Desmarque o seguinte ao fazer alterações no código do aplicativo, as alterações na conta do usuário com o provedor ou as alterações de configuração do aplicativo do provedor:

* Cookies de entrada do usuário
* Cookies do aplicativo
* Dados do site armazenados e em cache

Uma abordagem para impedir que cookies persistentes e dados do site interfira no teste e solução de problemas é:

* Configurar um navegador
  * Use um navegador para teste que você possa configurar para excluir todos os dados de cookies e do site sempre que o navegador for fechado.
  * Verifique se o navegador está fechado manualmente ou pelo IDE para qualquer alteração no aplicativo, no usuário de teste ou na configuração do provedor.
* Use um comando personalizado para abrir um navegador no modo Incognito ou privado no Visual Studio:
  * Abra a caixa de diálogo **procurar com** no botão **executar** do Visual Studio.
  * Selecione o botão **Adicionar**.
  * Forneça o caminho para o seu navegador no campo **programa** . Os seguintes caminhos executáveis são locais típicos de instalação para o Windows 10. Se o navegador estiver instalado em um local diferente ou se você não estiver usando o Windows 10, forneça o caminho para o executável do navegador.
    * Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`
  * No campo **argumentos** , forneça a opção de linha de comando que o navegador usa para abrir no modo Incognito ou privado. Alguns navegadores exigem a URL do aplicativo.
    * Microsoft Edge: use `-inprivate` .
    * Google Chrome: use `--incognito --new-window {URL}` , em que o espaço reservado `{URL}` é a URL a ser aberta (por exemplo, `https://localhost:5001` ).
    * Mozilla Firefox: use `-private -url {URL}` , em que o espaço reservado `{URL}` é a URL a ser aberta (por exemplo, `https://localhost:5001` ).
  * Forneça um nome no campo **nome amigável** . Por exemplo, `Firefox Auth Testing`.
  * Selecione o botão **OK** .
  * Para evitar ter que selecionar o perfil de navegador para cada iteração de teste com um aplicativo, defina o perfil como o padrão com o botão **definir como padrão** .
  * Verifique se o navegador está fechado pelo IDE para qualquer alteração no aplicativo, no usuário de teste ou na configuração do provedor.

### <a name="run-the-server-app"></a>Executar o aplicativo de servidor

Ao testar e solucionar problemas de um aplicativo de mais de um prazer, verifique se você está executando o aplicativo do **`Server`** projeto. Por exemplo, no Visual Studio, confirme se o projeto do servidor está realçado em **Gerenciador de soluções** antes de iniciar o aplicativo com qualquer uma das seguintes abordagens:

* Selecione o botão **Executar**.
* Use **depurar**  >  **Iniciar Depuração** no menu.
* Pressione <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Inspecionar o conteúdo de um token Web JSON (JWT)

Para decodificar um JSON Web token (JWT), use a ferramenta [JWT.ms](https://jwt.ms/) da Microsoft. Os valores na interface do usuário nunca deixam seu navegador.
