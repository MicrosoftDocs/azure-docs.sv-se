---
title: Arbeta med stora Azure-Virtual Machine Scale Sets
description: Vad du behöver veta om stora skalnings uppsättningar för virtuella Azure-datorer för att kunna använda dem i ditt program.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: ffa2a3a921e988b92ad90831041a6fb4d321bc42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747812"
---
# <a name="working-with-large-virtual-machine-scale-sets"></a>Arbeta med stora skalningsuppsättningar för virtuella datorer
Du kan nu skapa [skalningsuppsättningar för virtuella Azure-datorer](./index.yml) med en kapacitet på upp till 1 000 virtuella datorer. I detta dokument definieras en _stor VM-skalningsuppsättning_ som en skalningsuppsättning som kan skalas för över 100 virtuella datorer. Den här funktionen ställs in med skalningsuppsättningsegenskapen (_singlePlacementGroup=False_). 

Vissa aspekter av stora skalningsuppsättningar, som belastningsutjämning och feldomäner beter sig annorlunda jämfört med en vanlig skalningsuppsättning. I det här dokumentet beskrivs egenskaper för stora skalningsuppsättningar och vad du behöver veta för att kunna använda dem i dina program. 

Ett vanligt sätt att distribuera molninfrastruktur i stor skala är att skapa en uppsättning _skalningsenheter_, till exempel genom att skapa flera skalningsuppsättningar för virtuella datorer över flera virtuella nätverk och lagringskonton. Den här metoden tillhandahåller enklare hantering jämfört med enskilda virtuella datorer, och flera skalningsenheter är användbara för många program, särskilt de som kräver andra stapelbara komponenter som flera virtuella nätverk och slutpunkter. Men om programmet kräver ett stort kluster kan det vara enklare att distribuera en enda skalningsuppsättning med upp till 1 000 virtuella datorer. Exempelscenarier omfattar centraliserade distributioner av stordata eller beräkningsnät som kräver enkel hantering av en stor pool av arbetsnoder. När de kombineras med [anslutna datadiskar](virtual-machine-scale-sets-attached-disks.md) för skalningsuppsättningar för virtuella datorer möjliggör stora skalningsuppsättningar distribution av en skalbar infrastruktur som består av tusentals vCPU:er och petabytes av lagring. Allt med en enda åtgärd.

## <a name="placement-groups"></a>Placeringsgrupper 
Vad som gör en _stor_ skalningsuppsättning speciell är inte antalet virtuella datorer, utan antalet _placeringsgrupper_ som den innehåller. En placeringsgrupp är en konstruktion som liknar en Azure-tillgänglighetsuppsättning, med egna feldomäner och uppgraderingsdomäner. Som standard består en skalningsuppsättning av en enda placeringsgrupp med maximalt 100 virtuella datorer. Om en skalningsuppsättningsegenskap som kallas _singlePlacementGroup_ har angetts som _falskt_ kan skalningsuppsättningen bestå av flera placeringsgrupper och innehålla allt mellan 0-1 000 virtuella datorer. Om värdet är standardvärdet _sant_ består skalningsuppsättningen av en enda placeringsgrupp och innehåller mellan 0-100 virtuella datorer.

## <a name="checklist-for-using-large-scale-sets"></a>Checklista för att använda stora skalningsuppsättningar
Beakta följande krav för att lista ut om programmet effektivt kan använda stora skalningsuppsättningar:

