---
title: Proteger ASP.NET Core Blazor Server aplicativos
author: guardrex
description: Saiba como proteger aplicativos Blazor Server como ASP.NET Core aplicativos.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280316"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Proteger ASP.NET Core Blazor Server aplicativos

Blazor Server os aplicativos são configurados para segurança da mesma maneira que ASP.NET Core aplicativos. Para obter mais informações, consulte os artigos em <xref:security/index> . Os tópicos desta visão geral se aplicam especificamente ao Blazor Server .

## <a name="blazor-server-project-template"></a>Blazor Server modelo de projeto

O Blazor Server modelo de projeto pode ser configurado para autenticação quando o projeto é criado.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Siga as diretrizes do Visual Studio no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação.

Depois de escolher o modelo de **Blazor Server aplicativo** na caixa de diálogo **criar um novo ASP.NET Core aplicativo Web** , selecione **alterar** em **autenticação**.

Uma caixa de diálogo é aberta para oferecer o mesmo conjunto de mecanismos de autenticação para outros projetos ASP.NET Core:

* **Sem autenticação**
* **Contas de usuário individuais**: as contas de usuário podem ser armazenadas:
  * Dentro do aplicativo usando o sistema do ASP.NET Core [Identity](xref:security/authentication/identity) .
  * Com o [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Contas corporativas ou de estudante**
* **Autenticação do Windows**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Siga as diretrizes de Visual Studio Code no <xref:blazor/tooling> para criar um novo Blazor Server projeto com um mecanismo de autenticação:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação | Descrição |
| ------------------------ | ----------- |
| `None` (padrão)         | Sem autenticação |
| `Individual`             | Usuários armazenados no aplicativo com ASP.NET Core Identity |
| `IndividualB2C`          | Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticação organizacional para um único locatário |
| `MultiOrg`               | Autenticação organizacional para vários locatários |
| `Windows`                | Autenticação do Windows |

Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:

* Crie uma pasta para o projeto.
* Nomeie o projeto.

Para obter mais informações, consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/visual-studio-mac)

1. Siga as diretrizes de Visual Studio para Mac no <xref:blazor/tooling> .

1. Na etapa **configurar seu novo Blazor Server aplicativo** , selecione **autenticação individual (no aplicativo)** na lista suspensa **autenticação** .

1. O aplicativo é criado para usuários individuais armazenados no aplicativo com ASP.NET Core Identity .

# <a name="net-core-cli"></a>[CLI do .NET Core](#tab/netcore-cli/)

Crie um novo Blazor Server projeto com um mecanismo de autenticação usando o seguinte comando em um shell de comando:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Os valores de autenticação permitidos (`{AUTHENTICATION}`) são mostrados na tabela a seguir.

| Mecanismo de autenticação | Descrição |
| ------------------------ | ----------- |
| `None` (padrão)         | Sem autenticação |
| `Individual`             | Usuários armazenados no aplicativo com ASP.NET Core Identity |
| `IndividualB2C`          | Usuários armazenados no [Azure ad B2C](xref:security/authentication/azure-ad-b2c) |
| `SingleOrg`              | Autenticação organizacional para um único locatário |
| `MultiOrg`               | Autenticação organizacional para vários locatários |
| `Windows`                | Autenticação do Windows |

Usando a `-o|--output` opção, o comando usa o valor fornecido para o `{APP NAME}` espaço reservado para:

* Crie uma pasta para o projeto.
* Nomeie o projeto.

Para mais informações:

* Consulte o [`dotnet new`](/dotnet/core/tools/dotnet-new) comando no guia do .NET Core.
* Execute o comando de ajuda para o Blazor Server modelo ( `blazorserver` ) em um shell de comando:

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a>Scaffold Identity

Scaffold Identity em um Blazor Server projeto:

* [Sem autorização existente](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).
* [Com autorização](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).

## <a name="azure-app-service-on-linux-with-identity-server"></a>Serviço de Azure App no Linux com o Identity servidor

Especifique o emissor explicitamente ao implantar no serviço de Azure App no Linux com o Identity servidor. Para obter mais informações, consulte <xref:security/authentication/identity/spa#azure-app-service-on-linux>.

## <a name="additional-resources"></a>Recursos adicionais

* [Início Rápido: Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [Início Rápido: Proteger uma API Web ASP.NET Core com a plataforma de identidade da Microsoft](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <xref:host-and-deploy/proxy-load-balancer>: Inclui diretrizes sobre:
  * Usando o middleware de cabeçalhos encaminhados para preservar as informações do esquema HTTPS entre servidores proxy e redes internas.
  * Cenários adicionais e casos de uso, incluindo configuração de esquema manual, alterações de caminho de solicitação para roteamento de solicitação correto e encaminhamento do esquema de solicitação para proxies inversos do Linux e não do IIS.
