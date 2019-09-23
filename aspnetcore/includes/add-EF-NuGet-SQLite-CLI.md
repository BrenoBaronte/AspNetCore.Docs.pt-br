Execute os seguintes comandos da CLI do .NET Core:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

Os comandos anteriores adicionam:

* A [ferramenta ASPNET-CodeGenerator scaffolding](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* As ferramentas de Entity Framework Core para o CLI do .NET Core.
* O provedor do EF Core SQLite, que instala o pacote EF Core como uma dependência.
* Pacotes necessários para scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` e `Microsoft.EntityFrameworkCore.SqlServer`.