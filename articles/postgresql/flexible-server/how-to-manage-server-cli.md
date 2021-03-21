---
title: Hantera Server – Azure CLI – Azure Database for PostgreSQL-flexibel Server
description: Lär dig hur du hanterar en Azure Database for PostgreSQL-flexibel Server från Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96493686"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Hantera en Azure Database for PostgreSQL-flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version.

Den här artikeln visar hur du hanterar din flexibla server som distribueras i Azure. Hanterings uppgifter omfattar skalning av beräknings-och lagrings utrymme, återställning av administratörs lösen ord och visning av Server information.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. 

Du måste köra Azure CLI version 2,0 eller senare, lokalt. Kör kommandot `az --version` om du vill se vilken version som är installerad. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

Logga in på ditt konto med hjälp av kommandot [AZ login](/cli/azure/reference-index#az-login) . 

```azurecli-interactive
az login
```

Välj din prenumeration med kommandot [AZ Account set](/cli/azure/account) . Anteckna **ID-** värdet från **AZ-inloggnings** resultatet som ska användas som värde för **prenumerations** argumentet i följande kommando. Om du har flera prenumerationer väljer du den prenumeration som resursen ska faktureras till. Använd kommandot [AZ Account List](/cli/azure/account#az-account-list) för att identifiera alla prenumerationer.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Om du inte har skapat en flexibel Server ännu måste du göra det för att följa den här instruktions guiden.

## <a name="scale-compute-and-storage"></a>Skala beräkning och lagring

Du kan enkelt skala upp beräknings nivån, virtuella kärnor och lagringen med hjälp av följande kommando. En lista över alla Server åtgärder som du kan köra finns i Översikt över [AZ postgres-flexibla servrar](/cli/azure/postgres/flexible-server) .

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Nedan visas information om argumenten i föregående kod:

**Inställning** | **Exempelvärde** | **Beskrivning**
---|---|---
name | mydemoserver | Ange ett unikt namn för servern. Ditt servernamn får bara innehålla gemener, siffror och bindestreck. Det måste innehålla mellan 3 och 63 tecken.
resource-group | myresourcegroup | Ange namnet på Azure-resursgruppen.
sku-name|Standard_D4ds_v3|Ange namnet på beräknings nivån och storleken. Värdet följer konventions *Standard_ {VM-storlek}* i korthet. Mer information finns på [pris nivåerna](../concepts-pricing-tiers.md) .
storage-size | 6144 | Ange lagrings kapaciteten för servern i megabyte. Minimivärdet är 5120 och ökar i steg om 1024.

> [!IMPORTANT]
> Det går inte att skala upp lagringen. 

## <a name="manage-postgresql-databases-on-a-server"></a>Hantera PostgreSQL-databaser på en server

Det finns ett antal program som du kan använda för att ansluta till Azure Database för PostgreSQL-servern. Om din klient dator har PostgreSQL installerat kan du använda en lokal instans av [psql](https://www.postgresql.org/docs/current/static/app-psql.html). Nu ska vi använda kommando rads verktyget psql för att ansluta till den Azure Database for PostgreSQL servern.

1. Kör följande **psql** -kommando:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Till exempel ansluter följande kommando till standard databasen som heter **postgres** på din postgresql-server **mydemoserver.postgres.Database.Azure.com** via dina autentiseringsuppgifter för åtkomst. När du uppmanas till det anger du det `<server_admin_password>` som du har valt.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   När du har anslutit visar verktyget psql en **postgres** -prompt där du kan ange SQL-kommandon. En varning visas i det första anslutnings resultatet om den version av psql som du använder skiljer sig från versionen på Azure Database for PostgreSQL-servern.

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
   > "psql: allvarligt: ingen pg_hba. conf-post för värden `<IP address>` , användare" mina administratörer ", databas" postgres ", SSL på oåterkallelig: SSL-anslutning krävs. Ange SSL-alternativ och försök igen. "
   >
   > Kontrol lera att klientens IP-adress är tillåten i brand Väggs reglerna.

2. Skapa en tom databas med namnet **postgresdb** genom att skriva följande kommando i kommando tolken:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. I kommando tolken kör du följande kommando för att växla anslutning till den nyligen skapade databasen **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Skriv  `\q` och välj RETUR för att avsluta psql.

I det här avsnittet är du ansluten till Azure Database for PostgreSQL-servern via psql och skapat en tom användar databas.

## <a name="reset-the-admin-password"></a>Återställa administratörs lösen ordet

Du kan ändra administratörs rollens lösen ord med följande kommando:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Välj ett lösen ord som innehåller minst 8 tecken och högst 128 tecken. Lösen ordet måste innehålla tecken från tre av följande kategorier: 
> - Engelska versala bokstäver
> - Engelska gemena bokstäver
> - Tal
> - Icke-alfanumeriska tecken

## <a name="delete-a-server"></a>Ta bort en server

Om du vill ta bort den Azure Database for PostgreSQL flexibla servern kör du kommandot [AZ postgres flexibl-Server Delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

- [Förstå säkerhets kopierings-och återställnings begrepp](concepts-backup-restore.md)
- [Justera och övervaka servern](concepts-monitoring.md)