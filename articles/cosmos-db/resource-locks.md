---
title: Förhindra att Azure Cosmos DB resurser tas bort eller ändras
description: Använd Azure Resource Locks för att förhindra att Azure Cosmos DB resurser tas bort eller ändras.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636715"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Förhindra att Azure Cosmos DB resurser tas bort eller ändras
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Som administratör kan du behöva låsa ett Azure Cosmos-konto, en databas eller en behållare för att förhindra att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser. Du kan ange låsnivån till CanNotDelete eller ReadOnly.

- **CanNotDelete** innebär att behöriga användare fortfarande kan läsa och ändra en resurs, men de kan inte ta bort resursen.
- **ReadOnly** innebär att auktoriserade användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Att använda det här låset liknar att begränsa alla behöriga användare till de behörigheter som har beviljats av rollen läsare.

## <a name="how-locks-are-applied"></a>Hur låsen används

När du använder ett lås vid en överordnad omfattning ärver alla resurser inom den omfattningen samma lås. Även resurser som du lägger till senare ärver låset från det överordnade objektet. Det mest restriktiva låset i arv prioriteras.

Till skillnad från rollbaserad åtkomst kontroll i Azure använder du hanterings lås för att tillämpa en begränsning för alla användare och roller. För att lära dig mer om Azure RBAC för Azure Cosmos DB se [rollbaserad åtkomst kontroll i Azure i Azure Cosmos DB](role-based-access-control.md).

Resource Manager-lås gäller endast för åtgärder som sker i hanteringsplanet, som består av åtgärder som skickas till https://management.azure.com. Låsen begränsar inte hur resurser utför sina egna funktioner. Resursändringar är begränsade, men resursåtgärder är inte begränsade. Ett skrivskyddat lås på en Azure Cosmos-behållare förhindrar till exempel att du tar bort eller ändrar behållaren. Det hindrar dig inte från att skapa, uppdatera eller ta bort data i behållaren. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till https://management.azure.com.

## <a name="manage-locks"></a>Hantera lås

> [!WARNING]
> Resurs lås fungerar inte för ändringar som görs av användare som har åtkomst till Azure Cosmos DB med hjälp av konto nycklar om inte Azure Cosmos-kontot först låses genom att aktivera egenskapen disableKeyBasedMetadataWriteAccess. Var försiktig innan du aktiverar den här egenskapen för att se till att den inte bryter samman befintliga program som gör ändringar i resurser med hjälp av SDK, Azure Portal eller verktyg från tredje part som ansluter via konto nycklar och ändrar resurser, till exempel genom att ändra genomflödet, uppdatera index principer osv. Om du vill veta mer och gå igenom en check lista för att se till att programmen fortfarande fungerar, [förhindrar du ändringar från Azure Cosmos DB SDK](role-based-access-control.md#prevent-sdk-changes) : er

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Mall

Använd följande format när du använder ett lås till en Azure Cosmos DB-resurs:

- ändrat `{resourceName}/Microsoft.Authorization/{lockName}`
- bastyp `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> När du ändrar ett befintligt Azure Cosmos-konto ska du se till att ta med de andra egenskaperna för ditt konto och underordnade resurser när du redploying med den här egenskapen. Distribuera inte den här mallen som den är eller så kommer den att återställa alla dina konto egenskaper.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Resource Manager lås](../azure-resource-manager/management/lock-resources.md)
