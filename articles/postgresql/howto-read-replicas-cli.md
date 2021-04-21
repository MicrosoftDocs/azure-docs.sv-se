---
title: Hantera skrivskyddade repliker – Azure CLI, REST API – Azure Database for PostgreSQL – enskild server
description: Lär dig hur du hanterar skrivskyddade repliker i Azure Database for PostgreSQL – enskild server från Azure CLI och REST API
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764701"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Skapa och hantera skrivskyddade repliker från Azure CLI, REST API

I den här artikeln får du lära dig hur du skapar och hanterar skrivskyddade repliker i Azure Database for PostgreSQL med hjälp av Azure CLI och REST API. Mer information om skrivskyddade repliker finns i [översikten](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Stöd för Azure-replikering
[Skrivskyddade repliker](concepts-read-replicas.md) [och logisk avkodning](concepts-logical.md) är beroende av Postgres-loggen write ahead (WAL) för information. Dessa två funktioner behöver olika loggningsnivåer från Postgres. Logisk avkodning kräver en högre loggningsnivå än skrivskyddade repliker.

Om du vill konfigurera rätt nivå av loggning använder du azure-replikeringssupportparametern. Stöd för Azure-replikering har tre inställningsalternativ:

* **Av** – placerar minst information i WAL. Den här inställningen är inte tillgänglig på de Azure Database for PostgreSQL servrarna.  
* **Replik** – Mer utförligt än **Av**. Det här är den lägsta loggningsnivå som krävs för [att skrivskyddade repliker](concepts-read-replicas.md) ska fungera. Den här inställningen är standard på de flesta servrar.
* **Logiskt** – Mer utförligt än **Replik**. Det här är den lägsta loggningsnivån för att logisk avkodning ska fungera. Skrivskyddade repliker fungerar också med den här inställningen.


> [!NOTE]
> När du distribuerar skrivskyddade repliker för beständigt tunga skrivintensiva primära arbetsbelastningar kan replikeringsfördröjningen fortsätta att växa och kanske aldrig komma ikapp den primära. Detta kan också öka lagringsanvändningen på den primära eftersom WAL-filerna inte tas bort förrän de tas emot på repliken.

## <a name="azure-cli"></a>Azure CLI
Du kan skapa och hantera skrivskyddade repliker med hjälp av Azure CLI.

### <a name="prerequisites"></a>Förutsättningar

- [Installera Azure CLI 2.0](/cli/azure/install-azure-cli)
- En [Azure Database for PostgreSQL-server](quickstart-create-server-up-azure-cli.md) ska vara den primära servern.


### <a name="prepare-the-primary-server"></a>Förbereda den primära servern

1. Kontrollera den primära serverns `azure.replication_support` värde. Det bör vara minst REPLICA för att skrivskyddade repliker ska fungera.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Om `azure.replication_support` inte är minst REPLICA anger du det. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Starta om servern för att tillämpa ändringen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Kommandot [az postgres server replica create](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) kräver följande parametrar:

| Inställning | Exempelvärde | Description  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Resursgruppen där replikservern ska skapas.  |
| name | mydemoserver-replica | Namnet på den nya replikservern som skapas. |
| source-server | mydemoserver | Namnet eller resurs-ID:t för den befintliga primära servern att replikera från. Använd resurs-ID:t om du vill att repliken och huvudresursgrupperna ska vara olika. |

I CLI-exemplet nedan skapas repliken i samma region som huvudrepliken.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Om du vill skapa en skrivskyddade replik mellan regioner använder du `--location` parametern . CLI-exemplet nedan skapar repliken i USA, västra.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Mer information om vilka regioner du kan skapa en replik i finns i artikeln [om skrivskyddade replikbegrepp.](concepts-read-replicas.md) 

Om du inte har angett parametern till REPLICA på en Generell användning eller minnesoptimerad primär server och startat om `azure.replication_support` servern får du ett felmeddelande.  Slutför dessa två steg innan du skapar en replik.

> [!IMPORTANT]
> Läs avsnittet [överväganden i Read Replica-översikten.](concepts-read-replicas.md#considerations)
>
> Innan en primär serverinställning uppdateras till ett nytt värde uppdaterar du replikinställningen till ett lika med- eller större värde. Den här åtgärden hjälper repliken att hålla reda på alla ändringar som görs i huvudrepliken.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en primär server med kommandot [az postgres server replica list.](/cli/azure/postgres/server/replica#az_postgres_server_replica_list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en primär server och en skrivskyddad replik med hjälp av [kommandot az postgres server replica stop.](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop)

När du har stoppat replikeringen till en primär server och en skrivskyddad replik kan den inte ångras. Skrivskyddad replik blir en fristående server som stöder både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Ta bort en primär server eller replikserver
Om du vill ta bort en primär server eller replikserver använder du [kommandot az postgres server delete.](/cli/azure/postgres/server#az_postgres_server_delete)

När du tar bort en primär server stoppas replikeringen till alla skrivskyddade repliker. Skrivskyddade repliker blir fristående servrar som nu stöder både läsningar och skrivningar.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST-API
Du kan skapa och hantera skrivskyddade repliker med [hjälp av Azure REST API](/rest/api/azure/).

### <a name="prepare-the-primary-server"></a>Förbereda den primära servern

1. Kontrollera den primära serverns `azure.replication_support` värde. Det bör vara minst REPLICA för att skrivskyddade repliker ska fungera.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Om `azure.replication_support` inte är minst REPLICA anger du det.

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
Du kan skapa en skrivskyddad replik med hjälp av [API:et create](/rest/api/postgresql/servers/create):

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
> Mer information om vilka regioner du kan skapa en replik i finns i artikeln [om skrivskyddade replikbegrepp.](concepts-read-replicas.md) 

Om du inte har angett parametern till REPLICA på en Generell användning eller minnesoptimerad primär server och startat om `azure.replication_support` servern får du ett felmeddelande.  Slutför dessa två steg innan du skapar en replik.

En replik skapas med samma beräknings- och lagringsinställningar som huvudnoden. När en replik har skapats kan flera inställningar ändras oberoende av den primära servern: beräkningsgenerering, virtuella kärnor, lagring och kvarhållningsperiod för kopiering. Prisnivån kan också ändras oberoende av varandra, förutom till eller från Basic-nivån.


> [!IMPORTANT]
> Innan en primär serverinställning uppdateras till ett nytt värde uppdaterar du replikinställningen till ett lika med- eller större värde. Den här åtgärden hjälper repliken att hålla reda på alla ändringar som görs i huvudrepliken.

### <a name="list-replicas"></a>Lista repliker
Du kan visa listan över repliker av en primär server med hjälp av API:et [för repliklista:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver
Du kan stoppa replikeringen mellan en primär server och en skrivskyddad replik med hjälp av [uppdaterings-API:et](/rest/api/postgresql/servers/update).

När du har stoppat replikeringen till en primär server och en skrivskyddad replik kan den inte ångras. Den skrivskyddade repliken blir en fristående server som stöder både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

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

### <a name="delete-a-primary-or-replica-server"></a>Ta bort en primär server eller replikserver
Om du vill ta bort en primär server eller replikserver använder du API:et [delete](/rest/api/postgresql/servers/delete):

När du tar bort en primär server stoppas replikeringen till alla skrivskyddade repliker. Skrivskyddade repliker blir fristående servrar som nu stöder både läsningar och skrivningar.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Nästa steg
* Läs mer om [skrivskyddade repliker i Azure Database for PostgreSQL](concepts-read-replicas.md).
* Lär dig hur [du skapar och hanterar skrivskyddade repliker i Azure Portal](howto-read-replicas-portal.md).