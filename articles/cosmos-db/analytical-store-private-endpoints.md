---
title: Konfigurera privata slutpunkter för Azure Cosmos DB analysarkiv.
description: Lär dig hur du ställer in hanterade privata slutpunkter för Azure Cosmos DB analysarkiv för att begränsa nätverksåtkomst.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: fd0b3ada5fec283562cee9727e3f805a7d34c532
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479057"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Konfigurera Azure Private Link för Azure Cosmos DB analysarkiv
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

I den här artikeln får du lära dig att konfigurera hanterade privata slutpunkter för Azure Cosmos DB analysarkiv. Om du använder transaktionslagret kan du läsa [artikeln Privata slutpunkter för](how-to-configure-private-endpoints.md) transaktionsarkivet. Med [hanterade privata slutpunkter](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)kan du begränsa nätverksåtkomsten för ditt Azure Cosmos DB analysarkiv till en hanterad Virtual Network som är associerad med Azure Synapse arbetsyta. Hanterade privata slutpunkter upprättar en privat länk till analysarkivet.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Aktivera en privat slutpunkt för analysarkivet

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Konfigurera en Azure Synapse Analytics med ett hanterat virtuellt nätverk

[Skapa en arbetsyta Azure Synapse Analytics med datafiltrering aktiverat.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Med [data exfiltreringsskydd](../synapse-analytics/security/workspace-data-exfiltration-protection.md)kan du se till att obehöriga användare inte kan kopiera eller överföra data från dina Azure-resurser till platser utanför organisationens omfång.

Följande åtkomstbegränsningar gäller när skydd mot data exfiltrering är aktiverat för en Azure Synapse Analytics arbetsyta:

* Om du använder Azure Spark för Azure Synapse Analytics tillåts endast åtkomst till godkända hanterade privata slutpunkter för Azure Cosmos DB analysarkiv.

* Om du använder Synapse serverlösa SQL-pooler kan du köra frågor mot valfritt Azure Cosmos DB konto med Azure Synapse Link. Skrivbegäranden som skapar externa tabeller som [select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) tillåts dock endast till godkända hantera privata slutpunkter i arbetsytans virtuella nätverk.

> [!NOTE]
> Du kan inte ändra konfigurationen för hanterat virtuellt nätverk och data exfiltrering när arbetsytan har skapats.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Lägga till en hanterad privat slutpunkt för Azure Cosmos DB analysarkiv

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Från Azure Portal navigerar du till Synapse Analytics arbetsyta och öppnar **fönstret** Översikt.

1. Starta Synapse Studio genom att gå **Komma igång** fönstret och välja **Öppna** under **Öppna Synapse Studio**.

1. I Synapse Studio du **fliken** Hantera.

1. Gå till **Hanterade privata slutpunkter** och välj **Ny**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Skapa en ny privat slutpunkt för analysarkiv." border="true":::

1. Välj **Azure Cosmos DB(SQL API)-kontotyp** > **Fortsätt**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Välj Azure Cosmos DB SQL API för att skapa en privat slutpunkt." border="true":::

1. Fyll i formuläret **Ny hanterad privat** slutpunkt med följande information:

   * **Namn** – Namnet på den hanterade privata slutpunkten. Det här namnet kan inte uppdateras när det har skapats.
   * **Beskrivning** – Ange en användarvänlig beskrivning för att identifiera din privata slutpunkt.
   * **Azure-prenumeration** – Välj Azure Cosmos DB konto i listan över tillgängliga konton i dina Azure-prenumerationer.
   * **Azure Cosmos DB kontonamn** – Välj ett befintligt Azure Cosmos DB konto av typen SQL eller MongoDB.
   * **Målunderssouce** – Välj något av följande alternativ: **Analys:** Om du vill lägga till den privata slutpunkten för Azure Cosmos DB analysarkiv.
     **Sql** (eller **MongoDB):** Om du vill lägga till OLTP eller slutpunkten för transaktionskontot.

   > [!NOTE]
   > Du kan lägga till privata slutpunkter för både transaktionslager och analysarkiv till samma Azure Cosmos DB konto i en Azure Synapse Analytics arbetsyta. Om du bara vill köra analysfrågor kanske du bara vill mappa den privata slutpunkten för analys.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Välj analys för målunderkällan." border="true":::

1. När du har skapat går du till namnet på den privata slutpunkten **och väljer Hantera godkännanden i Azure Portal**.

1. Gå till Azure Cosmos DB konto, välj den privata slutpunkten och välj **Godkänn.**

1. Gå tillbaka till Synapse Analytics och klicka **på Uppdatera** i fönstret **Hanterade privata slutpunkter.** Kontrollera att den privata slutpunkten är **i tillståndet** Godkänd.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Kontrollera att den privata slutpunkten är godkänd." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Använda Apache Spark för Azure Synapse Analytics

Om du har Azure Synapse en arbetsyta med data exfiltreringsskydd aktiverat blockeras som standard den utgående åtkomsten från Synapse Spark till Azure Cosmos DB-konton. Och om Azure Cosmos DB har en befintlig privat slutpunkt blockeras Synapse Spark från att komma åt den.

Så här tillåter du åtkomst Azure Cosmos DB data:

* Om du använder Azure Synapse Link för att fråga Azure Cosmos DB data  lägger du till en hanterad privat analytisk slutpunkt för Azure Cosmos DB kontot.

* Om du använder batch-skrivningar/-läsningar och/eller strömmande skrivningar/läsningar till transaktionslager lägger du till en hanterad *privat SQL-* eller *MongoDB-slutpunkt* för Azure Cosmos DB-kontot. Dessutom bör du ange *connectionMode till* *Gateway* enligt följande kodfragment:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Använda Synapse-serverlösa SQL-pooler

Synapse-serverlösa SQL-pooler använder funktioner för flera innehavare som inte distribueras till hanterade virtuella nätverk. Om Azure Cosmos DB-kontot har en befintlig privat slutpunkt blockeras Synapse-serverlös SQL-pool från att komma åt kontot på grund av kontroller av nätverksisolering på Azure Cosmos DB konto.

Så här konfigurerar du nätverksisolering för det här kontot från en Synapse-arbetsyta:

1. Ge Synapse-arbetsytan åtkomst till Azure Cosmos DB genom att `NetworkAclBypassResourceId` ange inställningen för kontot.

   **Använda PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Använda Azure CLI**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB och Azure Synapse Analytics ska finnas under samma Azure Active Directory (AD).

2. Nu kan du komma åt kontot från serverlösa SQL-pooler med hjälp av T-SQL-frågor via Azure Synapse Link. För att säkerställa nätverksisolering för data i analysarkivet måste du dock lägga till en **analytiskt hanterad** privat slutpunkt för det här kontot. Annars blockeras inte data i analysarkivet från offentlig åtkomst.

> [!IMPORTANT]
> Om du använder Azure Synapse Link och behöver nätverksisolering för dina data i analysarkivet måste du mappa Azure Cosmos DB-kontot till Synapse-arbetsytan med analytisk **hanterad** privat slutpunkt.

## <a name="next-steps"></a>Nästa steg

* Kom igång med [att fråga analysarkiv med Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Kom igång med att [fråga analysarkiv med Azure Synapse serverlösa SQL-pooler](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
