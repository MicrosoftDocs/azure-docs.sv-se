---
title: Översikt över Azure Relay API | Microsoft Docs
description: 'Den här artikeln innehåller en översikt över tillgängliga Azure Relay-API: er (.NET standard, .NET Framework, Node.js osv.)'
ms.topic: article
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 391bd74f2bce8721b6d6359f3990af494277aa45
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313002"
---
# <a name="available-relay-apis"></a>Tillgängliga relä-API: er

## <a name="runtime-apis"></a>Körnings-API: er

I följande tabell visas alla tillgängliga relä körnings klienter.

Avsnittet [Ytterligare information](#additional-information) innehåller mer information om status för varje körnings bibliotek.

| Språk/plattform | Tillgänglig funktion | Klientpaketet | Lagringsplats |
| --- | --- | --- | --- |
| .NET Standard | Hybridanslutningar | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | WCF-relä | [WindowsAzure. Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | Ej tillämpligt |
| Nod | Hybridanslutningar | [WebSockets `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[WebSockets `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[HTTP-förfrågningar: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Ytterligare information

#### <a name="net"></a>.NET

.NET-eko systemet har flera körningar, och därför finns det flera .NET-bibliotek för reläet. Du kan köra .NET standard-biblioteket med hjälp av antingen .NET Core eller .NET Framework, medan .NET Frameworks biblioteket bara kan köras i en .NET Framework miljö. Mer information om .NET Framework finns i Framework- [versioner](/dotnet/articles/standard/frameworks).

.NET Framework bibliotek stöder bara WCF-programmerings modellen och förlitar sig på ett patentskyddat binärt protokoll baserat på WCF- `net.tcp` transporten. Det här protokollet och biblioteket upprätthålls för bakåtkompatibilitet med befintliga program.

.NET standard-biblioteket baseras på Open Protocol-definitionen för det Hybridanslutningar reläet som bygger på HTTP och WebSockets. Biblioteket har stöd för en Stream-abstraktion över WebSockets och en enkel API-gest för Request-Response för att besvara HTTP-förfrågningar. I [webb-API-](https://github.com/Azure/azure-relay-dotnet) exemplet visas hur du integrerar Hybridanslutningar med ASP.net Core för webb tjänster.

#### <a name="nodejs"></a>Node.js

De Hybridanslutningar modulerna som anges i tabellen ovan Ersätt eller ändra befintliga Node.js-moduler med alternativa implementeringar som lyssnar på Azure Relay-tjänsten i stället för den lokala nätverks stacken.

`hyco-https`Modulen ändrar och delvis åsidosätter kärnan Node.js moduler `http` och `https` tillhandahåller en https-lyssnare som är kompatibel med många befintliga Node.js moduler och program som är beroende av dessa huvud moduler.

`hyco-ws` `hyco-websocket` Modulerna och ändrar de populära `ws` och `websocket` modulerna för Node.js, vilket ger alternativa lyssnar implementeringar som gör att moduler och program som förlitar sig på någon av modulerna fungerar bakom hybridanslutningar reläet.

Information om dessa moduler finns i [Azure-relä-Node GitHub-](https://github.com/Azure/azure-relay-node) lagringsplatsen.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure Relay går du till följande länkar:
* [Vad är Azure Relay?](relay-what-is-it.md)
* [Vanliga frågor och svar om Relay](relay-faq.yml)
