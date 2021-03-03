---
title: Automatisk tilläggs uppgradering för virtuella datorer och skalnings uppsättningar i Azure
description: Lär dig hur du aktiverar den automatiska tilläggs uppgraderingen för dina virtuella datorer och skalnings uppsättningar för virtuella datorer i Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrades
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: 104eada6dc342c21b8da2f409756e9f34c103936
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668341"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>För hands version: automatisk tilläggs uppgradering för virtuella datorer och skalnings uppsättningar i Azure

Automatisk tilläggs uppgradering är tillgänglig i för hands versionen för virtuella Azure-datorer och Azure Virtual Machine Scale Sets. När automatisk tilläggs uppgradering har Aktiver ATS på en virtuell dator eller skalnings uppsättning uppgraderas tillägget automatiskt varje gång som tilläggets utgivare släpper en ny version för tillägget.

 Automatisk uppgradering av tillägg har följande funktioner:
- Stöds för virtuella Azure-datorer och Azure-Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets stöds inte för närvarande.
- Uppgraderingar tillämpas i en tillgänglighets-Första distributions modell (beskrivs nedan).
- För en skalnings uppsättning för virtuella datorer uppgraderas inte mer än 20% av de virtuella datorerna för skalnings uppsättningen i en enda batch. Den minsta batchstorleken är en virtuell dator.
- Fungerar för alla VM-storlekar och för både Windows-och Linux-tillägg.
- Du kan när som helst välja att inte använda automatiska uppgraderingar.
- Automatisk uppgradering av tillägg kan aktive ras i en Virtual Machine Scale Sets i valfri storlek.
- Alla tillägg som stöds registreras individuellt och du kan välja vilka tillägg som ska uppgraderas automatiskt.
- Stöds i alla offentliga moln regioner.


> [!IMPORTANT]
> Automatisk tilläggs uppgradering är för närvarande en offentlig för hands version. En opt-in-procedur krävs för att använda den offentliga för hands versions funktionen som beskrivs nedan.
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Hur fungerar automatisk uppgradering av tillägg?
Uppgraderings processen för tillägg ersätter den befintliga tilläggs versionen på en virtuell dator med en ny version av samma tillägg när den publiceras av tilläggs utgivaren. Hälso tillståndet för den virtuella datorn övervakas när det nya tillägget har installerats. Om den virtuella datorn inte är i felfritt tillstånd inom 5 minuter efter uppgraderingen, återställs tilläggs versionen till den tidigare versionen.

En misslyckad tilläggs uppdatering görs automatiskt. Ett nytt försök görs med några dagar automatiskt utan att användaren tillfrågas.

### <a name="availability-first-updates"></a>Tillgänglighet – första uppdateringar
Den tillgänglighets-första modellen för plattforms oberoende uppdateringar ser till att tillgänglighets konfigurationerna i Azure respekteras över flera tillgänglighets nivåer.

För en grupp virtuella datorer som genomgår en uppdatering kommer Azure-plattformen att dirigera uppdateringar:

**Mellan regioner:**
- En uppdatering flyttas över Azure globalt i ett stegvist sätt för att förhindra distributions problem i hela Azure.
- En "fas" kan ha en eller flera regioner, och en uppdatering flyttas över flera faser om kvalificerade virtuella datorer i föregående fas uppdateras.
- Geo-kopplade områden kommer inte att uppdateras samtidigt och får inte finnas i samma regionala fas.
- Framgången av en uppdatering mäts genom att spåra hälsan för en VM post-uppdatering. VM-hälsan spåras via plattforms hälso indikatorer för den virtuella datorn. För Virtual Machine Scale Sets spåras VM-hälsan genom program hälso avsökningar eller program hälso tillägget, om det tillämpas på skalnings uppsättningen.

**Inom en region:**
- Virtuella datorer i olika Tillgänglighetszoner uppdateras inte samtidigt.
- Enskilda virtuella datorer som inte ingår i en tillgänglighets uppsättning grupperas på bästa möjliga sätt för att undvika samtidiga uppdateringar för alla virtuella datorer i en prenumeration.  

**I en ' set ':**
- Alla virtuella datorer i en gemensam tillgänglighets uppsättning eller skalnings uppsättning uppdateras inte samtidigt.  
- Virtuella datorer i en gemensam tillgänglighets uppsättning uppdateras inom gränserna för uppdaterings domänen och virtuella datorer över flera uppdaterings domäner uppdateras inte samtidigt.  
- Virtuella datorer i en gemensam skalnings uppsättning för virtuella datorer grupperas i batchar och uppdateras inom gränserna för uppdaterings domänen.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Uppgraderings process för Virtual Machine Scale Sets
1. Innan du påbörjar uppgraderings processen ser Orchestrator till att högst 20% av de virtuella datorerna i hela skalnings uppsättningen är felaktiga (oavsett orsak).

