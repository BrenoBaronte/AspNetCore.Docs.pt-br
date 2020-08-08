---
title: Próximas etapas-DevOps com o ASP.NET Core e o Azure
author: CamSoper
description: Outros caminhos de aprendizagem para DevOps com o ASP.NET Core e o Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: feff73b307791c0a7ad8968b5145c0388df1ca10
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012574"
---
# <a name="next-steps"></a><span data-ttu-id="91fe1-103">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="91fe1-103">Next steps</span></span>

<span data-ttu-id="91fe1-104">Neste guia, você criou um pipeline do DevOps para um aplicativo de exemplo ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="91fe1-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="91fe1-105">Parabéns!</span><span class="sxs-lookup"><span data-stu-id="91fe1-105">Congratulations!</span></span> <span data-ttu-id="91fe1-106">Esperamos que você tenha gostado de aprender a publicar ASP.NET Core aplicativos Web para Azure App serviço e automatizar a integração contínua das alterações.</span><span class="sxs-lookup"><span data-stu-id="91fe1-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="91fe1-107">Além de hospedagem na Web e DevOps, o Azure tem uma ampla gama de serviços de PaaS (plataforma como serviço) úteis para ASP.NET Core desenvolvedores.</span><span class="sxs-lookup"><span data-stu-id="91fe1-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="91fe1-108">Esta seção fornece uma breve visão geral de alguns dos serviços mais usados.</span><span class="sxs-lookup"><span data-stu-id="91fe1-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="91fe1-109">Armazenamento e banco de dados</span><span class="sxs-lookup"><span data-stu-id="91fe1-109">Storage and databases</span></span>

<span data-ttu-id="91fe1-110">O [cache Redis](/azure/redis-cache/) é um cache de dados de baixa latência e alta taxa de transferência disponível como um serviço.</span><span class="sxs-lookup"><span data-stu-id="91fe1-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="91fe1-111">Ele pode ser usado para cache de saída de página, reduzindo solicitações de banco de dados e fornecendo ASP.NET Core estado de sessão em várias instâncias de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="91fe1-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="91fe1-112">O [armazenamento do Azure](/azure/storage/) é um armazenamento em nuvem altamente escalonável do Azure.</span><span class="sxs-lookup"><span data-stu-id="91fe1-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="91fe1-113">Os desenvolvedores podem aproveitar o [armazenamento de filas](/azure/storage/queues/storage-queues-introduction) para o serviço de enfileiramento de mensagens confiável, e o [armazenamento de tabelas](/azure/storage/tables/table-storage-overview) é um repositório de chave-valor NoSQL projetado para um rápido desenvolvimento usando conjuntos de dados maciços e semiestruturados.</span><span class="sxs-lookup"><span data-stu-id="91fe1-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="91fe1-114">O [banco de dados SQL do Azure](/azure/sql-database/) fornece funcionalidade familiar de banco de dados relacional como um serviço usando o mecanismo de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="91fe1-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="91fe1-115">[Cosmos DB](/azure/cosmos-db/) serviço de banco de dados NoSQL de vários modelos distribuído globalmente.</span><span class="sxs-lookup"><span data-stu-id="91fe1-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="91fe1-116">Várias APIs estão disponíveis, incluindo a API do SQL (anteriormente chamada de DocumentDB), Cassandra e MongoDB.</span><span class="sxs-lookup"><span data-stu-id="91fe1-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="91fe1-117">[Azure Active Directory](/azure/active-directory/) e [Azure Active Directory B2C](/azure/active-directory-b2c/) são serviços de identidade.</span><span class="sxs-lookup"><span data-stu-id="91fe1-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="91fe1-118">A Azure Active Directory é projetada para cenários empresariais e habilita a colaboração B2B (entre empresas) do Azure AD, enquanto Azure Active Directory B2C se destina a cenários entre clientes, incluindo a entrada de rede social.</span><span class="sxs-lookup"><span data-stu-id="91fe1-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="91fe1-119">Móvel</span><span class="sxs-lookup"><span data-stu-id="91fe1-119">Mobile</span></span>

<span data-ttu-id="91fe1-120">Os [hubs de notificação](/azure/notification-hubs/) são um mecanismo de notificação por push de várias plataformas e escalonáveis para enviar rapidamente milhões de mensagens para aplicativos em execução em vários tipos de dispositivos.</span><span class="sxs-lookup"><span data-stu-id="91fe1-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="91fe1-121">Infraestrutura da Web</span><span class="sxs-lookup"><span data-stu-id="91fe1-121">Web infrastructure</span></span>

<span data-ttu-id="91fe1-122">O [serviço de contêiner do Azure](/azure/aks/) gerencia seu ambiente kubernetes hospedado, tornando rápido e fácil implantar e gerenciar aplicativos em contêineres sem conhecimento de orquestração de contêiner.</span><span class="sxs-lookup"><span data-stu-id="91fe1-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="91fe1-123">[Azure Search](/azure/search/) é usado para criar uma solução de pesquisa empresarial sobre conteúdo privado e heterogêneo.</span><span class="sxs-lookup"><span data-stu-id="91fe1-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="91fe1-124">O [Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos que torna mais fácil empacotar, implantar e gerenciar microserviços e contêineres escalonáveis e confiáveis.</span><span class="sxs-lookup"><span data-stu-id="91fe1-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
