---
title: Använda virtuella Azure-datorer
description: Lär dig hur du använder virtuella Azure-datorer för att spara pengar.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 1e3934a8ff91d764a5148b3d490b44f30983a284
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202138"
---
# <a name="use-spot-vms-in-azure"></a>Använda virtuella datorer i Azure

Med hjälp av virtuella datorer kan du dra nytta av vår outnyttjade kapacitet till betydande besparingar. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella datorer. De virtuella datorerna är därför fantastiska för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid och dag. När du distribuerar virtuella datorer, allokerar Azure de virtuella datorerna om det finns tillgänglig kapacitet, men det finns inget service avtal för dessa virtuella datorer. En VM-VM ger inga garantier för hög tillgänglighet. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella platser med 30 sekunders varsel. 


## <a name="eviction-policy"></a>Avlägsnandeprincip

Virtuella datorer kan avlägsnas baserat på kapacitet eller det högsta pris som du har angett. När du skapar en virtuell dator för virtuella datorer kan du ange att principen ska *avallokeras* eller *tas bort*. 

Principen *frigör* flyttar den virtuella datorn till statusen stoppad-frigjord, så att du kan distribuera den igen senare. Det finns dock ingen garanti för att allokeringen ska lyckas. De friallokerade virtuella datorerna räknas av mot kvoten och du debiteras lagrings kostnaderna för de underliggande diskarna. 

Om du vill att den virtuella datorn ska tas bort när den tas bort kan du ange vilken borttagnings princip som ska *tas bort*. De avlägsnade virtuella datorerna tas bort tillsammans med deras underliggande diskar, så du kan inte fortsätta att debiteras för lagringen. 

Du kan välja att ta emot meddelanden i virtuella datorer via [Azure schemalagda händelser](./linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer avlägsnas och du har 30 sekunder på dig att slutföra jobben och utföra avstängnings uppgifter innan avlägsnandet. 


| Alternativ | Resultat |
|--------|---------|
| Högsta pris är inställt på >= det aktuella priset. | Den virtuella datorn distribueras om kapacitet och kvot är tillgängliga. |
| Högsta pris är inställt på < det aktuella priset. | Den virtuella datorn har inte distribuerats. Du får ett fel meddelande om att max priset måste vara >= aktuellt pris. |
| Starta om en virtuell dator för att stoppa/frigöra om max priset är >= aktuellt pris | Om det finns kapacitet och kvot distribueras den virtuella datorn. |
| Starta om en virtuell dator för att stoppa/frigöra om högsta pris är < det aktuella priset | Du får ett fel meddelande om att max priset måste vara >= aktuellt pris. | 
| Priset för den virtuella datorn har blivit nu > det högsta priset. | Den virtuella datorn har avlägsnats. Du får ett 30 s-meddelande innan du börjar med den faktiska borttagningen. | 
| Efter avlägsnandet går priset för den virtuella datorn tillbaka till < det högsta priset. | Den virtuella datorn kommer inte att startas om automatiskt. Du kan starta om den virtuella datorn själv och debiteras enligt det aktuella priset. |
| Om max priset är inställt på `-1` | Den virtuella datorn kommer inte att avlägsnas av prissättnings skäl. Det högsta priset är det aktuella priset, upp till priset för virtuella datorer med standard typ. Du debiteras aldrig enligt standard priset.| 
| Ändra Max priset | Du måste frigöra den virtuella datorn för att ändra det högsta priset. Frigör den virtuella datorn, ange ett nytt max pris och uppdatera den virtuella datorn. |


## <a name="limitations"></a>Begränsningar

Följande VM-storlekar stöds inte för virtuella datorer på platsen:
 - B-serien
 - Kampanj versioner av valfri storlek (t. ex. dv2, NV, NC, H kampanj storlek)

Virtuella datorer kan distribueras till vilken region som helst, förutom Microsoft Azure Kina 21Vianet.

<a name="channel"></a>

Följande [typer av erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) stöds för närvarande:

-   Enterprise-avtal
-   Betala per användning
-   Sponsrat
- För Cloud Service Provider (CSP) kontaktar du din partner


## <a name="pricing"></a>Prissättning

Priser för virtuella datorer i virtuella datorer är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Du kan också fråga pris information med [Azures API för åter försäljning](/rest/api/cost-management/retail-prices/azure-retail-prices) för att fråga efter information om prissättning. `meterName`Och `skuName` kommer båda att innehålla `Spot` .

Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.

## <a name="pricing-and-eviction-history"></a>Pris-och borttagnings historik

Du kan se historiska priser och avlägsna priser per storlek i en region i portalen. Välj **Visa pris historik och jämför priser i närliggande regioner** för att se en tabell eller ett diagram över priser för en speciell storlek.  Pris-och borttagnings priserna i följande avbildningar är bara exempel. 

**Diagram**:

:::image type="content" source="./media/spot-chart.png" alt-text="Skärm bild av alternativen för region med skillnaden i pris-och borttagnings taxa som ett diagram.":::

**Tabell**:

:::image type="content" source="./media/spot-table.png" alt-text="Skärm bild av alternativen för region med skillnaden i pris-och borttagnings satser som en tabell.":::



##  <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** När den har skapats är en virtuell dator på samma sätt som vanlig standard-VM?

**A:** Ja, förutom att det inte finns något service avtal för virtuella datorer på plats och de kan avlägsnas när som helst.


**F:** Vad ska jag göra när du har avlägsnat, men behöver fortfarande kapacitet?

**A:** Vi rekommenderar att du använder virtuella standard datorer i stället för virtuella datorer för virtuella datorer om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för virtuella datorer med virtuella datorer?

**A:** Virtuella datorer med virtuella datorer kommer att ha en separat kvotmall. Kvoten för kvoten kommer att delas mellan virtuella datorer och skalnings uppsättnings instanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kan jag begära ytterligare kvot för platsen?

**A:** Ja, du kommer att kunna skicka begäran om att öka din kvot för virtuella datorer med hjälp av [standard kvot processen](../azure-portal/supportability/per-vm-quota-requests.md).


**F:** Var kan jag skicka frågor?

**A:** Du kan skicka och tagga din fråga med `azure-spot` på [Q&A](/answers/topics/azure-spot.html). 


**F:** Hur kan jag ändra det högsta priset för en virtuell dator?

**A:** Innan du kan ändra det högsta priset måste du frigöra den virtuella datorn. Sedan kan du ändra det högsta priset i portalen, från **konfigurations** avsnittet för den virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Använd [CLI](./linux/spot-cli.md), [Portal](spot-portal.md), [arm-mallen](./linux/spot-template.md)eller [PowerShell](./windows/spot-powershell.md) för att distribuera virtuella datorer.

Du kan också distribuera en [skalnings uppsättning med virtuella dator instanser](../virtual-machine-scale-sets/use-spot.md).

Om du stöter på ett fel, se [felkoder](./error-codes-spot.md).
