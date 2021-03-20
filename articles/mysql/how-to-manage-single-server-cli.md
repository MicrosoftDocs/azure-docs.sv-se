---
title: Hantera Server – Azure CLI – Azure Database for MySQL
description: Lär dig hur du hanterar en Azure Database for MySQL-server från Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: bd068f0cf76a8edefca854d72d5240c0becaf8fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542073"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Hantera en Azure Database for MySQL enskild server med Azure CLI

Den här artikeln visar hur du hanterar dina enda servrar som distribueras i Azure. Hanterings uppgifter omfattar skalning av beräknings-och lagrings utrymme, återställning av administratörs lösen ord och visning av Server information.

## <a name="prerequisites"></a>Förutsättningar
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. Den här artikeln kräver att du kör Azure CLI version 2,0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/reference-index#az-login) . Observera egenskapen **ID** som refererar till **prenumerations-ID** för ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

Om du inte redan har skapat en server kan du läsa den här [snabb](quickstart-create-mysql-server-database-using-azure-cli.md) starten för att skapa en.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring
Du kan skala upp pris nivån, beräkning och lagring enkelt med hjälp av följande kommando. Du kan se alla Server åtgärder som du kan utföra [AZ MySQL server-översikt](/cli/azure/mysql/server)

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Här följer information om argument ovan:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för din Azure Database for MySQL-server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|GP_Gen5_2|Ange namnet på pris nivån och beräknings konfigurationen. Följer konventionen {prisnivå}_{beräkningsgenerering}_{vCores} i snabbformat. Mer information finns på [pris nivåerna](./concepts-pricing-tiers.md) .
storage-size | 6144 | Serverns lagringskapacitet (enheten är megabyte). Lägsta 5120 och ökar i steg om 1024.

> [!Important]
> - Lagringen kan skalas upp (men du kan inte skala lagringen)
> - Det finns inte stöd för att skala upp från Basic till generell användning eller minnesoptimerade pris nivå. Du kan skala manuellt med antingen  [med ett bash-skript](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) eller [med MySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-mysql-databases-on-a-server"></a>Hantera MySQL-databaser på en server
Du kan använda något av dessa kommandon för att skapa, ta bort, Visa och Visa databas egenskaper för en databas på servern

| Cmdlet | Användning| Beskrivning |
| --- | ---| --- |
|[AZ MySQL db Create](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Skapar en databas|
|[AZ MySQL db Delete](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Ta bort databasen från servern. Det här kommandot tar inte bort servern. |
|[AZ MySQL db-lista](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|visar en lista över alla databaser på servern|
|[AZ MySQL db show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Visar mer information om databasen|

## <a name="update-admin-password"></a>Uppdatera administratörs lösen ord
Du kan ändra administratörs rollens lösen ord med det här kommandot
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Kontrol lera att lösen ordet är minst 8 tecken och högst 128 tecken.
> Lösen ordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.

## <a name="delete-a-server"></a>Ta bort en server
Om du bara vill ta bort den fristående MySQL-servern kan du köra kommandot [AZ MySQL server Delete](/cli/azure/mysql/server#az-mysql-server-delete) .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- [Starta om en server](howto-restart-server-cli.md)
- [Återställa en server med felaktigt tillstånd](howto-restore-server-cli.md)
- [Övervaka och finjustera servern](concepts-monitoring.md)