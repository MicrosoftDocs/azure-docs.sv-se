---
title: 'Snabb start: ansluta med Azure CLI – Azure Database for MySQL-flexibel Server'
description: Den här snabb starten innehåller flera olika sätt att ansluta till Azure CLI med Azure Database for MySQL-flexibel Server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: d2b899150f0f740349c991319c87ef3a50f839b0
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102494780"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Snabb start: Anslut och fråga med Azure CLI med Azure Database for MySQL-flexibel Server

> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här snabb starten visar hur du ansluter till en Azure Database for MySQL flexibel server med hjälp av Azure CLI med ```az mysql flexible-server connect``` kommandot. Med det här kommandot kan du testa anslutningen till din databas server och köra frågor direkt mot servern.  Du kan också använda kör kommandot i ett interaktivt läge för att köra flera frågor.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har någon kan du [få en kostnads fri utvärderings version](https://azure.microsoft.com/free/).
- Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 eller senare)
- Logga in med kommandot Azure CLI med ```az login``` 
- Aktivera parameter persistence med ```az config param-persist on``` . Parameter persistence hjälper dig att använda lokal kontext utan att behöva upprepa många argument, t. ex. resurs grupp eller plats osv.

## <a name="create-an-mysql-flexible-server"></a>Skapa en MySQL-flexibel Server

Det första steget är att skapa en hanterad MySQL-server. Kör följande skript i [Azure Cloud Shell](https://shell.azure.com/)och anteckna **Server namnet**, **användar namnet** och  **lösen ordet** som genereras av det här kommandot.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Du kan ange ytterligare argument för det här kommandot för att anpassa det. Se alla argument för [AZ MySQL Flexible Server Create](/cli/azure/mysql/flexible-server?view=azure-cli-latest#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Skapa en databas
Kör följande kommando för att skapa en databas, **newdatabase** om du inte redan har skapat en.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Visa alla argument
Du kan visa alla argument för det här kommandot med ```--help``` argumentet. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testa databas server anslutning
Kör följande skript för att testa och validera anslutningen till databasen från utvecklings miljön.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Exempel:**
```azurecli
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

## <a name="run-single-query"></a>Kör en enskild fråga
Kör följande kommando för att köra en enskild fråga med ```--querytext``` argument, ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exempel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Du kommer att se utdata som visas nedan:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Köra flera frågor med interaktivt läge
Du kan köra flera frågor i **interaktivt** läge. Kör följande kommando för att aktivera interaktivt läge

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Exempel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Du kommer att se **MySQL** -Shell-upplevelsen som visas nedan:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera servern](./how-to-manage-server-cli.md)

