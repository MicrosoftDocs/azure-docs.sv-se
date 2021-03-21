---
title: 'REST API: synkronisera mellan flera databaser'
description: Använd ett REST API exempel skript för att synkronisera mellan flera databaser.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: ef2823b870f76922dd0dc157341aea9b502fb4bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565547"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Använd REST API för att synkronisera data mellan flera databaser 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

I det här REST API exemplet konfigureras SQL Data Sync för att synkronisera data mellan flera databaser.

En översikt över SQL Data Sync finns i [synkronisera data i flera moln-och lokala databaser med SQL Data Sync i Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL Data Sync stöder inte Azure SQL-hanterad instans för tillfället.

## <a name="create-sync-group"></a>Skapa sync-grupp

Använd mallen [skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate) för att skapa en Sync-grupp.
 
När du skapar en Sync-grupp ska du inte skicka i Sync-schemat (table\column) och inte skicka i masterSyncMemberName, eftersom det inte finns table\column information ännu i den här tidssynkroniserings gruppen.

Exempel förfrågan om att skapa en Sync-grupp: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Exempel svar för att skapa en Sync-grupp:

Status kod: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Status kod: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Skapa synkronisera medlem

Använd mallen [skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate) för att skapa en synkroniserad medlem.

Exempel förfrågan om att skapa en synkroniserad medlem:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Exempel svar för att skapa en synkroniserad medlem:

Status kod: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Status kod: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Uppdatera schema

Uppdatera schemat med följande mallar när synkroniseringsresursen har skapats.

Använd schema mal len [Uppdatera hubb](https://docs.microsoft.com/rest/api/sql/syncgroups/refreshhubschema)  för att uppdatera schemat för Hub-databasen. 

Exempel förfrågan om att uppdatera ett Hubbs databas schema: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Exempel svar för uppdatering av ett nav-databasschemat: 

Status kod: 200

Status kod: 202

Använd mallen för att visa en [lista med nav scheman](https://docs.microsoft.com/rest/api/sql/syncgroups/listhubschemas) för att Visa Hub-databasschemat. 

Använd mallen [Uppdatera medlems schema](https://docs.microsoft.com/rest/api/sql/syncmembers/refreshmemberschema) för att uppdatera medlems databasschemat. 

Använd en [lista över medlems schema](https://docs.microsoft.com/rest/api/sql/syncmembers/listmemberschemas) mal len för att lista medlems databasschemat. 

Fortsätt bara till nästa steg när schemat har uppdaterats. 

## <a name="update-sync-group"></a>Uppdatera Sync-grupp 

Använd mallen [skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/syncgroups/createorupdate) för att uppdatera din Sync-grupp.

Uppdatera Sync-gruppen genom att ange synkroniseringsschemat. Inkludera schemat och masterSyncMemberName, vilket är det namn som innehåller det schema som du vill använda. 

Exempel förfrågan om uppdatering av synkroniseringsresurs: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Exempel svar för uppdatering av Sync-grupp: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Uppdatera synkronisera medlem

Använd mallen [skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/syncmembers/createorupdate) för att uppdatera din synkroniserade medlem.

Exempel förfrågan om uppdatering av en synkroniserad medlem: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Exempel svar för uppdatering av en synkroniserad medlem: 

Status kod: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Status kod: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Utlös synkronisering

Använd [Sync](https://docs.microsoft.com/rest/api/sql/syncgroups/triggersync) -mallen för att utlösa en synkroniseringsåtgärd.

Exempel förfrågan om att utlösa synkroniseringsåtgärden: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Exempel svar för att utlösa synkroniseringsåtgärden: 

Status kod: 200

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../powershell-script-content-guide.md).

Mer information om SQL Data Sync finns i:

- Översikt – [synkronisera data över flera molnbaserade och lokala databaser med SQL Data Sync i Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Konfigurera Data Sync
    - Använd Azure Portal- [självstudie: konfigurera SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server](../sql-data-sync-sql-server-configure.md)
    - Använd PowerShell – [Använd PowerShell för att synkronisera data mellan en databas i Azure SQL Database och SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync-agent – [Data Sync-agent för SQL Data Sync i Azure](../sql-data-sync-agent-overview.md)
- Bästa praxis – [metod tips för SQL Data Sync i Azure](../sql-data-sync-best-practices.md)
- Övervaka [SQL Data Sync med Azure Monitor loggar](../monitor-tune-overview.md)
- Felsök – [Felsök problem med SQL Data Sync i Azure](../sql-data-sync-troubleshoot.md)
- Uppdatera synkroniseringsschemat
    - Använda Transact-SQL – [automatisera replikeringen av schema ändringar i SQL Data Sync i Azure](../sql-data-sync-update-sync-schema.md)
    - Använd PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsresurs](update-sync-schema-in-sync-group.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](../sql-database-paas-overview.md)
- [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
