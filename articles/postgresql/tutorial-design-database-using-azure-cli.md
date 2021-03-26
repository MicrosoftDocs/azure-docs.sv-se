---
title: 'Självstudie: utforma en Azure Database for PostgreSQL-enskild server – Azure CLI'
description: Den här självstudien visar hur du skapar, konfigurerar och frågar din första Azure Database for PostgreSQL-enskilda server med Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 478fc175304a24536d28807915f4a4ec05a7158e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605883"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-azure-cli"></a>Självstudie: utforma en Azure Database for PostgreSQL-enskild server med Azure CLI 
I den här självstudien kommer du att använda Azure CLI (kommandoradsgränssnittet) och andra verktyg till följande:
> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL-server
> * Konfigurera serverbrandväggen
> * Använd [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)-verktyget för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

Du kan använda Azure Cloud Shell i webbläsaren eller [installera Azure CLI ]( /cli/azure/install-azure-cli) lokalt när du ska köra kommandon i den här självstudiekursen.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [az group create](/cli/azure/group). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server
Skapa en [Azure Database för PostgreSQL-server](overview.md) med kommandot [az postgres server create](/cli/azure/postgres/server). En server innehåller en grupp med databaser som hanteras som en grupp. 

I följande exempel skapas en server som heter `mydemoserver` i din resursgrupp `myresourcegroup` med serveradmin-inloggningen `myadmin`. Namnet på en server mappar till DNS-namnet och måste därför vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget värde. Det här är 5:e generationens server för generell användning med 2 virtuella kärnor.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```
Parametervärdet för sku-namn följer namngivningskonventionen {prisnivå}\_{compute-generering}\_{vCores} som i exemplen nedan:
+ `--sku-name B_Gen5_2` mappar till Basic, gen 5 och 2 virtuella kärnor.
+ `--sku-name GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
+ `--sku-name MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Se dokumentationen om [prisnivåer](./concepts-pricing-tiers.md) för mer information om giltiga värden per region och nivå.

> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.

Som standard skapas **postgres**-databasen under din server. [Postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-databasen är en standarddatabas som är avsedd för användare, verktyg och tredje parts program. 


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en brandväggsregel på Azure PostgreSQL-servernivå med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule). En brandväggsregel på servernivå låter ett externt program som en [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) eller en [PgAdmin](https://www.pgadmin.org/) ansluta till din server via Azure PostgreSQL-tjänstens brandvägg. 

Du kan ställa in en brandväggsregel som omfattar ett IP-intervall för att kunna ansluta från ditt nätverk. I följande exempel används [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) för att skapa en brandväggsregel `AllowMyIP` som tillåter anslutning från en enda IP-adress.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

Om du vill begränsa åtkomsten till Azure PostgreSQL-servern så att endast ditt nätverk får åtkomst kan du ställa in brandväggsregeln så att den endast omfattar ditt företagsnätverks IP-adressintervall.

> [!NOTE]
> Azure PostgreSQL-servern kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till din Azure SQL Database-server.
>

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **administratorLogin** och **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Ansluta till Azure Database for PostgreSQL-databas med psql
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

Skapa först en tabell och läs in lite data till den. Skapa till exempel en tabell som spårar lagerinformation:
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

## <a name="load-data-into-the-table"></a>Läsa in data i tabellen
Nu när du har skapat en tabell infogar du lite data i den. Kör följande fråga i den öppna kommandotolken för att infoga några datarader:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Nu har du lagt till två rader exempeldata i tabellen som du skapade tidigare.

## <a name="query-and-update-the-data-in-the-tables"></a>Ställ frågor mot och uppdatera data i tabellerna
Kör följande fråga för att hämta information från inventeringstabellen: 
```sql
SELECT * FROM inventory;
```

Du kan även uppdatera data i inventeringstabellen:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Du ser de uppdaterade värdena när du hämtar data:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Återställa en databas till en tidigare tidpunkt
Anta att du av misstag har tagit bort en tabell. Det kan du lätt åtgärda genom återställning. Med Azure Database for PostgreSQL kan du gå tillbaka till valfri tidpunkt från vilken du har serversäkerhetskopior (bestäms utifrån kvarhållningsperioden för säkerhetskopior som du konfigurerar) och återställa tidpunkten på en ny server. Du kan använda den nya servern till att återställa dina data. 

Följande kommandon återställer exempelservern till en tidpunkt innan tabellen lades till:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Följande parametrar behövs för kommandot `az postgres server restore`:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| name | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Välj en tidpunkt att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd datum- och tidsformatet ISO8601. Du kan använda din egen lokala tidszon som t.ex. `2017-04-13T05:59:00-08:00`, eller använda UTC Zulu-formatet `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidpunkt så skapas en ny server. Den kopieras som den ursprungliga servern vid den tidpunkt du anger. Plats- och prisnivåvärden för den återställda servern är samma som för källservern.

Kommandot är synkront och återgår när servern har återställts. När återställningen är klar letar du upp den nya server som skapades. Kontrollera att dina data har återställts som förväntat.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen *ta bort* på sidan *Översikt* för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig *borttagning* .


## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att använda Azure CLI (kommandoradsgränssnittet) och andra verktyg för att:
> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL-server
> * Konfigurera serverbrandväggen
> * Använd verktyget **psql** för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

> [!div class="nextstepaction"]
> [Skapa din första Azure Database for PostgreSQL med Azure-portalen](tutorial-design-database-using-azure-portal.md)