2. Uppgraderings hanteraren identifierar batchen för de VM-instanser som ska uppgraderas. En uppgraderings grupp kan ha högst 20% av det totala antalet virtuella datorer, beroende på en minsta batchstorlek för en virtuell dator.

3. För skalnings uppsättningar med konfigurerade program hälso avsökningar eller program hälso tillägg, väntar uppgraderingen upp till 5 minuter (eller den definierade hälso avsöknings konfigurationen) för att den virtuella datorn ska bli felfri innan nästa batch uppgraderas. Om en virtuell dator inte återställer sitt hälso tillstånd efter en uppgradering, installeras som standard den tidigare tilläggs versionen på den virtuella datorn.

4. Uppgraderings hanteraren spårar även procent andelen virtuella datorer som blir skadade efter en uppgradering. Uppgraderingen stoppas om fler än 20% av de uppgraderade instanserna blir felaktiga under uppgraderings processen.

Ovanstående process fortsätter tills alla instanser i skalnings uppsättningen har uppgraderats.

Uppgraderings processen för skalnings uppsättningen kontrollerar den övergripande skalnings uppsättningens hälso tillstånd innan du uppgraderar varje batch. När du uppgraderar en batch kan det finnas andra samtidiga planerade eller oplanerade underhålls aktiviteter som kan påverka hälso tillståndet för dina virtuella datorer i skalnings uppsättningen. I sådana fall, om fler än 20% av skalnings uppsättningens instanser blir felaktiga, stoppas uppgraderingen av skalnings uppsättningen i slutet av den aktuella batchen.

## <a name="supported-extensions"></a>Tillägg som stöds
För hands versionen av den automatiska tilläggs uppgraderingen stöder följande tillägg (och fler läggs till regelbundet):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) och [Linux](./extensions/agent-dependency-linux.md)
- [Program hälso tillägg](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows och Linux


## <a name="enabling-preview-access"></a>Aktivera för hands versions åtkomst
Att aktivera för hands versions funktionen kräver en engångs anmälan för funktionen **AutomaticExtensionUpgradePreview** per prenumeration, enligt beskrivningen nedan.

### <a name="rest-api"></a>REST-API
I följande exempel beskrivs hur du aktiverar för hands versionen av din prenumeration:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Använd cmdleten [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) för att aktivera för hands versionen av din prenumeration.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Aktivera förhands granskningen för prenumerationen med hjälp av [AZ-funktionen registrera](/cli/azure/feature#az-feature-register) .

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Funktions registreringen kan ta upp till 15 minuter. Kontrol lera registrerings statusen:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

När funktionen har registrerats för din prenumeration slutför du opt-in-processen genom att sprida ändringen till beräknings resurs leverantören.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Aktiverar automatisk tilläggs uppgradering
Om du vill aktivera automatisk tilläggs uppgradering för ett tillägg måste du se till att egenskapen *enableAutomaticUpgrade* är inställd på *True* och läggs till varje tilläggs definition separat.


### <a name="rest-api-for-virtual-machines"></a>REST API för Virtual Machines
Om du vill aktivera automatisk uppgradering av tillägg för ett tillägg (i det här exemplet Dependency Agent tillägget) på en virtuell Azure-dator använder du följande:

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
Använd följande för att lägga till tillägget i skalnings uppsättnings modellen:

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
Använd cmdleten [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

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
Använd cmdleten [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) för att lägga till tillägget i skalnings uppsättnings modellen:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Uppdatera skalnings uppsättningen med [Update-AzVmss](/powershell/module/az.compute/update-azvmss) när du har lagt till tillägget.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI för Virtual Machines
Använd cmdlet: en för [AZ VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) :

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
Använd cmdleten [AZ VMSS Extension set](/cli/azure/vmss/extension#az_vmss_extension_set) för att lägga till tillägget i skalnings uppsättnings modellen:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Tilläggs uppgraderingar med flera tillägg

En virtuell dator eller en skalnings uppsättning för virtuella datorer kan ha flera tillägg med automatisk tilläggs uppgradering aktive rad. Samma virtuella dator eller skalnings uppsättning kan också ha andra tillägg utan automatisk tilläggs uppgradering aktive rad.  

Om flera tillägg för tillägg är tillgängliga för en virtuell dator, kan uppgraderingar skapas tillsammans, men varje tilläggs uppgradering tillämpas individuellt på en virtuell dator. Ett haveri i ett tillägg påverkar inte de andra tillägg som kan uppgraderas. Om till exempel två tillägg har schemalagts för en uppgradering och det första tillägget Miss lyckas, kommer det andra tillägget fortfarande att uppgraderas.

Automatiska tilläggs uppgraderingar kan också tillämpas när en virtuell dator eller en skalnings uppsättning för virtuella datorer har flera tillägg som kon figurer ATS med [tilläggs-sekvenseringen](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Det går att använda sekvenseringen för den första distributionen av den virtuella datorn och eventuella framtida tilläggs uppgraderingar i ett tillägg tillämpas oberoende av varandra.


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Läs mer om program hälso tillägget](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
