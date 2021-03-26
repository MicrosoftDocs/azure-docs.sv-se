---
title: 'Självstudie: utforma en Azure Database for PostgreSQL-enskild server – Azure Portal'
description: Den här självstudien visar hur du utformar din första Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: dd7ae71d8c3bc5415654527d5a650e0f09a99168
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605823"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Självstudie: utforma en Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. I Azure-portalen kan du enkelt hantera servern och utforma en databas.

I den här självstudien använder du Azure-portalen till att:
> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL-server
> * Konfigurera serverbrandväggen
> * Använd [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

## <a name="prerequisites"></a>Förutsättningar
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-an-azure-database-for-postgresql"></a>Skapa en Azure Database för PostgreSQL

En Azure Database för PostgreSQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-pricing-tiers.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på **skapa en resurs**  i det övre vänstra hörnet av Azure Portal.
2. Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/1-create-database.png" alt-text="Azure Database för PostgreSQL – Skapa databasen":::

3. Välj distributions alternativet **enskild server** .

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/select-deployment-option.png" alt-text="Välj Azure Database for PostgreSQL – distributions alternativ för enskild server":::

4. Fyll i formuläret **grundläggande** med följande information:

    :::image type="content" source="./media/tutorial-design-database-using-azure-portal/create-basics.png" alt-text="Skapa en server":::

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    Servernamn |*mydemoserver*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domännamnet *postgres.database.azure.com* läggs till i det servernamn du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla minst 3 och upp till 63 tecken.
    Datakälla | *Ingen* | Välj *Ingen* om du vill skapa en ny server från grunden. (Du väljer *Säkerhetskopiering* om du skapar en server från en geo-säkerhetskopia av en befintlig Azure Database for PostgreSQL-server).
    Användarnamn för administratör |*myadmin*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggnings namnet för administratören får inte vara **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest** eller **Public**. Den kan inte börja med **PG_**.
    Lösenord |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till och med 9) och icke-alfanumeriska tecken (!, $, #, % osv.).
    Location|Den region som är närmast dina användare| Den plats som är närmast dina användare.
    Version|Senaste huvudversion| Den senaste PostgreSQL-huvudversionen, om du inte har andra särskilda krav.
    Beräkning och lagring | **Generell användning**, **Gen 5**, **2 virtuella kärnor**, **5 GB**, **7 dagar**, **Geografiskt redundant** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server**. Välj sedan fliken **generell användning** . *gen 5*, *4 virtuella kärnor*, *100 GB* och *7 dagar* är standardvärden för **beräknings generering**, **vCore**, **lagring** och **bevarande period för säkerhets kopior**. Du kan lämna skjutreglagen som de är eller justera dem. Välj **Geografiskt redundant** bland **redundansalternativen för säkerhetskopiering** om du vill använda geo-redundant lagring för dina serversäkerhetskopior. Spara den valda prisnivån genom att välja **OK**. På nästa skärmbild visas dessa val.

   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad. Mer information finns på [sidan med priser](https://azure.microsoft.com/pricing/details/postgresql/).
   > 

    :::image type="content" source="./media/quickstart-create-database-portal/2-pricing-tier.png" alt-text="Fönstret pris nivå":::

    > [!TIP]
    > När den **automatiska tillväxten** är aktive rad ökar lagringen när du närmar dig den tilldelade gränsen, utan att påverka arbets belastningen.

5. Välj **Granska + skapa** för att granska dina val. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.

6. Välj ikonen **Aviseringar** (en bjällra) i verktygsfältet för att övervaka distributionsprocessen. När distributionen är färdig kan du välja **Fäst på instrumentpanelen**. Då skapas en panel för den här servern på instrumentpanelen i Azure Portal som fungerar som en genväg till serverns **översiktssida**. Om du väljer **Gå till resurs** öppnas serverns **översiktssida**.

    :::image type="content" source="./media/quickstart-create-database-portal/3-notifications.png" alt-text="Fönstret meddelanden":::
   
   Som standard skapas en **postgres**-databas under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. (Den andra standarddatabasen är **azure_maintenance**. Dess funktion är att skilja hanterade tjänstprocesser från användaråtgärder. Du har inte åtkomst till den här databasen.)


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Azure Database for PostgreSQL-tjänsten använder en brandvägg på servernivå. Brandväggen förhindrar som standard att alla externa program och verktyg ansluter till servern eller databaser på servern, om inte en brandväggsregel skapas som öppnar brandväggen för specifika IP-adressintervall. 

1. När distributionen är färdig klickar du på **Alla resurser** på den vänstra menyn och anger namnet **mydemoserver** för att söka efter servern som du nyss skapade. Klicka på servernamnet som listas i sökresultatet. **Översikt**-sidan för din server öppnas och innehåller alternativ ytterligare konfiguration.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure Database för PostgreSQL – sök efter server":::

2. På sidan server väljer du **anslutnings säkerhet**. 

3. Klicka i text rutan under **regel namn** och Lägg till en ny brand Väggs regel för att ange IP-intervallet för anslutning. Ange IP-adressintervall. Klicka på **Spara**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/5-firewall-2.png" alt-text="Azure Database för PostgreSQL – Skapa brandväggsregel":::

4. Klicka på **Spara** och sedan på **X** för att stänga sidan **anslutnings säkerhet** .

   > [!NOTE]
   > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till din Azure SQL Database-server om inte din IT-avdelning öppnar port 5432.
   >

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När du skapade Azure Database for PostgreSQL-servern, skapades även standarddatabasen **postgres**. För att ansluta till din databasserver, måste du ange värddatorinformation och autentiseringsuppgifter för åtkomst.

1. På den vänstra menyn i Azure Portal klickar du på **Alla resurser** och söker efter den server som du nyss skapade.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure Database för PostgreSQL – sök efter server":::

2. Klicka på servernamnet **mydemoserver**.

3. Välj serverns **översikt**-sida. Anteckna **servernamn** och **inloggningsnamnet för serveradministratören**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/6-server-name.png" alt-text="Azure Database för PostgreSQL – inloggning för serveradministratör":::


## <a name="connect-to-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql
Om din klient dator har PostgreSQL installerat kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html)eller Azures moln konsol för att ansluta till en Azure postgresql-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure Database för PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database for PostgreSQL-databas:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Följande kommando ansluter exempelvis till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Om du föredrar att använda en URL-sökväg för att ansluta till postgres, kodar URL @-tecknet i användar namnet med `%40` . Anslutnings strängen för psql skulle exempelvis vara,
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. När du är ansluten till servern skapar du en tom databas:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. I prompten, kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for PostgreSQL-databasen, kan du utföra några grundläggande uppgifter:

Skapa först en tabell och läs in lite data till den. Nu ska vi skapa en tabell som spårar inventeringsinformation med följande SQL-kod:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Du kan se den nyligen skapade tabellen i listan med tabeller genom att skriva:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Läs in data i tabellerna
Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i inventeringstabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande fråga för att hämta information från inventeringsdatabastabellen. 
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellen.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Du ser de uppdaterade värdena när du hämtar data.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Återställa data till en tidigare tidpunkt
Anta att du har tagit bort den här tabellen av misstag. Det är inte så enkelt att återställa från den här situationen. Med Azure Database for PostgreSQL kan du gå tillbaka till valfri tidpunkt från vilken du har serversäkerhetskopior (bestäms utifrån kvarhållningsperioden för säkerhetskopior som du konfigurerar) och återställa tidpunkten på en ny server. Du kan använda den nya servern till att återställa dina data. Med följande steg återställs servern **mydemoserver** till en tidpunkt innan inventeringstabellen lades till.

1. I Azure Database for PostgreSQL på sidan **Översikt** för servern, klickar du på **Återställ** i verktygsfältet. Sidan **Återställ** öppnas.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png" alt-text="Skärm bild som visar sidan för Azure Database for PostgreSQL * * översikt * * för servern och markerar knappen Återställ.":::

2. Fyll i formuläret **Återställ** med den information som behövs:

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png" alt-text="Azure-portalen – Återställningsalternativ för formulär":::

   - **Återställningspunkt**: Välj en tidpunkt innan servern ändrades
   - **Målserver**: Ange ett nytt servernamn som du vill återställa till
   - **Plats**: Du kan inte välja region, som standard är det samma som källservern
   - **Prisnivå**: Du kan inte ändra det här värdet när du återställer en server. Det är samma som källservern. 
3. Klicka på **OK** om du vill [återställa servern till en tidpunkt](./howto-restore-server-portal.md) innan tabellen togs bort. Om du återställer en server till en annan tidpunkt skapas en dubblett av den ursprungliga servern vid den tidpunkt du angav, förutsatt att den infaller inom kvarhållningsperioden för din [prisnivå](./concepts-pricing-tiers.md).

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen *ta bort* på sidan *Översikt* för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig *borttagning* .

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du använder Azure-portalen och andra verktyg för att:
> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL-server
> * Konfigurera serverbrandväggen
> * Använd verktyget **psql** för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
>[Utforma din första Azure Database for PostgreSQL med hjälp av Azure CLI](tutorial-design-database-using-azure-cli.md)