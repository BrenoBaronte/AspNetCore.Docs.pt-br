---
title: Visão geral das APIs do consumidor para ASP.NET Core
author: rick-anderson
description: Receba uma breve visão geral das várias APIs de consumidor disponíveis na biblioteca de proteção de dados ASP.NET Core.
ms.author: riande
ms.date: 06/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 0bb163b677062c46a077731c014a40c602764594
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406713"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Visão geral das APIs do consumidor para ASP.NET Core

As `IDataProtectionProvider` `IDataProtector` interfaces e são as interfaces básicas por meio das quais os consumidores usam o sistema de proteção de dados. Eles estão localizados no pacote [Microsoft. AspNetCore. dataprotection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>IDataProtectionProvider

A interface do provedor representa a raiz do sistema de proteção de dados. Ele não pode ser usado diretamente para proteger ou desproteger dados. Em vez disso, o consumidor deve obter uma referência para um `IDataProtector` chamando `IDataProtectionProvider.CreateProtector(purpose)` , em que a finalidade é uma cadeia de caracteres que descreve o caso de uso pretendido do consumidor. Consulte [cadeias de finalidade](xref:security/data-protection/consumer-apis/purpose-strings) para obter muito mais informações sobre a intenção desse parâmetro e como escolher um valor apropriado.

## <a name="idataprotector"></a>IDataProtector

A interface de protetor é retornada por uma chamada para `CreateProtector` , e é essa interface que os consumidores podem usar para executar operações de proteger e desproteger.

Para proteger um dado, passe os dados para o `Protect` método. A interface básica define um método que converte o byte []-> byte [], mas também há uma sobrecarga (fornecida como um método de extensão) que converte cadeia de caracteres > cadeia. A segurança oferecida pelos dois métodos é idêntica; o desenvolvedor deve escolher qualquer sobrecarga que seja mais conveniente para seu caso de uso. Independentemente da sobrecarga escolhida, o valor retornado pelo método Protect agora é protegido (criptografado e prova de adulteração) e o aplicativo pode enviá-lo para um cliente não confiável.

Para desproteger um pedaço de dados protegido anteriormente, passe os dados protegidos para o `Unprotect` método. (Há sobrecargas baseadas em byte [] e baseadas em cadeia de caracteres para conveniência do desenvolvedor). Se a carga protegida tiver sido gerada por uma chamada anterior para `Protect` `IDataProtector` , o `Unprotect` método retornará a carga original não protegida. Se a carga protegida tiver sido violada ou tiver sido produzida por um diferente `IDataProtector` , o `Unprotect` método gerará CryptographicException.

O conceito do mesmo vs. diferente `IDataProtector` se vincula ao conceito de finalidade. Se duas `IDataProtector` instâncias tiverem sido geradas a partir da mesma raiz, `IDataProtectionProvider` mas por meio de cadeias de finalidade diferentes na chamada para `IDataProtectionProvider.CreateProtector` , elas serão consideradas [protetores diferentes](xref:security/data-protection/consumer-apis/purpose-strings)e uma não será capaz de desproteger as cargas geradas pelo outro.

## <a name="consuming-these-interfaces"></a>Consumindo essas interfaces

Para um componente com reconhecimento de DI, o uso pretendido é que o componente usa um `IDataProtectionProvider` parâmetro em seu construtor e que o sistema de di fornece automaticamente esse serviço quando o componente é instanciado.

> [!NOTE]
> Alguns aplicativos (como aplicativos de console ou aplicativos ASP.NET 4. x) podem não ser compatíveis com DI, portanto não podem usar o mecanismo descrito aqui. Para esses cenários, consulte o documento [cenários de reconhecimento não-di](xref:security/data-protection/configuration/non-di-scenarios) para obter mais informações sobre como obter uma instância de um `IDataProtection` provedor sem passar por di.

O exemplo a seguir demonstra três conceitos:

1. [Adicionar o sistema de proteção de dados](xref:security/data-protection/configuration/overview) ao contêiner de serviço,

2. Usando DI para receber uma instância de um `IDataProtectionProvider` e

3. Criar um `IDataProtector` de um `IDataProtectionProvider` e usá-lo para proteger e desproteger dados.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

O pacote Microsoft. AspNetCore. dataprotection. abstrações contém um método de extensão `IServiceProvider.GetDataProtector` como uma conveniência do desenvolvedor. Ele encapsula como uma única operação, recuperando um `IDataProtectionProvider` do provedor de serviços e chamando `IDataProtectionProvider.CreateProtector` . O exemplo a seguir demonstra seu uso.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instâncias do `IDataProtectionProvider` e `IDataProtector` são thread-safe para vários chamadores. É intencional que, uma vez que um componente obtenha uma referência a um `IDataProtector` por meio de uma chamada para `CreateProtector` , ele usará essa referência para várias chamadas para `Protect` e `Unprotect` . Uma chamada para `Unprotect` emitirá CryptographicException se a carga protegida não puder ser verificada ou decifrada. Alguns componentes podem querer ignorar erros durante operações de desproteção; um componente que lê cookies de autenticação pode lidar com esse erro e tratar a solicitação como se não tivesse nenhum cookie em vez de falhar a solicitação imediatamente. Os componentes que desejam esse comportamento devem capturar especificamente CryptographicException em vez de assimilar todas as exceções.
