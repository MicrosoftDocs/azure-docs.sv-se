---
title: 'Snabb start: skapa server-Azure Portal-Azure Database for PostgreSQL-flexibel Server'
description: Snabb starts guide för att skapa och hantera en Azure Database for PostgreSQL-flexibel server med hjälp av Azure Portal användar gränssnittet.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 6d10298b016cbcf362af2d272f9fa822db6e569d
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604761"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Snabb start: skapa en Azure Database for PostgreSQL-flexibel server i Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabb starten visar hur du skapar en Azure Database for PostgreSQL-flexibel server på ungefär fem minuter med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Öppna webbläsaren och gå till [portalen](https://portal.azure.com/). Ange dina autentiseringsuppgifter och logga in på portalen. Standardvyn är instrumentpanelen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

En Azure Database for PostgreSQL-server skapas med en konfigurerad uppsättning [beräknings- och lagringsresurser](./concepts-compute-storage.md). Servern skapas inom en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md).

Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.

2. Välj **databaser**  >  **Azure Database for PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL på menyn":::

3. Välj alternativet för **flexibel Server** distribution.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Välj Azure Database for PostgreSQL-flexibel Server distributions alternativ":::

4. Fyll i formuläret **grundläggande** med följande information:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Skapa en server":::

    Inställning|Föreslaget värde|Beskrivning
    ---|---|---
    Prenumeration|Ditt prenumerationsnamn|Den Azure-prenumeration som ska användas för servern. Om du har flera prenumerationer väljer du den prenumeration som du vill fakturera för resursen.
    Resursgrupp|*myresourcegroup*| Ett nytt resursgruppnamn eller ett befintligt namn i prenumerationen.
    Servernamn |*mydemoserver*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domännamnet *postgres.database.azure.com* läggs till i det servernamn du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla minst 3 och upp till 63 tecken.
    Användarnamn för administratör |*myadmin*| Ett eget inloggningskonto att använda när du ansluter till servern. Inloggnings namnet för administratören får inte vara **azure_superuser**, **azure_pg_admin**, **admin**, **Administrator**, **root**, **Guest** eller **Public**. Den kan inte börja med **PG_**.
    Lösenord |Ditt lösenord| Ett nytt lösenord för serverns administratörskonto. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till och med 9) och icke-alfanumeriska tecken (!, $, #, % osv.).
    Location|Den region som är närmast dina användare| Den plats som är närmast dina användare.
    Version|Senaste huvudversion| Den senaste PostgreSQL-huvudversionen, om du inte har andra särskilda krav.
    Beräkning och lagring | **Generell användning**, **4 virtuella kärnor**, **512 GB**, **7 dagar** | Konfigurationerna för beräkning, lagring och säkerhetskopiering för den nya servern. Välj **Konfigurera Server**. *Generell användning*, *4 virtuella kärnor*, *512 GB* och *7 dagar* är standardvärden för **beräknings nivå**, **vCore**, **lagring** och **bevarande period för säkerhets kopior**. Du kan lämna skjutreglagen som de är eller justera dem. Spara den valda prisnivån genom att välja **OK**. På nästa skärmbild visas dessa val.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="Fönstret pris nivå":::
    
5. Konfigurera nätverks alternativ

    På fliken nätverk kan du välja hur servern kan kontaktas. Azure Database för PostgreSQL skapar en brandvägg på server-nivå. Den förhindrar att externa program och verktyg ansluter till servern eller databaser på servern, om du inte konfigurerar en regel som öppnar brandväggen för specifika IP-adresser. Vi rekommenderar att du gör servern offentligt tillgänglig:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="Fönstret nätverk":::

    Och begränsar det till din egen klient-IP-adress:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Välj Lägg till aktuell klient-IP-adress":::

6. Välj **Granska + skapa** för att granska dina val. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.

7. Välj ikonen **Aviseringar** (en bjällra) i verktygsfältet för att övervaka distributionsprocessen. När distributionen är färdig kan du välja **Fäst på instrumentpanelen**. Då skapas en panel för den här servern på instrumentpanelen i Azure Portal som fungerar som en genväg till serverns **översiktssida**. Om du väljer **Gå till resurs** öppnas serverns **översiktssida**.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Fönstret meddelanden":::

   Som standard skapas en **postgres**-databas under din server. [Postgres](https://www.postgresql.org/docs/12/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. (Den andra standarddatabasen är **azure_maintenance**. Dess funktion är att skilja hanterade tjänstprocesser från användaråtgärder. Du har inte åtkomst till den här databasen.)

    > [!NOTE]
    > Anslutningar till din Azure Database för PostgreSQL-server kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 5432.
    >

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När du skapar din Azure Database för PostgreSQL-server skapas även en standarddatabas med namnet **postgres**. Du behöver det fullständiga servernamnet och inloggningsuppgifterna för administratör för att ansluta till databasservern. Du kan ha antecknat de här värdena tidigare i snabbstartsartikeln. I annat fall hittar du enkelt servernamnet och inloggningsuppgifterna på serversidan **Översikt** i portalen.

Öppna serverns **Översikt**-sida. Anteckna **Servernamn** och **Inloggningsnamn för serveradministratören**. Håll markören över varje fält så att kopieringssymbolen visas till höger om texten. Välj kopieringssymbolen för att kopiera värdena.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="Sidan Server översikt":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Anslut till PostgreSQL-databasen med psql

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Om din klientdator har PostgreSQL installerat, kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html) för att ansluta till en Azure PostgreSQL-server. Nu använder vi psql-kommandoradsverktyget för att ansluta till Azure PostgreSQL-servern.

1. Kör följande psql-kommando för att ansluta till en Azure Database för PostgreSQL-server

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Följande kommando ansluter exempelvis till standarddatabasen som heter **postgres** på din PostgreSQL-server **mydemoserver.postgres.database.azure.com** med hjälp av autentiseringsuppgifter. Ange den `<server_admin_password>` som du valde när du uppmanades att ange lösenordet.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   När du har anslutit visar psql-verktyget en postgres-kommandotolk där du skriver sql-kommandon. Vid den första anslutningen kanske en varning visas eftersom det psql-verktyg du använder kan vara en annan version än Azure Database for PostgreSQL-serverversionen.

   Exempel på psql-utdata:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Om brandväggen inte är konfigurerad att tillåta IP-adressen för din klient uppstår följande fel:
   >
   > "psql: allvarligt: ingen pg_hba. conf-post för värden `<IP address>` , användare" mina administratörer ", databas" postgres ", SSL på oåterkallelig: SSL-anslutning krävs. Specify SSL options and retry.
   >
   > Kontrol lera att klientens IP-adress tillåts i steg ovan för brand Väggs regler.

2. Skapa en tom databas med namnet "mypgsqldb" i kommandotolken genom att skriva följande kommando:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. I kommandotolken kör du följande kommando för att växla anslutning till den nyligen skapade databasen **mypgsqldb**:

    ```bash
    \c mypgsqldb
    ```

4. Ange `\q` och tryck på retur för att avsluta psql.

Du anslöt till Azure Database for PostgreSQL-servern via psql, och skapade en tom användardatabas.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan rensa de resurser som du skapade i snabbstarten på något av två sätt. Du kan ta bort den Azure-resursgrupp som innehåller alla resurser i resursgruppen. Om du vill bevara alla andra resurser tar du bara bort serverresursen.

> [!TIP]
> De andra snabbstarterna i den här samlingen bygger på den här snabbstarten. Rensa inte upp resurserna som du skapade i den här snabbstarten om du tänker fortsätta att arbeta med efterföljande snabbstarter. Om du inte planerar att fortsätta följer du dessa steg för att ta bort alla resurser som har skapats i den här snabbstarten i portalen.

Ta bort hela resursgruppen, inklusive den nya servern:

1. Leta reda på resursgruppen i portalen. Välj **Resursgrupper** på den vänstra menyn. Välj sedan namnet på resursgruppen, som i vårt exempel **myresourcegroup**.

2. Välj **Ta bort** på din resursgruppssida. Ange namnet på resurs gruppen, till exempel **myresourcegroup**, i text rutan för att bekräfta borttagningen. Välj **Ta bort**.

Ta bort bara den nyligen skapade servern:

1. Leta upp servern i portalen om du inte har den öppen. Välj **Alla resurser** på menyn till vänster. Sök sedan efter den server som du skapade.

2. Välj **Ta bort** på sidan **Översikt**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="Knappen Ta bort":::

3. Bekräfta namnet på servern som du vill ta bort och visa de databaser under den som påverkas. Ange Server namnet i text rutan, till exempel **mydemoserver**. Välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Distribuera en django-app med App Service och PostgreSQL](tutorial-django-app-service-postgres.md)