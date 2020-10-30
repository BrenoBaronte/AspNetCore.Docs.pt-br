---
title: Publicar um SignalR aplicativo de ASP.NET Core no serviço Azure app
author: bradygaster
description: Saiba como publicar um SignalR aplicativo de ASP.NET Core no serviço Azure app.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: e00eea81788c9b335691b7e5ffe6a46534c3c492
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058214"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a>Publicar um SignalR aplicativo de ASP.NET Core no serviço Azure app

Por [Brady GASTER](https://twitter.com/bradygaster)

O [serviço de Azure app](/azure/app-service/app-service-web-overview) é um serviço de plataforma de computação em nuvem da [Microsoft](https://azure.microsoft.com/) para hospedar aplicativos Web, incluindo ASP.NET Core.

> [!NOTE]
> Este artigo refere-se à publicação de um SignalR aplicativo ASP.NET Core do Visual Studio. Para obter mais informações, consulte [ SignalR Service for Azure](https://azure.microsoft.com/services/signalr-service).

## <a name="publish-the-app"></a>Publicar o aplicativo

Este artigo aborda a publicação usando as ferramentas do Visual Studio. Visual Studio Code usuários podem usar [CLI do Azure](/cli/azure) comandos para publicar aplicativos no Azure. Para obter mais informações, consulte [publicar um aplicativo ASP.NET Core no Azure com ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet).

1. Clique com o botão direito do mouse no projeto, no **Gerenciador de Soluções** , e selecione **Publicar** .

1. Confirme se o **serviço de aplicativo** e **criar novo** estão selecionados na caixa de diálogo **escolher um destino de publicação** .

1. Selecione **Criar perfil** na lista suspensa do botão **publicar** .

   Insira as informações descritas na tabela a seguir na caixa de diálogo **Criar serviço de aplicativo** e selecione **criar** .

   | Item               | Descrição |
   | ------------------ | ----------- |
   | **Nome**           | Nome exclusivo do aplicativo. |
   | **Assinatura**   | Assinatura do Azure que o aplicativo usa. |
   | **Grupo de Recursos** | Grupo de recursos relacionados aos quais o aplicativo pertence. |
   | **Plano de hospedagem**   | Plano de preços para o aplicativo Web. |

1. Selecione o **SignalR serviço do Azure** na lista suspensa **dependências**  >  **Adicionar** :

   ![Área de dependências mostrando a seleção de Azure::: no-Loc (Signalr)::: Service na lista suspensa adicionar](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. Na caixa de diálogo **SignalR serviço do Azure** , selecione **criar uma nova SignalR instância de serviço do Azure** .

1. Forneça um **nome** , um **grupo de recursos** e um **local** . Volte para a caixa de diálogo **SignalR serviço do Azure** e selecione **Adicionar** .

O Visual Studio conclui as seguintes tarefas:

* Cria um perfil de publicação que contém as configurações de publicação.
* Cria um *aplicativo Web do Azure* com os detalhes fornecidos.
* Publica o aplicativo.
* Inicia um navegador, que carrega o aplicativo Web.

O formato da URL do aplicativo é `{APP SERVICE NAME}.azurewebsites.net` . Por exemplo, um aplicativo chamado `SignalRChatApp` tem uma URL de `https://signalrchatapp.azurewebsites.net` .

Se ocorrer um erro de *Gateway insatisfatório HTTP 502,2* ao implantar um aplicativo destinado a uma versão prévia do .NET Core, consulte [implantar ASP.NET Core versão de visualização para Azure app serviço](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) para resolvê-lo.

## <a name="configure-the-app-in-azure-app-service"></a>Configurar o aplicativo no serviço Azure App

> [!NOTE]
> *Esta seção se aplica somente a aplicativos que não usam o serviço do Azure SignalR .*
>
> Se o aplicativo usar o serviço do Azure SignalR , o serviço de aplicativo não exigirá a configuração de afinidade de Application Request Routing (ARR) e os soquetes da Web descritos nesta seção. Os clientes conectam seus soquetes Web ao serviço do Azure SignalR , não diretamente ao aplicativo.

Para aplicativos hospedados sem o SignalR serviço do Azure, habilite:

* [Afinidade ARR] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(Arr- cookie ) -for-Azure-web-apps.html) para rotear solicitações de um usuário de volta para a mesma instância do serviço de aplicativo. A configuração padrão é **on** .
* [Web Sockets](xref:fundamentals/websockets) para permitir que o transporte de soquetes da Web funcione. A configuração padrão é **off** .

1. Na portal do Azure, navegue até o aplicativo Web nos **serviços de aplicativos** .
1. Abra **Configuration**  >  **configurações gerais** de configuração.
1. Defina os **soquetes da Web** como **ativado** .
1. Verifique se a **afinidade arr** está definida como **on** .

## <a name="app-service-plan-limits"></a>Limites do plano do serviço de aplicativo

Os soquetes da Web e outros transportes são limitados com base no plano do serviço de aplicativo selecionado. Para obter mais informações, consulte as seções *limites de serviços de nuvem do Azure* e limites de serviço de *aplicativo* do artigo [assinatura e limites de serviço, cotas e restrições do Azure](/azure/azure-subscription-service-limits#app-service-limits) .

## <a name="additional-resources"></a>Recursos adicionais

* [O que é o serviço do Azure SignalR ?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Publicar um aplicativo ASP.NET Core no Azure com as ferramentas de linha de comando](/azure/app-service/app-service-web-get-started-dotnet)
* [Hospedar e implantar ASP.NET Core aplicativos de visualização no Azure](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
