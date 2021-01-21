---
title: 'Snabb start: skapa en server – Azure CLI – Azure Database for MariaDB'
description: Den här snabbstarten beskriver hur du använder Azure CLI för att skapa en Azure Database for MariaDB-server i en Azure-resursgrupp.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 2242b0842bda9587915fc949046b8fa7effb725c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662168"
---
# <a name="quickstart-create-an-azure-database-for-mariadb-server-by-using-the-azure-cli"></a>Snabb start: skapa en Azure Database for MariaDB-server med hjälp av Azure CLI

Du kan använda Azure CLI för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI för att skapa en Azure Database for MariaDB-server i en Azure-resursgrupp på ungefär fem minuter.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

Om du har flera prenumerationer väljer du den prenumeration som innehåller den resurs eller prenumeration där du debiteras. För att välja ett specifikt prenumerations-ID i ditt konto använder du kommandot [az account set](/cli/azure/account#az-account-set):

```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med hjälp av kommandot [az group create](/cli/azure/group#az-group-create). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med namnet `myresourcegroup` på platsen `westus`:

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Skapa en Azure Database for MariaDB-server

Skapa en Azure Database for MariaDB-server med hjälp av kommandot [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create). En server kan hantera flera databaser. Normalt används en separat databas för varje projekt eller för varje användare.

Inställning | Exempelvärde | Description
---|---|---
name | **mydemoserver** | Ange ett unikt namn för din Azure Database for MariaDB-server. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | **myresourcegroup** | Ange namnet på Azure-resursgruppen.
sku-name | **GP_Gen5_2** | Namnet på SKU:n. Efter *pris nivån* för konventions \_ *beräknings generation* \_ *virtuella kärnor* i korthet. Mer information om parametern **sku-name** finns i avsnittet efter den här tabellen.
backup-retention | **7** | Hur länge en säkerhetskopia ska behållas. Enheten är dagar. Intervall: 7 till 35. 
geo-redundant-backup | **Disabled** (Inaktiverat) | Huruvida geo-redundanta säkerhetskopieringar ska aktiveras för den här servern. Tillåtna värden: **aktiverade**, **inaktiverade**.
location | **westus** | Azure-platsen för servern.
ssl-enforcement | **Aktiverad** | Huruvida SSL ska aktiveras för den här servern. Tillåtna värden: **aktiverade**, **inaktiverade**.
storage-size | **51200** | Serverns lagringskapacitet (enheten är megabyte). Giltiga lagringsstorlekar är 5 120 MB (minst) med ökningar i steg om 1 024 MB. Mer information om storleksgränser för lagring finns i [Prisnivåer](./concepts-pricing-tiers.md). 
version | **10,2** | MariaDB-huvudmotorversionen.
admin-user | **myadmin** | Användarnamnet för administratörsinloggningen. Parametern **admin-user** får inte bvara **azure_superuser**, **admin**, **administrator**, **root**, **guest** eller **public**.
admin-password | *ditt lösen ord* | Lösenordet för administratörsanvändaren. Lösenordet måste innehålla mellan 8 och 128 tecken. Det måste innehålla tecken från tre av följande kategorier: engelska versala bokstäver, engelska gemena bokstäver, siffror och icke-alfanumeriska tecken.

Parametervärdet för sku-namn följer namngivningskonventionen {prisnivå}\_{compute-generering}\_{vCores} som i exemplen nedan:
+ `--sku-name B_Gen5_1` mappar till Basic, Gen 5 och 1 virtuell kärna. Det här alternativet är minsta tillgängliga SKU.
+ `--sku-name GP_Gen5_32` mappar till generell användning, Gen 5 och 32 vCores.
+ `--sku-name MO_Gen5_2` mappar till minnesoptimerad, Gen 5 och 2 virtuella kärnor.

Information om giltiga värden efter region och om nivåer finns i [Prisnivåer](./concepts-pricing-tiers.md).

I följande exempel skapas en server med namnet **mydemoserver** i regionen USA, västra. Servern är i resursgruppen **myresourcegroup** och har serveradministratörsinloggningen **myadmin**. Servern är en Gen 5-server på prisnivån Generell användning med 2 virtuella kärnor. Ett servernamn mappar till ett DNS-namn och måste vara globalt unikt i Azure. Ersätt `<server_admin_password>` med ditt eget serveradministratörslösenord.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

> [!NOTE]
> Överväg att använda prisnivån Basic om lätt beräkning och I/O är lämpligt för din arbetsbelastning. Observera att servrar som skapas på prisnivån Basic inte senare kan skalas till Generell användning eller Minnesoptimerad. Mer information finns på [sidan med priser](https://azure.microsoft.com/pricing/details/mariadb/).

## <a name="configure-a-firewall-rule"></a>Konfigurera en brandväggsregel

Skapa en Azure Database for MariaDB-brandväggsregel på servernivå med hjälp av kommandot [az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create). En brandväggsregel på servernivå gör att externa program, som mysql-kommandoradsverktyget eller MySQL Workbench, kan ansluta till servern via Azure Database for MariaDB-tjänstens brandvägg.

I följande exempel skapas en brandväggsregel som kallas `AllowMyIP` som tillåter anslutningar från den specifika IP-adressen 192.168.0.1. Ersätt en IP-adress eller ett intervall med IP-adresser som motsvarar den plats som du ansluter från.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Anslutningar till Azure Database for MariaDB kommunicerar via port 3306. Om du försöker ansluta inifrån ett företagsnätverk tillåts kanske inte utgående trafik via port 3306. I så fall kan du bara ansluta till servern om IT-avdelningen öppnar port 3306.

## <a name="configure-ssl-settings"></a>Konfigurera SSL-inställningar

Som standard verkställs SSL-anslutningar mellan servern och dina klientprogram. Denna standardinställning garanterar säkerheten för data ”i rörelse” genom att dataströmmen över Internet krypteras. För den här snabbstarten inaktiverar du SSL-anslutningar för din server. Avaktivering av SSL rekommenderas inte för produktionsservrar. Se [Konfigurera SSL-anslutning i din app för säker anslutning till Azure Database for MariaDB](./howto-configure-ssl.md) för mer information.

I följande exempel inaktiveras tvingande SSL på din Azure Database for MariaDB-server:

```azurecli-interactive
az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
```

## <a name="get-connection-information"></a>Hämta anslutningsinformation

För att ansluta till servern måste du ange värddatorinformationen och autentiseringsuppgifterna. För att få anslutningsinformationen kör du följande kommando:

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Resultatet är i JSON-format. Anteckna värdena för **fullyQualifiedDomainName** och **administratorLogin**.

```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
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
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-by-using-the-mysql-command-line-tool"></a>Ansluta till servern med hjälp av mysql-kommandoradsverktyget

Anslut till servern med hjälp av mysql-kommandoradsverktyget. Du kan [ladda ned](https://dev.mysql.com/downloads/) kommandoradsverktyget och installera det på datorn. Du kan även komma åt kommandoradsverktyget genom att välja knappen **Prova** på ett kodexempel i den här artikeln. Ett annat sätt att komma åt kommandoradsverktyget är att välja knappen **>_** i det övre högra verktygsfältet i Azure-portalen för att öppna **Azure Cloud Shell**.

Så här ansluter du till servern med hjälp av mysql-kommandoradsverktyget:

1. Anslut till servern:

   ```azurecli-interactive
   mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   ```

2. Visa serverstatusen i `mysql>`-prompten:

   ```sql
   status
   ```

   Nu bör du se utdata som liknar följande text:

   ```cmd
   C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
   Enter password: ***********
   Welcome to the MySQL monitor.  Commands end with ; or \g.
   Your MySQL connection id is 65512
   Server version: 5.6.39.0 MariaDB Server

   Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

   Oracle is a registered trademark of Oracle Corporation and/or its
   affiliates. Other names may be trademarks of their respective
   owners.

   Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

   mysql> status
   --------------
   mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

   Connection id:          64681
   Current database:
   Current user:           myadmin@40.118.201.21
   SSL:                    Cipher in use is AES256-SHA
   Current pager:          stdout
   Using outfile:          ''
   Using delimiter:        ;
   Server version:         5.6.39.0 MariaDB Server
   Protocol version:       10
   Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
   Server characterset:    latin1
   Db     characterset:    latin1
   Client characterset:    utf8
   Conn.  characterset:    utf8
   TCP port:               3306
   Uptime:                 1 day 3 hours 28 min 50 sec

   Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
   --------------

   mysql>
   ```

> [!TIP]
> Fler kommandon finns i [referenshandboken för MySQL 5.7 – kapitel 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Ansluta till servern med MySQL Workbench

1. Öppna MySQL Workbench på klientdatorn. Om det inte redan är installerat [laddar du ned](https://dev.mysql.com/downloads/workbench/) och installerar programmet.

2. I dialogrutan **Konfigurera ny anslutning** anger du följande information på fliken **Parametrar**:

   ![Skapa en ny anslutning](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

   | Inställning | Föreslaget värde | Beskrivning |
   |---|---|---|
   | Anslutningsnamn | **Demoanslutning** | Ange ett namn på den här anslutningen (anslutningsnamnet kan vara vad som helst) |
   | Anslutningsmetod | **Standard (TCP/IP)** | Använda TCP/IP-protokollet för att ansluta till Azure Database for MariaDB |
   | Värdnamn | **mydemoserver.mariadb.database.azure.com** | Det servernamn som du antecknade tidigare. |
   | Port | **3306** | Standardporten för Azure Database for MariaDB. |
   | Användarnamn | **mydemoserver för administratörer \@** | Den serveradministratörsinloggning som du antecknade tidigare. |
   | Lösenord | *ditt lösen ord* | Ange det lösenord som du konfigurerade tidigare. |

3. För att kontrollera om alla parametrar har konfigurerats korrekt väljer du **Testa anslutning**.

4. Välj anslutningen för att ansluta till servern.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver de resurser som du använde i den här snabbstarten för en annan snabbstart eller självstudie kan du ta bort dem genom att köra följande kommando: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Om du bara vill ta bort den server som du skapade i den här snabbstarten kör du kommandot [az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete):

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforma en MariaDB-databas med Azure CLI](tutorial-design-database-cli.md)
