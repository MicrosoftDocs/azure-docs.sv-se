---
title: Instansskydd för instanser av skalningsuppsättningen för virtuella Azure-datorer
description: Lär dig hur du skyddar instanser av skalningsuppsättningsinstanser för virtuella Azure-datorer från åtgärder för inskalning och skalningsuppsättning.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 292abce3361c000eeeef2c399d5ffa2d2c4852e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762865"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Instansskydd för instanser av skalningsuppsättningen för virtuella Azure-datorer

Skalningsuppsättningar för virtuella Azure-datorer ger [](virtual-machine-scale-sets-autoscale-overview.md)bättre elasticitet för dina arbetsbelastningar via autoskalning, så att du kan konfigurera när din infrastruktur skalar ut och när den skalar in. Med skalningsuppsättningar kan du också centralt hantera, konfigurera och uppdatera ett stort antal virtuella datorer via olika [inställningar för uppgraderingsprincipen.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) Du kan konfigurera en uppdatering av skalningsuppsättningsmodellen och den nya konfigurationen tillämpas automatiskt på varje skalningsuppsättningsinstans om du har angett uppgraderingsprincipen till Automatisk eller Rullande.

När ditt program bearbetar trafik kan det finnas situationer där du vill att specifika instanser ska behandlas annorlunda än resten av skalningsuppsättningsinstansen. Till exempel kan vissa instanser i skalningsuppsättningen utföra långvariga åtgärder och du vill inte att dessa instanser ska skalas in förrän åtgärderna har slutförts. Du kan också ha specialiserade några instanser i skalningsuppsättningen för att utföra ytterligare eller andra uppgifter än de andra medlemmarna i skalningsuppsättningen. Du kräver att dessa "särskilda" virtuella datorer inte ska ändras med de andra instanserna i skalningsuppsättningen. Instansskydd ger ytterligare kontroller för att aktivera dessa och andra scenarier för ditt program.

Den här artikeln beskriver hur du kan tillämpa och använda olika funktioner för instansskydd med skalningsuppsättningsinstanser.

## <a name="types-of-instance-protection"></a>Typer av instansskydd
Skalningsuppsättningar har två typer av funktioner för instansskydd:

-   **Skydda mot inskalning**
    - Aktiveras via **egenskapen protectFromScaleIn** på skalningsuppsättningsinstansen
    - Skyddar instansen från autoskalningsinitierad inskalning
    - Användarinitierade instansåtgärder (inklusive instansens borttagning) **blockeras inte**
    - Åtgärder som initierats på skalningsuppsättningen (uppgradering, avimning, frilokalisering osv.) **blockeras inte**

-   **Skydda från åtgärder för skalningsuppsättning**
    - Aktiveras via **egenskapen protectFromScaleSetActions** på skalningsuppsättningsinstansen
    - Skyddar instansen från autoskalningsinitierad inskalning
    - Skyddar instansen från åtgärder som initierats på skalningsuppsättningen (till exempel uppgradering, avimning, frilokalisering osv.)
    - Användarinitierade instansåtgärder (inklusive instansens borttagning) **blockeras inte**
    - Borttagning av den fullständiga skalningsuppsättningen **blockeras inte**

