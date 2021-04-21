---
title: Konfigurera Azure File Sync nätverksslutpunkter | Microsoft Docs
description: Lär dig hur du konfigurerar Azure File Sync nätverksslutpunkter.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: b89e0f8209c0165b71eef3d143ea1a84ea8bf64e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797112"
---
# <a name="configuring-azure-file-sync-network-endpoints"></a>Konfigurera nätverksslutpunkter i Azure File Sync
Azure Files och Azure File Sync två huvudtyper av slutpunkter för åtkomst till Azure-filresurser: 
- Offentliga slutpunkter, som har en offentlig IP-adress och kan nås var som helst i världen.
- Privata slutpunkter, som finns i ett virtuellt nätverk och har en privat IP-adress inifrån det virtuella nätverkets adressutrymme.

För både Azure Files och Azure File Sync kan Azure-hanteringsobjekten, lagringskontot respektive tjänsten för synkronisering av lagring styra både de offentliga och privata slutpunkterna. Lagringskontot är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blobcontainrar eller köer. Tjänsten för synkronisering av lagring är en hanteringskonstruktion som representerar registrerade servrar, som är Windows-filservrar med en etablerad förtroenderelation med Azure File Sync och synkroniseringsgrupper, som definierar topologin för synkroniseringsrelationen. 

