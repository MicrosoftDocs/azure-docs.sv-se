---
title: Vanliga frågor om Azure Cosmos DB funktionen för återställning av tidpunkt.
description: Den här artikeln innehåller vanliga frågor om den Azure Cosmos DB funktionen för återställning av en viss tidpunkt som uppnås med hjälp av läget för kontinuerlig säkerhets kopiering.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c0af1db12f3ade2945524f48e4539d2d2e9aa6b9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539197"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Vanliga frågor och svar om den Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller vanliga frågor och svar om Azure Cosmos DB tidpunkt för återställning (för hands version) som uppnås med hjälp av läget för kontinuerlig säkerhets kopiering.

## <a name="how-much-time-does-it-takes-to-restore"></a>Hur lång tid tar det att återställa?
Återställnings tiden är beroende av storleken på dina data.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Kan jag skicka in återställnings tiden i lokal tid?
Återställningen kanske inte sker beroende på om de viktiga resurserna som databaser eller behållare fanns vid den tidpunkten. Du kan kontrol lera genom att ange tiden och titta på den valda databasen eller behållaren under en viss tid. Om det inte finns några resurser att återställa, fungerar inte återställnings processen.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Hur kan jag spåra om ett konto återställs?
När du har skickat kommandot Restore och väntar på samma sida när åtgärden har slutförts, visar statusfältet ett fullständigt återställt konto meddelande. Du kan också söka efter det återställda kontot och [spåra statusen för det konto som återställs](continuous-backup-restore-portal.md#track-restore-status). När återställningen pågår, ändras kontots status till "online" när återställningen är slutförd.

På samma sätt för PowerShell och CLI kan du spåra förloppet för återställnings åtgärden genom att köra `az cosmosdb show` kommandot på följande sätt:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

ProvisioningState visar "lyckades" när kontot är online.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Hur kan jag ta reda på om ett konto har återställts från ett annat konto?
Kör `az cosmosdb show` kommandot, i utdata, kan du se att `createMode` egenskapens värde. Om värdet är inställt på **Återställ**. Det anger att kontot har återställts från ett annat konto. `restoreParameters`Egenskapen innehåller ytterligare information, till exempel `restoreSource` , som har käll konto-ID: t. Det sista GUID i `restoreSource` parametern är InstanceID för käll kontot.

I följande utdata är till exempel käll kontots instans-ID "7b4bb-f6a0-430E-ade1-638d781830cc"

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Vad händer när jag återställer en delad data flödes databas eller en behållare i en delad data flödes databas?
Hela den delade data flödes databasen återställs. Du kan inte välja en delmängd av behållare i en delad data flödes databas för återställning.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Vad är användningen av InstanceID i konto definitionen?
Vid en viss tidpunkt är Azure Cosmos DB kontots accountName-egenskap globalt unikt medan den är aktiv. När kontot har tagits bort är det dock möjligt att skapa ett annat konto med samma namn, och därför är "accountName" inte längre tillräckligt för att identifiera en instans av ett konto. 

ID eller "instanceId" är en egenskap hos en instans av ett konto och det används för att disambiguate över flera konton (Live och Deleted) om de har samma namn för återställning. Du kan hämta instans-ID genom att köra `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` kommandona eller. Värdet för namnattributet anger "InstanceID".

## <a name="next-steps"></a>Nästa steg

* Vad är [kontinuerligt säkerhets kopierings](continuous-backup-restore-introduction.md) läge?
* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)