---
title: Använd anpassade skalnings principer med skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du använder anpassade skalnings principer med skalnings uppsättningar för virtuella Azure-datorer som använder automatisk skalnings konfiguration för att hantera instans antal
services: virtual-machine-scale-sets
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 02/26/2020
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 9ca6310705d54d563aae746ab2dbfe6cb412e6a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92747798"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Använd anpassade skalnings principer med skalnings uppsättningar för virtuella Azure-datorer

En distribution av skalnings uppsättningar för virtuella datorer kan skalas upp eller skalas – i baserat på en matris med mått, inklusive plattforms-och användardefinierade anpassade mått. En skalbarhet som skapar nya virtuella datorer baserat på skalnings uppsättnings modellen, påverkar en skalbarhet som kör virtuella datorer som kan ha olika konfigurationer och/eller funktioner när den skalnings uppsättningens arbets belastning utvecklas. 

Funktionen för skalnings-i-princip ger användarna möjlighet att konfigurera i vilken ordning de virtuella datorerna ska skalas – i, med tre skalnings-konfigurationer: 

1. Standardvärde
2. NewestVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Standard princip för skalning

Som standard tillämpar skalnings uppsättningen för virtuella datorer den här principen för att avgöra vilken eller vilka instanser som ska skalas i. Med *standard* principen väljs virtuella datorer för skalning – i följande ordning:

1. Balansera virtuella datorer över tillgänglighets zoner (om skalnings uppsättningen har distribuerats i zonindelade-konfigurationen)
2. Balansera virtuella datorer över fel domäner (bästa ansträngning)
3. Ta bort virtuell dator med högst instans-ID

Användarna behöver inte ange någon skalnings princip om de bara vill att standard ordningen ska följas.

Observera att balans mellan tillgänglighets zoner eller fel domäner inte flyttar instanser mellan tillgänglighets zoner eller fel domäner. Balanseringen uppnås genom att virtuella datorer tas bort från obalanserade tillgänglighets zoner eller fel domäner tills distributionen av virtuella datorer blir balanserade.

### <a name="newestvm-scale-in-policy"></a>NewestVM-skalning – princip

Den här principen tar bort den senaste skapade virtuella datorn i skalnings uppsättningen, efter att de virtuella datorerna har balanserats över tillgänglighets zoner (för zonindelade-distributioner). Om den här principen aktive ras krävs en konfigurations ändring i den virtuella datorns skalnings uppsättnings modell.

### <a name="oldestvm-scale-in-policy"></a>OldestVM-skalning – princip

Den här principen tar bort den äldsta skapade virtuella datorn i skalnings uppsättningen, efter att de virtuella datorerna har balanserats över tillgänglighets zoner (för zonindelade-distributioner). Om den här principen aktive ras krävs en konfigurations ändring i den virtuella datorns skalnings uppsättnings modell.

## <a name="enabling-scale-in-policy"></a>Aktivera skalnings princip

En skalnings princip definieras i den virtuella datorns skal uppsättnings modell. Som anges i avsnitten ovan behövs en definition för skalnings i princip när du använder principerna "NewestVM" och "OldestVM". Skalnings uppsättningen för den virtuella datorn använder automatiskt "default"-skalnings principen om det inte finns någon definition av skalnings i modellen i skalnings uppsättningen. 

En skalnings princip kan definieras i modellen för skalnings uppsättningar för virtuella datorer på följande sätt:

### <a name="azure-portal"></a>Azure Portal
 
I följande steg definieras en skalnings princip när du skapar en ny skalnings uppsättning. 
 
1. Gå till **skalnings uppsättningar för virtuella datorer**.
1. Välj **+ Lägg** till för att skapa en ny skalnings uppsättning.
1. Gå till fliken **skalning** . 
1. Leta upp avsnittet **Scale-in-princip** .
1. Välj en skalnings princip i list rutan.
1. När du är klar med att skapa den nya skalnings uppsättningen väljer du **Granska + skapa** .

### <a name="using-api"></a>Med API:et

