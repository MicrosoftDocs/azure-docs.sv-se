---
title: 'Snabb start: skapa en server – Azure CLI-Azure Database for MySQL-flexibel Server'
description: I den här snabb starten beskrivs hur du använder Azure CLI för att skapa en Azure Database for MySQL flexibel server i en Azure-resurs grupp.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 65cc3d2fdcbdea934e80a5f0012ca4f3da157ca3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94843442"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Snabb start: skapa en Azure Database for MySQL flexibel server med Azure CLI

Den här snabb starten visar hur du använder [Azure CLI](/cli/azure/get-started-with-azure-cli) -kommandon i [Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for MySQL flexibel server på fem minuter. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT] 
> Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och välj **RETUR** för att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabb starten Azure CLI version 2,0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [AZ login](/cli/azure/reference-index#az-login) . Observera egenskapen **ID** som refererar till **prenumerations-ID** för ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den aktuella prenumerationen under ditt konto med kommandot [AZ Account set](/cli/azure/account#az-account-set) . Anteckna **ID-** värdet från **AZ inloggnings** -utdata som ska användas som värde för argumentet **prenumeration** i kommandot. Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Använd [AZ Account List](/cli/azure/account#az-account-list)för att hämta alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Skapa en flexibel server

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md) med `az group create` kommandot och skapa sedan din MySQL-flexibla server i den här resurs gruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Skapa en flexibel server med `az mysql flexible-server create` kommandot. En server kan innehålla flera databaser. Följande kommando skapar en server som använder tjänstens standardinställningar och värden från Azure CLI: s [lokala kontext](/cli/azure/local-context): 

```azurecli
az mysql flexible-server create
```

Servern som har skapats har följande attribut: 
- Automatiskt genererat Server namn, administratörs användar namn, administratörs lösen ord, resurs grupp namn (om det inte redan har angetts i den lokala kontexten) och på samma plats som resurs gruppen 
- Standardinställningar för tjänsten för återstående serverkonfigurationer: beräknings nivå (burst), Compute size/SKU (B1MS), kvarhållning av säkerhets kopior (7 dagar) och MySQL-version (5,7)
- Standard anslutnings metoden är privat åtkomst (VNet-integrering) med ett automatiskt genererat virtuellt nätverk och undernät

> [!NOTE] 
> Anslutnings metoden kan inte ändras när servern har skapats. Om du till exempel har valt *privat åtkomst (VNet-integrering)* under Create kan du inte ändra till *offentlig åtkomst (tillåtna IP-adresser)* när du har skapat. Vi rekommenderar starkt att du skapar en server med privat åtkomst för säker åtkomst till servern med VNet-integrering. Läs mer om privat åtkomst i [artikeln begrepp](./concepts-networking.md).

Om du vill ändra några standardvärden, se [referens dokumentationen](/cli/azure/mysql/flexible-server) för Azure CLI för den fullständiga listan över KONFIGURERBARa CLI-parametrar. 

Nedan visas några exempel på utdata: 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

Om du vill ändra några standardvärden, se [referens dokumentationen](/cli/azure/mysql/flexible-server) för Azure CLI för den fullständiga listan över KONFIGURERBARa CLI-parametrar. 

> [!NOTE]
> Anslutningar till Azure Database för MySQL kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk kanske utgående trafik via port 3306 inte tillåts. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 3306.

## <a name="get-the-connection-information"></a>Hämta anslutningsinformationen

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna.

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna **fullyQualifiedDomainName** och **administratorLogin**. Nedan visas ett exempel på JSON-utdata: 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-using-mysql-command-line-client"></a>Anslut med MySQL kommando rads klient

Eftersom den flexibla servern skapades med *privat åtkomst (VNet-integrering)* måste du ansluta till servern från en resurs i samma VNet som servern. Du kan skapa en virtuell dator och lägga till den i det virtuella nätverk som skapats. 

När den virtuella datorn har skapats kan du använda SSH i datorn och installera det populära klient verktyget **[mysql.exe](https://dev.mysql.com/downloads/)** kommando rads verktyget.

Med mysql.exe ansluter du med kommandot nedan. Ersätt värden med det faktiska Server namnet och lösen ordet. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du bara vill ta bort den nyligen skapade servern kan du köra `az mysql server delete` kommandot.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Bygg en PHP-webbapp (Laravel) med MySQL](tutorial-php-database-app.md)
