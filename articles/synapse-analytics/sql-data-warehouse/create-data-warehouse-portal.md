---
title: 'Snabbstart: Skapa och fråga en dedikerad SQL-pool (tidigare SQL DW) (Azure Portal)'
description: Skapa och fråga en dedikerad SQL-pool (tidigare SQL DW) med hjälp av Azure Portal
services: synapse-analytics
author: pimorano
ms.author: pimorano
manager: craigg
ms.reviewer: igorstan
ms.date: 05/28/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 99530b1949f03867a5b755208191341556ba8083
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535840"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Snabbstart: Skapa och fråga en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med hjälp av Azure Portal

Skapa och fråga snabbt en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

   > [!NOTE]
   > Att skapa en dedikerad SQL-pool (tidigare SQL DW) Azure Synapse kan resultera i en ny fakturerbar tjänst. Mer information finns i [Azure Synapse Analytics priser.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

2. Ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS). Obs! SSMS är endast tillgängligt på Windows-baserade plattformar. Se den [fullständiga listan över plattformar som stöds.](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15&preserve-view=true#supported-operating-systems-ssms-185t)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

Informationslager skapas med dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics. En dedikerad SQL-pool (tidigare SQL DW) skapas med en definierad uppsättning [beräkningsresurser](memory-concurrency-limits.md). Databasen skapas i en [Azure-resursgrupp och](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i en [logisk SQL-server.](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

Följ de här stegen för att skapa en dedikerad SQL-pool (tidigare SQL DW) som innehåller **AdventureWorksDW-exempeldata.**

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.

   ![skapa en resurs i Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. I sökfältet skriver du "dedikerad SQL-pool" och väljer dedikerad SQL-pool (tidigare SQL DW). Välj **Skapa** på sidan som öppnas.

   ![skapa ett tomt informationslager](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. I **Grundläggande anger** du din prenumeration, resursgrupp, dedikerad SQL-pool (tidigare SQL DW)-namn och servernamn:

   | Inställning | Föreslaget värde | Beskrivning |
   | :------ | :-------------- | :---------- |
   | **Prenumeration** | Din prenumeration | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
   | **SQL-poolnamn** | Ett globalt unikt namn (ett exempel är *mySampleDataWarehouse*) | Giltiga databasnamn finns i [Databasidentifierare.](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
   | **Server** | Valfritt globalt unikt namn | Välj en befintlig server eller skapa ett nytt servernamn och välj **Skapa ny.** Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

   ![skapa ett informationslager med grundläggande information](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Under **Prestandanivå väljer** du **Välj prestandanivå om du** vill ändra konfigurationen med ett skjutreglage.

   ![ändra prestandanivå för informationslager](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Mer information om prestandanivåer finns i [Hantera beräkning i Azure Synapse Analytics](sql-data-warehouse-manage-compute-overview.md).

5. Välj **Ytterligare inställningar** under Använd befintliga **data** och välj **Exempel** så att AdventureWorksDW skapas som exempeldatabas.

    ![välj Använd befintliga data](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Nu när du har slutfört fliken Grundläggande inställningar i Azure Synapse Analytics väljer du **Granska + skapa** och sedan Skapa för att skapa SQL-poolen.  Etableringen tar några minuter.

   ![välj Granska + skapa](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![Välj Skapa](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Välj **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![Skärmbild som visar meddelanden med distribution pågår.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Tjänsten Azure Synapse skapar en brandvägg på servernivå. Den här brandväggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern. Om du vill kan du lägga till brandväggsregler som tillåter anslutningar för specifika IP-adresser. Följ dessa steg för att skapa en [brandväggsregel på servernivå](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för klientens IP-adress.

> [!NOTE]
> Azure Synapse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 1433.

1. När distributionen är klar väljer **du Alla** tjänster på menyn till vänster. Välj **Databaser**, välj stjärnan bredvid den **Azure Synapse Analytics** lägga till Azure Synapse Analytics i dina favoriter.

2. Välj **Azure Synapse Analytics** på menyn till vänster och välj **sedan mySampleDataWarehouse** på **Azure Synapse Analytics** sidan. Översiktssidan för databasen öppnas och visar det fullständigt kvalificerade servernamnet (till exempel **sqlpoolservername.database.windows.net**) och alternativ för ytterligare konfiguration.

3. Kopiera det här fullständigt kvalificerade servernamnet för användning för att ansluta till servern och dess databaser i den här och andra snabbstarter. Välj servernamnet för att öppna serverinställningarna.

   ![hitta servernamn](./media/create-data-warehouse-portal/find-server-name.png)

4. Välj **Visa brandväggsinställningar**.

   ![serverinställningar](./media/create-data-warehouse-portal/server-settings.png)

5. Sidan **Brandväggsinställningar** för servern öppnas.

   ![brandväggsregler för server](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Om du vill lägga till din aktuella IP-adress i en ny brandväggsregel **väljer du Lägg till klient-IP** i verktygsfältet. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

7. välj **Spara**. En brandväggsregel på servernivå skapas för din aktuella IP-adress som öppnar port 1433 på servern.

8. Välj **OK** och stäng sedan sidan **Brandväggsinställningar.**

Du kan nu ansluta till servern och dess SQL-pooler med den här IP-adressen. Anslutningen fungerar från SQL Server Management Studio eller något annat verktyg du väljer. När du ansluter kan du använda ServerAdmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Välj **AV** på den här sidan och välj sedan **Spara för** att inaktivera brandväggen för alla Azure-tjänster.

## <a name="get-the-fully-qualified-server-name"></a>Hämta det fullständigt kvalificerade servernamnet

Hämta det fullständigt kvalificerade servernamnet för servern i Azure Portal. Du använder det fullständigt kvalificerade namnet senare när du ska ansluta till servern.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Azure Synapse Analytics** på menyn till vänster och välj din på **Azure Synapse Analytics** sidan.

3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**. I det här exemplet är det fullständigt kvalificerade namnet sqlpoolservername.database.windows.net.

    ![anslutningsinformation](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Ansluta till servern som serveradministratör

Det här avsnittet [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) för att upprätta en anslutning till servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning | Föreslaget värde | Beskrivning |
   | :------ | :-------------- | :---------- |
   | Servertyp | Databasmotor | Det här värdet är obligatoriskt |
   | Servernamn | Fullständigt kvalificerat servernamn | Här är ett exempel: **sqlpoolservername.database.windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi konfigurerar i den här självstudiekursen. |
   | Inloggning | Serveradministratörskontot | Konto som du angav när du skapade servern. |
   | Lösenord | Lösenordet för serveradministratörskontot | Lösenord som du angav när du skapade servern. |
   ||||

   ![Anslut till server](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. välj **Anslut.** Fönstret Object Explorer öppnas i SSMS.

4. Expandera **Databaser** i Object Explorer. Expandera **mySampleDatabase** så visas objekten i den nya databasen.

   ![databasobjekt](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Köra några frågor

Vi rekommenderar inte att du kör stora frågor när du loggas som serveradministratör eftersom den använder en [begränsad resursklass](resource-classes-for-workload-management.md). Konfigurera i stället [arbetsbelastningsisolering](./quickstart-configure-workload-isolation-tsql.md) [enligt vad som visas i självstudierna](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data).

Azure Synapse Analytics använder T-SQL som frågespråk. Använd följande steg om du vill öppna ett frågefönster och köra några T-SQL-frågor:

1. Högerklicka på **mySampleDataWarehouse och** välj **Ny fråga.** Ett nytt frågefönster öppnas.

2. Ange följande kommando i frågefönstret för att visa en lista över databaser.

    ```sql
    SELECT * FROM sys.databases
    ```

3. välj **Kör**. Resultatet av frågan visar två databaser: **master** och **mySampleDataWarehouse**.

   ![Skicka frågor mot databaser](./media/create-data-warehouse-portal/query-databases.png)

4. Vi kan titta på vissa data genom att använda följande kommando för att se hur många kunder med efternamnet Adams som har tre barn hemma. I resultatlistan finns sex kunder.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Skicka frågor till dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i din dedikerade SQL-pool (tidigare SQL DW). Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill lagra data kan du pausa beräkningen när du inte använder den dedikerade SQL-poolen (tidigare SQL DW). Genom att pausa beräkningen debiteras du bara för datalagring. Du kan återuppta beräkningen när du är redo att arbeta med data.

- Om du vill ta bort framtida avgifter kan du ta bort den dedikerade SQL-poolen (tidigare SQL DW).

Följ de här stegen för att rensa resurser som du inte längre behöver.

1. Logga in på [Azure Portal](https://portal.azure.com), välj din dedikerade SQL-pool (tidigare SQL DW).

   ![Rensa resurser](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du **knappen Pausa.** När den dedikerade SQL-poolen (tidigare SQL DW) har pausats visas knappen **Återuppta.** Om du vill återuppta beräkningen väljer du **Återuppta**.

3. Om du vill ta bort den dedikerade SQL-poolen (tidigare SQL DW) så att du inte debiteras för beräkning eller lagring väljer du Ta **bort**.

4. Om du vill ta bort servern som du **skapade sqlpoolservername.database.windows.net** i föregående bild och väljer sedan Ta **bort.** Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen väljer **du myResourceGroup** och sedan Ta **bort resursgrupp.**

Vill du optimera och spara på dina molnutgifter?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om att läsa in data i din dedikerade SQL-pool (tidigare SQL DW) fortsätter du till artikeln Läsa in [data till en dedikerad SQL-pool.](load-data-from-azure-blob-storage-using-copy.md)
