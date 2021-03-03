---
title: Konfigurera privata slut punkter för Azure Cosmos DB analys lager.
description: Lär dig hur du konfigurerar hanterade privata slut punkter för Azure Cosmos DB Analytical Store för att begränsa nätverks åtkomsten.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 26c99beb95d60b476fff255321d85c0db8aa7363
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679994"
---
# <a name="configure-private-endpoints-for-azure-cosmos-db-analytical-store"></a>Konfigurera privata slut punkter för Azure Cosmos DB analys lager
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

I den här artikeln får du lära dig hur du konfigurerar hanterade privata slut punkter för Azure Cosmos DB analys lager. Om du använder transaktions arkivet, se [privata slut punkter för den transaktions Arkiv](how-to-configure-private-endpoints.md) artikeln. Med hanterade privata slut punkter kan du begränsa nätverks åtkomsten för Azure Cosmos DB Analytical Store till Azure Synapse Managed Virtual Network. Hanterade privata slut punkter upprättar en privat länk till analys lagret.

## <a name="enable-private-endpoint-for-the-analytical-store"></a>Aktivera privat slut punkt för analys lagret

### <a name="set-up-an-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Konfigurera en Azure Synapse Analytics-arbetsyta med ett hanterat virtuellt nätverk

[Skapa en arbets yta i Azure Synapse Analytics med data exfiltrering aktiverat.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Med [exfiltrering skydd](../synapse-analytics/security/workspace-data-exfiltration-protection.md)kan du se till att skadliga användare inte kan kopiera eller överföra data från dina Azure-resurser till platser utanför organisationens omfattning.

Följande åtkomst begränsningar gäller när data exfiltrering Protection är aktiverat för en Azure Synapse Analytics-arbetsyta:

* Om du använder Azure Spark för Azure Synapse Analytics tillåts åtkomst endast till godkända hanterade privata slut punkter för Azure Cosmos DB analys lager.

* Om du använder Synapse-server utan SQL-pooler kan du fråga alla Azure Cosmos DB-konton med hjälp av Azure Synapse-länken. Skriv förfrågningar som [skapar externa tabeller som Select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) tillåts dock endast för godkända hantera privata slut punkter i arbets ytans virtuella nätverk.

> [!NOTE]
> Du kan inte ändra hanterade virtuella nätverk och exfiltrering konfiguration när arbets ytan har skapats.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Lägg till en hanterad privat slut punkt för Azure Cosmos DB analys lager

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Från Azure Portal navigerar du till din Synapse Analytics-arbetsyta och öppnar **översikts** fönstret.

1. Starta Synapse Studio genom att gå till **komma igång** fönstret och välj **Öppna** under **Öppna Synapse Studio**.

1. Öppna fliken **Hantera** i Synapse Studio.

1. Navigera till **hanterade privata slut punkter** och välj **nytt**

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Skapa en ny privat slut punkt för analys lagringen." border="true":::

1. Välj **Azure Cosmos DB-kontotyp (SQL-API)** > **Fortsätt**.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Välj Azure Cosmos DB SQL API för att skapa en privat slut punkt." border="true":::

1. Fyll i det **nya hanterade privata slut punkt** formuläret med följande information:

   * **Namn** – namnet på den hanterade privata slut punkten. Det här namnet kan inte uppdateras efter att det har skapats.
   * **Beskrivning** – ange en egen beskrivning för att identifiera din privata slut punkt.
   * **Azure-prenumeration** – välj ett Azure Cosmos DB konto i listan över tillgängliga konton i dina Azure-prenumerationer.
   * **Azure Cosmos DB konto namn** – Välj ett befintligt Azure Cosmos DB konto av typen SQL eller MongoDB.
   * **Mål under resurs** – Välj något av följande alternativ: **analys**: om du vill lägga till den privata slut punkten för Azure Cosmos DB Analytical Store.
     **SQL** (eller **MongoDB**): om du vill lägga till OLTP eller transaktions konto slut punkt.

   > [!NOTE]
   > Du kan lägga till både transaktions lager och analys lager privata slut punkter till samma Azure Cosmos DB konto i en Azure Synapse Analytics-arbetsyta. Om du bara vill köra analytiska frågor kanske du bara vill mappa den analytiska privata slut punkten.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Välj analys för mål under resursen." border="true":::

1. När du har skapat går du till namnet på den privata slut punkten och väljer **Hantera godkännanden i Azure Portal**.

1. Navigera till ditt Azure Cosmos DB konto, Välj den privata slut punkten och välj **Godkänn**.

1. Gå tillbaka till Synapse Analytics-arbetsytan och klicka på **Uppdatera** i fönstret **hanterade privata slut punkter** . Kontrol lera att den privata slut punkten är i **godkänt** tillstånd.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Kontrol lera att den privata slut punkten är godkänd." border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Använda Apache Spark för Azure Synapse Analytics

Om du har skapat en Azure Synapse-arbetsyta med data exfiltrering-skydd aktiverat, blockeras den utgående åtkomsten från Synapse Spark till Azure Cosmos DB-konton som standard. Om Azure Cosmos DB redan har en befintlig privat slut punkt kommer Synapse Spark att blockeras från att komma åt den.

För att tillåta åtkomst till Azure Cosmos DB data:

* Om du använder Azure Synapse-länken för att fråga Azure Cosmos DB data lägger du till en hanterad **analytisk** privat slut punkt för det Azure Cosmos DB kontot.

* Om du använder batch-skrivningar/läsningar och/eller strömmande skrivningar/läsningar till transaktions lager, lägger du till en hanterad *SQL* -eller *MongoDB* -slutpunkt för det Azure Cosmos DB kontot. Dessutom bör du också ange *connectionMode* till *Gateway* som det visas i följande kodfragment:

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

## <a name="using-synapse-serverless-sql-pools"></a>Använda Synapse server utan SQL-pooler

Synapse-server utan SQL-pooler använder funktioner för flera klienter som inte har distribuerats till det hanterade virtuella nätverket. Om Azure Cosmos DB-kontot har en befintlig privat slut punkt kommer Synapse-server utan SQL-pool att blockeras från att komma åt kontot, på grund av att nätverks isoleringen kontrollerar Azure Cosmos DB-kontot.

Så här konfigurerar du nätverks isolering för det här kontot från en Synapse-arbets yta:

1. Tillåt att Synapse-arbetsytan får åtkomst till Azure Cosmos DB konto genom att ange `NetworkAclBypassResourceId` inställning för kontot.

   **Använda PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Använda Azure CLI**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > Lägg Azure Cosmos DB konto och Azure Synapse Analytics-arbetsytan bör ligga under samma Azure Active Directory (AD)-klient.

2. Du kan nu komma åt kontot från serverbaserade SQL-pooler med hjälp av T-SQL-frågor över Azure Synapse-länken. För att säkerställa nätverks isolering för data i analytisk lagring måste du dock lägga till en **analytisk** hanterad privat slut punkt för det här kontot. Annars blockeras inte data i analys lagret från offentlig åtkomst.

> [!IMPORTANT]
> Om du använder Azure Synapse-länken och behöver nätverks isolering för dina data i ett analys lager måste du mappa Azure Cosmos DB-kontot till Synapse-arbetsytan med **analytisk** hanterad privat slut punkt.

## <a name="next-steps"></a>Nästa steg

* Kom igång med att [fråga analys lager med Azure Synapse Spark](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Kom igång med att [fråga analys lager med Azure Synapse-server utan SQL-pooler](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
