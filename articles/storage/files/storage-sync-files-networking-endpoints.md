---
title: Konfigurera Azure File Sync nätverks slut punkter | Microsoft Docs
description: Lär dig hur du konfigurerar Azure File Sync nätverks slut punkter.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 5/11/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 97ccbd0858a7b85c4b5d1e460f67416d8139e49a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218612"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Konfigurera nätverksslutpunkter i Azure File Sync
Azure Files och Azure File Sync ger två huvud typer av slut punkter för åtkomst till Azure-fil resurser: 
- Offentliga slut punkter som har en offentlig IP-adress och som kan nås från var som helst i världen.
- Privata slut punkter, som finns i ett virtuellt nätverk och har en privat IP-adress inom det virtuella nätverkets adress utrymme.

För både Azure Files och Azure File Sync kontrollerar Azure-hanterings objekt, lagrings kontot och tjänsten för synkronisering av lagring de både offentliga och privata slut punkter. Lagrings kontot är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. Tjänsten Storage Sync är en hanterings konstruktion som representerar registrerade servrar, som är Windows-filservrar med en upprättad förtroende relation med Azure File Sync och synkronisera grupper som definierar topologin för synkroniseringsrelation. 

Den här artikeln fokuserar på hur du konfigurerar nätverks slut punkter för både Azure Files och Azure File Sync. Om du vill veta mer om hur du konfigurerar nätverks slut punkter för åtkomst till Azure-filresurser direkt, i stället för att cachelagring lokalt med Azure File Sync, se [konfigurera Azure Files nätverks slut punkter](storage-files-networking-endpoints.md).

Vi rekommenderar att du läser [Azure File Sync nätverks överväganden](storage-sync-files-networking-overview.md) innan du läser den här guiden.

## <a name="prerequisites"></a>Förutsättningar 
Den här artikeln förutsätter att:
- Du har en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Du har redan skapat en Azure-filresurs i ett lagrings konto som du vill ansluta till från den lokala platsen. Information om hur du skapar en Azure-filresurs finns i [skapa en Azure-fil resurs](storage-how-to-create-file-share.md).
- Du har redan skapat en tjänst för synkronisering av lagring och registrerat din Windows-filserver med den. Information om hur du distribuerar Azure File Sync finns i [distribuera Azure File Sync](storage-sync-files-deployment-guide.md).

Dessutom:
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="create-the-private-endpoints"></a>Skapa de privata slut punkterna
När du skapar en privat slut punkt för en Azure-resurs distribueras följande resurser:

- **En privat slut punkt**: en Azure-resurs som representerar antingen den privata slut punkten för lagrings kontot eller tjänsten för synkronisering av lagring. Du kan tänka på detta som en resurs som ansluter din Azure-resurs och ett nätverks gränssnitt.
- **Ett nätverks gränssnitt (NIC)**: det nätverks gränssnitt som upprätthåller en privat IP-adress inom det angivna virtuella nätverket/under nätet. Det här är exakt samma resurs som distribueras när du distribuerar en virtuell dator, men i stället för att tilldelas en virtuell dator, ägs den av den privata slut punkten.
- **En privat DNS-zon**: om du aldrig har distribuerat en privat slut punkt för det här virtuella nätverket tidigare, kommer en ny privat DNS-zon att distribueras för det virtuella nätverket. En DNS A-post skapas också för Azure-resursen i den här DNS-zonen. Om du redan har distribuerat en privat slut punkt i det här virtuella nätverket läggs en ny A-post för Azure-resurs till i den befintliga DNS-zonen. Att distribuera en DNS-zon är valfritt, men rekommenderas för att förenkla DNS-hanteringen som krävs.

> [!Note]  
> I den här artikeln används DNS-suffix för Azures offentliga regioner, `core.windows.net` för lagrings konton och `afs.azure.net` för tjänster för synkronisering av lagring. Den här kommentarer gäller också för Azures suveräna moln, till exempel Azures myndigheter för amerikanska myndigheter – du behöver bara ersätta lämpliga suffix för din miljö.

