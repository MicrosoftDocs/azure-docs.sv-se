---
title: Konfigurera behörigheter för att återställa ett Azure Cosmos DB-konto.
description: Lär dig hur du isolerar och begränsar återställnings behörigheter för kontinuerligt säkerhets kopierings konto till en speciell roll eller ett huvud konto. Det visar hur du tilldelar en inbyggd roll med hjälp av Azure Portal, CLI eller definiera en anpassad roll.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 3614a85a6df2e793a73a2609d6f5762e4dc873fb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527865"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Hantera behörigheter för att återställa ett Azure Cosmos DB konto
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Med Azure Cosmos DB kan du isolera och begränsa återställnings behörigheter för kontinuerligt säkerhets kopierings konto till en speciell roll eller ett huvud konto. Kontots ägare kan utlösa en återställning och tilldela en roll till andra huvud konton för att utföra återställnings åtgärden. Dessa behörigheter kan tillämpas i prenumerations omfattningen eller mer detaljerad i käll konto omfånget som visas i följande bild:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lista över roller som krävs för att utföra återställnings åtgärden." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Omfattning är en uppsättning resurser som har åtkomst, Läs mer om omfattningar i [Azure RBAC](../role-based-access-control/scope-overview.md) -dokumentationen. I Azure Cosmos DB är tillämpliga omfattningar käll prenumerationen och databas kontot för de flesta användnings fall. Huvudobjektet som utför återställnings åtgärderna ska ha Skriv behörighet till mål resurs gruppen.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Tilldela roller för återställning med hjälp av Azure Portal

För att utföra en återställning behöver en användare eller ett huvud konto behörighet att återställa (det vill säga "Restore/Action"-behörighet) och behörighet att etablera ett nytt konto (som är "Skriv"-behörighet).  För att bevilja dessa behörigheter kan ägaren tilldela rollen "CosmosRestoreOperator" och "Cosmos DB operatör" som är inbyggd i roller till ett huvud konto.

