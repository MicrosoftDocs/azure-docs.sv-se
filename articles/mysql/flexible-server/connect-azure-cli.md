---
title: 'Snabbstart: Ansluta med Azure CLI – Azure Database for MySQL – flexibel server'
description: Den här snabbstarten innehåller flera olika sätt att ansluta till Azure CLI med Azure Database for MySQL – flexibel server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: e0fd5969a3c4f84b6e8f98e99335bf120179e7af
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481097"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Snabbstart: Ansluta och fråga med Azure CLI med Azure Database for MySQL – flexibel server

> [!IMPORTANT]
> Azure Database for MySQL – Flexibel server är för närvarande i offentlig förhandsversion.

Den här snabbstarten visar hur du ansluter till en Azure Database for MySQL flexibel server med hjälp av Azure CLI med ```az mysql flexible-server connect``` kommandot . Med det här kommandot kan du testa anslutningen till databasservern och köra frågor direkt mot servern.  Du kan också köra kommandot i ett interaktivt läge för att köra flera frågor.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har någon kan du skaffa [en kostnadsfri utvärderingsversion.](https://azure.microsoft.com/free/)
- Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 eller senare)
- Logga in med Azure CLI med ```az login``` kommandot 
- Aktivera parameterpersistence med ```az config param-persist on``` . Parameterperssistence hjälper dig att använda lokal kontext utan att behöva upprepa många argument som resursgrupp eller plats osv.

## <a name="create-an-mysql-flexible-server"></a>Skapa en flexibel MySQL-server

Det första steget är att skapa en hanterad MySQL-server. I [Azure Cloud Shell](https://shell.azure.com/)kör du följande skript och anteckning om **servernamnet,** **användarnamnet** och lösenordet  **som genererades** från det här kommandot.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Du kan ange ytterligare argument för det här kommandot för att anpassa det. Se alla argument för [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Skapa en databas
Kör följande kommando för att skapa en databas, **newdatabase** om du inte redan har skapat en.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Visa alla argumenten
Du kan visa alla argument för det här kommandot med ```--help``` argumentet . 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testa databasserveranslutningen
Kör följande skript för att testa och verifiera anslutningen till databasen från utvecklingsmiljön.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Exempel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Du bör se följande utdata för att anslutningen ska lyckas:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Om anslutningen misslyckades kan du prova följande lösningar:
- Kontrollera om port 3306 är öppen på klientdatorn.
- om serveradministratörens användarnamn och lösenord är korrekta
- om du har konfigurerat brandväggsregeln för klientdatorn
- Om du har konfigurerat servern med privat åtkomst i virtuella nätverk kontrollerar du att klientdatorn finns i samma virtuella nätverk.

## <a name="run-single-query"></a>Köra enskild fråga
Kör följande kommando för att köra en enskild fråga med ```--querytext``` argumentet ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Exempel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Du ser utdata enligt nedan:

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
Du kan köra flera frågor i det **interaktiva** läget. Om du vill aktivera interaktivt läge kör du följande kommando

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Exempel:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Du ser **MySQL-gränssnittet** enligt nedan:

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
* [Ansluta till Azure Database for MySQL – flexibel server med krypterade anslutningar](how-to-connect-tls-ssl.md)
* [Hantera servern](./how-to-manage-server-cli.md)

