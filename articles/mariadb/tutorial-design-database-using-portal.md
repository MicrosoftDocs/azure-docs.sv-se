---
title: 'Självstudie: utforma en Azure Database for MariaDB-Azure Portal'
description: I den här självstudien beskrivs hur du skapar och hanterar en Azure Database for MariaDB-server och -databas med Azure-portalen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: fe503d0e579e30b0c355d3be3e8d02b2bd7a44a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664476"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Självstudie: skapa en Azure Database for MariaDB-databas med Azure-portalen

Azure Database for MariaDB är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Med Azure-portalen kan du enkelt hantera servern och utforma en databas.

I den här självstudien använder du Azure-portalen till att:

> [!div class="checklist"]
> * Skapa en Azure Database for MariaDB
> * Konfigurera serverbrandväggen
> * Använda kommandoradsverktyget mysql till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I en webbläsare går du till [Azure-portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server

Du skapar en Azure Database for MariaDB-server med en definierad uppsättning [beräknings- och lagringsresurser](concepts-pricing-tiers.md). Servern skapas i en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for MariaDB**. Du kan också skriva **MariaDB** i sökrutan för att hitta tjänsten.

   ![Gå till MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Välj panelen **Azure Database for MariaDB** . Ange eller välj informationen som krävs.

   ![Skapa formulär](./media/tutorial-design-database-using-portal/2-create-form.png)

    Inställning | Föreslaget värde | Fältbeskrivning
    ---|---|---
    Servernamn | *ett unikt servernamn* | Välj ett unikt namn för Azure Database for MariaDB-server. Till exempel **mydemoserver**. Domännamnet *.mariadb.database.azure.com* läggs till i det servernamn du anger. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
    Prenumeration | *din prenumeration* | Välj den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | **myresourcegroup** | Ange ett nytt resursgruppsnamn eller välj en befintlig resursgrupp.
    Välj källa | **Tom** | Välj **Tom** om du vill skapa en ny server. (Välj **Säkerhetskopiering** om du skapar en server från en geo-säkerhetskopia av en befintlig Azure Database for MariaDB-server).
    Inloggning för serveradministratör | **myadmin** | Ett inloggningskonto att använda när du ansluter till servern. Inloggningsnamnet för administratören får inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
    Lösenord | *ditt val* | Ange ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv).
    Bekräfta lösenordet | *ditt val*| Bekräfta administratörslösenordet.
    Location | *den region som är närmast dina användare*| Välj den plats som är närmast dina användare eller dina andra Azure-program.
    Version | *den senaste versionen*| Välj den senaste versionen (om du inte har särskilda krav på att använda en annan version).
    Prisnivå | Se beskrivningen. | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **pris nivå**  >  **generell användning**. Behåll standardvärdena för följande inställningar:<br><ul><li>**Compute-generering** (Gen 5)</li><li>**vCore** (4 virtuella kärnor)</li><li>**Lagring** (100 GB)</li><li>**Kvarhållningsperiod för säkerhetskopiering** (7 dagar)</li></ul><br>Välj **Geografiskt redundant** bland **redundansalternativen för säkerhetskopiering** om du vill använda geo-redundant lagring för dina serversäkerhetskopior. <br><br>Spara den valda prisnivån genom att välja **OK**. På nästa skärmbild visas dessa val.
    
   ![Prisnivå](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > När den **automatiska tillväxten** är aktive rad ökar lagringen när du närmar dig den tilldelade gränsen, utan att påverka arbets belastningen.

4. Klicka på **Granska + skapa**. Du kan klicka på knappen **meddelanden** i verktygsfältet för att övervaka distributions processen. Distributionen kan ta upp till 20 minuter.

## <a name="configure-the-firewall"></a>Konfigurera brandväggen

En Azure Database for MariaDB skyddas av en brandvägg. Som standard avvisas alla anslutningar till servern och databaserna på servern. Innan du ansluter till Azure Database for MariaDB för första gången ska du konfigurera brandväggen att lägga till klientdatorns offentliga nätverks-IP-adress (eller IP-adressintervall).

1. Välj servern du nyss skapade och sedan **Anslutningssäkerhet**.

   ![Anslutningssäkerhet](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Välj **Lägg till min IP** eller konfigurera brandväggsregler här. Kom ihåg att välja **Spara** när du har skapat reglerna.

Nu kan du ansluta till servern med kommandoradsverktyget mysql eller MySQL Workbench.

> [!TIP]
> Azure Database for MariaDB-servern kommunicerar över port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts av nätverkets brandvägg. I det här fallet måste IT-avdelningen öppna port 3306 för att du ska kunna ansluta till din Azure Database for MariaDB-server.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta värden för **servernamnet** (fullständigt kvalificerat) och **inloggningsnamnet för serveradministratören** för din Azure Database for MariaDB-server från Azure-portalen. Du använder det fullständigt kvalificerade servernamnet när du ska ansluta till servern med kommandoradsverktyget mysql.

1. Välj **Alla resurser** på menyn längst till vänster i [Azure-portalen](https://portal.azure.com/). Ange servernamnet och sök efter din Azure Database for MariaDB-server. Välj servernamnet så visas serverinformationen.

2. På sidan **Översikt** antecknar du värdena för **Servernamn** och **Inloggningsnamn för serveradministratören**. Du kan även välja **kopieringsknappen** bredvid respektive fält för att kopiera värdet till Urklipp.

   ![Serveregenskaper](./media/tutorial-design-database-using-portal/2-server-properties.png)

I vårt exempel är Server namnet **mydemoserver.MariaDB.Database.Azure.com** och inloggnings namnet för Server administratören är **unadmin- \@ mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Anslut till servern med mysql

Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) till att upprätta en anslutning till din Azure Database för MariaDB-server. Du kan köra kommandoradsverktyget mysql från Azure Cloud Shell i webbläsaren, eller från din egen dator med mysql-verktyg som installerats lokalt. Om du vill öppna Azure Cloud Shell klickar du på knappen **Try It** (Prova) i ett kodblock i den här artikeln, eller så öppnar du Azure-portalen och klickar på ikonen **>_** i det övre högra verktygsfältet.

Ange anslutningskommandot:

```bash
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas

När du är ansluten till servern skapar du en tom databas för att arbeta med:

```sql
CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen

Nu när du vet hur du ansluter till Azure Database for MariaDB-databasen kan du utföra några grundläggande uppgifter.

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Läsa in data till tabellerna

Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken för att infoga några datarader:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna

Kör följande frågor för att hämta information från databastabellen:

```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt

Anta att du av misstag har tagit bort en viktig databastabell och inte enkelt kan återställa dessa data. I Azure Database for MariaDB kan du återställa servern till en annan tidpunkt och skapa en kopia av databaserna på din nya server. Du kan använda den nya servern till att återställa dina data. Följande steg återställer exempelservern till en tidpunkt innan tabellen lades till:

1. Leta rätt på din Azure Database for MariaDB i Azure-portalen. På **översiktssidan** väljer du **Återställ**.

   ![Återställ en databas](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Ange eller välj följande information på sidan **Återställa**:

   ![Återställningsformulär](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Återställningspunkt**: Välj en tidpunkt du vill återställa till, inom den tidsperiod som visas. Var noga med att konvertera din lokala tidszon till UTC.
   - **Återställ till ny server**: Ange ett nytt servernamn som du vill återställa till.
   - **Plats**: Regionen är samma som källservern och kan inte ändras.
   - **Prisnivå**: Prisnivån är samma som källservern och kan inte ändras.
   
3. Klicka på **OK** för att återställa servern till en tidpunkt [återställa till en tidpunkt](./howto-restore-server-portal.md) innan tabellen togs bort. När du återställer en server skapas en ny kopia av servern vid den tidpunkt du anger. 

## <a name="next-steps"></a>Nästa steg
I den här kursen har du använt Azure Portal till följande:

> [!div class="checklist"]
> * Skapa en Azure Database for MariaDB
> * Konfigurera serverbrandväggen
> * Använda kommandoradsverktyget mysql till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
> [Så här ansluter du program till Azure Database for MariaDB](howto-connection-string.md)
