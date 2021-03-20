---
title: Åtkomst till långsamma Query-loggar – Azure CLI – Azure Database for MariaDB
description: Den här artikeln beskriver hur du kommer åt långsamma loggar i Azure Database for MariaDB med hjälp av kommando rads verktyget för Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cea1571a48afe00350dde247c3c10e222a446247
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662397"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Konfigurera och få åtkomst till Azure Database för Maria DB långsam fråga med Azure CLI

Du kan hämta Azure Database for MariaDB långsamma frågemeddelanden med hjälp av Azure CLI, kommando rads verktyget för Azure.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- [Azure Database for MariaDB Server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI](/cli/azure/install-azure-cli) eller Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern för att få åtkomst till MySQL-långsamma Query-loggen genom att utföra följande steg:
1. Aktivera långsam loggning av frågor genom att ställa in den **långsamma \_ fråge \_ logg** parametern på på.
2. Välj var loggarna ska matas till med **loggen \_ utdata**. Om du vill skicka loggar till både lokal lagring och Azure Monitor diagnostikloggar väljer du **fil**. Om du bara vill skicka loggar till Azure Monitor loggar väljer du **ingen**
3. Justera andra parametrar, t. ex. **lång \_ fråge \_ stund** och **loggs \_ långsamma \_ Administratörs \_ instruktioner**.

Information om hur du ställer in värdet för dessa parametrar via Azure CLI finns i [så här konfigurerar du Server parametrar](howto-configure-server-parameters-cli.md).

Följande CLI-kommando aktiverar till exempel loggen långsam fråga, ställer in tiden för lång tid till 10 sekunder och stänger sedan av loggningen av den långsamma administratörs instruktionen. Slutligen visas konfigurations alternativen för din granskning.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista loggar för Azure Database for MariaDB Server
Om **log_output** har kon figurer ATS till "File", kan du komma åt loggarna direkt från serverns lokala lagring. Om du vill visa en lista över tillgängliga långsamma loggfiler för servern kör du kommandot [AZ MariaDB Server-logs List](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) .

Du kan visa en lista över loggfilerna för Server **mydemoserver.MariaDB.Database.Azure.com** under resurs gruppen **myresourcegroup**. Dirigera sedan listan över loggfiler till en textfil som kallas **loggfiler \_ \_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Hämta loggar från servern
Om **log_output** har kon figurer ATS till "File" kan du hämta enskilda loggfiler från servern med kommandot [AZ MariaDB Server-logs Download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) .

Använd följande exempel för att hämta den speciella logg filen för servern **mydemoserver.MariaDB.Database.Azure.com** under resurs gruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [långsamma frågemeddelanden i Azure Database for MariaDB](concepts-server-logs.md).
