---
title: Skapa en skalnings uppsättning som använder virtuella Azure-datorer
description: Lär dig hur du skapar skalnings uppsättningar för virtuella Azure-datorer som använder virtuella datorer för att spara pengar.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: cynthn
ms.custom: jagaveer, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 03bf5e0ef7e6268e68139b6d73685f67d88f6231
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385939"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Virtuella Azure-datorer för skalnings uppsättningar för virtuella datorer 

Med hjälp av Azure-punkt på skalnings uppsättningar kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten återkommer Azure-infrastrukturen att ta bort plats instanser. Därför är det bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid och dag. När du distribuerar plats instanser på skalnings uppsättningar, kommer Azure att allokera instansen endast om det finns tillgänglig kapacitet, men det finns inget service avtal för dessa instanser. En dekor Skale uppsättning distribueras i en enskild feldomän och ger inga garantier för hög tillgänglighet.


## <a name="pricing"></a>Prissättning

Priser för punkt instanser är varierande, baserat på region och SKU. Mer information finns i avsnittet om priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer instansen inte att avlägsnas baserat på priset. Priset för instansen är det aktuella priset för dekor eller priset för en standard instans, vilket någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.


## <a name="limitations"></a>Begränsningar

Följande storlekar stöds inte för Azure-platsen:
 - B-serien
 - Kampanj versioner av valfri storlek (t. ex. dv2, NV, NC, H kampanj storlek)

Azure-platsen kan distribueras till vilken region som helst, förutom Microsoft Azure Kina.

<a name="channel"></a>

Följande [typer av erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) stöds för närvarande:

-   Enterprise-avtal
-   Erbjudande för betala per användning-kod 003P
-   Sponsrat
- För Cloud Service Provider (CSP) kontaktar du din partner

## <a name="eviction-policy"></a>Avlägsnandeprincip

När du skapar dekor skalnings uppsättningar kan du ställa in principen för att *frigöra* (standard) eller *ta bort*. 

Principen *frigör* flyttar de inaktuella instanserna till läget Stoppad-frigjord, så att du kan distribuera om avlägsnade instanser. Det finns dock ingen garanti för att allokeringen ska lyckas. De friallokerade virtuella datorerna räknas över till kvoten för din skalnings uppsättning och du debiteras för de underliggande diskarna. 

Om du vill att dina instanser i din dekor Skale uppsättning ska tas bort när de avlägsnas, kan du ange vilken borttagnings princip som ska *tas bort*. När du har angett en princip för borttagning kan du skapa nya virtuella datorer genom att öka antalet skalnings uppsättnings instanser. De avlägsnade virtuella datorerna tas bort tillsammans med deras underliggande diskar och därför debiteras du inte för lagringen. Du kan också använda funktionen för automatisk skalning i skalnings uppsättningar för att automatiskt försöka och kompensera för avlägsnade virtuella datorer, men det finns ingen garanti för att allokeringen kommer att lyckas. Vi rekommenderar att du bara använder funktionen för automatisk skalning på dekor skalnings uppsättningar när du anger att borttagnings principen ska tas bort för att undvika kostnaden för diskarna och vid kvot gränser. 

