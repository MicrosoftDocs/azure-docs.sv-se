---
title: Snabb start – skapa en Azure-hanterad instans för Apache Cassandra-kluster från Azure Portal
description: Den här snabb starten visar hur du skapar en Azure-hanterad instans för Apache Cassandra-kluster med hjälp av Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: db3f188cc796642285d9b082b46371879491c632
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225242"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>Snabb start: skapa en Azure-hanterad instans för Apache Cassandra-kluster från Azure Portal (för hands version)
 
Azure Managed instance för Apache Cassandra tillhandahåller automatiserade distributions-och skalnings åtgärder för hanterade Apache Cassandra-datacenter med öppen källkod, påskyndar hybrid scenarier och minskar pågående underhåll.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabb starten visar hur du använder Azure Portal för att skapa en Azure-hanterad instans för Apache Cassandra-kluster.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>Skapa ett hanterat instans kluster

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I Sök fältet söker du efter **hanterad instans för Apache Cassandra** och väljer resultatet.

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="Sök efter hanterad instans för Apache Cassandra." lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. Klicka **på knappen Skapa hanterad instans för Apache Cassandra-kluster** .

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="Skapa klustret." lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. I fönstret **skapa hanterad instans för Apache Cassandra** anger du följande information:

   * **Prenumeration** – välj din Azure-prenumeration från List rutan.
   * **Resurs grupp**– ange om du vill skapa en ny resurs grupp eller Använd en befintlig. En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Mer information finns i artikeln Översikt över [Azure resurs grupp](../azure-resource-manager/management/overview.md) .
   * **Kluster namn** – ange ett namn för klustret.
   * **Plats** – plats där klustret ska distribueras.
   * **SKU** – typ av SKU för klustret.
   * **Nej. av noder**-antal noder i ett kluster. Dessa noder fungerar som repliker för dina data.
   * **Initialt Cassandra Admin Password** -Password som används för att skapa klustret.
   * **Bekräfta Cassandra Admin Password** – ange ditt lösen ord igen.

    > [!NOTE]
    > Under den offentliga för hands versionen kan du skapa det hanterade instans klustret i *USA, västra USA, östra USA 2, västra USA 2, centrala USA, södra centrala USA, norra Europa, Västeuropa, Asien, sydöstra och östra Australien* .

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="Fyll i formuläret Skapa kluster." lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. Välj sedan fliken **nätverk** .

1. I fönstret **nätverk** väljer du **Virtual Network** namn och **undernät**. Du kan välja en befintlig Virtual Network eller skapa en ny.

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="Konfigurera nätverksinformation." lightbox="./media/create-cluster-portal/networking.png" border="true":::

1. Om du har skapat ett nytt VNet i det sista steget går du vidare till steg 8. Om du har valt ett befintligt VNet, innan du skapar klustret, måste du tillämpa vissa särskilda behörigheter för Virtual Network och under nätet. Det gör du genom att använda `az role assignment create` kommandot, ersätta `<subscription ID>` , `<resource group name>` , `<VNet name>` och `<subnet name>` med lämpliga värden:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>
   ```

   > [!NOTE]
   > `assignee`Värdena och `role` i föregående kommando är fasta värden, ange värdena exakt som de anges i kommandot. Om du inte gör det kommer det att leda till fel när klustret skapas. Om du stöter på fel när du kör det här kommandot kanske du inte har behörighet att köra det, kontakta administratören för behörigheter.

1. Nu när du är färdig med nätverket klickar du på **Granska + skapa**  >  **skapa**

    > [!NOTE]
    > Det kan ta upp till 15 minuter innan klustret skapas.

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="Granska Sammanfattning för att skapa klustret." lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. När distributionen är färdig kontrollerar du resurs gruppen för att se det nyligen skapade hanterade instans klustret:

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="Översikts sida när klustret har skapats." lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. Om du vill bläddra igenom klusternoderna går du till Virtual Networks fönstret som du använde för att skapa klustret och öppnar **översikts** fönstret för att visa dem:

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="Visa kluster resurserna." lightbox="./media/create-cluster-portal/resources.png" border="true":::



## <a name="connecting-to-your-cluster"></a>Ansluter till klustret

Den Azure-hanterade instansen för Apache Cassandra skapar inte noder med offentliga IP-adresser, så för att ansluta till det nya Cassandra-klustret måste du skapa en annan resurs i VNet. Detta kan vara ett program eller en virtuell dator med Apache: s [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) för öppen källkod installerad. Du kan använda en [mall](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/) för att distribuera en virtuell Ubuntu-dator. När du har distribuerat använder du SSH för att ansluta till datorn och installerar CQLSH med nedanstående kommandon:

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

Om du inte kommer att fortsätta att använda det här hanterade instans klustret, tar du bort det med följande steg:

1. Välj **resurs grupper** på den vänstra menyn i Azure Portal.
1. I listan väljer du den resurs grupp som du skapade för den här snabb starten.
1. I **översikts** fönstret resurs grupp väljer du **ta bort resurs grupp**.
1. I nästa fönster anger du namnet på den resurs grupp som ska tas bort och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar en Azure-hanterad instans för Apache Cassandra-kluster med hjälp av Azure Portal. Nu kan du börja arbeta med klustret:

> [!div class="nextstepaction"]
> [Distribuera ett hanterat Apache Spark-kluster med Azure Databricks](deploy-cluster-databricks.md)
