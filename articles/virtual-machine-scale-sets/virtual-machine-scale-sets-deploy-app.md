---
title: Distribuera ett program till en VM-skalningsuppsättning i Azure
description: Lär dig hur du distribuerar program till instanser av virtuella Linux- och Windows-datorer i en skalningsuppsättning
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 078c78f9fe9e52ee2a71784d5c5ae5c2a478fbe4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484263"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuera din app i VM-skalningsuppsättningar

Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. Den här artikeln beskriver olika sätt att skapa en anpassad VM-avbildning för instanser i en skalningsuppsättning eller automatiskt köra installationsskript på befintliga VM-instanser. Du får också lära dig hur du hanterar program- eller OS-uppdateringar i en skalningsuppsättning.


## <a name="build-a-custom-vm-image"></a>Skapa en anpassad vm-avbildning
När du använder en av Azure-plattformsavbildningarna för att skapa instanserna i din skalningsuppsättning installeras eller konfigureras ingen ytterligare programvara. Du kan automatisera installationen av dessa komponenter, men det ökar den tid det tar att etablera VM-instanser till dina skalningsuppsättningar. Om du tillämpar många konfigurationsändringar på de virtuella datorinstanserna finns det hanteringskostnader för dessa konfigurationsskript och uppgifter.

Om du vill minska konfigurationshanteringen och tiden för att etablera en virtuell dator kan du skapa en anpassad virtuell datoravbildning som är redo att köra programmet så snart en instans etableras i skalningsuppsättningen. Mer information om hur du skapar och använder en anpassad vm-avbildning med en skalningsuppsättning finns i följande självstudier:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Installera en app med tillägget för anpassat skript
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Mer information om hur du installerar en app med ett tillägg för anpassat skript finns i följande självstudier:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-mall](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installera en app på en virtuell Windows-dator med PowerShell DSC
[PowerShell Desired State Configuration (DSC) är](/powershell/scripting/dsc/overview/overview) en hanteringsplattform för att definiera konfigurationen av måldatorer. DSC-konfigurationer definierar vad som ska installeras på en dator och hur värden ska konfigureras. En LCM Konfigurationshanteraren motor (Local Konfigurationshanteraren) körs på varje målnod som bearbetar begärda åtgärder baserat på push-konfigurationer.

Med PowerShell DSC-tillägget kan du anpassa virtuella datorinstanser i en skalningsuppsättning med PowerShell. Följande exempel:

- Instruerar de virtuella datorinstanserna att ladda ned ett DSC-paket från GitHub – *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Anger tillägget för att köra ett installationsskript – `configure-http.ps1`
- Hämtar information om en skalningsuppsättning [med Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Tillämpar tillägget på de virtuella datorinstanserna [med Update-AzVmss](/powershell/module/az.compute/update-azvmss)

DSC-tillägget tillämpas på *myScaleSet* VM-instanserna i resursgruppen med namnet *myResourceGroup.* Ange dina egna namn på följande sätt:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Om uppgraderingsprincipen för din skalningsuppsättning är *manuell* uppdaterar du dina [VM-instanser med Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Denna cmdlet tillämpar den uppdaterade skalningsuppsättningskonfigurationen på VM-instanserna och installerar ditt program.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installera en app på en virtuell Linux-dator med cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Mer information, inklusive ett exempel på *cloud-init.txt* finns i [Använda cloud-init för att anpassa virtuella Azure-datorer.](../virtual-machines/linux/using-cloud-init.md)

Om du vill skapa en skalningsuppsättning och använda en cloud-init-fil lägger du till parametern i kommandot az vmss create och anger namnet på en `--custom-data` cloud-init-fil. [](/cli/azure/vmss) I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* i *myResourceGroup* och vm-instanser konfigureras med en fil *med namnetcloud-init.txt*. Ange dina egna namn på följande sätt:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>Installera program med OS-uppdateringar
När nya versioner av operativsystemet är tillgängliga kan du använda eller skapa en ny anpassad avbildning och [distribuera OS-uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md) till en skalningsuppsättning. Varje VM-instans uppgraderas till den senaste avbildning som du anger. Du kan använda en anpassad avbildning med programmet förinstallerat, tillägget för anpassat skript eller PowerShell DSC för att göra programmet automatiskt tillgängligt när du utför uppgraderingen. Du kan behöva planera för programunderhåll när du utför den här processen för att säkerställa att det inte finns några versionskompatibilitetsproblem.

Om du använder en anpassad virtuell datoravbildning med programmet förinstallerat kan du integrera programuppdateringarna med en distributionspipeline för att skapa de nya avbildningarna och distribuera OS-uppgraderingar i skalningsuppsättningen. Med den här metoden kan pipelinen hämta de senaste programbyggena, skapa och verifiera en VM-avbildning och sedan uppgradera VM-instanserna i skalningsuppsättningen. Om du vill köra en distributionspipeline som skapar och distribuerar programuppdateringar över anpassade VM-avbildningar kan du skapa en Packer-avbildning och distribuera med [Azure DevOps Services,](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)eller använda en annan plattform som [Spinnaker](https://www.spinnaker.io/) eller [Jenkins.](https://jenkins.io/)


## <a name="next-steps"></a>Nästa steg
När du skapar och distribuerar program till dina skalningsuppsättningar kan du läsa Översikt över [skalningsuppsättningens design.](virtual-machine-scale-sets-design-overview.md) Mer information om hur du hanterar din skalningsuppsättning finns i [Använda PowerShell för att hantera din skalningsuppsättning.](./virtual-machine-scale-sets-manage-powershell.md)
