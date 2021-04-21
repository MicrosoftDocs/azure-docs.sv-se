---
title: 'Snabbstart: Skapa server – Azure CLI – Azure Database for PostgreSQL – enskild server'
description: I den här snabbstartsguiden skapar du en Azure Database for PostgreSQL-server med hjälp av Azure CLI.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a595d677cf0964083526cb7e2c73471148be0fd4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778448"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-cli"></a>Snabbstart: Skapa en Azure Database for PostgreSQL-server med hjälp av Azure CLI

Den här snabbstarten visar hur du använder Azure [CLI-kommandon i Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for PostgreSQL server på fem minuter. [](/cli/azure/get-started-with-azure-cli) Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

    > [!TIP]
    >  Överväg att använda det [enklare az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-kommandot som för närvarande är i förhandsversion. Prova [snabbstarten](./quickstart-create-server-up-azure-cli.md).

- Välj det specifika prenumerations-ID:t under ditt konto med [hjälp av kommandot az account set.](/cli/azure/account)

    - Anteckna ID-värdet **från az** **login-utdata** som ska användas som värde för **prenumerationsargumentet** i kommandot. 

        ```azurecli
        az account set --subscription <subscription id>
        ```

    - Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta alla dina prenumerationer använder [du az account list](/cli/azure/account#az_account_list).

## <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database for PostgreSQL-server

Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [az group create](/cli/azure/group#az_group_create) och skapa sedan din PostgreSQL-server i den här resursgruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Skapa en [Azure Database for PostgreSQL server med](overview.md) hjälp av kommandot az [postgres server](/cli/azure/postgres/server) create. En server kan innehålla flera databaser.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
Här är information om föregående argument: 

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Unikt namn som identifierar din Azure Database for PostgreSQL server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla 3 till 63 tecken. Mer information finns i [Azure Database for PostgreSQL namngivningsregler.](../azure-resource-manager/management/resource-name-rules.md#microsoftdbforpostgresql)
resource-group | myresourcegroup | Namnet på Azure-resursgruppen.
location | westus | Azure-plats för servern.
admin-user | myadmin | Användarnamn för administratörsinloggningen. Det kan inte **azure_superuser,** **admin,** **administratör,** **rot,** **gäst** eller **offentlig**.
admin-password | *säkert lösenord* | Administratörsanvändaren lösenord. Det måste innehålla 8 till 128 tecken från tre av följande kategorier: engelska versaler, engelska gemener, siffror och icke-alfanumeriska tecken.
sku-name|GP_Gen5_2| Namnet på prisnivån och beräkningskonfigurationen. Följ konventionen {pricing tier}_{compute generation}_{vCores} i korthet. Mer information finns i [Azure Database for PostgreSQL prissättning.](https://azure.microsoft.com/pricing/details/postgresql/server/)

>[!IMPORTANT] 
>- Standardversionen av PostgreSQL på servern är 9.6. Alla versioner som stöds finns i [PostgreSQL-huvudversioner som stöds.](./concepts-supported-versions.md)
>- Om du vill visa alla argument för **kommandot az postgres server create** kan du se det här [referensdokumentet](/cli/azure/postgres/server#az_postgres_server_create).
>- SSL är aktiverat som standard på servern. Mer information om SSL finns i Konfigurera [SSL-anslutning.](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå 
Som standard är den server som du skapade inte offentligt tillgänglig och skyddas med brandväggsregler. Du kan konfigurera brandväggsreglerna på servern med kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) för att ge din lokala miljö åtkomst till att ansluta till servern. 

I följande exempel skapas en brandväggsregel som kallas `AllowMyIP` som tillåter anslutningar från den specifika IP-adressen 192.168.0.1. Ersätt IP-adressen eller intervallet med IP-adresser som motsvarar den plats som du ska ansluta från. Om du inte känner till din IP-adress går du till [WhatIsMyIPAddress.com](https://whatismyipaddress.com/) hämta den.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Kontrollera att nätverkets brandvägg tillåter port 5432 för att undvika anslutningsproblem. Azure Database for PostgreSQL-servrar använder den porten. 

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

Om du vill ansluta till servern anger du värdinformation och autentiseringsuppgifter för åtkomst.

```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna värdena **administratorLogin och** **fullyQualifiedDomainName.**

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-the-azure-database-for-postgresql-server-by-using-psql"></a>Ansluta till Azure Database for PostgreSQL med psql
[Psql-klienten](https://www.postgresql.org/docs/current/static/app-psql.html) är ett populärt alternativ för att ansluta till PostgreSQL-servrar. Du kan ansluta till servern med psql med [Azure Cloud Shell](../cloud-shell/overview.md). Du kan även använda psql i din lokala miljö om du har det tillgängligt. En tom databas, **postgres,** skapas automatiskt med en ny PostgreSQL-server. Du kan använda databasen för att ansluta till psql, som du ser i följande kod. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

> [!TIP]
> Om du föredrar att använda en URL-sökväg för att ansluta till Postgres kodar URL:en @-inloggningen i användarnamnet med `%40` . Anslutningssträngen för psql skulle till exempel vara:
>
> ```
> psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
> ```


## <a name="clean-up-resources"></a>Rensa resurser
Om du inte behöver dessa resurser för en annan snabbstart eller självstudie kan du ta bort dem genom att köra följande kommando. 

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du bara vill ta bort den nyligen skapade servern kan du köra [kommandot az postgres server delete.](/cli/azure/postgres/server)

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Migrera databasen med export och import](./howto-migrate-using-export-and-import.md)
