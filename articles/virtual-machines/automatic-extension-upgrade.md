---
title: Automatisk tilläggsuppgradering för virtuella datorer och skalningsuppsättningar i Azure
description: Lär dig hur du aktiverar uppgradering av automatiska tillägg för dina virtuella datorer och VM-skalningsuppsättningar i Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: bf9e802e2485e84211044ce650c7748e789e752e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762613"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Förhandsversion: Automatisk uppgradering av tillägg för virtuella datorer och skalningsuppsättningar i Azure

Automatisk uppgradering av tillägg är tillgängligt som förhandsversion för virtuella Azure-datorer och Azure Virtual Machine Scale Sets. När automatisk tilläggsuppgradering är aktiverat på en virtuell dator eller skalningsuppsättning uppgraderas tillägget automatiskt när tilläggsutgivaren släpper en ny version för tillägget.

 Automatisk tilläggsuppgradering har följande funktioner:
- Stöds för virtuella Azure-datorer och Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets stöds inte för närvarande.
- Uppgraderingar tillämpas i en distributionsmodell för tillgänglighet först (se nedan).
- För en VM-skalningsuppsättning uppgraderas högst 20 % av skalningsuppsättningens virtuella datorer i en enda batch. Den minsta batchstorleken är en virtuell dator.
- Fungerar för alla VM-storlekar och för både Windows- och Linux-tillägg.
- Du kan välja bort automatiska uppgraderingar när som helst.
- Automatisk tilläggsuppgradering kan aktiveras på en Virtual Machine Scale Sets av valfri storlek.
- Varje tillägg som stöds registreras individuellt och du kan välja vilka tillägg som ska uppgraderas automatiskt.
- Stöds i alla offentliga molnregioner.


> [!IMPORTANT]
> Automatisk tilläggsuppgradering är för närvarande i allmänt tillgänglig förhandsversion. En procedur för att delta krävs för att använda funktionerna i den offentliga förhandsversionen som beskrivs nedan.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Hur fungerar automatisk tilläggsuppgradering?
Uppgraderingsprocessen ersätter den befintliga tilläggsversionen på en virtuell dator med en ny version av samma tillägg när den publiceras av tilläggsutgivaren. Hälsotillståndet för den virtuella datorn övervakas när det nya tillägget har installerats. Om den virtuella datorn inte är i felfritt tillstånd inom 5 minuter efter uppgraderingen återställs tilläggsversionen till den tidigare versionen.

En misslyckad tilläggsuppdatering försöker automatiskt igen. Ett nytt försök görs automatiskt med några dagar utan att användaren behöver göra något.

### <a name="availability-first-updates"></a>Tillgänglighetsbaserade uppdateringar
Tillgänglighetsmodellen för plattformsorkestrerade uppdateringar säkerställer att tillgänglighetskonfigurationer i Azure respekteras över flera tillgänglighetsnivåer.

För en grupp virtuella datorer som genomgår en uppdatering dirigerar Azure-plattformen uppdateringar:

**Mellan regioner:**
- En uppdatering flyttas över Azure globalt i en fas för att förhindra distributionsfel i Hela Azure.
- En fas kan ha en eller flera regioner och en uppdatering flyttas mellan faser endast om berättigade virtuella datorer i föregående fas uppdateras korrekt.
- Geo-parkopplade regioner uppdateras inte samtidigt och kan inte vara i samma regionala fas.
- En uppdaterings framgång mäts genom att spåra hälsan för en virtuell dator efter uppdateringen. VM-hälsa spåras via hälsoindikatorer för plattformar för den virtuella datorn. Till Virtual Machine Scale Sets spåras DEN virtuella datorns hälsa via programhälsoavsökningar eller tillägget för programhälsa, om det tillämpas på skalningsuppsättningen.

**Inom en region:**
- Virtuella datorer i Tillgänglighetszoner uppdateras inte samtidigt.
- Enskilda virtuella datorer som inte ingår i en tillgänglighetsuppsättning batchas efter bästa försök för att undvika samtidiga uppdateringar för alla virtuella datorer i en prenumeration.  

**Inom en "uppsättning":**
- Alla virtuella datorer i en gemensam tillgänglighetsuppsättning eller skalningsuppsättning uppdateras inte samtidigt.  
- Virtuella datorer i en gemensam tillgänglighetsuppsättning uppdateras inom gränserna för uppdateringsdomänen och virtuella datorer över flera uppdateringsdomäner uppdateras inte samtidigt.  
- Virtuella datorer i en gemensam VM-skalningsuppsättning grupperas i batchar och uppdateras inom uppdateringsdomängränserna.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Uppgraderingsprocess för Virtual Machine Scale Sets
1. Innan du påbörjar uppgraderingsprocessen ser orkestratorn till att högst 20 % av de virtuella datorerna i hela skalningsuppsättningen är felaktiga (av någon anledning).

2. Uppgraderingsdirigeringen identifierar batchen med vm-instanser som ska uppgraderas. En uppgraderingsbatch kan ha högst 20 % av det totala antalet virtuella datorer, med en minsta batchstorlek på en virtuell dator.

