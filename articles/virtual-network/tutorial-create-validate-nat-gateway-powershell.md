---
title: Skapa och testa en NAT-gateway – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: Den här självstudien visar hur du skapar en NAT-gateway med hjälp av Azure PowerShell och testar NAT-tjänsten
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: f319e77bd4801a2844a54ba8eba955c2b062fe7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934283"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Självstudie: skapa en NAT-gateway med Azure PowerShell och testa NAT-tjänsten

I den här självstudien skapar du en NAT-gateway för att tillhandahålla utgående anslutning för virtuella datorer i Azure. Om du vill testa NAT-gatewayen distribuerar du en virtuell käll-och mål dator. Du testar NAT-gatewayen genom att göra utgående anslutningar till en offentlig IP-adress. Anslutningarna kommer från källan till den virtuella mål datorn. Den här självstudien distribuerar källa och mål i två olika virtuella nätverk i samma resurs grupp för enkelhetens skull.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan slutföra den här självstudien med Azure Cloud Shell eller köra respektive kommandon lokalt.  Om du aldrig har använt Azure Cloud Shell bör du [Logga in nu](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroupNAT** på **eastus2** -platsen:


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att få åtkomst till Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPsource** i **myResourceGroupNAT**. Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$publicIPsource** för senare användning.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

 Använd [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) för att skapa en offentlig IP-prefixlängd som heter **myPublicIPprefixsource** i **myResourceGroupNAT**.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$publicIPPrefixsource** för senare användning.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gateway-resurs

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gateway-resursen:
  - En offentlig IP-pool och ett offentligt IP-prefix som används för utgående flöden som översätts av NAT gateway-resursen.
  - Ändra tids gränsen för inaktivitet från standardvärdet 4 minuter till 10 minuter.

Skapa en global Azure NAT-gateway med [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Resultatet av det här kommandot skapar en gateway-resurs med namnet **myNATgateway** som använder den offentliga IP- **myPublicIPsource** och det offentliga IP-prefixet **myPublicIPprefixsource**. Tids gränsen för inaktivitet har angetts till 10 minuter.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$natGateway** för senare användning.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

NAT-gatewayen är nu funktionell och allt som saknas är att konfigurera vilka undernät i ett virtuellt nätverk som ska använda det.

## <a name="prepare-the-source-for-outbound-traffic"></a>Förbered källan för utgående trafik

Vi vägleder dig genom installationen av en fullständig test miljö. Du måste konfigurera ett test med hjälp av verktyg med öppen källkod för att verifiera NAT-gatewayen. Vi börjar med källan, som kommer att använda NAT-gatewayen som vi skapade tidigare.

### <a name="configure-virtual-network-for-source"></a>Konfigurera virtuellt nätverk för källa

Skapa det virtuella nätverket och koppla under nätet till gatewayen.

Skapa ett virtuellt nätverk med namnet **myVnetsource** med ett undernät med namnet **mySubnetsource** med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i **myResourceGroupNAT** med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Under nätet i det virtuella nätverket är **192.168.0.0/24**.  Resultatet av kommandon lagras i variabler med namnet **$subnetsource** och **$vnetsource** för senare användning.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.

Innan vi kan testa NAT-gatewayen måste vi skapa en virtuell käll dator.  Vi tilldelar en offentlig IP-adressresurs som en offentlig IP-adress på instans nivå för att få åtkomst till den här virtuella datorn från utsidan. Den här adressen används bara för att komma åt den för testet.  Vi visar hur NAT-tjänsten prioriteras över andra utgående alternativ.

Du kan också skapa den här virtuella datorn utan en offentlig IP-adress och skapa en annan virtuell dator som du kan använda som en hoppning utan en offentlig IP-adress som en övning.

### <a name="create-public-ip-for-source-vm"></a>Skapa en offentlig IP-adress för den virtuella käll datorn

Vi skapar en offentlig IP-adress som ska användas för åtkomst till den virtuella datorn.  Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPVM** i **myResourceGroupNAT**.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$publicIpsourceVM** för senare användning.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Skapa en NSG och exponera SSH-slutpunkten för den virtuella datorn

Eftersom standard-offentliga IP-adresser är "säkra som standard", skapar vi en NSG för att tillåta inkommande åtkomst för SSH. NAT-tjänsten är en flödes riktning som är medveten om. Den här NSG används inte för utgående trafik när NAT-gateway har kon figurer ATS i samma undernät. Använd [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) för att skapa en NSG-resurs med namnet **myNSGsource**. Använd [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) för att skapa en NSG-regel för SSH-åtkomst med namnet **SSH** i **myResourceGroupNAT**. Resultatet av det här kommandot kommer att lagras i variabeln med namnet **$nsgsource** för senare användning.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>Skapa ett nätverkskort för den virtuella käll datorn

Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) med namnet **myNicsource**. Det här kommandot kommer att associera den offentliga IP-adressen och nätverks säkerhets gruppen. Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$nicsource** för senare användning.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>Skapa en virtuell käll dator

#### <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Du behöver ett SSH-nyckelpar för att slutföra den här snabbstarten. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.

Använd ssh-keygen för att skapa ett SSH-nyckelpar.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
Mer detaljerad information om hur du skapar SSH-nyckelpar, inklusive användning av PuTTy, finns i [Använd SSH-nycklar med Windows](../virtual-machines/linux/ssh-from-windows.md).

Om du skapar SSH-nyckelpar med hjälp av Cloud Shell, lagras nyckel paret i en behållar avbildning. Det här [lagrings kontot skapas automatiskt](../cloud-shell/persisting-shell-storage.md). Ta inte bort lagrings kontot eller fil resursen i förrän du har hämtat dina nycklar.

#### <a name="create-vm-configuration"></a>Skapa VM-konfiguration

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration som har inställningar som avbildningen för alternativ för användning, storlek och autentisering. Konfigurationen används sedan till att skapa den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinera konfigurations definitionerna för att skapa en virtuell dator med namnet **myVMsource** med [New-AzVM](/powershell/module/az.compute/new-azvm) i **myResourceGroupNAT**.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

Kommandot kommer att returneras omedelbart, men det kan ta några minuter innan den virtuella datorn har distribuerats.

## <a name="prepare-destination-for-outbound-traffic"></a>Förbered mål för utgående trafik

Nu ska vi skapa ett mål för den utgående trafik som översätts av NAT-tjänsten så att du kan testa den.

### <a name="configure-virtual-network-for-destination"></a>Konfigurera virtuellt nätverk för mål

Vi måste skapa ett virtuellt nätverk där den virtuella mål datorn kommer att vara.  Dessa kommandon är samma steg som för den virtuella käll datorn. Små ändringar har lagts till för att exponera mål slut punkten.

Skapa ett virtuellt nätverk med namnet **myVnetdestination** med ett undernät med namnet **mySubnetdestination** med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i **myResourceGroupNAT** med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). IP-adressutrymmet för det virtuella nätverket är **192.168.0.0/16**. Under nätet i det virtuella nätverket är **192.168.0.0/24**.  Resultatet av kommandon lagras i variabler med namnet **$subnetdestination** och **$vnetdestination** för senare användning.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>Skapa en offentlig IP-adress för den virtuella mål datorn

