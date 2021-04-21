---
title: Använda CLI för att distribuera Azure Spot Virtual Machines
description: Lär dig hur du använder CLI för att distribuera Azure Spot Virtual Machines för att spara kostnader.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789617"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Distribuera Azure Spot Virtual Machines med hjälp av Azure CLI

Med [Azure Spot Virtual Machines](../spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet med betydande kostnadsbesparingar. När Azure behöver kapaciteten tillbaka avlägsnar Azure-infrastrukturen Azure Spot Virtual Machines. Därför är Azure Spot Virtual Machines bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Prissättningen för Azure Spot Virtual Machines varierar beroende på region och SKU. Mer information finns i Prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

Du har möjlighet att ange ett högsta pris som du är beredd att betala per timme för den virtuella datorn. Maxpriset för en virtuell Azure Spot-dator kan anges i AMERIKANSKA dollar (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett maxpris på 0,98765 USD per timme. Om du anger det högsta priset `-1` till avlägsnas inte den virtuella datorn baserat på priset. Priset för den virtuella datorn är det aktuella priset för virtuella Azure-datorer för VM med spot-kapacitet eller priset för en standard-VM, som någonsin är mindre, så länge det finns tillgänglig kapacitet och kvot. Mer information om hur du anger maxpriset finns i [Azure Spot Virtual Machines – Priser.](../spot-vms.md#pricing)

Processen för att skapa en virtuell Azure Spot-dator med Hjälp av Azure CLI är densamma som beskrivs i [snabbstartsartikeln](./quick-create-cli.md). Lägg bara till parametern "--priority Spot" (prioritetspunkt), ange till antingen `--eviction-policy` Deallocate (standardinställningen) eller , och `Delete` ange ett maxpris eller `-1` . 


## <a name="install-azure-cli"></a>Installera Azure CLI

Om du vill Virtual Machines Azure Spot måste du köra Azure CLI version 2.0.74 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

Logga in på Azure med [az login](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Skapa en virtuell Azure-dator för o vm med odator

Det här exemplet visar hur du distribuerar en virtuell Linux Azure Spot-dator som inte avlägsnas baserat på priset. Avlägsningsprincipen är inställd på att frisöka den virtuella datorn så att den kan startas om vid ett senare tillfälle. Om du vill ta bort den virtuella datorn och den underliggande disken när den virtuella datorn avlägsnas anger du `--eviction-policy` till `Delete` .

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



När den virtuella datorn har skapats kan du fråga för att se det högsta faktureringspriset för alla virtuella datorer i resursgruppen.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulera en avlägsning

Du kan simulera en avlägsning av en virtuell Azure Spot-dator med hjälp av REST, PowerShell eller CLI för att testa hur väl ditt program svarar på en plötslig avlägsning.

I de flesta fall bör du använda REST API [Virtual Machines Simulera avlägsning](/rest/api/compute/virtualmachines/simulateeviction) för att hjälpa till med automatiserad testning av program. För REST innebär det `Response Code: 204` att den simulerade avlägsning lyckades. Du kan kombinera simulerade avlägsningar med tjänsten Schemalagd händelse [för](scheduled-events.md)att automatisera hur din app svarar när den virtuella datorn avlägsnas.

Om du vill se schemalagda händelser i praktiken kan du [titta på Azure Friday – Använda Azure Schemalagda händelser för att förbereda för underhåll av virtuella datorer.](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)


### <a name="quick-test"></a>Kort guide

För ett snabbtest som visar hur en simulerad avlägsning fungerar ska vi gå igenom frågor till den schemalagda händelsetjänsten för att se hur det ser ut när du simulerar en avlägsning med hjälp av Azure CLI.

Tjänsten Schemalagd händelse aktiveras för din tjänst första gången du gör en begäran om händelser. 

Fjärranslut till den virtuella datorn och öppna sedan en kommandotolk. 

I kommandotolken på den virtuella datorn skriver du:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Det första svaret kan ta upp till 2 minuter. Från och med nu bör de visa utdata nästan omedelbart.

Från en dator där Azure CLI är installerat (t.ex. din lokala dator) simulerar du en avlägsning [med az vm simulate-eviction](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Ersätt resursgruppens namn och namnet på den virtuella datorn med ditt eget. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Svarsutdata har `Status: Succeeded` om begäran har gjorts.

Gå snabbt tillbaka till fjärranslutningen till den virtuella spotdatorn och fråga Schemalagda händelser slutpunkten igen. Upprepa följande kommando tills du får utdata som innehåller mer information:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

När tjänsten Schemalagd händelse får avlägsning får du ett svar som ser ut ungefär så här:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Du kan se att `"EventType":"Preempt"` , och resursen är VM-resursen `"Resources":["myspotvm"]` . 

Du kan också se när den virtuella datorn avlägsnas genom att kontrollera – den virtuella datorn tas inte bort före den angivna tiden, så det är ditt fönster för att programmet ska stängas på ett smidigt `"NotBefore"` sätt.


## <a name="next-steps"></a>Nästa steg

Du kan också skapa en virtuell Azure [Spot-dator med Azure PowerShell,](../windows/spot-powershell.md) [portal](../spot-portal.md)eller en [mall](spot-template.md).

Fråga aktuell prisinformation med hjälp av [AZURE-API:et för detaljhandelspriser](/rest/api/cost-management/retail-prices/azure-retail-prices) för information om virtuell Azure Spot-dator. och `meterName` `skuName` innehåller båda `Spot` .

Om du stöter på ett fel kan du se [Felkoder.](../error-codes-spot.md)
