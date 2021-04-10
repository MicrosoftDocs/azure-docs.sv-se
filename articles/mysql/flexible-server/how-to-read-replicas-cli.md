---
title: Hantera Läs repliker i Azure Database for MySQL flexibel server med Azure CLI.
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for MySQL flexibel server med hjälp av Azure CLI.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: bc95cd3ab471826538a551687c38d1422e4b7163
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108663"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Skapa och hantera Läs repliker i Azure Database for MySQL flexibel server med hjälp av Azure CLI

> [!IMPORTANT]
> Läs repliker i Azure Database for MySQL-flexibel Server är i för hands version.

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i den Azure Database for MySQL flexibla servern med hjälp av Azure CLI. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).

> [!Note]
> Repliken stöds inte på en server med hög tillgänglighet. 

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera Läs repliker med hjälp av Azure CLI.

### <a name="prerequisites"></a>Förutsättningar

- [Installera Azure CLI 2.0](/cli/azure/install-azure-cli)
- En [Azure Database for MySQL flexibel Server](quickstart-create-server-cli.md) som ska användas som käll Server.

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

> [!IMPORTANT]
> När du skapar en replik för en källa som inte har några befintliga repliker startas källan om först för att förbereda sig för replikering. Ta detta i beaktande och utför dessa åtgärder under en låg belastnings period.

Du kan skapa en Läs replik server med följande kommando:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som källan. Replik Server konfigurationen kan ändras efter att den har skapats. Replik servern skapas alltid i samma resurs grupp, samma plats och samma prenumeration som käll servern. Om du vill skapa en replik server till en annan resurs grupp eller en annan prenumeration kan du [Flytta replik servern](../../azure-resource-manager/management/move-resource-group-and-subscription.md) när du har skapat den. Vi rekommenderar att replik serverns konfiguration ska hållas på lika stora eller större värden än källan för att säkerställa att repliken kan fortsätta med källan.


### <a name="list-replicas-for-a-source-server"></a>Lista repliker för en käll Server

Om du vill visa alla repliker för en specifik käll server kör du följande kommando: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server

> [!IMPORTANT]
> Att stoppa replikeringen till en server går inte att ångra. När replikeringen har stoppats mellan en källa och replik kan den inte återställas. Replik servern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Replikering till en Läs replik Server kan stoppas med hjälp av följande kommando:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Ta bort en replik Server

Du kan ta bort en Läs replik Server genom att köra kommandot **[AZ MySQL server Delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Ta bort en käll Server

> [!IMPORTANT]
> Om du tar bort en källserver stoppas replikeringen till alla replikservrar och själva källservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Om du vill ta bort en käll Server kan du köra kommandot **[AZ MySQL Flexible-Server Delete](/cli/azure/mysql/flexible-server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Läs repliker](concepts-read-replicas.md)