---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 6/2/2020
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d3d2afa3b02d4ab4524d9b5c5d5f981cddebe1a9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511595"
---
För att begränsa åtkomsten till lagringskontots offentliga slutpunkt till specifika virtuella nätverk med hjälp av tjänstslutpunkter måste vi först samla in information om lagringskontot och det virtuella nätverket. Fyll i `<storage-account-resource-group>` , , , och för att samla in den här `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>` `<subnet-name>` informationen.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

För att trafik från det virtuella nätverket ska tillåtas av Azure-nätverks fabric att komma till den offentliga slutpunkten för lagringskontot måste det virtuella nätverkets undernät ha `Microsoft.Storage` tjänstslutpunkten exponerad. Följande CLI-kommandon lägger till `Microsoft.Storage` tjänstslutpunkten i undernätet om den inte redan finns där.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

Det sista steget i att begränsa trafiken till lagringskontot är att skapa en nätverksregel och lägga till den i lagringskontots nätverksregeluppsättning.

```azurecli
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```
