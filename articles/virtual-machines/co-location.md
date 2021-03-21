---
title: Närhetsplaceringsgrupper
description: Lär dig mer om att använda närhets placerings grupper i Azure.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 1a65a1e4ecd989f3a7c4968c424472c3c6dfe472
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559083"
---
# <a name="proximity-placement-groups"></a>Närhetsplaceringsgrupper

Att placera virtuella datorer i en enda region minskar det fysiska avståndet mellan instanserna. Att placera dem i en enda tillgänglighets zon kommer också att ta dem fysiskt närmare varandra. I takt med att Azure-utrymmet växer kan en enda tillgänglighets zon sträcka sig över flera fysiska data Center, vilket kan leda till en nätverks fördröjning som påverkar ditt program. 

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en närhets placerings grupp.

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


- Låg latens mellan fristående virtuella datorer.
- Låg latens mellan virtuella datorer i en enda tillgänglighets uppsättning eller en skalnings uppsättning för virtuella datorer. 
- Låg latens mellan fristående virtuella datorer, virtuella datorer i flera tillgänglighets uppsättningar eller flera skalnings uppsättningar. Du kan ha flera beräknings resurser i en enda placerings grupp för att sammanföra ett program med flera nivåer. 
- Låg latens mellan flera program nivåer med olika maskin varu typer. Du kan till exempel köra Server delen med M-serien i en tillgänglighets uppsättning och klient delen på en D-seriens instans i en skalnings uppsättning i en enda närhets placerings grupp.


