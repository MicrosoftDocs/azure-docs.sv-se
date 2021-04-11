---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 04/07/2021
ms.topic: include
ms.openlocfilehash: 73886b966676af75cc74484ccdc0632f080b041a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029156"
---
Azure Defender tillhandahåller hot skydd i molnet för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Azure Defender tillhandahåller hot skydd på olika nivåer: 

* **Värdnivå (tillhandahålls av Azure Defender för servrar)** – med samma Log Analytics-agent som Security Center använder på andra virtuella datorer, övervakar Azure Defender dina Linux Kubernetes-noder för misstänkta aktiviteter som identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser. Agenten övervakar även certifikatbaserad analys, till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    Om du väljer att inte installera agenterna på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

    >[!IMPORTANT]
    > Vi stöder för närvarande inte installation av Log Analytics agent på Azure Kubernetes service-kluster som körs på virtuella datorers skalnings uppsättningar.

    En lista över aviseringar på kluster nivå finns i [referens tabellen för aviseringar](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **Kluster nivå (tillhandahålls av Azure Defender for Kubernetes)** – på kluster nivå baseras hotet Protection på analyser av Kubernetes gransknings loggar. Aktivera Azure Defender om du vill aktivera övervakning utan **agent** . Aktivera [Arc-aktiverade Kubernetes och Azure Defender-tillägget](../articles/security-center/defender-for-kubernetes-azure-arc.md)om klustret är lokalt eller på en annan moln leverantör.

    För att generera aviseringar på den här nivån övervakar Azure Defender dina kluster loggar. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar.

    >[!NOTE]
    > Azure Defender genererar säkerhets aviseringar för åtgärder och distributioner som inträffar när du har aktiverat Defender for Kubernetes-planen i din prenumeration. 

    En lista över aviseringar på kluster nivå finns i [referens tabellen för aviseringar](../articles/security-center/alerts-reference.md#alerts-akscluster).

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

> [!TIP]
> Du kan simulera behållar aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).