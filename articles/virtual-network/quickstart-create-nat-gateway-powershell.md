---
title: Skapa en NAT-gateway – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: Den här snabb starten visar hur du skapar en NAT-gateway med Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 54ec676da3311473f86a47aae600c2ec29192bb2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935127"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Självstudie: skapa en NAT-gateway med hjälp av Azure PowerShell

Den här självstudien visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan slutföra den här självstudien med Azure Cloud Shell eller köra kommandona lokalt.  [Logga in nu](https://shell.azure.com)om du inte har använt Azure Cloud Shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroupNAT** på **eastus2** -platsen:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

Offentliga IP-alternativ för NAT-gateway är:

* **Offentliga IP-adresser**
* **Offentliga IP-prefix**

Båda kan användas med NAT-gateway.

Vi ska lägga till en offentlig IP-adress och ett offentligt IP-prefix i det här scenariot för att demonstrera.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att få åtkomst till Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT**. Resultatet av det här kommandot kommer att lagras i en variabel **$publicIP** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

Använd [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) för att skapa en offentlig IP-prefixlängd som heter **myPublicIPprefix** i **myResourceGroupNAT**.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$publicIPPrefix** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gateway-resurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gateway-resursen:
  - En offentlig IP-pool och ett offentligt IP-prefix som används för utgående flöden som översätts av NAT gateway-resursen.
  - Ändra tids gränsen för inaktivitet från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure NAT-gateway med [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Resultatet av det här kommandot skapar en gateway-resurs med namnet **myNATgateway** som använder den offentliga IP- **myPublicIP** och det offentliga IP-prefixet **myPublicIPprefix**. Tids gränsen för inaktivitet har angetts till 10 minuter.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$natGateway** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

NAT-gatewayen är nu funktionell och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda det.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Skapa det virtuella nätverket och koppla under nätet till gatewayen.

Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet mina **undernät** med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i **myResourceGroup** med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Under nätet i det virtuella nätverket är **192.168.0.0/24**.  Resultatet av kommandon lagras i variabler med namnet **$Subnet** och **$VNet** för senare användning.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Skapa en virtuell dator för att använda tjänsten NAT

Nu ska vi skapa en virtuell dator för att använda NAT-tjänsten.  Den här virtuella datorn har en offentlig IP-adress som kan användas som en offentlig IP-adress på instans nivå för att få åtkomst till den virtuella datorn.  NAT-tjänsten är en flödes riktning som är medveten om och kommer att ersätta standard målet för Internet i ditt undernät. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

### <a name="create-public-ip-for-source-vm"></a>Skapa en offentlig IP-adress för den virtuella käll datorn

Vi skapar en offentlig IP-adress som ska användas för åtkomst till den virtuella datorn.  Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPVM** i **myResourceGroupNAT**.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$publicIpVM** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Skapa en NSG och exponera SSH-slutpunkten för den virtuella datorn

Standard offentliga IP-adresser är "säkra som standard", vi måste skapa en NSG för att tillåta inkommande åtkomst för SSH. Använd [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) för att skapa en NSG-resurs med namnet **myNSG**. Använd [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) för att skapa en NSG-regel för SSH-åtkomst med namnet **SSH** i **myResourceGroupNAT**.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$NSG** för senare användning.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Skapa NIC för virtuell dator

Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) med namnet **myNic**. Det här kommandot kopplar den offentliga IP-adressen och nätverks säkerhets gruppen. Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$NIC** för senare användning.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Skapa en virtuell dator

#### <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Använd ssh-keygen för att skapa ett SSH-nyckelpar.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Mer detaljerad information om hur du skapar SSH-nyckelpar, inklusive användning av PuTTy, finns i [Använd SSH-nycklar med Windows](../virtual-machines/linux/ssh-from-windows.md).

Om du skapar SSH-nyckelpar med hjälp av Cloud Shell, lagras nyckel paret i en behållar avbildning. Det här [lagrings kontot skapas automatiskt](../cloud-shell/persisting-shell-storage.md). Ta inte bort lagrings kontot eller fil resursen i förrän du har hämtat dina nycklar.

#### <a name="create-vm-configuration"></a>Skapa VM-konfiguration

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration med inställningar för avbildningen för användning, storlek och autentisering. Konfigurationen används för att bygga den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinera konfigurations definitionerna för att skapa en virtuell dator med namnet **myVM** med [New-AzVM](/powershell/module/az.compute/new-azvm) i **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Vänta tills den virtuella datorn förbereds för distribution och fortsätt sedan med resten av stegen.

## <a name="discover-the-ip-address-of-the-vm"></a>Identifiera IP-adressen för den virtuella datorn

Först måste vi identifiera IP-adressen för den virtuella dator som du har skapat. Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)för att hämta den offentliga IP-adressen för den virtuella datorn. 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den för att få åtkomst till den virtuella datorn.

### <a name="sign-in-to-vm"></a>Logga in på virtuell dator

SSH-autentiseringsuppgifterna ska lagras i Cloud Shell från föregående åtgärd.  Öppna en [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn.

```bash
ssh azureuser@<ip-address-destination>
```

Du är nu redo att använda NAT-tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en NAT-gateway och en virtuell dator för att använda den. 

Granska mått i Azure Monitor för att se hur NAT-tjänsten fungerar. Diagnostisera problem som resurs utbelastning av tillgängliga SNAT-portar.  Resurs utbelastningen på SNAT-portar åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix eller både och.


- Lär dig mer om [Azure Virtual Network NAT](./nat-overview.md)
- Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]
