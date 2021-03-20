---
title: Azure CLI – begränsa import/export-åtkomst till hanterade diskar med privata länkar
description: Aktivera privata Länkar för dina hanterade diskar med Azure CLI. Gör det möjligt att exportera och importera diskar på ett säkert sätt inom bara ditt virtuella nätverk.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684533"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI – begränsa import/export-åtkomst för hanterade diskar med privata länkar

Du kan använda [privata slut punkter](../../private-link/private-endpoint-overview.md) för att begränsa exporten och importen av hanterade diskar och säker åtkomst till data via en [privat länk](../../private-link/private-link-overview.md) från klienter i ditt virtuella Azure-nätverk. Den privata slut punkten använder en IP-adress från det virtuella nätverkets adress utrymme för tjänsten Managed disks. Nätverks trafik mellan klienter på sina virtuella nätverk och hanterade diskar passerar bara över det virtuella nätverket och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponering från det offentliga Internet.

Om du vill använda privata Länkar för att exportera/importera hanterade diskar, skapar du först en disk åtkomst resurs och länkar den till ett virtuellt nätverk i samma prenumeration genom att skapa en privat slut punkt. Associera sedan en disk eller en ögonblicks bild med en instans av disk åtkomst. Ange slutligen egenskapen NetworkAccessPolicy för disken eller ögonblicks bilden till `AllowPrivate` . Detta begränsar åtkomsten till det virtuella nätverket. 

Du kan ställa in egenskapen NetworkAccessPolicy så att `DenyAll` vem inte exporterar data från en disk eller en ögonblicks bild. Standardvärdet för egenskapen NetworkAccessPolicy är `AllowAll` .

## <a name="limitations"></a>Begränsningar

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Logga in i din prenumeration och ange dina variabler

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Skapa en disk åtkomst med Azure CLI
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Nätverks principer som nätverks säkerhets grupper (NSG) stöds inte för privata slut punkter. För att kunna distribuera en privat slut punkt i ett angivet undernät krävs en explicit inaktivera inställning i det under nätet. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Inaktivera privata slut punkts principer för undernät

Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste uppdatera under nätet för att inaktivera nätverks principer för privata slut punkter. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Skapa en privat slut punkt för disk åtkomst objekt

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Konfigurera Privat DNS zon

Skapa en Privat DNS zon för Storage BLOB-domän, skapa en kopplings länk med Virtual Network och skapa en DNS-zon grupp för att koppla den privata slut punkten till Privat DNS zonen. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Skapa en disk som är skyddad med privata länkar
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Skapa en ögonblicks bild av en disk som skyddas med privata länkar
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar om privata länkar](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportera/kopiera hanterade ögonblicksbilder som VHD till ett lagringskonto i annan region med CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)