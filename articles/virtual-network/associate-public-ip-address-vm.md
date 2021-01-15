---
title: Associera en offentlig IP-adress till en virtuell dator
titlesuffix: Azure Virtual Network
description: Lär dig att associera en offentlig IP-adress till en virtuell dator (VM) med hjälp av Azure Portal eller Azure CLI.
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
ms.openlocfilehash: 76f92b5da2331748fbbbfc68f1e456fd50dd71ee
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223031"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associera en offentlig IP-adress till en virtuell dator

I den här artikeln får du lära dig hur du associerar en offentlig IP-adress till en befintlig virtuell dator (VM). Om du vill ansluta till en virtuell dator från Internet måste den virtuella datorn ha en offentlig IP-adress som är kopplad till den. Om du vill skapa en ny virtuell dator med en offentlig IP-adress kan du göra det med hjälp av [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), [kommando rads gränssnittet för Azure (CLI)](virtual-network-deploy-static-pip-arm-cli.md)eller [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Offentliga IP-adresser har en nominell avgift. Mer information finns i [prissättning](https://azure.microsoft.com/pricing/details/ip-addresses/). Det finns en gräns för hur många offentliga IP-adresser som du kan använda per prenumeration. Mer information finns i [begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Du kan använda [Azure Portal](#azure-portal), Azures [kommando rads gränssnitt](#azure-cli) (CLI) eller [PowerShell](#powershell) för att associera en offentlig IP-adress till en virtuell dator.

## <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Bläddra till eller Sök efter den virtuella dator som du vill lägga till den offentliga IP-adressen till och välj sedan den.
3. Under **Inställningar** väljer du **nätverk** och väljer sedan det nätverks gränssnitt som du vill lägga till den offentliga IP-adressen till, som du ser i följande bild:

   ![Välj nätverks gränssnitt](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Offentliga IP-adresser är kopplade till nätverks gränssnitt som är anslutna till en virtuell dator. I föregående bild har den virtuella datorn bara ett nätverks gränssnitt. Om den virtuella datorn hade flera nätverks gränssnitt visas alla, och du väljer det nätverks gränssnitt som du vill associera den offentliga IP-adressen med.

4. Välj **IP-konfigurationer** och välj sedan en IP-konfiguration, som du ser i följande bild:

   ![Välj IP-konfiguration](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Offentliga IP-adresser är kopplade till IP-konfigurationer för ett nätverks gränssnitt. I föregående bild har nätverks gränssnittet en IP-konfiguration. Om nätverks gränssnittet har flera IP-konfigurationer visas alla i listan, och du väljer den IP-konfiguration som du vill associera den offentliga IP-adressen med.

5. Välj **aktive rad** och välj sedan **IP-adress (*Konfigurera nödvändiga inställningar*)**. Välj en befintlig offentlig IP-adress som automatiskt stänger rutan **Välj offentlig IP-adress** . Om du inte har några tillgängliga offentliga IP-adresser i listan, måste du skapa en. Mer information finns i [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Välj **Spara**, som du ser i bilden nedan, och stäng sedan rutan för IP-konfigurationen.

   ![Aktivera offentlig IP-adress](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > De offentliga IP-adresserna som visas är de som finns i samma region som den virtuella datorn. Om du har flera offentliga IP-adresser som skapats i regionen visas alla här. Om de är nedtonade beror det på att adressen redan är kopplad till en annan resurs.

6. Visa den offentliga IP-adress som tilldelats IP-konfigurationen, som du ser i bilden nedan. Det kan ta några sekunder innan en IP-adress visas.

   ![Visa tilldelad offentlig IP-adress](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Om du vill se en lista över adresspooler som används i varje region, se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den tilldelade adressen kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver adressen som ska tilldelas från en speciell pool i regionen använder du ett [offentligt IP](public-ip-address-prefix.md)-adressprefix.

7. [Tillåt nätverks trafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhets regler i en nätverks säkerhets grupp.

## <a name="azure-cli"></a>Azure CLI

Installera [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)eller Använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **prova** i CLI-kommandona som följer. Om du väljer **försök** anropas ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder CLI lokalt i bash loggar du in på Azure med `az login` .
2. En offentlig IP-adress är kopplad till en IP-konfiguration för ett nätverks gränssnitt som är kopplat till en virtuell dator. Använd kommandot [AZ Network NIC-IP-config Update](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-update) för att associera en offentlig IP-adress med en IP-konfiguration. I följande exempel associeras en befintlig offentlig IP-adress med namnet *myVMPublicIP* till IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverks gränssnitt med namnet *myVMVMNic* som finns i en resurs grupp med namnet *myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Om du inte har en befintlig offentlig IP-adress använder du kommandot [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) för att skapa ett. Följande kommando skapar till exempel en offentlig IP-adress med namnet *myVMPublicIP* i en resurs grupp med namnet *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kanske vill anpassa. Mer information om inställningar för offentliga IP-adresser finns i [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. Om du vill se en lista över adresspooler som används i varje region, se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

   - Om du inte känner till namnet på ett nätverks gränssnitt som är kopplat till den virtuella datorn använder du kommandot [AZ VM NIC List](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-list) för att visa dem. Följande kommando visar till exempel namnen på de nätverks gränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resurs grupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     Utdata innehåller en eller flera rader som liknar följande exempel:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     I det tidigare exemplet är *myVMVMNic* namnet på nätverks gränssnittet.

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverks gränssnitt använder du kommandot [AZ Network NIC IP-config List](/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-list) för att hämta dem. Följande kommando visar till exempel namnen på IP-konfigurationerna för ett nätverks gränssnitt med namnet *myVMVMNic* i en resurs grupp med namnet *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visa den offentliga IP-adress som tilldelats IP-konfigurationen med kommandot [AZ VM List-IP-addresss](/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) . I följande exempel visas de IP-adresser som har tilldelats till en befintlig virtuell dator med namnet *myVM* i en resurs grupp med namnet *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Om du vill se en lista över adresspooler som används i varje region, se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den tilldelade adressen kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver adressen som ska tilldelas från en speciell pool i regionen använder du ett [offentligt IP](public-ip-address-prefix.md)-adressprefix.

4. [Tillåt nätverks trafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhets regler i en nätverks säkerhets grupp.

## <a name="powershell"></a>PowerShell

Installera [PowerShell](/powershell/azure/install-az-ps)eller Använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt gränssnitt som du kan köra direkt i Azure-portalen. Den har PowerShell förinstallerat och konfigurerat för användning med ditt konto. Välj knappen **testa** i PowerShell-kommandona som följer. Om du väljer **försök** anropas ett Cloud Shell som du kan logga in på ditt Azure-konto med.

1. Om du använder PowerShell lokalt loggar du in på Azure med `Connect-AzAccount` .
2. En offentlig IP-adress är kopplad till en IP-konfiguration för ett nätverks gränssnitt som är kopplat till en virtuell dator. Använd kommandona [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) och [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) för att hämta det virtuella nätverk och undernät som nätverks gränssnittet finns i. Använd sedan kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta ett nätverks gränssnitt och kommandot [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att hämta en befintlig offentlig IP-adress. Använd sedan kommandot [set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) för att associera den offentliga IP-adressen med IP-konfigurationen och kommandot [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) för att skriva den nya IP-konfigurationen till nätverks gränssnittet.

   I följande exempel associeras en befintlig offentlig IP-adress med namnet *myVMPublicIP* till IP-konfigurationen med namnet *ipconfigmyVM* för ett befintligt nätverks gränssnitt med namnet *myVMVMNic* som finns i ett undernät med namnet *MyVMSubnet* i ett virtuellt nätverk med namnet *myVMVNet*. Alla resurser finns i en resurs grupp med namnet *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Om du inte har en befintlig offentlig IP-adress använder du kommandot [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) för att skapa en. Följande kommando skapar till exempel en *dynamisk* offentlig IP-adress med namnet *myVMPublicIP* i en resurs grupp med namnet *myResourceGroup* i regionen *östra* .
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Föregående kommando skapar en offentlig IP-adress med standardvärden för flera inställningar som du kanske vill anpassa. Mer information om inställningar för offentliga IP-adresser finns i [skapa en offentlig IP-adress](virtual-network-public-ip-address.md#create-a-public-ip-address). Adressen tilldelas från en pool med offentliga IP-adresser som används för varje Azure-region. Om du vill se en lista över adresspooler som används i varje region, se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

   - Om du inte känner till namnet på ett nätverks gränssnitt som är kopplat till den virtuella datorn använder du kommandot [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) för att visa dem. Följande kommando visar till exempel namnen på de nätverks gränssnitt som är kopplade till en virtuell dator med namnet *myVM* i en resurs grupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     Utdata innehåller en eller flera rader som liknar exemplet nedan. I exempel resultatet är *myVMVMNic* namnet på nätverks gränssnittet.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Om du inte känner till namnet på det virtuella nätverk eller undernät som nätverks gränssnittet finns i använder du `Get-AzNetworkInterface` kommandot för att visa informationen. Följande kommando hämtar till exempel information om virtuellt nätverk och undernät för ett nätverks gränssnitt med namnet *myVMVMNic* i en resurs grupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     Utdata innehåller en eller flera rader som liknar exemplet nedan. I exempel resultatet är *myVMVNET* namnet på det virtuella nätverket och *myVMSubnet* är namnet på under nätet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Om du inte känner till namnet på en IP-konfiguration för ett nätverks gränssnitt använder du kommandot [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) för att hämta dem. Följande kommando visar till exempel namnen på IP-konfigurationerna för ett nätverks gränssnitt med namnet *myVMVMNic* i en resurs grupp med namnet *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     Utdata innehåller en eller flera rader som liknar exemplet nedan. I exempel resultatet är *ipconfigmyVM* namnet på en IP-konfiguration.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visa den offentliga IP-adress som tilldelats IP-konfigurationen med kommandot [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) . I följande exempel visas adressen som har tilldelats till en offentlig IP-adress med namnet *myVMPublicIP* i en resurs grupp med namnet *myResourceGroup*.

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

   Utdata innehåller en eller flera rader som liknar exemplet nedan. I exempel resultatet är *myVMPublicIP* namnet på den offentliga IP-adress som tilldelats IP-konfigurationen.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > Adressen tilldelas från en pool med adresser som används i varje Azure-region. Om du vill se en lista över adresspooler som används i varje region, se [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den tilldelade adressen kan vara vilken adress som helst i de pooler som används för regionen. Om du behöver adressen som ska tilldelas från en speciell pool i regionen använder du ett [offentligt IP](public-ip-address-prefix.md)-adressprefix.

4. [Tillåt nätverks trafik till den virtuella datorn](#allow-network-traffic-to-the-vm) med säkerhets regler i en nätverks säkerhets grupp.

## <a name="allow-network-traffic-to-the-vm"></a>Tillåt nätverks trafik till den virtuella datorn

Innan du kan ansluta till den offentliga IP-adressen från Internet måste du se till att de nödvändiga portarna är öppna i alla nätverks säkerhets grupper som du kan ha kopplat till nätverks gränssnittet, under nätet som nätverks gränssnittet finns i eller båda. Även om säkerhets grupper filtrerar trafik till den privata IP-adressen för nätverks gränssnittet när inkommande Internet trafik anländer till den offentliga IP-adressen, översätter Azure den offentliga adressen till den privata IP-adressen, så om en nätverks säkerhets grupp förhindrar trafikflödet Miss lyckas kommunikationen med den offentliga IP-adressen. Du kan visa de effektiva säkerhets reglerna för ett nätverks gränssnitt och dess undernät med hjälp av [portalen](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)eller [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Nästa steg

Tillåt inkommande Internet trafik till den virtuella datorn med en nätverks säkerhets grupp. Information om hur du skapar en nätverks säkerhets grupp finns i [arbeta med nätverks säkerhets grupper](manage-network-security-group.md#work-with-network-security-groups). Mer information om nätverks säkerhets grupper finns i [säkerhets grupper](./network-security-groups-overview.md).