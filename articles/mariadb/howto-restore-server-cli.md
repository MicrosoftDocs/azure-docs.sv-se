---
title: Säkerhetskopiera och återställa – Azure CLI – Azure Database for MariaDB
description: Lär dig hur du säkerhetskopierar och återställer en server Azure Database for MariaDB med hjälp av Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: af197f2dd144e7541f669fdd67b58c38d91b4400
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777139"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Återställning och återställning av en server i Azure Database for MariaDB hjälp av Azure CLI

Azure Database for MariaDB säkerhetskopieras regelbundet för att aktivera återställningsfunktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt på en ny server.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en [Azure Database for MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här guiden kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="set-backup-configuration"></a>Konfigurera säkerhetskopiering

Du kan välja mellan att konfigurera servern för lokalt redundanta säkerhetskopieringar eller geografiskt redundanta säkerhetskopieringar när servern skapas.

> [!NOTE]
> När en server har skapats kan inte den typ av redundans den har, geografiskt redundant eller lokalt redundant, växlas.
>

När du skapar en server via `az mariadb server create` kommandot avgör `--geo-redundant-backup` parametern redundansalternativ för säkerhetskopiering. Om `Enabled` , tas geo-redundanta säkerhetskopieringar. Eller om `Disabled` lokalt redundanta säkerhetskopieringar tas.

Kvarhållningsperioden för säkerhetskopior anges av parametern `--backup-retention` .

Mer information om hur du anger dessa värden under skapa finns i [Azure Database for MariaDB CLI-snabbstart för server.](quickstart-create-mariadb-server-database-using-azure-cli.md)

Kvarhållningsperioden för säkerhetskopior för en server kan ändras på följande sätt:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Föregående exempel ändrar kvarhållningsperioden för säkerhetskopior för mydemoserver till 10 dagar.

Kvarhållningsperioden för säkerhetskopior styr hur långt tillbaka i tiden en återställning till en viss tidpunkt kan hämtas, eftersom den baseras på tillgängliga säkerhetskopior. Återställning till tidpunkt beskrivs närmare i nästa avsnitt.

## <a name="server-point-in-time-restore"></a>Återställning till tidpunkt för server

Du kan återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern lämnas som den är. Om en tabell till exempel av misstag tas bort kl. 12 i dag kan du återställa till tiden strax före 12.00. Sedan kan du hämta den saknade tabellen och data från den återställda kopian av servern.

Om du vill återställa servern använder du kommandot Azure CLI [az mariadb server restore.](/cli/azure/mariadb/server#az_mariadb_server_restore)

### <a name="run-the-restore-command"></a>Kör kommandot restore

Om du vill återställa servern anger du följande kommando i Azure CLI-kommandotolken:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Kommandot `az mariadb server restore` kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resursgruppen där källservern finns.  |
| name | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Välj en tidpunkt att återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd datum- och tidsformatet ISO8601. Du kan till exempel använda din egen lokala tidszon, till exempel `2018-03-13T05:59:00-08:00` . Du kan också använda UTC Zulu-formatet, till exempel `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

När du återställer en server till en tidigare tidpunkt skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats- och prisnivåvärdena för den återställda servern förblir desamma som för den ursprungliga servern. 

När återställningen är klar letar du upp den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggningsnamn och lösenord för serveradministratören som var giltigt för den befintliga servern när återställningen initierades. Lösenordet kan ändras från den nya serverns **översiktssida.**

Den nya servern som skapades under en återställning har inte de VNet-tjänstslutpunkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brandväggsregler från den ursprungliga servern återställs.

## <a name="geo-restore"></a>Geo-återställning

Om du har konfigurerat servern för geografiskt redundanta säkerhetskopieringar kan en ny server skapas från säkerhetskopian av den befintliga servern. Den här nya servern kan skapas i valfri region som Azure Database for MariaDB är tillgänglig.  

Om du vill skapa en server med hjälp av en geo-redundant säkerhetskopiering använder du Azure `az mariadb server georestore` CLI-kommandot .

> [!NOTE]
> När en server skapas första gången kanske den inte är omedelbart tillgänglig för geo-återställning. Det kan ta några timmar innan nödvändiga metadata fylls i.
>

Om du vill geo-återställa servern anger du följande kommando i Azure CLI-kommandotolken:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Det här kommandot skapar en ny server med *namnet mydemoserver-georestored* i USA, östra som tillhör *myresourcegroup.* Det är en Generell användning Gen 5-server med 8 virtuella kärnor. Servern skapas från den geo-redundanta säkerhetskopian av *mydemoserver*, som också finns i resursgruppen *myresourcegroup*

Om du vill skapa den nya servern i en annan resursgrupp än den befintliga servern kvalificerar du servernamnet i parametern som `--source-server` i följande exempel:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

Kommandot `az mariadb server georestore` kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
|resource-group| myresourcegroup | Namnet på resursgruppen som den nya servern ska tillhöra.|
|name | mydemoserver-georestored | Namnet på den nya servern. |
|source-server | mydemoserver | Namnet på den befintliga server vars geo-redundanta säkerhetskopieringar används. |
|location | USA, östra | Platsen för den nya servern. |
|sku-name| GP_Gen5_8 | Den här parametern anger prisnivå, beräkningsgenerering och antal virtuella kärnor för den nya servern. GP_Gen5_8 mappar till en Generell användning Gen 5-server med 8 virtuella kärnor.|

När du skapar en ny server med en geo-återställning ärver den samma lagringsstorlek och prisnivå som källservern. Dessa värden kan inte ändras när de skapas. När den nya servern har skapats kan lagringsstorleken skalas upp.

När återställningen är klar letar du upp den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggningsnamn och lösenord för serveradministratör som var giltigt för den befintliga servern när återställningen initierades. Lösenordet kan ändras från den nya serverns **översiktssida.**

Den nya servern som skapades under en återställning har inte de VNet-tjänstslutpunkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brandväggsregler från den ursprungliga servern återställs.

## <a name="next-steps"></a>Nästa steg

- Läs mer om tjänstens [säkerhetskopior](concepts-backup.md)
- Lär dig [mer om repliker](concepts-read-replicas.md)
- Läs mer om [alternativ för affärskontinuering](concepts-business-continuity.md)
