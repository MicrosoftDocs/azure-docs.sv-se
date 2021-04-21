---
title: Ta bort en offentlig IP-adress från en virtuell Azure-dator
titlesuffix: Azure Virtual Network
description: Lär dig hur du tar bort en offentlig IP-adress från en virtuell dator
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
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776548"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Ta bort en offentlig IP-adress från en virtuell Azure-dator 

I den här artikeln får du lära dig att ta bort en offentlig IP-adress från en virtuell Azure-dator (VM).

Du kan använda [Azure Portal,](#azure-portal)Azure-kommandoradsgränssnittet (CLI) eller [PowerShell](#powershell) för att ta bort en offentlig [IP-adress](#azure-cli) från en virtuell dator.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till eller sök efter den virtuella dator som du vill avassociera den offentliga IP-adressen från och välj den sedan.
3. På sidan för den virtuella datorn **väljer du** Översikt och väljer den offentliga IP-adressen enligt följande bild:

   ![Välj Offentlig IP](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. På sidan för offentlig IP-adress **väljer du Översikt** och väljer sedan Ta bort **,** som du ser i följande bild:

    ![Ta bort offentlig IP-adress](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. I **Inaktivera offentlig IP-adress väljer** du **Ja.**

## <a name="azure-cli"></a>Azure CLI

Installera [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **Prova i** de CLI-kommandon som följer. Om **du väljer** Testa anropas Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder CLI lokalt i Bash loggar du in på Azure med `az login` .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd kommandot [az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) för att ta bort en offentlig IP-adress från en IP-konfiguration. I följande exempel kopplas en offentlig IP-adress med namnet *myVMPublicIP* bort från IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverksgränssnitt med namnet *myVMVMNic* som är kopplat till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup.*
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) för att visa dem. Följande kommando visar till exempel namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I föregående exempel är *myVMVMNic* namnet på nätverksgränssnittet.

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) för att hämta dem. Följande kommando listar till exempel namnen på de offentliga IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Om du inte känner till namnet på en offentlig IP-konfiguration för ett nätverksgränssnitt använder du kommandot [az network nic ip-config show](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) för att hämta dem. Följande kommando listar till exempel namnen på de offentliga IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och konfigurerat för användning med ditt konto. Välj knappen **Prova i** de PowerShell-kommandon som följer. Om **du väljer** Prova anropas Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder PowerShell lokalt loggar du in på Azure med `Connect-AzAccount` .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd kommandot [Get-AzNetworkInterface för](/powershell/module/Az.Network/Get-AzNetworkInterface) att hämta ett nätverksgränssnitt. Ange värdet för Offentlig IP-adress till null och använd sedan kommandot [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) för att skriva den nya IP-konfigurationen till nätverksgränssnittet.

   I följande exempel kopplas en offentlig IP-adress med namnet *myVMPublicIP* från ett nätverksgränssnitt med namnet *myVMVMNic* som är kopplat till en virtuell dator med namnet *myVM*. Alla resurser finns i en resursgrupp med namnet *myResourceGroup.*
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att visa dem. Följande kommando visar till exempel namnen på de nätverksgränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I exempelutdata *är myVMVMNic* namnet på nätverksgränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta dem. Följande kommando listar till exempel namnen på IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I exempelutdata är *ipconfigmyVM* namnet på en IP-konfiguration.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [associerar en offentlig IP-adress till en virtuell dator](associate-public-ip-address-vm.md).
