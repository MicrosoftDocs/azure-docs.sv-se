---
title: Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files | Microsoft Docs
description: Så här konfigurerar du en punkt-till-plats (P2S) VPN i Linux för användning med Azure Files
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 74422318718e318a00d7bd7ebaf8e4093ef75aa6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629282"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurera en punkt-till-plats (P2S) VPN på Linux som ska användas med Azure Files
Du kan använda en punkt-till-plats (P2S) VPN-anslutning för att montera dina Azure-filresurser över SMB utanför Azure, utan att öppna port 445. En punkt-till-plats-VPN-anslutning är en VPN-anslutning mellan Azure och en enskild klient. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Om du har många klienter som behöver ansluta till dina Azure-filresurser från ditt lokala nätverk kan du använda en plats-till-plats (S2S) VPN-anslutning i stället för en punkt-till-plats-anslutning för varje klient. Mer information finns i [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

Vi rekommenderar starkt att du läser [Azure Files nätverks översikt](storage-files-networking-overview.md) innan du fortsätter med den här artikeln för en fullständig diskussion av de nätverks alternativ som är tillgängliga för Azure Files.

Artikeln beskriver stegen för att konfigurera en punkt-till-plats-VPN i Linux för att montera Azure-filresurser direkt lokalt. Om du vill dirigera Azure File Sync trafik via en VPN-anslutning kan du läsa [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Förutsättningar
- Den senaste versionen av Azure CLI. Mer information om hur du installerar Azure CLI finns i [installera Azure POWERSHELL CLI](/cli/azure/install-azure-cli) och välj operativ system. Om du föredrar att använda Azure PowerShell-modulen i Linux, kan du följa anvisningarna nedan för Azure CLI.

- En Azure-filresurs som du vill montera lokalt. Azure-filresurser distribueras i lagrings konton, som är hanterings konstruktioner som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. Du kan lära dig mer om hur du distribuerar Azure-filresurser och lagrings konton i [skapa en Azure-filresurs](storage-how-to-create-file-share.md).

- En privat slut punkt för det lagrings konto som innehåller den Azure-filresurs som du vill montera lokalt. Mer information om hur du skapar en privat slut punkt finns i [konfigurera Azure Files nätverks slut punkter](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Installera nödvändig program vara
Den virtuella Azure-Nätverksgatewayen kan tillhandahålla VPN-anslutningar med flera VPN-protokoll, inklusive IPsec och OpenVPN. Den här guiden visar hur du använder IPsec och använder strongSwan-paketet för att tillhandahålla support på Linux. 

> Verifierad med Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Distribuera ett virtuellt nätverk 
För att få åtkomst till Azure-filresursen och andra Azure-resurser från lokala platser via en punkt-till-plats-VPN måste du skapa ett virtuellt nätverk eller ett virtuellt nätverk. P2S VPN-anslutningen som du skapar automatiskt är en brygga mellan den lokala Linux-datorn och det virtuella Azure-nätverket.

Följande skript skapar ett virtuellt Azure-nätverk med tre undernät: ett för lagrings kontots tjänst slut punkt, ett för lagrings kontots privata slut punkt, vilket krävs för att komma åt lagrings kontot lokalt utan att skapa en anpassad routning för den offentliga IP-adressen för det lagrings konto som kan ändras, och ett för din virtuella nätverksgateway som tillhandahåller VPN-tjänsten. 

Kom ihåg att ersätta `<region>` , `<resource-group>` och `<desired-vnet-name>` med lämpliga värden för din miljö.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="create-certificates-for-vpn-authentication"></a>Skapa certifikat för VPN-autentisering
För att VPN-anslutningar från dina lokala Linux-datorer ska kunna autentiseras för åtkomst till ditt virtuella nätverk måste du skapa två certifikat: ett rot certifikat som kommer att tillhandahållas till den virtuella datorns gateway och ett klient certifikat, som kommer att signeras med rot certifikatet. Följande skript skapar de certifikat som krävs.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Distribuera virtuell nätverksgateway
Den virtuella Azure-Nätverksgatewayen är den tjänst som dina lokala Linux-datorer kommer att ansluta till. Att distribuera den här tjänsten kräver två grundläggande komponenter: en offentlig IP-adress som identifierar gatewayen för dina klienter oavsett var de finns i världen och ett rot certifikat som du skapade tidigare och som ska användas för att autentisera dina klienter.

Kom ihåg att ersätta `<desired-vpn-name-here>` med det namn som du vill ha för dessa resurser.

> [!Note]  
> Det kan ta upp till 45 minuter att distribuera den virtuella Azure-Nätverksgatewayen. När den här resursen distribueras blockerar det här bash-skript skriptet för att distributionen ska slutföras.
>
> P2S IKEv2/OpenVPN-anslutningar stöds inte med **Basic** SKU. Det här skriptet använder **VpnGw1** SKU: n för den virtuella Nätverksgatewayen.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Konfigurera VPN-klienten
Den virtuella Azure-Nätverksgatewayen kommer att skapa ett nedladdnings Bart paket med konfigurationsfiler som krävs för att initiera VPN-anslutningen på din lokala Linux-dator. Följande skript kommer att placera de certifikat som du skapade på rätt plats och konfigurera `ipsec.conf` filen med rätt värden från konfigurations filen i det nedladdnings bara paketet.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Montera Azure-filresurs
Nu när du har konfigurerat din punkt-till-plats-VPN kan du montera Azure-filresursen. I följande exempel monteras resursen icke-permanent. Information om hur du monterar permanent finns i [använda en Azure-filresurs med Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Se även
- [Översikt över Azure Files nätverk](storage-files-networking-overview.md)
- [Konfigurera en punkt-till-plats (P2S) VPN i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurera ett VPN för plats-till-plats (S2S) som ska användas med Azure Files](storage-files-configure-s2s-vpn.md)