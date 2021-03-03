---
title: Snabb start – Använd CLI för att skapa en Azure-hanterad instans för Apache Cassandra-kluster
description: Använd den här snabb starten för att skapa en Azure-hanterad instans för Apache Cassandra-kluster med Azure CLI.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 510fcf48091266af255c15aced80651619133aab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748941"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>Snabb start: skapa en Azure-hanterad instans för Apache Cassandra-kluster med hjälp av Azure CLI (för hands version)

Azure Managed instance för Apache Cassandra tillhandahåller automatiserade distributions-och skalnings åtgärder för hanterade Apache Cassandra-datacenter med öppen källkod. Den här tjänsten hjälper dig att påskynda hybrid scenarier och minska det löpande underhållet.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabb starten visar hur du använder Azure CLI-kommandon för att skapa ett kluster med Azure Managed instance för Apache Cassandra. Den visar också för att skapa ett Data Center och skala upp eller ned noder i data centret.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Den här artikeln kräver Azure CLI-version 2.12.1 eller senare. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) med anslutning till din egen värd eller lokala miljö. Mer information om hur du ansluter lokala miljöer till Azure finns i artikeln [ansluta ett lokalt nätverk till Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) .

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

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

1. Använd vissa särskilda behörigheter för Virtual Network och under nätet som krävs av den hanterade instansen. För att göra det måste du hämta resurs-ID: t för din befintliga Virtual Network. Kör följande kommando och kopiera värdet för `Resource ID` parametern:

   ```azurecli-interactive
   # get the resource ID of the Virtual Network
   az network vnet show -n <VNet_name> -g <Resource_Group_Name> --query "id" --output tsv

1. Now apply the special permissions by using the `az role assignment create` command. Use the `Resource ID` parameter from the output of previous command to the `scope` parameter:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```

   > [!NOTE]
   > `assignee`Värdena och `role` i föregående kommando är fasta tjänst principer respektive roll identifierare.

1. Skapa sedan klustret i det nyskapade Virtual Network. Kör följande kommando och se till att du använder det `Resource ID` värde som hämtades i föregående kommando som `delegatedManagementSubnetId` variabelns värde:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. Skapa slutligen ett Data Center för klustret med tre noder:

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. När data centret har skapats, om du vill skala upp eller skala ned noderna i data centret kör du följande kommando. Ändra värdet för `node-count` parametern till önskat värde:

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
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

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda `az group delete` kommandot för att ta bort resurs gruppen, den hanterade instansen och alla relaterade resurser:

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-hanterad instans för Apache Cassandra-kluster med hjälp av Azure CLI. Nu kan du börja arbeta med klustret:

> [!div class="nextstepaction"]
> [Distribuera ett hanterat Apache Spark-kluster med Azure Databricks](deploy-cluster-databricks.md)
