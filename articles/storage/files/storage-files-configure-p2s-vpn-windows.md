---
title: Konfigurera en punkt-till-plats-VPN (P2S) i Windows för användning med Azure Files | Microsoft Docs
description: Så här konfigurerar du ett P2S-VPN (punkt-till-plats) i Windows för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: de342267292c6a93c4a1ba2eae232403ccaf9514
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785279"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurera en punkt-till-plats-VPN (P2S) i Windows för användning med Azure Files
Du kan använda en P2S VPN-anslutning (punkt-till-plats) för att montera dina Azure-filresurser via SMB utanför Azure, utan att öppna port 445. En punkt-till-plats-VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en VPN-anslutning från plats till plats (S2S) i stället för en punkt-till-plats-anslutning för varje klient. Mer information finns i Konfigurera [en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser Nätverksöverväganden för direkt [åtkomst](storage-files-networking-overview.md) till Azure-filresurs innan du fortsätter med den här artikeln för en fullständig diskussion om de nätverksalternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en punkt-till-plats-VPN på Windows (Windows-klient och Windows Server) för att montera Azure-filresurser direkt lokalt. Om du vill dirigera trafik via Azure File Sync VPN kan du gå till konfigurera Azure File Sync [och brandväggsinställningar.](../file-sync/file-sync-firewall-and-proxy.md)

## <a name="prerequisites"></a>Förutsättningar
- Den senaste versionen av Azure PowerShell modulen. Mer information om hur du installerar Azure PowerShell finns i [Installera Azure PowerShell och](/powershell/azure/install-az-ps) välja operativsystem. Om du föredrar att använda Azure CLI i Windows kan du, men anvisningarna nedan visas för Azure PowerShell.

- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagringskonton, som är hanteringskonstruktioner som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blobcontainrar eller köer. Du kan lära dig mer om hur du distribuerar Azure-filresurser och lagringskonton [i Skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slutpunkt för lagringskontot som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slutpunkt finns i [Konfigurera Azure Files nätverksslutpunkter](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk
För att få åtkomst till din Azure-filresurs och andra Azure-resurser från en lokal plats via ett punkt-till-plats-VPN måste du skapa ett virtuellt nätverk eller VNet. P2S VPN-anslutningen som du skapar automatiskt är en brygga mellan din lokala Windows-dator och det här virtuella Azure-nätverket.

Följande PowerShell skapar ett virtuellt Azure-nätverk med tre undernät: ett för lagringskontots tjänstslutpunkt, ett för lagringskontots privata slutpunkt, som krävs för att få åtkomst till lagringskontot lokalt utan att skapa anpassad routning för den offentliga IP-adressen för lagringskontot som kan ändras och ett för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg att `<region>` ersätta , och med lämpliga värden för din `<resource-group>` `<desired-vnet-name>` miljö.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Skapa rotcertifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Windows-datorer ska autentiseras för åtkomst till ditt virtuella nätverk måste du skapa två certifikat: ett rotcertifikat som kommer att tillhandahållas till den virtuella datorgatewayen och ett klientcertifikat som signeras med rotcertifikatet. Följande PowerShell skapar rotcertifikatet: klientcertifikatet skapas när den virtuella Azure-nätverksgatewayen har skapats med information från gatewayen. 

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
Den virtuella Azure-nätverksgatewayen är den tjänst som dina lokala Windows-datorer ska ansluta till. Distributionen av den här tjänsten kräver två grundläggande komponenter: en offentlig IP-adress som identifierar gatewayen till dina klienter oavsett var de finns i världen och ett rotcertifikat som du skapade tidigare som används för att autentisera dina klienter.

Kom ihåg att `<desired-vpn-name-here>` ersätta med det namn som du vill använda för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera den virtuella Azure-nätverksgatewayen. Medan den här resursen distribueras blockerar det här PowerShell-skriptet för att distributionen ska slutföras. Detta är förväntat.

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

## <a name="create-client-certificate"></a>Skapa klientcertifikat
Klientcertifikatet skapas med den virtuella nätverksgatewayens URI. Det här certifikatet signeras med det rotcertifikat som du skapade tidigare.

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
Den virtuella Azure-nätverksgatewayen skapar ett nedladdningsbart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Windows-dator. Vi konfigurerar VPN-anslutningen med funktionen [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) i Windows 10/Windows Server 2016+. Det här paketet innehåller också körbara paket som konfigurerar den äldre Windows VPN-klienten, om så önskas. Den här guiden använder Always On VPN i stället för den äldre Windows VPN-klienten eftersom Always On VPN-klienten tillåter slutanvändare att ansluta/koppla från Azure VPN utan att ha administratörsbehörighet till sin dator. 

Följande skript installerar det klientcertifikat som krävs för autentisering mot den virtuella nätverksgatewayen, laddar ned och installerar VPN-paketet. Kom ihåg att `<computer1>` ersätta och med önskade `<computer2>` datorer. Du kan köra det här skriptet på så många datorer du vill genom att lägga till fler PowerShell-sessioner i `$sessions` matrisen. Ditt användningskonto måste vara administratör på var och en av dessa datorer. Om en av dessa datorer är den lokala dator som du kör skriptet från måste du köra skriptet från en upphöjd PowerShell-session. 

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

## <a name="mount-azure-file-share"></a>Montera En Azure-filresurs
Nu när du har ställt in ditt punkt-till-plats-VPN kan du använda det för att montera Azure-filresursen på de datorer som du installationen via PowerShell. I följande exempel monteras resursen, rotkatalogen för resursen visas för att bevisa att resursen faktiskt är monterad och resursen demonteras. Tyvärr går det inte att montera resursen beständigt över PowerShell-fjärrkommunikation. Information om hur du monterar [beständigt finns i Använda en Azure-filresurs med Windows](storage-how-to-use-files-windows.md). 

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
- [Nätverksöverväganden för direkt åtkomst till Azure-filresurs](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats-VPN (P2S) i Linux för användning med Azure Files](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurera ett VPN för plats-till-plats (S2S) för användning med Azure Files](storage-files-configure-s2s-vpn.md)