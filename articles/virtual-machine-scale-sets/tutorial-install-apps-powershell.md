---
title: Självstudie – installera program i en skalnings uppsättning med Azure PowerShell
description: Läs hur du använder Azure PowerShell för att installera program på VM-skalningsuppsättningar med det anpassade skripttillägget
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 11/08/2018
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 7c365e83c0208a5105d9396fb788966bbd4643ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934592"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-azure-powershell"></a>Självstudie: Installera program i VM-skalningsuppsättningar med Azure PowerShell

Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. I en föregående självstudie fick du lära dig hur du skapar och använder en anpassad virtuell datoravbildning för att distribuera dina virtuella datorinstanser. Den här anpassade avbildningen inkluderade programinstallationer och konfigurationer. Du kan också automatisera installationen av programmen till en skalningsuppsättning efter att varje virtuell datorinstans distribueras, eller uppdatera ett program som redan körs på en skalningsuppsättning. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="what-is-the-azure-custom-script-extension"></a>Vad är det anpassade Azure-skripttillägget?
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning.

Tillägget för anpassat skript integreras med Azure Resource Manager-mallar. Det kan också användas med Azure CLI, Azure PowerShell, Azure-portalen eller REST API. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/extensions/custom-script-windows.md).

Om du vill se hur det anpassade skripttillägget fungerar, skapar du en skalningsuppsättning som installerar IIS-webbservern och matar ut värddatornamnet för skalningsuppsättningens virtuella datorinstans. Den anpassade skripttilläggsdefinition laddar ner ett exempelskript från GitHub, installerar nödvändiga paket och skriver sedan den virtuella datorinstansens värddatornamn till en grundläggande HTML-sida.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Skapa en VM-skalningsuppsättning med [New-AzVmss](/powershell/module/az.compute/new-azvmss). För att distribuera trafik till flera virtuella datorinstanser så skapas även en lastbalanserare. Lastbalanseraren innehåller regler för att distribuera trafik på TCP-port 80. Den tillåter även fjärrskrivbordstrafik på TCP-port 3389 och PowerShell-fjärrkommunikation på TCP-port 5985. När du uppmanas kan du ange dina egna administrativa autentiseringsuppgifter för de virtuella datorinstanserna i skalningsuppsättningen:

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="create-custom-script-extension-definition"></a>Skapa en anpassad skripttilläggsdefinition
Azure PowerShell använder en hash-tabell för att lagra filen som ska hämtas och kommandot som ska köras. I följande exempel används ett exempelskript från GitHub. Först skapar du det här konfigurationsobjektet:

```azurepowershell-interactive
$customConfig = @{
  "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}
```


Tillämpa det anpassade skripttillägget med [Add-AzVmssExtension](/powershell/module/az.Compute/Add-azVmssExtension). Konfigurationsobjektet som tidigare definierats skickas till tillägget. Uppdatera och kör tillägget på de virtuell datorinstanserna med [Update-AzVmss](/powershell/module/az.compute/update-azvmss).


```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
  -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.9 `
  -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Varje virtuell datorinstans i skalningsuppsättningen laddar ned och kör skriptet från GitHub. I ett mer avancerat exempel, kan flera programkomponenter och filer installeras. Om skalningsuppsättningen skalas upp, använder de nya virtuella datorinstanserna automatiskt samma definition för anpassade skripttillägg och installerar det program som krävs.


## <a name="allow-traffic-to-application"></a>Tillåta trafik till program

Om du vill tillåta åtkomst till den grundläggande webbappen måste du skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) och [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). Mer information finns i [Nätverk för skalningsuppsättningar för virtuella Azure-datorer](virtual-machine-scale-sets-networking.md).

```azurepowershell-interactive

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroup" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroup" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

```



## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill testa webbservern hämtar du den offentliga IP-adressen för lastbalanseraren med [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). I följande exempel visas den IP-adress som skapats i resursgruppen *myResourceGroup*:

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Skriv den offentliga IP-adressen för lastbalanseraren i en webbläsare. Lastbalanseraren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i IIS](media/tutorial-install-apps-powershell/running-iis.png)

Lämna webbläsaren öppen så att du kan se en uppdaterad version i nästa steg.


## <a name="update-app-deployment&quot;></a>Uppdatera appdistributionen
Under livscykeln för en skalningsuppsättning, kan du behöva distribuera en uppdaterad version av ditt program. Med det anpassade skripttillägget, kan du referera till ett uppdaterat distributionsskript och sedan tillämpa tillägget till din skalningsuppsättning igen. När skalningsuppsättningen skapades i ett föregående steg, angavs `-UpgradePolicyMode` som *automatiskt*. Den här inställningen låter virtuella datorinstanser i skalningsuppsättningen att automatiskt uppdatera och tillämpa den senaste versionen av ditt program.

Skapa en ny konfigurationsdefinition med namnet *customConfigv2*. Den här definitionen kör en uppdaterad *v2*-version av installationsskriptet för programmet:

```azurepowershell-interactive
$customConfigv2 = @{
  &quot;fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis-v2.ps1");
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis-v2.ps1"
}
```

Uppdatera konfigurationen av tillägget för anpassat skript till VM-instanserna i skalningsuppsättningen. Definitionen *customConfigv2.json* används för att tillämpa den uppdaterade versionen av programmet:

```azurepowershell-interactive
$vmss = Get-AzVmss `
          -ResourceGroupName "myResourceGroup" `
          -VMScaleSetName "myScaleSet"
 
$vmss.VirtualMachineProfile.ExtensionProfile[0].Extensions[0].Settings = $customConfigv2
 
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

Alla virtuella datorinstanser i skalningsuppsättningen uppdateras automatiskt med den senaste versionen av exempelwebbsidan. Uppdatera webbplatsen i webbläsaren om du vill se den uppdaterade versionen:

![Uppdaterad webbsida i IIS](media/tutorial-install-apps-powershell/running-iis-updated.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Parametern `-Force` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång. Parametern `-AsJob` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien, läste du hur du automatiskt installerar och uppdaterar program på din skalningsuppsättning med Azure PowerShell:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

Gå vidare till nästa självstudie för att lära dig hur du automatiskt skalar din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Skala dina skalningsuppsättningar automatiskt](tutorial-autoscale-powershell.md)
