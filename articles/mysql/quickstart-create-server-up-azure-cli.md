---
title: 'Snabbstart: Skapa Azure Database for MySQL med az mysql up'
description: Snabbstartsguide för att Azure Database for MySQL server med hjälp av Azure CLI-kommandot (kommandoradsgränssnittet).
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d753061c6141dd0ca75415cab5502e7fa350cd90
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873549"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Snabbstart: Skapa en Azure Database for MySQL med ett enkelt Azure CLI-kommando – az mysql up (förhandsversion)

> [!IMPORTANT]
> Azure [CLI-kommandot az mysql up](/cli/azure/mysql#az_mysql_up) är i förhandsversion.

Azure Database för MySQL är en hanterad tjänst som låter dig köra, hantera och skala högtillgängliga MySQL-databaser i molnet. Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten visar hur du använder [kommandot az mysql up](/cli/azure/mysql#az_mysql_up) för att skapa en Azure Database for MySQL-server med hjälp av Azure CLI. Förutom att skapa servern skapar kommandot en exempeldatabas, en rotanvändare i databasen, öppnar brandväggen för Azure-tjänster och skapar standardbrandväggsregler för `az mysql up` klientdatorn. Detta hjälper till att påskynda utvecklingsprocessen.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/authenticate-azure-cli) Observera egenskapen **ID** från kommandoutdata för det motsvarande prenumerationsnamnet.

```azurecli
az login
```

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account). Ersätt egenskapen **prenumerations-ID** från **az login-utdata** för din prenumeration i platshållaren för prenumerations-ID.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server

Om du vill använda kommandona installerar du [tillägget db-up.](/cli/azure/) Om ett fel returneras ser du till att du har installerat den senaste versionen av Azure CLI. Se [Installera Azure CLI.](/cli/azure/install-azure-cli)

```azurecli
az extension add --name db-up
```

Skapa en Azure Database for MySQL-server med följande kommando:

```azurecli
az mysql up
```

Servern skapas med följande standardvärden (om du inte åsidosätter dem manuellt):

**Inställning** | **Standardvärdet** | **Beskrivning**
---|---|---
server-name | Systemgenererat | Ett unikt namn för Azure Database för MySQL-server.
resource-group | Systemgenererat | En ny Azure-resursgrupp.
sku-name | GP_Gen5_2 | Namnet på SKU:n. Följer konventionen {prisnivå}\_{beräkningsgenerering}\_{vCores} i snabbformat. Standardvärdet är en Generell användning Gen5-server med 2 virtuella kärnor. Mer information [om nivåerna](https://azure.microsoft.com/pricing/details/mysql/) finns på vår prissättningssida.
backup-retention | 7 | Hur länge en säkerhetskopia ska behållas. Enheten är dagar.
geo-redundant-backup | Inaktiverad | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern eller inte.
location | USA, västra 2 | Azure-platsen för servern.
ssl-enforcement | Enabled | Om SSL ska vara aktiverat eller inte för den här servern.
storage-size | 5120 | Serverns lagringskapacitet (enheten är megabyte).
version | 5.7 | Huvudversion för MySQL.
admin-user | Systemgenererat | Användarnamnet för administratörsinloggning.
admin-password | Systemgenererat | Lösenordet för administratörsanvändaren.

> [!NOTE]
> Mer information om kommandot `az mysql up` och dess ytterligare parametrar finns i Azure [CLI-dokumentationen.](/cli/azure/mysql#az_mysql_up)

När servern har skapats levereras den med följande inställningar:

- En brandväggsregel med namnet "devbox" skapas. Azure CLI försöker identifiera IP-adressen för den dator som kommandot `az mysql up` körs från och tillåter den IP-adressen.
- "Tillåt åtkomst till Azure-tjänster" är inställt på PÅ. Den här inställningen konfigurerar serverns brandvägg så att den accepterar anslutningar från alla Azure-resurser, inklusive resurser som inte ingår i din prenumeration.
- Parametern `wait_timeout` är inställd på 8 timmar
- En tom databas med namnet "sampledb" skapas
- En ny användare med namnet "root" med behörigheten "sampledb" skapas

> [!NOTE]
> Azure Database for MySQL kommunicerar via port 3306. Vid anslutning inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts av nätverkets brandvägg. Be IT-avdelningen öppna port 3306 för att ansluta till servern.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

När kommandot `az mysql up` har slutförts returneras en lista över anslutningssträngar för populära programmeringsspråk till dig. Dessa anslutningssträngar är förkonfigurerade med de specifika attributen för den nya Azure Database for MySQL servern.

Du kan använda kommandot [az mysql show-connection-string](/cli/azure/mysql#az_mysql_show_connection_string) för att lista dessa anslutningssträngar igen.

## <a name="clean-up-resources"></a>Rensa resurser

Rensa alla resurser som du skapade i snabbstarten med hjälp av följande kommando. Det här kommandot tar Azure Database for MySQL servern och resursgruppen.

```azurecli
az mysql down --delete-group
```

Om du bara vill ta bort den nyligen skapade servern kan du köra [kommandot az mysql](/cli/azure/mysql#az_mysql_down) down.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MySQL-databas med Azure CLI](./tutorial-design-database-using-cli.md)
