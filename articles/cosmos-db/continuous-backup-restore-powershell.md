---
title: Använd Azure PowerShell för att konfigurera kontinuerlig säkerhets kopiering och återställning av tidpunkt i Azure Cosmos DB.
description: Lär dig hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure PowerShell.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381842"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Konfigurera och hantera kontinuerlig säkerhets kopiering och tidpunkts återställning (för hands version) – med Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version) hjälper dig att återställa från en oavsiktlig ändring i en behållare, för att återställa ett borttaget konto, en databas eller en behållare eller för att återställa till en region (där säkerhets kopior fanns). Med läget för kontinuerlig säkerhets kopiering kan du återställa till en viss tidpunkt inom de senaste 30 dagarna.

Den här artikeln beskriver hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure PowerShell.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Installera Azure PowerShell

1. Kör följande kommando från Azure PowerShell för att installera `Az.CosmosDB` Preview-modulen, som innehåller kommandon relaterade till återställning av tidpunkt:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. När du har installerat modulerna loggar du in på Azure med

   ```azurepowershell
   Connect-AzAccount
   ```

1. Välj en speciell prenumeration med följande kommando:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Etablera ett SQL API-konto med kontinuerlig säkerhets kopiering

Om du vill etablera ett konto med kontinuerlig säkerhets kopiering lägger du till ett argument `-BackupPolicyType Continuous` tillsammans med det vanliga etablerings kommandot.

Följande cmdlet är ett exempel på ett enda region Skriv konto `pitracct2` med kontinuerlig säkerhets kopierings princip som skapats i regionen *västra USA* under *myrg* resurs grupp:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Etablera ett MongoDB API-konto med kontinuerlig säkerhets kopiering

Följande cmdlet är ett exempel på ett kontinuerligt säkerhets kopierings konto *pitracct2* som skapats i regionen *västra USA* under *myrg* -resurs grupp:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Utlös en återställnings åtgärd

Följande cmdlet är ett exempel på att utlösa en återställnings åtgärd med kommandot Restore genom att använda mål kontot, käll kontot, platsen, resurs gruppen och tidsstämpeln:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Exempel 1:** Hela kontot återställs:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Exempel 2:** Återställer vissa samlingar och databaser. I det här exemplet återställs samlingarna myCol1, myCol2 från myDB1 och hela databasen myDB2, som innehåller alla behållare under den.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Räkna upp återställas-resurser för SQL API

Med uppräknings-cmdlet: arna kan du identifiera vilka resurser som är tillgängliga för återställning vid olika tidsstämplar. Dessutom ger de också en feed av viktiga händelser för återställas-kontot,-databasen och container resurserna.

**Lista alla konton som kan återställas i den aktuella prenumerationen**

Kör `Get-AzCosmosDBRestorableDatabaseAccount` PowerShell-kommandot för att visa en lista över alla konton som kan återställas i den aktuella prenumerationen.

Svaret innehåller alla databas konton (både Live och borttaget) som kan återställas och de regioner som de kan återställas från.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Precis som `CreationTime` eller `DeletionTime` för kontot finns det `CreationTime` också en eller `DeletionTime` för regionen. Med de här tiderna kan du välja rätt region och ett giltigt tidsintervall för att återställa till den regionen.

**Visa en lista över alla versioner av SQL-databaser i ett Live Database-konto**

Genom att visa alla versioner av databaserna kan du välja rätt databas i ett scenario där den faktiska tiden det finns en okänd databas.

Kör följande PowerShell-kommando för att visa en lista över alla versioner av-databaser. Det här kommandot fungerar bara med Live-konton. `DatabaseAccountInstanceId` `LocationName` Parametrarna och hämtas från-och- `name` `location` egenskaperna i svar på `Get-AzCosmosDBRestorableDatabaseAccount` cmdleten. `DatabaseAccountInstanceId`Attributet refererar till `instanceId` egenskapen för det käll databas konto som återställs:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Visa en lista över alla versioner av SQL-behållare för en databas i ett Live Database-konto.**

Använd följande kommando för att visa en lista över alla versioner av SQL-behållare. Det här kommandot fungerar bara med Live-konton. `DatabaseRid`Parametern är `ResourceId` av den databas som du vill återställa. Det är värdet för `ownerResourceid` attributet som finns i svar på `Get-AzCosmosdbSqlRestorableDatabase` cmdleten. Svaret innehåller också en lista med åtgärder som utförs på alla behållare i den här databasen.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Hitta databaser eller behållare som kan återställas vid en bestämd tidsstämpel**

Använd följande kommando för att hämta en lista över databaser eller behållare som kan återställas vid en bestämd tidsstämpel. Det här kommandot fungerar bara med Live-konton.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Räkna upp återställas-resurser för MongoDB

Uppräknings kommandon som beskrivs nedan hjälper dig att identifiera de resurser som är tillgängliga för återställning vid olika tidsstämplar. Dessutom ger de också en feed av viktiga händelser för återställas-kontot,-databasen och container resurserna. Dessa kommandon fungerar bara för Live-konton och de liknar SQL API-kommandon, men med `MongoDB` i kommando namnet i stället för `sql` .

**Visa en lista över alla versioner av MongoDB-databaser i ett Live Database-konto**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Visa en lista över alla versioner av MongoDB-samlingar i en databas i ett Live Database-konto**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Visa en lista över alla resurser i ett MongoDB Database-konto som är tillgängliga för återställning vid en specifik tidsstämpel och region**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure CLI](continuous-backup-restore-command-line.md), [Resource Manager](continuous-backup-restore-template.md)eller [Azure Portal](continuous-backup-restore-portal.md).
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
