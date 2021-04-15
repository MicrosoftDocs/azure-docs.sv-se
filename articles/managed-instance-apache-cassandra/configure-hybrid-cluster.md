---
title: Snabbstart – Konfigurera ett hybridkluster med Azure Managed Instance för Apache Cassandra
description: Den här snabbstarten visar hur du konfigurerar ett hybridkluster med Azure Managed Instance för Apache Cassandra.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 9f3ad2a5d5b275ff611653855eff73bd36afda9f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379425"
---
# <a name="quickstart-configure-a-hybrid-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>Snabbstart: Konfigurera ett hybridkluster med Azure Managed Instance för Apache Cassandra (förhandsversion)

Azure Managed Instance för Apache Cassandra tillhandahåller automatiserade distributioner och skalningsåtgärder för hanterade Apache Cassandra-datacenter med öppen källkod. Den här tjänsten hjälper dig att påskynda hybridscenarier och minska pågående underhåll.

> [!IMPORTANT]
> Azure Managed Instance för Apache Cassandra finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabbstarten visar hur du använder Azure CLI-kommandon för att konfigurera ett hybridkluster. Om du har befintliga datacenter i en lokal eller lokal miljö kan du använda Azure Managed Instance för Apache Cassandra för att lägga till andra datacenter i klustret och underhålla dem.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* Den här artikeln kräver Azure CLI version 2.12.1 eller senare. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) med anslutning till din lokala eller lokala miljö. Mer information om hur du ansluter lokala miljöer till Azure finns i [artikeln Ansluta ett lokalt nätverk till Azure.](/azure/architecture/reference-architectures/hybrid-networking/)

## <a name="configure-a-hybrid-cluster"></a><a id="create-account"></a>Konfigurera ett hybridkluster