Vi skapar en offentlig IP-adress som ska användas för åtkomst till den virtuella mål datorn.  Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adressresurs med namnet **myPublicIPdestinationVM** i **myResourceGroupNAT**.  Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$publicIpdestinationVM** för senare användning.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Skapa en NSG och exponera SSH-och HTTP-slutpunkt för virtuell dator

Offentliga standard-IP-adresser är "säkra som standard", vi skapar en NSG för att tillåta inkommande åtkomst för SSH. Använd [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) för att skapa en NSG-resurs med namnet **myNSGdestination**. Använd [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) för att skapa en NSG-regel för SSH-åtkomst med namnet **SSH**.  Använd [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) för att skapa en NSG-regel för HTTP-åtkomst med namnet **http**. Båda reglerna kommer att skapas i **myResourceGroupNAT**. Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$nsgdestination** för senare användning.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>Skapa ett nätverkskort för den virtuella mål datorn

Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) med namnet **myNicdestination**. Det här kommandot kommer att associeras med den offentliga IP-adressen och nätverks säkerhets gruppen. Resultatet av det här kommandot kommer att lagras i en variabel med namnet **$nicdestination** för senare användning.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>Skapa en virtuell måldator

#### <a name="create-vm-configuration"></a>Skapa VM-konfiguration

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration som har inställningar som avbildningen för alternativ för användning, storlek och autentisering. Konfigurationen används sedan till att skapa den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i ~/.ssh/id_rsa. pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinera konfigurations definitionerna för att skapa en virtuell dator med namnet **myVMdestination** med [New-AzVM](/powershell/module/az.compute/new-azvm) i **myResourceGroupNAT**.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

