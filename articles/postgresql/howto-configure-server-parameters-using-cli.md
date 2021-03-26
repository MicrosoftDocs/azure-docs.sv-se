---
title: Konfigurera parametrar – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du konfigurerar postgres-parametrar i Azure Database for PostgreSQL-enskild server med hjälp av Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 06/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: cadf21423ac7eb997db5bb42005ee307d9813331
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604200"
---
# <a name="customize-server-configuration-parameters-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Anpassa Server konfigurations parametrar för Azure Database for PostgreSQL-enskild server med Azure CLI
Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure PostgreSQL-server med hjälp av kommando rads gränssnittet (Azure CLI). En del av motor konfigurationerna exponeras på server nivå och kan ändras. 

## <a name="prerequisites"></a>Förutsättningar
För att gå igenom den här instruktions guiden behöver du:
- Skapa en Azure Database for PostgreSQL-Server och-databas genom att följa [skapa en Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Installera kommando rads gränssnittet för [Azure CLI](/cli/azure/install-azure-cli) på datorn eller Använd [Azure Cloud Shell](../cloud-shell/overview.md) i Azure Portal med hjälp av webbläsaren.

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Visa lista med Server konfigurations parametrar för Azure Database for PostgreSQL Server
Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du kommandot [AZ postgres Server Configuration List](/cli/azure/postgres/server/configuration) .

Du kan ange serverns konfigurations parametrar för servern **mydemoserver.postgres.Database.Azure.com** under resurs grupp **myresourcegroup**.
```azurecli-interactive
az postgres server configuration list --resource-group myresourcegroup --server mydemoserver
```
## <a name="show-server-configuration-parameter-details"></a>Visa information om Server konfigurations parameter
Om du vill visa information om en viss konfigurations parameter för en server kör du kommandot [AZ postgres Server Configuration show](/cli/azure/postgres/server/configuration)  .

I det här exemplet visas information om konfigurations parametern för **loggen för \_ min \_ e-post** för Server- **mydemoserver.postgres.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az postgres server configuration show --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
## <a name="modify-server-configuration-parameter-value"></a>Ändra värdet för Server konfigurations parameter
Du kan också ändra värdet för en viss server konfigurations parameter, som uppdaterar det underliggande konfiguration svärdet för PostgreSQL-Server motorn. Om du vill uppdatera konfigurationen använder du kommandot [AZ postgres Server Configuration set](/cli/azure/postgres/server/configuration) . 

Så här uppdaterar du serverns konfigurations parameter för **\_ minsta antal \_ meddelanden** i Server **mydemoserver.postgres.Database.Azure.com** under resurs grupp **myresourcegroup.**
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver --value INFO
```
Om du vill återställa värdet för en konfigurations parameter väljer du bara att lämna den valfria `--value` parametern och tjänsten använder standardvärdet. I exemplet ovan skulle det se ut så här:
```azurecli-interactive
az postgres server configuration set --name log_min_messages --resource-group myresourcegroup --server mydemoserver
```
Det här kommandot återställer konfigurationen **för \_ minsta antal \_ meddelanden** till **varningen** för standardvärdet. Mer information om Server konfiguration och tillåtna värden finns i PostgreSQL-dokumentation om [Server konfiguration](https://www.postgresql.org/docs/9.6/static/runtime-config.html).

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du startar om en server](howto-restart-server-cli.md)
- Information om hur du konfigurerar och åtkomst till Server loggar finns [i Server loggar i Azure Database for PostgreSQL](concepts-server-logs.md)
