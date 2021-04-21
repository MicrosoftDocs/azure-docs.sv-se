---
title: Konfigurera serverparametrar – Azure CLI – Azure Database for MySQL
description: Den här artikeln beskriver hur du konfigurerar tjänstparametrarna i Azure Database for MySQL med hjälp av kommandoradsverktyget Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 94dd7f72f6411934587c76850a05d6885e9f6862
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763225"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Konfigurera serverparametrar i Azure Database for MySQL med hjälp av Azure CLI
Du kan visa, visa och uppdatera konfigurationsparametrar för en Azure Database for MySQL-server med hjälp av Azure CLI, Kommandoradsverktyg för Azure. En delmängd av motorkonfigurationerna exponeras på servernivå och kan ändras. 

>[!Note]
> Serverparametrar kan uppdateras globalt på servernivå, använda [Azure CLI,](./howto-configure-server-parameters-using-cli.md) [PowerShell](./howto-configure-server-parameters-using-powershell.md)eller [Azure Portal](./howto-server-parameters.md)

## <a name="prerequisites"></a>Förutsättningar
Om du vill gå igenom den här guiden behöver du:
- [En Azure Database for MySQL server](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure](/cli/azure/install-azure-cli) CLI-kommandoradsverktyget eller använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Lista serverkonfigurationsparametrar för Azure Database for MySQL server
Om du vill lista alla ändringsbara parametrar i en server och deras värden kör du [kommandot az mysql server configuration list.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list)

Du kan lista serverkonfigurationsparametrarna för servern **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
Definitionen av var och en av de angivna parametrarna finns i MySQL-referensavsnittet [på Server System Variables](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om serverkonfigurationsparametern
Om du vill visa information om en viss konfigurationsparameter för en server kör du [kommandot az mysql server configuration show.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_show)

Det här exemplet visar information om den **långsamma \_ \_ frågeloggserverns** konfigurationsparameter **för** mydemoserver.mysql.database.azure.com under resursgruppen **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Ändra parametervärdet för serverkonfigurationen
Du kan också ändra värdet för en viss parameter för serverkonfiguration, vilket uppdaterar det underliggande konfigurationsvärdet för MySQL-servermotorn. Om du vill uppdatera konfigurationen använder du [kommandot az mysql server configuration set.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) 

Uppdatera den långsamma **\_ \_ frågeloggserverns** konfigurationsparameter för servern **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Om du vill återställa värdet för en konfigurationsparameter utelämnar du den valfria `--value` parametern så tillämpar tjänsten standardvärdet. I exemplet ovan skulle det se ut så här:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Den här koden återställer den **långsamma \_ \_ frågeloggkonfigurationen** till standardvärdet **AV**. 

## <a name="setting-parameters-not-listed"></a>Inställningsparametrar visas inte
Om serverparametern som du vill uppdatera inte finns med i listan Azure Portal kan du välja att ange parametern på anslutningsnivå med `init_connect` . Detta anger serverparametrarna för varje klient som ansluter till servern. 

Uppdatera **serverkonfigurationsparametern init \_ connect** **för mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup** för att ange värden, till exempel teckenuppsättning.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tidszonsparametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tidszonstabellerna

Du kan fylla tidszonstabellerna på servern genom att anropa den lagrade proceduren från ett verktyg som `mysql.az_load_timezone` MySQL-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör kommandot `mysql.az_load_timezone` från MySQL Workbench kan du behöva inaktivera felsäkert uppdateringsläge först med `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att säkerställa att tidszonstabellerna är korrekt ifyllda. Om du vill starta om servern använder [du Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).

Om du vill visa tillgängliga tidszonsvärden kör du följande kommando:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Du kan ange tidszonen på global nivå med kommandot [az mysql server configuration set.](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set)

Följande kommando uppdaterar tidszonsserverkonfigurationsparametern för servern **mydemoserver.mysql.database.azure.com** under resursgruppen **myresourcegroup** till **\_** **USA/Stillahavsområdet.**

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ange tidszon för sessionsnivå

Tidszonen på sessionsnivå kan anges genom att köra kommandot från ett verktyg som `SET time_zone` MySQL-kommandoraden eller MySQL Workbench. I exemplet nedan anges tidszonen till **tidszonen USA/Stillahavsområdet.**  

```sql
SET time_zone = 'US/Pacific';
```

Se MySQL-dokumentationen för [Datum- och tidsfunktioner.](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)


## <a name="next-steps"></a>Nästa steg

- Så här [konfigurerar du serverparametrar i Azure Portal](howto-server-parameters.md)
