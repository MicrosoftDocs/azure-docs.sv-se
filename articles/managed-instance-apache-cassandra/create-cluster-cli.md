---
title: Snabbstart – Använda CLI för att skapa Azure Managed Instance för Apache Cassandra-kluster
description: Använd den här snabbstarten för att skapa en Hanterad Azure-instans för Apache Cassandra-kluster med hjälp av Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: 53fe53e1406bfcde1f2d8c7b2a1ce8369303426f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379394"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Snabbstart: Skapa en Hanterad Azure-instans för Apache Cassandra-kluster med Hjälp av Azure CLI (förhandsversion)

Azure Managed Instance för Apache Cassandra tillhandahåller automatiserade distributioner och skalningsåtgärder för hanterade Apache Cassandra-datacenter med öppen källkod. Den här tjänsten hjälper dig att påskynda hybridscenarier och minska pågående underhåll.

> [!IMPORTANT]
> Azure Managed Instance för Apache Cassandra finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabbstarten visar hur du använder Azure CLI-kommandon för att skapa ett kluster med Azure Managed Instance för Apache Cassandra. Den visar också hur du skapar ett datacenter och skalar noder uppåt eller nedåt i datacentret.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) med anslutning till din lokala eller lokala miljö. Mer information om hur du ansluter lokala miljöer till Azure finns i [artikeln Ansluta ett lokalt nätverk till Azure.](/azure/architecture/reference-architectures/hybrid-networking/)

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Den här artikeln kräver Azure CLI version 2.17.1 eller senare. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Skapa ett hanterat instanskluster

1. Logga in på [Azure-portalen](https://portal.azure.com/)

1. Ange ditt prenumerations-ID i Azure CLI:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Skapa sedan en Virtual Network med ett dedikerat undernät i resursgruppen:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```
    > [!NOTE]
    > Distributionen av en Hanterad Azure-instans för Apache Cassandra kräver Internetåtkomst. Distributionen misslyckas i miljöer där Internetåtkomsten är begränsad. Kontrollera att du inte blockerar åtkomsten i ditt VNet till följande viktiga Azure-tjänster som krävs för att Managed Cassandra ska fungera korrekt:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Säkerhet i Azure

1. Tillämpa vissa särskilda behörigheter på Virtual Network som krävs av den hanterade instansen. Använd kommandot `az role assignment create` och ersätt , och med lämpliga `<subscription ID>` `<resource group name>` `<VNet name>` värden:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Värdena `assignee` och i föregående kommando är fasta `role` värden. Ange dessa värden exakt som anges i kommandot. Om du inte gör det uppstår fel när klustret skapas. Om du stöter på fel när du kör det här kommandot kanske du inte har behörighet att köra det. Kontakta administratören om behörighet.

1. Skapa sedan klustret i det nya Virtual Network med kommandot [az managed-cassandra cluster create.](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) Kör följande kommando för `delegatedManagementSubnetId` variabelvärdet:

   > [!NOTE]
   > Värdet för `delegatedManagementSubnetId` variabeln som du kommer att ange nedan är exakt samma som värdet `--scope` för som du angav i kommandot ovan:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Skapa slutligen ett datacenter för klustret med tre noder med kommandot [az managed-cassandra datacenter create:](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create)

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. Om du vill skala upp eller skala ned noderna i datacentret när datacentret har skapats kör du [kommandot az managed-cassandra datacenter update.](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) Ändra värdet för `node-count` parametern till önskat värde:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>Ansluta till klustret

Azure Managed Instance för Apache Cassandra skapar inte noder med offentliga IP-adresser. Om du vill ansluta till ditt nyligen skapade Cassandra-kluster måste du skapa en annan resurs i det virtuella nätverket. Den här resursen kan vara ett program eller en virtuell dator med Apaches frågeverktyg med öppen källkod [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) installerat. Du kan använda en [Resource Manager för att](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) distribuera en virtuell Ubuntu-dator. När den har distribuerats använder du SSH för att ansluta till datorn och installera CQLSH enligt följande kommandon:

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på ett fel när du tillämpar behörigheter på din Virtual Network, till exempel Cannot find user or *service principal in graph database for 'e5007d2c-4b13-4a74-9b6a-605d99f03501'*, kan du tillämpa samma behörighet manuellt från Azure Portal. Om du vill tillämpa behörigheter från portalen går du till fönstret Åtkomstkontroll **(IAM)** i ditt befintliga virtuella nätverk och lägger till en rolltilldelning för "Azure Cosmos DB" till rollen Nätverksadministratör. Om två poster visas när du söker efter "Azure Cosmos DB" lägger du till båda posterna enligt följande bild: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Tillämpa behörigheter" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Den Azure Cosmos DB rolltilldelningen används endast i distributionssyfte. Azure Managed Instanced för Apache Cassandra har inga backend-beroenden på Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du använda kommandot `az group delete` för att ta bort resursgruppen, den hanterade instansen och alla relaterade resurser:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en Azure Managed Instance för Apache Cassandra-kluster med Azure CLI. Nu kan du börja arbeta med klustret:

> [!div class="nextstepaction"]
> [Distribuera ett hanterat Apache Spark kluster med Azure Databricks](deploy-cluster-databricks.md)