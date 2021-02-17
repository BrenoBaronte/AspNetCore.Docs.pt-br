---
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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552182"
---
> [!NOTE]
> 
> **Limitações do SQLite**
>
> Este tutorial usa o recurso de *migrações* do Entity Framework Core sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só fazem os tipos de alterações compatíveis com o mecanismo de banco de dados e os recursos de alteração de esquema do SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção de uma coluna. Se uma migração for criada para remover uma coluna, o comando `ef migrations add` terá êxito, mas o comando `ef database update` falhará. 
>
> A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. O código seria inserido nos métodos `Up` e `Down` para uma migração e envolveria:
>
> * Criar uma nova tabela.
> * Copiar dados da tabela antiga para a nova tabela.
> * Remover a tabela antiga.
> * Renomear a nova tabela.
>
> Escrever código específico do banco de dados desse tipo está fora do escopo deste tutorial. Em vez disso, este tutorial descarta e recria o banco de dados sempre que uma tentativa de aplicar uma migração falha. Para obter mais informações, consulte os seguintes recursos:
>
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
> * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)