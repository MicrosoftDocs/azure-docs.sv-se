---
title: Självstudie – installera program på en virtuell Windows-dator i Azure
description: I kursen får du lära dig hur du använder tillägget för anpassat skript för att köra skript och distribuera appar till Windows-datorer i Azure
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c7c0fc8aaa3779390060ae283f1427f4955ed421
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555983"
---
# <a name="tutorial---deploy-applications-to-a-windows-virtual-machine-in-azure-with-the-custom-script-extension"></a>Självstudiekurs – Distribuera appar till en Windows-dator i Azure med tillägget för anpassat skript

För att konfigurera virtuella datorer (VM) på ett snabbt och konsekvent sätt kan du använda [Anpassat skripttillägg för Windows](../extensions/custom-script-windows.md). I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Använd det anpassade skripttillägget till att installera IIS
> * Skapa en virtuell dator som använder det anpassade skripttillägget
> * Visa en IIS-webbplats efter att tillägget har tillämpats

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="custom-script-extension-overview"></a>Översikt över Anpassat skripttillägg
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning.

Det anpassade skripttillägget är integrerat med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller REST API:n för virtuella Azure-datorer.

Du kan använda det anpassade skripttillägget med både Windows och Linux virtuella datorer.


## <a name="create-virtual-machine"></a>Skapa en virtuell dator
Ange ett administratörsanvändarnamn och -lösenord för den virtuella datorn med hjälp av [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Nu kan du skapa den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVM* på platsen *EastUS* (Östra USA). Resursgruppen *myResourceGroupAutomate* och stödjande nätverksresurser skapas om de inte redan finns. För att tillåta webbtrafik öppnar cmdleten även port *80*.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Credential $cred
```

Det tar några minuter att skapa resurserna och den virtuella datorn.


## <a name="automate-iis-install"></a>Automatisera installationen av IIS
Använd [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) för att installera Anpassat skripttillägg. Tillägget kör `powershell Add-WindowsFeature Web-Server` för att installera IIS-webbservern och uppdaterar sedan sidan *Default.htm* till att visa värddatornamnet för den virtuella datorn:

```azurepowershell-interactive
Set-AzVMExtension -ResourceGroupName "myResourceGroupAutomate" `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location "EastUS" `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
```


## <a name="test-web-site"></a>Testwebbplats
Hämta den offentliga IP-adressen för lastbalanseraren med hjälp av [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). I följande exempel hämtas IP-adressen för *myPublicIPAddress* som skapades tidigare:

```azurepowershell-interactive
Get-AzPublicIPAddress `
    -ResourceGroupName "myResourceGroupAutomate" `
    -Name "myPublicIPAddress" | select IpAddress
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Webbplatsen visas, inklusive värddatornamnet för den virtuella dator som lastbalanseraren distribuerade trafik till, som i följande exempel:

![Köra IIS-webbplatsen](./media/tutorial-automate-vm-deployment/running-iis-website.png)


## <a name="next-steps"></a>Nästa steg

I den här självstudien kommer du att automatisera installationen av IIS på en virtuell dator. Du har lärt dig att:

> [!div class="checklist"]
> * Använd det anpassade skripttillägget till att installera IIS
> * Skapa en virtuell dator som använder det anpassade skripttillägget
> * Visa en IIS-webbplats efter att tillägget har tillämpats

Gå vidare till nästa självstudie där du får lära dig att skapa anpassade avbildningar för virtuella datorer.

> [!div class="nextstepaction"]
> [Skapa anpassade avbildningar av en virtuell dator](./tutorial-custom-images.md)