- Om du planerar att distribuera ett stort antal virtuella datorer kan du behöva öka din kvotgräns för Compute-vCPU:er. 
- Skalningsuppsättningar som skapats från Azure Marketplace-avbildningar kan skalas upp till 1 000 virtuella datorer.
- Skalningsuppsättningar som skapas från anpassade avbildningar (VM-avbildningar som du skapar och laddar upp själv) kan för närvarande skala upp till 600 virtuella datorer.
- Stora skalningsuppsättningar kräver Azure Managed Disks. Skalningsuppsättningar som inte har skapats med Managed Disks kräver flera lagringskonton (ett konto kan användas för 20 virtuella dator). Stora skalningsuppsättningar är utformade för att endast fungera med Managed Disks för att minska dina omkostnader för lagringshantering och för att undvika risken att du får problem med prenumerationsbegränsningar för lagringskonton. 
- Storskalig nätverks skalning (SPG = false) stöder inte InfiniBand-nätverk
- Layer-4-lastbalansering med skalningsuppsättningar som består av flera placeringsgrupper kräver [Azure Load Balancers standard-SKU](../load-balancer/load-balancer-overview.md). Load Balancers standard-SKU ger ytterligare fördelar, till exempel möjligheten att utföra lastbalanseringar mellan flera olika skalningsuppsättningar. En standard-SKU kräver också en skalningsuppsättning som har en nätverkssäkerhetsgrupp kopplad till den, annars fungerar inte NAT-poolerna som de ska. Kontrollera att skalningsuppsättningen är konfigurerad för att använda standardinställningen att bara använda en enda placeringsgrupp om du behöver använda Azure Load Balancers grundläggande SKU.
- Layer-7-belastningsutjämning med Azure Application Gateway stöds för alla skalningsuppsättningar.
- En skalningsuppsättning definieras med ett enda undernät – kontrollera att ditt undernät har ett adressutrymme som är tillräckligt stort för alla de virtuella datorerna du behöver. Som standard överetablerar skalningsuppsättningar (skapar extra virtuella datorer vid tidpunkten för distribution eller vid utskalning, som du inte debiteras för) för att förbättra distributionstillförlitlighet och prestanda. Tillåt ett adressutrymme 20% större än antalet virtuella datorer som du planerar att skala till.
- Feldomäner och uppgraderingsdomäner är endast konsekventa i en placeringsgrupp. Den här arkitekturen ändrar inte den övergripande tillgängligheten för en skalningsuppsättning eftersom virtuella datorer är jämnt distribuerade över distinkt fysisk maskinvara. Men det innebär att om du behöver garantera att två virtuella datorer finns på olika maskinvara så måste du se till att de finns i olika feldomäner i samma placeringsgrupp. Se de här alternativen för länk [tillgänglighet](../virtual-machines/availability.md). 
- Feldomän och placeringsgrupp-ID som visas i _instansvyn_ för en virtuell dator i en skalningsuppsättning. Du kan se instansvyn för en virtuell dator i en skalningsuppsättning i [Resursutforskaren i Azure](https://resources.azure.com/).

## <a name="creating-a-large-scale-set"></a>Skapa en stor skalningsuppsättning
När du skapar en skalningsuppsättning i Azure-portalen anger du värde för *antalet instanser* till upp till 1 000. Om det är mer än 100 instanser anges *Enable scaling beyond 100 instances* (Aktivera skalning över 100 instanser) till *Ja* så att det kan skalas till flera placeringsgrupper. 

![Den här bilden visar bladet instanser i Azure-portalen. Alternativen för att välja antalet instanser och instans storleken är tillgängliga.](./media/virtual-machine-scale-sets-placement-groups/portal-large-scale.png)

Du kan skapa en stor skalningsuppsättning för virtuella datorer med [Azure CLI-kommandot](https://github.com/Azure/azure-cli) _az vmss create_. Det här kommandot anger intelligenta standardvärden som storleken för undernät baserat på argumentet för _antal instanser_:

```azurecli
az group create -l southcentralus -n biginfra
az vmss create -g biginfra -n bigvmss --image ubuntults --instance-count 1000
```

Kommandot _vmss create_ ställer in vissa konfigurationsvärden som standard om du inte själv anger dem. För att se de tillgängliga alternativen som du kan åsidosätta kan du försöka följande:

```azurecli
az vmss create --help
```

Kontrollera att mallen skapar en skalningsuppsättning som baseras på Azure Managed Disks om du skapar en stor skalningsuppsättning genom att skapa en Azure Resource Manager-mall. Du kan ställa in egenskapen _singlePlacementGroup_ som _falsk_ i avsnittet _egenskaper_ i resursen _Microsoft.Compute/virtualMachineScaleSets_. Följande JSON-fragment visas i början av en mall för skalningsuppsättningar, inklusive kapaciteten på 1 000 virtuella datorer och inställningen _"singlePlacementGroup" : false_:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "location": "australiaeast",
  "name": "bigvmss",
  "sku": {
    "name": "Standard_DS1_v2",
    "tier": "Standard",
    "capacity": 1000
  },
  "properties": {
    "singlePlacementGroup": false,
    "upgradePolicy": {
      "mode": "Automatic"
    }
```

Ett fullständigt exempel på en mall för stor skalnings uppsättning finns i [https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json](https://github.com/gbowerman/azure-myriad/blob/main/bigtest/bigbottle.json) .

## <a name="converting-an-existing-scale-set-to-span-multiple-placement-groups"></a>Konvertera en befintlig skalningsuppsättning som sträcker sig över flera placeringsgrupper
Om du vill göra det möjligt för en befintlig skalningsuppsättning för virtuella datorer att skalas till mer än 100 virtuella datorer så måste du ändra egenskapen _singplePlacementGroup_ till _falskt_ i skalningsuppsättningsmodellen. Du kan ändra den här egenskapen med [Resursutforskaren i Azure](https://resources.azure.com/). Hitta en befintlig skalningsuppsättning, välj _Redigera_ och ändra egenskapen _singlePlacementGroup_. Om du inte ser den här egenskapen kanske du tittar på en skalningsuppsättning med en äldre version av Microsoft.Compute-API.

> [!NOTE]
> Du kan ändra så att en skalningsuppsättning går från att endast stödja en enda placeringsgrupp (standardinställningen) till att stödja flera placeringsgrupper, men du kan inte konvertera åt det andra hållet. Se därför till att du förstår egenskaperna för stora skalningsuppsättningar innan du konverterar.
