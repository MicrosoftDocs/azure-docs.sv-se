---
title: 'Snabb start: skapa server-AZ postgres up-Azure Database for PostgreSQL-Single-Server'
description: Snabb starts guide för att skapa Azure Database for PostgreSQL-enskild server med Azure CLI-kommandot (kommando rads gränssnitt).
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 28007b4e17bd698e4aaa2bddbf0c8f9ffaaa8351
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606461"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Snabb start: Använd ett Azure CLI-kommando, AZ postgres (för hands version), för att skapa en Azure Database for PostgreSQL-enskild server

> [!IMPORTANT]
> [AZ postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-kommandot är i för hands version.

Azure Database för PostgreSQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga PostgreSQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabb starten visar hur du använder kommandot [AZ postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) för att skapa en Azure Database for postgresql-server med hjälp av Azure CLI. Förutom att skapa-servern `az postgres up` skapar kommandot en exempel databas, en rot användare i databasen, öppnar brand väggen för Azure-tjänster och skapar standard brand Väggs regler för klient datorn. Dessa standardvärden hjälper till att påskynda utvecklings processen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2,0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/authenticate-azure-cli) . Observera egenskapen **ID** från kommandots utdata för motsvarande prenumerations namn.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **AZ-inloggnings** resultatet för din prenumeration till plats hållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

Om du vill använda kommandona installerar du [db-up-](/cli/azure/ext/db-up) tillägget. Om ett fel returneras kontrollerar du att du har installerat den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Skapa en Azure Database for PostgreSQL server med följande kommando:

```azurecli
az postgres up
```

Servern skapas med följande standardvärden (om du inte åsidosätter dem manuellt):

**Inställning** | **Standardvärde** | **Beskrivning**
---|---|---
server-name | Genereras av systemet | Ett unikt namn som identifierar Azure Database för PostgreSQL-servern.
resource-group | Genereras av systemet | En ny Azure-resurs grupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardvärdet är en Generell användning Gen5-server med 2 virtuella kärnor. På vår [prissättnings sida](https://azure.microsoft.com/pricing/details/postgresql/) finns mer information om nivåerna.
backup-retention | 7 | Hur länge en säkerhets kopia behålls. Enheten är dagar.
geo-redundant-backup | Inaktiverad | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | USA, västra 2 | Azure-platsen för servern.
ssl-enforcement | Inaktiverad | Om TLS/SSL ska vara aktiverat eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 10 | Huvudversion för PostgreSQL.
admin-user | Genereras av systemet | Administratörens användar namn.
admin-password | Genereras av systemet | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om `az postgres up` kommandot och dess ytterligare parametrar finns i [Azure CLI-dokumentationen](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

När servern har skapats levereras den med följande inställningar:

- En brand Väggs regel med namnet "devbox" skapas. Azure CLI försöker identifiera datorns IP-adress som `az postgres up` kommandot körs från och tillåter den IP-adressen.
- "Tillåt åtkomst till Azure-tjänster" är inställt på på. Den här inställningen konfigurerar serverns brand vägg så att den accepterar anslutningar från alla Azure-resurser, inklusive resurser som inte finns i din prenumeration.
- En tom databas med namnet "sampledb" skapas
- En ny användare med namnet "rot" med behörighet till "sampledb" skapas

> [!NOTE]
> Azure Database for PostgreSQL kommunicerar via port 5432. När du ansluter innifrån ett företagsnätverk är det möjligt att utgående trafik via port 5432 inte tillåts av nätverkets brandvägg. Be din IT-avdelning öppna port 5432 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När `az postgres up` kommandot har slutförts returneras en lista över anslutnings strängar för populära programmeringsspråk. Dessa anslutnings strängar är förkonfigurerade med de angivna attributen för den nyskapade Azure Database for PostgreSQL-servern.

Du kan använda kommandot [AZ postgres show-Connection-String](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) för att lista dessa anslutnings strängar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i snabb starten med hjälp av följande kommando. Det här kommandot tar bort Azure Database for PostgreSQL-servern och resurs gruppen.

```azurecli
az postgres down --delete-group
```

Om du bara vill ta bort den nyligen skapade servern kan du köra kommandot [AZ postgres Down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) .

```azurecli
az postgres down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera din databas med Exportera och importera](./howto-migrate-using-export-and-import.md)
