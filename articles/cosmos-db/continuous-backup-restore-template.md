---
title: Använd ARM-mallen för att konfigurera kontinuerlig säkerhets kopiering och tidpunkts återställning i Azure Cosmos DB.
description: Lär dig hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure Resource Manager mallar.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6c388a08a589cc89d83b7178e31e3f4497b924bb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527809"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore---using-azure-resource-manager-templates"></a>Konfigurera och hantera kontinuerlig säkerhets kopiering och tidpunkts återställning – använda Azure Resource Manager mallar
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB funktionen för återställning av en viss tidpunkt hjälper dig att återställa från en oavsiktlig ändring i en behållare, för att återställa ett borttaget konto, en databas eller en behållare eller för att återställa till en region (där säkerhets kopior fanns). Med läget för kontinuerlig säkerhets kopiering kan du återställa till en viss tidpunkt inom de senaste 30 dagarna.

Den här artikeln beskriver hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure Resource Manager mallar.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Etablera ett konto med kontinuerlig säkerhets kopiering

Du kan använda Azure Resource Manager mallar för att distribuera ett Azure Cosmos DB-konto med kontinuerligt läge. När du definierar mallen för att etablera ett konto inkluderar du parametern "backupPolicy" som visas i följande exempel:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

Distribuera sedan mallen med hjälp av Azure PowerShell eller CLI. I följande exempel visas hur du distribuerar mallen med ett CLI-kommando:

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="restore-using-the-resource-manager-template"></a><a id="restore"></a>Återställa med Resource Manager-mallen

Du kan också återställa ett konto med hjälp av Resource Manager-mallen. När du definierar mallen ingår följande parametrar:

* Ange "createMode"-parametern till "Restore"
* Definiera "restoreParameters", Observera att värdet "restoreSource" extraheras från utdata från `az cosmosdb restorable-database-account list` kommandot för ditt käll konto. Attributet instans-ID för ditt konto namn används för att utföra återställningen.
* Ange parametern "restoreMode" till "PointInTime" och konfigurera värdet "restoreTimestampInUtc".

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "vinhpitrarmrestore-kal3",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "databaseAccountOfferType": "Standard",
        "createMode": "Restore",
        "restoreParameters": {
            "restoreSource": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/6a18ecb8-88c2-4005-8dce-07b44b9741df",
            "restoreMode": "PointInTime",
            "restoreTimestampInUtc": "6/24/2020 4:01:48 AM"
        }
      }
    }
  ]
}
```

Distribuera sedan mallen med hjälp av Azure PowerShell eller CLI. I följande exempel visas hur du distribuerar mallen med ett CLI-kommando:  

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <RestoreTemplateFilePath> 
```

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med [Azure CLI](continuous-backup-restore-command-line.md), [PowerShell](continuous-backup-restore-command-line.md)eller [Azure Portal](continuous-backup-restore-portal.md).
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)
* [Hantera behörigheter](continuous-backup-restore-permissions.md) som krävs för att återställa data med kontinuerligt säkerhets kopierings läge.