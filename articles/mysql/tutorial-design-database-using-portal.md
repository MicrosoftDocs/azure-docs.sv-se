---
title: 'Självstudie: utforma en server – Azure Portal-Azure Database for MySQL'
description: I den här självstudien beskrivs hur du skapar och hanterar Azure Database for MySQL server och databas med Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: tutorial
ms.date: 3/20/2020
ms.custom: mvc
ms.openlocfilehash: 7559bc2246ca26cf2b14071396e075b28d2af3a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94532689"
---
# <a name="tutorial-design-an-azure-database-for-mysql-database-using-the-azure-portal"></a>Självstudie: Utforma en Azure Database for MySQL-databas med Azure Portal

Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. I Azure-portalen kan du enkelt hantera servern och utforma en databas.

I den här självstudien använder du Azure-portalen till att:

> [!div class="checklist"]
> * Skapa en Azure Database för MySQL-server
> * Konfigurera serverbrandväggen
> * Använda kommandoradsverktyget mysql till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna din webbläsare och gå till [Microsoft Azure Portal](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server

En Azure Database för MySQL-server skapas med en definierad uppsättning [compute- och lagringsresurser](./concepts-pricing-tiers.md). Servern skapas inom en [Azure-resursgrupp](../azure-resource-manager/management/overview.md).

1. Välj knappen **Skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for MySQL**. Om du inte hittar MySQL server under kategorin **databaser** klickar du på **Visa alla** för att visa alla tillgängliga databas tjänster. Du kan också skriva **Azure Database for MySQL** i sökrutan för att snabbt hitta tjänsten.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Navigate-to-MySQL.png" alt-text="Navigera till MySQL":::

3. Klicka på **Azure Database for MySQL** panel. Fyll i formuläret för Azure Database for MySQL.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-create-form.png" alt-text="Skapa formulär":::

    **Inställning** | **Föreslaget värde** | **Fältbeskrivning**
    ---|---|---
    Servernamn | Unikt servernamn | Välj ett unikt namn för Azure Database för MySQL-server. Till exempel mydemoserver. Domännamnet *.mysql.database.azure.com* läggs till i det servernamn du anger. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
    Prenumeration | Din prenumeration | Välj den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som resursen ska debiteras till.
    Resursgrupp | *myresourcegroup* | Ange ett nytt eller ett befintligt resursgruppnamn.
    Välj källa | *Tom* | Välj *Tom* om du vill skapa en ny server från början. (Du kan välja *Säkerhetskopiering* om du vill skapa en server från en geo-säkerhetskopia av en befintlig Azure Database for MySQL-server).
    Inloggning för serveradministratör | myadmin | Ett inloggningskonto som du använder när du ansluter till servern. Administratörens inloggnings namn får inte vara **azure_superuser**, **administratör**, **administratör**, **rot**, **gäst** eller **offentlig**.
    Lösenord | *Ditt val* | Ange ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #, % osv).
    Bekräfta lösenordet | *Ditt val*| Bekräfta administratörslösenordet.
    Location | *Den region som är närmast dina användare*| Välj den plats som är närmast dina användare eller dina andra Azure-program.
    Version | *Den senaste versionen*| Välj den senaste versionen (om du inte har särskilda behov som gör att du måste ha en annan version).
    Prisnivå | **Generell användning**, **Gen 5**, **2 virtuella kärnor**, **5 GB**, **7 dagar**, **Geografiskt redundant** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Prisnivå**. Välj sedan fliken **generell användning** . *gen 5*, *2 virtuella kärnor*, *5 GB* och *7 dagar* är standardvärden för **beräknings generering**, **vCore**, **lagring** och **bevarande period för säkerhets kopior**. Du kan lämna dessa skjutreglage som de är. Om du vill aktivera server säkerhets kopieringar i Geo-redundant lagring väljer du **geografiskt redundant** från **alternativen för redundans för säkerhets kopiering**. Spara den valda prisnivån genom att välja **OK**. På nästa skärmbild visas dessa val.

   :::image type="content" source="./media/tutorial-design-database-using-portal/3-pricing-tier.png" alt-text="Prisnivå":::

   > [!TIP]
   > När den **automatiska tillväxten** är aktive rad ökar lagringen när du närmar dig den tilldelade gränsen, utan att påverka arbets belastningen.

4. Klicka på **Granska + skapa**. Du kan klicka på knappen **meddelanden** i verktygsfältet för att övervaka distributions processen. Distributionen kan ta upp till 20 minuter.

## <a name="configure-firewall"></a>Konfigurera brandvägg

Azure Database for MySQL-databaser skyddas av en brandvägg. Som standard avvisas alla anslutningar till servern och databaserna på servern. Innan du ansluter till Azure Database for MySQL för första gången ska du konfigurera brandväggen och lägga till klientdatorns offentliga nätverks-IP-adress (eller IP-adressintervall).

1. Klicka på server du skapade och sedan på **Anslutningssäkerhet**.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Connection-security.png" alt-text="Anslutningssäkerhet":::
2. Välj **Lägg till min IP** eller konfigurera brandväggsregler här. Kom ihåg att klicka på **Spara** när du har skapat reglerna.
Nu kan du ansluta till servern med kommandoradsverktyget mysql eller gränssnittsverktyget MySQL Workbench.

> [!TIP]
> Azure Database for MySQL-servern kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 3306 bli nekad av nätverkets brandvägg. I så fall kommer du inte att kunna ansluta till Azure MySQL-servern om inte IT-avdelningen öppnar port 3306.

## <a name="get-connection-information"></a>Hämta anslutningsinformation

Hämta det fullständiga **servernamnet** och **inloggningsnamnet för serveradministratören** för din Azure Database for MySQL-server från Azure-portalen. Du använder det fullständigt kvalificerade servernamnet när du ska ansluta till servern med kommandoradsverktyget mysql.

1. I [Azure Portal](https://portal.azure.com/) klickar du på **Alla resurser** i menyn till vänster, skriver namnet och letar sedan rätt på din Azure Database for MySQL-server. Välj servernamnet så visas informationen.

2. Skriv ned **Servernamn** och **Inloggningsnamn för serveradministratör** på sidan **Översikt**. Du kan klicka på kopieringsknappen bredvid respektive fält för att kopiera till Urklipp.
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-server-properties.png" alt-text="4-2 Serveregenskaper":::

I det här exemplet är Server namnet *mydemoserver.mysql.Database.Azure.com* och inloggningen för Server administratören är *Administratörs \@ mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Anslut till servern med mysql

Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) till att upprätta en anslutning till din Azure Database för MySQL-server. Du kan köra kommandoradsverktyget mysql från Azure Cloud Shell i webbläsaren, eller från din egen dator med mysql-verktyg som installerats lokalt. Om du vill starta Azure Cloud Shell klickar du på knappen `Try It` i ett kodblock i den här artikeln, eller så öppnar du Azure-portalen och klickar på ikonen `>_` i det övre högra verktygsfältet.

Skriv anslutningskommandot:

```azurecli-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas

När du är ansluten till servern skapar du en tom databas för att arbeta med.

```sql
CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen

Nu när du vet hur du ansluter till Azure Database for MySQL-databasen kan du utföra några grundläggande uppgifter:

Skapa först en tabell och läs in lite data till den. Vi skapar en tabell som innehåller lagerinformation.

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Läs in data i tabellerna

Nu när du har en tabell kan du infoga lite data i den. Kör följande fråga i den öppna kommandotolken så at du löser in några datarader.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du två rader med exempeldata i tabellen du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna

Kör följande frågor för att hämta information från databastabellen.

```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i tabellerna.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Raden uppdateras när du hämtar data.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt

Anta att du av misstag har tagit bort en viktig databastabell och inte enkelt kan återställa dessa data. Med Azure Database for MySQL kan du återställa servern till en annan tidpunkt och skapa en kopia av databaserna på den nya servern. Du kan använda den nya servern till att återställa dina data. Följande steg återställer exempelservern till en tidpunkt innan tabellen lades till.

1. Leta rätt på din Azure Database for MySQL-databas i Azure-portalen. På sidan **Översikt** klickar du på **Återställ** i verktygsfältet. Sidan Återställ öppnas.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-restore-a-db.png" alt-text="10-1 Återställ en databas":::

2. Fyll i formuläret **Återställ** med den information som krävs.

   :::image type="content" source="./media/tutorial-design-database-using-portal/2-restore-form.png" alt-text="10-2 Återställningsformulär":::

   - **Återställningspunkt**: Välj en tidpunkt du vill återställa till, inom den tidsperiod som visas. Var noga med att konvertera din lokala tidszon till UTC.
   - **Återställ till ny server**: Ange ett nytt servernamn som du vill återställa till.
   - **Plats**: Regionen är samma som källservern och kan inte ändras.
   - **Prisnivå**: Prisnivån är samma som källservern och kan inte ändras.
   
3. Klicka på **OK** för att återställa servern för att [återställa till en tidpunkt](./howto-restore-server-portal.md) innan tabellen togs bort. När du återställer en server skapas en ny kopia av servern vid den tidpunkt du anger.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort resurs gruppen eller bara ta bort MySQL-servern. Följ dessa steg om du vill ta bort resurs gruppen:
1. I Azure Portal söker du efter och väljer **resurs grupper**. 
2. I listan resurs grupp väljer du namnet på din resurs grupp.
3. På sidan Översikt i resurs gruppen väljer du **ta bort resurs grupp**.
4. I bekräftelse dialog rutan skriver du namnet på din resurs grupp och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du använt Azure Portal till följande:

> [!div class="checklist"]
> * Skapa en Azure Database för MySQL-server
> * Konfigurera serverbrandväggen
> * Använda kommandoradsverktyget mysql till att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
> [Så ansluter du program till Azure Database for MySQL](./howto-connection-string.md)