---
title: 'Snabb start: skapa och fråga en dedikerad SQL-pool (tidigare SQL DW) (Azure Portal)'
description: Skapa och fråga en dedikerad SQL-pool (tidigare SQL DW) med hjälp av Azure Portal
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/28/2019
ms.author: pimorano
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ecab573f7a98bcee79231b8e20077bb32bffd48c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680122"
---
# <a name="quickstart-create-and-query-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-using-the-azure-portal"></a>Snabb start: skapa och fråga en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med hjälp av Azure Portal

Skapa och fråga snabbt en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

1. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

   > [!NOTE]
   > Att skapa en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse kan resultera i en ny fakturerbar tjänst. Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Ladda ned och installera den senaste versionen av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Skapa en SQL-pool

Informations lager skapas med dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics. En dedikerad SQL-pool (tidigare SQL DW) skapas med en definierad uppsättning [beräknings resurser](memory-concurrency-limits.md). Databasen skapas i en Azure- [resurs grupp](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och i en [logisk SQL-Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

Följ dessa steg om du vill skapa en dedikerad SQL-pool (tidigare SQL DW) som innehåller **AdventureWorksDW** -exempel data.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.

   ![skapa en resurs i Azure Portal](./media/create-data-warehouse-portal/create-a-resource.png)

2. I Sök fältet typ "dedikerad SQL-pool" väljer du dedikerad SQL-pool (tidigare SQL DW). Välj **skapa** på sidan som öppnas.

   ![skapa ett tomt informationslager](./media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. I **grunderna**, anger du din prenumeration, resurs grupp, dedikerad SQL-pool (tidigare SQL DW) och Server Namn:

   | Inställning | Föreslaget värde | Beskrivning |
   | :------ | :-------------- | :---------- |
   | **Prenumeration** | Din prenumeration | Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions). |
   | **Resursgrupp** | myResourceGroup | Giltiga resursgruppnamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |
   | **SQL-poolnamn** | Globalt unikt namn (ett exempel är *mySampleDataWarehouse*) | För giltiga databas namn, se [databas identifierare](/sql/relational-databases/databases/database-identifiers?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  |
   | **Server** | Valfritt globalt unikt namn | Välj befintlig server eller skapa ett nytt Server namn och välj **Skapa ny**. Giltiga servernamn finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

   ![skapa grundläggande information om informations lagret](./media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. Under **prestanda nivå** väljer du **Välj prestanda nivå** om du vill ändra konfigurationen genom att välja ett skjutreglage.

   ![ändra prestanda nivå för informations lager](./media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Mer information om prestanda nivåer finns i [Hantera beräkning i Azure Synapse Analytics](sql-data-warehouse-manage-compute-overview.md).

5. Välj **ytterligare inställningar**. under **Använd befintliga data** väljer du **exempel** så att AdventureWorksDW skapas som exempel databas.

    ![Välj Använd befintliga data](./media/create-data-warehouse-portal/create-sql-pool-additional-1.png)

6. Nu när du har slutfört fliken grundläggande i formuläret för Azure Synapse Analytics väljer du **Granska + skapa** och sedan **skapa** för att skapa SQL-poolen. Etableringen tar några minuter.

   ![Välj granska + skapa](./media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![Välj Skapa](./media/create-data-warehouse-portal/create-sql-pool-create.png)

7. Välj **Aviseringar** i verktygsfältet för att övervaka distributionsprocessen.

   ![Skärm bild som visar meddelanden med pågående distribution.](./media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Skapa en brandväggsregel på servernivå

Azure Synapse-tjänsten skapar en brand vägg på server nivå. Den här brand väggen förhindrar att externa program och verktyg ansluter till servern eller databaser på servern. Om du vill kan du lägga till brandväggsregler som tillåter anslutningar för specifika IP-adresser. Följ dessa steg för att skapa en [brandväggsregel på servernivå](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för klientens IP-adress.

> [!NOTE]
> Azure-Synapse kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 1433 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte din IT-avdelning öppnar port 1433.

1. När distributionen är klar väljer du **alla tjänster** i den vänstra menyn. Välj **databaser**, Välj stjärnan bredvid **Azure Synapse Analytics** för att lägga till Azure Synapse Analytics i dina favoriter.

2. Välj **Azure Synapse Analytics** på menyn till vänster och välj sedan **MySampleDataWarehouse** på sidan **Azure Synapse Analytics** . Översikts sidan för databasen öppnas och visar det fullständigt kvalificerade Server namnet (till exempel **sqlpoolservername.Database.Windows.net**) och alternativ för ytterligare konfiguration.

3. Kopiera det här fullständigt kvalificerade Server namnet som ska användas för att ansluta till servern och dess databaser i den här och andra snabb starter. Välj Server namnet för att öppna Server inställningar.

   ![hitta servernamn](./media/create-data-warehouse-portal/find-server-name.png)

4. Välj **Visa brandväggsinställningar**.

   ![serverinställningar](./media/create-data-warehouse-portal/server-settings.png)

5. Sidan **brand Väggs inställningar** för servern öppnas.

   ![brandväggsregler för server](./media/create-data-warehouse-portal/server-firewall-rule.png)

6. Om du vill lägga till din aktuella IP-adress i en ny brand Väggs regel väljer du **Lägg till klient-IP** i verktygsfältet. Med en brandväggsregel kan du öppna port 1433 för en enskild IP-adress eller för IP-adressintervall.

7. Välj **Spara**. En brand Väggs regel på server nivå skapas för din aktuella IP-adress som öppnar port 1433 på servern.

8. Välj **OK** och stäng sedan sidan **brand Väggs inställningar** .

Nu kan du ansluta till servern och dess SQL-pooler med hjälp av den här IP-adressen. Anslutningen fungerar från SQL Server Management Studio eller något annat verktyg du väljer. När du ansluter kan du använda ServerAdmin-kontot som du skapade tidigare.

> [!IMPORTANT]
> Som standard är åtkomst genom SQL Database-brandväggen aktiverad för alla Azure-tjänster. Välj **av** på den här sidan och välj sedan **Spara** för att inaktivera brand väggen för alla Azure-tjänster.

## <a name="get-the-fully-qualified-server-name"></a>Hämta det fullständigt kvalificerade servernamnet

Hämta det fullständigt kvalificerade Server namnet för servern i Azure Portal. Du använder det fullständigt kvalificerade namnet senare när du ska ansluta till servern.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Azure Synapse Analytics** på menyn till vänster och välj sedan på **Azure Synapse Analytics** -sidan.

3. I rutan **Essentials** på sidan för Azure Portal för databasen letar du reda på och kopierar **servernamnet**. I det här exemplet är det fullständigt kvalificerade namnet sqlpoolservername.database.windows.net.

    ![anslutningsinformation](./media/create-data-warehouse-portal/find-server-name.png)

## <a name="connect-to-the-server-as-server-admin"></a>Ansluta till servern som serveradministratör

I det här avsnittet används [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) för att upprätta en anslutning till servern.

1. Öppna SQL Server Management Studio.

2. I dialogrutan **Anslut till server** anger du följande information:

   | Inställning | Föreslaget värde | Beskrivning |
   | :------ | :-------------- | :---------- |
   | Servertyp | Databasmotor | Det här värdet är obligatoriskt |
   | Servernamn | Fullständigt kvalificerat servernamn | Här är ett exempel: **sqlpoolservername.Database.Windows.net**. |
   | Autentisering | SQL Server-autentisering | SQL-autentisering är den enda autentiseringstypen som vi konfigurerar i den här självstudiekursen. |
   | Inloggning | Serveradministratörskontot | Kontot som du angav när du skapade servern. |
   | Lösenord | Lösenordet för serveradministratörskontot | Lösen ordet du angav när du skapade servern. |
   ||||

   ![Anslut till server](./media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Välj **Anslut**. Fönstret Object Explorer öppnas i SSMS.

4. Expandera **Databaser** i Object Explorer. Expandera **mySampleDatabase** så visas objekten i den nya databasen.

   ![databasobjekt](./media/create-data-warehouse-portal/connected-ssms.png)

## <a name="run-some-queries"></a>Köra några frågor

Vi rekommenderar inte att du kör stora frågor när du loggar som Server administratör, eftersom den använder en [begränsad resurs klass](resource-classes-for-workload-management.md). Konfigurera i stället [arbets belastnings isolering](./quickstart-configure-workload-isolation-tsql.md) som visas [i självstudierna](./load-data-wideworldimportersdw.md#create-a-user-for-loading-data).

Azure Synapse Analytics använder T-SQL som frågespråk. Använd följande steg om du vill öppna ett frågefönster och köra några T-SQL-frågor:

1. Högerklicka på **mySampleDataWarehouse** och välj **ny fråga**. Ett nytt frågefönster öppnas.

2. Ange följande kommando i frågefönstret för att visa en lista över databaser.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Välj **Kör**. Resultatet av frågan visar två databaser: **master** och **mySampleDataWarehouse**.

   ![Skicka frågor mot databaser](./media/create-data-warehouse-portal/query-databases.png)

4. Vi kan titta på vissa data genom att använda följande kommando för att se hur många kunder med efternamnet Adams som har tre barn hemma. I resultatlistan finns sex kunder.

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Skicka frågor till dbo.dimCustomer](./media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för data lager enheter och data som lagras i din dedikerade SQL-pool (tidigare SQL DW). Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagrings utrymmet kan du pausa beräkningen när du inte använder den dedikerade SQL-poolen (tidigare SQL DW). Genom att pausa beräkning debiteras du bara för data lagring. Du kan återuppta beräkningen när du är redo att arbeta med data.

- Om du vill ta bort framtida avgifter kan du ta bort den dedikerade SQL-poolen (tidigare SQL DW).

Följ dessa steg för att rensa resurser som du inte längre behöver.

1. Logga in på [Azure Portal](https://portal.azure.com), välj din DEDIKERADe SQL-pool (tidigare SQL DW).

   ![Rensa resurser](./media/create-data-warehouse-portal/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När den dedikerade SQL-poolen (tidigare SQL DW) har pausats visas en knappen **Fortsätt** . Om du vill återuppta beräkningen väljer du **återuppta**.

3. Om du vill ta bort den dedikerade SQL-poolen (tidigare SQL DW) så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

4. Om du vill ta bort den server som du har skapat väljer du **sqlpoolservername.Database.Windows.net** i föregående bild och väljer sedan **ta bort**. Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resurs gruppen väljer du **myResourceGroup** och väljer sedan **ta bort resurs grupp**.

Vill du optimera och Spara på dina moln utgifter?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om att läsa in data i en dedikerad SQL-pool (tidigare SQL DW) fortsätter du till artikeln [Läs in data i en särskild SQL-pool](load-data-from-azure-blob-storage-using-copy.md) .