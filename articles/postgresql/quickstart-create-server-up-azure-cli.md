---
title: 'Snabbstart: Skapa server – az postgres up – Azure Database for PostgreSQL – enskild server'
description: Snabbstartsguide för att Azure Database for PostgreSQL – enskild server med hjälp av Azure CLI-kommandot (kommandoradsgränssnittet).
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f0f0dc56dcaad73ff945d319cf98dc30483ee07e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875097"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Snabbstart: Använd ett Azure CLI-kommando, az postgres up (förhandsversion) för att skapa en Azure Database for PostgreSQL – enskild server

> [!IMPORTANT]
> Azure [CLI-kommandot az postgres up](/cli/azure/postgres#az_postgres_up) är i förhandsversion.

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder [kommandot az postgres up](/cli/azure/postgres#az_postgres_up) för att skapa en Azure Database for PostgreSQL-server med hjälp av Azure CLI. Förutom att skapa servern skapar kommandot en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar standardbrandväggsregler för `az postgres up` klientdatorn. Dessa standardvärden hjälper till att påskynda utvecklingsprocessen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/authenticate-azure-cli) Observera **ID-egenskapen** från kommandoutdata för motsvarande prenumerationsnamn.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **az login-utdata** för din prenumeration i platshållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

Om du vill använda kommandona installerar du [tillägget db-up.](/cli/azure/) Om ett fel returneras ser du till att du har installerat den senaste versionen av Azure CLI. Se [Installera Azure CLI.](/cli/azure/install-azure-cli)

```azurecli
az extension add --name db-up
```

Skapa en Azure Database for PostgreSQL-server med följande kommando:

```azurecli
az postgres up
```

Servern skapas med följande standardvärden (om du inte åsidosätter dem manuellt):

**Inställning** | **Standardvärdet** | **Beskrivning**
---|---|---
server-name | Systemgenererat | Ett unikt namn som identifierar Azure Database för PostgreSQL-servern.
resource-group | Systemgenererat | En ny Azure-resursgrupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardvärdet är en Generell användning Gen5-server med 2 virtuella kärnor. Mer information [om nivåerna](https://azure.microsoft.com/pricing/details/postgresql/) finns på vår prissättningssida.
backup-retention | 7 | Hur länge en säkerhetskopia behålls. Enheten är dagar.
geo-redundant-backup | Inaktiverad | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | USA, västra 2 | Azure-platsen för servern.
ssl-enforcement | Inaktiverad | Om TLS/SSL ska vara aktiverat eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 10 | Huvudversion för PostgreSQL.
admin-user | Systemgenererat | Administratörens användarnamn.
admin-password | Systemgenererat | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om kommandot `az postgres up` och dess ytterligare parametrar finns i Azure [CLI-dokumentationen.](/cli/azure/postgres#az_postgres_up)

När servern har skapats levereras den med följande inställningar:

- En brandväggsregel med namnet "devbox" skapas. Azure CLI försöker identifiera IP-adressen för den dator som kommandot `az postgres up` körs från och tillåter den IP-adressen.
- "Tillåt åtkomst till Azure-tjänster" är inställt på PÅ. Den här inställningen konfigurerar serverns brandvägg så att den accepterar anslutningar från alla Azure-resurser, inklusive resurser som inte ingår i din prenumeration.
- En tom databas med namnet "sampledb" skapas
- En ny användare med namnet "root" med behörigheten "sampledb" skapas

> [!NOTE]
> Azure Database for PostgreSQL kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be IT-avdelningen öppna port 5432 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När kommandot `az postgres up` har slutförts returneras en lista över anslutningssträngar för populära programmeringsspråk till dig. Dessa anslutningssträngar är förkonfigurerade med de specifika attributen för den nya Azure Database for PostgreSQL servern.

Du kan använda kommandot [az postgres show-connection-string](/cli/azure/postgres#az_postgres_show_connection_string) för att lista dessa anslutningssträngar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i snabbstarten med hjälp av följande kommando. Det här kommandot tar Azure Database for PostgreSQL servern och resursgruppen.

```azurecli
az postgres down --delete-group
```

Om du bara vill ta bort den nyligen skapade servern kan du köra [kommandot az postgres](/cli/azure/postgres#az_postgres_down) down.

```azurecli
az postgres down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