1. Logga in på [Azure Portal](https://portal.azure.com/) och gå till din Virtual Network resurs.

1. Öppna fliken **Undernät** och skapa ett nytt undernät. Mer information om fälten i formuläret **Lägg till undernät** finns i [Virtual Network](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) artikeln:

   :::image type="content" source="./media/configure-hybrid-cluster/subnet.png" alt-text="Lägg till ett nytt undernät i Virtual Network." lightbox="./media/configure-hybrid-cluster/subnet.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/subnet.png) -->

    > [!NOTE]
    > Distributionen av en Hanterad Azure-instans för Apache Cassandra kräver Internetåtkomst. Distributionen misslyckas i miljöer där Internetåtkomsten är begränsad. Kontrollera att du inte blockerar åtkomsten i ditt VNet till följande viktiga Azure-tjänster som krävs för att Managed Cassandra ska fungera korrekt:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Säkerhet i Azure

1. Nu ska vi tillämpa vissa särskilda behörigheter för det virtuella nätverket och undernätet som Cassandra Managed Instance kräver med hjälp av Azure CLI. Använd kommandot `az role assignment create` och ersätt , och med lämpliga `<subscription ID>` `<resource group name>` `<VNet name>` värden:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Värdena `assignee` och i föregående kommando är fasta `role` tjänstprinciper respektive rollidentifierare.

1. Nu ska vi konfigurera resurser för vårt hybridkluster. Eftersom du redan har ett kluster är klusternamnet här bara en logisk resurs för att identifiera namnet på ditt befintliga kluster. Se till att använda namnet på ditt befintliga kluster när du `clusterName` `clusterNameOverride` definierar variablerna och i följande skript. Du behöver också starttidsnoderna, offentliga klientcertifikat (om du har konfigurerat en offentlig/privat nyckel på cassandra-slutpunkten) och skapa certifikat för ditt befintliga kluster.

   > [!NOTE]
   > Värdet för `delegatedManagementSubnetId` variabeln som du kommer att ange nedan är exakt samma som värdet `--scope` för som du angav i kommandot ovan:

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
    > Du bör veta var dina befintliga offentliga certifikat och/eller certifikatcertifikat förvaras. Om du är osäker bör du kunna köra för `keytool -list -keystore <keystore-path> -rfc -storepass <password>` att skriva ut certifikaten. 

1. När klusterresursen har skapats kör du följande kommando för att hämta information om klusterinstallationen:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
    
   az managed-cassandra cluster show \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName \
   ```

1. Föregående kommando returnerar information om den hanterade instansmiljön. Du behöver certifikaten så att du kan installera dem på noderna i ditt befintliga datacenter. Följande skärmbild visar utdata från föregående kommando och certifikatformatet:

   :::image type="content" source="./media/configure-hybrid-cluster/show-cluster.png" alt-text="Hämta certifikatinformationen från klustret." lightbox="./media/configure-hybrid-cluster/show-cluster.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-cluster.png) -->

1. Skapa sedan ett nytt datacenter i hybridklustret. Ersätt variabelvärdena med klusterinformationen:

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

1. Nu när det nya datacentret har skapats kör du kommandot show datacenter för att visa dess information:

   ```azurecli-interactive
   resourceGroupName='MyResourceGroup'
   clusterName='cassandra-hybrid-cluster'
   dataCenterName='dc1'
    
   az managed-cassandra datacenter show \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName 
   ```

1. Föregående kommando matar ut det nya datacentrets start seed-noder. Lägg till det nya datacentrets start seed-noder i det befintliga datacentrets konfiguration i *filen cassandra.yaml.* Och installera certifikat för hanterad instans som du samlade in tidigare:

   :::image type="content" source="./media/configure-hybrid-cluster/show-datacenter.png" alt-text="Hämta information om datacentret." lightbox="./media/configure-hybrid-cluster/show-datacenter.png" border="true":::
    <!-- ![image](./media/configure-hybrid-cluster/show-datacenter.png) -->

    > [!NOTE]
    > Om du vill lägga till fler datacenter kan du upprepa stegen ovan, men du behöver bara start startnoderna. 

1. Använd slutligen följande CQL-fråga för att uppdatera replikeringsstrategin i varje nyckelutrymme så att alla datacenter ingår i klustret:

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3};
   ```
   Du måste också uppdatera lösenordstabellerna:

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', ‘on-premise-dc': 3, ‘managed-instance-dc': 3}
   ```

## <a name="troubleshooting"></a>Felsökning

Om du stöter på ett fel när du tillämpar behörigheter på din Virtual Network, till exempel Det går inte att hitta användarens eller tjänstens huvudnamn i grafdatabasen för *"e5007d2c-4b13-4a74-9b6a-605d99f03501"* kan du tillämpa samma behörighet manuellt från Azure Portal. Om du vill tillämpa behörigheter från portalen går du till fönstret Åtkomstkontroll **(IAM)** i ditt befintliga virtuella nätverk och lägger till en rolltilldelning för "Azure Cosmos DB" till rollen Nätverksadministratör. Om två poster visas när du söker efter "Azure Cosmos DB" lägger du till båda posterna enligt följande bild: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Tillämpa behörigheter" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Den Azure Cosmos DB rolltilldelningen används endast i distributionssyfte. Azure Managed Instanced för Apache Cassandra har inga backend-beroenden på Azure Cosmos DB.  

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta att använda det här hanterade instansklustret tar du bort det med följande steg:

1. På den vänstra menyn i Azure Portal väljer du **Resursgrupper**.
1. I listan väljer du den resursgrupp som du skapade för den här snabbstarten.
1. I fönstret Översikt för **resursgruppen** väljer du **Ta bort resursgrupp.**
3. I nästa fönster anger du namnet på den resursgrupp som ska tas bort och väljer sedan Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett hybridkluster med Hjälp av Azure CLI och Azure Managed Instance för Apache Cassandra. Nu kan du börja arbeta med klustret.

> [!div class="nextstepaction"]
> [Hantera Azure Managed Instance för Apache Cassandra-resurser med Hjälp av Azure CLI](manage-resources-cli.md)
