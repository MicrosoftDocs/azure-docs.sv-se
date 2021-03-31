---
title: Utöka lagringen automatiskt – Azure CLI – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs hur du kan konfigurera automatisk storleks ökning för lagring med hjälp av Azure CLI i Azure Database for PostgreSQL-enskild server.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 8/7/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b6f3875af4c7f0eace327d810c632a6bb217092
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94534202"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Utöka Azure Database for PostgreSQLs lagring automatiskt till en server med hjälp av Azure CLI
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for PostgreSQL Server lagring så att den växer utan att arbets belastningen påverkas.

Servern som [når lagrings gränsen](./concepts-pricing-tiers.md#reaching-the-storage-limit)är skrivskyddad. Om automatisk storleks ökning har Aktiver ATS för servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 5% av den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](./concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar

- Du behöver en [Azure Database for postgresql-server](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="enable-postgresql-server-storage-auto-grow"></a>Aktivera PostgreSQL Server Storage Auto-växer

Aktivera server för automatisk storleks ökning på en befintlig server med följande kommando:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera automatisk storleks ökning av servern när du skapar en ny server med följande kommando:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).