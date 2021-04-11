---
title: Skapa en skalnings uppsättning som använder Azure-Virtual Machines
description: Lär dig hur du skapar skalnings uppsättningar för virtuella Azure-datorer som använder Azure-Virtual Machines för att spara pengar.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ec73d1363fb18d1d6c46589fe69879a8f6df1dab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722573"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure-Virtual Machines för skalnings uppsättningar för virtuella datorer 

Genom att använda Azure-Virtual Machines i skalnings uppsättningar kan du dra nytta av vår outnyttjade kapacitet till betydande besparingar. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azures virtuella dator instanser. Därför är Azures virtuella dator instanser bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid och dag. När du distribuerar instanser av virtuella Azure-datorer i skalnings uppsättningar, kommer Azure att allokera instansen endast om det finns tillgänglig kapacitet, men det finns inget service avtal för dessa instanser. En skalnings uppsättning för en virtuell Azure-dator distribueras i en enskild feldomän och ger inga garantier för hög tillgänglighet.


## <a name="pricing"></a>Priser

Priser för virtuella Azure-dator instanser är varierande, baserat på region och SKU. Mer information finns i avsnittet om priser för [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till fem decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer instansen inte att avlägsnas baserat på priset. Priset för instansen är det aktuella priset för den virtuella Azure-datorn eller priset för en standard instans, vilket någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.


## <a name="limitations"></a>Begränsningar

Följande storlekar stöds inte för Azure-Virtual Machines:
 - B-serien
 - Kampanj versioner av valfri storlek (t. ex. dv2, NV, NC, H kampanj storlek)

En virtuell Azure-dator kan distribueras till vilken region som helst, förutom Microsoft Azure Kina 21Vianet.

<a name="channel"></a>

Följande [typer av erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) stöds för närvarande:

-   Enterprise-avtal
-   Erbjudande kod för betala per användning (003P)
-   Sponsrat (0036P och 0136P)
- För Cloud Service Provider (CSP), se [partner Center](/partner-center/azure-plan-get-started) eller kontakta din partner direkt.

## <a name="eviction-policy"></a>Avlägsnandeprincip

När du skapar en skalnings uppsättning med Azure-Virtual Machines kan du ange att *principen ska* frigöras eller *tas bort*. 

Principen *frigör* flyttar de inaktuella instanserna till läget Stoppad-frigjord, så att du kan distribuera om avlägsnade instanser. Det finns dock ingen garanti för att allokeringen ska lyckas. De friallokerade virtuella datorerna räknas över till kvoten för din skalnings uppsättning och du debiteras för de underliggande diskarna. 

Om du vill att dina instanser ska tas bort när de avlägsnas, kan du ange vilken borttagnings princip som ska *tas bort*. När du har angett en princip för borttagning kan du skapa nya virtuella datorer genom att öka antalet skalnings uppsättnings instanser. De avlägsnade virtuella datorerna tas bort tillsammans med deras underliggande diskar och därför debiteras du inte för lagringen. Du kan också använda funktionen för automatisk skalning i skalnings uppsättningar för att automatiskt försöka och kompensera för avlägsnade virtuella datorer, men det finns ingen garanti för att allokeringen kommer att lyckas. Vi rekommenderar att du bara använder funktionen för autoskalning i skalnings uppsättningar för virtuella datorer i Azure-datorer när du anger att borttagnings principen ska tas bort för att undvika kostnaden för diskarna och vid kvot gränser. 

Användare kan välja att ta emot meddelanden i virtuella datorer via [Azure schemalagda händelser](../virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer avlägsnas och du har 30 sekunder på dig att slutföra jobben och utföra avstängnings uppgifter innan avlägsnandet. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Försök & återställa (förhands granskning)

Den här nya plattforms nivå funktionen kommer att använda AI för att automatiskt försöka återställa avstängda virtuella datorer i Azure-platsen i en skalnings uppsättning som upprätthåller antalet mål instanser. 

> [!IMPORTANT]
> Försök & Restore finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Försök & Restore Benefits:
- Försök att återställa Azure-Virtual Machines avlägsnas på grund av kapacitet.
- Återställda Azure-Virtual Machines förväntas köras under en längre tid med en lägre sannolikhet för en kapacitet som utlöses genom att avlägsnas.
- Förbättrar livs längd för en virtuell Azure-dator, så att arbets belastningar körs under en längre tid.
- Hjälper Virtual Machine Scale Sets att underhålla mål antalet för Azure-Virtual Machines, på samma sätt som att det redan finns en funktion som redan finns för virtuella datorer med betala per användning.

Försök & Restore är inaktiverat i skalnings uppsättningar som använder [autoskalning](virtual-machine-scale-sets-autoscale-overview.md). Antalet virtuella datorer i skalnings uppsättningen styrs av reglerna för autoskalning.

### <a name="register-for-try--restore"></a>Registrera dig för försök & återställning

Innan du kan använda funktionen försök & återställning måste du registrera din prenumeration för för hands versionen. Registreringen kan ta flera minuter att slutföra. Du kan slutföra funktions registreringen med Azure CLI eller PowerShell.


**Använda CLI**

Aktivera förhands granskningen för prenumerationen med hjälp av [AZ-funktionen registrera](/cli/azure/feature#az-feature-register) . 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Använda PowerShell** 

Använd cmdleten [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera för hands versionen av din prenumeration. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Placerings grupper

Placerings gruppen är en konstruktion som liknar en Azures tillgänglighets uppsättning, med egna fel domäner och uppgraderings domäner. Som standard består en skalningsuppsättning av en enda placeringsgrupp med maximalt 100 virtuella datorer. Om den skalnings uppsättnings egenskap som kallas `singlePlacementGroup` är *false* kan skalnings uppsättningen bestå av flera placerings grupper och ha ett intervall på 0 till 1 000 virtuella datorer. 

> [!IMPORTANT]
> Om du inte använder InfiniBand med HPC rekommenderar vi starkt att du ställer in egenskapen skalnings uppsättning `singlePlacementGroup` på *falskt* för att aktivera flera placerings grupper för bättre skalning i regionen eller zonen. 

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Distribuera Azure-Virtual Machines i skalnings uppsättningar

Om du vill distribuera Azure-Virtual Machines på skalnings uppsättningar kan du ställa in den nya *prioritets* flaggan på *plats*. Alla virtuella datorer i din skalnings uppsättning anges till dekor. Om du vill skapa en skalnings uppsättning med Azure-Virtual Machines använder du någon av följande metoder:
- [Azure-portalen](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-mallar](#resource-manager-templates)

## <a name="portal"></a>Portal

Processen för att skapa en skalnings uppsättning som använder Azure-Virtual Machines är densamma som beskrivs i [artikeln komma igång](quick-create-portal.md). När du distribuerar en skalnings uppsättning kan du välja att ange en dekor flagga och avpolicyn för borttagning: ![ skapa en skalnings uppsättning med Azure-Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Processen för att skapa en skalnings uppsättning med Azure-Virtual Machines är densamma som beskrivs i [artikeln komma igång](quick-create-cli.md). Lägg bara till "--prioritets plats" och Lägg till `--max-price` . I det här exemplet använder vi `-1` för `--max-price` så att instansen inte avlägsnas baserat på priset.

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

Processen för att skapa en skalnings uppsättning med Azure-Virtual Machines är densamma som beskrivs i [artikeln komma igång](quick-create-powershell.md).
Lägg bara till "-prioritets punkt" och ange en `-max-price` till [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    -max-price -1
```

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

Processen för att skapa en skalnings uppsättning som använder Azure-Virtual Machines är densamma som beskrivs i komma igång-artikeln för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). 

För distributioner av mallar för virtuella Azure-datorer, Använd `"apiVersion": "2019-03-01"` eller senare. 

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


## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan [simulera en avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) av en virtuell Azure-dator för att testa hur bra ditt program kommer att reagera på en plötslig avlägsning. 

Ersätt följande med din information: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` innebär att den simulerade avtagningen lyckades. 

## <a name="faq"></a>Vanliga frågor

**F:** Är en instans av en virtuell Azure-dator som är samma som standard instans när den har skapats?

**A:** Ja, förutom att det inte finns något service avtal för Azure-Virtual Machines och de kan avlägsnas när som helst.


**F:** Vad ska jag göra när du har avlägsnat, men behöver fortfarande kapacitet?

**A:** Vi rekommenderar att du använder standard-VM: ar i stället för Azure-Virtual Machines om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för den virtuella Azure-datorn?

**A:** Instanser av virtuella Azure-datorer och standard instanser kommer att ha separata kvotmallar. Kvoten för den virtuella Azure-datorn kommer att delas mellan virtuella datorer och skalnings uppsättnings instanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kan jag begära ytterligare kvot för den virtuella Azure-datorn?

**A:** Ja, du kommer att kunna skicka begäran om att öka kvoten för Azure-Virtual Machines genom processen för [standard kvot förfrågan](../azure-portal/supportability/per-vm-quota-requests.md).


**F:** Kan jag konvertera befintliga skalnings uppsättningar till skalnings uppsättningar för virtuella Azure-datorer?

**A:** Nej, det går bara att ställa in `Spot` flaggan vid skapande tillfället.


**F:** `low` Måste jag börja använda i stället om jag använde för låg prioritets skalnings uppsättningar `Spot` ?

**A:** För tillfället kommer både `low` och `Spot` att fungera, men du bör börja över gången till med `Spot` .


**F:** Kan jag skapa en skalnings uppsättning med både vanliga virtuella datorer och Azure-Virtual Machines?

**A:** Nej, en skalnings uppsättning kan inte stödja fler än en prioritets typ.


**F:**  Kan jag använda autoskalning med skalnings uppsättningar för virtuella Azure-datorer?

**A:** Ja, du kan ange regler för automatisk skalning på din skalnings uppsättning för den virtuella Azure-datorn. Om dina virtuella datorer avlägsnas kan autoskalning försöka skapa nya Virtual Machines för Azure-platsen. Kom ihåg att du inte garanterar den här kapaciteten. 


**F:**  Kan autoskalning användas med både borttagnings principer (frigör och ta bort)?

**A:** Ja, men vi rekommenderar att du anger en princip för att ta bort när du använder autoskalning. Detta beror på att friallokerade instanser räknas mot ditt kapacitets antal i skalnings uppsättningen. När du använder autoskalning når du förmodligen antalet mål instanser snabbt på grund av de frikopplade, avlägsnade instanserna. Dessutom kan skalnings åtgärderna påverkas av punkt avvisningar. Till exempel kan virtuella datorers skalnings uppsättnings instanser falla under angivet minsta antal på grund av flera borttagningar av punkter under skalnings åtgärder. 


**F:** Var kan jag skicka frågor?

**A:** Du kan skicka och tagga din fråga med `azure-spot` på [Q&A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Nästa steg

Se [prissättnings sidan för den virtuella datorns skalnings uppsättning](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) för pris information.
