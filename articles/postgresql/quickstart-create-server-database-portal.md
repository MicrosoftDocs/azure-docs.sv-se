---
title: 'Snabb start: skapa server-Azure Portal-Azure Database for PostgreSQL-enskild server'
description: I den här snabb starts guiden ska du skapa och hantera en Azure Database for PostgreSQL-server med hjälp av Azure Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492394"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Snabb start: skapa en Azure Database for PostgreSQL-server med hjälp av Azure Portal

Azure Database för PostgreSQL är en hanterad tjänst som du använder för att köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Den här snabb starten visar hur du skapar en enda Azure Database for PostgreSQL Server och ansluter till den.

## <a name="prerequisites"></a>Förutsättningar
En Azure-prenumeration krävs. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server
Gå till [Azure Portal](https://portal.azure.com/) för att skapa en Azure Database for PostgreSQL enskild Server-databas. Sök efter och välj *Azure Database for PostgreSQL-servrar*.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Hitta Azure Database for PostgreSQL.":::

1. Välj **Lägg till**.

2. På sidan Skapa en Azure Database for PostgreSQL väljer du  **enskild server**.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Välj en enskild server":::

3. Ange nu **grunderna** i formuläret med följande information.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Skärm bild som visar fliken grundläggande för att skapa en enskild server.":::

   |Inställning|Föreslaget värde|Beskrivning|
   |:---|:---|:---|
   |Prenumeration|ditt prenumerations namn|Välj önskad Azure-prenumeration.|
   |Resursgrupp|*myresourcegroup*| En ny eller en befintlig resurs grupp från din prenumeration.|
   |Servernamn |*mydemoserver*|Ett unikt namn som identifierar Azure Database för PostgreSQL-servern. Domän namnet *postgres.Database.Azure.com* läggs till i det Server namn som du anger. Servernamnet får bara innehålla gemener, siffror och bindestreck (-). Det måste innehålla mellan 3 och 63 tecken.|
   |Datakälla | Inget | Välj **Ingen** om du vill skapa en ny server från grunden. Välj endast **säkerhets kopiering** om du har återställt från en geo-säkerhetskopia av en befintlig server.|
   |Användarnamn för administratör |*myadmin*| Ange ditt användar namn för Server administratör. Den kan inte börja med **PG_** och dessa värden är inte tillåtna: **azure_superuser**, **azure_pg_admin**, **admin**, **administratör**, **rot**, **gäst** eller **offentlig**.|
   |Lösenord |ditt lösenord| Ett nytt lösen ord för Server administratörs användaren. Det måste innehålla 8 till 128 tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0 till 9) och icke-alfanumeriska tecken (till exempel!, $, #,%).|
   |Location|önskad plats| Välj en plats i list rutan.|
   |Version|Senaste huvudversion| Den senaste PostgreSQL-huvudversionen, om du inte har andra särskilda krav.|
   |Beräkning och lagring | *Använd standardinställningarna*| Standard pris nivån är **generell användning**  med **4 virtuella kärnor** och **100 GB** lagring. Kvarhållning av säkerhets kopior har angetts till **7 dagar** med **geografiskt redundant** säkerhets kopierings alternativ.<br/>Läs om [prissättningen](https://azure.microsoft.com/pricing/details/postgresql/server/) och uppdatera standardinställningarna om det behövs.|


   > [!NOTE]
   > Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapats på grund pris nivån inte kan skalas senare till Generell användning eller Minnesoptimerade.

5. Välj **Granska + skapa** för att granska dina val. Välj **Skapa** för att etablera servern. Den här åtgärden kan ta några minuter.
    > [!NOTE]
    > En tom databas, **postgres**, skapas. Du hittar också en **azure_maintenance** databas som används för att avgränsa hanterade tjänst processer från användar åtgärder. Du har inte åtkomst till **azure_maintenance** databasen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="lyckad distribution.":::

[Har du problem? Berätta för oss.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel
Som standard är den server som du skapar inte offentligt tillgänglig. Du måste ge behörighet till din IP-adress. Gå till Server resursen i Azure Portal och välj **anslutnings säkerhet** på den vänstra menyn för Server resursen. Om du inte är säker på hur du hittar din resurs, se [Öppna resurser](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Skärm bild som visar brand Väggs regler för anslutnings säkerhet.":::

Välj **Lägg till aktuell klient-IP-adress** och välj sedan **Spara**. Du kan lägga till fler IP-adresser eller ange ett IP-intervall för att ansluta till servern från dessa IP-adresser. Mer information finns [i brand Väggs regler i Azure Database for PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> Undvik anslutnings problem genom att kontrol lera om nätverket tillåter utgående trafik via port 5432. Azure Database for PostgreSQL använder den porten.

[Har du problem? Berätta för oss.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Anslut till servern med psql

Du kan använda [psql](http://postgresguide.com/utilities/psql.html) eller [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), som är populära postgresql-klienter. I den här snabb starten ska vi ansluta med psql i [Azure Cloud Shell](../cloud-shell/overview.md) i Azure Portal.

1. Anteckna Server namnet, inloggnings namnet för Server administratören, lösen ordet och prenumerations-ID: t för den nya servern från **översikts** avsnittet på servern.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="Hämta anslutnings information.":::


2. Öppna Azure Cloud Shell i portalen genom att välja ikonen på den övre vänstra sidan.

   > [!NOTE]
   > Om du öppnar Cloud Shell för första gången visas en uppfrågad om att skapa en resurs grupp och ett lagrings konto. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Skärm bild som visar Server information och ikonen för att öppna Azure Cloud Shell.":::

3. Kör följande kommando i Azure Cloud Shell terminalen. Ersätt värdena med det faktiska Server namnet och inloggnings namnet för administratörs användaren. Använd den tomma databasen **postgres** med administratörs användaren i det här formatet: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Så här ser upplevelsen ut i Cloud Shell terminalen:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Skapa en databas med namnet **gäst** i samma Azure Cloud Shell terminal.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Växla anslutningar till den nyligen skapade **gäst** databasen.

   ```bash
   \c guest
   ```
6. Skriv `\q` och välj sedan Retur för att stänga psql.

[Har du problem? Berätta för oss.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Rensa resurser
Du har skapat en Azure Database for PostgreSQL-server i en resurs grupp. Om du inte tror att du behöver dessa resurser i framtiden kan du ta bort dem genom att ta bort antingen resurs gruppen eller PostgreSQL-servern.

Så här tar du bort resursgruppen:

1. I Azure Portal söker du efter och väljer **resurs grupper**.
2. I listan resurs grupp väljer du namnet på din resurs grupp.
3. På sidan **Översikt** i resurs gruppen väljer du **ta bort resurs grupp**.
4. I bekräftelse dialog rutan anger du namnet på din resurs grupp och väljer sedan **ta bort**.

Om du vill ta bort servern väljer du knappen **ta bort** på sidan **Översikt** på servern:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Skärm bild som visar knappen för att ta bort en server.":::

[Har du problem? Berätta för oss.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera din databas med export och import](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Utforma en databas](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Hittar du inte det du letar efter? Berätta för oss.](https://aka.ms/postgres-doc-feedback)