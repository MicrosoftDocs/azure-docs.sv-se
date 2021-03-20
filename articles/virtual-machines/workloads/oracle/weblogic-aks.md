---
title: Vad är lösningar för att köra Oracle WebLogic-Server i Azure Kubernetes-tjänsten
description: Lär dig hur du kör Oracle WebLogic Server på Azure Kubernetes-tjänsten.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: ac9f81fbde33bdd10bc8374a566a4f2ba83fc253
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669011"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Vad är lösningar för att köra Oracle WebLogic-Server i Azure Kubernetes-tjänsten?

På den här sidan beskrivs lösningar för att köra Oracle WebLogic Server (WLS) på Azure Kubernetes service (AKS). Dessa lösningar har utvecklats gemensamt och stöds av Oracle och Microsoft.

Det går också att köra WebLogic-Server på Azure Virtual Machines. Lösningarna för detta beskrivs i [den här artikeln i Microsoft](./oracle-weblogic.md).

WebLogic Server är en ledande Java-Programserver som kör några av de viktigaste viktiga företags Java-programmen över hela världen. WebLogic-servern utgör mellanvaru Stiftelsen för Oracle-programsviten. Oracle och Microsoft strävar efter att ge WebLogic Server kunder möjlighet att välja och flexibilitet för att köra arbets belastningar på Azure som en ledande moln plattform.

## <a name="wls-on-aks-certified-and-supported"></a>WLS on AKS-certifierad och stöds
WebLogic-servern certifieras av Oracle och Microsoft för att köra bra på AKS. WebLogic-servern på AKS-lösningarna syftar till att göra det så enkelt som möjligt att köra dina behållar och dirigerade Java-program i Docker-och Kubernetes-infrastrukturen. Lösningarna fokuserar på tillförlitlighet, skalbarhet, hanterbarhet och Enterprise support.

WebLogic Server-kluster är helt aktiverade för att köras på Kubernetes via WebLogic Kubernetes-operatören (kallas "operatören" här och senare). Operatorn följer standard mönstret för Kubernetes-operatorn. Det fören klar hanteringen och driften av WebLogic-domäner och distributioner på Kubernetes genom att automatisera andra manuella uppgifter och lägga till extra funktioner för drifts tillförlitlighet. Operatören har stöd för Oracle WebLogic Server 12C, Oracle Fusion mellanliggande infrastruktur 12C och senare. Vi har testat de officiella Docker-avbildningarna för WebLogic Server 12.2.1.3 och 12.2.1.4 med operatören. Mer information om operatören finns i den [officiella dokumentationen från Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Vägledning, skript och exempel för WLS på AKS
Utöver att certifiera WebLogic-servern på AKS, innehåller Oracle och Microsoft gemensamt detaljerade instruktioner, skript och exempel för att köra WebLogic-Server på AKS. Rikt linjerna är införlivade i avsnittet om Azure Kubernetes service-exempel i [operatörs dokumentationen](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/). Rikt linjerna syftar till att göra produktions WebLogic-Server på AKS-distributioner så enkelt som möjligt. Vägledningen använder officiella WebLogic Server Docker-avbildningar från Oracle. Redundansväxlingen uppnås via Azure Files som nås genom Kubernetes-beständiga volym anspråk. Azure Load Balancer stöds vid etablering med en Kubernetes-tjänst av typen LoadBalancer. Azure Container Registry (ACR) stöds för distribution av WLS-domäner i anpassade Docker-avbildningar. Vägledningen ger en hög grad av konfiguration och anpassning.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="Du kan använda exempel skripten för att distribuera WebLogic-servern på AKS":::

Lösningarna innehåller två sätt att distribuera WLS-domäner till AKS. Domäner kan distribueras direkt till Kubernetes-beständiga volymer. Det här distributions alternativet är bra om du vill migrera till AKS men ändå vill administrera WLS med hjälp av-administratörs konsolen eller WebLogic-skript verktyget (WLST). Med alternativet kan du också flytta till AKS utan att införa Docker-utveckling. Det mer Kubernetes inbyggda sättet att distribuera WLS-domäner till AKS är att bygga anpassade Docker-avbildningar baserade på officiella WLS-avbildningar från Oracle Container Registry, publicera de anpassade avbildningarna till ACR och distribuera domänen till AKS med hjälp av-operatorn. Med det här alternativet i lösningen kan du också uppdatera domänen genom Kubernetes ConfigMaps när distributionen är klar.

Ytterligare enkel användning och integrering av Azure-tjänster är möjliga i framtiden via Marketplace-erbjudanden som speglar Oracle WebLogic-Server på Azure Virtual Machines-lösningar.

_De här lösningarna är en egen licens_. De förutsätter att du redan har rätt licenser med Oracle och att de är korrekt licensierade för att köra erbjudanden i Azure.

_Om du är intresse rad av att jobba nära dina migrerings scenarier med teknik teamet som utvecklar dessa lösningar kan du fylla i [den här korta undersökningen](https://aka.ms/wls-on-azure-survey) och ta med din kontakt information_. Program chefer, arkitekter och tekniker kommer att kontakta dig inom kort och börja nära samarbete. Möjligheten att samar beta med ett migreringsjobb är kostnads fri medan lösningarna är aktiva inledande utveckling.

## <a name="deployment-architectures"></a>Distributions arkitekturer

Lösningarna för att köra Oracle WebLogic-servern på Azure Kubernetes-tjänsten gör det möjligt att använda en rad olika produktions klara distributions arkitekturer med relativt enkelt.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Komplexa WebLogic-Server distributioner är aktiverade på AKS":::

Utöver vad lösningarna ger kunderna fullständig flexibilitet att anpassa sina distributioner ytterligare. Det är sannolikt ovanpå att distribuera program kunder att integrera ytterligare Azure-resurser med sina distributioner. Kunderna uppmanas att ge feedback i undersökningen för att ytterligare förbättra lösningarna.

## <a name="next-steps"></a>Nästa steg

Utforska att köra Oracle WebLogic-servern på Azure Kubernetes-tjänsten.

> [!div class="nextstepaction"]
> [Vägledning, skript och exempel för att köra WLS på AKS](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes-operatör](https://oracle.github.io/weblogic-kubernetes-operator/)
