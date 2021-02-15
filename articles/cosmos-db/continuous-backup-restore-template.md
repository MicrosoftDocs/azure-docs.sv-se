---
title: Använd ARM-mallen för att konfigurera kontinuerlig säkerhets kopiering och tidpunkts återställning i Azure Cosmos DB.
description: Lär dig hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure Resource Manager mallar.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 4abfdd0209bd9f13fb7bd902b27a53f65156da2e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381825"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-resource-manager-templates"></a>Konfigurera och hantera kontinuerlig säkerhets kopiering och tidpunkts återställning (för hands version) – använda Azure Resource Manager mallar
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Funktionen för återställning av tidpunkt (kontinuerlig säkerhets kopiering) för Azure Cosmos DB är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB funktionen för återställning av tidpunkt (för hands version) hjälper dig att återställa från en oavsiktlig ändring i en behållare, för att återställa ett borttaget konto, en databas eller en behållare eller för att återställa till en region (där säkerhets kopior fanns). Med läget för kontinuerlig säkerhets kopiering kan du återställa till en viss tidpunkt inom de senaste 30 dagarna.

Den här artikeln beskriver hur du etablerar ett konto med kontinuerlig säkerhets kopiering och återställning av data med hjälp av Azure Resource Manager mallar.

## <a name="provision-an-account-with-continuous-backup"></a><a id="provision"></a>Etablera ett konto med kontinuerlig säkerhets kopiering

Du kan använda Azure Resource Manager mallar för att distribuera ett Azure Cosmos DB-konto med kontinuerligt läge. När du definierar mallen för att etablera ett konto inkluderar du `backupPolicy` parametern som visas i följande exempel:

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

* Ange `createMode` parametern för att *återställa*
* Definiera `restoreParameters` , Observera att `restoreSource` värdet extraheras från resultatet av `az cosmosdb restorable-database-account list` kommandot för ditt käll konto. Attributet instans-ID för ditt konto namn används för att utföra återställningen.
* Ange `restoreMode` parametern till *PointInTime* och konfigurera `restoreTimestampInUtc` värdet.

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