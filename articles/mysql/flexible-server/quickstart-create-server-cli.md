---
title: 'Snabbstart: Skapa en server – Azure CLI – Azure Database for MySQL – flexibel server'
description: Den här snabbstarten beskriver hur du använder Azure CLI för att skapa Azure Database for MySQL flexibel server i en Azure-resursgrupp.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770245"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>Snabbstart: Skapa en Azure Database for MySQL flexibel server med Azure CLI

Den här snabbstarten visar hur du använder [Azure CLI-kommandon](/cli/azure/get-started-with-azure-cli) [i Azure Cloud Shell](https://shell.azure.com) för att skapa en Azure Database for MySQL flexibel server på fem minuter. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT] 
> Azure Database for MySQL flexibel server är för närvarande i offentlig förhandsversion

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Det [Azure Cloud Shell](../../cloud-shell/overview.md) är ett kostnadsfritt interaktivt gränssnitt som du kan använda för att köra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också öppna Cloud Shell en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kodblocken, klistra in det i Cloud Shell och välj Retur **för** att köra den.

Om du föredrar att installera och använda CLI lokalt kräver den här snabbstarten Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Förutsättningar

Du måste logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) Observera **id-egenskapen,** som refererar till **prenumerations-ID för** ditt Azure-konto.

```azurecli-interactive
az login
```

Välj den specifika prenumerationen under ditt konto med [kommandot az account](/cli/azure/account#az_account_set) set. Anteckna ID-värdet **från az** login-utdata som ska användas som värde för  **prenumerationsargumentet** i kommandot . Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Om du vill hämta alla dina prenumerationer använder [du az account list](/cli/azure/account#az_account_list).

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>Skapa en flexibel server

Skapa en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) med `az group create` kommandot och skapa sedan din mySQL flexibla server i den här resursgruppen. Du bör ange ett unikt namn. I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `eastus2`.

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

Skapa en flexibel server med `az mysql flexible-server create` kommandot . En server kan innehålla flera databaser. Följande kommando skapar en server med hjälp av tjänstens standardvärden och värden från den lokala Azure [CLI-kontexten](/cli/azure/local-context): 

```azurecli-interactive
az mysql flexible-server create
```

Servern som skapas har attributen nedan: 
- Automatiskt genererat servernamn, administratörsnamn, administratörslösenord, resursgruppsnamn (om det inte redan har angetts i lokal kontext) och på samma plats som resursgruppen 
- Tjänstens standardinställningar för återstående serverkonfigurationer: beräkningsnivå (burstable), beräkningsstorlek/SKU (B1MS), kvarhållningsperiod för säkerhetskopior (7 dagar) och MySQL-version (5.7)
- Standardanslutningsmetoden är Privat åtkomst (VNet-integrering) med ett automatiskt genererat virtuellt nätverk och undernät

> [!NOTE] 
> Anslutningsmetoden kan inte ändras när du har skapat servern. Om du till exempel valde *Privat åtkomst (VNet-integrering)* under skapa kan du inte ändra till Offentlig *åtkomst (tillåtna IP-adresser)* efter att du skapat. Vi rekommenderar starkt att du skapar en server med privat åtkomst för säker åtkomst till servern med VNet-integrering. Läs mer om privat åtkomst i [begreppsartikeln](./concepts-networking.md).

Om du vill ändra standardvärdena kan du läsa [](/cli/azure/mysql/flexible-server) den fullständiga listan med konfigurerbara CLI-parametrar i Azure CLI-referensdokumentationen. 

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

Om du vill ändra standardvärdena kan du läsa [](/cli/azure/mysql/flexible-server) den fullständiga listan med konfigurerbara CLI-parametrar i Azure CLI-referensdokumentationen. 

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

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Ansluta och testa anslutningen med Azure CLI

Azure Database for MySQL Flexibel server kan du ansluta till mysql-servern med Azure ```az mysql flexible-server connect``` CLI-kommandot. Med det här kommandot kan du testa anslutningen till databasservern, skapa en snabbstartdatabas och köra frågor direkt mot servern utan att behöva installera mysql.exe eller MySQL Workbench.  Du kan också köra kommandot i ett interaktivt läge för att köra flera frågor.

Kör följande skript för att testa och verifiera anslutningen till databasen från utvecklingsmiljön.

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
Om anslutningen misslyckades kan du prova följande lösningar:
- Kontrollera om port 3306 är öppen på klientdatorn.
- om användarnamnet och lösenordet för serveradministratören är korrekta
- om du har konfigurerat brandväggsregeln för klientdatorn
- Om du har konfigurerat servern med privat åtkomst i virtuella nätverk kontrollerar du att klientdatorn finns i samma virtuella nätverk.

Kör följande kommando för att köra en enskild fråga med ```--querytext``` argumentet ```-q``` .

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exempel:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
Mer information om hur du ```az mysql flexible-server connect``` använder kommandot finns i dokumentationen för att ansluta [och](connect-azure-cli.md) fråga.

