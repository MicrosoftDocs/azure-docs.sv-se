---
title: Konfigurera Server parametrar – Azure CLI – Azure Database for MySQL flexibel Server
description: Den här artikeln beskriver hur du konfigurerar tjänst parametrarna i Azure Database for MySQL flexibel server med hjälp av kommando rads verktyget för Azure CLI.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 11/10/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58e7c024d6494aee745884997e42b527c51ab237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489547"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Konfigurera Server parametrar i Azure Database for MySQL flexibel server med Azure CLI

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Du kan visa, Visa och uppdatera parametrar för en Azure Database for MySQL flexibel server med hjälp av Azure CLI, kommando rads verktyget för Azure. Server parametrarna konfigureras med standard och Rekommenderat värde när du skapar servern.  

Den här artikeln beskriver hur du visar, visar och uppdaterar Server parametrar med hjälp av Azure CLI.

>[!Note]
> Server parametrar kan uppdateras globalt på server nivå, använda [Azure CLI](./how-to-configure-server-parameters-cli.md) eller [Azure Portal](./how-to-configure-server-parameters-portal.md)

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- [En Azure Database for MySQL flexibel Server](quickstart-create-server-cli.md)
- Kommando rads verktyget för [Azure CLI](/cli/azure/install-azure-cli) eller Använd Azure Cloud Shell i webbläsaren.

## <a name="list-server-parameters-for-azure-database-for-mysql-flexible-server"></a>Visa en lista med Server parametrar för Azure Database for MySQL flexibel Server
Om du vill visa alla parametrar på en server och deras värden kör du kommandot [AZ MySQL Flexible-Server parameter List](/cli/azure/mysql/flexible-server/parameter) .

Du kan visa Server parametrarna för Server- **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup**.
```azurecli-interactive
az mysql flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```
För definitionen av var och en av de angivna parametrarna, se referens avsnittet MySQL på [Server system-variabler](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html).

## <a name="show-server-parameter-details"></a>Visa information om Server parameter
Om du vill visa information om en viss parameter för en server kör du kommandot [AZ MySQL Flexible-Server parameter show](/cli/azure/mysql/flexible-server/parameter) .

I det här exemplet visas information om den **långsamma \_ fråge \_ logg** Server parametern för Server- **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter show --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
## <a name="modify-a-server-parameter-value"></a>Ändra ett Server parameter värde
Du kan också ändra värdet för en viss server parameter, som uppdaterar det underliggande konfiguration svärdet för MySQL Server-motorn. Om du vill uppdatera Server parametern använder du kommandot [AZ MySQL böjlig-Server parameter uppsättning](/cli/azure/mysql/flexible-server/parameter) . 

Så här uppdaterar du den **långsamma \_ fråge \_ logg** server parametern för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver --value ON
```
Om du vill återställa värdet för en parameter utelämnar du den valfria `--value` parametern, och tjänsten använder standardvärdet. I exemplet ovan skulle det se ut så här:
```azurecli-interactive
az mysql flexible-server parameter set --name slow_query_log --resource-group myresourcegroup --server-name mydemoserver
```
Den här koden återställer den **långsamma \_ fråge \_ loggen** till standardvärdet **.** 

## <a name="setting-non-modifiable-server-parameters"></a>Ange server parametrar som inte går att ändra

Om den server parameter som du vill uppdatera inte kan ändras kan du ange parametern på anslutnings nivån med `init_connect` . Detta anger Server parametrarna för varje klient som ansluter till servern. 

Uppdatera parametern **init \_ Connect** server för Server **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup** för att ange värden som teckenuppsättning.
```azurecli-interactive
az mysql flexible-server parameter set --name init_connect --resource-group myresourcegroup --server-name mydemoserver --value "SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;"
```
>[!Note]
> `init_connect` kan användas för att ändra parametrar som inte kräver superprivilegier på sessionsnivå. Kontrollera om du kan ange parametern med `init_connect` genom att köra kommandot `set session parameter_name=YOUR_DESIRED_VALUE;`. Om kommandot returnerar ett fel av typen **Åtkomst nekad; du behöver superprivilegier**, betyder det att du inte kan ange parametern med ”init_connect”.

## <a name="working-with-the-time-zone-parameter"></a>Arbeta med tids zons parametern

### <a name="populating-the-time-zone-tables"></a>Fylla i tids zons tabellerna

Du kan fylla i tids zons tabellerna på servern genom att anropa den `mysql.az_load_timezone` lagrade proceduren från ett verktyg som mysql-kommandoraden eller MySQL Workbench.

> [!NOTE]
> Om du kör `mysql.az_load_timezone` kommandot från MySQL Workbench kan du behöva inaktivera läget för säker uppdatering först med `SET SQL_SAFE_UPDATES=0;` .

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> Du bör starta om servern för att se till att tids zons tabellerna är korrekt ifyllda.<!-- fIX me To restart the server, use the [Azure portal](howto-restart-server-portal.md) or [CLI](howto-restart-server-cli.md). -->

Kör följande kommando för att visa tillgängliga tids zons värden:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>Ange tidszon på global nivå

Du kan ange tids zonen för global nivå med kommandot [AZ MySQL Flexible-Server parameter uppsättning](/cli/azure/mysql/flexible-server/parameter) .

Följande kommando uppdaterar **tids \_ zons** Server parametern för Server- **mydemoserver.mysql.Database.Azure.com** under resurs grupp **myresourcegroup** till **US/Pacific**.

```azurecli-interactive
az mysql flexible-server parameter set --name time_zone --resource-group myresourcegroup --server-name mydemoserver --value "US/Pacific"
```

### <a name="setting-the-session-level-time-zone"></a>Ange tids zonen för sessionen

Tids zonen för tids zonen kan ställas in genom att köra `SET time_zone` kommandot från ett verktyg som mysql-kommandoraden eller MySQL Workbench. I exemplet nedan ställs tids zonen till i **USA/Stilla havs** området.  

```sql
SET time_zone = 'US/Pacific';
```

I MySQL-dokumentationen hittar du [datum-och tids funktioner](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz).


## <a name="next-steps"></a>Nästa steg

- Konfigurera [Server parametrar i Azure Portal](./how-to-configure-server-parameters-portal.md)
