---
title: Säkerhets kopiering och återställning – Azure CLI – Azure Database for MariaDB
description: Lär dig hur du säkerhetskopierar och återställer en server i Azure Database for MariaDB med hjälp av Azure CLI.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: ceda6c99493818042aa281ab545465e91493a80e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664842"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mariadb-using-the-azure-cli"></a>Säkerhetskopiera och återställa en server i Azure Database for MariaDB med Azure CLI

Azure Database for MariaDB servrar säkerhets kopie ras regelbundet för att aktivera återställnings funktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt på en ny server.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en [Azure Database for MariaDB-Server och-databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här instruktions guiden kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="set-backup-configuration"></a>Ange konfiguration för säkerhets kopiering

Du väljer mellan att konfigurera servern för antingen lokalt redundanta säkerhets kopieringar eller geografiskt redundanta säkerhets kopieringar när servern skapas.

> [!NOTE]
> När en server har skapats har den typ av redundans som den har, geografiskt redundant vs lokalt redundant, inte växlats.
>

När du skapar en server via `az mariadb server create` kommandot, `--geo-redundant-backup` bestämmer parametern säkerhets kopians redundans alternativ. Om `Enabled` så tas geo-redundanta säkerhets kopieringar. Eller om `Disabled` lokalt redundanta säkerhets kopieringar görs.

Kvarhållningsperioden för säkerhets kopior anges av parametern `--backup-retention` .

Mer information om hur du anger dessa värden under skapa finns i [snabb starten för Azure Database for MariaDB Server CLI](quickstart-create-mariadb-server-database-using-azure-cli.md).

Du kan ändra kvarhållningsperioden för säkerhets kopior för en server på följande sätt:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

I föregående exempel ändras kvarhållningsperioden för säkerhets kopior på mydemoserver till 10 dagar.

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka i tiden en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Återställning av tidpunkt för tidpunkt beskrivs ytterligare i nästa avsnitt.

## <a name="server-point-in-time-restore"></a>Återställning av Server-in-Time-återställning

Du kan återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern är kvar som den är. Om en tabell till exempel råkat släppas efter middag idag, kan du återställa till tiden strax före 12.00. Sedan kan du hämta den saknade tabellen och data från den återställda kopian av servern.

Om du vill återställa servern använder du kommandot Azure CLI [AZ MariaDB Server Restore](/cli/azure/mariadb/server#az-mariadb-server-restore) .

### <a name="run-the-restore-command"></a>Kör kommandot Restore

Återställ servern genom att ange följande kommando i kommando tolken för Azure CLI:

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mariadb server restore`Kommandot kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Resurs gruppen där käll servern finns.  |
| name | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Välj en tidpunkt då du vill återställa till. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd ISO8601 datum-och tids format. Du kan till exempel använda din egen lokala tidszon, till exempel `2018-03-13T05:59:00-08:00` . Du kan också använda formatet UTC-Zulu, till exempel `2018-03-13T13:59:00Z` . |
| source-server | mydemoserver | Namn eller ID på källservern som återställningen görs från. |

WWhen du återställer en server till en tidigare tidpunkt skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats-och pris nivå värden för den återställda servern förblir samma som den ursprungliga servern. 

När återställnings processen har slutförts letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen initierades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="geo-restore"></a>Geo-återställning

Om du har konfigurerat servern för geografiskt redundanta säkerhets kopieringar kan en ny server skapas från säkerhets kopian av den befintliga servern. Den nya servern kan skapas i vilken region som helst som Azure Database for MariaDB tillgänglig.  

Använd Azure CLI-kommandot om du vill skapa en server med hjälp av en Geo-redundant säkerhets kopia `az mariadb server georestore` .

> [!NOTE]
> När en server först skapas kanske den inte är omedelbart tillgänglig för geo Restore. Det kan ta några timmar för nödvändiga metadata att fyllas i.
>

För geo-återställning av servern, i kommando tolken för Azure CLI, anger du följande kommando:

```azurecli-interactive
az mariadb server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen5_8
```

Det här kommandot skapar en ny server med namnet *mydemoserver-långsiktig återställning* i östra USA som ska tillhöra *myresourcegroup*. Det är en Generell användning, gen 5-Server med 8 virtuella kärnor. Servern skapas från den geo-redundanta säkerhets kopieringen av *mydemoserver*, som också finns i resurs gruppen *myresourcegroup*

Om du vill skapa den nya servern i en annan resurs grupp än den befintliga servern, kan du i- `--source-server` parametern kvalificera Server namnet som i följande exempel:

```azurecli-interactive
az mariadb server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMariaDB/servers/mydemoserver" --location eastus --sku-name GP_Gen5_8

```

`az mariadb server georestore`Kommandot kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
|resource-group| myresourcegroup | Namnet på den resurs grupp som den nya servern ska tillhöra.|
|name | mydemoserver – omåterställd | Namnet på den nya servern. |
|source-server | mydemoserver | Namnet på den befintliga server vars geo-redundanta säkerhets kopieringar används. |
|location | USA, östra | Platsen för den nya servern. |
|sku-name| GP_Gen5_8 | Den här parametern anger pris nivån, beräknings genereringen och antalet virtuella kärnor för den nya servern. GP_Gen5_8 mappar till en Generell användning, gen 5-Server med 8 virtuella kärnor.|

När du skapar en ny server med en geo-återställning ärver den samma lagrings storlek och pris nivå som käll servern. Dessa värden kan inte ändras när de skapas. När den nya servern har skapats kan dess lagrings storlek skalas upp.

När återställnings processen har slutförts letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen initierades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="next-steps"></a>Nästa steg

- Läs mer om tjänstens [säkerhets kopior](concepts-backup.md)
- Lär dig mer om [repliker](concepts-read-replicas.md)
- Läs mer om alternativ för [affärs kontinuitet](concepts-business-continuity.md)
