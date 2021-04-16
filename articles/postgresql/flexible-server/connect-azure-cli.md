---
title: 'Snabbstart: Ansluta med Azure CLI – Azure Database for PostgreSQL – flexibel server'
description: Den här snabbstarten innehåller flera olika sätt att ansluta till Azure CLI med Azure Database for PostgreSQL – flexibel server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 7526644e02b0ed4d0522ad00a27b691ece98754a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479244"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Snabbstart: Ansluta och fråga med Azure CLI med Azure Database for PostgreSQL – flexibel server

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibel server är för närvarande i offentlig förhandsversion.

Den här snabbstarten visar hur du ansluter till en Azure Database for PostgreSQL flexibel server med hjälp av Azure CLI med ```az postgres flexible-server connect``` kommandot . Med det här kommandot kan du testa anslutningen till databasservern och köra frågor. Du kan också köra flera frågor i det interaktiva läget. 

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto. Om du inte har någon kan du skaffa [en kostnadsfri utvärderingsversion.](https://azure.microsoft.com/free/)
- Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 eller senare)
- Logga in med Azure CLI med ```az login``` kommandot 
- Aktivera parameterpersistence med ```az config param-persist on``` . Parameterpersistence hjälper dig att använda lokal kontext utan att behöva upprepa många argument som resursgrupp eller plats.

## <a name="create-an-postgresql-flexible-server"></a>Skapa en flexibel PostgreSQL-server

Det första vi ska skapa är en hanterad PostgreSQL-server. I [Azure Cloud Shell](https://shell.azure.com/)kör du följande skript och anteckning om servernamnet,  **användarnamnet** och lösenordet som **genererades** av det här kommandot.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Du kan ange ytterligare argument för det här kommandot för att anpassa det. Se alla argument för [az postgres flexible-server create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Visa alla argumenten
Du kan visa alla argument för det här kommandot med ```--help``` argument. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testa databasserveranslutningen
Du kan testa och verifiera anslutningen till databasen från utvecklingsmiljön med hjälp av kommandot .

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Exempel:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Du ser utdata om anslutningen lyckades.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Om anslutningen misslyckades kan du prova följande lösningar:
- Kontrollera om port 5432 är öppen på klientdatorn.
- om serveradministratörens användarnamn och lösenord är korrekta
- om du har konfigurerat brandväggsregeln för klientdatorn
- Om du har konfigurerat servern med privat åtkomst i virtuella nätverk kontrollerar du att klientdatorn finns i samma virtuella nätverk.

## <a name="run-single-query"></a>Köra enskild fråga
Du kan köra en enskild fråga med kommandot med ```--querytext``` argumentet ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Exempel:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Du ser utdata enligt nedan:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
Du kan köra flera frågor med hjälp av det **interaktiva** läget . Kör följande kommando för att aktivera interaktivt läge

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Exempel:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Du ser **psql-gränssnittet** enligt nedan:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera servern](./how-to-manage-server-cli.md)
