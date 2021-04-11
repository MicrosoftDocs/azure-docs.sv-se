---
title: Använd CLI för att distribuera Azure-Virtual Machines
description: Lär dig hur du använder CLI för att distribuera Azure-Virtual Machines för att spara kostnader.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 90ad35757834c14abdffb017ff31b3296074ca24
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802445"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Distribuera Azure-Virtual Machines med Azure CLI

Med hjälp av [Azure-Virtual Machines](../spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azure-Virtual Machines. Därför är Azures Virtual Machines bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för Azure-Virtual Machines är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en virtuell Azure-dator kan anges i kronor (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för den virtuella Azure-datorn eller priset för en virtuell standard dator, vilket någonsin är mindre, så länge som det finns kapacitet och tillgänglig kvot. Mer information om hur du ställer in högsta pris finns i [Azure-Virtual Machines – prissättning](../spot-vms.md#pricing).

Processen för att skapa en virtuell Azure-dator med hjälp av Azure CLI är samma som beskrivs i [snabb starts artikeln](./quick-create-cli.md). Lägg bara till parametern "--priority", ange `--eviction-policy` antingen för att frigöra (detta är standard) eller `Delete` , och ange ett högsta pris eller `-1` . 


## <a name="install-azure-cli"></a>Installera Azure CLI

Om du vill skapa Azure-Virtual Machines måste du köra Azure CLI-version 2.0.74 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

Logga in på Azure med [AZ-inloggning](/cli/azure/reference-index#az-login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Skapa en virtuell Azure-dator

Det här exemplet visar hur du distribuerar en virtuell Linux Azure-dator som inte kommer att avlägsnas utifrån pris. Borttagnings principen är inställd på att frigöra den virtuella datorn, så att den kan startas om vid ett senare tillfälle. Om du vill ta bort den virtuella datorn och den underliggande disken när den virtuella datorn avlägsnas, anger `--eviction-policy` du till `Delete` .

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



När den virtuella datorn har skapats kan du fråga om du vill se det högsta fakturerings priset för alla virtuella datorer i resurs gruppen.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan simulera en avlägsnande av en virtuell Azure-dator med hjälp av REST, PowerShell eller CLI för att testa hur bra ditt program kommer att reagera på en plötslig avlägsning.

I de flesta fall ska du använda REST API [Virtual Machines-simulera avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) för att hjälpa till med automatiserad testning av program. För REST `Response Code: 204` innebär det att den simulerade avtagningen lyckades. Du kan kombinera simulerade avvisningar med den [schemalagda händelse tjänsten](scheduled-events.md)för att automatisera hur appen kommer att svara när den virtuella datorn avlägsnas.

Om du vill se schemalagda händelser i praktiken kan du titta på [Azure fredag – använda azure schemalagda händelser för att förbereda för underhåll av virtuella datorer](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Kort guide

För ett snabb test för att visa hur en simulerad avlägsnande kommer att fungera, ska vi gå igenom den schemalagda händelse tjänsten och se hur den ser ut när du simulerar en avtagning med hjälp av Azure CLI.

Tjänsten schemalagd händelse tjänst är aktive rad för din tjänst första gången du gör en begäran om händelser. 

Fjärranslut till den virtuella datorn och öppna en kommando tolk. 

Från kommando tolken på den virtuella datorn skriver du:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Det här första svaret kan ta upp till 2 minuter. Från och med nu bör de visa utdata nästan omedelbart.

Från en dator som har Azure CLI installerat (t. ex. din lokala dator) simulerar du en avtagning med [AZ VM simulera-avlägsning](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Ersätt resurs gruppens namn och namnet på den virtuella datorn med dina egna. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Svarets utdata kommer att ha `Status: Succeeded` om begäran har gjorts.

Gå snabbt tillbaka till din fjärr anslutning till din virtuella dator och fråga Schemalagda händelser slut punkten igen. Upprepa följande kommando tills du får ett utdata som innehåller mer information:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

När den schemalagda händelse tjänsten får meddelandet om borttagning, får du ett svar som ser ut ungefär så här:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Du kan se att `"EventType":"Preempt"` och resursen är den virtuella dator resursen `"Resources":["myspotvm"]` . 

Du kan också se när den virtuella datorn tas bort genom att markera `"NotBefore"` – den virtuella datorn tas inte bort före den tid som anges, så att är ditt fönster för ditt program att stängas av korrekt.


## <a name="next-steps"></a>Nästa steg

Du kan också skapa en virtuell Azure-dator med hjälp av [Azure PowerShell](../windows/spot-powershell.md), [portalen](../spot-portal.md)eller en [mall](spot-template.md).

Fråga aktuell pris information med hjälp av [Azures API för åter försäljning](/rest/api/cost-management/retail-prices/azure-retail-prices) för information om Azure-platsen för virtuella datorer. `meterName`Och `skuName` kommer båda att innehålla `Spot` .

Om du stöter på ett fel, se [felkoder](../error-codes-spot.md).
