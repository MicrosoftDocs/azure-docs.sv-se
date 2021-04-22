---
title: 'Snabbstart: Skapa en server – Azure CLI – Azure Database for MySQL'
description: I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 63c7fe703a1fbf4cb46532085a33efd74e6a76ef
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875385"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Snabbstart: Skapa en Azure Database for MySQL server med Azure CLI

> [!TIP]
> Överväg att använda det enklare [Azure CLI-kommandot az mysql](/cli/azure/mysql#az_mysql_up) (för närvarande i förhandsversion). Prova [snabbstarten](./quickstart-create-server-up-azure-cli.md).

Den här snabbstarten visar hur du använder [Azure CLI-kommandon](/cli/azure/get-started-with-azure-cli) [i Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for MySQL-server på fem minuter. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabbstarten kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

 - Välj den specifika prenumerationen under ditt konto med [kommandot az account](/cli/azure/account) set. Anteckna ID-värdet **från az** login-utdata som ska användas som värde för  **prenumerationsargumentet** i kommandot . Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta hela prenumerationen använder [du az account list](/cli/azure/account#az_account_list).

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [az group create](/cli/azure/group) och skapa sedan mySQL-servern i den här resursgruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Skapa en Azure Database för MySQL-server med kommandot [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create). En server kan innehålla flera databaser.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Här är information om argumenten ovan: 

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för din Azure Database for MySQL server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
location | westus | Azure-platsen för servern.
admin-user | myadmin | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
admin-password | *säkert lösenord* | Lösenordet för administratörsanvändaren. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.
sku-name|GP_Gen5_2|Ange namnet på prisnivån och beräkningskonfigurationen. Följer konventionen {prisnivå}_{beräkningsgenerering}_{vCores} i snabbformat. Mer information [finns i prisnivåerna.](./concepts-pricing-tiers.md)

>[!IMPORTANT] 
>- Standardversionen av MySQL på servern är 5.7. Vi har för närvarande även versionerna 5.6 och 8.0 tillgängliga.
>- Om du vill visa alla argument för **kommandot az mysql server create** kan du se det här [referensdokumentet](/cli/azure/mysql/server#az_mysql_server_create).
>- SSL är aktiverat som standard på servern . Mer information om SSL finns i Konfigurera [SSL-anslutning](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå 
Som standard skyddas den nya server som skapas med brandväggsregler och kan inte nås offentligt. Du kan konfigurera brandväggsregeln på servern med kommandot [az mysql server firewall-rule create.](/cli/azure/mysql/server/firewall-rule) På så sätt kan du ansluta till servern lokalt.

I följande exempel skapas en brandväggsregel som kallas `AllowMyIP` som tillåter anslutningar från den specifika IP-adressen 192.168.0.1. Ersätt IP-adressen som du ska ansluta från. Du kan använda ett intervall med IP-adresser om det behövs. Vet inte hur du söker efter din IP-adress och gå sedan till för [https://whatismyipaddress.com/](https://whatismyipaddress.com/) att hämta din IP-adress.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 3306.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Ansluta till Azure Database for MySQL-servern med mysql-kommandoradsklienten
Du kan ansluta till servern med ett populärt **[ klientverktyg,mysql.exe](https://dev.mysql.com/downloads/)** kommandoradsverktyg [med](../cloud-shell/overview.md)Azure Cloud Shell . Du kan också använda mysql-kommandoraden i din lokala miljö.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du endast vill ta bort den nyss skapade servern kan du köra kommandot [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Skapa en PHP-app i Windows med MySQL](../app-service/tutorial-php-mysql-app.md)