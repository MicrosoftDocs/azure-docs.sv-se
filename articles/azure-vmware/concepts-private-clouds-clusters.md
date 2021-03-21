---
title: Koncept – privata moln och kluster
description: Lär dig mer om de viktigaste funktionerna i Azure VMware-lösningen program varu definierade Data Center och vSphere-kluster.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: d1837ae7cf01fcb9642e0cafe4e0430e403b9899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462531"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware-lösning, privata moln och kluster koncept

Azure VMware-lösningen levererar VMware-baserade privata moln i Azure. Privata moln innehåller kluster som skapats med dedikerade, Bare-Metal Azure-värdar. De distribueras och hanteras via Azure Portal, CLI eller PowerShell.  Kluster som tillhandahålls i privata moln är VMware vSphere, vCenter, virtuellt San och NSX-programvara. Azure VMware-lösningar privata moln maskin-och program varu distributioner är helt integrerade och automatiserade i Azure.

Det finns en logisk relation mellan Azure-prenumerationer, privata moln i Azure VMware-lösningar, virtuellt San-kluster och-värdar. Diagrammet visar en enskild Azure-prenumeration med två privata moln som representerar utvecklings-och produktions miljön.  I vart och ett av dessa privata moln finns två kluster. 

I den här artikeln beskrivs alla dessa begrepp.

![Bild av två privata moln i en kund prenumeration](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>Privata moln

Privata moln innehåller virtuellt San-kluster som skapats med dedikerade, Bare-Metal Azure-värdar. Varje privat moln kan ha flera kluster som hanteras av samma vCenter-Server och NSX-T-hanterare. Du kan distribuera och hantera privata moln i portalen, CLI eller PowerShell. 

Precis som med andra resurser installeras och hanteras privata moln i en Azure-prenumeration. Antalet privata moln i en prenumeration är skalbart. Inlednings vis finns det en gräns för ett privat moln per prenumeration.

## <a name="clusters"></a>Kluster
Det finns ett virtuellt San-kluster som standard för varje privat moln som skapas. Du kan lägga till, ta bort och skala kluster med hjälp av Azure Portal eller via API: et.  Alla kluster har en standard storlek på tre värdar och kan skala upp till 16 värdar. Du kan ha upp till fyra kluster per privat moln.

Utvärderings kluster är tillgängliga för utvärdering och begränsade till tre värdar. Det finns ett enda utvärderings kluster per privat moln. Du kan skala ett utvärderings kluster med en enda värd under utvärderings perioden.

Du kan använda vSphere och NSX-T Manager för att hantera de flesta andra aspekter av kluster konfigurationen eller åtgärden. All lokal lagring för varje värd i ett kluster styrs av virtuellt San.

## <a name="hosts"></a>Värdar

Azure VMware-lösnings kluster baseras på en klustrad, Bare Metal-infrastruktur. I följande tabell visas processorns RAM-, processor-och disk kapacitet.

| Värdtyp              |             Processor             |   RAM (GB)   |  Virtuellt San NVMe cache-nivå (TB, RAW)  |  kapacitets nivå för virtuellt San SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  dubbla Intel 18 kärnor 2,3 GHz  |     576      |                3.2               |                15,20               |

Värdar som används för att bygga eller skala kluster kommer från en isolerad pool med värdar. De här värdarna har klarat maskin varu test och har tagit bort alla data på ett säkert sätt. 

## <a name="vmware-software-versions"></a>Versioner av VMware-programvara

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="host-maintenance-and-lifecycle-management"></a>Underhåll av värd och livs cykel hantering

Underhåll av värd och livs cykel hantering påverkar inte det privata moln klustrets kapacitet eller prestanda.  Exempel på automatiserat värd underhåll är uppgraderingar av inbyggd program vara och reparation eller ersättning av maskin vara.

Microsoft ansvarar för livs cykel hanteringen av NSX-T-apparater, till exempel NSX-T-tjänsthanteraren och NSX-T-Edge. Microsoft ansvarar för att starta Nätverks konfigurationen, till exempel att skapa nivå 0-gatewayen och aktivera North-South routning. Du är ansvarig för NSX-T SDN-konfigurationen. Till exempel nätverks segment, distribuerade brand Väggs regler, nivå 1-gatewayer och belastningsutjämnare.

## <a name="backup-and-restoration"></a>Säkerhets kopiering och återställning

Schemat för privat moln vCenter och NSX-T-konfigurationer finns i ett schema för säkerhets kopiering per timme.  Säkerhets kopior sparas i tre dagar. Om du behöver återställa från en säkerhets kopia öppnar du en [supportbegäran](https://rc.portal.azure.com/#create/Microsoft.Support) i Azure Portal för att begära återställning.

## <a name="next-steps"></a>Nästa steg

Nu när du har täckt Azure VMware-lösningens privata moln koncept, kanske du vill lära dig mer om: 

- [Nätverks-och samanslutnings koncept i Azure VMware-lösningen](concepts-networking.md).
- [Lagrings koncept för Azure VMware-lösningar](concepts-storage.md).
- [Så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