![Bild för närhets placerings grupper](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Använda närhets placerings grupper 

En närhets placerings grupp är en ny resurs typ i Azure. Du måste skapa ett innan du använder det med andra resurser. När den har skapats kan den användas med virtuella datorer, tillgänglighets uppsättningar eller skalnings uppsättningar för virtuella datorer. Du anger en närhets placerings grupp när du skapar beräknings resurser som ger närhets grupp-ID: t. 

Du kan också flytta en befintlig resurs till en närhets placerings grupp. När du flyttar en resurs till en närhets placerings grupp bör du stoppa (frigöra) till gången först eftersom den kommer att omdistribueras till ett annat data Center i regionen för att uppfylla begränsningen för samplacering. 

Om det gäller tillgänglighets uppsättningar och skalnings uppsättningar för virtuella datorer bör du ange placerings gruppen närhet på resurs nivå i stället för de enskilda virtuella datorerna. 

En närhets placerings grupp är en begränsning i en gemensam plats i stället för en fäst funktion. Den fästs i ett Data Center med distributionen av den första resursen för att använda den. När alla resurser som använder närhets placerings gruppen har stoppats (frigjorts) eller tagits bort, är de inte längre fästa. När du använder en närhets placerings grupp med flera VM-serier är det därför viktigt att du anger alla nödvändiga typer som är aktiva i en mall när det är möjligt eller följer en distributions ordning som förbättrar risken för en lyckad distribution. Om distributionen Miss lyckas startar du om distributionen med den VM-storlek som har misslyckats som den första storleken som ska distribueras.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Vad du kan förvänta när du använder grupper för närhets placering 
Närhets placerings grupper erbjuder samplacering i samma data Center. Men eftersom närhets placerings grupper representerar en ytterligare distributions begränsning kan allokeringsfel uppstå. Det finns några användnings fall där du kan se allokeringsfel när du använder närhets placerings grupper:

- När du frågar efter den första virtuella datorn i närheten av placerings gruppen väljs Data Center automatiskt. I vissa fall kan en andra begäran för en annan virtuell dator-SKU Miss lyckas om den inte finns i data centret. I det här fallet returneras ett **OverconstrainedAllocationRequest** -fel. Undvik detta genom att ändra i vilken ordning du distribuerar SKU: er eller låta båda resurserna distribueras med en enda ARM-mall.
-   I händelse av elastiska arbets belastningar, där du lägger till och tar bort virtuella dator instanser, kan en grupp begränsning för närhets placering i distributionen leda till att det inte går att uppfylla begäran som resulterade i **AllocationFailure** -fel. 
- Att stoppa (frigöra) och starta dina virtuella datorer efter behov är ett annat sätt att uppnå elastiskhet. Eftersom kapaciteten inte hålls kvar när du stoppar (frigör) en virtuell dator och startar den igen, kan det resultera i ett **AllocationFailure** -fel.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Placerings grupper för planerat underhåll och närhet

Planerade underhålls händelser, t. ex. maskin varu avställning i ett Azure-datacenter, kan eventuellt påverka justeringen av resurser i närhets placerings grupper. Resurser kan flyttas till ett annat data Center, vilket stör de samplacering och förväntningar som är kopplade till närhets placerings gruppen.

### <a name="check-the-alignment-status"></a>Kontrol lera justerings status

Du kan göra följande för att kontrol lera justerings status för närhets grupper.


- Status för närhets placeringen kan visas med hjälp av portalen, CLI och PowerShell.

    -   När du använder PowerShell kan du hämta samplacerings status med hjälp av Get-AzProximityPlacementGroup cmdlet genom att inkludera den valfria parametern-ColocationStatus.

    -   När du använder CLI kan du hämta samplacerings status med hjälp `az ppg show` av genom att inkludera den valfria parametern--include-superlocation-status.

- För varje närhets placerings grupp tillhandahåller en egenskap för status för en **samplacering** av den aktuella justerings status sammanfattningen för de grupperade resurserna. 

    - **Justerad**: resursen är inom samma latens-kuvert som den närhets placerings gruppen.

    - **Okänd**: minst en av de virtuella dator resurserna har frigjorts. När du har startat dem igen bör statusen gå tillbaka till **justerad**.

    - **Inte justerad**: minst en VM-resurs är inte justerad med närhets placerings gruppen. De särskilda resurser som inte justeras kommer också att anropas separat i avsnittet medlemskap

- För tillgänglighets uppsättningar kan du se information om justering för enskilda virtuella datorer på översikts sidan tillgänglighets uppsättning.

- För skalnings uppsättningar kan information om justering av enskilda instanser visas på fliken **instanser** på **översikts** sidan för skalnings uppsättningen. 


### <a name="re-align-resources"></a>Justera resurser på nytt 

Om det finns en närhets placerings grupp `Not Aligned` kan du stop\deallocate och sedan starta om de berörda resurserna. Om den virtuella datorn finns i en tillgänglighets uppsättning eller en skalnings uppsättning måste alla virtuella datorer i tillgänglighets uppsättningen eller skalnings uppsättningen vara stopped\deallocated först innan de startas om.

Om det uppstår ett allokeringsfel på grund av distributions begränsningar kan du behöva stop\deallocate alla resurser i den påverkade närhets placerings gruppen (inklusive de justerade resurserna) först och sedan starta om dem för att återställa justeringen.

## <a name="best-practices"></a>Bästa praxis 
- Använd närhets placerings grupper tillsammans med accelererat nätverk för lägsta latens. Mer information finns i [skapa en virtuell Linux-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md) eller [skapa en virtuell Windows-dator med accelererat nätverk](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Distribuera alla VM-storlekar i en enda mall. För att undvika landning på maskin vara som inte stöder alla VM-SKU: er och storlekar som du behöver, måste du ta med alla program nivåer i en enda mall så att de kan distribueras på samma gång.
- Om du kör skript för distributionen med PowerShell, CLI eller SDK kan du få ett allokeringsfel `OverconstrainedAllocationRequest` . I det här fallet bör du stoppa/frigöra alla befintliga virtuella datorer och ändra ordningen i distributions skriptet så att den börjar med VM-SKU: n/storlekarna som misslyckades. 
- När du återanvänder en befintlig placerings grupp som de virtuella datorerna togs bort från, väntar du tills borttagningen har slutförts innan du lägger till virtuella datorer i den.
- Om svars tiden är din första prioritet ska du placera virtuella datorer i en närhets placerings grupp och hela lösningen i en tillgänglighets zon. Men om återhämtning är din högsta prioritet sprider du dina instanser över flera tillgänglighets zoner (en enda närhets placerings grupp kan inte omfatta zoner).

## <a name="next-steps"></a>Nästa steg

Distribuera en virtuell dator till en närhets placerings grupp med hjälp av [Azure CLI](./linux/proximity-placement-groups.md) eller [PowerShell](./windows/proximity-placement-groups.md)

Lär dig hur du [testar nätverks fördröjningen](../virtual-network/virtual-network-test-latency.md).

Lär dig hur du [optimerar nätverks data flödet](../virtual-network/virtual-network-optimize-network-bandwidth.md).  

Lär dig hur du [använder närhets placerings grupper med SAP-program](./workloads/sap/sap-proximity-placement-scenarios.md).