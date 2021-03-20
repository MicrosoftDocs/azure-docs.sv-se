---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 06/30/2020
ms.topic: include
ms.openlocfilehash: 4c09057f606423dc92b3364e502e632a385bf83f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100007825"
---
Security Center tillhandahåller hot skydd i real tid för dina behållares miljöer och genererar aviseringar för misstänkta aktiviteter. Du kan använda den här informationen för att snabbt åtgärda säkerhetsproblem och förbättra säkerheten för dina containrar.

Security Center ger hot skydd på olika nivåer: 

* **Värdnivå (tillhandahålls av Azure Defender för servrar)** – med samma Log Analytics-agent som Security Center använder på andra virtuella datorer, övervakar Azure Defender dina Linux AKS-noder för misstänkta aktiviteter som identifiering av webb gränssnitt och anslutning med kända misstänkta IP-adresser. Agenten övervakar även certifikatbaserad analys, till exempel skapande av privilegierade behållare, misstänkt åtkomst till API-servrar och SSH-servrar (Secure Shell) som körs i en Docker-behållare.

    Om du väljer att inte installera agenterna på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

    >[!IMPORTANT]
    > Vi stöder för närvarande inte installation av Log Analytics agent på Azure Kubernetes service-kluster som körs på virtuella datorers skalnings uppsättningar.

    En lista över AKS-aviseringar om värd nivå finns i [referens tabellen för aviseringar](../articles/security-center/alerts-reference.md#alerts-containerhost).


* **AKS-kluster nivå (tillhandahålls av Azure Defender för Kubernetes)** – på kluster nivå baseras skydd mot hot på analys av Kubernetes gransknings loggar. Aktivera Azure Defender om du vill aktivera övervakning utan **agent** . Om du vill generera aviseringar på den här nivån övervakar Security Center dina AKS-hanterade tjänster med hjälp av de loggar som hämtats av AKS. Exempel på händelser på den här nivån är exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar.

    >[!NOTE]
    > Security Center genererar säkerhets aviseringar för Azure Kubernetes service-åtgärder och distributioner som inträffar när alternativet Kubernetes har Aktiver ATS i prenumerations inställningarna. 

    En lista över AKS-aviseringar på kluster nivå finns i [referens tabellen för aviseringar](../articles/security-center/alerts-reference.md#alerts-akscluster).

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

> [!TIP]
> Du kan simulera behållar aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).