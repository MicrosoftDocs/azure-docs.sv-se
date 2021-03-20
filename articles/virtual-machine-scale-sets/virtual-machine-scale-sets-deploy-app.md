---
title: Distribuera ett program till en skalnings uppsättning för virtuella Azure-datorer
description: Lär dig hur du distribuerar program till instanser av virtuella Linux-och Windows-datorer i en skalnings uppsättning
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7a4ddaf74df6e04c1597b9c106cd458ddebac55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079599"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Distribuera din app i VM-skalningsuppsättningar

Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. Den här artikeln beskriver hur du skapar en anpassad virtuell dator avbildning för instanser i en skalnings uppsättning eller kör installations skript automatiskt på befintliga VM-instanser. Du lär dig också hur du hanterar program-eller OS-uppdateringar i en skalnings uppsättning.


## <a name="build-a-custom-vm-image"></a>Bygg en anpassad VM-avbildning
När du använder en av Azures plattforms avbildningar för att skapa instanserna i din skalnings uppsättning installeras eller konfigureras ingen ytterligare program vara. Du kan automatisera installationen av dessa komponenter, men det lägger till den tid det tar att etablera virtuella dator instanser i dina skalnings uppsättningar. Om du tillämpar många konfigurations ändringar på VM-instanserna finns det hanterings resurser för dessa konfigurations skript och-uppgifter.

För att minska konfigurations hanteringen och tiden för att etablera en virtuell dator kan du skapa en anpassad VM-avbildning som är redo att köra ditt program så snart en instans etableras i skalnings uppsättningen. Mer information om hur du skapar och använder en anpassad VM-avbildning med en skalnings uppsättning finns i följande Självstudier:

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="install-an-app-with-the-custom-script-extension"></a><a name="already-provisioned"></a>Installera en app med tillägget för anpassat skript
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Mer information om hur du installerar en app med ett anpassat skript tillägg finns i följande Självstudier:

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager-mall](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installera en app på en virtuell Windows-dator med PowerShell DSC
[PowerShell Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) är en hanterings plattform för att definiera konfigurationen för mål datorer. DSC-konfigurationer definierar vad som ska installeras på en dator och hur du konfigurerar värden. En lokal Configuration Manager-motor (LCM) körs på varje målnod som bearbetar begärda åtgärder baserat på push-konfigurationer.

Med PowerShell DSC-tillägget kan du anpassa VM-instanser i en skalnings uppsättning med PowerShell. Följande exempel:

- Instruerar de virtuella dator instanserna att hämta ett DSC-paket från GitHub- *https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*
- Ställer in tillägget för att köra ett installations skript – `configure-http.ps1`
- Hämtar information om en skalnings uppsättning med [Get-AzVmss](/powershell/module/az.compute/get-azvmss)
- Tillämpar tillägget på de virtuella dator instanserna med [Update-AzVmss](/powershell/module/az.compute/update-azvmss)

DSC-tillägget tillämpas på *myScaleSet* VM-instanser i resurs gruppen med namnet *myResourceGroup*. Ange dina egna namn enligt följande:

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

Om uppgraderings principen på din skalnings uppsättning är *manuell* uppdaterar du VM-instanserna med [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance). Denna cmdlet tillämpar den uppdaterade skalnings uppsättnings konfigurationen på VM-instanserna och installerar programmet.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installera en app på en virtuell Linux-dator med Cloud-Init
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) är ett vanligt sätt att anpassa en virtuell Linux-dator när den startas för första gången. Du kan använda cloud-init till att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom cloud-init körs under hela den ursprungliga startprocessen finns det inga fler steg eller obligatoriska agenter att tillämpa för konfigurationen.

Cloud-init fungerar med olika distributioner. Du använder till exempel inte **apt-get install** eller **yum install** när du vill installera ett paket. I stället definierar du en lista med paket att installera. Cloud-init använder automatiskt rätt pakethanteringsverktyg för den distribution du valt.

Mer information, inklusive ett exempel *cloud-init.txt* -fil, finns i [använda Cloud-Init för att anpassa virtuella Azure-datorer](../virtual-machines/linux/using-cloud-init.md).

Om du vill skapa en skalnings uppsättning och använda en Cloud-Init-fil lägger du till `--custom-data` parametern i kommandot [AZ VMSS Create](/cli/azure/vmss) och anger namnet på en Cloud-Init-fil. I följande exempel skapas en skalnings uppsättning med namnet *myScaleSet* i *myResourceGroup* och konfigurerar virtuella dator instanser med en fil med namnet *cloud-init.txt*. Ange dina egna namn enligt följande:

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
När nya OS-versioner är tillgängliga kan du använda eller skapa en ny anpassad avbildning och [distribuera OS-uppgraderingar](virtual-machine-scale-sets-upgrade-scale-set.md) till en skalnings uppsättning. Varje VM-instans uppgraderas till den senaste avbildning som du anger. Du kan använda en anpassad avbildning med programmet förinstallerat, det anpassade skript tillägget eller PowerShell DSC för att programmet ska vara automatiskt tillgängligt när du utför uppgraderingen. Du kan behöva planera för program underhåll när du utför den här processen för att säkerställa att det inte finns några kompatibilitetsproblem med versioner.

Om du använder en anpassad VM-avbildning med programmet förinstallerat kan du integrera program uppdateringarna med en distributions-pipeline för att skapa nya avbildningar och distribuera OS-uppgraderingar över skalnings uppsättningen. Med den här metoden kan pipelinen hämta de senaste versionerna av programmet, skapa och validera en VM-avbildning och sedan uppgradera de virtuella dator instanserna i skalnings uppsättningen. Om du vill köra en distributions pipeline som skapar och distribuerar program uppdateringar i anpassade VM-avbildningar kan du [skapa en paket avbildning och distribuera med Azure DevOps Services](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), eller använda en annan plattform som [Spinnaker](https://www.spinnaker.io/) eller [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Nästa steg
När du skapar och distribuerar program till dina skalnings uppsättningar kan du granska [skalnings uppsättningens design översikt](virtual-machine-scale-sets-design-overview.md). Mer information om hur du hanterar din skalnings uppsättning finns i [använda PowerShell för att hantera din skalnings uppsättning](./virtual-machine-scale-sets-manage-powershell.md).
