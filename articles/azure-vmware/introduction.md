---
title: Introduktion
description: Lär dig om funktionerna och fördelarna med Azure VMware-lösningen för att distribuera och hantera VMware-baserade arbets belastningar i Azure.
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 32dd73108dc12d7d7acc605f43a0fbf6d2cf1cd7
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107998"
---
# <a name="what-is-azure-vmware-solution"></a>Vad är Azure VMware Solution?

Med Azure VMware-lösningen får du privata moln som innehåller vSphere-kluster som bygger på dedikerad Azure-infrastruktur utan operativ system. Den minsta första distributionen är tre värdar, men ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster.  Alla etablerade privata moln har vCenter Server, virtuellt San, vSphere och NSX-T. Du kan migrera arbets belastningar från dina lokala miljöer, distribuera nya virtuella datorer (VM) och använda Azure-tjänster från dina privata moln.

Azure VMware-lösningen är en VMware-verifierad lösning med pågående verifiering och testning av förbättringar och uppgraderingar. Microsoft hanterar och upprätthåller infrastrukturen för privata moln och program vara. Det gör att du kan fokusera på att utveckla och köra arbets belastningar i dina privata moln. 

Diagrammet visar sammanlänkning mellan privata moln och virtuella nätverk i Azure, Azure-tjänster och lokala miljöer. Nätverks åtkomst från privata moln till Azure-tjänster eller virtuella nätverk tillhandahåller SLA-driven integrering av Azures tjänst slut punkter. ExpressRoute Global Reach ansluter din lokala miljö till ditt privata moln i Azure VMware-lösningen. 

![Bild av Azure VMware-lösning för privat moln sammanlänkning till Azure och lokalt](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Värdar, kluster och privata moln

Privata moln och kluster i Azure VMware-lösningen bygger på en dator utan operativ system som är värd för Azure-infrastrukturen utan operativ system. De avancerade värdarna har 576 GB RAM och dubbla Intel 18 kärnor, 2,3 GHz-processorer. Han är värd för att ha två virtuellt San-diskgroups med 15,36 TB (SSD) av RAW virtuellt San-kapacitets nivå och en 3,2 TB (NVMe) virtuellt San-cache-nivå.

Nya privata moln distribueras via Azure Portal eller Azure CLI.

## <a name="networking"></a>Nätverk

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Mer information finns i [nätverks koncept](concepts-networking.md).

## <a name="access-and-security"></a>Åtkomst och säkerhet

Privata moln i Azure VMware-lösningar använder vSphere rollbaserad åtkomst kontroll för förbättrad säkerhet. Du kan integrera vSphere SSO LDAP-funktioner med Azure Active Directory. Mer information finns i [begreppen åtkomst och identitet](concepts-identity.md).  

Virtuellt San data-at-rest-kryptering är som standard aktiverat och används för att tillhandahålla virtuellt San data lagrings säkerhet. Mer information finns i [Storage Concepts](concepts-storage.md).

## <a name="host-and-software-lifecycle-maintenance"></a>Underhåll av värd-och program varu livs cykel

Vanliga uppgraderingar av Azure VMware-lösningen privat moln och VMware-programvara ser till att de senaste säkerhets-, stabilitets-och funktions uppsättningarna körs i dina privata moln. Mer information finns i [uppdateringar och uppgraderingar av privata moln](concepts-upgrades.md).

## <a name="monitoring-your-private-cloud"></a>Övervaka ditt privata moln

När Azure VMware-lösningen har distribuerats i din prenumeration genereras [Azure Monitor loggar](../azure-monitor/overview.md) automatiskt. 

I ditt privata moln kan du:
- Samla in loggar på var och en av dina virtuella datorer.
- [Hämta och installera MMA-agenten](../azure-monitor/agents/log-analytics-agent.md#installation-options) på virtuella Linux-och Windows-datorer.
- Aktivera [Azure Diagnostics-tillägget](../azure-monitor/agents/diagnostics-extension-overview.md).
- [Skapa och kör nya frågor](../azure-monitor/logs/data-platform-logs.md#log-queries).
- Kör samma frågor som du brukar köra på dina virtuella datorer.

Övervaknings mönster i Azure VMware-lösningen liknar virtuella Azure-datorer i IaaS-plattformen. Mer information och instruktioner finns i [övervaka virtuella Azure-datorer med Azure Monitor](../azure-monitor/vm/monitor-vm-azure.md).

## <a name="customer-communication"></a>Kund kommunikation
[!INCLUDE [customer-communications](includes/customer-communications.md)]

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig viktiga [koncept för privata moln och kluster](concepts-private-clouds-clusters.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md