3. För skalningsuppsättningar med konfigurerade programhälsoavsökningar eller tillägget för programhälsa väntar uppgraderingen i upp till 5 minuter (eller den definierade hälsoavsökningskonfigurationen) för att den virtuella datorn ska bli felfri innan nästa batch uppgraderas. Om en virtuell dator inte återställer sitt hälsotillstånd efter en uppgradering installeras som standard den tidigare tilläggsversionen på den virtuella datorn om.

4. Uppgraderingsdirigeringen spårar också procentandelen virtuella datorer som blir felaktiga efter en uppgradering. Uppgraderingen avbryts om mer än 20 % av de uppgraderade instanserna blir felaktiga under uppgraderingsprocessen.

Processen ovan fortsätter tills alla instanser i skalningsuppsättningen har uppgraderats.

Orkestratorn för skalningsuppsättningsuppgradering kontrollerar den övergripande skalningsuppsättningens hälsa innan varje batch uppgraderas. När du uppgraderar en batch kan det finnas andra samtidiga planerade eller oplanerade underhållsaktiviteter som kan påverka hälsotillståndet för dina virtuella datorer med skalningsuppsättningen. I sådana fall, om mer än 20 % av skalningsuppsättningens instanser blir felaktiga, stoppas uppgraderingen av skalningsuppsättningen i slutet av den aktuella batchen.

## <a name="supported-extensions"></a>Tillägg som stöds
Förhandsversionen av automatisk tilläggsuppgradering stöder följande tillägg (och fler läggs till regelbundet):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) och [Linux](./extensions/agent-dependency-linux.md)
- [Tillägget för programhälsa](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows och Linux


## <a name="enabling-preview-access"></a>Aktivera åtkomst till förhandsversionen
För att aktivera förhandsgranskningsfunktionen krävs en enda gång för funktionen **AutomaticExtensionUpgradePreview** per prenumeration, enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du aktiverar förhandsversionen för din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till Compute-resursprovidern.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd [cmdleten Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera förhandsversionen för din prenumeration.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Använd [az feature register](/cli/azure/feature#az_feature_register) för att aktivera förhandsversionen för din prenumeration.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Funktionsregistreringen kan ta upp till 15 minuter. Så här kontrollerar du registreringsstatusen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

När funktionen har registrerats för din prenumeration slutför du registreringsprocessen genom att sprida ändringen till Compute-resursprovidern.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Aktivera automatisk uppgradering av tillägg
Om du vill aktivera automatisk uppgradering av tillägg för ett tillägg måste du se till att egenskapen *enableAutomaticUpgrade* är inställd på *true* och läggs till i varje tilläggsdefinition individuellt.


### <a name="rest-api-for-virtual-machines"></a>REST API för Virtual Machines
Om du vill aktivera automatisk uppgradering av tillägg för ett tillägg (i det här Dependency Agent tillägget) på en virtuell Azure-dator använder du följande:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>REST API för Virtual Machine Scale Sets
Använd följande för att lägga till tillägget i skalningsuppsättningsmodellen:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell för Virtual Machines
Använd [cmdleten Set-AzVMExtension:](/powershell/module/az.compute/set-azvmextension)

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell för Virtual Machine Scale Sets
Använd [cmdleten Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till tillägget i skalningsuppsättningsmodellen:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Uppdatera skalningsuppsättningen med [Update-AzVmss när](/powershell/module/az.compute/update-azvmss) du har lagt till tillägget.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI för Virtual Machines
Använd [cmdleten az vm extension set:](/cli/azure/vm/extension#az_vm_extension_set)

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI för Virtual Machine Scale Sets
Använd [cmdleten az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) för att lägga till tillägget i skalningsuppsättningsmodellen:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Tilläggsuppgraderingar med flera tillägg

En VM- eller VM-skalningsuppsättning kan ha flera tillägg med automatisk tilläggsuppgradering aktiverad. Samma virtuella dator eller skalningsuppsättning kan också ha andra tillägg utan automatisk tilläggsuppgradering aktiverad.  

Om flera tilläggsuppgraderingar är tillgängliga för en virtuell dator kan uppgraderingarna batchas tillsammans, men varje tilläggsuppgradering tillämpas individuellt på en virtuell dator. Ett fel på ett tillägg påverkar inte de andra tilläggen som kan uppgraderas. Om två tillägg till exempel är schemalagda för en uppgradering och den första tilläggsuppgraderingen misslyckas, kommer det andra tillägget fortfarande att uppgraderas.

Automatiska tilläggsuppgraderingar kan också användas när en VM- eller VM-skalningsuppsättning har flera tillägg konfigurerade [med tilläggssekvensering](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Tilläggssekvensering gäller för den första distributionen av den virtuella datorn, och eventuella framtida tilläggsuppgraderingar för ett tillägg tillämpas oberoende av varandra.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Läs mer om tillägget för programhälsa](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
