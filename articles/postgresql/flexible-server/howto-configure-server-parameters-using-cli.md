---
title: Konfigurera parametrar – Azure Database for PostgreSQL-flexibel Server
description: Den här artikeln beskriver hur du konfigurerar postgres-parametrar i Azure Database for PostgreSQL-flexibel server med hjälp av Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/22/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 315e4439ca66a3fbadac228c013797f516bc2940
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605772"
---
# <a name="customize-server-parameters-for-azure-database-for-postgresql---flexible-server-using-azure-cli"></a>Anpassa Server parametrar för Azure Database for PostgreSQL-flexibel server med Azure CLI

Du kan visa, Visa och uppdatera konfigurations parametrar för en Azure PostgreSQL-server med hjälp av kommando rads gränssnittet (Azure CLI). En delmängd av motor parametrarna exponeras på server nivå och kan ändras. 

## <a name="prerequisites"></a>Förutsättningar

För att gå igenom den här instruktions guiden behöver du:
- Skapa en Azure Database for PostgreSQL-Server och-databas genom att följa [skapa en Azure Database for PostgreSQL](quickstart-create-server-cli.md)
- Installera kommando rads gränssnittet för [Azure CLI](/cli/azure/install-azure-cli) på datorn eller Använd [Azure Cloud Shell](../../cloud-shell/overview.md) i Azure Portal med hjälp av webbläsaren.

## <a name="list-server-parameters-for-a-flexible-server"></a>Visa en lista med Server parametrar för en flexibel Server

Om du vill visa alla ändrings bara parametrar på en server och deras värden kör du kommandot [AZ postgres Flexible-Server parameter List](/cli/azure/postgres/flexible-server/parameter) .

Du kan visa Server parametrarna för Server- **mydemoserver.postgres.Database.Azure.com** under resurs grupp **myresourcegroup**.

```azurecli-interactive
az postgres flexible-server parameter list --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="show-server-parameter-details"></a>Visa information om Server parameter

Om du vill visa information om en viss parameter för en server kör du kommandot [AZ postgres flexibla-Server parameter Visa](/cli/azure/postgres/flexible-server/parameter)  .

I det här exemplet visas information om Server **mydemoserver.postgres.Database.Azure.com** för **loggen för \_ min \_ e-post** under resurs grupp **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter show --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

## <a name="modify-server-parameter-value"></a>Ändra värdet för Server parametern

Du kan också ändra värdet för en viss server parameter, som uppdaterar det underliggande konfiguration svärdet för PostgreSQL-Server motorn. Om du vill uppdatera parametern använder du kommandot [AZ postgres Flexible-Server parameter uppsättning](/cli/azure/postgres/flexible-server/parameter) . 

Så här uppdaterar du Server parametern för **loggen för \_ minsta antal \_ meddelanden** i Server **mydemoserver.postgres.Database.Azure.com** under resurs grupp **myresourcegroup.**

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --value INFO --resource-group myresourcegroup --server-name mydemoserver
```

Om du vill återställa värdet för en parameter väljer du bara att lämna den valfria `--value` parametern och tjänsten använder standardvärdet. I exemplet ovan skulle det se ut så här:

```azurecli-interactive
az postgres flexible-server parameter set --name log_min_messages --resource-group myresourcegroup --server-name mydemoserver
```

Det här kommandot återställer parametern **för \_ minsta antal \_ meddelanden** till **varningen** standardvärdet. Mer information om Server parametrar och tillåtna värden finns i PostgreSQL-dokumentation om hur du [ställer in parametrar](https://www.postgresql.org/docs/12/config-setting.html).

## <a name="next-steps"></a>Nästa steg

- Information om hur du konfigurerar och åtkomst till Server loggar finns [i Server loggar i Azure Database for PostgreSQL](concepts-logging.md)
