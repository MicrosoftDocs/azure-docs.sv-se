---
title: Läs mer om support alternativ för Azure Service Fabric
description: Azure Service Fabric-kluster versioner stöds och länkar till fil support biljetter
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92316501"
---
# <a name="azure-service-fabric-support-options"></a>Support alternativ för Azure Service Fabric

Vi har skapat ett antal support begär ande alternativ för att uppfylla behoven för att hantera dina Service Fabric-kluster och program arbets belastningar. Beroende på hur brådskande support som krävs och problemets allvarlighets grad kan du välja det alternativ som passar dig bäst.

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>Rapportera produktions problem eller begär betald support för Azure

Om du vill rapportera problem som rör Service Fabric kluster som körs på Azure öppnar du ett support ärende [på Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) eller [Microsoft Support Portal](https://support.microsoft.com/oas/default.aspx?prid=16146).

Läs mer om:
 
- [Support från Microsoft för Azure](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier-Support](https://support.microsoft.com/en-us/premier).

> [!Note]
> Kluster som körs på en brons Tillförlitlighets nivå eller ett kluster med en nod kan bara köra test arbets belastningar. Om du får problem med ett kluster som körs på brons tillförlitlighet eller ett kluster med en enda nod, kommer Microsoft support team att hjälpa dig att lösa problemet, men det kommer inte att utföra en rotor Saks analys. Mer information finns i [Tillförlitlighets egenskaperna för klustret](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Mer information om vad som krävs för ett produktions klart kluster finns i [Check listan för produktions beredskap](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>Rapportera produktions problem eller begär betald support för fristående Service Fabric kluster

Om du vill rapportera problem som rör Service Fabric kluster som körs lokalt eller i andra moln, kan du öppna en biljett för professionell support på [Microsofts support Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Läs mer om:

- [Professional support från Microsoft för lokal](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
- [Microsoft Premier-Support](https://support.microsoft.com/en-us/premier).

## <a name="report-azure-service-fabric-issues"></a>Rapportera problem med Azure-Service Fabric

Vi har konfigurerat en GitHub-lagrings platsen för rapportering Service Fabric problem.  Vi arbetar också aktivt med att övervaka följande forum.

### <a name="github-repo"></a>GitHub lagrings platsen 

Rapportera problem med Azure-Service Fabric på [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues). Den här lagrings platsen är avsedd för rapporterings-och spårnings problem samt att göra små funktions förfrågningar relaterade till Azure Service Fabric. **Använd inte det här mediet för att rapportera problem med Live-webbplatser**.

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow-och MSDN-forum

[Taggen Service Fabric på StackOverflow][stackoverflow] och Service Fabric- [forumet på MSDN][msdn-forum] används bäst för att ställa frågor till allmänna frågor om hur plattformen fungerar och hur du kan använda den för att utföra vissa uppgifter.

### <a name="azure-feedback-forum"></a>Azure feedback-forum

[Azure feedback-forumet för Service Fabric][uservoice-forum] är den bästa platsen för att skicka betydande produkt funktions idéer. Vi går igenom de populäraste förfrågningarna och debiterar dem för vår medels enlångsiktiga planering. Vi rekommenderar att du Rally support för dina förslag i communityn.

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric för hands versioner – stöds inte för produktions användning

Ibland kan vi göra särskilda för hands versioner som innehåller betydande funktions förändringar för vilka vi skulle vilja undersöka tidigare feedback. Du bör endast använda för hands versioner i isolerade test miljöer som inte hanterar produktions arbets belastningar. Ditt produktions kluster bör alltid köra en stabil, stabil Service Fabric version som stöds. Vi erbjuder inte något avgiftsbelagd support-alternativ för dessa för hands versioner.

En för hands version börjar alltid med ett större och lägre versions nummer på 255. Om du till exempel ser en Service Fabric version 255.255.5703.949 är den här versionen i för hands version och är endast avsedd att användas i test kluster. De här för hands versionerna visas också i [Service Fabric teamets blogg](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) och innehåller information om de funktioner som ingår. Använd något av alternativen som anges under [rapportera Azure-Service Fabric problem](#report-azure-service-fabric-issues) för att ställa frågor eller ge feedback.

## <a name="next-steps"></a>Nästa steg

[Service Fabric versioner som stöds](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform