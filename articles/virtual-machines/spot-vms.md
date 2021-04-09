---
title: Använd Virtual Machines för Azure-plats
description: Lär dig hur du använder Azures Virtual Machines för att spara pengar.
author: JagVeerappan
ms.author: jagaveer
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/05/2020
ms.reviewer: cynthn
ms.openlocfilehash: fb53fc37227e040ed7bd7fc8e47de9aed538bc2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721400"
---
# <a name="use-azure-spot-virtual-machines"></a>Använd Virtual Machines för Azure-plats 

Med hjälp av Azure-Virtual Machines kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azure-Virtual Machines. Därför är Azures Virtual Machines bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid och dag. När du distribuerar Azure-Virtual Machines allokerar Azure de virtuella datorerna om det finns tillgänglig kapacitet, men det finns inget service avtal för dessa virtuella datorer. En virtuell Azure-dator ger inga garantier för hög tillgänglighet. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azures Virtual Machines med 30 sekunders varsel. 


## <a name="eviction-policy"></a>Avlägsnandeprincip

Virtuella datorer kan avlägsnas baserat på kapacitet eller det högsta pris som du har angett. När du skapar en virtuell Azure-dator kan du ange att borttagnings principen ska *frigöra* (standard) eller *ta bort*. 

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

Följande VM-storlekar stöds inte för Azure-Virtual Machines:
 - B-serien
 - Kampanj versioner av valfri storlek (t. ex. dv2, NV, NC, H kampanj storlek)

Azure-Virtual Machines kan distribueras till vilken region som helst, förutom Microsoft Azure Kinas 21Vianet.

<a name="channel"></a>

Följande [typer av erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) stöds för närvarande:

-   Enterprise-avtal 
-   Erbjudande kod för betala per användning (003P)
-   Sponsrat (0036P och 0136P)
- För Cloud Service Provider (CSP) kontaktar du din partner


## <a name="pricing"></a>Priser

Priser för Azure-Virtual Machines är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Du kan också fråga pris information med [Azures API för åter försäljning](/rest/api/cost-management/retail-prices/azure-retail-prices) för att fråga efter information om prissättning. `meterName`Och `skuName` kommer båda att innehålla `Spot` .

Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.

## <a name="pricing-and-eviction-history"></a>Pris-och borttagnings historik

Du kan se historiska priser och avlägsna priser per storlek i en region i portalen. Välj **Visa pris historik och jämför priser i närliggande regioner** för att se en tabell eller ett diagram över priser för en speciell storlek.  Pris-och borttagnings priserna i följande avbildningar är bara exempel. 

**Diagram**:

:::image type="content" source="./media/spot-chart.png" alt-text="Skärm bild av alternativen för region med skillnaden i pris-och borttagnings taxa som ett diagram.":::

**Tabell**:

:::image type="content" source="./media/spot-table.png" alt-text="Skärm bild av alternativen för region med skillnaden i pris-och borttagnings satser som en tabell.":::



##  <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F:** När den har skapats är en virtuell Azure-dator samma som vanlig standard-VM?

**A:** Ja, förutom att det inte finns något service avtal för Azure-Virtual Machines och de kan avlägsnas när som helst.


**F:** Vad ska jag göra när du har avlägsnat, men behöver fortfarande kapacitet?

**A:** Vi rekommenderar att du använder standard-VM: ar i stället för Azure-Virtual Machines om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för Azure-Virtual Machines?

**A:** Virtual Machines för Azure-plats har en separat kvotmall. Kvoten för kvoten kommer att delas mellan virtuella datorer och skalnings uppsättnings instanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kan jag begära ytterligare kvot för Azure-Virtual Machines?

**A:** Ja, du kommer att kunna skicka begäran om att öka kvoten för Azure-Virtual Machines genom processen för [standard kvot förfrågan](../azure-portal/supportability/per-vm-quota-requests.md).


**F:** Var kan jag skicka frågor?

**A:** Du kan skicka och tagga din fråga med `azure-spot` på [Q&A](/answers/topics/azure-spot.html). 


**F:** Hur kan jag ändra det högsta priset för en virtuell dator?

**A:** Innan du kan ändra det högsta priset måste du frigöra den virtuella datorn. Sedan kan du ändra det högsta priset i portalen, från **konfigurations** avsnittet för den virtuella datorn. 

## <a name="next-steps"></a>Nästa steg
Använd [CLI](./linux/spot-cli.md), [Portal](spot-portal.md), [arm-mall](./linux/spot-template.md)eller [PowerShell](./windows/spot-powershell.md) för att distribuera Azure-Virtual Machines.

Du kan även distribuera en [skalnings uppsättning med Azure-instanser av virtuella datorer](../virtual-machine-scale-sets/use-spot.md).

Om du stöter på ett fel, se [felkoder](./error-codes-spot.md).
