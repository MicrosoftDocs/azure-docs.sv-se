---
title: Hantera server – Azure CLI – Azure Database for PostgreSQL – flexibel server
description: Lär dig hur du hanterar Azure Database for PostgreSQL – flexibel server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 9a7e16bf85293a412baf5015af825377438ebb7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778507"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Hantera en Azure Database for PostgreSQL – flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibel server är en förhandsversion.

Den här artikeln visar hur du hanterar din flexibla server som distribueras i Azure. Hanteringsaktiviteter omfattar beräknings- och lagringsskalning, återställning av administratörslösenord och visning av serverinformation.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. 

Du måste köra Azure CLI version 2.0 eller senare lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Logga in på ditt konto med kommandot [az login.](/cli/azure/reference-index#az_login) 

```azurecli-interactive
az login
```

Välj din prenumeration med kommandot [az account set.](/cli/azure/account) Anteckna ID-värdet **från az** login-utdata som ska användas som värde för  **prenumerationsargumentet** i följande kommando. Om du har flera prenumerationer väljer du den prenumeration som resursen ska faktureras till. Du kan identifiera alla dina prenumerationer med [kommandot az account list.](/cli/azure/account#az_account_list)

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Om du inte har skapat en flexibel server ännu måste du göra det för att följa den här instruktionerna.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

Du kan enkelt skala upp beräkningsnivå, virtuella kärnor och lagring med hjälp av följande kommando. En lista över alla serveråtgärder som du kan köra finns i översikten [az postgres flexible-server.](/cli/azure/postgres/flexible-server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Här följer information om argumenten i föregående kod:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för servern. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|Standard_D4ds_v3|Ange namnet på beräkningsnivån och storleken. Värdet följer konventionen *Standard_{VM size}* i korthet. Mer information [finns i prisnivåerna.](../concepts-pricing-tiers.md)
storage-size | 6144 | Ange serverns lagringskapacitet i megabyte. Minimiantalet är 5 120, vilket ökar i steg om 1 024.

> [!IMPORTANT]
> Du kan inte skala ned lagringen. 

## <a name="manage-postgresql-databases-on-a-server"></a>Hantera PostgreSQL-databaser på en server

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Om PostgreSQL är installerat på klientdatorn kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Nu ska vi använda psql-kommandoradsverktyget för att ansluta till Azure Database for PostgreSQL servern.

1. Kör följande **psql-kommando:**

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Följande kommando ansluter till exempel till standarddatabasen med namnet **postgres** på Din PostgreSQL-server **mydemoserver.postgres.database.azure.com** via dina autentiseringsuppgifter för åtkomst. När du uppmanas till det anger du `<server_admin_password>` den som du har valt.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   När du har anslutt visar psql-verktyget en **postgres-kommandotolk** där du kan ange SQL-kommandon. En varning visas i de första anslutningsutdata om versionen av psql som du använder skiljer sig från versionen på Azure Database for PostgreSQL servern.

   Exempel på psql-utdata:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Om brandväggen inte är konfigurerad att tillåta IP-adressen för din klient uppstår följande fel:
   >
   > "psql: FATAL: no pg_hba.conf entry for host `<IP address>` , user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Ange SSL-alternativ och försök igen."
   >
   > Bekräfta att klientens IP-adress tillåts i brandväggsreglerna.

2. Skapa en tom databas med **namnet postgresdb** genom att skriva följande kommando i kommandotolken:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. I kommandotolken kör du följande kommando för att växla anslutningar till den nyligen skapade databasen **postgresdb:**

    ```bash
    \c postgresdb
    ```

4. Skriv  `\q` och välj Retur för att avsluta psql.

I det här avsnittet anslöt du till Azure Database for PostgreSQL server via psql och skapade en tom användardatabas.

## <a name="reset-the-admin-password"></a>Återställa administratörslösenordet

Du kan ändra administratörsrollens lösenord med följande kommando:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Välj ett lösenord som har minst 8 tecken och högst 128 tecken. Lösenordet måste innehålla tecken från tre av följande kategorier: 
> - Engelska versala bokstäver
> - Engelska gemena bokstäver
> - Tal
> - Icke-alfanumeriska tecken

## <a name="delete-a-server"></a>Ta bort en server

Om du vill Azure Database for PostgreSQL flexibel server kör du [kommandot az postgres flexible-server delete.](/cli/azure/postgres/flexible-server#az_postgresql_flexible_server_delete)

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

- [Förstå begrepp för säkerhetskopiering och återställning](concepts-backup-restore.md)
- [Finjustera och övervaka servern](concepts-monitoring.md)