---
title: Skapa en skalningsuppsättning som använder Azure Spot Virtual Machines
description: Lär dig hur du skapar skalningsuppsättningar för virtuella Azure-datorer som använder Azure Spot Virtual Machines för att spara på kostnader.
author: JagVeerappan
ms.author: jagaveer
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 61bb87d84b96f988ae065a70b85d445fc8b96ccf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762955"
---
# <a name="azure-spot-virtual-machines-for-virtual-machine-scale-sets"></a>Azure Spot Virtual Machines för VM-skalningsuppsättningar 

Med Azure Spot Virtual Machines på skalningsuppsättningar kan du dra nytta av vår outnyttjade kapacitet med betydande kostnadsbesparingar. När Azure behöver kapaciteten tillbaka tar Azure-infrastrukturen bort instanser av virtuella Azure-datorer för VM med spot-kapacitet. Därför är instanser av virtuella Azure-datorer för ouppdelar bra för arbetsbelastningar som kan hantera avbrott som batchbearbetningsjobb, utvecklings-/testmiljöer, stora beräkningsarbetsbelastningar med mera.

Mängden tillgänglig kapacitet kan variera beroende på storlek, region, tid på dagen med mera. När du distribuerar instanser av virtuella Azure-datorer för VM med vm för VM med vm med hög kapacitet allokerar Azure endast instansen om det finns tillgänglig kapacitet, men det finns inget serviceavtal för dessa instanser. En VM-skalningsuppsättning för oskadlig plats i Azure distribueras i en enda feldomän och erbjuder inga garantier för hög tillgänglighet.


## <a name="pricing"></a>Priser

Prissättningen för instanser av virtuella Azure Spot-datorer varierar beroende på region och SKU. Mer information finns i prissättningen för [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) och [Windows.](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/) 


Med variabelprissättning har du möjlighet att ange ett maxpris i AMERIKANSKA dollar (USD) med upp till fem decimaler. Värdet skulle till exempel vara `0.98765` ett maxpris på 0,98765 USD per timme. Om du anger maxpriset `-1` till tas instansen inte bort baserat på priset. Priset för instansen blir det aktuella priset för den virtuella Azure Spot-datorn eller priset för en standardinstans, som någonsin är mindre, så länge det finns tillgänglig kapacitet och kvot.


## <a name="limitations"></a>Begränsningar

Följande storlekar stöds inte för Azure Spot Virtual Machines:
 - B-serien
 - Kampanjversioner av valfri storlek (t.ex. Dv2, NV, NC, H-kampanjstorlekar)

Azure Spot Virtual Machine kan distribueras till valfri region, förutom Microsoft Azure China 21Vianet.

<a name="channel"></a>