Användare kan välja att ta emot meddelanden i den virtuella datorn via [Azure schemalagda händelser](../virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer avlägsnas och du har 30 sekunder på dig att slutföra jobben och utföra avstängnings uppgifter innan avlägsnandet. 

## <a name="placement-groups"></a>Placerings grupper
Placerings gruppen är en konstruktion som liknar en Azures tillgänglighets uppsättning, med egna fel domäner och uppgraderings domäner. Som standard består en skalningsuppsättning av en enda placeringsgrupp med maximalt 100 virtuella datorer. Om den skalnings uppsättnings egenskap som kallas `singlePlacementGroup` är *false* kan skalnings uppsättningen bestå av flera placerings grupper och ha ett intervall på 0 till 1 000 virtuella datorer. 

> [!IMPORTANT]
> Om du inte använder InfiniBand med HPC rekommenderar vi starkt att du ställer in egenskapen skalnings uppsättning `singlePlacementGroup` på *falskt* för att aktivera flera placerings grupper för bättre skalning i regionen eller zonen. 

## <a name="deploying-spot-vms-in-scale-sets"></a>Distribuera virtuella datorer i skalnings uppsättningar

Om du vill distribuera virtuella datorer på skalnings uppsättningar kan du ställa in den nya *prioritets* flaggan på *plats*. Alla virtuella datorer i din skalnings uppsättning anges till dekor. Använd någon av följande metoder för att skapa en skalnings uppsättning med virtuella datorer:
- [Azure-portalen](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-mallar](#resource-manager-templates)

## <a name="portal"></a>Portalen

Processen för att skapa en skalnings uppsättning som använder virtuella datorer är samma som i [artikeln komma igång](quick-create-portal.md). När du distribuerar en skalnings uppsättning kan du välja att ange flaggan för punkt och avinstallations principen: ![ skapa en skalnings uppsättning med virtuella datorer](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Processen att skapa en skalnings uppsättning med virtuella datorer är samma som beskrivs i [artikeln komma igång](quick-create-cli.md). Lägg bara till "--prioritets plats" och Lägg till `--max-price` . I det här exemplet använder vi `-1` för `--max-price` så att instansen inte avlägsnas baserat på priset.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --single-placement-group false \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

Processen att skapa en skalnings uppsättning med virtuella datorer är samma som beskrivs i [artikeln komma igång](quick-create-powershell.md).
Lägg bara till "-prioritets punkt" och ange en `-max-price` till [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Den process som används för att skapa en skalnings uppsättning som använder virtuella datorer är samma som beskrivs i komma igång-artikeln för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). 

För distributioner av dekor mallar använder `"apiVersion": "2019-03-01"` eller senare. 

Lägg till `priority` - `evictionPolicy` och- `billingProfile` egenskaperna i `"virtualMachineProfile":` avsnittet och `"singlePlacementGroup": false,` egenskapen till `"Microsoft.Compute/virtualMachineScaleSets"` avsnittet i mallen:

```json

{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  },
  "properties": {
    "singlePlacementGroup": false,
    }

        "virtualMachineProfile": {
              "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            },
```

Ändra parametern till om du vill ta bort instansen när den har avlägsnats `evictionPolicy` `Delete` .

## <a name="faq"></a>Vanliga frågor

**F:** När det har skapats är en punkt instans samma som standard instans?

**A:** Ja, förutom att det inte finns något service avtal för virtuella datorer på plats och de kan avlägsnas när som helst.


**F:** Vad ska jag göra när du har avlägsnat, men behöver fortfarande kapacitet?

**A:** Vi rekommenderar att du använder virtuella standard datorer i stället för virtuella datorer för virtuella datorer om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för dekor?

**A:** Punkt instanser och standard instanser kommer att ha separata kvotmallar. Kvoten för kvoten kommer att delas mellan virtuella datorer och skalnings uppsättnings instanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kan jag begära ytterligare kvot för platsen?

**A:** Ja, du kommer att kunna skicka begäran om att öka din kvot för virtuella datorer med hjälp av [standard kvot processen](../azure-portal/supportability/per-vm-quota-requests.md).


**F:** Kan jag konvertera befintliga skalnings uppsättningar till dekor skalnings uppsättningar?

**A:** Nej, det går bara att ställa in `Spot` flaggan vid skapande tillfället.


**F:** `low` Måste jag börja använda i stället om jag använde för låg prioritets skalnings uppsättningar `Spot` ?

**A:** För tillfället kommer både `low` och `Spot` att fungera, men du bör börja över gången till med `Spot` .


**F:** Kan jag skapa en skalnings uppsättning med både vanliga virtuella datorer och virtuella datorer?

**A:** Nej, en skalnings uppsättning kan inte stödja fler än en prioritets typ.


**F:**  Kan jag använda autoskalning med dekor skalnings uppsättningar?

**A:** Ja, du kan ange regler för automatisk skalning på din plats skalnings uppsättning. Om dina virtuella datorer avlägsnas kan autoskalning försöka skapa nya virtuella dator datorer. Kom ihåg att du inte garanterar den här kapaciteten. 


**F:**  Kan autoskalning användas med både borttagnings principer (frigör och ta bort)?

**A:** Ja, men vi rekommenderar att du anger en princip för att ta bort när du använder autoskalning. Detta beror på att friallokerade instanser räknas mot ditt kapacitets antal i skalnings uppsättningen. När du använder autoskalning når du förmodligen antalet mål instanser snabbt på grund av de frikopplade, avlägsnade instanserna. Dessutom kan skalnings åtgärderna påverkas av punkt avvisningar. Till exempel kan virtuella datorers skalnings uppsättnings instanser falla under angivet minsta antal på grund av flera borttagningar av punkter under skalnings åtgärder. 


**F:** Var kan jag skicka frågor?

**A:** Du kan skicka och tagga din fråga med `azure-spot` på [Q&A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Nästa steg

Se [prissättnings sidan för den virtuella datorns skalnings uppsättning](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) för pris information.
