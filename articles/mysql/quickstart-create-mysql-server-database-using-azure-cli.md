---
title: 'Snabb start: skapa en server – Azure CLI – Azure Database for MySQL'
description: I den här snabbstarten beskrivs hur du använder Azure CLI till att skapa en Azure Database för MySQL-server i en Azure-resursgrupp.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 4f774351fc0eaaf32069687a1943c72b74b6308a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96494332"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Snabb start: skapa en Azure Database for MySQL-server med Azure CLI

> [!TIP]
> Överväg att använda det enklare [AZ MySQL](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) med Azure CLI-kommandot (för närvarande i för hands version). Prova [snabb](./quickstart-create-server-up-azure-cli.md)starten.

Den här snabb starten visar hur du använder [Azure CLI](/cli/azure/get-started-with-azure-cli) -kommandon i [Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for MySQL server på fem minuter. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabb starten kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

 - Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

   ```azurecli
   az account set --subscription <subscription id>
   ```

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med kommandot [AZ Group Create](/cli/azure/group) och skapa sedan en MySQL-server i den här resurs gruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Skapa en Azure Database för MySQL-server med kommandot [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create). En server kan innehålla flera databaser.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

Här följer information om argument ovan: 

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för din Azure Database for MySQL-server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
location | westus | Azure-platsen för servern.
admin-user | myadmin | Användarnamnet för administratörsinloggning. Det kan inte vara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
admin-password | *säkert lösenord* | Lösenordet för administratörsanvändaren. Det måste innehålla mellan 8 och 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.
sku-name|GP_Gen5_2|Ange namnet på pris nivån och beräknings konfigurationen. Följer konventionen {prisnivå}_{beräkningsgenerering}_{vCores} i snabbformat. Mer information finns på [pris nivåerna](./concepts-pricing-tiers.md) .

>[!IMPORTANT] 
>- Standard versionen av MySQL på servern är 5,7. Det finns för närvarande 5,6-versioner och 8,0-versioner.
>- Se det här [referens dokumentet](/cli/azure/mysql/server#az-mysql-server-create)om du vill visa alla argument för kommandot **AZ MySQL server Create** .
>- SSL är aktiverat som standard på servern. Mer infroamtion på SSL finns i [Konfigurera SSL-anslutning](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå 
Som standard är den nya servern som skapas skyddad med brand Väggs regler och inte är tillgänglig offentligt. Du kan konfigurera brand Väggs regeln på servern med hjälp av kommandot [AZ MySQL server Firewall-Rule Create](/cli/azure/mysql/server/firewall-rule) . Detta gör att du kan ansluta till servern lokalt.

I följande exempel skapas en brandväggsregel som kallas `AllowMyIP` som tillåter anslutningar från den specifika IP-adressen 192.168.0.1. Ersätt IP-adressen som du kommer att ansluta från. Du kan använda ett intervall med IP-adresser om det behövs. Vet du inte hur du letar efter din IP-adress och går sedan till [https://whatismyipaddress.com/](https://whatismyipaddress.com/) för att hämta din IP-adress.

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

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Ansluta till Azure Database for MySQL server med MySQL kommando rads klient
Du kan ansluta till servern med hjälp av ett populärt klient verktyg, **[mysql.exe](https://dev.mysql.com/downloads/)** kommando rads verktyg med [Azure Cloud Shell](../cloud-shell/overview.md). Du kan också använda mysql-kommandoraden i din lokala miljö.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du endast vill ta bort den nyss skapade servern kan du köra kommandot [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete).

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Bygg en PHP-app i Windows med MySQL](../app-service/tutorial-php-mysql-app.md)