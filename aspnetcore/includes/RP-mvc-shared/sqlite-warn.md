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
> <span data-ttu-id="b493e-101">Para este tutorial, use o recurso de *migrações* do Entity Framework Core sempre que possível.</span><span class="sxs-lookup"><span data-stu-id="b493e-101">For this tutorial you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="b493e-102">As migrações atualizam o esquema de banco de dados para corresponder às alterações no modelo de dados.</span><span class="sxs-lookup"><span data-stu-id="b493e-102">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="b493e-103">No entanto, as migrações só podem fazer os tipos de alterações que o provedor do EF Core oferece suporte. Os recursos do provedor SQLite são limitados.</span><span class="sxs-lookup"><span data-stu-id="b493e-103">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="b493e-104">Por exemplo, há suporte para adicionar uma coluna, mas não há suporte para a remoção ou a alteração de uma coluna.</span><span class="sxs-lookup"><span data-stu-id="b493e-104">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="b493e-105">Se uma migração é criada para remover ou alterar uma coluna, o comando `ef migrations add` tem êxito, mas o comando `ef database update` falha.</span><span class="sxs-lookup"><span data-stu-id="b493e-105">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="b493e-106">Devido a essas limitações, este tutorial não usa as migrações para alterações de esquema do SQLite.</span><span class="sxs-lookup"><span data-stu-id="b493e-106">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="b493e-107">Em vez disso, quando o esquema for alterado, você deverá remover e recriar o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="b493e-107">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="b493e-108">A solução alternativa para as limitações do SQLite é escrever manualmente o código das migrações para executar uma recompilação de tabela quando algo na tabela for alterado.</span><span class="sxs-lookup"><span data-stu-id="b493e-108">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="b493e-109">Uma recompilação de tabela envolve:</span><span class="sxs-lookup"><span data-stu-id="b493e-109">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="b493e-110">Criar uma nova tabela.</span><span class="sxs-lookup"><span data-stu-id="b493e-110">Creating a new table.</span></span>
>* <span data-ttu-id="b493e-111">Copiar dados da tabela antiga para a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="b493e-111">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="b493e-112">Remover a tabela antiga.</span><span class="sxs-lookup"><span data-stu-id="b493e-112">Dropping the old table.</span></span>
>* <span data-ttu-id="b493e-113">Renomear a nova tabela.</span><span class="sxs-lookup"><span data-stu-id="b493e-113">Renaming the new table.</span></span>
>
><span data-ttu-id="b493e-114">Para obter mais informações, consulte os seguintes recursos:</span><span class="sxs-lookup"><span data-stu-id="b493e-114">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="b493e-115">Limitações do Provedor de Banco de Dados EF Core do SQLite</span><span class="sxs-lookup"><span data-stu-id="b493e-115">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="b493e-116">Personalizar o código de migração</span><span class="sxs-lookup"><span data-stu-id="b493e-116">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="b493e-117">Propagação de dados</span><span class="sxs-lookup"><span data-stu-id="b493e-117">Data seeding</span></span>](/ef/core/modeling/data-seeding)
  * [<span data-ttu-id="b493e-118">Instrução SQLite ALTER TABLE</span><span class="sxs-lookup"><span data-stu-id="b493e-118">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)