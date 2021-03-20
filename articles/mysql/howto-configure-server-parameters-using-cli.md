---
title: Konfigurera Server parametrar – Azure CLI – Azure Database for MySQL
description: I den här artikeln beskrivs hur du konfigurerar tjänst parametrarna i Azure Database for MySQL med hjälp av kommando rads verktyget för Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5a84d93400e713f66545387fd146148ee735c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541546"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-using-the-azure-cli"></a>Konfigurera Server parametrar i Azure Database for MySQL med Azure CLI
Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure Database for MySQL-server med hjälp av Azure CLI, kommando rads verktyget för Azure. En del av motor konfigurationerna exponeras på server nivå och kan ändras. 

>[!Note]
> Server parametrar kan uppdateras globalt på server nivå, använda [Azure CLI](./howto-configure-server-parameters-using-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md)eller [Azure Portal](./howto-server-parameters.md)

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- [En Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-cli.md)
- Kommando rads verktyget för [Azure CLI](/cli/azure/install-azure-cli) eller Använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-mysql-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for MySQL Server
Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du kommandot [AZ MySQL Server Configuration List](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-list) .

Du kan ange serverns konfigurations parametrar för servern **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup**.
```azurecli-interactive
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```
För definitionen av var och en av de angivna parametrarna, se referens avsnittet MySQL på [Server system-variabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter
Om du vill visa information om en viss konfigurations parameter för en server kör du kommandot [AZ MySQL Server Configuration show](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-show) .

Det här exemplet visar information om konfigurations parametern för **långsam \_ fråge \_ logg** server för Server- **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mysql server configuration show --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-a-server-configuration-parameter-value"></a>Ändra ett parameter värde för Server konfiguration
Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för MySQL Server-motorn. Om du vill uppdatera konfigurationen använder du kommandot [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) . 

Så här uppdaterar du den **långsamma \_ fråge \_ loggs** serverns konfigurations parameter för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
```
Om du vill återställa värdet för en konfigurations parameter utelämnar du den valfria `--value` parametern, och tjänsten använder standardvärdet. I exemplet ovan skulle det se ut så här:
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver
```
Den här koden återställer logg konfigurationen för **långsam \_ \_ fråga** till standardvärdet **.** 

## <a name="setting-parameters-not-listed"></a>Ange parametrar som inte listas
Om den server parameter som du vill uppdatera inte visas i Azure Portal, kan du välja att ange parametern på anslutnings nivå med `init_connect` . Detta anger Server parametrarna för varje klient som ansluter till servern. 

Uppdatera konfigurations parametern **init \_ Connect** server för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup** för att ange värden som teckenuppsättning.
```azurecli-interactive
az mysql server configuration set --name init_connect --resource-group myresourcegroup --server mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Du kan fylla i tids zons tabellerna på servern genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som mysql-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva inaktivera läget för säker uppdatering först med `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda. Om du vill starta om servern använder du [Azure Portal](howto-restart-server-portal.md) eller [CLI](howto-restart-server-cli.md).

Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Du kan ange tids zonen för global nivå med hjälp av kommandot [AZ MySQL Server Configuration set](/cli/azure/mysql/server/configuration#az-mysql-server-configuration-set) .

Följande kommando uppdaterar konfigurations parametern för **tids \_ zons** servern för Server- **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup** till **US/Pacific**.

```azurecli-interactive
az mysql server configuration set --name time_zone --resource-group myresourcegroup --server mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att köra `SET time_zone` kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.  

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Nästa steg

- Konfigurera [Server parametrar i Azure Portal](howto-server-parameters.md)
