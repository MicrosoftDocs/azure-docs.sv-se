---
title: Snabb start – konfigurera ett hybrid kluster med Azure Managed instance för Apache Cassandra
description: Den här snabb starten visar hur du konfigurerar ett hybrid kluster med en Azure-hanterad instans för Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: b022bff9db87c248881cd18cc21569aaef8f404a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562145"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Snabb start: Konfigurera ett hybrid kluster med en Azure-hanterad instans för Apache Cassandra (för hands version)

Azure Managed instance för Apache Cassandra tillhandahåller automatiserade distributions-och skalnings åtgärder för hanterade Apache Cassandra-datacenter med öppen källkod. Den här tjänsten hjälper dig att påskynda hybrid scenarier och minska det löpande underhållet.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabb starten visar hur du använder Azure CLI-kommandon för att konfigurera ett hybrid kluster. Om du har befintliga data Center i en lokal eller lokal miljö kan du använda Azure Managed instance för Apache Cassandra för att lägga till andra data Center i klustret och underhålla dem.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Den här artikeln kräver Azure CLI-version 2.12.1 eller senare. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) med anslutning till din egen värd eller lokala miljö. Mer information om hur du ansluter lokala miljöer till Azure finns i artikeln [ansluta ett lokalt nätverk till Azure](/azure/architecture/reference-architectures/hybrid-networking/) .

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Konfigurera ett hybrid kluster

1. Logga in på [Azure Portal](https://portal.azure.com/) och navigera till din Virtual Network-resurs.

1. Öppna fliken **undernät** och skapa ett nytt undernät. Mer information om fälten i formuläret för att **lägga till undernät** finns i [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) artikeln:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Lägg till ett nytt undernät till din Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

1. Nu ska vi tillämpa vissa särskilda behörigheter för det virtuella nätverket och under nätet som Cassandra-hanterade instanser kräver, med hjälp av Azure CLI. Använd `az role assignment create` kommandot, Ersätt `<subscription ID>` ,, `<resource group name>` `<VNet name>` och `<subnet name>` med lämpliga värden:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Värdena och `role` i föregående kommando är fasta tjänst principer respektive roll identifierare.

1. Nu ska vi konfigurera resurser för vårt hybrid kluster. Eftersom du redan har ett kluster, är kluster namnet här bara en logisk resurs för att identifiera namnet på ditt befintliga kluster. Se till att använda namnet på ditt befintliga kluster när du definierar `clusterName` och `clusterNameOverride` variabler i följande skript. Du behöver också startnoder, offentliga klient certifikat (om du har konfigurerat en offentlig/privat nyckel på din Cassandra-slutpunkt) och Gossip-certifikat för det befintliga klustret.

   > [!NOTE]
   > Värdet för `delegatedManagementSubnetId` variabeln som du anger nedan är exakt detsamma som värdet för `--scope` som du angav i kommandot ovan:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster-legal-name'
   clusterNameOverride='cassandra-hybrid-cluster-illegal-name'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
    
   # You can override the cluster name if the original name is not legal for an Azure resource:
   # overrideClusterName='ClusterNameIllegalForAzureResource'
   # the default cassandra version will be v3.11
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --external-seed-nodes 10.52.221.2,10.52.221.3,10.52.221.4
      --client-certificates 'BEGIN CERTIFICATE-----\n...PEM format..\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format...\n-----END CERTIFICATE-----' \
      --external-gossip-certificates 'BEGIN CERTIFICATE-----\n...PEM format 1...\n-----END CERTIFICATE-----','BEGIN CERTIFICATE-----\n...PEM format 2...\n-----END CERTIFICATE-----'
   ```

    > [!NOTE]
    > Du bör veta var dina befintliga offentliga och/eller Gossip-certifikat behålls. Om du är osäker bör du kunna köra `keytool -list -keystore <keystore-path> -rfc -storepass <password>` för att skriva ut certifikaten. 

1. När kluster resursen har skapats kör du följande kommando för att hämta information om kluster konfigurationen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Föregående kommando returnerar information om den hanterade instans miljön. Du behöver Gossip-certifikat så att du kan installera dem på noderna i ditt befintliga data Center. Följande skärm bild visar utdata från föregående kommando och formatet för certifikat:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Hämta certifikat information från klustret." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Skapa sedan ett nytt Data Center i hybrid klustret. Se till att ersätta variabel värden med kluster informationen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 9 
   ```

1. Nu när det nya data centret har skapats kör du kommandot show datacenter för att visa information:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Föregående kommando matar ut det nya datacenter-startnoderna. Lägg till de nya data centrets startnoder i din befintliga data centers konfiguration i filen *Cassandra. yaml* . Och installera de hanterade instans Gossip-certifikat som du har samlat in tidigare:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Information om att hämta data Center." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Om du vill lägga till fler data Center kan du upprepa stegen ovan, men du behöver bara dirigera-noderna. 

1. Använd slutligen följande CQL-fråga för att uppdatera replikeringsprincipen i varje-utrymme för att ta med alla data Center i klustret:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Du måste också uppdatera lösen ords tabellerna:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Felsökning

Om det uppstår ett fel när du tillämpar behörigheter på Virtual Network, till exempel *inte kan hitta användare eller tjänstens huvud namn i graf-databasen för "e5007d2c-4b13-4a74-9b6a-605d99f03501"*, kan du använda samma behörighet manuellt från Azure Portal. Om du vill tillämpa behörigheter från portalen går du till fönstret **åtkomst kontroll (IAM)** i ditt befintliga virtuella nätverk och lägger till en roll tilldelning för "Azure Cosmos dB" i rollen "nätverks administratör". Om två poster visas när du söker efter "Azure Cosmos DB" lägger du till båda posterna som visas i följande bild: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Tillämpa behörigheter" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Roll tilldelningen Azure Cosmos DB används endast i distributions syfte. Azure-hanterad instans för Apache Cassandra har inga Server dels beroenden på Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här hanterade instans klustret, tar du bort det med följande steg:

1. Välj **resurs grupper** på den vänstra menyn i Azure Portal.
1. I listan väljer du den resurs grupp som du skapade för den här snabb starten.
1. I **översikts** fönstret resurs grupp väljer du **ta bort resurs grupp**.
3. I nästa fönster anger du namnet på den resurs grupp som ska tas bort och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett hybrid kluster med Azure CLI och Azure Managed instance för Apache Cassandra. Nu kan du börja arbeta med klustret.

> [!div class="nextstepaction"]
> [Hantera Azure-hanterad instans för Apache Cassandra-resurser med hjälp av Azure CLI](manage-resources-cli.md)
