---
title: Introduktion
description: Lär dig funktionerna och fördelarna med Azure VMware Solution att distribuera och hantera VMware-baserade arbetsbelastningar i Azure. Azure VMware Solution serviceavtal garanterar att Azure VMware-hanteringsverktyg (vCenter Server och NSX Manager) kommer att vara tillgängliga minst 99,9 % av tiden.
ms.topic: overview
ms.date: 04/20/2021
ms.openlocfilehash: 9471aa2e427d28e0b8211dc8b25b2e61a4bfa8c4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752650"
---
# <a name="what-is-azure-vmware-solution"></a>Vad är Azure VMware Solution?

Azure VMware Solution med privata moln som innehåller vSphere-kluster som skapats från dedikerad Azure-infrastruktur utan metal. Den minsta inledande distributionen är tre värdar, men ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster.  Alla etablerade privata moln har vCenter Server, vSAN, vSphere och NSX-T. Du kan migrera arbetsbelastningar från dina lokala miljöer, distribuera nya virtuella datorer (VM) och använda Azure-tjänster från dina privata moln.  Azure VMware-hanteringsverktyg (vCenter Server och NSX Manager) kommer att vara tillgängliga minst 99,9 % av tiden. Mer information finns i [Azure VMware Solution serviceavtal](https://aka.ms/avs/sla).

Azure VMware Solution är en VMware-verifierad lösning med en framtida validering och testning av förbättringar och uppgraderingar. Microsoft hanterar och underhåller infrastruktur och programvara i privata moln. Du kan fokusera på att utveckla och köra arbetsbelastningar i dina privata moln. 

Diagrammet visar angränsande mellan privata moln och virtuella nätverk i Azure, Azure-tjänster och lokala miljöer. Nätverksåtkomst från privata moln till Azure-tjänster eller virtuella nätverk ger SLA-driven integrering av Azure-tjänstslutpunkter. ExpressRoute Global Reach ansluter din lokala miljö till din Azure VMware Solution privata molnet. 

![Bild Azure VMware Solution av angränsande privata moln till Azure och lokalt](./media/adjacency-overview-drawing-final.png)

## <a name="hosts-clusters-and-private-clouds"></a>Värdar, kluster och privata moln

Azure VMware Solution privata moln och kluster är byggda från en hyperkonvergerad Azure-infrastrukturvärd utan metal. De avancerade värdarna har 576 GB RAM-minne och dubbla Intel 18-kärnor, 2,3 GHz-processorer. HE-värdarna har två vSAN-diskgrupper med 15,36 TB (SSD) rå vSAN-kapacitetsnivå och en vSAN-cachenivå på 3,2 TB (NVMe).

Nya privata moln distribueras via Azure Portal eller Azure CLI.

## <a name="networking"></a>Nätverk

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Mer information finns i [Nätverksbegrepp.](concepts-networking.md)

## <a name="access-and-security"></a>Åtkomst och säkerhet

Azure VMware Solution privata moln använder rollbaserad åtkomstkontroll i vSphere för ökad säkerhet. Du kan integrera vSphere SSO LDAP-funktioner med Azure Active Directory. Mer information finns i [Begreppen Åtkomst och Identitet.](concepts-identity.md)  

vSAN-kryptering av vilodata är som standard aktiverad och används för att tillhandahålla säkerhet för vSAN-datalager. Mer information finns i [Lagringsbegrepp.](concepts-storage.md)

## <a name="host-and-software-lifecycle-maintenance"></a>Underhåll av värd- och programvarulivscykel

Regelbundna uppgraderingar av Azure VMware Solution privata moln och VMware-programvara säkerställer att de senaste säkerhets-, stabilitets- och funktionsuppsättningarna körs i dina privata moln. Mer information finns i Uppdateringar [och uppgraderingar av privata moln.](concepts-upgrades.md)

## <a name="monitoring-your-private-cloud"></a>Övervaka ditt privata moln

När Azure VMware Solution har distribuerats till din [prenumeration Azure Monitor genereras](../azure-monitor/overview.md) loggarna automatiskt. 

I ditt privata moln kan du:
- Samla in loggar på var och en av dina virtuella datorer.
- [Ladda ned och installera MMA-agenten](../azure-monitor/agents/log-analytics-agent.md#installation-options) på virtuella Linux- och Windows-datorer.
- Aktivera [Azure Diagnostics-tillägget](../azure-monitor/agents/diagnostics-extension-overview.md).
- [Skapa och kör nya frågor.](../azure-monitor/logs/data-platform-logs.md#log-queries)
- Kör samma frågor som du vanligtvis kör på dina virtuella datorer.

Övervakningsmönstren i Azure VMware Solution liknar virtuella Azure-datorer på IaaS-plattformen. Mer information och hur du gör finns i Övervaka virtuella [Azure-datorer med Azure Monitor](../azure-monitor/vm/monitor-vm-azure.md).

## <a name="customer-communication"></a>Kundkommunikation
[!INCLUDE [customer-communications](includes/customer-communications.md)]

## <a name="next-steps"></a>Nästa steg

Nästa steg är att lära dig viktiga [begrepp för privata moln och kluster.](concepts-private-clouds-clusters.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md


