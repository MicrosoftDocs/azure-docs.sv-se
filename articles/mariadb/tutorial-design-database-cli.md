---
title: 'Självstudie: utforma en Azure Database for MariaDB – Azure CLI'
description: Den här självstudien beskriver hur du skapar och hanterar en Azure Database for MariaDB-server och en databas med Azure CLI från kommandoraden.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e40a0a63069b09ddcc3e7014e62b2f1cb9806bfd
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664492"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Självstudie: Utforma en Azure Database for MariaDB med Azure CLI

Azure Database for MariaDB är en relationsdatabastjänst i Microsoft-molnet som är baserad på databasmotorn MariaDB Community Edition. I den här självstudien kommer du att använda Azure CLI (kommandoradsgränssnittet) och andra verktyg till följande:

> [!div class="checklist"]
> * Skapa en Azure Database for MariaDB
> * Konfigurera serverbrandväggen
> * Använd [kommando rads verktyget MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

Om du har flera prenumerationer väljer du en lämplig prenumerationen där resursen ligger eller faktureras. Välj en specifik prenumerations-ID under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [az group create](/cli/azure/group#az-group-create). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server
Skapa en Azure Database for MariaDB-server med kommandot `az mariadb server create`. En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

I följande exempel skapas en Azure Database för MariaDB-server i `westus` i resursgruppen `myresourcegroup` med namnet `mydemoserver`. Servern har en administratörsinloggning med namnet `myadmin`. Det här är 5:e generationens server för generell användning med 2 virtuella kärnor. Ersätt `<server_admin_password>` med ditt eget värde.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Parametervärdet för sku-namn följer namngivningskonventionen {prisnivå}\_{compute-generering}\_{vCores} som i exemplen nedan:
+ `--sku-name B_Gen5_4` mappar till Basic, Gen 5 och 4 vCores.
+ `--sku-name GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
+ `--sku-name MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Se dokumentationen om [prisnivåer](./concepts-pricing-tiers.md) för mer information om giltiga värden per region och nivå.

> [!IMPORTANT]
> Det användarnamn och lösenord för serveradministration du anger här krävs för inloggning på servern och databaserna senare i den här snabbstarten. Kom ihåg eller skriv ned den här informationen så att du kan använda den senare.


## <a name="configure-firewall-rule"></a>Konfigurera brandväggsregeln
Skapa en brandväggsregel på Azure Database for MariaDB-servernivå med kommandot `az mariadb server firewall-rule create`. En brandväggsregel på servernivå gör att externa program, som **mysql**-kommandoradsverktyget eller MySQL Workbench, kan ansluta till servern via Azure MariaDB-tjänstens brandvägg.

I följande exempel skapas en brandväggsregel som kallas `AllowMyIP` som tillåter anslutningar från den specifika IP-adressen 192.168.0.1. Ersätt en IP-adress eller omfång av IP-adresser som motsvarar platsen som du kommer att ansluta från.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Anslut till servern med mysql
Använd [kommandoradsverktyget mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) till att upprätta en anslutning till din Azure Database för MariaDB-server. I det här exemplet är kommandot:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Skapa en tom databas
När du är ansluten till servern skapar du en tom databas.
```sql
mysql> CREATE DATABASE mysampledb;
```

Kör följande kommando från prompten för att växla anslutningen till databasen du skapade nyss:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Skapa tabeller i databasen
Nu när du vet hur du ansluter till Azure Database for MariaDB-databasen kan du utföra några grundläggande uppgifter.

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
Anta att du har tagit bort den här tabellen av misstag. Det kan du lätt åtgärda genom återställning. Med Azure Database for MariaDB kan du gå tillbaka till valfri tidpunkt under de senaste 35 dagarna och återställa tidpunkten på en ny server. Du kan använda den nya servern till att återställa dina data. Följande steg återställer exempelservern till en tidpunkt innan tabellen lades till.

Du behöver följande information vid återställningen:

- Återställningspunkt: Välj en tidpunkt innan servern ändrades. Måste vara senare än eller lika med källdatabasens äldsta säkerhetskopiering.
- Målserver: Ange ett nytt servernamn som du vill återställa till.
- Källserver: Ange namnet på den server du vill återställa från.
- Plats: Du kan inte välja region, som standard är det samma som källservern.

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Följande parametrar behövs för kommandot `az mariadb server restore`:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| name | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Välj en tidpunkt att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd datum- och tidsformatet ISO8601. Du kan använda din egen lokala tidszon som t.ex. `2017-04-13T05:59:00-08:00`, eller använda UTC Zulu-formatet `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidpunkt så skapas en ny server. Den kopieras som den ursprungliga servern vid den tidpunkt du anger. Plats- och prisnivåvärden för den återställda servern är samma som för källservern.

Kommandot är synkront och återgår när servern har återställts. När återställningen är klar letar du upp den nya server som skapades. Kontrollera att dina data har återställts som förväntat.

## <a name="next-steps"></a>Nästa steg
I de här självstudierna lärde du dig att:
> [!div class="checklist"]
> * Skapa en Azure Database for MariaDB-server
> * Konfigurera serverbrandväggen
> * Använd [kommando rads verktyget MySQL](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) för att skapa en databas
> * Läsa in exempeldata
> * Söka i data
> * Uppdatera data
> * Återställa data