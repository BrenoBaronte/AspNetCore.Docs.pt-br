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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551661"
---
> [!NOTE]
> Para este tutorial, use o recurso de *migrações* do Entity Framework Core sempre que possível. As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados. No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados. Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna. Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha. Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite. Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.
>
>A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado. Uma recompilação de tabela envolve:
>
>* Criar uma nova tabela.
>* Copiar dados da tabela antiga para a nova tabela.
>* Remover a tabela antiga.
>* Renomear a nova tabela.
>
>Para obter mais informações, consulte os seguintes recursos:
>
> * [Limitações do Provedor de Banco de Dados EF Core do SQLite](/ef/core/providers/sqlite/limitations)
> * [Personalizar o código de migração](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Propagação de dados](/ef/core/modeling/data-seeding)
  * [Instrução SQLite ALTER TABLE](https://sqlite.org/lang_altertable.html)