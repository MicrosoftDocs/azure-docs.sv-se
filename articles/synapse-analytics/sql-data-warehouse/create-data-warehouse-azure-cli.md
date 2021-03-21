---
title: 'Snabb start: skapa en Synapse SQL-pool med Azure CLI'
description: Skapa snabbt en Synapse SQL-pool med en brand Väggs regel på server nivå med hjälp av Azure CLI.
services: synapse-analytics
author: gaursa
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.custom: azure-synapse
ms.openlocfilehash: c17bfc06e76bad7730707530001ebeccea539c37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602272"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Snabb start: skapa en Synapse SQL-pool med Azure CLI

Skapa en Synapse SQL-pool (informations lager) i Azure Synapse Analytics med hjälp av Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Komma igång

Använd de här kommandona för att logga in på Azure och konfigurera en resurs grupp.

1. Om du använder en lokal installation kör du kommandot [AZ login](/cli/azure/reference-index#az_login) för att logga in på Azure:

   ```azurecli
   az login
   ```

1. Om det behövs använder du kommandot [AZ Account set](/cli/azure/account#az_account_set) för att välja din prenumeration:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kör kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Skapa en [logisk SQL-Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med kommandot [AZ SQL Server Create](/cli/azure/sql/server#az_sql_server_create) :

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   En server innehåller en grupp med databaser som hanteras som en grupp.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en [brand Väggs regel på server nivå](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). En brand Väggs regel på server nivå gör det möjligt för ett externt program, till exempel SQL Server Management Studio eller SQLCMD-verktyget, att ansluta till en SQL-pool via SQL-poolens tjänst brand vägg.

Kör kommandot [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) för att skapa en brand Väggs regel:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

I det här exemplet öppnas brand väggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

> [!NOTE]
> SQL-slutpunkter kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte din IT-avdelning öppnar port 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Skapa och hantera SQL-poolen

Skapa SQL-poolen. I det här exemplet används DW100c som tjänst mål, som är en lägre start punkt för SQL-poolen.

> [!NOTE]
> Du behöver en arbets yta som skapats tidigare. Mer information finns i [snabb start: skapa en Azure datasynapses-arbetsyta med Azure CLI](../quickstart-create-workspace-cli.md).

Använd kommandot [AZ Synapse SQL pool Create](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) för att skapa SQL-poolen:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Mer information om parameter alternativen finns i [AZ Synapse SQL pool](/cli/azure/ext/synapse/synapse/sql/pool).

Du kan se dina SQL-pooler med kommandot [AZ Synapse SQL pool List](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list) :

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Använd [uppdaterings kommandot AZ Synapse SQL pool](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) för att uppdatera en befintlig pool:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Använd [paus kommandot AZ Synapse SQL-pool](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) för att pausa poolen:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Använd kommandot [AZ Synapse SQL pool Resume](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) för att starta en pausad pool:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Om du vill ta bort en befintlig SQL-pool använder du kommandot [AZ Synapse SQL pool Delete](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete) :

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten.

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabb starts guider ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta använder du kommandot [AZ Group Delete](/cli/azure/group#az_group_delete) för att ta bort alla resurser som skapats i den här snabb starten.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu har du skapat en SQL-pool, skapat en brand Väggs regel och anslutit till din SQL-pool. Om du vill veta mer kan du fortsätta till artikeln [Läs in data i SQL-poolen](./load-data-from-azure-blob-storage-using-copy.md) .