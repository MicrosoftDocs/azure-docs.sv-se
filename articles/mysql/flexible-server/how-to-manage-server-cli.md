---
title: Hantera server – Azure CLI – Azure Database for MySQL flexibel server
description: Lär dig hur du hanterar Azure Database for MySQL flexibel server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4ef1408d5f7afc3b78ab021cdd25eedd75110849
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776940"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Hantera en Azure Database for MySQL – flexibel server (förhandsversion) med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL – flexibel server är för närvarande i offentlig förhandsversion.

Den här artikeln visar hur du hanterar din flexibla server (förhandsversion) som distribueras i Azure. Hanteringsaktiviteter omfattar beräknings- och lagringsskalning, återställning av administratörslösenord och visning av serverinformation.

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

> [!Important]
> Om du inte redan har skapat en flexibel server ännu skapar du en för att komma igång med den här guiden.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

Du kan enkelt skala upp beräkningsnivån, de virtuella kärnorna och lagringen med hjälp av följande kommando. Du kan se all serveråtgärd som du kan utföra [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Här är information om argumenten ovan:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för din Azure Database for MySQL server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|Standard_D4ds_v4|Ange namnet på beräkningsnivån och storleken. Följer konventionen för Standard_{VM-storlek} i korthet. Mer information [finns i prisnivåerna.](../concepts-pricing-tiers.md)
storage-size | 6144 | Serverns lagringskapacitet (enheten är megabyte). Minst 5120 och ökningar i steg om 1 024.

> [!Important]
> - Lagring kan skalas upp (du kan dock inte skala ned lagringen)


## <a name="manage-mysql-databases-on-a-server"></a>Hantera MySQL-databaser på en server.
Du kan använda något av dessa kommandon för att skapa, ta bort , lista och visa databasegenskaper för en databas på servern

| Cmdlet | Användning| Description |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Skapar en databas|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Ta bort databasen från servern. Det här kommandot tar inte bort servern. |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|visar en lista över alla databaser på servern|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Visar mer information om databasen|

## <a name="update-admin-password"></a>Uppdatera administratörslösenordet
Du kan ändra administratörsrollens lösenord med det här kommandot
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Kontrollera att lösenordet är minst 8 tecken och högst 128 tecken.
> Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.

## <a name="delete-a-server"></a>Ta bort en server
Om du bara vill ta bort den flexibla MySQL-servern kan du köra [kommandot az mysql flexible-server server delete.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du startar eller stoppar en server](how-to-stop-start-server-portal.md)
- [Lär dig hur du hanterar ett virtuellt nätverk](how-to-manage-virtual-network-cli.md)
- [Felsöka anslutningsproblem](how-to-troubleshoot-common-connection-issues.md)
- [Skapa och hantera brandvägg](how-to-manage-firewall-cli.md)