1. Logga in på [Azure Portal](https://portal.azure.com/)

1. Gå till din prenumeration och gå till fliken **åtkomst kontroll (IAM)** och välj **Lägg till**  >  **Lägg till roll tilldelning**

1. I fönstret **Lägg till roll tilldelning** för **roll** väljer du **CosmosRestoreOperator** roll. Välj **användare, grupp eller ett huvud** namn för tjänsten för fältet **tilldela åtkomst till** och Sök efter en användares namn eller e-post-ID som visas i följande bild:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Tilldela CosmosRestoreOperator-och Cosmos DB-operatörs roller." border="true":::

1. Välj **Spara** för att ge behörigheten "återställning/åtgärd".

1. Upprepa steg 3 med rollen **Cosmos DB operatör** för att bevilja Skriv behörigheten. När du tilldelar den här rollen från Azure Portal, beviljas återställnings behörigheten till hela prenumerationen.

## <a name="permission-scopes"></a>Behörighetsomfattning

|Omfång  |Exempel  |
|---------|---------|
|Prenumeration | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resursgrupp | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Resurs för CosmosDB återställas-konto | /Subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/västra USA/restorableDatabaseAccounts/23e99a35-CD36-4df4-9614-f767a03b9995|

Återställas-konto resursen kan extraheras från utdata från `az cosmosdb restorable-database-account list --name <accountname>` kommandot i CLI eller `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet i PowerShell. Namnattributet i utdata representerar "instanceID" för återställas-kontot. Mer information finns i [PowerShell](continuous-backup-restore-powershell.md) -eller [CLI](continuous-backup-restore-command-line.md) -artikeln.

## <a name="permissions"></a>Behörigheter

Följande behörigheter krävs för att utföra de olika aktiviteterna som är kopplade till återställning för kontinuerliga säkerhets kopierings läges konton:

|Behörighet  |Påverkan  |Minsta omfång  |Maximalt omfång  |
|---------|---------|---------|---------|
|Microsoft. Resources/Deployments/validate/Action, Microsoft. Resources/Deployments/Write | De här behörigheterna krävs för distribution av ARM-mallen för att skapa det återställda kontot. Se exempel behörighets [RestorableAction]() nedan för hur du ställer in den här rollen. | Inte tillämpligt | Inte tillämpligt  |
|Microsoft.DocumentDB/databaseAccounts/Write | Den här behörigheten krävs för att återställa ett konto till en resurs grupp | Resurs grupp som det återställda kontot skapas under. | Den prenumeration som det återställda kontot skapas under |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/Restore/Action |Den här behörigheten krävs för käll återställas databas konto omfång för att tillåta att återställnings åtgärder utförs på den.  | Resursen "RestorableDatabaseAccount" som tillhör det käll konto som återställs. Det här värdet anges också av egenskapen "ID" för databas konto resursen återställas. Ett exempel på ett återställas-konto är `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>` | Prenumerationen som innehåller återställas Database-kontot. Det går inte att välja resurs gruppen som omfång.  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/Read |Den här behörigheten krävs för käll återställas databas konto omfång för att lista de databas konton som kan återställas.  | Resursen "RestorableDatabaseAccount" som tillhör det käll konto som återställs. Det här värdet anges också av egenskapen "ID" för databas konto resursen återställas. Ett exempel på ett återställas-konto är `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| Prenumerationen som innehåller återställas Database-kontot. Det går inte att välja resurs gruppen som omfång.  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/Read | Den här behörigheten krävs för käll återställas för att tillåta läsning av återställas-resurser, till exempel en lista över databaser och behållare för ett återställas-konto.  | Resursen "RestorableDatabaseAccount" som tillhör det käll konto som återställs. Det här värdet anges också av egenskapen "ID" för databas konto resursen återställas. Ett exempel på ett återställas-konto är `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| Prenumerationen som innehåller återställas Database-kontot. Det går inte att välja resurs gruppen som omfång. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Roll tilldelnings scenarier i Azure CLI för återställning i olika omfång

Roller med behörighet kan tilldelas olika omfattningar för att få detaljerad kontroll över vem som kan utföra återställnings åtgärden i en prenumeration eller ett angivet konto.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Tilldela möjlighet att återställa från ett återställas-konto i en prenumeration

Tilldela den `CosmosRestoreOperator` inbyggda rollen på prenumerations nivå

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Tilldela möjlighet att återställa från ett speciellt konto

* Tilldela en användar Skriv åtgärd för den angivna resurs gruppen. Den här åtgärden krävs för att skapa ett nytt konto i resurs gruppen.

* Tilldela den inbyggda rollen "CosmosRestoreOperator" till det angivna återställas Database-kontot som behöver återställas. I följande kommando hämtas omfånget för "RestorableDatabaseAccount" från egenskapen "ID" i utdatan `az cosmosdb restorable-database-account` (om du använder CLI) eller  `Get-AzCosmosDBRestorableDatabaseAccount` (om du använder PowerShell).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Tilldela möjlighet att återställa från alla käll konton i en resurs grupp.
Den här åtgärden stöds inte för närvarande.

## <a name="custom-role-creation-for-restore-action-with-cli"></a>Skapa anpassad roll för återställnings åtgärden med CLI

Prenumerationens ägare kan ge behörighet att återställa till en annan Azure AD-identitet. Återställnings behörigheten baseras på åtgärden: "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/Restore/Action", och den bör tas med i deras återställnings behörighet. Det finns en inbyggd roll med namnet "CosmosRestoreOperator" som har den här rollen inkluderad. Du kan antingen tilldela behörigheten med den här inbyggda rollen eller skapa en anpassad roll.

RestorableAction nedan representerar en anpassad roll. Du måste skapa den här rollen explicit. Följande JSON-mall skapar en anpassad roll, "RestorableAction" med återställnings behörighet:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Använd sedan följande mall för distribution av mallar för att skapa en roll med behörighet att återställa med ARM-mall:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Nästa steg

* Konfigurera och hantera kontinuerlig säkerhets kopiering med hjälp av [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md)eller [Azure Resource Manager](continuous-backup-restore-template.md).
* [Resurs modell för kontinuerligt säkerhets kopierings läge](continuous-backup-restore-resource-model.md)
