---
title: Koppla bort en offentlig IP-adress från en virtuell Azure-dator
titlesuffix: Azure Virtual Network
description: Lär dig hur du kopplar från en offentlig IP-adress från en virtuell dator
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98934196"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Koppla bort en offentlig IP-adress från en virtuell Azure-dator 

I den här artikeln får du lära dig hur du kopplar från en offentlig IP-adress från en virtuell Azure-dator (VM).

Du kan använda [Azure Portal](#azure-portal), Azures [kommando rads gränssnitt](#azure-cli) (CLI) eller [PowerShell](#powershell) för att koppla bort en offentlig IP-adress från en virtuell dator.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till eller Sök efter den virtuella dator som du vill koppla från den offentliga IP-adressen från och välj sedan den.
3. På sidan VM väljer du **Översikt** och väljer den offentliga IP-adressen som visas på följande bild:

   ![Välj offentlig IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. På sidan offentlig IP-adress väljer du **Översikt** och väljer sedan ta **bort, som du ser** i följande bild:

    ![Koppla bort offentlig IP](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. I ta bort **offentlig IP-adress** väljer du **Ja**.

## <a name="azure-cli"></a>Azure CLI

Installera [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)eller Använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **prova** i CLI-kommandona som följer. Om du väljer **försök** anropas ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder CLI lokalt i bash loggar du in på Azure med `az login` .
2. En offentlig IP-adress är kopplad till en IP-konfiguration för ett nätverks gränssnitt som är kopplat till en virtuell dator. Använd kommandot [AZ Network NIC-IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) för att koppla bort en offentlig IP-adress från en IP-konfiguration. I följande exempel kopplas en offentlig IP-adress med namnet *myVMPublicIP* från IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverks gränssnitt med namnet *myVMVMNic* som är kopplat till en virtuell dator med namnet *MyVM* i en resurs grupp med namnet *myResourceGroup*.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Om du inte känner till namnet på ett nätverks gränssnitt som är kopplat till den virtuella datorn använder du kommandot [AZ VM NIC List](/cli/azure/vm/nic#az-vm-nic-list) för att visa dem. Följande kommando visar till exempel namnen på de nätverks gränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resurs grupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I det tidigare exemplet är *myVMVMNic* namnet på nätverks gränssnittet.

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverks gränssnitt använder du kommandot [AZ Network NIC IP-config List](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) för att hämta dem. Följande kommando visar till exempel namnen på offentliga IP-konfigurationer för ett nätverks gränssnitt med namnet *myVMVMNic* i en resurs grupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Om du inte känner till namnet på en offentlig IP-konfiguration för ett nätverks gränssnitt använder du kommandot [AZ Network NIC IP-config show](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) för att hämta dem. Följande kommando visar till exempel namnen på offentliga IP-konfigurationer för ett nätverks gränssnitt med namnet *myVMVMNic* i en resurs grupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps)eller Använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och konfigurerat för användning med ditt konto. Välj knappen **testa** i PowerShell-kommandona som följer. Om du väljer **försök** anropas ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder PowerShell lokalt loggar du in på Azure med `Connect-AzAccount` .
2. En offentlig IP-adress är kopplad till en IP-konfiguration för ett nätverks gränssnitt som är kopplat till en virtuell dator. Använd kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta ett nätverks gränssnitt. Ange värdet null för den offentliga IP-adressen och Använd sedan kommandot [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) för att skriva den nya IP-konfigurationen till nätverks gränssnittet.

   I följande exempel kopplas en offentlig IP-adress med namnet *myVMPublicIP* från ett nätverks gränssnitt med namnet *myVMVMNic* som är kopplat till en virtuell dator med namnet *myVM*. Alla resurser finns i en resurs grupp med namnet *myResourceGroup*.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Om du inte känner till namnet på ett nätverks gränssnitt som är kopplat till den virtuella datorn använder du kommandot [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att visa dem. Följande kommando visar till exempel namnen på de nätverks gränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resurs grupp med namnet *myResourceGroup*:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Utdata innehåller en eller flera rader som liknar exemplet nedan. I exempel resultatet är *myVMVMNic* namnet på nätverks gränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverks gränssnitt använder du kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta dem. Följande kommando visar till exempel namnen på IP-konfigurationerna för ett nätverks gränssnitt med namnet *myVMVMNic* i en resurs grupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Utdata innehåller en eller flera rader som liknar exemplet nedan. I exempel resultatet är *ipconfigmyVM* namnet på en IP-konfiguration.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [associerar en offentlig IP-adress till en virtuell dator](associate-public-ip-address-vm.md).
