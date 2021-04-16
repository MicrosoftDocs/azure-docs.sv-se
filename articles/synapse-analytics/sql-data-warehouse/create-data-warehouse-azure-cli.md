---
title: 'Snabbstart: Skapa en Synapse SQL pool med Azure CLI'
description: Skapa snabbt en Synapse SQL-pool med en brandväggsregel på servernivå med hjälp av Azure CLI.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 532c06cc49f9fddab09c6678ee5fff9fe32347cb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565941"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Snabbstart: Skapa en Synapse SQL pool med Azure CLI

Skapa en Synapse SQL (informationslager) i Azure Synapse Analytics med hjälp av Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Komma igång

Använd de här kommandona för att logga in på Azure och konfigurera en resursgrupp.

1. Om du använder en lokal installation kör du kommandot [az login](/cli/azure/reference-index#az_login) för att logga in på Azure:

   ```azurecli
   az login
   ```

1. Om det behövs använder du [kommandot az account set](/cli/azure/account#az_account_set) för att välja din prenumeration:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kör kommandot [az group create](/cli/azure/group#az_group_create) för att skapa en resursgrupp:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Skapa en [logisk SQL-server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med kommandot [az sql server](/cli/azure/sql/server#az_sql_server_create) create:

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   En server innehåller en grupp med databaser som hanteras som en grupp.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en [brandväggsregel på servernivå.](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) En brandväggsregel på servernivå gör att externa program, till SQL Server Management Studio eller SQLCMD-verktyget, kan ansluta till en SQL-pool via SQL-pooltjänstens brandvägg.

Kör kommandot [az sql server firewall-rule create för](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) att skapa en brandväggsregel:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

I det här exemplet öppnas brandväggen endast för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

> [!NOTE]
> SQL-slutpunkter kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Skapa och hantera din SQL-pool

Skapa SQL-poolen. I det här exemplet används DW100c som tjänstmål, vilket är en startpunkt med lägre kostnad för din SQL-pool.

> [!NOTE]
> Du behöver en arbetsyta som skapats tidigare. Mer information finns i [Snabbstart: Skapa en Azure Synapse-arbetsyta med Azure CLI.](../quickstart-create-workspace-cli.md)

Använd kommandot [az synapse sql pool create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) för att skapa SQL-poolen:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Mer information om parameteralternativen finns i [az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool).

Du kan se dina SQL-pooler med kommandot [az synapse sql pool list:](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list)

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Använd kommandot [az synapse sql pool update](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) för att uppdatera en befintlig pool:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Använd kommandot [az synapse sql pool pause för](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) att pausa poolen:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Använd kommandot [az synapse sql pool resume för](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) att starta en pausad pool:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Om du vill ta bort en befintlig SQL-pool använder [du kommandot az synapse sql pool delete:](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete)

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten.

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabbstartsguider ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta använder du kommandot [az group delete för](/cli/azure/group#az_group_delete) att ta bort alla resurser som har skapats i den här snabbstarten.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu har du skapat en SQL-pool, skapat en brandväggsregel och anslutit till din SQL-pool. Om du vill veta mer fortsätter du till [artikeln Läsa in data i SQL-pool.](./load-data-from-azure-blob-storage-using-copy.md)
