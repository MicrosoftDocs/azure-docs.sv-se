---
title: Utöka lagring automatiskt – Azure CLI – Azure Database for MariaDB
description: I den här artikeln beskrivs hur du kan aktivera lagring med automatisk tillväxt med hjälp av Azure CLI i Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f2ae7a890fc1dab29b6cc99e4cc88087dbc6e052
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366189"
---
# <a name="auto-grow-azure-database-for-mariadb-storage-using-the-azure-cli"></a>Utöka automatiskt Azure Database for MariaDB lagring med hjälp av Azure CLI
Den här artikeln beskriver hur du kan konfigurera en Azure Database for MariaDB serverlagring så att den växer utan att arbetsbelastningen påverkas.

Servern som [når lagringsgränsen](concepts-pricing-tiers.md#reaching-the-storage-limit)är inställd på skrivskydd. Om automatisk storleksfördrering är aktiverad för servrar med mindre än 100 GB etablerat lagringsutrymme ökas den etablerade lagringsstorleken med 5 GB så snart det lediga lagringsutrymmet är lägre än större än 1 GB eller 10 % av det etablerade lagringsutrymmet. För servrar med mer än 100 GB etablerat lagringsutrymme ökar den etablerade lagringsstorleken med 5 % när det lediga utrymmet är mindre än 10 GB av den etablerade lagringsstorleken. De maximala lagringsgränser som anges [här](concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar

Så här slutför du den här guiden:

- Du behöver en [Azure Database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="enable-mariadb-server-storage-auto-grow"></a>Aktivera automatisk tillväxt för MariaDB-serverlagring

Aktivera lagring med automatisk tillväxt för servern på en befintlig server med följande kommando:

```azurecli-interactive
az mariadb server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera lagring med automatisk tillväxt för servern när du skapar en ny server med följande kommando:

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.3
```

## <a name="next-steps"></a>Nästa steg

Lär dig [mer om hur du skapar aviseringar för mått](howto-alert-metric.md).
