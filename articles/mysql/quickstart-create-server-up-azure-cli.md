---
title: 'Snabb start: Skapa Azure Database for MySQL med AZ MySQL up'
description: Snabb starts guide för att skapa Azure Database for MySQL server med Azure CLI-kommandot (kommando rads gränssnitt).
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542175"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Snabb start: skapa en Azure Database for MySQL med ett enkelt Azure CLI-kommando – AZ MySQL up (för hands version)

> [!IMPORTANT]
> [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI-kommandot är i för hands version.

Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommando raden eller i skript. Den här snabb starten visar hur du använder kommandot [AZ MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) för att skapa en Azure Database for MySQL-server med hjälp av Azure CLI. Förutom att skapa-servern `az mysql up` skapar kommandot en exempel databas, en rot användare i databasen, öppnar brand väggen för Azure-tjänster och skapar standard brand Väggs regler för klient datorn. Detta hjälper till att påskynda utvecklings processen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2,0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/authenticate-azure-cli) . Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **AZ-inloggnings** resultatet för din prenumeration till plats hållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server

Om du vill använda kommandona installerar du [db-up-](/cli/azure/ext/db-up) tillägget. Om ett fel returneras kontrollerar du att du har installerat den senaste versionen av Azure CLI. Se [Installera Azure CLI](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Skapa en Azure Database for MySQL server med följande kommando:

```azurecli
az mysql up
```

Servern skapas med följande standardvärden (om du inte åsidosätter dem manuellt):

**Inställning** | **Standardvärde** | **Beskrivning**
---|---|---
server-name | Genereras av systemet | Ett unikt namn för Azure Database för MySQL-server.
resource-group | Genereras av systemet | En ny Azure-resurs grupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardvärdet är en Generell användning Gen5-server med 2 virtuella kärnor. På vår [prissättnings sida](https://azure.microsoft.com/pricing/details/mysql/) finns mer information om nivåerna.
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar.
geo-redundant-backup | Inaktiverad | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | USA, västra 2 | Azure-platsen för servern.
ssl-enforcement | Enabled | Om SSL ska vara aktiverat eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 5.7 | Huvudversion för MySQL.
admin-user | Genereras av systemet | Användarnamnet för administratörsinloggning.
admin-password | Genereras av systemet | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om `az mysql up` kommandot och dess ytterligare parametrar finns i [Azure CLI-dokumentationen](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

När servern har skapats levereras den med följande inställningar:

- En brand Väggs regel med namnet "devbox" skapas. Azure CLI försöker identifiera datorns IP-adress som `az mysql up` kommandot körs från och tillåter den IP-adressen.
- "Tillåt åtkomst till Azure-tjänster" är inställt på på. Den här inställningen konfigurerar serverns brand vägg så att den accepterar anslutningar från alla Azure-resurser, inklusive resurser som inte finns i din prenumeration.
- `wait_timeout`Parametern har angetts till 8 timmar
- En tom databas med namnet "sampledb" skapas
- En ny användare med namnet "rot" med behörighet till "sampledb" skapas

> [!NOTE]
> Azure Database for MySQL kommunicerar via port 3306. När du ansluter inifrån ett företags nätverk kanske utgående trafik via port 3306 inte tillåts av nätverkets brand vägg. Be din IT-avdelning öppna port 3306 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När `az mysql up` kommandot har slutförts returneras en lista över anslutnings strängar för populära programmeringsspråk. Dessa anslutnings strängar är förkonfigurerade med de angivna attributen för den nyskapade Azure Database for MySQL-servern.

Du kan använda kommandot [AZ MySQL show-Connection-String](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) för att lista dessa anslutnings strängar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i snabb starten med hjälp av följande kommando. Det här kommandot tar bort Azure Database for MySQL-servern och resurs gruppen.

```azurecli
az mysql down --delete-group
```

Om du bara vill ta bort den nyligen skapade servern kan du köra [AZ MySQL](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) -kommandot.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md)
