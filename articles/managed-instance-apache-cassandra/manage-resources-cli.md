---
title: Hantera Azure Managed Instance för Apache Cassandra-resurser med Hjälp av Azure CLI
description: Lär dig mer om vanliga kommandon för att automatisera hanteringen av din Azure Managed Instance för Apache Cassandra med Hjälp av Azure CLI.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/15/2021
ms.author: thvankra
ms.openlocfilehash: ea28bf21424f0624b4f1bb5856a17672c1c7b106
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875457"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Hantera Azure Managed Instance för Apache Cassandra-resurser med Azure CLI (förhandsversion)

I den här artikeln beskrivs vanliga kommandon för att automatisera hanteringen av azure-hanterade instanser för Apache Cassandra-kluster med hjälp av Azure CLI.

> [!IMPORTANT]
> Azure Managed Instance för Apache Cassandra finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Den här artikeln kräver Azure CLI version 2.17.1 eller senare. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.
>
> Det går inte att byta namn på Hantera Azure Managed Instance för Apache Cassandra-resurser eftersom det strider mot Azure Resource Manager fungerar med resurs-URI:er.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Azure Managed Instance för Apache Cassandra-kluster

Följande avsnitt visar hur du hanterar Azure Managed Instance för Apache Cassandra-kluster, inklusive:

* [Skapa ett hanterat instanskluster](#create-cluster)
* [Ta bort ett hanterat instanskluster](#delete-cluster)
* [Hämta klusterinformationen](#get-cluster-details)
* [Hämta status för klusternoden](#get-cluster-status)
* [Lista kluster efter resursgrupp](#list-clusters-resource-group)
* [Lista kluster efter prenumerations-ID](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Skapa ett hanterat instanskluster

Skapa en Azure Managed Instance för Apache Cassandra-kluster med kommandot [az managed-cassandra cluster create:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>Ta bort ett hanterat instanskluster

Ta bort ett kluster med kommandot [az managed-cassandra cluster delete:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>Hämta klusterinformationen

Hämta klusterinformation med hjälp av [kommandot az managed-cassandra cluster show:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>Hämta status för klusternoden

Hämta klusterinformation med hjälp av [kommandot az managed-cassandra cluster node-status:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_node_status)

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>Lista klustren efter resursgrupp

Visa en lista över kluster efter resursgrupp med [kommandot az managed-cassandra cluster list:](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_list)

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>Lista kluster efter prenumerations-ID

Visa en lista över kluster efter prenumerations-ID med hjälp av [kommandot az managed-cassandra cluster list:](/cli/azure/managed-cassandra?view=azure-cli-latest&preserve-view=true)

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>Datacenter för hanterade instanser

Följande avsnitt visar hur du hanterar Azure Managed Instance för Apache Cassandra-datacenter, inklusive:

* [Skapa ett datacenter](#create-datacenter)
* [Ta bort ett datacenter](#delete-datacenter)
* [Hämta information om datacenter](#get-datacenter-details)
* [Uppdatera eller skala ett datacenter](#update-datacenter)
* [Hämta datacenter i ett kluster](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>Skapa ett datacenter

Skapa ett datacenter med kommandot [az managed-cassandra datacenter create:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>Ta bort ett datacenter

Ta bort ett datacenter med kommandot [az managed-cassandra datacenter delete:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_delete)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>Hämta information om datacenter

Hämta datacenterinformation med hjälp av [kommandot az managed-cassandra datacenter show:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_show)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>Uppdatera eller skala ett datacenter

Uppdatera eller skala ett datacenter (för att skala ändra nodeCount-värde) med hjälp av [kommandot az managed-cassandra datacenter update:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>Hämta datacenter i ett kluster

Hämta datacenter i ett kluster med kommandot [az managed-cassandra datacenter list:](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_list)

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>Nästa steg

* [Skapa ett hanterat instanskluster från Azure Portal](create-cluster-portal.md)
* [Distribuera ett hanterat Apache Spark kluster med Azure Databricks](deploy-cluster-databricks.md)
