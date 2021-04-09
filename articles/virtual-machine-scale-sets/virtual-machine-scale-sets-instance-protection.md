---
title: Instans skydd för instanser av skalnings uppsättningar för virtuella Azure-datorer
description: Lär dig hur du skyddar skalnings uppsättnings instanser i Azure Virtual Machine från skalnings-och skalnings uppsättningar.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 8b331eaf52a0a97232d481dccfff932221cd5faa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933465"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Instans skydd för instanser av skalnings uppsättningar för virtuella Azure-datorer

Skalnings uppsättningar för virtuella Azure-datorer ger bättre elastiskhet för dina arbets belastningar genom [autoskalning](virtual-machine-scale-sets-autoscale-overview.md), så du kan konfigurera när infrastrukturen skalar ut och när den skalas upp. Med skalnings uppsättningar kan du också centralt hantera, konfigurera och uppdatera ett stort antal virtuella datorer via olika inställningar för [uppgraderings principer](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) . Du kan konfigurera en uppdatering för skalnings uppsättnings modellen och den nya konfigurationen tillämpas automatiskt på varje skalnings uppsättnings instans om du har angett att uppgraderings principen ska vara automatisk eller rullande.

När programmet bearbetar trafik kan det finnas situationer där du vill att specifika instanser ska behandlas annorlunda än resten av skalnings uppsättnings instansen. Vissa instanser i skalnings uppsättningen kan till exempel utföra långvariga åtgärder och du vill inte att instanserna ska skalas förrän åtgärderna har slutförts. Du kanske också har specialiserade några få instanser i skalnings uppsättningen för att utföra ytterligare eller andra uppgifter än andra medlemmar i skalnings uppsättningen. Du behöver dessa "särskilda" virtuella datorer som inte ska ändras med de andra instanserna i skalnings uppsättningen. Instans skydd ger ytterligare kontroller för att aktivera dessa och andra scenarier för ditt program.

I den här artikeln beskrivs hur du kan tillämpa och använda olika instans skydds funktioner med skalnings uppsättnings instanser.

## <a name="types-of-instance-protection"></a>Typer av instans skydd
Skalnings uppsättningar ger två typer av funktioner för instans skydd:

-   **Skydda från skalbarhet**
    - Aktive rad via egenskapen **protectFromScaleIn** på skalnings uppsättnings instansen
    - Skyddar instans från autoskalning initierad skalning – in
    - Instans åtgärder som initierats av användaren (inklusive instans borttagning) **blockeras inte**
    - Åtgärder som initieras på skalnings uppsättningen (uppgradering, avbildning, frigörning osv.) **blockeras inte**

-   **Skydda från skalnings uppsättnings åtgärder**
    - Aktive rad via egenskapen **protectFromScaleSetActions** på skalnings uppsättnings instansen
    - Skyddar instans från autoskalning initierad skalning – in
    - Skyddar instansen från åtgärder som initierats i skalnings uppsättningen (till exempel uppgradering, avbildning, frigörning osv.)
    - Instans åtgärder som initierats av användaren (inklusive instans borttagning) **blockeras inte**
    - Borttagning av fullständig skalnings uppsättning är **inte blockerad**

## <a name="protect-from-scale-in"></a>Skydda från skalbarhet
Instans skydd kan tillämpas på skalnings uppsättnings instanser när instanserna har skapats. Skyddet tillämpas och ändras endast för [instans modellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) och inte av [skalnings uppsättnings modellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Det finns flera sätt att tillämpa skalnings skydd på dina skalnings uppsättnings instanser enligt beskrivningen i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Du kan använda skalnings skydd genom Azure Portal till en instans i skalnings uppsättningen. Det går inte att justera fler än en instans i taget. Upprepa stegen för varje instans som du vill skydda.
 
1. Gå till en befintlig skalnings uppsättning för virtuella datorer.
1. Välj **instanser** på menyn till vänster under **Inställningar**.
1. Välj namnet på den instans som du vill skydda.
1. Välj fliken **skydds princip** .
1. På bladet **skydds princip** väljer du alternativet **skydda från skalbarhet** .
1. Välj **Spara**. 

### <a name="rest-api"></a>REST-API

I följande exempel används skalnings skydd till en instans i skalnings uppsättningen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Instans skydd stöds endast med API version 2019-03-01 och senare

### <a name="azure-powershell"></a>Azure PowerShell

Använd cmdleten [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) för att tillämpa skalnings skydd på din skalnings uppsättnings instans.

I följande exempel används skalnings skydd till en instans i skalnings uppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) för att tillämpa skalnings skydd på din skalnings uppsättnings instans.

I följande exempel används skalnings skydd till en instans i skalnings uppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Skydda från skalnings uppsättnings åtgärder
Instans skydd kan tillämpas på skalnings uppsättnings instanser när instanserna har skapats. Skyddet tillämpas och ändras endast för [instans modellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) och inte av [skalnings uppsättnings modellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Att skydda en instans från skalnings uppsättnings åtgärder skyddar också instansen från autoskalning initierad skalning – i.

Det finns flera sätt att tillämpa skalnings uppsättnings åtgärder på dina skalnings uppsättnings instanser enligt beskrivningen i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Du kan använda skydd från skalnings uppsättnings åtgärder via Azure Portal till en instans i skalnings uppsättningen. Det går inte att justera fler än en instans i taget. Upprepa stegen för varje instans som du vill skydda.
 
1. Gå till en befintlig skalnings uppsättning för virtuella datorer.
1. Välj **instanser** på menyn till vänster under **Inställningar**.
1. Välj namnet på den instans som du vill skydda.
1. Välj fliken **skydds princip** .
1. Välj alternativet **skydda från skalnings uppsättning åtgärder** på bladet **skydds princip** .
1. Välj **Spara**. 

### <a name="rest-api"></a>REST-API

I följande exempel används skydd från skalnings uppsättnings åtgärder till en instans i skalnings uppsättningen.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Instans skydd stöds endast med API version 2019-03-01 och senare.</br>
Att skydda en instans från skalnings uppsättnings åtgärder skyddar också instansen från autoskalning initierad skalning – i. Du kan inte ange "protectFromScaleIn": false när du anger "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Använd cmdleten [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) för att tillämpa skydd från skalnings uppsättnings åtgärder till din skalnings uppsättnings instans.

I följande exempel används skydd från skalnings uppsättnings åtgärder till en instans i skalnings uppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [AZ VMSS Update](/cli/azure/vmss#az-vmss-update) för att tillämpa skydd från skalnings uppsättnings åtgärder till din skalnings uppsättnings instans.

I följande exempel används skydd från skalnings uppsättnings åtgärder till en instans i skalnings uppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Felsöka
### <a name="no-protectionpolicy-on-scale-set-model"></a>Ingen protectionPolicy i skalnings uppsättnings modellen
Instans skydd kan bara användas på skalnings uppsättnings instanser och inte i skalnings uppsättnings modellen.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Ingen protectionPolicy på en instans modell för skalnings uppsättning
Skydds principen tillämpas som standard inte på en instans när den skapas.

Du kan använda instans skydd för att skala uppsättnings instanser efter att instanserna har skapats.

### <a name="not-able-to-apply-instance-protection"></a>Det går inte att använda instans skydd
Instans skydd stöds endast med API version 2019-03-01 och senare. Kontrol lera vilken API-version som används och uppdatera vid behov. Du kan också behöva uppdatera din PowerShell eller CLI till den senaste versionen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på virtuella datorers skalnings uppsättningar.