Den här artikeln fokuserar på hur du konfigurerar nätverksslutpunkter för både Azure Files och Azure File Sync. Mer information om hur du konfigurerar nätverksslutpunkter för åtkomst till Azure-filresurser direkt i stället för att cachelagra lokalt med Azure File Sync finns i [Konfigurera Azure Files nätverksslutpunkter](../files/storage-files-networking-endpoints.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

Vi rekommenderar att [du Azure File Sync nätverksöverväganden](file-sync-networking-overview.md) innan du läser den här guiden.

## <a name="prerequisites"></a>Förutsättningar 
Den här artikeln förutsätter att:
- Du har en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Du har redan skapat en Azure-filresurs i ett lagringskonto som du vill ansluta till från en lokal plats. Information om hur du skapar en Azure-filresurs finns i [Skapa en Azure-filresurs.](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- Du har redan skapat en tjänst för synkronisering av lagring och registrerat din Windows-filserver med den. Information om hur du distribuerar Azure File Sync finns i [Distribuera Azure File Sync](file-sync-deployment-guide.md).

Dessutom:
- Om du tänker använda Azure PowerShell installerar [du den senaste versionen](/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI installerar [du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="create-the-private-endpoints"></a>Skapa de privata slutpunkterna
När du skapar en privat slutpunkt för en Azure-resurs distribueras följande resurser:

- **En privat slutpunkt:** En Azure-resurs som representerar antingen den privata slutpunkten för lagringskontot eller tjänsten för synkronisering av lagring. Du kan se det här som en resurs som ansluter din Azure-resurs och ett nätverksgränssnitt.
- **Ett nätverksgränssnitt (NIC):** Nätverksgränssnittet som upprätthåller en privat IP-adress i det angivna virtuella nätverket/undernätet. Det här är exakt samma resurs som distribueras när du distribuerar en virtuell dator, men i stället för att tilldelas till en virtuell dator ägs den av den privata slutpunkten.
- **En privat DNS-zon:** Om du aldrig har distribuerat en privat slutpunkt för det här virtuella nätverket tidigare distribueras en ny privat DNS-zon för det virtuella nätverket. En DNS A-post skapas också för Azure-resursen i den här DNS-zonen. Om du redan har distribuerat en privat slutpunkt i det här virtuella nätverket läggs en ny A-post för Azure-resurs till i den befintliga DNS-zonen. Det är valfritt att distribuera en DNS-zon, men rekommenderas starkt för att förenkla DNS-hanteringen som krävs.

> [!Note]  
> I den här artikeln används DNS-suffix för offentliga Azure-regioner, `core.windows.net` lagringskonton och `afs.azure.net` tjänster för synkronisering av lagring. Den här inställningen gäller även för nationella Azure-moln, till exempel Azure US Government-molnet– ersätter bara lämpliga suffix för din miljö.

### <a name="create-the-storage-account-private-endpoint"></a>Skapa lagringskontots privata slutpunkt
# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Om du har en virtuell dator i ditt virtuella nätverk eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i Konfigurera DNS-vidarebefordran för [Azure Files](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)kan du testa att din privata slutpunkt har konfigurerats korrekt genom att köra följande kommandon från PowerShell, kommandoraden eller terminalen (fungerar för Windows, Linux eller macOS). Du måste ersätta `<storage-account-name>` med lämpligt lagringskontonamn:

```console
nslookup <storage-account-name>.file.core.windows.net
```

Om allt har fungerat korrekt bör du se följande utdata, där är den privata IP-adressen för den privata slutpunkten i `192.168.0.5` ditt virtuella nätverk (utdata visas för Windows):

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

Om du har en virtuell dator i det virtuella nätverket eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i Konfigurera DNS-vidarebefordran för [Azure Files](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)kan du testa att din privata slutpunkt har konfigurerats korrekt med följande kommandon:

```powershell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Om allt har fungerat korrekt bör du se följande utdata, där är den privata `192.168.0.5` IP-adressen för den privata slutpunkten i det virtuella nätverket:

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

Om du har en virtuell dator i det virtuella nätverket eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i Konfigurera DNS-vidarebefordran för [Azure Files](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)kan du testa att din privata slutpunkt har konfigurerats korrekt med följande kommandon:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Om allt har fungerat korrekt bör du se följande utdata, där är den privata IP-adressen för `192.168.0.5` den privata slutpunkten i det virtuella nätverket:

```output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="create-the-storage-sync-service-private-endpoint"></a>Skapa den privata slutpunkten för tjänsten för synkronisering av lagring
> [!Important]  
> För att kunna använda privata slutpunkter på resursen för tjänsten för synkronisering av lagring måste du använda Azure File Sync agentversion 10.1 eller senare. Agentversioner före 10.1 stöder inte privata slutpunkter i tjänsten för synkronisering av lagring. Alla tidigare agentversioner stöder privata slutpunkter på lagringskontoresursen.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Navigera till **Private Link Center** genom *att Private Link* i sökfältet längst upp i Azure Portal. I innehållsförteckningen för den Private Link Center väljer du **Privata slutpunkter** och sedan **+** Lägg till för att skapa en ny privat slutpunkt.

[![En skärmbild av private link-centrum](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png)](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-0.png#lightbox)

Den resulterande guiden har flera sidor att slutföra.

På **bladet Grundläggande** väljer du önskad resursgrupp, namn och region för din privata slutpunkt. De kan vara vad du vill, de behöver inte matcha tjänsten för synkronisering av lagring på något sätt, men du måste skapa den privata slutpunkten i samma region som det virtuella nätverk som du vill skapa den privata slutpunkten i.

![En skärmbild av avsnittet Grundläggande i avsnittet Skapa privat slutpunkt](media/storage-sync-files-networking-endpoints/create-storage-sync-private-endpoint-1.png)

På **bladet** Resurs väljer du alternativknappen för **Anslut till en Azure-resurs i min katalog**. Under **Resurstyp väljer** du **Microsoft.StorageSync/storageSyncServices** som resurstyp. 

På **bladet** Konfiguration kan du välja det specifika virtuella nätverk och undernät som du vill lägga till din privata slutpunkt i. Välj samma virtuella nätverk som du använde för lagringskontot ovan. Bladet Konfiguration innehåller också information om hur du skapar/uppdaterar den privata DNS-zonen.

Klicka **på Granska + skapa** för att skapa den privata slutpunkten.

Du kan testa att den privata slutpunkten har ställts in korrekt genom att köra följande kommandon från PowerShell. 

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

Om allt har fungerat korrekt bör du se följande utdata där , , och är de privata IP-adresser som tilldelats `192.168.1.4` `192.168.1.5` till den privata `192.168.1.6` `192.168.1.7` slutpunkten:

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
Om du vill skapa en privat slutpunkt för tjänsten för synkronisering av lagring måste du först hämta en referens till tjänsten för synkronisering av lagring. Kom ihåg att `<storage-sync-service-resource-group>` ersätta och med rätt värden för din `<storage-sync-service>` miljö. Följande PowerShell-kommandon förutsätter att du använder redan har fyllt i informationen om det virtuella nätverket ovan. 

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

Om du vill skapa en privat slutpunkt måste du skapa en privat länktjänstanslutning till tjänsten för synkronisering av lagring. Den privata länkanslutningen är indata för att skapa den privata slutpunkten.

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

Genom att skapa en privat DNS-zon i Azure kan värdnamnen för tjänsten för synkronisering av lagring, till exempel , matchas till rätt privata IP-adresser för tjänsten för synkronisering av lagring i det `mysssmanagement.westus2.afs.azure.net` virtuella nätverket. Även om det är valfritt när det gäller att skapa en privat slutpunkt, krävs det uttryckligen att Azure File Sync agenten får åtkomst till tjänsten för synkronisering av lagring. 

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
Nu när du har en referens till den privata DNS-zonen måste du skapa A-poster för tjänsten för synkronisering av lagring.

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
Om du vill skapa en privat slutpunkt för tjänsten för synkronisering av lagring måste du först hämta en referens till tjänsten för synkronisering av lagring. Kom ihåg att `<storage-sync-service-resource-group>` ersätta och med rätt värden för din `<storage-sync-service>` miljö. Följande CLI-kommandon förutsätter att du använder redan har fyllt i informationen om det virtuella nätverket ovan. 

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

Om du vill skapa en privat slutpunkt måste du först se till att undernätets nätverksprincip för privata slutpunkter är inställd på inaktiverad. Sedan kan du skapa en privat slutpunkt med `az network private-endpoint create` kommandot .

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

Genom att skapa en privat DNS-zon i Azure kan värdnamnen för tjänsten för synkronisering av lagring, till exempel , matchas till rätt privata IP-adresser för tjänsten för synkronisering av lagring i det `mysssmanagement.westus2.afs.azure.net` virtuella nätverket. Även om det är valfritt när det gäller att skapa en privat slutpunkt, krävs det uttryckligen att Azure File Sync agenten får åtkomst till tjänsten för synkronisering av lagring. 

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

Nu när du har en referens till den privata DNS-zonen måste du skapa A-poster för tjänsten för synkronisering av lagring.

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

## <a name="restrict-access-to-the-public-endpoints"></a>Begränsa åtkomsten till offentliga slutpunkter
Du kan begränsa åtkomsten till de offentliga slutpunkterna för både lagringskontot och tjänsterna för synkronisering av lagring. Begränsa åtkomsten till den offentliga slutpunkten ger ytterligare säkerhet genom att se till att nätverkspaket endast accepteras från godkända platser. 

### <a name="restrict-access-to-the-storage-account-public-endpoint"></a>Begränsa åtkomsten till lagringskontots offentliga slutpunkt
Åtkomstbegränsning till den offentliga slutpunkten görs med hjälp av brandväggsinställningarna för lagringskontot. I allmänhet begränsar de flesta brandväggsprinciper för ett lagringskonto nätverksåtkomst till ett eller flera virtuella nätverk. Det finns två sätt att begränsa åtkomsten till ett lagringskonto till ett virtuellt nätverk:

- [Skapa en eller flera privata slutpunkter för lagringskontot och](#create-the-storage-account-private-endpoint) inaktivera åtkomst till den offentliga slutpunkten. Detta säkerställer att endast trafik som kommer inifrån de önskade virtuella nätverken kan komma åt Azure-filresurser i lagringskontot.
- Begränsa den offentliga slutpunkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter.* När du begränsar trafiken till ett lagringskonto via en tjänstslutpunkt kommer du fortfarande åt lagringskontot via den offentliga IP-adressen.

#### <a name="disable-access-to-the-storage-account-public-endpoint"></a>Inaktivera åtkomst till den offentliga slutpunkten för lagringskontot
När åtkomsten till den offentliga slutpunkten är inaktiverad kan lagringskontot fortfarande nås via dess privata slutpunkter. Annars avvisas giltiga begäranden till lagringskontots offentliga slutpunkt. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-storage-account-public-endpoint-to-specific-virtual-networks"></a>Begränsa åtkomsten till lagringskontots offentliga slutpunkt till specifika virtuella nätverk
När du begränsar lagringskontot till specifika virtuella nätverk tillåter du begäranden till den offentliga slutpunkten inifrån de angivna virtuella nätverken. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter.* Detta kan användas med eller utan privata slutpunkter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

### <a name="disable-access-to-the-storage-sync-service-public-endpoint"></a>Inaktivera åtkomst till den offentliga slutpunkten för tjänsten för synkronisering av lagring
Azure File Sync kan du begränsa åtkomsten till specifika virtuella nätverk endast via privata slutpunkter. Azure File Sync stöder inte tjänstslutpunkter för att begränsa åtkomsten till den offentliga slutpunkten till specifika virtuella nätverk. Det innebär att de två tillstånden för tjänsten för synkronisering av lagring är aktiverade och inaktiverade.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Detta är inte möjligt via Azure Portal. Välj fliken Azure PowerShell för att få instruktioner om hur du inaktiverar den offentliga slutpunkten för tjänsten för synkronisering av lagring. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill inaktivera åtkomsten till den offentliga slutpunkten för tjänsten för synkronisering av lagring anger vi egenskapen för tjänsten för synkronisering `incomingTrafficPolicy` av lagring till `AllowVirtualNetworksOnly` . Om du vill aktivera åtkomst till den offentliga slutpunkten för tjänsten för synkronisering av lagring anger du `incomingTrafficPolicy` till `AllowAllTraffic` i stället. Kom ihåg att ersätta `<storage-sync-service-resource-group>` och `<storage-sync-service>` .

```powershell
$storageSyncServiceResourceGroupName = "<storage-sync-service-resource-group>"
$storageSyncServiceName = "<storage-sync-service>"

Set-AzStorageSyncService `
    -ResourceGroupName $storageSyncServiceResourceGroupName `
    -Name $storageSyncServiceName `
    -IncomingTrafficPolicy AllowVirtualNetworksOnly
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI stöder inte inställning av egenskapen `incomingTrafficPolicy` i tjänsten för synkronisering av lagring. Välj fliken Azure PowerShell för att få anvisningar om hur du inaktiverar den offentliga slutpunkten för tjänsten för synkronisering av lagring.

---

## <a name="azure-policy"></a>Azure Policy
Azure Policy hjälper till att framtvinga organisationsstandarder och utvärdera efterlevnaden mot dessa standarder i stor skala. Azure Files och Azure File Sync exponerar flera användbara principer för gransknings- och reparationsnätverk som hjälper dig att övervaka och automatisera distributionen.

Principer granskar din miljö och varnar dig om dina lagringskonton eller synkroniseringstjänster för lagring skiljer sig från det definierade beteendet. Om till exempel en offentlig slutpunkt är aktiverad när principen var inställd på att de offentliga slutpunkterna skulle inaktiveras. Ändra/distribuera principer tar saker ett steg längre och ändrar proaktivt en resurs (till exempel tjänsten för synkronisering av lagring) eller distribuerar resurser (till exempel privata slutpunkter) för att anpassa den till principerna.

Följande fördefinierade principer är tillgängliga för Azure Files och Azure File Sync:

| Action | Tjänst | Villkor | Principnamn |
|-|-|-|-|
| Granska | Azure Files | Lagringskontots offentliga slutpunkt är aktiverad. Mer [information finns i Inaktivera åtkomst till lagringskontots](#disable-access-to-the-storage-account-public-endpoint) offentliga slutpunkt. | Lagringskonton bör begränsa nätverksåtkomsten |
| Granska | Azure File Sync | Den offentliga slutpunkten för tjänsten för synkronisering av lagring är aktiverad. Mer [information finns i Inaktivera åtkomst till den offentliga slutpunkten för tjänsten för](#disable-access-to-the-storage-sync-service-public-endpoint) synkronisering av lagring. | Offentlig nätverksåtkomst ska inaktiveras för Azure File Sync |
| Granska | Azure Files | Lagringskontot behöver minst en privat slutpunkt. Mer information [finns i Skapa den privata slutpunkten för](#create-the-storage-account-private-endpoint) lagringskontot. | Lagringskontot bör använda en privat länkanslutning |
| Granska | Azure File Sync | Tjänsten för synkronisering av lagring behöver minst en privat slutpunkt. Mer [information finns i Skapa den privata slutpunkten för tjänsten för](#create-the-storage-sync-service-private-endpoint) synkronisering av lagring. | Azure File Sync bör använda private link |
| Ändra | Azure File Sync | Inaktivera den offentliga slutpunkten för tjänsten för synkronisering av lagring. | Ändra – Konfigurera Azure File Sync inaktivera offentlig nätverksåtkomst |
| Distribuera | Azure File Sync | Distribuera en privat slutpunkt för tjänsten för synkronisering av lagring. | Konfigurera Azure File Sync med privata slutpunkter |
| Distribuera | Azure File Sync | Distribuera en A-post till privatelink.afs.azure.net DNS-zon. | Konfigurera Azure File Sync att använda privata DNS-zoner |

### <a name="set-up-a-private-endpoint-deployment-policy"></a>Konfigurera en distributionsprincip för privata slutpunkter
Om du vill konfigurera en distributionsprincip för privata [slutpunkter går du Azure Portal](https://portal.azure.com/)och söker efter **Princip**. Det Azure Policy center bör vara ett toppresultat. Gå till  >  **Redigeringsdefinitioner** i innehållsförteckningen i Principcenter. Fönstret Definitioner **som** visas innehåller fördefinierade principer för alla Azure-tjänster. Om du vill hitta den specifika principen väljer du kategorin **Lagring** i kategorifiltret eller söker efter **Konfigurera Azure File Sync med privata slutpunkter**. Välj **...** och **Tilldela** för att skapa en ny princip från definitionen.

På **bladet** Grundläggande  inställningar i guiden Tilldela princip kan du ange ett omfång, en resurs eller en undantagslista för resursgrupper och ge principen ett eget namn som hjälper dig att särskilja den. Du behöver inte ändra dessa för att principen ska fungera, men det kan du om du vill göra ändringar. Välj **Nästa** för att gå till **sidan** Parametrar. 

På **bladet** Parametrar väljer du **...** bredvid listrutan **privateEndpointSubnetId** för att välja det virtuella nätverket och undernätet där de privata slutpunkterna för dina Storage Sync Service-resurser ska distribueras. Den resulterande guiden kan ta flera sekunder att läsa in de tillgängliga virtuella nätverken i din prenumeration. Välj lämpligt virtuellt nätverk/undernät för din miljö och klicka på **Välj**. Välj **Nästa** för att gå **vidare till bladet** Reparation.

För att den privata slutpunkten ska kunna distribueras när en tjänst för  synkronisering av lagring utan en privat slutpunkt identifieras måste du välja uppgiften Skapa en reparation **på sidan** Reparation. Välj slutligen Granska **+ skapa för att** granska principtilldelningen och Skapa **för** att skapa den.

Den resulterande principtilldelningen körs regelbundet och körs kanske inte omedelbart efter att den har skapats.

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](file-sync-planning.md)
- [Distribuera Azure File Sync](file-sync-deployment-guide.md)
