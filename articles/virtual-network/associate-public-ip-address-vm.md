---
title: Associera en offentlig IP-adress med en virtuell dator
titlesuffix: Azure Virtual Network
description: Lär dig att associera en offentlig IP-adress med en virtuell dator (VM) med hjälp Azure Portal eller Azure CLI.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6e8fe92f88a5934c55febf42a0768274211ed76f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767717"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associera en offentlig IP-adress med en virtuell dator

I den här artikeln får du lära dig hur du associerar en offentlig IP-adress med en befintlig virtuell dator (VM). Om du vill ansluta till en virtuell dator från Internet måste den virtuella datorn ha en associerad offentlig IP-adress. Om du vill skapa en ny virtuell dator med en offentlig IP-adress kan du göra det med [hjälp av Azure Portal,](virtual-network-deploy-static-pip-arm-portal.md) [Azure-kommandoradsgränssnittet (CLI)](virtual-network-deploy-static-pip-arm-cli.md)eller [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Offentliga IP-adresser har en nominell avgift. Mer information finns i [prissättningen](https://azure.microsoft.com/pricing/details/ip-addresses/). Det finns en gräns för hur många offentliga IP-adresser som du kan använda per prenumeration. Mer information finns i [gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)

Du kan använda [Azure Portal,](#azure-portal)Azure-kommandoradsgränssnittet (CLI) eller [PowerShell](#powershell) för att associera en offentlig [IP-adress](#azure-cli) till en virtuell dator.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till eller sök efter den virtuella dator som du vill lägga till den offentliga IP-adressen till och välj den sedan.
3. Under **Inställningar** väljer du **Nätverk** och sedan det nätverksgränssnitt som du vill lägga till den offentliga IP-adressen till, enligt följande bild:

   ![Välj nätverksgränssnitt](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Offentliga IP-adresser är kopplade till nätverksgränssnitt som är anslutna till en virtuell dator. I föregående bild har den virtuella datorn bara ett nätverksgränssnitt. Om den virtuella datorn hade flera nätverksgränssnitt skulle alla visas och du skulle välja det nätverksgränssnitt som du vill koppla den offentliga IP-adressen till.

4. Välj **IP-konfigurationer** och välj sedan en IP-konfiguration, som du ser i följande bild:

   ![Välj IP-konfiguration](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Offentliga IP-adresser är associerade med IP-konfigurationer för ett nätverksgränssnitt. I föregående bild har nätverksgränssnittet en IP-konfiguration. Om nätverksgränssnittet har flera IP-konfigurationer visas alla i listan och du väljer den IP-konfiguration som du vill koppla den offentliga IP-adressen till.

5. Välj **Aktiverad** och sedan **IP-adress *(Konfigurera nödvändiga inställningar).*** Välj en befintlig offentlig IP-adress som automatiskt stänger rutan **Välj offentlig IP-adress.** Om du inte har några tillgängliga offentliga IP-adresser i listan måste du skapa en. Mer information finns i Skapa [en offentlig IP-adress.](virtual-network-public-ip-address.md#create-a-public-ip-address) Välj **Spara** enligt följande bild och stäng sedan rutan för IP-konfigurationen.

   ![Aktivera offentlig IP-adress](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > De offentliga IP-adresser som visas är de som finns i samma region som den virtuella datorn. Om du har flera offentliga IP-adresser som skapats i regionen visas alla här. Om någon är nedtonad beror det på att adressen redan är associerad med en annan resurs.

6. Visa den offentliga IP-adress som tilldelats IP-konfigurationen, som du ser i bilden nedan. Det kan ta några sekunder innan en IP-adress visas.

   ![Visa tilldelad offentlig IP-adress](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. En lista över adresspooler som används i varje region finns i Microsoft Azure [IP-intervall för datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) Den tilldelade adressen kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver tilldela adressen från en specifik pool i regionen använder du prefixet [Offentlig IP-adress.](public-ip-address-prefix.md)

7. [Tillåt nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en nätverkssäkerhetsgrupp.

## <a name="azure-cli"></a>Azure CLI

Installera [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **Prova i** de CLI-kommandon som följer. Om **du väljer** Prova anropas Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder CLI lokalt i Bash loggar du in på Azure med `az login` .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd kommandot [az network nic-ip-config update för](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) att associera en offentlig IP-adress till en IP-konfiguration. I följande exempel associeras en befintlig offentlig IP-adress med namnet *myVMPublicIP* med IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverksgränssnitt med namnet *myVMVMNic* som finns i en resursgrupp med namnet *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Om du inte har en befintlig offentlig IP-adress använder du kommandot [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) för att skapa en. Följande kommando skapar till exempel en offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kanske vill anpassa. Mer information om alla inställningar för offentliga IP-adresser finns i [Skapa en offentlig IP-adress.](virtual-network-public-ip-address.md#create-a-public-ip-address) Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. En lista över adresspooler som används i varje region finns i Microsoft Azure [IP-intervall för datacenter.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) för att visa dem. Följande kommando visar till exempel namnen på nätverksgränssnitten som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I föregående exempel är *myVMVMNic* namnet på nätverksgränssnittet.

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) för att hämta dem. Följande kommando listar till exempel namnen på IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visa den offentliga IP-adress som tilldelats IP-konfigurationen med kommandot [az vm list-ip-addresses.](/cli/azure/vm#az_vm_list_ip_addresses) I följande exempel visas IP-adresserna som tilldelats till en befintlig virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. En lista över adresspooler som används i varje region finns i Microsoft Azure [IP-intervall för datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) Den tilldelade adressen kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver tilldela adressen från en specifik pool i regionen använder du prefixet [Offentlig IP-adress.](public-ip-address-prefix.md)

4. [Tillåt nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en nätverkssäkerhetsgrupp.

## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och konfigurerat för användning med ditt konto. Välj knappen **Prova i** de PowerShell-kommandon som följer. Om **du väljer** Testa anropas Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder PowerShell lokalt loggar du in på Azure med `Connect-AzAccount` .
2. En offentlig IP-adress är associerad med en IP-konfiguration av ett nätverksgränssnitt som är kopplat till en virtuell dator. Använd [kommandona Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) och [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) för att hämta det virtuella nätverket och undernätet som nätverksgränssnittet finns i. Använd sedan kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta ett nätverksgränssnitt och kommandot [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att hämta en befintlig offentlig IP-adress. Använd sedan kommandot [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) för att associera den offentliga IP-adressen med IP-konfigurationen och kommandot [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) för att skriva den nya IP-konfigurationen till nätverksgränssnittet.

   I följande exempel associeras en befintlig offentlig IP-adress med namnet *myVMPublicIP* med IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverksgränssnitt med namnet *myVMVMNic* som finns i ett undernät med namnet *myVMSubnet* i ett virtuellt nätverk med namnet *myVMVNet.* Alla resurser finns i en resursgrupp med namnet *myResourceGroup.*
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Om du inte har en befintlig offentlig IP-adress använder du kommandot [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) för att skapa en. Följande kommando skapar till  exempel en dynamisk offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup* i *regionen eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kanske vill anpassa. Mer information om alla inställningar för offentliga IP-adresser finns i [Skapa en offentlig IP-adress.](virtual-network-public-ip-address.md#create-a-public-ip-address) Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. En lista över adresspooler som används i varje region finns i Microsoft Azure [IP-intervall för datacenter.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Om du inte känner till namnet på ett nätverksgränssnitt som är kopplat till den virtuella datorn använder du kommandot [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att visa dem. Följande kommando visar till exempel namnen på nätverksgränssnitten som är kopplade till en virtuell dator med namnet *myVM* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I exempelutdata *är myVMVMNic* namnet på nätverksgränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte vet namnet på det virtuella nätverket eller undernätet som nätverksgränssnittet finns i kan du använda kommandot `Get-AzNetworkInterface` för att visa informationen. Följande kommando hämtar till exempel information om det virtuella nätverket och undernätet för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I exempelutdata *är myVMVNET* namnet på det virtuella nätverket och *myVMSubnet* är namnet på undernätet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverksgränssnitt använder du kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta dem. Följande kommando listar till exempel namnen på IP-konfigurationerna för ett nätverksgränssnitt med namnet *myVMVMNic* i en resursgrupp med namnet *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel. I exempelutdata är *ipconfigmyVM* namnet på en IP-konfiguration.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visa den offentliga IP-adress som tilldelats IP-konfigurationen med kommandot [Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) I följande exempel visas adressen som tilldelats till en offentlig IP-adress med namnet *myVMPublicIP* i en resursgrupp med namnet *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Om du inte känner till namnet på den offentliga IP-adress som tilldelats till en IP-konfiguration kör du följande kommandon för att hämta den:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   Utdata innehåller en eller flera rader som liknar följande exempel. I exempelutdata är *myVMPublicIP* namnet på den offentliga IP-adress som tilldelats IP-konfigurationen.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. En lista över adresspooler som används i varje region finns i Microsoft Azure [IP-intervall för datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) Den tilldelade adressen kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver tilldela adressen från en specifik pool i regionen använder du prefixet [Offentlig IP-adress.](public-ip-address-prefix.md)

4. [Tillåt nätverkstrafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhetsregler i en nätverkssäkerhetsgrupp.

## <a name="allow-network-traffic-to-the-vm"></a>Tillåt nätverkstrafik till den virtuella datorn

Innan du kan ansluta till den offentliga IP-adressen från Internet måste du se till att alla nödvändiga portar är öppna i alla nätverkssäkerhetsgrupp som du kan ha kopplat till nätverksgränssnittet, det undernät som nätverksgränssnittet finns i eller båda. Även om säkerhetsgrupper filtrerar trafik till nätverksgränssnittens privata IP-adress, så när inkommande Internettrafik kommer till den offentliga IP-adressen översätter Azure den offentliga adressen till den privata IP-adressen, så om en nätverkssäkerhetsgrupp förhindrar trafikflödet misslyckas kommunikationen med den offentliga IP-adressen. Du kan visa de gällande säkerhetsreglerna för ett nätverksgränssnitt och dess undernät med hjälp [av portalen,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal) [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)eller [PowerShell.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Nästa steg

Tillåt inkommande Internettrafik till den virtuella datorn med en nätverkssäkerhetsgrupp. Information om hur du skapar en nätverkssäkerhetsgrupp finns i [Arbeta med nätverkssäkerhetsgrupper.](manage-network-security-group.md#work-with-network-security-groups) Mer information om nätverkssäkerhetsgrupper finns i [Säkerhetsgrupper.](./network-security-groups-overview.md)
