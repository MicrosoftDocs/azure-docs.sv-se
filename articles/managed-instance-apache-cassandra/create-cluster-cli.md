---
title: Snabb start – Använd CLI för att skapa en Azure-hanterad instans för Apache Cassandra-kluster
description: Använd den här snabb starten för att skapa en Azure-hanterad instans för Apache Cassandra-kluster med Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/15/2021
ms.openlocfilehash: b719310a331044df363efcc6b79be323faf49247
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562111"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Snabb start: skapa en Azure-hanterad instans för Apache Cassandra-kluster med hjälp av Azure CLI (för hands version)

Azure Managed instance för Apache Cassandra tillhandahåller automatiserade distributions-och skalnings åtgärder för hanterade Apache Cassandra-datacenter med öppen källkod. Den här tjänsten hjälper dig att påskynda hybrid scenarier och minska det löpande underhållet.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabb starten visar hur du använder Azure CLI-kommandon för att skapa ett kluster med Azure Managed instance för Apache Cassandra. Den visar också för att skapa ett Data Center och skala upp eller ned noder i data centret.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) med anslutning till din egen värd eller lokala miljö. Mer information om hur du ansluter lokala miljöer till Azure finns i artikeln [ansluta ett lokalt nätverk till Azure](/azure/architecture/reference-architectures/hybrid-networking/) .

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Den här artikeln kräver Azure CLI-version 2.17.1 eller senare. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>Skapa ett hanterat instans kluster

1. Logga in på [Azure-portalen](https://portal.azure.com/)

1. Ange ditt prenumerations-ID i Azure CLI:

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. Skapa sedan en Virtual Network med ett dedikerat undernät i resurs gruppen:

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. Använd vissa särskilda behörigheter för Virtual Network och under nätet som krävs av den hanterade instansen. Använd `az role assignment create` kommandot, Ersätt `<subscription ID>` ,, `<resource group name>` `<VNet name>` och `<subnet name>` med lämpliga värden:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Värdena och `role` i föregående kommando är fasta värden, ange värdena exakt som de anges i kommandot. Om du inte gör det kommer det att leda till fel när klustret skapas. Om du stöter på fel när du kör det här kommandot kanske du inte har behörighet att köra det, kontakta administratören för behörigheter.

1. Skapa sedan klustret i det nyligen skapade Virtual Network med hjälp av kommandot [AZ Managed-Cassandra Cluster Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_cluster_create) . Kör följande kommando som värde för `delegatedManagementSubnetId` variabeln:

   > [!NOTE]
   > Värdet för `delegatedManagementSubnetId` variabeln som du anger nedan är exakt detsamma som värdet för `--scope` som du angav i kommandot ovan:

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

1. Skapa slutligen ett Data Center för klustret med tre noder genom att använda kommandot [AZ Managed-Cassandra Data Center Create](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_create) :

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

1. När data centret har skapats, om du vill skala upp eller skala ned noderna i data centret, kör du kommandot [AZ Managed-Cassandra Data Center Update](/cli/azure/ext/cosmosdb-preview/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#ext_cosmosdb_preview_az_managed_cassandra_datacenter_update) . Ändra värdet för `node-count` parametern till önskat värde:

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

## <a name="connect-to-your-cluster"></a>Anslut till klustret

Den Azure-hanterade instansen för Apache Cassandra skapar inte noder med offentliga IP-adresser. Om du vill ansluta till ditt nyligen skapade Cassandra-kluster måste du skapa en annan resurs i det virtuella nätverket. Den här resursen kan vara ett program eller en virtuell dator med Apache: s [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) -verktyg för öppen källkod installerad. Du kan använda en [Resource Manager-mall](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) för att distribuera en virtuell Ubuntu-dator. När den har distribuerats använder du SSH för att ansluta till datorn och installerar CQLSH, som du ser i följande kommandon:

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

Om det uppstår ett fel när du tillämpar behörigheter på Virtual Network, till exempel *inte kan hitta användare eller tjänstens huvud namn i graf-databasen för "e5007d2c-4b13-4a74-9b6a-605d99f03501"*, kan du använda samma behörighet manuellt från Azure Portal. Om du vill tillämpa behörigheter från portalen går du till fönstret **åtkomst kontroll (IAM)** i ditt befintliga virtuella nätverk och lägger till en roll tilldelning för "Azure Cosmos dB" i rollen "nätverks administratör". Om två poster visas när du söker efter "Azure Cosmos DB" lägger du till båda posterna som visas i följande bild: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Tillämpa behörigheter" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Roll tilldelningen Azure Cosmos DB används endast i distributions syfte. Azure-hanterad instans för Apache Cassandra har inga Server dels beroenden på Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda `az group delete` kommandot för att ta bort resurs gruppen, den hanterade instansen och alla relaterade resurser:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-hanterad instans för Apache Cassandra-kluster med hjälp av Azure CLI. Nu kan du börja arbeta med klustret:

> [!div class="nextstepaction"]
> [Distribuera ett hanterat Apache Spark-kluster med Azure Databricks](deploy-cluster-databricks.md)