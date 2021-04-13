---
title: Utöka lagring automatiskt – Azure CLI – Azure Database for PostgreSQL – enskild server
description: I den här artikeln beskrivs hur du kan konfigurera automatisk tillväxt för lagring med hjälp av Azure CLI Azure Database for PostgreSQL – enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: d16fe5ef6654ee29c3e345ff0532ed91206d86d3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366179"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Utöka automatiskt Azure Database for PostgreSQL – enskild server med Hjälp av Azure CLI
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL-serverlagring så att den växer utan att arbetsbelastningen påverkas.

Servern som [når lagringsgränsen](./concepts-pricing-tiers.md#reaching-the-storage-limit)är inställd på skrivskydd. Om automatisk storleksfördrering är aktiverad för servrar med mindre än 100 GB etablerat lagringsutrymme ökas den etablerade lagringsstorleken med 5 GB så snart det lediga lagringsutrymmet är lägre än större än 1 GB eller 10 % av det etablerade lagringsutrymmet. För servrar med mer än 100 GB etablerat lagringsutrymme ökar den etablerade lagringsstorleken med 5 % när det lediga utrymmet är mindre än 10 GB av den etablerade lagringsstorleken. De maximala lagringsgränser som anges [här](./concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en [Azure Database for PostgreSQL server](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Aktivera automatisk tillväxt för PostgreSQL-serverlagring

Aktivera lagring med automatisk tillväxt för servern på en befintlig server med följande kommando:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera lagring med automatisk tillväxt för servern när du skapar en ny server med följande kommando:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om hur du skapar aviseringar för mått](howto-alert-on-metric.md).
