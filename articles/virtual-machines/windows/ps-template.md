---
title: Skapa en virtuell Windows-dator från en mall i Azure
description: Använd en Resource Manager-mall och PowerShell för att enkelt skapa en ny virtuell Windows-dator.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 534a8480e783853f7497a0538b04e0302a9cda0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102553620"
---
# <a name="create-a-windows-virtual-machine-from-a-resource-manager-template"></a>Skapa en virtuell Windows-dator från en Resource Manager-mall

Lär dig hur du skapar en virtuell Windows-dator med hjälp av en Azure Resource Manager-mall och Azure PowerShell från Azure Cloud Shell. Mallen som används i den här artikeln distribuerar en enskild virtuell dator som kör Windows Server i ett nytt virtuellt nätverk med ett enda undernät. Information om hur du skapar en virtuell Linux-dator finns i [så här skapar du en virtuell Linux-dator med Azure Resource Manager mallar](../linux/create-ssh-secured-vm-from-template.md).

Ett alternativ är att distribuera mallen från Azure Portal. Öppna mallen i portalen genom att välja knappen **distribuera till Azure** .

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json)

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Att skapa en virtuell Azure-dator innehåller vanligt vis två steg:

- Skapa en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn.
- Skapa en virtuell dator.

I följande exempel skapas en virtuell dator från en [Azure snabb starts mall](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json). Här är en kopia av mallen:

[!code-json[create-windows-vm](~/quickstart-templates/101-vm-simple-windows/azuredeploy.json)]

Om du vill köra PowerShell-skriptet väljer du **prova** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på gränssnittet och väljer **Klistra in**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter an unique DNS name for the public IP"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json" `
    -adminUsername $adminUsername `
    -adminPassword $adminPassword `
    -dnsLabelPrefix $dnsLabelPrefix

 (Get-AzVm -ResourceGroupName $resourceGroupName).name

```

Om du väljer att installera och använda PowerShell lokalt i stället för från Azure Cloud Shell, kräver den här självstudien Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

I föregående exempel angav du en mall som lagrats i GitHub. Du kan också hämta eller skapa en mall och ange den lokala sökvägen med `--template-file` parametern.

Här följer några ytterligare resurser:

- Information om hur du utvecklar Resource Manager-mallar finns i [Azure Resource Manager-dokumentation](../../azure-resource-manager/index.yml).
- Information om Azures virtuella dator scheman finns i [referens för Azure-mallar](/azure/templates/microsoft.compute/allversions).
- För att se fler exempel på mallar för virtuella datorer, se [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-the-virtual-machine"></a>Ansluta till den virtuella datorn

Det sista PowerShell-kommandot från det föregående skriptet visar namnet på den virtuella datorn. Information om hur du ansluter till den virtuella datorn finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](./connect-logon.md).

## <a name="next-steps"></a>Nästa steg

- Om det uppstod problem med distributionen kan du ta en titt på [Felsök vanliga fel i Azure-distribution med Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md).
- Lär dig hur du skapar och hanterar en virtuell dator i [skapa och hantera virtuella Windows-datorer med Azure PowerShell-modulen](tutorial-manage-vm.md).

Om du vill veta mer om hur du skapar mallar kan du Visa JSON-syntaxen och egenskaperna för de resurs typer som du har distribuerat:

- [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
