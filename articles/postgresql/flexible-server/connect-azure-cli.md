---
title: 'Snabb start: ansluta med Azure CLI – Azure Database for PostgreSQL-flexibel Server'
description: Den här snabb starten innehåller flera olika sätt att ansluta till Azure CLI med Azure Database for PostgreSQL-flexibel Server.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: f4eec89aadee1966271286b9280916af973e4b1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614351"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Snabb start: Anslut och fråga med Azure CLI med Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är för närvarande en offentlig för hands version.

Den här snabb starten visar hur du ansluter till en Azure Database for PostgreSQL flexibel server med hjälp av Azure CLI med ```az postgres flexible-server connect``` kommandot. Med det här kommandot kan du testa anslutningen till databas servern och köra frågor. Du kan också köra flera frågor i interaktivt läge. 

## <a name="prerequisites"></a>Förutsättningar
- Ett Azure-konto. Om du inte har någon kan du [få en kostnads fri utvärderings version](https://azure.microsoft.com/free/).
- Installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 eller senare)
- Logga in med kommandot Azure CLI med ```az login``` 
- Aktivera parameter persistence med ```az config param-persist on``` . Parameter persistence hjälper dig att använda lokal kontext utan att behöva upprepa många argument, t. ex. resurs grupp eller plats.

## <a name="create-an-postgresql-flexible-server"></a>Skapa en PostgreSQL-flexibel Server

Det första vi ska skapa är en hanterad PostgreSQL-Server. Kör följande skript i [Azure Cloud Shell](https://shell.azure.com/)och anteckna **Server namnet**, **användar namnet** och  **lösen ordet** som genereras av det här kommandot.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Du kan ange ytterligare argument för det här kommandot för att anpassa det. Se alla argument för [AZ postgres Flexible Server Create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Visa alla argument
Du kan visa alla argument för det här kommandot med ```--help``` argumentet. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Testa databas server anslutning
Du kan testa och verifiera anslutningen till databasen från utvecklings miljön med hjälp av kommandot.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Exempel:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Resultatet visas om anslutningen lyckades.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Om anslutningen Miss lyckas kan du prova följande lösningar:
- Kontrol lera om Port 5432 är öppen på klient datorn.
- om Server administratörens användar namn och lösen ord är korrekta
- Om du har konfigurerat brand Väggs regeln för klient datorn
- Om du har konfigurerat servern med privat åtkomst i virtuella nätverk kontrollerar du att klient datorn finns i samma virtuella nätverk.

## <a name="run-single-query"></a>Kör en enskild fråga
Du kan köra en enda fråga med kommandot med ```--querytext``` argument, ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Exempel:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Du kommer att se utdata som visas nedan:

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
Du kan köra flera frågor i **interaktivt** läge. Kör följande kommando för att aktivera interaktivt läge

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Exempel:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

**Psql** Shell-upplevelsen visas på det sätt som visas nedan:

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
