---
title: Utöka lagringen automatiskt – Azure CLI – Azure Database for MySQL
description: I den här artikeln beskrivs hur du kan aktivera automatisk storleks ökning med hjälp av Azure CLI i Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ae22a80829ecaaff84c308ec9059d398b1ccbfb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365067"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Utöka Azure Database for MySQL lagring automatiskt med hjälp av Azure CLI
I den här artikeln beskrivs hur du kan konfigurera en Azure Database for MySQL Server lagring så att den växer utan att arbets belastningen påverkas.

Servern som [når lagrings gränsen](./concepts-pricing-tiers.md#reaching-the-storage-limit)är skrivskyddad. Om automatisk storleks ökning har Aktiver ATS för servrar med mindre än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5 GB så snart det lediga lagrings utrymmet är lägre än 1 GB eller 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är under 10 GB för den allokerade lagrings storleken. De maximala lagrings gränser som anges [här](./concepts-pricing-tiers.md#storage) gäller.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden:

- Du behöver en [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="enable-mysql-server-storage-auto-grow"></a>Aktivera automatisk tillväxt i MySQL server Storage

Aktivera server för automatisk storleks ökning på en befintlig server med följande kommando:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Aktivera automatisk storleks ökning av servern när du skapar en ny server med följande kommando:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [hur du skapar aviseringar för mått](howto-alert-on-metric.md).
