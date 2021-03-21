---
title: Snabb start – distribuera ett hanterat Apache Spark-kluster med Azure Databricks
description: Den här snabb starten visar hur du distribuerar ett hanterat Apache Spark-kluster med Azure Databricks med hjälp av Azure Portal.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: fd0d5c5b73ae1fb1eae7f38a22913018555ebe11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101748965"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>Snabb start: Distribuera ett hanterat Apache Spark-kluster (för hands version) med Azure Databricks

Azure Managed instance för Apache Cassandra tillhandahåller automatiserade distributions-och skalnings åtgärder för hanterade Apache Cassandra-datacenter med öppen källkod, påskyndar hybrid scenarier och minskar pågående underhåll.

> [!IMPORTANT]
> Azure Managed instance för Apache Cassandra är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här snabb starten visar hur du använder Azure Portal för att skapa ett fullständigt hanterat Apache Spark-kluster i Azure-Virtual Network av din Azure-hanterade instans för Apache Cassandra-kluster. Du skapar Spark-klustret i Azure Databricks. Senare kan du skapa eller koppla antecknings böcker till klustret, läsa data från olika data källor och analysera insikter.

Du kan också lära dig mer med detaljerade instruktioner om hur [du distribuerar Azure Databricks i Azure Virtual Network (Virtual Network insprutning)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="create-an-azure-databricks-cluster"></a>Skapa ett Azure Databricks-kluster

Följ dessa steg om du vill skapa ett Azure Databricks kluster i en Virtual Network som har den Azure-hanterade instansen för Apache Cassandra:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Leta upp **resurs grupper** i det vänstra navigerings fältet och navigera till din resurs grupp som innehåller Virtual Network där din hanterade instans distribueras.

1. Öppna **Virtual Network** resurs och anteckna **adress utrymmet**:

    :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="Hämta adress utrymmet för Virtual Network." border="true":::

1. Välj **Lägg till** i resurs gruppen och sök efter **Azure Databricks** i Sök fältet:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="Sök efter Azure Databricks." border="true":::

1. Välj **skapa** för att skapa ett Azure Databricks-konto:

    :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="Skapa ett Azure Databricks-konto." border="true":::

1. Fyll i följande värden:

   * **Arbets ytans namn** – ange ett namn för din Databricks-arbetsyta.
   * **Region** – se till att välja samma region som din Virtual Network.
   * **Pris nivå** – Välj mellan standard, Premium eller utvärdering. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).

    :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="Fyll i arbets ytans namn, region och pris nivå för Databricks-kontot." border="true":::

1. Välj sedan fliken **nätverk** och fyll i följande information:

   * **Distribuera Azure Databricks arbets yta i ditt Virtual Network (VNet)** – Välj **Ja**.
   * **Virtual Network** – i list rutan väljer du Virtual Network där din hanterade instans finns.
   * **Namn på offentligt undernät** – ange ett namn för det offentliga under nätet.
   * **CIDR-intervall för offentliga undernät** – ange ett IP-intervall för det offentliga under nätet.
   * **Namn på privat undernät** – ange ett namn för det privata under nätet.
   * **CIDR-intervall för privata undernät** – ange ett IP-intervall för det privata under nätet.

   Se till att du väljer högre intervall för att undvika intervall kollisioner. Vid behov använder du en [kalkylator för visuella undernät](https://www.fryguy.net/wp-content/tools/subnets.html) för att dela upp intervallen:

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="Använd Kalkylatorn för Virtual Network under nätet." border="true":::

   Följande skärm bild visar exempel information i fönstret nätverk:

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="Ange offentliga och privata under näts namn." border="true":::

1. Välj **Granska och skapa** och sedan **skapa** för att distribuera arbets ytan.

1. **Starta arbets ytan** när den har skapats.

1. Du omdirigeras till Azure Databricks-portalen. Välj **nytt kluster** från portalen.

1. I fönstret **nytt kluster** accepterar du standardvärden för alla andra fält än följande fält:

   * **Kluster namn** – ange ett namn för klustret.
   * **Databricks runtime version** – välj Scala 2,11 eller lägre version som stöds av Cassandra-anslutningen.

    :::image type="content" source="./media/deploy-cluster-databricks/spark-cluster.png" alt-text="Välj Databricks runtime-versionen och Spark-klustret." border="true":::

1. Expandera **Avancerade alternativ** och Lägg till följande konfiguration. Se till att ersätta nodens IP-adresser och autentiseringsuppgifter:

    ```java
    spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
    spark.cassandra.auth.password cassandra
    spark.cassandra.connection.port 9042
    spark.cassandra.auth.username cassandra
    spark.cassandra.connection.ssl.enabled true
    ```

1. På fliken **bibliotek** installerar du den senaste versionen av Spark Connector för Cassandra (*Spark-Cassandra-Connector*) och startar om klustret:

    :::image type="content" source="./media/deploy-cluster-databricks/connector.png" alt-text="Installera Cassandra-anslutningen." border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här hanterade instans klustret, tar du bort det med följande steg:

1. Välj **resurs grupper** på den vänstra menyn i Azure Portal.
1. I listan väljer du den resurs grupp som du skapade för den här snabb starten.
1. I **översikts** fönstret resurs grupp väljer du **ta bort resurs grupp**.
3. I nästa fönster anger du namnet på den resurs grupp som ska tas bort och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett fullständigt hanterat Apache Spark kluster i Virtual Network av din Azure-hanterade instans för Apache Cassandra-kluster. Sedan kan du lära dig hur du hanterar klustret och data Center resurser: 

> [!div class="nextstepaction"]
> [Hantera Azure-hanterad instans för Apache Cassandra-resurser med hjälp av Azure CLI](manage-resources-cli.md)

