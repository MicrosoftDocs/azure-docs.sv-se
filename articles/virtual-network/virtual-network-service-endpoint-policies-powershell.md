---
title: Begränsa data exfiltrering till Azure Storage-Azure PowerShell
description: I den här artikeln får du lära dig hur du begränsar och begränsar virtuella nätverks data exfiltrering till Azure Storage resurser med tjänst slut punkts principer för virtuella nätverk med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 3ff0ee79413640c09132b8399a3b0c07dd1783cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063718"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Hantera data exfiltrering för att Azure Storage konton med tjänst slut punkts principer för virtuella nätverk med hjälp av Azure PowerShell

Med tjänst slut punkts principer för virtuella nätverk kan du tillämpa åtkomst kontroll på Azure Storage-konton inifrån ett virtuellt nätverk över tjänst slut punkter. Det här är en nyckel för att skydda dina arbets belastningar, hantera vilka lagrings konton som tillåts och var data exfiltrering tillåts.
I den här artikeln kan du se hur du:

* Skapa ett virtuellt nätverk.
* Lägg till ett undernät och aktivera tjänstens slut punkt för Azure Storage.
* Skapa två Azure Storage konton och Tillåt nätverks åtkomst till det från det undernät som skapades ovan.
* Skapa en tjänst slut punkts princip för att endast tillåta åtkomst till ett av lagrings kontona.
* Distribuera en virtuell dator (VM) till under nätet.
* Bekräfta åtkomst till det tillåtna lagrings kontot från under nätet.
* Bekräfta åtkomst nekas till det icke-tillåtna lagrings kontot från under nätet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resurs grupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp med namnet *myResourceGroup*: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Aktivera en tjänstslutpunkt

Skapa ett undernät i det virtuella nätverket. I det här exemplet skapas ett undernät med namnet *Private* med en tjänst slut punkt för *Microsoft. Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Begränsa nätverks åtkomsten för under nätet

Skapa säkerhets regler för nätverks säkerhets grupper med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Följande regel tillåter utgående åtkomst till de offentliga IP-adresser som tilldelats den Azure Storage tjänsten: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Följande regel nekar åtkomst till alla offentliga IP-adresser. Den föregående regeln åsidosätter den här regeln, på grund av dess högre prioritet, vilket ger åtkomst till de offentliga IP-adresserna för Azure Storage.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

Följande regel tillåter att Remote Desktop Protocol (RDP) inkommande trafik till under nätet var som helst. Anslutning till fjärr skrivbord tillåts till under nätet så att du kan bekräfta nätverks åtkomsten till en resurs i ett senare steg.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). I följande exempel skapas en nätverks säkerhets grupp med namnet *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Koppla nätverks säkerhets gruppen till det *privata* under nätet med [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) och skriv sedan under näts konfigurationen till det virtuella nätverket. I följande exempel associeras nätverks säkerhets gruppen *myNsgPrivate* till det *privata* under nätet:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Begränsa nätverks åtkomsten till Azure Storage konton

De steg som behövs för att begränsa nätverksåtkomsten till resurser som har skapats via Azure-tjänster som är aktiverade för tjänstslutpunkter varierar från tjänst till tjänst. Läs dokumentationen för enskilda tjänster för specifika åtgärder för varje tjänst. Resten av den här artikeln innehåller steg för att begränsa nätverks åtkomsten för ett Azure Storage konto, som ett exempel.

### <a name="create-two-storage-accounts"></a>Skapa två lagrings konton