Kommandot kommer att returneras omedelbart, men det kan ta några minuter innan den virtuella datorn har distribuerats.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Förbered en webb server och testa nytto lasten på den virtuella mål datorn

Först måste vi identifiera IP-adressen för den virtuella mål datorn.  Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)för att hämta den offentliga IP-adressen för den virtuella datorn. 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den i efterföljande steg. Ange det här är den virtuella mål datorn.

### <a name="sign-in-to-destination-vm"></a>Logga in på den virtuella mål datorn

SSH-autentiseringsuppgifterna ska lagras i Cloud Shell från föregående åtgärd.  Öppna en [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn. 

```azurepowershell-interactive
ssh azureuser@$destip

```

Kopiera och klistra in följande kommandon när du har loggat in.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

De här kommandona kommer att uppdatera den virtuella datorn, installera nginx och skapa en 100-KByte-fil. Den här filen kommer att hämtas från den virtuella käll datorn med hjälp av NAT-tjänsten.

Stäng SSH-sessionen med den virtuella mål datorn.

## <a name="prepare-test-on-source-vm"></a>Förbered testet på den virtuella käll datorn

Först måste vi identifiera IP-adressen för den virtuella käll datorn.  Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)för att hämta den offentliga IP-adressen för den virtuella datorn. 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den i efterföljande steg. Ange det här är den virtuella käll datorn.

### <a name="log-into-source-vm"></a>Logga in på den virtuella käll datorn

SSH-autentiseringsuppgifterna lagras på nytt i Cloud Shell. Öppna en ny flik för [Azure Cloud Shell](https://shell.azure.com) i webbläsaren.  Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

Kopiera och klistra in följande kommandon för att förbereda för att testa NAT-tjänsten.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

De här kommandona kommer att uppdatera den virtuella datorn, installera Go, installera [Hej](https://github.com/rakyll/hey) från GitHub och uppdatera din gränssnitts miljö.

Nu är du redo att testa NAT-tjänsten.

## <a name="validate-nat-service"></a>Verifiera NAT-tjänst

När du har loggat in på den virtuella käll datorn kan du använda **sväng** och **Hej** för att generera begär anden till målets IP-adress.

Använd sväng för att hämta filen 100-KByte.  Ersätt **\<ip-address-destination>** i exemplet nedan med mål-IP-adressen som du har kopierat tidigare.  Parametern **--output** anger att den hämtade filen kommer att tas bort.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

Du kan också skapa en serie förfrågningar med hjälp av **Hej**. Ersätt igen **\<ip-address-destination>** med mål-IP-adressen som du tidigare har kopierat.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

Det här kommandot genererar 100-begäranden, 10 samtidigt, med en tids gräns på 30 sekunder. TCP-anslutningen återanvänds inte.  Varje begäran kommer att hämta 100 kByte.  I slutet av körningen rapporterar **Hej** viss statistik om hur bra NAT-tjänsten gjorde.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som ingår i.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en NAT-gateway, skapat en källa och en virtuell måldator och testat NAT-gatewayen.

Granska mått i Azure Monitor för att se hur NAT-tjänsten fungerar. Diagnostisera problem som resurs utbelastning av tillgängliga SNAT-portar.  Resurs överbelastning av SNAT-portar kan enkelt åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix eller båda.

- Läs mer om [Virtual Network NAT](./nat-overview.md)
- Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]
