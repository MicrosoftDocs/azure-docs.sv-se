---
title: Snabb start – skapa en virtuell Linux-dator med Azure PowerShell
description: I den här snabbstarten lär du dig hur du använder Azure PowerShell för att skapa en virtuell Linux-dator
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5711e3f406af04d0dc3a233031379c9ef18244ba
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549659"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Snabbstart: Skapa en virtuell Linux-dator i Azure med PowerShell

Azure PowerShell-modulen används för att skapa och hantera Azure-resurser från PowerShell-kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure PowerShell-modulen för att distribuera en virtuell Linux-dator (VM) i Azure. I den här snabb starten används Ubuntu 18,04 LTS Marketplace-avbildningen från kanoniskt. För att se hur den virtuella datorn fungerar i praktiken använder du sedan SSH för att ansluta till den virtuella datorn och installerar NGINX-webbservern.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="create-ssh-key-pair"></a>Skapa SSH-nyckelpar

Använd [ssh-keygen](https://www.ssh.com/ssh/keygen/) för att skapa ett SSH-nyckelpar. Om du redan har ett SSH-nyckelpar kan du hoppa över det här steget.


```azurepowershell-interactive
ssh-keygen -m PEM -t rsa -b 4096
```

Du uppmanas att ange ett fil namn för nyckel paret eller så kan du trycka på **RETUR** om du vill använda standard platsen för `/home/<username>/.ssh/id_rsa` . Du kommer också att kunna skapa ett lösen ord för nycklarna, om du vill.

Mer detaljerad information om hur du skapar SSH-nyckelpar finns i [så här använder du SSH-nycklar med Windows](ssh-from-windows.md).

Om du skapar ett SSH-nyckelpar med hjälp av Cloud Shell kommer det att lagras i ett [lagrings konto som skapas automatiskt av Cloud Shell](../../cloud-shell/persisting-shell-storage.md). Ta inte bort lagrings kontot eller fil resursen i det förrän du har hämtat dina nycklar eller om du kommer att förlora åtkomsten till den virtuella datorn. 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Skapa virtuella nätverksresurser

Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. Dessa resurser används för att tillhandahålla nätverksanslutning till den virtuella datorn och ansluta den till Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Skapa en Azure-nätverkssäkerhetsgrupp och trafikregel. Nätverkssäkerhetsgruppen skyddar den virtuella datorn med regler för inkommande och utgående trafik. I följande exempel skapas en regel för inkommande trafik för TCP-port 22 som tillåter SSH-anslutningar. För att tillåta inkommande webbtrafik skapas också en regel för inkommande trafik för TCP-port 80.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Skapa ett virtuellt nätverkskort (NIC) med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Det virtuella nätverkskortet ansluter den virtuella datorn till ett undernät, en nätverkssäkerhetsgrupp och en offentlig IP-adress.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator i PowerShell skapar du en konfiguration som har inställningar som avbildningen för alternativ för användning, storlek och autentisering. Konfigurationen används sedan till att skapa den virtuella datorn.

Definiera SSH-autentiseringsuppgifterna, informationen om operativsystemet och VM-storleken. I det här exemplet lagras SSH-nyckeln i `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Kombinera de tidigare konfigurationsdefinitionerna för att skapa den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm):

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

Det tar några minuter för den virtuella datorn att distribueras. När distributionen är klar kan du gå vidare till nästa avsnitt.

## <a name="connect-to-the-vm"></a>Anslut till VM:en

Skapa en SSH-anslutning med den virtuella datorn med hjälp av en offentlig IP-adress. Du hämtar den virtuella datorns offentliga IP-adress genom att köra cmdleten [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Med samma gränssnitt som du använde för att skapa SSH-nyckelpar klistrar du in följande kommando i gränssnittet för att skapa en SSH-session. Ersätt *10.111.12.123* med IP-adressen för den virtuella datorn.

```bash
ssh azureuser@10.111.12.123
```

När du får en uppmaning är *azureuser* användarnamnet för inloggningen. Om du använder en lösenfras med dina SSH-nycklar måste du ange den när du uppmanas att göra det.


## <a name="install-nginx"></a>Installera NGINX

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Från din SSH-session uppdaterar du dina paketkällor och installera det senaste NGINX-paketet.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen genom att skriva `exit`.


## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

Använd valfri webbläsare för att visa välkomstsidan för NGINX. Ange den virtuella datorns offentliga IP-adress som webbadress. Den offentliga IP-adressen kan hittas på översiktssidan för den virtuella datorn eller som en del av SSH-anslutningssträngen du använde tidigare.

![NGINX standard sida för Välkommen](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, skapade en nätverkssäkerhetsgrupp och en regel och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.

> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
