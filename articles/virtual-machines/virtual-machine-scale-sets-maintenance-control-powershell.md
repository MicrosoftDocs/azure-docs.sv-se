---
title: Underhålls kontroll för uppgraderingar av OS-avbildningar i skalnings uppsättningar för virtuella Azure-datorer med PowerShell
description: Lär dig hur du styr när automatiska uppgraderingar av operativ system avbildningar distribueras till dina skalnings uppsättningar för virtuella Azure-datorer med hjälp av underhålls kontroll och PowerShell.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91974846"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>För hands version: underhålls kontroll för uppgraderingar av OS-avbildningar på virtuella Azure-datorer med hjälp av PowerShell

Med underhålls kontrollen kan du bestämma när automatiska uppgraderingar av gäst operativ system avbildningar ska användas för skalnings uppsättningar för virtuella datorer. I det här avsnittet beskrivs Azure PowerShell alternativ för underhålls kontroll. Mer information om hur du använder underhålls kontroll finns i [underhålls kontroll för skalnings uppsättningar för virtuella Azure-datorer](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> Underhålls kontroll för uppgraderingar av OS-avbildningar på virtuella Azure-dators skalnings uppsättningar finns för närvarande i offentlig för hands
> Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


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

## <a name="connect-to-an-azure-account"></a>Ansluta till ett Azure-konto

Anslut till önskat Azure-konto med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och [set-AzAccount](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

Skapa en resurs grupp som en behållare för din konfiguration. I det här exemplet skapas en resurs grupp med namnet *myMaintenanceRG* i *eastus2*. Om du redan har en resurs grupp som du vill använda kan du hoppa över den här delen. Ersätt bara resurs gruppens namn med ditt eget i resten av exemplen.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Använd [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) för att skapa en underhålls konfiguration. I det här exemplet skapas en underhålls konfiguration med namnet *unconfig* som omfattas av operativ system avbildningen. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> Underhålls **tiden** måste vara *5 timmar* eller längre. **Upprepning** av underhåll måste anges till *dag*.

Med `-MaintenanceScope OSImage` ser du till att underhålls konfigurationen används för att styra uppdateringar av gäst operativ systemet.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett fel meddelande. Konfigurations namn måste vara unika för din resurs grupp.

Du kan fråga efter tillgängliga underhålls konfigurationer med [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Associera skalnings uppsättningen för den virtuella datorn med underhålls konfigurationen

En skalnings uppsättning för en virtuell dator kan kopplas till alla underhålls konfigurationer oavsett region och prenumeration på underhålls konfigurationen. Genom väljer i till underhålls konfigurationen schemaläggs nya uppdateringar av operativ system avbildningar för skalnings uppsättningen automatiskt i nästa tillgängliga underhålls period.

Använd [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) för att associera skalnings konfigurationen för den virtuella datorn.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Aktivera automatisk uppgradering av operativ system

Du kan aktivera automatiska OS-uppgraderingar för varje skalnings uppsättning för virtuella datorer som ska använda underhålls kontroll. Se dokumentera [skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) för att aktivera automatiska uppgraderingar av operativ systemet på den virtuella datorns skalnings uppsättning. 


## <a name="next-steps"></a>Nästa steg

Lär dig mer om underhåll och uppdateringar för virtuella datorer som körs i Azure.

> [!div class="nextstepaction"]
> [Underhåll och uppdateringar](maintenance-and-updates.md)