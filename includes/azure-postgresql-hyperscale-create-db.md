---
title: ta med fil
description: ta med fil
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 12/16/2020
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 54cc31cdb0ddc7694568d60b1446f1cf99663e95
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616634"
---
## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Skapa en Azure Database for PostgreSQL-storskalig (citus)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

### <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).


Följ de här stegen för att skapa en Azure Database för PostgreSQL-server:
1. Klicka på **skapa en resurs**  i det övre vänstra hörnet av Azure Portal.
2. Välj **databaser** från sidan **Nytt** och välj **Azure Database för PostgreSQL** från sidan **databaser**.
3. För distributions alternativet klickar du på knappen **skapa** under **Server grupp för citus-Server.**
4. Fyll i formuläret om den nya servern och uppge följande information:
   - Resurs grupp: Klicka på länken **Skapa ny** under text rutan för det här fältet. Ange ett namn, till exempel **myresourcegroup**.
   - Server grupp namn: Ange ett unikt namn för den nya server gruppen som också ska användas för en server under domän.
   - Administratörens användar namn: krävs för att vara värdet **citus** och kan inte ändras.
   - Lösen ord: måste innehålla minst åtta tecken och innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror (0-9) och icke-alfanumeriska tecken (!, $, #,% osv.)
   - Plats: Använd den plats som är närmast användarna för att ge dem snabbast åtkomst till data.

   > [!IMPORTANT]
   > Det Server administratörs lösen ord som du anger här krävs för att logga in på servern och databaserna. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

5. Klicka på **Konfigurera Server grupp**. Lämna inställningarna i avsnittet oförändrade och klicka på **Spara**.
6. Klicka på **Nästa: nätverks >** längst ned på skärmen.

7. På fliken **nätverk** klickar du på alternativ knappen **offentlig slut punkt** .
   ![Offentlig slut punkt vald](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Klicka på länken **+ Lägg till aktuell klient-IP-adress**.
   ![Klientens IP-adress har lagts till](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > Azure PostgreSQL-servern kommunicerar via port 5432. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 5432 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till ditt citus-kluster om inte IT-avdelningen öppnar port 5432.
   >

9. Klicka på **Granska + skapa** och sedan på **skapa** för att etablera servern. Etableringen tar några minuter.
10. Sidan omdirigeras för att övervaka distributionen. När Live-statusen ändras från **distributionen sker** till att **distributionen är klar** klickar du på meny alternativet **utdata** till vänster på sidan.
11. Sidan utdata kommer att innehålla ett koordinator värd namn med en knapp bredvid den för att kopiera värdet till Urklipp. Registrera den här informationen för senare användning.

### <a name="connect-to-the-database-using-psql"></a>Anslut till databasen med psql

När du skapar din Azure Database for PostgreSQL-server skapas en standard databas som heter **citus** . Du behöver en anslutnings sträng och administratörs lösen ordet för att ansluta till databas servern.

1. Hämta anslutnings strängen. På sidan Server grupp klickar du på meny alternativet **anslutnings strängar** . (Det är under **Inställningar**.) Hitta strängen som marker ATS som **psql**. Formatet är:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Kopiera strängen. Du måste ersätta "{ditt \_ lösen ord}" med det administrativa lösen ord som du valde tidigare. Systemet lagrar inte lösen ordet i klartext och kan därför inte Visa det åt dig i anslutnings strängen.

2. Öppna ett terminalfönster på den lokala datorn.

3. I prompten ansluter du till Azure Database for PostgreSQL-servern med verktyget [psql](https://www.postgresql.org/docs/current/app-psql.html) . Skicka din anslutnings sträng i citat tecken och se till att den innehåller ditt lösen ord:
   ```bash
   psql "host=..."
   ```

   Följande kommando ansluter till exempel noden koordinator för Server gruppen **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
