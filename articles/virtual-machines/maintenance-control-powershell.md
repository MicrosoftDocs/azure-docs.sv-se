---
title: Underhålls kontroll för virtuella Azure-datorer med PowerShell
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll och PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 2868d559f0d848095fa7fec174e09e1b9376c4ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552464"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>Styra uppdateringar med underhålls kontroll och Azure PowerShell

Med underhålls kontrollen kan du bestämma när du ska tillämpa plattforms uppdateringar på värd infrastrukturen för de isolerade virtuella datorerna och Azure-dedikerade värdar. I det här avsnittet beskrivs Azure PowerShell alternativ för underhålls kontroll. Mer information om fördelarna med att använda underhålls kontroll, dess begränsningar och andra hanterings alternativ finns i [Hantera plattforms uppdateringar med underhålls kontroll](maintenance-control.md).
 
## <a name="enable-the-powershell-module"></a>Aktivera PowerShell-modulen

Se till att `PowerShellGet` det är aktuellt.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Installera `Az.Maintenance` PowerShell-modulen.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Om du installerar lokalt, se till att du öppnar PowerShell-prompten som administratör.

Du kan också uppmanas att bekräfta att du vill installera från ett *ej betrott lager*. Skriv `Y` eller Välj **Ja till alla** för att installera modulen.


## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

Skapa en resurs grupp som en behållare för din konfiguration. I det här exemplet skapas en resurs grupp med namnet *myMaintenanceRG* i *öster*. Om du redan har en resurs grupp som du vill använda kan du hoppa över den här delen och ersätta resurs gruppens namn med dig i resten av exemplen.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Använd [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) för att skapa en underhålls konfiguration. I det här exemplet skapas en underhålls konfiguration med namnet *unconfig* som är begränsad till värden. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Med `-MaintenanceScope host` ser du till att underhålls konfigurationen används för att styra uppdateringar av värden.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett fel meddelande. Konfigurations namn måste vara unika för din resurs grupp.

Du kan fråga efter tillgängliga underhålls konfigurationer med [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Skapa en underhålls konfiguration med schemalagt fönster

Du kan också deklarera ett schemalagt fönster när Azure ska tillämpa uppdateringarna på dina resurser. I det här exemplet skapas en underhålls konfiguration med namnet config med ett schemalagt fönster på 5 timmar den fjärde måndagen i varje månad. När du har skapat en schemalagd period behöver du inte längre använda uppdateringarna manuellt.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> Underhålls **tiden** måste vara *2 timmar* eller längre. **Upprepning** av underhåll måste anges till minst en gång i 35 dagar.

**Upprepning** av underhåll kan uttryckas som varje dag, varje vecka eller varje månad. Några exempel är:
 - **dag**-RecurEvery "dag" **eller** "3Days" 
 - **veckovis**-RecurEvery "3Weeks" **eller** "Week lördag, söndag" 
 - **månatlig**-RecurEvery "månads day23, day24" **eller** "månads sista söndag" **eller** "månad fjärde måndagen"  
      

## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Använd [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) för att tilldela konfigurationen till den isolerade virtuella datorn eller den dedikerade Azure-värden.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med hjälp av konfigurationens ID. Ange `-ResourceType VirtualMachines` och ange namnet på den virtuella datorn för och `-ResourceName` resurs gruppen för den virtuella datorn för `-ResourceGroupName` . 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Dedikerad värd

Om du vill tillämpa en konfiguration på en dedikerad värd måste du också inkludera `-ResourceType hosts` , `-ResourceParentName` med namnet på värd gruppen och `-ResourceParentType hostGroups` . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Sök efter väntande uppdateringar

Använd [Get-AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) för att se om det finns väntande uppdateringar. Använd `-subscription` för att ange Azure-prenumerationen för den virtuella datorn om den skiljer sig från den som du är inloggad på.

Om det inte finns några uppdateringar att Visa kommer det här kommandot att returnera ingenting. Annars returneras ett PSApplyUpdate-objekt:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter väntande uppdateringar för en isolerad virtuell dator. I det här exemplet formateras utdata som en tabell för läsbarhet.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Dedikerad värd

För att söka efter väntande uppdateringar för en dedikerad värd. I det här exemplet formateras utdata som en tabell för läsbarhet. Ersätt värdena för resurserna med dina egna.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Tillämpa uppdateringar

Använd [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) för att tillämpa väntande uppdateringar.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Skapa en begäran om att tillämpa uppdateringar på en isolerad virtuell dator.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Vid lyckad kommer det här kommandot att returnera ett `PSApplyUpdate` objekt. Du kan använda attributet name i `Get-AzApplyUpdate` kommandot för att kontrol lera uppdaterings statusen. Se [kontrol lera uppdaterings status](#check-update-status).

### <a name="dedicated-host"></a>Dedikerad värd

Tillämpa uppdateringar på en dedikerad värd.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Kontrol lera uppdaterings status
Använd [Get-AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) för att kontrol lera status för en uppdatering. De kommandon som visas nedan visar status för den senaste uppdateringen med hjälp av `default` för- `-ApplyUpdateName` parametern. Du kan ersätta namnet på uppdateringen (som returneras av kommandot [New-AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) ) för att hämta status för en speciell uppdatering.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime kommer att vara den tid då uppdateringen slutfördes, antingen initierad av dig eller av plattformen i fallet själv underhålls period inte användes. Om en uppdatering aldrig har tillämpats via underhålls kontrollen visas standardvärdet.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter uppdateringar för en speciell virtuell dator.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Dedikerad värd

Sök efter uppdateringar till en dedikerad värd.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Ta bort en underhålls konfiguration

Använd [Remove-AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) om du vill ta bort en underhålls konfiguration.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).
