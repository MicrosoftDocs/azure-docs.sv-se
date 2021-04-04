---
title: Hantera Server – Azure CLI – Azure Database for MySQL flexibel Server
description: Lär dig hur du hanterar en Azure Database for MySQL flexibel Server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92545063"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Hantera en Azure Database for MySQL-flexibel Server (för hands version) med Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln visar hur du hanterar din flexibla Server (förhands granskning) distribuerad i Azure. Hanterings uppgifter omfattar skalning av beräknings-och lagrings utrymme, återställning av administratörs lösen ord och visning av Server information.

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

> [!Important]
> Om du inte redan har skapat en flexibel Server ännu kan du skapa en för att komma igång med den här guiden.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

Du kan skala upp beräknings nivån, virtuella kärnor och lagringen på ett enkelt sätt med hjälp av följande kommando. Du kan se alla Server åtgärder som du kan utföra [AZ MySQL-uppdateringen för flexibel Server](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Här följer information om argument ovan:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för din Azure Database for MySQL-server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|Standard_D4ds_v4|Ange namnet på beräknings nivån och storleken. Följ regel Standard_ {VM-storlek} i korthet. Mer information finns på [pris nivåerna](../concepts-pricing-tiers.md) .
storage-size | 6144 | Serverns lagringskapacitet (enheten är megabyte). Lägsta 5120 och ökar i steg om 1024.

> [!Important]
> - Lagringen kan skalas upp (men du kan inte skala lagringen)


## <a name="manage-mysql-databases-on-a-server"></a>Hantera MySQL-databaser på en server.
Du kan använda något av dessa kommandon för att skapa, ta bort, Visa och Visa databas egenskaper för en databas på servern

| Cmdlet | Användning| Beskrivning |
| --- | ---| --- |
|[AZ MySQL-databas skapa flexibelt-Server DB](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Skapar en databas|
|[AZ MySQL-databas ta bort flexibelt Server DB](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Ta bort databasen från servern. Det här kommandot tar inte bort servern. |
|[AZ MySQL-lista med flexibla Server databaser](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|visar en lista över alla databaser på servern|
|[AZ MySQL-flexibelt-Server DB show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Visar mer information om databasen|

## <a name="update-admin-password"></a>Uppdatera administratörs lösen ord
Du kan ändra administratörs rollens lösen ord med det här kommandot
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Kontrol lera att lösen ordet är minst 8 tecken och högst 128 tecken.
> Lösen ordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.

## <a name="delete-a-server"></a>Ta bort en server
Om du bara vill ta bort den MySQL-flexibla servern kan du köra kommandot [AZ MySQL Flexible Server Delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
- [Lär dig hur du startar eller stoppar en server](how-to-stop-start-server-portal.md)
- [Lär dig hur du hanterar ett virtuellt nätverk](how-to-manage-virtual-network-cli.md)
- [Felsöka anslutningsproblem](how-to-troubleshoot-common-connection-issues.md)
- [Skapa och hantera brand vägg](how-to-manage-firewall-cli.md)