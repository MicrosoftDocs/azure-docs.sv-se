---
title: Skapa en virtuell dator och ett lagrings konto för ett skalbart program i Azure
description: Lär dig hur du distribuerar en virtuell dator som ska användas för att köra ett skalbart program med Azure Blob Storage
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4b8c52b03cb6dec6096565e9eac26b7b2c4a30e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89073275"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Skapa en virtuell dator och ett lagringskonto för ett skalbart program

Den här självstudien ingår i en serie. Den här självstudien visar hur du distribuerar ett program som överför och laddar ner stora mängder slumpmässiga data med ett Azure-lagringskonto. När du är klar har du ett konsolprogram som körs på en virtuell dator där du laddar upp och laddar ner stora mängder data till ett lagringskonto.

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Skapa en virtuell dator
> * Konfigurera ett anpassat skripttillägg

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modulens Az-version 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto
 
I exemplet laddas 50 stora filer upp till en blobcontainer på ett Azure Storage-konto. Ett Azure-lagringskonto tillhandahåller en unik namnrymd där du kan lagra och få åtkomst till dina Azure-lagringdataobjekt. Skapa ett lagringskonto i resursgruppen som du skapade med kommandot [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount).

I följande kommando infogar du ditt globalt unika lagringskontonamn på blobblagringskontot istället för platshållaren `<blob_storage_account>`.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell datorkonfiguration. Den här konfigurationen innehåller de inställningar som används vid distribution av den virtuella datorn, t.ex. den virtuella datorns avbildning, storlek och konfiguration för verifiering. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

Skapa den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Distribuera konfigurationen

I den här självstudien finns det förutsättningar för vad som måste installeras på den virtuella datorn. Det anpassade skripttillägget används för att köra ett PowerShell-skript som utför följande uppgifter:

> [!div class="checklist"]
> * Installera .NET Core 2.0
> * Installera Chocolatey
> * Installera GIT
> * Klona lagringsplatsexemplet
> * Återställ NuGet-paketen
> * Skapa 50 stycken 1 GB-filer med slumpmässiga data

Kör följande cmdlet för att slutföra konfigurationen av den virtuella datorn. Det här steget tar 5 till 15 minuter att slutföra.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Nästa steg

I del ett av serien lärde du dig att skapa ett lagringskonto, distribuera en virtuell dator och konfigurera den virtuella datorn med nödvändiga förutsättningar, till exempel:

> [!div class="checklist"]
> * Skapa ett lagringskonto
> * Skapa en virtuell dator
> * Konfigurera ett anpassat skripttillägg

Gå till del två i serien för att ladda upp stora mängder data till ett lagringskonto med exponentiellt återförsök och parallellitet.

> [!div class="nextstepaction"]
> [Överföra stora mängder av stora filer parallellt till ett lagringskonto](storage-blob-scalable-app-upload-files.md)
