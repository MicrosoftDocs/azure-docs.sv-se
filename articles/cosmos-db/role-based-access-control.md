---
title: Rollbaserad åtkomst kontroll i Azure i Azure Cosmos DB
description: Lär dig hur Azure Cosmos DB skyddar databas skydd med Active Directory-integrering (Azure RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: 3a0f0f617e45834f1f205bcb4c8bcbc884b20d22
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048122"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Rollbaserad åtkomst kontroll i Azure i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Den här artikeln är en rollbaserad åtkomst kontroll för hanterings plan åtgärder i Azure Cosmos DB. Om du använder data Plans åtgärder, se [Azure Cosmos DB RBAC](how-to-setup-rbac.md) för rollbaserad åtkomst kontroll som används för dina data Plans åtgärder.

Azure Cosmos DB tillhandahåller inbyggd rollbaserad åtkomst kontroll i Azure (Azure RBAC) för vanliga hanterings scenarier i Azure Cosmos DB. En person som har en profil i Azure Active Directory kan tilldela dessa Azure-roller till användare, grupper, tjänstens huvud namn eller hanterade identiteter för att bevilja eller neka åtkomst till resurser och åtgärder på Azure Cosmos DB resurser. Roll tilldelningar är begränsade till kontroll – endast plan åtkomst, vilket omfattar åtkomst till Azure Cosmos-konton, databaser, behållare och erbjudanden (data flöde).

## <a name="built-in-roles"></a>Inbyggda roller

Följande är de inbyggda roller som stöds av Azure Cosmos DB:

|**Inbyggd roll**  |**Beskrivning**  |
|---------|---------|
|[DocumentDB-konto deltagare](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Kan hantera Azure Cosmos DB-konton.|
|[Cosmos DB konto läsare](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Kan läsa Azure Cosmos DB konto data.|
|[Ansvarig för Cosmos-säkerhetskopiering](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| Kan skicka en Restore-begäran för Azure Portal för en regelbunden säkerhets kopierings aktive rad databas eller en behållare. Kan ändra intervallet för säkerhets kopiering och kvarhållning av Azure Portal. Det går inte att komma åt data eller använda Datautforskaren.  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | Det går att utföra återställnings åtgärden för Azure Cosmos DB konto med kontinuerlig säkerhets kopierings läge.|
|[Cosmos DB operatör](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Kan etablera Azure Cosmos-konton, databaser och behållare. Det går inte att komma åt data eller använda Datautforskaren.|

> [!IMPORTANT]
> Azure RBAC-stöd i Azure Cosmos DB gäller endast för kontroll Plans åtgärder. Data Plans åtgärder skyddas med hjälp av primära nycklar eller resurs-token. Mer information finns i [säker åtkomst till data i Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Identitets-och åtkomst hantering (IAM)

Fönstret **åtkomst kontroll (IAM)** i Azure Portal används för att konfigurera rollbaserad åtkomst kontroll i Azure Cosmos-resurser. Rollerna tillämpas på användare, grupper, tjänstens huvud namn och hanterade identiteter i Active Directory. Du kan använda inbyggda roller eller anpassade roller för enskilda användare och grupper. Följande skärm bild visar Active Directory integration (Azure RBAC) med åtkomst kontroll (IAM) i Azure Portal:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Åtkomst kontroll (IAM) i Azure Portal-demonstrera databas säkerhet.":::

## <a name="custom-roles"></a>Anpassade roller

Förutom de inbyggda rollerna kan användare också skapa [anpassade roller](../role-based-access-control/custom-roles.md) i Azure och tillämpa dessa roller för tjänstens huvud namn i alla prenumerationer inom sin Active Directory-klient. Anpassade roller ger användarna möjlighet att skapa Azure-roll definitioner med en anpassad uppsättning av resurs leverantörs åtgärder. Om du vill veta vilka åtgärder som är tillgängliga för att skapa anpassade roller för Azure Cosmos DB, se [Azure Cosmos DB Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

> [!TIP]
> Anpassade roller som behöver komma åt data som lagras i Cosmos DB eller använda Datautforskaren i Azure Portal måste ha `Microsoft.DocumentDB/databaseAccounts/listKeys/*` åtgärd.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Förhindra ändringar från Azure Cosmos DB SDK: er

Azure Cosmos DB Resource Provider kan låsas för att förhindra ändringar av resurser från en klient som ansluter med hjälp av konto nycklarna (det vill säga program som ansluter via Azure Cosmos SDK). Detta omfattar även ändringar som gjorts från Azure Portal. Den här funktionen kan vara önskvärd för användare som vill ha högre kontroll och styrning för produktions miljöer. Om du förhindrar ändringar från SDK: n, aktive ras även funktioner som resurs lås och diagnostikloggar för kontroll Plans åtgärder. Klienter som ansluter från Azure Cosmos DB SDK kommer inte att kunna ändra någon egenskap för Azure Cosmos-konton, databaser, behållare och data flöde. De åtgärder som rör läsning och skrivning av data till Cosmos-behållare påverkas inte.

När den här funktionen är aktive rad kan ändringar i alla resurser endast göras från en användare med rätt Azure-roll och Azure Active Directory autentiseringsuppgifter, inklusive hanterade tjänst identiteter.

> [!WARNING]
> Aktivering av den här funktionen kan påverka ditt program. Se till att du förstår påverkan innan du aktiverar den.

### <a name="check-list-before-enabling"></a>Kontrol lera listan innan du aktiverar

Den här inställningen förhindrar eventuella ändringar av Cosmos-resurser från klienter som ansluter med hjälp av konto nycklar, inklusive Cosmos DB SDK, verktyg som ansluter via konto nycklar eller från Azure Portal. För att förhindra problem eller fel från program när du har aktiverat den här funktionen kontrollerar du om program eller Azure Portal användare utför någon av följande åtgärder innan du aktiverar den här funktionen, inklusive:

- Alla ändringar i Cosmos-kontot, inklusive eventuella egenskaper eller att lägga till eller ta bort regioner.

- Skapa, ta bort underordnade resurser, till exempel databaser och behållare. Detta inkluderar resurser för andra API: er som Cassandra, MongoDB, Gremlin och table-resurser.

- Uppdaterar data flödet på databas-eller container nivå resurser.

- Ändra behållar egenskaper, inklusive index princip, TTL och unika nycklar.

- Ändra lagrade procedurer, utlösare eller användardefinierade funktioner.

Om dina program (eller användare via Azure Portal) utför någon av dessa åtgärder måste de migreras för att köras via [arm-mallar](./manage-with-templates.md), [POWERSHELL](manage-with-powershell.md), [Azure CLI](manage-with-cli.md), rest eller [Azures hanterings bibliotek](https://github.com/Azure-Samples/cosmos-management-net). Observera att Azure-hantering är tillgängligt på [flera språk](/azure/?product=developer-tools#languages-and-tools).

### <a name="set-via-arm-template"></a>Ställ in via ARM-mall

Om du vill ange den här egenskapen med en ARM-mall uppdaterar du din befintliga mall eller exporterar en ny mall för den aktuella distributionen och inkluderar sedan `"disableKeyBasedMetadataWriteAccess": true` egenskaperna för `databaseAccounts` resurserna. Nedan visas ett grundläggande exempel på en Azure Resource Manager-mall med den här egenskaps inställningen.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Se till att du inkluderar de andra egenskaperna för ditt konto och underordnade resurser när du redploying med den här egenskapen. Distribuera inte den här mallen som den är eller så kommer den att återställa alla dina konto egenskaper.

### <a name="set-via-azure-cli"></a>Ange via Azure CLI

Om du vill aktivera med hjälp av Azure CLI använder du kommandot nedan:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Ange via PowerShell

Om du vill aktivera med Azure PowerShell använder du kommandot nedan:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md)
- [Anpassade roller i Azure](../role-based-access-control/custom-roles.md)
- [Åtgärder för Azure Cosmos DB Resource Provider](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