Kör en placering på den virtuella datorns skalnings uppsättning med API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Skapa en resurs grupp och skapa sedan en ny skalnings uppsättning med skalnings princip uppsättning som *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

I följande exempel läggs en skalnings princip till när du skapar en ny skalnings uppsättning. Skapa först en resurs grupp och skapa sedan en ny skalnings uppsättning med Scale-in-princip som *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Använda mall

Lägg till följande i mallen under "egenskaper":

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Ovanstående block anger att den virtuella datorns skalnings uppsättning tar bort den äldsta virtuella datorn i en zon bal anse rad skalnings uppsättning, när en skalning aktive ras (genom autoskalning eller manuell borttagning).

När en skalnings uppsättning för en virtuell dator inte är en avgränsad zon, tar skalnings uppsättningen först bort virtuella datorer över de Utjämnings zoner som är balanserade. I de obalanserade zonerna använder skalnings uppsättningen den skalnings princip som anges ovan för att avgöra vilken virtuell dator som ska skalas i. I det här fallet väljer skalnings uppsättningen den äldsta virtuella datorn i den zonen som ska tas bort inom en balanserad zon.

För icke-zonindelade skalnings uppsättning för virtuella datorer väljer principen den äldsta virtuella datorn över den skalnings uppsättning som ska tas bort.

Samma process gäller när "NewestVM" används i ovanstående skalnings princip.

## <a name="modifying-scale-in-policies"></a>Ändra skalnings principer

Att ändra skalnings principen följer samma process som när du tillämpar skalnings principen. Om du till exempel vill ändra principen från "OldestVM" till "NewestVM" i exemplet ovan kan du göra det genom att:

### <a name="azure-portal"></a>Azure Portal

Du kan ändra en skalnings princip för en befintlig skalnings uppsättning via Azure Portal. 
 
1. I en befintlig skalnings uppsättning för virtuell dator väljer du **skalning** från menyn till vänster.
1. Välj fliken **Scale-in-princip** .
1. Välj en skalnings princip i list rutan.
1. När du är klar väljer du **Spara**. 

### <a name="using-api"></a>Med API:et

Kör en placering på den virtuella datorns skalnings uppsättning med API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Uppdatera skalnings principen för en befintlig skalnings uppsättning:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Följande är ett exempel på hur du uppdaterar en skalnings princip för en befintlig skalnings uppsättning: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Använda mall

I mallen, under "egenskaper", ändrar du mallen enligt nedan och distribuerar om: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Samma process gäller om du väljer att ändra "NewestVM" till "default" eller "OldestVM"

## <a name="instance-protection-and-scale-in-policy"></a>Instans skydd och skalnings princip