## <a name="connect-using-mysql-command-line-client"></a>Ansluta med kommandoradsklienten mysql

Om du har skapat din flexibla server med hjälp av privat åtkomst (VNet-integrering) måste du ansluta till servern från en resurs i samma virtuella nätverk som servern. Du kan skapa en virtuell dator och lägga till den i det virtuella nätverket som skapats med din flexibla server. Läs mer [i dokumentationen om att konfigurera](how-to-manage-virtual-network-portal.md) privat åtkomst.

Om du har skapat din flexibla server med hjälp av offentlig åtkomst (tillåtna IP-adresser) kan du lägga till din lokala IP-adress i listan över brandväggsregler på servern. Stegvisa [anvisningar finns i dokumentationen om att](how-to-manage-firewall-portal.md) skapa eller hantera brandväggsregler.

Du kan använda [ antingenmysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) eller [MySQL Workbench](./connect-workbench.md) för att ansluta till servern från din lokala miljö. Azure Database for MySQL Flexibel server stöder anslutning av klientprogram till MySQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). TLS är ett branschstandardprotokoll som säkerställer krypterade nätverksanslutningar mellan databasservern och klientprogram, så att du kan följa efterlevnadskraven. Om du vill ansluta till din flexibla MySQL-server måste du ladda ned det offentliga [SSL-certifikatet för](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) verifiering av certifikatutfärdare. Mer information om hur du ansluter med krypterade anslutningar eller inaktiverar SSL finns i dokumentationen Anslut till Azure Database for MySQL – flexibel [server med krypterade](how-to-connect-tls-ssl.md) anslutningar.

I följande exempel visas hur du ansluter till din flexibla server med mysql-kommandoradsgränssnittet. Du installerar först mysql-kommandoraden om den inte redan är installerad. Du laddar ned Det DigiCertGlobalRootCA-certifikat som krävs för SSL-anslutningar. Använd inställningen --ssl-mode=REQUIRED connection string för att framtvinga TLS/SSL-certifikatverifiering. Skicka den lokala certifikatfilens sökväg till parametern --ssl-ca. Ersätt värden med det faktiska servernamnet och lösenordet.

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

Om du har etablerat din flexibla server med offentlig åtkomst kan du även använda [Azure Cloud Shell](https://shell.azure.com/bash) för att ansluta till din flexibla server med hjälp av förinstallerad mysql-klient enligt nedan:

För att kunna använda Azure Cloud Shell för att ansluta till din flexibla server måste du tillåta nätverksåtkomst från Azure Cloud Shell till din flexibla server. För att uppnå detta  kan du gå till bladet Nätverk på Azure Portal för din flexibla MySQL-server och markera kryssrutan **under** avsnittet Brandvägg, där det står "Tillåt offentlig åtkomst från valfri Azure-tjänst i Azure till den här servern" enligt skärmbilden nedan och klicka på Spara för att spara inställningen.

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="Skärmbild som visar hur du Azure Cloud Shell åtkomst till MySQL – flexibel server för konfiguration av offentligt åtkomstnätverk.":::
 
 
> [!NOTE]
> Kontroll av **Tillåt offentlig åtkomst från alla Azure-tjänster i Azure till den här** servern bör endast användas för utveckling eller testning. Den konfigurerar brandväggen för att tillåta anslutningar från IP-adresser som allokerats till valfri Azure-tjänst eller Azure-tillgång, inklusive anslutningar från prenumerationer för andra kunder.

Klicka på **Prova för** att starta Azure Cloud Shell och använd följande kommandon för att ansluta till din flexibla server. Använd ditt servernamn, användarnamn och lösenord i kommandot. 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> När du ansluter till din flexibla server med Azure Cloud Shell måste du använda parametern --ssl=true och inte --ssl-mode=REQUIRED.
> Den primära orsaken är Azure Cloud Shell levereras med förinstallerad mysql.exe-klient från MariaDB-distribution som kräver parametern --ssl medan mysql-klienten från Oracles distribution kräver parametern --ssl-mode.

Om du ser följande felmeddelande när du ansluter till din flexibla server efter kommandot tidigare missade du att ange brandväggsregeln med hjälp av "Tillåt offentlig åtkomst från alla Azure-tjänster i Azure till den här servern" som nämndes tidigare, eller så sparas inte alternativet. Försök att konfigurera brandväggen igen och försök igen.

FEL 2002 (HY000): Det går inte att ansluta till MySQL-servern <servername> på (115)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de här resurserna för en annan snabbstart/självstudie kan du ta bort dem genom att utföra följande kommando:

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du bara vill ta bort den nyligen skapade servern kan du köra `az mysql server delete` kommandot .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

>[!div class="nextstepaction"]
> [Ansluta och fråga med Azure CLI](connect-azure-cli.md) 
>  [Ansluta till Azure Database for MySQL – flexibel server med krypterade anslutningar](how-to-connect-tls-ssl.md) 
>  [Skapa en PHP-webbapp (Laravel) med MySQL](tutorial-php-database-app.md)
