---
title: Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files | Microsoft Docs
description: Så här konfigurerar du en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6253deb53229172cd499a6aa14b8d8f19bc07b63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629265"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files
Du kan använda en punkt-till-plats (P2S) VPN-anslutning för att montera dina Azure-filresurser över SMB utanför Azure, utan att öppna port 445. En punkt-till-plats-VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en plats-till-plats (S2S) VPN-anslutning i stället för en punkt-till-plats-anslutning för varje klient. Mer information finns i [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser [nätverks överväganden för direkt åtkomst till Azure-filresurser](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion av de nätverks alternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en punkt-till-plats-VPN i Windows (Windows-klient och Windows Server) för att montera Azure-filresurser direkt lokalt. Om du vill dirigera Azure File Sync trafik via en VPN-anslutning kan du läsa [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Förutsättningar
- Den senaste versionen av Azure PowerShell-modulen. Mer information om hur du installerar Azure PowerShell finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps) och välja operativ system. Om du föredrar att använda Azure CLI i Windows kan du se till att anvisningarna nedan visas för Azure PowerShell.

- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagrings konton, som är hanterings konstruktioner som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. Du kan lära dig mer om hur du distribuerar Azure-filresurser och lagrings konton i [skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slut punkt för det lagrings konto som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slut punkt finns i [konfigurera Azure Files nätverks slut punkter](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk
För att få åtkomst till Azure-filresursen och andra Azure-resurser från lokala platser via en punkt-till-plats-VPN måste du skapa ett virtuellt nätverk eller ett virtuellt nätverk. P2S VPN-anslutningen som du skapar automatiskt är en bro mellan den lokala Windows-datorn och det virtuella Azure-nätverket.

Följande PowerShell skapar ett virtuellt Azure-nätverk med tre undernät: ett för lagrings kontots tjänst slut punkt, ett för lagrings kontots privata slut punkt, vilket krävs för att komma åt lagrings kontot lokalt utan att skapa en anpassad routning för den offentliga IP-adressen för det lagrings konto som kan ändras, och ett för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg att ersätta `<region>` , `<resource-group>` och `<desired-vnet-name>` med lämpliga värden för din miljö.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Skapa rot certifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Windows-datorer ska kunna autentiseras för åtkomst till ditt virtuella nätverk måste du skapa två certifikat: ett rot certifikat som kommer att tillhandahållas till den virtuella datorns gateway och ett klient certifikat som kommer att signeras med rot certifikatet. Följande PowerShell skapar rot certifikatet. klient certifikatet skapas efter det att Azures virtuella nätverksgateway har skapats med information från gatewayen. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Distribuera virtuell nätverksgateway
Den virtuella Azure-Nätverksgatewayen är den tjänst som dina lokala Windows-datorer kommer att ansluta till. Att distribuera den här tjänsten kräver två grundläggande komponenter: en offentlig IP-adress som identifierar gatewayen för dina klienter oavsett var de finns i världen och ett rot certifikat som du skapade tidigare och som ska användas för att autentisera dina klienter.

Kom ihåg att ersätta `<desired-vpn-name-here>` med det namn som du vill ha för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera den virtuella Azure-Nätverksgatewayen. Även om den här resursen distribueras, kommer det här PowerShell-skriptet att blockeras för att distributionen ska slutföras. Detta är förväntat.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Skapa klient certifikat
Klient certifikatet skapas med URI: n för den virtuella Nätverksgatewayen. Certifikatet är signerat med rot certifikatet som du skapade tidigare.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Konfigurera VPN-klienten
Den virtuella Azure-Nätverksgatewayen kommer att skapa ett nedladdnings Bart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Windows-dator. Vi konfigurerar VPN-anslutningen med funktionen [Always on VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) i Windows 10/Windows Server 2016 +. Det här paketet innehåller också körbara paket som konfigurerar den äldre Windows VPN-klienten, om så önskas. Den här guiden använder alltid VPN i stället för den äldre Windows VPN-klienten som Always on VPN-klienten, vilket gör att slutanvändarna kan ansluta eller koppla från Azure VPN utan administratörs behörighet till datorn. 

Följande skript kommer att installera det klient certifikat som krävs för autentisering mot den virtuella Nätverksgatewayen, ladda ned och installera VPN-paketet. Kom ihåg att ersätta `<computer1>` och `<computer2>` med önskade datorer. Du kan köra det här skriptet på så många datorer som du vill genom att lägga till fler PowerShell-sessioner i `$sessions` matrisen. Ditt användnings konto måste vara administratör på var och en av dessa datorer. Om någon av dessa datorer är den lokala dator som du kör skriptet från, måste du köra skriptet från en upphöjd PowerShell-session. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Montera Azure-filresurs
Nu när du har konfigurerat din punkt-till-plats-VPN kan du använda den för att montera Azure-filresursen på de datorer som du installerar via PowerShell. I följande exempel monteras resursen, en lista visas i resursens rot Katalog för att bevisa att resursen faktiskt monteras och demontera resursen. Det går tyvärr inte att montera resursen beständigt över PowerShell-fjärrkommunikation. Information om hur du monterar permanent finns i [använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Se även
- [Nätverks överväganden för direkt åtkomst till Azure-filresurser](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurera ett VPN för plats-till-plats (S2S) som ska användas med Azure Files](storage-files-configure-s2s-vpn.md)