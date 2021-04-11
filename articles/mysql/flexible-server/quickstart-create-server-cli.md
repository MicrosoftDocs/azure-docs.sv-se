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
ms.openlocfilehash: a63c6f074178794db38b47950e176dd729344a54
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492736"
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

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Skapa en flexibel server

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md) med `az group create` kommandot och skapa sedan din MySQL-flexibla server i den här resurs gruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Skapa en flexibel server med `az mysql flexible-server create` kommandot. En server kan innehålla flera databaser. Följande kommando skapar en server som använder tjänstens standardinställningar och värden från Azure CLI: s [lokala kontext](/cli/azure/local-context): 

```azurecli-interactive
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

## <a name="create-a-database"></a>Skapa en databas
Kör följande kommando för att skapa en databas, **newdatabase** om du inte redan har skapat en.

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

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

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Anslut och testa anslutningen med Azure CLI

Azure Database for MySQL flexibel Server kan du ansluta till din MySQL-server med Azure CLI- ```az mysql flexible-server connect``` kommandot. Med det här kommandot kan du testa anslutningen till databas servern, skapa en snabb start databas och köra frågor direkt mot servern utan att behöva installera mysql.exe eller MySQL Workbench.  Du kan också använda kör kommandot i ett interaktivt läge för att köra flera frågor.

Kör följande skript för att testa och validera anslutningen till databasen från utvecklings miljön.

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**Exempel:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
Du bör se följande utdata för lyckad anslutning:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Om anslutningen Miss lyckas kan du prova följande lösningar:
- Kontrol lera om Port 3306 är öppen på klient datorn.
- om Server administratörens användar namn och lösen ord är korrekta
- Om du har konfigurerat brand Väggs regeln för klient datorn
- Om du har konfigurerat servern med privat åtkomst i virtuella nätverk kontrollerar du att klient datorn finns i samma virtuella nätverk.

Kör följande kommando för att köra en enskild fråga med ```--querytext``` argument, ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exempel:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Mer information om hur du använder ```az mysql flexible-server connect``` kommandot finns i dokumentationen för [anslutning och fråga](connect-azure-cli.md) .

## <a name="connect-using-mysql-command-line-client"></a>Anslut med MySQL kommando rads klient

Om du har skapat din flexibla server med hjälp av privat åtkomst (VNet-integrering) måste du ansluta till servern från en resurs i samma virtuella nätverk som din server. Du kan skapa en virtuell dator och lägga till den i det virtuella nätverket som skapats med din flexibla Server. Mer information om hur du konfigurerar [privat åtkomst-dokumentationen](how-to-manage-virtual-network-portal.md) .

Om du har skapat din flexibla server med hjälp av offentlig åtkomst (tillåtna IP-adresser) kan du lägga till din lokala IP-adress i listan över brand Väggs regler på servern. Se [skapa eller hantera brand Väggs regler dokumentation](how-to-manage-firewall-portal.md) för steg-för-steg-anvisningar.

Du kan använda antingen [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från den lokala miljön. Azure Database for MySQL flexibla servern stöder anslutning av klient program till MySQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). TLS är ett bransch standard protokoll som garanterar krypterade nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad. Om du vill ansluta till din MySQL-flexibla Server måste du ladda ned det [offentliga SSL-certifikatet](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) för verifiering av certifikat utfärdare. Om du vill veta mer om hur du ansluter till krypterade anslutningar eller inaktiverar SSL, se [ansluta till Azure Database for MySQL-flexibel server med krypterade anslutnings](how-to-connect-tls-ssl.md) -dokumentation.

I följande exempel visas hur du ansluter till den flexibla servern med hjälp av kommando rads gränssnittet MySQL. Först installerar du kommando raden MySQL om den inte redan är installerad. Du kommer att hämta det DigiCertGlobalRootCA-certifikat som krävs för SSL-anslutningar. Använd inställningen--SSL-mode = krav på anslutnings sträng för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till parametern--SSL-ca. Ersätt värden med det faktiska Server namnet och lösen ordet.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Om du har etablerad den flexibla servern med hjälp av **offentlig åtkomst** kan du också använda [Azure Cloud Shell](https://shell.azure.com/bash) för att ansluta till din flexibla server med den förinstallerade mysql-klienten som visas nedan:

För att du ska kunna använda Azure Cloud Shell för att ansluta till din flexibla Server måste du tillåta nätverks åtkomst från Azure Cloud Shell till din flexibla Server. För att åstadkomma detta kan du gå till bladet **nätverk** på Azure Portal för din MySQL-flexibla Server och markera kryss rutan under **brand Väggs** avsnittet som säger "Tillåt offentlig åtkomst från valfri Azure-tjänst i Azure till den här servern", som visas på skärm bilden nedan och klicka på Spara för att spara inställningen.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Skärm bild som visar hur du tillåter Azure Cloud Shell åtkomst till MySQL-flexibel Server för nätverks konfiguration för offentlig åtkomst.":::
 
 
> [!NOTE]
> Kontroll av alternativet **Tillåt offentlig åtkomst från alla Azure-tjänster i Azure till den här servern** bör endast användas för utveckling eller testning. Den konfigurerar brand väggen så att den tillåter anslutningar från IP-adresser som tilldelats till en Azure-tjänst eller till gång, inklusive anslutningar från andra kunders prenumerationer.

Klicka på **försök** att starta Azure Cloud Shell och Använd följande kommandon för att ansluta till din flexibla Server. Använd Server namnet, användar namnet och lösen ordet i kommandot. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> När du ansluter till en flexibel server med hjälp av Azure Cloud Shell måste du använda parametern--SSL = True och inte--SSL-mode = nödvändig.
> Den primära orsaken är Azure Cloud Shell levereras med förinstallerad mysql.exe-klient från MariaDB-distribution som kräver parametern--SSL medan mysql-klienten från Oracle-distributionen kräver parametern--SSL-mode.

Om du ser följande fel meddelande när du ansluter till den flexibla servern efter kommandot tidigare missade du inte att ange brand Väggs regeln med alternativet "Tillåt offentlig åtkomst från alla Azure-tjänster i Azure till den här servern" ovan, eller så har alternativet inte sparats. Försök att ställa in brand väggen igen och försök igen.

FEL 2002 (HY000): det går inte att ansluta till MySQL server på <servername> (115)

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

>[!div class="nextstepaction"]
> [Anslut och fråga med Azure CLI](connect-azure-cli.md) 
>  [Ansluta till Azure Database for MySQL-flexibel server med krypterade anslutningar](how-to-connect-tls-ssl.md) 
>  [Bygg en php-webbapp (Laravel) med MySQL](tutorial-php-database-app.md)
