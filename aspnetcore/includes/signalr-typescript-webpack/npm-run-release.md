```console
npm run release
```

Esse comando gera os ativos do lado do cliente a serem atendidos ao executar o aplicativo. Os ativos são colocados na pasta *wwwroot*.

O Webpack concluiu as seguintes tarefas:

* Limpou os conteúdos do diretório *wwwroot*.
* O TypeScript foi convertido em JavaScript em um processo conhecido como *transpilação*.
* O JavaScript gerado foi desconfigurado para reduzir o tamanho do arquivo em um processo conhecido como *minificação*.
* Copiou os arquivos JavaScript, CSS e HTML processados do *src* para o diretório *wwwroot*.
* Injetou os seguintes elementos no arquivo *wwwroot/index.html*:
  * Uma `<link>` marca, referenciando o *wwwroot/Main. \<hash\> . arquivo CSS* . Essa marca é colocada imediatamente antes do fim da marca `</head>`.
  * Uma `<script>` marca, referenciando reduzidos *wwwroot/Main. \<hash\> . arquivo js* . Essa marca é colocada imediatamente antes do fim da marca `</body>`.