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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551574"
---
### <a name="view-the-identity-database"></a>Exibir o Identity banco de dados

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* No menu **Exibir** , selecione **pesquisador de objetos do SQL Server** (SSOX).
* Navegue até **(LocalDB) MSSQLLocalDB (SQL Server 13)**. Clique com o botão direito do mouse em **dbo. AspNetUsers**  >  **exibir dados**:

![Menu contextual na tabela AspNetUsers no Pesquisador de Objetos do SQL Server](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Há muitas ferramentas de terceiros que você pode baixar para gerenciar e exibir um banco de dados SQLite, por exemplo, [navegador de BD para SQLite](https://sqlitebrowser.org/).

---