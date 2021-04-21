---
title: Hantera server – Azure CLI – Azure Database for PostgreSQL
description: Lär dig hur du hanterar Azure Database for PostgreSQL server från Azure CLI.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: 36151a9afda0bb23ee244ee778a30f30a41243ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791471"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Hantera en Azure Database for PostgreSQL enskild server med hjälp av Azure CLI

Den här artikeln visar hur du hanterar dina enskilda servrar som distribueras i Azure. Hanteringsuppgifter omfattar beräknings- och lagringsskalning, återställning av administratörslösenord och visning av serverinformation.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. Den här artikeln kräver att du kör Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) Observera **id-egenskapen,** som refererar till **prenumerations-ID för** ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den specifika prenumerationen under ditt konto med [kommandot az account](/cli/azure/account) set. Anteckna ID-värdet **från az** login-utdata som ska användas som värde för  **prenumerationsargumentet** i kommandot . Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta alla dina prenumerationer använder [du az account list](/cli/azure/account#az_account_list).

```azurecli
az account set --subscription <subscription id>
```

Om du inte redan har skapat en server kan du gå till den här [snabbstarten](quickstart-create-server-database-azure-cli.md) för att skapa en.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

Du kan enkelt skala upp din prisnivå, beräkning och lagring med hjälp av följande kommando. Du kan se alla serveråtgärden som du kan utföra [az postgres server overview](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Här är information om argumenten ovan:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för din Azure Database for PostgreSQL server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|GP_Gen5_2|Ange namnet på prisnivån och beräkningskonfigurationen. Följer konventionen {prisnivå}_{beräkningsgenerering}_{vCores} i snabbformat. Mer information [finns i prisnivåerna.](./concepts-pricing-tiers.md)
storage-size | 6144 | Serverns lagringskapacitet (enheten är megabyte). Minst 5120 och ökningar i steg om 1 024.

> [!Important]
> - Lagring kan skalas upp (du kan dock inte skala ned lagringen)
> - Det går inte att skala upp från Basic till prisnivån Generell användning eller Minnesoptimerad. Du kan skala upp manuellt med  [antingen ett bash-skript](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) eller [med PostgreSQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>Hantera PostgreSQL-databaser på en server.
Du kan använda något av dessa kommandon för att skapa, ta bort, lista och visa databasegenskaper för en databas på servern

| Cmdlet | Användning| Description |
| --- | ---| --- |
|[az postgres db create](/cli/azure/sql/db#az_mysql_db_create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Skapar en databas|
|[az postgres db delete](/cli/azure/sql/db#az_mysql_db_delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Ta bort databasen från servern. Det här kommandot tar inte bort servern. |
|[az postgres db list](/cli/azure/sql/db#az_mysql_db_list)|```az postgres db list -g myresourcegroup -s mydemoserver```|visar en lista över alla databaser på servern|
|[az postgres db show](/cli/azure/sql/db#az_mysql_db_show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Visar mer information om databasen|

## <a name="update-admin-password"></a>Uppdatera administratörslösenord
Du kan ändra administratörsrollens lösenord med det här kommandot
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Kontrollera att lösenordet är minst 8 tecken och högst 128 tecken.
> Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.

## <a name="delete-a-server"></a>Ta bort en server
Om du bara vill ta bort PostgreSQL – enskild server kan du köra [kommandot az postgres server delete.](/cli/azure/mysql/server#az_mysql_server_delete)

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- [Starta om en server](howto-restart-server-cli.md)
- [Återställa en server i felaktigt tillstånd](howto-restore-server-cli.md)
- [Övervaka och finjustera servern](concepts-monitoring.md)