Följande typer [av erbjudanden](https://azure.microsoft.com/support/legal/offer-details/) stöds för närvarande:

-   Enterprise-avtal
-   Kod för betala per betala-erbjudande (003P)
-   Sponsored (0036P och 0136P)
- För Molntjänstleverantör (CSP) kan du gå [till Partnercenter](/partner-center/azure-plan-get-started) eller kontakta din partner direkt.

## <a name="eviction-policy"></a>Avlägsnandeprincip

När du skapar en skalningsuppsättning med Azure Spot Virtual Machines kan du ange borttagningsprincipen till *Avallokera* (standard) eller Ta *bort*. 

Principen *Avallokera* flyttar dina avlägsnade instanser till tillståndet stoppad-frigiven så att du kan omdistribuera avlägsnade instanser. Det finns dock ingen garanti för att allokeringen lyckas. De friterade virtuella datorerna räknas mot din skalningsuppsättningsinstanskvot och du debiteras för dina underliggande diskar. 

Om du vill att dina instanser ska tas bort när de avlägsnas kan du ange borttagningsprincipen för att ta *bort*. När borttagningsprincipen är inställd på att ta bort kan du skapa nya virtuella datorer genom att öka skalningsuppsättningsegenskapen för instansantal. De avlägsnade virtuella datorerna tas bort tillsammans med deras underliggande diskar, och du debiteras därför inte för lagringen. Du kan också använda funktionen för automatisk skalning i skalningsuppsättningar för att automatiskt försöka kompensera för avlägsnade virtuella datorer, men det finns ingen garanti för att allokeringen lyckas. Vi rekommenderar att du endast använder funktionen för automatisk skalning på Skalningsuppsättningar för virtuella Azure-datorer med oskadlig användning när du anger borttagningsprincipen att ta bort för att undvika kostnaden för dina diskar och för att nå kvotgränser. 

Användare kan välja att ta emot aviseringar på virtuella datorer via [Azure Schemalagda händelser](../virtual-machines/linux/scheduled-events.md). Detta meddelar dig om dina virtuella datorer avlägsnas och du har 30 sekunder på dig att slutföra alla jobb och utföra avstängningsåtgärder före avlägsningen. 

<a name="bkmk_try"></a>
## <a name="try--restore-preview"></a>Prova & återställning (förhandsversion)

Den här nya funktionen på plattformsnivå använder AI för att automatiskt försöka återställa avlägsnade instanser av virtuella Azure-datorer för VM med spot i en skalningsuppsättning för att upprätthålla antalet målinstanser. 

> [!IMPORTANT]
> Prova & återställningen för närvarande är en allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Prova & för återställning:
- Försök att återställa Azure Spot Virtual Machines avlägsnas på grund av kapacitet.
- Återställda Azure-Virtual Machines förväntas köras under en längre tid med en lägre sannolikhet för en kapacitet som utlöses avlägsning.
- Förbättrar livslängden för en virtuell Azure Spot-dator, så arbetsbelastningar körs under en längre tid.
- Hjälper Virtual Machine Scale Sets att upprätthålla målantalet för Azure Spot Virtual Machines, på liknande sätt som för att underhålla funktionen för antal mål som redan finns för virtuella datorer med användningsbaserade betalning.

Prova & har inaktiverats i skalningsuppsättningar som använder [autoskalning.](virtual-machine-scale-sets-autoscale-overview.md) Antalet virtuella datorer i skalningsuppsättningen styrs av reglerna för automatisk skalning.

### <a name="register-for-try--restore"></a>Registrera dig för att & återställning

Innan du kan använda funktionen & för återställning måste du registrera din prenumeration för förhandsversionen. Registreringen kan ta flera minuter att slutföra. Du kan använda Azure CLI eller PowerShell för att slutföra funktionsregistreringen.


**Använda CLI**

Använd [az feature register](/cli/azure/feature#az_feature_register) för att aktivera förhandsversionen för din prenumeration. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SpotTryRestore 
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SpotTryRestore 
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```
**Använda PowerShell** 

Använd [cmdleten Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera förhandsversionen för din prenumeration. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SpotTryRestore -ProviderNamespace Microsoft.Compute 
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

## <a name="placement-groups"></a>Placeringsgrupper

Placeringsgruppen är en konstruktion som liknar en Azure-tillgänglighetsuppsättning, med egna feldomäner och uppgraderingsdomäner. Som standard består en skalningsuppsättning av en enda placeringsgrupp med maximalt 100 virtuella datorer. Om skalningsuppsättningsegenskapen som anropas är inställd på false kan skalningsuppsättningen bestå av flera placeringsgrupper och ha ett intervall på `singlePlacementGroup` 0–1 000 virtuella datorer.  

> [!IMPORTANT]
> Såvida du inte använder Infiniband med HPC rekommenderar vi starkt att du ställer in skalningsuppsättningsegenskapen på false för att aktivera flera placeringsgrupper för bättre skalning i `singlePlacementGroup` regionen eller zonen.  

## <a name="deploying-azure-spot-virtual-machines-in-scale-sets"></a>Distribuera Azure Spot Virtual Machines i skalningsuppsättningar

Om du vill distribuera Azure Spot Virtual Machines på skalningsuppsättningar kan du ange den nya *prioritetsflaggan* till *Punkt.* Alla virtuella datorer i skalningsuppsättningen ställs in på Spot. Om du vill skapa en skalningsuppsättning med Azure Spot Virtual Machines använder du någon av följande metoder:
- [Azure-portalen](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-mallar](#resource-manager-templates)

## <a name="portal"></a>Portalen

Processen för att skapa en skalningsuppsättning som använder Azure Spot Virtual Machines är samma som beskrivs i [artikeln komma igång.](quick-create-portal.md) När du distribuerar en skalningsuppsättning kan du välja att ange flaggan för punkt och avlägsningsprincipen: Skapa en skalningsuppsättning med ![ Azure Spot Virtual Machines](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

Processen för att skapa en skalningsuppsättning med Azure Spot Virtual Machines är samma som beskrivs i [artikeln komma igång.](quick-create-cli.md) Lägg bara till "--Priority Spot" och lägg till `--max-price` . I det här exemplet använder `-1` vi för så att `--max-price` instansen inte avlägsnas baserat på priset.

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

Processen för att skapa en skalningsuppsättning med Azure Spot Virtual Machines är samma som beskrivs i [artikeln komma igång.](quick-create-powershell.md)
Lägg bara till "-Priority Spot" och ange en `-max-price` till [New-AzVmssConfig.](/powershell/module/az.compute/new-azvmssconfig)

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

Processen för att skapa en skalningsuppsättning som använder Azure Spot Virtual Machines är samma som beskrivs i artikeln komma igång för [Linux](quick-create-template-linux.md) eller [Windows](quick-create-template-windows.md). 

För distributioner av malldistributioner för virtuella Azure-datorer för VM med spot-användning `"apiVersion": "2019-03-01"` använder du eller senare. 

Lägg till `priority` egenskaperna , och i avsnittet och egenskapen i avsnittet i `evictionPolicy` `billingProfile` `"virtualMachineProfile":` `"singlePlacementGroup": false,` `"Microsoft.Compute/virtualMachineScaleSets"` mallen:

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

Om du vill ta bort instansen efter att den har avlägsnats ändrar du `evictionPolicy` parametern till `Delete` .


## <a name="simulate-an-eviction"></a>Simulera en avlägsning

Du kan [simulera en avlägsning](/rest/api/compute/virtualmachines/simulateeviction) av en virtuell Azure Spot-dator för att testa hur väl ditt program svarar på en plötslig avlägsning. 

Ersätt följande med din information: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

`Response Code: 204` innebär att den simulerade avlägsning lyckades. 

## <a name="faq"></a>Vanliga frågor

**F:** Är instansen av en virtuell Azure Spot-dator densamma som standardinstansen när den har skapats?

**S:** Ja, förutom att det inte finns något serviceavtal för Azure Spot Virtual Machines och de kan avlägsnas när som helst.


**F:** Vad ska du göra när du avlägsnas, men fortfarande behöver kapacitet?

**S:** Vi rekommenderar att du använder virtuella standard-datorer i stället för Azure Spot Virtual Machines om du behöver kapacitet direkt.


**F:** Hur hanteras kvoten för virtuella Azure Spot-datorer?

**S:** Instanser av virtuella Azure-datorer för VM med vm med vm för VM med vm som standard har separata kvotpooler. Kvoten för virtuella Azure-datorer för VM med vm med spot delas mellan virtuella datorer och skalningsuppsättningsinstanser. Läs mer i dokumentationen om [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](../azure-resource-manager/management/azure-subscription-service-limits.md).


**F:** Kan jag begära ytterligare kvot för virtuella Azure Spot-datorer?

**S:** Ja, du kommer att kunna skicka begäran för att öka din kvot för Azure Spot Virtual Machines genom [standardprocessen för kvotbegäran.](../azure-portal/supportability/per-vm-quota-requests.md)


**F:** Kan jag konvertera befintliga skalningsuppsättningar till vm-skalningsuppsättningar för virtuella Azure-datorer med spot- eller VM-skalningsuppsättningar?

**S:** Nej, det går `Spot` bara att ange flaggan när den skapas.


**F:** Om jag använde för `low` skalningsuppsättningar med låg prioritet, måste jag börja använda `Spot` i stället?

**S:** För tillfället fungerar både `low` `Spot` och, men du bör börja övergå till att använda `Spot` .


**F:** Kan jag skapa en skalningsuppsättning med både vanliga virtuella datorer och Azure Spot Virtual Machines?

**S:** Nej, en skalningsuppsättning har inte stöd för fler än en prioritetstyp.


**F:**  Kan jag använda autoskalning med Skalningsuppsättningar för virtuella datorer med Azure Spot?

**S:** Ja, du kan ange regler för automatisk skalning på vm-skalningsuppsättningen för Azure Spot. Om dina virtuella datorer avlägsnas kan autoskalning försöka skapa nya Azure Spot Virtual Machines. Kom dock ihåg att du inte är garanterad den här kapaciteten. 


**F:**  Fungerar autoskalning med både borttagningsprinciper (frisallokera och ta bort)?

**S:** Ja, men vi rekommenderar att du anger borttagningsprincipen som ska tas bort när du använder autoskalning. Det beror på att frisatta instanser räknas mot ditt kapacitetsantal i skalningsuppsättningen. När du använder autoskalning kommer du troligen att nå antalet målinstanser snabbt på grund av de frisade, avlägsnade instanserna. Dina skalningsåtgärder kan också påverkas av avlägsning av punkt. Instanser av VM-skalningsuppsättningen kan till exempel hamna under det minsta antal som angetts på grund av flera avlägsningar av vm-skalningsuppsättningen under skalningsåtgärder. 


**F:** Var kan jag ställa frågor?

**S:** Du kan publicera och tagga din fråga `azure-spot` med på Q&[A](/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Nästa steg

Se prissättningssidan [för VM-skalningsuppsättningen för](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) prisinformation.