### <a name="create-the-storage-account-private-endpoint"></a>Skapa den privata slut punkten för lagrings kontot
# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i [Konfigurera DNS-vidarebefordran för Azure Files](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt genom att köra följande kommandon från PowerShell, kommando raden eller terminalen (fungerar för Windows, Linux eller MacOS). Du måste ersätta `<storage-account-name>` med lämpligt lagrings konto namn:

```console
nslookup <storage-account-name>.file.core.windows.net
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket (utdata som visas för Windows):

```output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i [Konfigurera DNS-vidarebefordran för Azure Files](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt med följande kommandon:

```powershell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket:

```output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i [Konfigurera DNS-vidarebefordran för Azure Files](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt med följande kommandon:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket:

```output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-service-private-endpoint"></a>Skapa privat slut punkt för synkroniseringstjänsten för lagring
> [!Important]  
> Du måste använda Azure File Sync agent version 10,1 eller senare för att kunna använda privata slut punkter i resursens synkroniseringstjänst för lagring. Agent versioner före 10,1 stöder inte privata slut punkter på tjänsten för synkronisering av lagring. Alla tidigare agent versioner stöder privata slut punkter på lagrings konto resursen.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till det **privata länk centret** genom att skriva in en *privat länk* i sök fältet överst i Azure Portal. I innehålls förteckningen för det privata länk centret väljer du **privata slut punkter** och sedan **+ Lägg till** för att skapa en ny privat slut punkt.

[![En skärm bild av det privata länk centret](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Den resulterande guiden har flera sidor att slutföra.

På bladet **grundläggande** väljer du önskad resurs grupp, namn och region för din privata slut punkt. Det kan vara vad du vill, de behöver inte matcha tjänsten för synkronisering av lagring på något sätt, men du måste skapa den privata slut punkten i samma region som det virtuella nätverk som du vill skapa den privata slut punkten i.

![En skärm bild av grunderna i avsnittet Skapa privat slut punkt](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

På **resurs** bladet väljer du alternativ knappen för **att ansluta till en Azure-resurs i min katalog**. Under **resurs typ** väljer du **Microsoft. StorageSync/storageSyncServices** för resurs typen. 

På bladet **konfiguration** kan du välja det angivna virtuella nätverk och undernät som du vill lägga till din privata slut punkt till. Välj samma virtuella nätverk som det du använde för lagrings kontot ovan. Konfigurations bladet innehåller också information om hur du skapar/uppdaterar den privata DNS-zonen.

Klicka på **Granska + skapa** för att skapa den privata slut punkten.

Du kan testa att den privata slut punkten har kon figurer ATS korrekt genom att köra följande kommandon från PowerShell. 

```powershell
$privateEndpointResourceGroupName = "<your-private-endpoint-resource-group>"
$privateEndpointName = "<your-private-endpoint-name>"

Get-AzPrivateEndpoint `
        -ResourceGroupName $privateEndpointResourceGroupName `
        -Name $privateEndpointName `
        -ErrorAction Stop | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
    Select-Object -ExpandProperty Fqdns | `
    ForEach-Object { Resolve-DnsName -Name $_ } | `
    Format-List
```

Om allt har fungerat korrekt bör du se följande utdata där `192.168.1.4` , `192.168.1.5` , `192.168.1.6` och `192.168.1.7` är de privata IP-adresserna som tilldelats till den privata slut punkten:

```output
Name     : mysssmanagement.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmanagement.westus2.privatelink.afs.azure.net


Name       : mysssmanagement.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.4

Name     : myssssyncp.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncp.westus2.privatelink.afs.azure.net


Name       : myssssyncp.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.5

Name     : myssssyncs.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : myssssyncs.westus2.privatelink.afs.azure.net


Name       : myssssyncs.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.6

Name     : mysssmonitoring.westus2.afs.azure.net
Type     : CNAME
TTL      : 60
Section  : Answer
NameHost : mysssmonitoring.westus2.privatelink.afs.azure.net


Name       : mysssmonitoring.westus2.privatelink.afs.azure.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 192.168.1.7

```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill skapa en privat slut punkt för lagrings tjänsten för synkronisering måste du först hämta en referens till tjänsten för synkronisering av lagring. Kom ihåg att ersätta `<storage-sync-service-resource-group>` och `<storage-sync-service>` med rätt värden för din miljö. Följande PowerShell-kommandon förutsätter att du använder redan har fyllt i informationen om det virtuella nätverket. 

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

$storageSyncService = Get-AzStorageSyncService `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name $storageSyncServiceName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageSyncService) {
    $errorMessage = "Storage Sync Service $storageSyncServiceName not found "
    $errorMessage += "in resource group $storageSyncServiceResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}
```

Om du vill skapa en privat slut punkt måste du skapa en anslutning för privata länkar till tjänsten för synkronisering av lagring. Den privata länk anslutningen är inmatad för att skapa den privata slut punkten.

```powershell 
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork = $virtualNetwork | `
    Set-AzVirtualNetwork -ErrorAction Stop

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageSyncServiceName-Connection" `
        -PrivateLinkServiceId $storageSyncService.ResourceId `
        -GroupId "Afs" `
        -ErrorAction Stop

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageSyncServiceResourceGroupName `
        -Name "$storageSyncServiceName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

När du skapar en privat DNS-zon för Azure kan du använda värdnamn för tjänsten lagrings synkronisering, till exempel `mysssmanagement.westus2.afs.azure.net` , för att matcha rätt privata IP-adresser för tjänsten för synkronisering av lagring i det virtuella nätverket. Även om det är valfritt från perspektivet att skapa en privat slut punkt krävs det uttryckligen för att Azure File Sync agenten ska kunna komma åt tjänsten för synkronisering av lagring. 

```powershell
# Get the desired Storage Sync Service suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$azureEnvironment = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

switch($azureEnvironment) {
    "AzureCloud" {
        $storageSyncSuffix = "afs.azure.net"
    }

    "AzureUSGovernment" {
        $storageSyncSuffix = "afs.azure.us"
    }
    
    default {
        Write-Error 
                -Message "The Azure environment $_ is not currently supported by Azure File Sync." `
                -ErrorAction Stop
    }
}

# For public cloud, this will generate the following DNS suffix:
# privatelink.afs.azure.net
$dnsZoneName = "privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```
Nu när du har en referens till den privata DNS-zonen måste du skapa en A-post för lagrings tjänsten för synkronisering.

```powershell 
$privateEndpointIpFqdnMappings = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty Id | `
    ForEach-Object { Get-AzNetworkInterface -ResourceId $_ } | `
    Select-Object -ExpandProperty IpConfigurations | `
    ForEach-Object { 
        $privateIpAddress = $_.PrivateIpAddress; 
        $_ | `
            Select-Object -ExpandProperty PrivateLinkConnectionProperties | `
            Select-Object -ExpandProperty Fqdns | `
            Select-Object `
                @{ 
                    Name = "PrivateIpAddress"; 
                    Expression = { $privateIpAddress } 
                }, `
                @{ 
                    Name = "FQDN"; 
                    Expression = { $_ } 
                } 
    }

foreach($ipFqdn in $privateEndpointIpFqdnMappings) {
    $privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $ipFqdn.PrivateIpAddress
    
    $dnsEntry = $ipFqdn.FQDN.Substring(0, 
        $ipFqdn.FQDN.IndexOf(".", $ipFqdn.FQDN.IndexOf(".") + 1))

    New-AzPrivateDnsRecordSet `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsEntry `
            -RecordType A `
            -ZoneName $dnsZoneName `
            -Ttl 600 `
            -PrivateDnsRecords $privateDnsRecordConfig `
            -ErrorAction Stop | `
        Out-Null
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa en privat slut punkt för lagrings tjänsten för synkronisering måste du först hämta en referens till tjänsten för synkronisering av lagring. Kom ihåg att ersätta `<storage-sync-service-resource-group>` och `<storage-sync-service>` med rätt värden för din miljö. Följande CLI-kommandon förutsätter att du använder redan har fyllt i informationen om det virtuella nätverket. 

```bash
storageSyncServiceResourceGroupName="<storage-sync-service-resource-group>"
storageSyncServiceName="<storage-sync-service>"

storageSyncService=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "id" | \
    tr -d '"')

storageSyncServiceRegion=$(az resource show \
        --resource-group $storageSyncServiceResourceGroupName \
        --name $storageSyncServiceName \
        --resource-type "Microsoft.StorageSync/storageSyncServices" \
        --query "location" | \
    tr -d '"')
```

Om du vill skapa en privat slut punkt måste du först se till att under nätets nätverks princip är inställd på inaktive rad. Sedan kan du skapa en privat slut punkt med `az network private-endpoint create` kommandot.

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $storageSyncServiceResourceGroupName \
        --name "$storageSyncServiceName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageSyncService \
        --group-id "Afs" \
        --connection-name "$storageSyncServiceName-Connection" \
        --query "id" | \
    tr -d '"')
```

När du skapar en privat DNS-zon för Azure kan du använda värdnamn för tjänsten lagrings synkronisering, till exempel `mysssmanagement.westus2.afs.azure.net` , för att matcha rätt privata IP-adresser för tjänsten för synkronisering av lagring i det virtuella nätverket. Även om det är valfritt från perspektivet att skapa en privat slut punkt krävs det uttryckligen för att Azure File Sync agenten ska kunna komma åt tjänsten för synkronisering av lagring. 

```bash
# Get the desired storage account suffix (afs.azure.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
azureEnvironment=$(az cloud show \
        --query "name" |
    tr -d '"')

storageSyncSuffix=""
if [ $azureEnvironment == "AzureCloud" ]
then
    storageSyncSuffix="afs.azure.net"
elif [ $azureEnvironment == "AzureUSGovernment" ]
then
    storageSyncSuffix="afs.azure.us"
else
    echo "Unsupported Azure environment $azureEnvironment."
fi

# For public cloud, this will generate the following DNS suffix:
# privatelinke.afs.azure.net.
dnsZoneName="privatelink.$storageSyncSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=""
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

dnsZone=""
possibleDnsZone=""
for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        echo "1" > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $virtualNetworkResourceGroupName \
            --zone-name $dnsZoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
    
    dnsZoneResourceGroup=$virtualNetworkResourceGroupName
fi
```

Nu när du har en referens till den privata DNS-zonen måste du skapa en A-post för lagrings tjänsten för synkronisering.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateIpAddresses=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateIpAddress" \
        --output tsv) 

hostNames=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[].privateLinkConnectionProperties.fqdns[]" \
        --output tsv)

i=0
for privateIpAddress in $privateIpAddresses
do
    j=0
    targetHostName=""
    for hostName in $hostNames
    do
        if [ $i == $j ]
        then
            targetHostName=$hostName
            break
        fi

        j=$(expr $j + 1)
    done

    endpointName=$(echo $targetHostName | \
        cut -c1-$(expr $(expr index $targetHostName ".") - 1))

    az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name "$endpointName.$storageSyncServiceRegion" \
        --output none
    
    az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name "$endpointName.$storageSyncServiceRegion" \
        --ipv4-address $privateIpAddress \
        --output none

    i=$(expr $i + 1)
done
```
---

## <a name="restrict-access-to-the-public-endpoints"></a>Begränsa åtkomsten till de offentliga slut punkterna
Du kan begränsa åtkomsten till de offentliga slut punkterna för både lagrings kontot och tjänsterna för synkronisering av lagring. Begränsa åtkomsten till den offentliga slut punkten ger ytterligare säkerhet genom att se till att nätverks paket endast godkänns från godkända platser. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>Begränsa åtkomsten till den offentliga slut punkten för lagrings kontot
Åtkomst begränsning till den offentliga slut punkten görs med hjälp av brand Väggs inställningarna för lagrings kontot. I allmänhet kommer de flesta brand Väggs principer för ett lagrings konto att begränsa nätverks åtkomsten till ett eller flera virtuella nätverk. Det finns två sätt att begränsa åtkomsten till ett lagrings konto till ett virtuellt nätverk:

- [Skapa en eller flera privata slut punkter för lagrings kontot](#create-the-storage-account-private-endpoint) och inaktivera åtkomst till den offentliga slut punkten. Detta säkerställer att endast trafik som härstammar från de önskade virtuella nätverken kan komma åt Azure-filresurserna i lagrings kontot.
- Begränsa den offentliga slut punkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. När du begränsar trafiken till ett lagrings konto via en tjänst slut punkt har du fortfarande åtkomst till lagrings kontot via den offentliga IP-adressen.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>Inaktivera åtkomst till den offentliga slut punkten för lagrings kontot
När åtkomst till den offentliga slut punkten är inaktive rad kan lagrings kontot fortfarande nås via dess privata slut punkter. Annars avvisas giltiga begär anden till lagrings kontots offentliga slut punkt. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>Begränsa åtkomsten till den offentliga slut punkten för lagrings kontot till vissa virtuella nätverk
När du begränsar lagrings kontot till specifika virtuella nätverk kan du tillåta begär anden till den offentliga slut punkten från de angivna virtuella nätverken. Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. Detta kan användas med eller utan privata slut punkter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>Inaktivera åtkomst till den offentliga slut punkten för Storage Sync-tjänsten
Med Azure File Sync kan du begränsa åtkomsten till vissa virtuella nätverk via privata slut punkter. Azure File Sync har inte stöd för tjänst slut punkter för att begränsa åtkomsten till den offentliga slut punkten till vissa virtuella nätverk. Det innebär att de två tillstånden för lagrings tjänstens offentliga slut punkt är aktiverade och inaktiverade.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Detta är inte möjligt via Azure Portal. Välj fliken Azure PowerShell för att få anvisningar om hur du inaktiverar den offentliga slut punkten för Storage Sync-tjänsten. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
För att inaktivera åtkomst till lagrings tjänstens offentliga slut punkt ställer vi in `incomingTrafficPolicy` egenskapen på lagrings tjänsten för synkronisering till `AllowVirtualNetworksOnly` . Ange `incomingTrafficPolicy` till i stället om du vill aktivera åtkomst till lagrings tjänstens offentliga slut punkt `AllowAllTraffic` . Kom ihåg att ersätta `<storage-sync-service-resource-group>` och `<storage-sync-service>` .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

Set-AzStorageSyncService `
    -ResourceGroupName $storageSyncServiceResourceGroupName `
    -Name $storageSyncServiceName `
    -IncomingTrafficPolicy AllowVirtualNetworksOnly
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI har inte stöd för att ange `incomingTrafficPolicy` egenskapen för synkroniseringstjänsten för lagring. Välj fliken Azure PowerShell för att få anvisningar om hur du inaktiverar den offentliga slut punkten för Storage Sync-tjänsten.

---

## <a name="azure-policy"></a>Azure Policy
Azure Policy hjälper till att upprätthålla organisations standarder och utvärdera efterlevnaden mot dessa standarder i stor skala. Azure Files och Azure File Sync visar flera användbara nätverks principer för granskning och reparationer som hjälper dig att övervaka och automatisera distributionen.

Principer granskar din miljö och varnar dig om dina lagrings konton eller lagrings Sync-tjänster avviker från det definierade beteendet. Till exempel om en offentlig slut punkt är aktive rad när din princip har ställts in för att låta de offentliga slut punkterna vara inaktiverade. Med ändra/distribuera principer får du ett steg ytterligare och proaktivt ändra en resurs (till exempel tjänsten för synkronisering av lagring) eller distribuera resurser (t. ex. privata slut punkter) för att anpassa sig efter principerna.

Följande fördefinierade principer är tillgängliga för Azure Files och Azure File Sync:

| Action | Tjänst | Villkor | Principnamn |
|-|-|-|-|
| Granska | Azure Files | Lagrings kontots offentliga slut punkt är aktive rad. Mer information finns i [inaktivera åtkomst till den offentliga slut punkten för lagrings kontot](#disable-access-to-the-storage-account-public-endpoint) . | Lagrings konton bör begränsa nätverks åtkomsten |
| Granska | Azure File Sync | Den offentliga slut punkten för Storage Sync-tjänsten är aktive rad. Mer information finns i [inaktivera åtkomst till den offentliga slut punkten för Storage Sync-tjänsten](#disable-access-to-the-storage-sync-service-public-endpoint) . | Åtkomst till offentligt nätverk ska inaktive ras för Azure File Sync |
| Granska | Azure Files | Lagrings kontot måste ha minst en privat slut punkt. Mer information finns i [skapa lagrings kontots privata slut punkt](#create-the-storage-account-private-endpoint) . | Lagrings kontot bör använda en anslutning för privat anslutning |
| Granska | Azure File Sync | Tjänsten Storage Sync kräver minst en privat slut punkt. Mer information finns i [skapa en privat slut punkt för Storage Sync-tjänsten](#create-the-storage-sync-service-private-endpoint) . | Azure File Sync ska använda privat länk |
| Ändra | Azure File Sync | Inaktivera den offentliga slut punkten för synkroniseringstjänsten för lagring. | Ändra-konfigurera Azure File Sync för att inaktivera offentlig nätverks åtkomst |
| Distribuera | Azure File Sync | Distribuera en privat slut punkt för lagrings tjänsten för synkronisering. | Konfigurera Azure File Sync med privata slut punkter |
| Distribuera | Azure File Sync | Distribuera en A-post till privatelink.afs.azure.net DNS-zon. | Konfigurera Azure File Sync att använda privata DNS-zoner |

### <a name="set-up-a-private-endpoint-deployment-policy"></a>Konfigurera en distributions princip för privat slut punkt
Om du vill konfigurera en distributions princip för en privat slut punkt går du till [Azure Portal](https://portal.azure.com/)och söker efter **princip**. Azure Policy Center bör vara ett bästa resultat. Navigera till **redigerings**  >  **definitioner** i princip Centers innehålls förteckning. **Definitions** fönstret som innehåller de fördefinierade principerna i alla Azure-tjänster. Om du vill hitta en viss princip väljer du **lagrings** kategori i kategori filtret eller söker efter **Konfigurera Azure File Sync med privata slut punkter**. Välj **...** och **tilldela** för att skapa en ny princip från definitionen.

På bladet **grundläggande** i guiden **tilldela princip** kan du ange en undantags lista för en omfattning, resurs eller resurs grupp och ge principen ett eget namn som hjälper dig att särskilja det. Du behöver inte ändra dessa för att principen ska fungera, men du kan om du vill göra ändringar. Välj **Nästa** för att gå vidare till sidan **parametrar** . 

På bladet **parametrar** väljer du **...** bredvid List rutan **privateEndpointSubnetId** och väljer det virtuella nätverk och undernät där de privata slut punkterna för dina resurser för synkroniseringstjänst för lagring ska distribueras. Den resulterande guiden kan ta flera sekunder att läsa in tillgängliga virtuella nätverk i din prenumeration. Välj lämpligt virtuellt nätverk/undernät för din miljö och klicka på **Välj**. Välj **Nästa** för att gå vidare till **reparations** bladet.

För att den privata slut punkten ska kunna distribueras när en tjänst för synkronisering av lagring utan en privat slut punkt identifieras måste du välja **åtgärden Skapa en reparation** på **reparations** sidan. Slutligen väljer du **Granska + skapa** för att granska princip tilldelningen och **skapa** för att skapa den.

Den resulterande princip tilldelningen utförs regelbundet och kan inte köras omedelbart efter att den har skapats.

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)