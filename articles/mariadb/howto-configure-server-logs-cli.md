---
title: Få åtkomst till långsamma frågeloggar – Azure CLI – Azure Database for MariaDB
description: Den här artikeln beskriver hur du kommer åt de långsamma loggarna i Azure Database for MariaDB med hjälp av kommandoradsverktyget Azure CLI.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 4/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42382076b6c14989eb153725e991c8ef047ad15b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774799"
---
# <a name="configure-and-access-azure-database-for-maria-db-slow-query-logs-by-using-azure-cli"></a>Konfigurera och få åtkomst till loggar för långsamma frågor i Azure Database for Maria DB med hjälp av Azure CLI

Du kan ladda ned Azure Database for MariaDB långsamma frågeloggar med hjälp av Azure CLI, kommandoradsverktyget i Azure.

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här i guiden behöver du:
- [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md)
- [Azure CLI eller](/cli/azure/install-azure-cli) Azure Cloud Shell i webbläsaren

## <a name="configure-logging"></a>Konfigurera loggning
Du kan konfigurera servern så att den får åtkomst till den långsamma MySQL-frågeloggen genom att göra följande:
1. Aktivera långsam frågeloggning genom att ange **\_ loggparametern för \_ långsamma frågor** till PÅ.
2. Välj var loggarna ska matas ut med hjälp av **\_ loggutdata**. Om du vill skicka loggar till både lokal lagring och Azure Monitor diagnostikloggar väljer du **Arkiv**. Om du bara vill skicka loggar Azure Monitor loggar väljer du **Ingen**
3. Justera andra parametrar, till exempel **lång \_ frågetid \_ och** **logga långsamma \_ \_ \_ administratörsutdrag**.

Information om hur du anger värdet för dessa parametrar via Azure CLI finns i [Så här konfigurerar du serverparametrar.](howto-configure-server-parameters-cli.md)

Följande CLI-kommando aktiverar till exempel den långsamma frågeloggen, anger den långa frågetiden till 10 sekunder och inaktiverar sedan loggning av den långsamma admin-instruktionen. Slutligen visas konfigurationsalternativen för granskningen.
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name log_output --resource-group myresourcegroup --server mydemoserver --value FILE
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Lista loggar för Azure Database for MariaDB server
Om **log_output** har konfigurerats till "Fil" kan du komma åt loggar direkt från serverns lokala lagring. Om du vill visa en lista över tillgängliga långsamma frågeloggfiler för servern kör du kommandot [az mariadb server-logs list.](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_list)

Du kan lista loggfilerna för servern **mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup**. Dirigera sedan listan över loggfiler till en textfil med namnet **log \_ files \_list.txt**.
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>Ladda ned loggar från servern
Om **log_output** har konfigurerats till "File" kan du ladda ned enskilda loggfiler från servern med kommandot [az mariadb server-logs download.](/cli/azure/mariadb/server-logs#az_mariadb_server_logs_download)

Använd följande exempel för att ladda ned den specifika loggfilen **för servern mydemoserver.mariadb.database.azure.com** under resursgruppen **myresourcegroup** till din lokala miljö.
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- Lär dig mer [om långsamma frågeloggar i Azure Database for MariaDB](concepts-server-logs.md).
