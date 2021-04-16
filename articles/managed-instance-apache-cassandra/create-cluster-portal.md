---
title: Snabbstart – Skapa Azure Managed Instance för Apache Cassandra-kluster från Azure Portal
description: Den här snabbstarten visar hur du skapar en Azure Managed Instance för Apache Cassandra-kluster med hjälp av Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e42f85bb79dcb1bfe14cacbbfda3576888b841c9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481336"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Snabbstart: Skapa en Hanterad Azure-instans för Apache Cassandra-kluster från Azure Portal (förhandsversion)
 
Azure Managed Instance för Apache Cassandra tillhandahåller automatiserade distributions- och skalningsåtgärder för hanterade Apache Cassandra-datacenter med öppen källkod, vilket påskyndar hybridscenarier och minskar pågående underhåll.

> [!IMPORTANT]
> Azure Managed Instance för Apache Cassandra finns för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabbstarten visar hur du använder Azure Portal för att skapa en Azure Managed Instance för Apache Cassandra-kluster.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Skapa ett hanterat instanskluster

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I sökfältet söker du efter **Managed Instance för Apache Cassandra** och väljer resultatet.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Sök efter Managed Instance för Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Välj **knappen Skapa hanterad instans för Apache Cassandra-kluster.**

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Skapa klustret." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. I fönstret **Skapa hanterad instans för Apache Cassandra** anger du följande information:

   * **Prenumeration** – Välj din Azure-prenumeration i listrutan.
   * **Resursgrupp**– Ange om du vill skapa en ny resursgrupp eller använda en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i [översiktsartikeln om Azure-resursgrupper.](../azure-resource-manager/management/overview.md)
   * **Klusternamn** – Ange ett namn för klustret.
   * **Plats** – Plats där klustret ska distribueras.
   * **SKU** – Typ av SKU för klustret.
   * **Nej, nej. antal noder**– antalet noder i ett kluster. Dessa noder fungerar som repliker för dina data.
   * **Ursprungligt administratörslösenord** för Cassandra – Lösenord som används för att skapa klustret.
   * **Bekräfta administratörslösenordet** för Cassandra – Ange ditt lösenord igen.

    > [!NOTE]
    > I den offentliga förhandsversionen kan du skapa det hanterade instansklustret i regionerna *USA, östra, USA, västra, USA, östra 2, USA, västra 2, USA, centrala, USA, södra centrala, Europa, norra, Europa, västra, Asien, sydöstra och Australien, östra.*

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Fyll i formuläret skapa kluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Välj sedan **fliken** Nätverk.

1. I fönstret **Nätverk** väljer du **Virtual Network** namn och **Undernät.** Du kan välja en befintlig Virtual Network eller skapa en ny.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Konfigurera nätverksinformation." lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > Distributionen av en Hanterad Azure-instans för Apache Cassandra kräver Internetåtkomst. Distributionen misslyckas i miljöer där Internetåtkomsten är begränsad. Kontrollera att du inte blockerar åtkomsten i ditt VNet till följande viktiga Azure-tjänster som krävs för att Managed Cassandra ska fungera korrekt:
    > - Azure Storage
    > - Azure KeyVault
    > - Azure Virtual Machine Scale Sets
    > - Azure Monitoring
    > - Azure Active Directory
    > - Säkerhet i Azure

1. Om du skapade ett nytt VNet i det sista steget går du vidare till steg 8. Om du har valt ett befintligt VNet måste du, innan du skapar klustret, tillämpa vissa särskilda behörigheter för Virtual Network och undernätet. Det gör du genom att `az role assignment create` använda kommandot , ersätta , och med lämpliga `<subscription ID>` `<resource group name>` `<VNet name>` värden:

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > Värdena `assignee` och i föregående kommando är fasta `role` värden. Ange dessa värden exakt som anges i kommandot. Om du inte gör det uppstår fel när klustret skapas. Om du stöter på fel när du kör det här kommandot kanske du inte har behörighet att köra det. Kontakta administratören om behörighet.

1. Nu när du är klar med nätverket klickar du på **Granska + skapa**  >  **skapa**

    > [!NOTE]
    > Det kan ta upp till 15 minuter för klustret att skapas.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Granska sammanfattningen för att skapa klustret." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. När distributionen är klar kontrollerar du resursgruppen för att se det nyligen skapade hanterade instansklustret:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Översiktssidan när klustret har skapats." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Om du vill bläddra igenom klusternoderna går du till Virtual Network som du använde för att skapa klustret och öppnar **fönstret** Översikt för att visa dem:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Visa klusterresurserna." lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>Ansluta till klustret

Azure Managed Instance för Apache Cassandra skapar inte noder med offentliga IP-adresser, så för att ansluta till ditt nyligen skapade Cassandra-kluster måste du skapa en till resurs i det virtuella nätverket. Det kan vara ett program eller en virtuell dator med Apaches frågeverktyg med öppen källkod [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) installerat. Du kan använda en mall [för](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) att distribuera en virtuell Ubuntu-dator. När den har distribuerats använder du SSH för att ansluta till datorn och installerar CQLSH med hjälp av kommandona nedan:

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

Om du stöter på ett fel när du tillämpar behörigheter på din Virtual Network, till exempel Det går inte att hitta användarens eller tjänstens huvudnamn i grafdatabasen för *"e5007d2c-4b13-4a74-9b6a-605d99f03501"* kan du tillämpa samma behörighet manuellt från Azure Portal. Om du vill tillämpa behörigheter från portalen går du till fönstret Åtkomstkontroll **(IAM)** i ditt befintliga virtuella nätverk och lägger till en rolltilldelning för "Azure Cosmos DB" till rollen Nätverksadministratör. Om två poster visas när du söker efter "Azure Cosmos DB" lägger du till båda posterna enligt följande bild: 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="Tillämpa behörigheter" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Den Azure Cosmos DB rolltilldelningen används endast i distributionssyfte. Azure Managed Instanced för Apache Cassandra har inga backend-beroenden på Azure Cosmos DB.   

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tänker fortsätta att använda det här hanterade instansklustret tar du bort det med följande steg:

1. På den vänstra menyn i Azure Portal väljer du **Resursgrupper**.
1. I listan väljer du den resursgrupp som du skapade för den här snabbstarten.
1. I fönstret Översikt för **resursgruppen** väljer du **Ta bort resursgrupp.**
1. I nästa fönster anger du namnet på den resursgrupp som ska tas bort och väljer sedan Ta **bort.**

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar en Azure Managed Instance för Apache Cassandra-kluster med Azure Portal. Nu kan du börja arbeta med klustret:

> [!div class="nextstepaction"]
> [Distribuera ett hanterat Apache Spark kluster med Azure Databricks](deploy-cluster-databricks.md)