Skalnings uppsättningar för virtuella datorer ger två typer av [instans skydd](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Skydda från skalbarhet
2. Skydda från skalnings uppsättnings åtgärder

En skyddad virtuell dator tas inte bort via en skalnings åtgärd, oavsett vilken skalnings princip som tillämpas. Till exempel, om VM_0 (äldsta virtuella datorn i skalnings uppsättningen) är skyddad från Scale-in och skalnings uppsättningen har "OldestVM" skalnings princip aktive rad, kommer VM_0 inte att beaktas för skalning i, även om det är den äldsta virtuella datorn i skalnings uppsättningen. 

En skyddad virtuell dator kan när som helst tas bort manuellt av användaren, oavsett vilken skalnings princip som är aktive rad i skalnings uppsättningen. 

## <a name="usage-examples"></a>Användningsexempel 

I exemplen nedan visas hur en skalnings uppsättning för virtuella datorer väljer vilka virtuella datorer som ska tas bort när en skalnings händelse utlöses. Virtuella datorer med högst instans-ID förutsätts vara de nyaste virtuella datorerna i skalnings uppsättningen och de virtuella datorerna med de minsta instans-ID: na antas vara de äldsta virtuella datorerna i skalnings uppsättningen. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM-skalning – princip

| Händelse                 | Instans-ID: n i zon 1  | Instans-ID: n i 1  | Instans-ID: n i zon 3  | Skalning – i markering                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inledande               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skala in              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Välj mellan Zon 1 och 2, även om Zon 3 har den äldsta virtuella datorn. Ta bort VM2 från Zon 2 eftersom det är den äldsta virtuella datorn i zonen.   |
| Skala in              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Välj Zon 1 även om Zon 3 har den äldsta virtuella datorn. Ta bort VM3 från Zon 1 eftersom det är den äldsta virtuella datorn i zonen.                  |
| Skala in              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Zoner är balanserade. Ta bort VM1 i Zon 3 eftersom det är den äldsta virtuella datorn i skalnings uppsättningen.                                               |
| Skala in              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Välj mellan Zon 1 och Zon 2. Ta bort VM4 i Zon 1 eftersom det är den äldsta virtuella datorn i de två zonerna.                              |
| Skala in              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Välj Zon 2 även om Zon 1 har den äldsta virtuella datorn. Ta bort VM6 i Zon 1 eftersom det är den äldsta virtuella datorn i zonen.                    |
| Skala in              | ***5***, 10            | 9, 11                  | 7, 8                   | Zoner är balanserade. Ta bort VM5 i Zon 1 eftersom det är den äldsta virtuella datorn i skalnings uppsättningen.                                                |

För icke-zonindelade virtuella datorers skalnings uppsättningar väljer principen den äldsta virtuella datorn över den skalnings uppsättning som ska tas bort. En "skyddad" virtuell dator kommer att hoppas över för borttagning.

### <a name="newestvm-scale-in-policy"></a>NewestVM-skalning – princip

| Händelse                 | Instans-ID: n i zon 1  | Instans-ID: n i 1  | Instans-ID: n i zon 3  | Skalning – i markering                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inledande               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skala in              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Välj mellan Zon 1 och 2. Ta bort VM11 – från Zon 2 eftersom det är den senaste virtuella datorn i de två zonerna.                                |
| Skala in              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Välj Zon 1 som har fler virtuella datorer än de andra två zonerna. Ta bort VM10 från Zon 1 som den senaste virtuella datorn i zonen.          |
| Skala in              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Zoner är balanserade. Ta bort VM9 i Zon 2 eftersom det är den nyaste virtuella datorn i skalnings uppsättningen.                                                |
| Skala in              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Välj mellan Zon 1 och Zon 3. Ta bort VM8 i Zon 3 eftersom det är den senaste virtuella datorn i zonen.                                      |
| Skala in              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Välj Zon 1 även om Zon 3 har den senaste virtuella datorn. Ta bort VM5 i Zon 1 eftersom det är den senaste virtuella datorn i zonen.                    |
| Skala in              | 3, 4                   | 2, 6                   | 1, ***7***             | Zoner är balanserade. Ta bort VM7 i Zon 3 eftersom det är den nyaste virtuella datorn i skalnings uppsättningen.                                                |

För icke-zonindelade virtuella datorers skalnings uppsättningar väljer principen den senaste virtuella datorn över den skalnings uppsättning som ska tas bort. En "skyddad" virtuell dator kommer att hoppas över för borttagning. 

## <a name="troubleshoot"></a>Felsöka

1. Det gick inte att aktivera scaleInPolicy om du får ett "BadRequest"-fel med fel meddelandet "Det gick inte att hitta medlemmen" scaleInPolicy "på objekt av typen" Properties "", kontrol lera den API-version som används för skalnings uppsättningen för den virtuella datorn. API version 2019-03-01 eller senare krävs för den här funktionen.

2. Fel urval av virtuella datorer för skalning – se exemplen ovan. Om den virtuella datorns skalnings uppsättning är en zonindelade-distribution tillämpas skalnings principen först i de obalanserade zonerna och sedan över skalnings uppsättningen när den är en zon bal anse rad. Om ordningen för skalning i inte är konsekvent med exemplen ovan kan du generera en fråga med den virtuella datorns skal uppsättnings team för fel sökning.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorers skalnings uppsättningar.