## <a name="protect-from-scale-in"></a>Skydda mot inskalning
Instansskydd kan tillämpas på skalningsuppsättningsinstanser när instanserna har skapats. Skyddet tillämpas och ändras endast på [instansmodellen och](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) inte på [skalningsuppsättningsmodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Det finns flera sätt att tillämpa inskalningsskydd på dina skalningsuppsättningsinstanser enligt beskrivningen i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Du kan använda inskalningsskydd via Azure Portal på en instans i skalningsuppsättningen. Du kan inte justera fler än en instans i taget. Upprepa stegen för varje instans som du vill skydda.
 
1. Gå till en befintlig VM-skalningsuppsättning.
1. Välj **Instanser** på menyn till vänster under **Inställningar**.
1. Välj namnet på den instans som du vill skydda.
1. Välj **fliken Skyddsprincip.**
1. På **bladet Skyddsprincip** väljer du **alternativet Skydda från inskalning.**
1. Välj **Spara**. 

### <a name="rest-api"></a>REST-API

I följande exempel tillämpas inskalningsskydd på en instans i skalningsuppsättningen.

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
>Instansskydd stöds endast med API-version 2019-03-01 och senare

### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) för att tillämpa inskalningsskydd på din skalningsuppsättningsinstans.

I följande exempel tillämpas inskalningsskydd på en instans i skalningsuppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss update för](/cli/azure/vmss#az_vmss_update) att tillämpa inskalningsskydd på din skalningsuppsättningsinstans.

I följande exempel tillämpas inskalningsskydd på en instans i skalningsuppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Skydda från skalningsuppsättningsåtgärder
Instansskydd kan tillämpas på skalningsuppsättningsinstanser när instanserna har skapats. Skyddet tillämpas och ändras endast på [instansmodellen och](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) inte på [skalningsuppsättningsmodellen](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model).

Om du skyddar en instans från skalningsuppsättningsåtgärder skyddas även instansen från autoskalningsinitierad inskalning.

Det finns flera sätt att tillämpa skydd av skalningsuppsättningsåtgärder på dina skalningsuppsättningsinstanser enligt beskrivningen i exemplen nedan.

### <a name="azure-portal"></a>Azure Portal

Du kan tillämpa skydd från skalningsuppsättningsåtgärder via Azure Portal till en instans i skalningsuppsättningen. Du kan inte justera mer än en instans i taget. Upprepa stegen för varje instans som du vill skydda.
 
1. Gå till en befintlig VM-skalningsuppsättning.
1. Välj **Instanser** på menyn till vänster under **Inställningar**.
1. Välj namnet på den instans som du vill skydda.
1. Välj **fliken Skyddsprincip.**
1. På **bladet Skyddsprincip** väljer du alternativet **Skydda från skalningsuppsättningsåtgärder.**
1. Välj **Spara**. 

### <a name="rest-api"></a>REST-API

I följande exempel tillämpas skydd från skalningsuppsättningsåtgärder till en instans i skalningsuppsättningen.

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
>Instansskydd stöds endast med API-version 2019-03-01 och senare.</br>
Om du skyddar en instans från skalningsuppsättningsåtgärder skyddas även instansen från autoskalningsinitierad inskalning. Du kan inte ange "protectFromScaleIn": false när du anger "protectFromScaleSetActions": true

### <a name="azure-powershell"></a>Azure PowerShell

Använd [cmdleten Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) för att tillämpa skydd från skalningsuppsättningsåtgärder till din skalningsuppsättningsinstans.

I följande exempel tillämpas skydd från skalningsuppsättningsåtgärder till en instans i skalningsuppsättningen med instans-ID 0.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Använd [az vmss update för](/cli/azure/vmss#az_vmss_update) att tillämpa skydd från skalningsuppsättningsåtgärder till din skalningsuppsättningsinstans.

I följande exempel tillämpas skydd från skalningsuppsättningsåtgärder till en instans i skalningsuppsättningen med instans-ID 0.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Felsöka
### <a name="no-protectionpolicy-on-scale-set-model"></a>Ingen protectionPolicy på skalningsuppsättningsmodellen
Instansskydd gäller endast för skalningsuppsättningsinstanser och inte på skalningsuppsättningsmodellen.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Ingen protectionPolicy på skalningsuppsättningsinstansmodellen
Som standard tillämpas inte skyddsprincipen på en instans när den skapas.

Du kan använda instansskydd för skalningsuppsättningsinstanser när instanserna har skapats.

### <a name="not-able-to-apply-instance-protection"></a>Det går inte att tillämpa instansskydd
Instansskydd stöds endast med API-version 2019-03-01 och senare. Kontrollera vilken API-version som används och uppdatera efter behov. Du kan också behöva uppdatera PowerShell eller CLI till den senaste versionen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på VM-skalningsuppsättningar.
