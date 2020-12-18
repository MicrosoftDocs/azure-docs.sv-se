---
title: Hantera Läs repliker – Azure CLI, REST API-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du hanterar Läs repliker i Azure Database for PostgreSQL-enskild server från Azure CLI och REST API
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e74a58a14bdcc2a6fe1e9f86305aae415c6abf7
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674522"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Skapa och hantera Läs repliker från Azure CLI REST API

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for PostgreSQL med hjälp av Azure CLI och REST API. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Stöd för Azure-replikering
[Läsning av repliker](concepts-read-replicas.md) och [logisk avkodning](concepts-logical.md) är beroende av postgres Write Ahead-loggen (Wal) för information. De här två funktionerna behöver olika loggnings nivåer från postgres. Logisk avkodning kräver en högre loggnings nivå än Läs repliker.

Om du vill konfigurera rätt loggnings nivå använder du parametern Azure Replication support. Support för Azure-replikering har tre inställnings alternativ:

* **Off** – lägger till minst information i Wal. Den här inställningen är inte tillgänglig på de flesta Azure Database for PostgreSQL-servrar.  
* **Replik** – mer utförligt än **.** Detta är den lägsta loggnings nivå som krävs för att [läsa repliker](concepts-read-replicas.md) ska fungera. Den här inställningen är standard på de flesta servrar.
* **Logisk** – mer utförlig än **replik**. Detta är den lägsta loggnings nivån för logisk avkodning att arbeta. Läs repliker fungerar också med den här inställningen.


> [!NOTE]
> När du distribuerar Läs repliker för permanenta tunga Skriv intensiva primär arbets belastningar kan replikeringen fortsätta att växa och kanske aldrig kan fånga upp med den primära. Detta kan också öka lagrings användningen på den primära servern eftersom WAL-filerna inte tas bort förrän de tas emot på repliken.

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera Läs repliker med hjälp av Azure CLI.

### <a name="prerequisites"></a>Krav

- [Installera Azure CLI 2.0](/cli/azure/install-azure-cli)
- En [Azure Database for postgresql-server](quickstart-create-server-up-azure-cli.md) som ska vara den primära servern.


### <a name="prepare-the-primary-server"></a>Förbered den primära servern

1. Kontrol lera värdet för den primära servern `azure.replication_support` . Det måste vara minst replik för att Läs repliker ska fungera.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Om `azure.replication_support` inte är minst replik anger du det. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Starta om servern för att tillämpa ändringen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Kommandot [AZ postgres Server Replica Create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) kräver följande parametrar:

| Inställning | Exempelvärde | Beskrivning  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Resurs gruppen där replik servern kommer att skapas.  |
| name | mydemoserver-replik | Namnet på den nya replik servern som skapas. |
| source-server | mydemoserver | Namnet eller resurs-ID: t för den befintliga primära server som ska replikeras från. Använd resurs-ID om du vill att repliken och originalets resurs grupper ska vara olika. |

I CLI-exemplet nedan skapas repliken i samma region som huvud servern.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Använd parametern om du vill skapa en skrivskyddad replik av en kors region `--location` . CLI-exemplet nedan skapar repliken i USA, västra.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

Om du inte har angett `azure.replication_support` parametern till **replik** på en generell användning eller en minnesoptimerade primär server och startat om servern, visas ett fel meddelande. Utför dessa två steg innan du skapar en replik.

> [!IMPORTANT]
> Läs igenom [avsnittet överväganden i Översikt över läsning av replik](concepts-read-replicas.md#considerations).
>
> Innan en primär server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en primär server genom att använda kommandot [AZ postgres Server Replica List](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server
Du kan stoppa replikeringen mellan en primär server och en Läs replik med hjälp av kommandot [AZ postgres Server Replica Stop](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) .

När du har stoppat replikeringen till en primär server och en Läs replik kan du inte återställa den. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Ta bort en primär server eller replik Server
Om du vill ta bort en primär server eller replik Server använder du kommandot [AZ postgres Server Delete](/cli/azure/postgres/server#az-postgres-server-delete) .

När du tar bort en primär server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST-API
Du kan skapa och hantera Läs repliker med hjälp av [Azure REST API](/rest/api/azure/).

### <a name="prepare-the-primary-server"></a>Förbered den primära servern

1. Kontrol lera värdet för den primära servern `azure.replication_support` . Det måste vara minst replik för att Läs repliker ska fungera.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Om `azure.replication_support` inte är minst replik anger du det.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Starta om servern](/rest/api/postgresql/servers/restart) för att tillämpa ändringen.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik
Du kan skapa en Läs replik med hjälp av [create API](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

Om du inte har angett `azure.replication_support` parametern till **replik** på en generell användning eller en minnesoptimerade primär server och startat om servern, visas ett fel meddelande. Utför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings-och lagrings inställningar som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende av den primära servern: beräknings generering, virtuella kärnor, lagring och säkerhets kopierings perioden. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.


> [!IMPORTANT]
> Innan en primär server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en primär server med hjälp av [replik listans API](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server
Du kan stoppa replikeringen mellan en primär server och en Läs replik med hjälp av [uppdaterings-API: et](/rest/api/postgresql/servers/update).

När du har stoppat replikeringen till en primär server och en Läs replik kan du inte återställa den. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Ta bort en primär server eller replik Server
Om du vill ta bort en primär server eller replik Server använder du [borttagnings-API: et](/rest/api/postgresql/servers/delete):

När du tar bort en primär server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Nästa steg
* Läs mer om att [läsa repliker i Azure Database for PostgreSQL](concepts-read-replicas.md).
* Lär dig hur du [skapar och hanterar Läs repliker i Azure Portal](howto-read-replicas-portal.md).