Skapa ett Azure Storage-konto med [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

När lagrings kontot har skapats hämtar du nyckeln för lagrings kontot till en variabel med [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

Nyckeln används för att skapa en fil resurs i ett senare steg. Ange `$storageAcctKey` och anteckna värdet, eftersom du också måste ange det manuellt i ett senare steg när du mappar fil resursen till en enhet i en virtuell dator.

Upprepa nu stegen ovan för att skapa ett andra lagrings konto.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Hämta också lagrings konto nyckeln från det här kontot för att använda senare för att skapa en fil resurs.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Skapa en fil resurs i varje lagrings konto

Skapa en kontext för ditt lagrings konto och din nyckel med [New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext). Kontexten kapslar in lagrings kontots namn och konto nyckel:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Skapa en fil resurs med [New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare):

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Neka all nätverks åtkomst till ett lagrings konto

Som standard godkänner lagringskonton nätverksanslutningar från klienter i alla nätverk. Om du vill begränsa åtkomsten till valda nätverk ändrar du standard åtgärden till *neka* med [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset). När nätverksåtkomsten nekas är lagringskontot inte tillgängligt från något nätverk.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Aktivera endast nätverks åtkomst från VNet-undernätet

Hämta det skapade virtuella nätverket med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) och hämta sedan objektet för det privata under nätet till en variabel med [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Tillåt nätverks åtkomst till lagrings kontot från det *privata* under nätet med [Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Använd princip för att tillåta åtkomst till ett giltigt lagrings konto

För att se till att användarna i det virtuella nätverket bara kan komma åt de Azure Storage konton som är säkra och tillåtna, kan du skapa en tjänst slut punkts princip med listan över tillåtna lagrings konton i definitionen. Den här principen tillämpas sedan på det virtuella nätverkets undernät som är anslutet till lagring via tjänst slut punkter.

### <a name="create-a-service-endpoint-policy"></a>Skapa en princip för tjänst slut punkt

Det här avsnittet skapar princip definitionen med listan över tillåtna resurser för åtkomst över tjänstens slut punkt

Hämta resurs-ID för det första (tillåtna) lagrings kontot 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

Skapa princip definitionen för att tillåta resursen ovan

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

Skapa tjänst slut punkts principen med den princip definition som skapats ovan

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>Koppla tjänstens slut punkts princip till det virtuella nätverkets undernät

När du har skapat tjänst slut punkts principen kopplar du den till mål under nätet med tjänstens slut punkts konfiguration för Azure Storage.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Verifiera åtkomst begränsning till Azure Storage konton

### <a name="deploy-the-virtual-machine"></a>Distribuera den virtuella datorn

Om du vill testa nätverks åtkomst till ett lagrings konto distribuerar du en virtuell dator i under nätet.

Skapa en virtuell dator i det *privata* under nätet med [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör kommandot nedan uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn. Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

Utdata som liknar följande exempel returnerar utdata:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Bekräfta åtkomst till det *tillåtna* lagrings kontot

Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella *myVmPrivate* -datorn:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Ersätt `<publicIpAddress>` i följande kommando med den offentliga IP-adressen som returnerades från föregående kommando och ange sedan följande kommando:

```powershell
mstsc /v:<publicIpAddress>
```

En RDP-fil (Remote Desktop Protocol) skapas och laddas ned till datorn. Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Du kan behöva välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn. Välj **OK**. Du kan få en certifikatvarning under inloggningen. Om du ser varningen väljer du **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

På den virtuella datorn *myVmPrivate* mappar du Azure-filresursen från tillåtet lagrings konto till enhet Z med PowerShell. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell returnerar utdata som liknar följande exempelutdata:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure-fildelningen har mappats till enhet Z.

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Bekräfta åtkomst nekas till *icke-tillåtet* lagrings konto

På samma virtuella *myVmPrivate* -dator försöker du mappa Azure-filresursen till enhet X. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

Åtkomst till resursen nekas och du får ett `New-PSDrive : Access is denied` fel meddelande. Åtkomst nekas eftersom lagrings kontots *notallowedaccount* inte finns i listan över tillåtna resurser i tjänstens slut punkts princip. 

Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPublic*.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tillämpat en tjänst slut punkt princip över en tjänst slut punkt för Azure Virtual Network för att Azure Storage. Du har skapat Azure Storage konton och begränsad nätverks åtkomst till endast vissa lagrings konton (och därmed nekade andra) från ett virtuellt nätverks under nät. Läs mer om principer för tjänst slut punkter i [Översikt över tjänst slut punkter](virtual-network-service-endpoint-policies-overview.md).
