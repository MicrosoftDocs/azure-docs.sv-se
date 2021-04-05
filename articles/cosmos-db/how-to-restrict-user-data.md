---
title: Begränsa användar åtkomsten till data åtgärder endast med Azure Cosmos DB
description: Lär dig hur du begränsar åtkomsten till data åtgärder med Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93085997"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Begränsa användar åtkomsten till data åtgärder i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

I Azure Cosmos DB finns det två sätt att autentisera dina interaktioner med databas tjänsten:

- Använd Azure Active Directory identitet när du interagerar med Azure Portal,
- använda Azure Cosmos DB [nycklar](database-security.md#primary-keys) eller [resurs-token](secure-access-to-data.md#resource-tokens) vid utfärdande av anrop från API: er och SDK: er.

Varje autentiseringsmetod ger åtkomst till olika uppsättningar med åtgärder, med vissa överlappningar:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Delning av åtgärder per autentiseringstyp" border="false":::

I vissa fall kanske du vill begränsa vissa användare av organisationen för att utföra data åtgärder (som endast är CRUD-begäranden och frågor). Detta är vanligt vis fallet för utvecklare som inte behöver skapa eller ta bort resurser eller ändra det etablerade data flödet för de behållare som de arbetar med.

Du kan begränsa åtkomsten genom att använda följande steg:
1. Skapa en anpassad Azure Active Directory roll för de användare som du vill begränsa åtkomsten till. Den anpassade Active Directorys rollen ska ha detaljerad åtkomst nivå för åtgärder som använder Azure Cosmos DBs [detaljerade åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb).
1. Otillåten körning av icke-data åtgärder med nycklar. Du kan åstadkomma detta genom att begränsa åtgärderna till endast Azure Resource Manager samtal.

Nästa avsnitt i den här artikeln visar hur du utför de här stegen.

> [!NOTE]
> För att kunna köra kommandona i nästa avsnitt måste du installera Azure PowerShell Module 3.0.0 eller senare, samt [rollen Azure-ägare](../role-based-access-control/built-in-roles.md#owner) för den prenumeration som du försöker ändra.

I PowerShell-skripten i nästa avsnitt ersätter du följande plats hållare med värden som är speciella för din miljö:
- `$MySubscriptionId` – Prenumerations-ID: t som innehåller det Azure Cosmos-konto där du vill begränsa behörigheterna. Exempel: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` – Resurs gruppen som innehåller Azure Cosmos-kontot. Exempel: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` – Namnet på ditt Azure Cosmos-konto. Exempel: `mycosmosdbsaccount`.
- `$MyUserName` -Inloggningen ( username@domain ) för den användare som du vill begränsa åtkomsten för. Exempel: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Välj din Azure-prenumeration

Azure PowerShell-kommandon kräver att du loggar in och väljer den prenumeration som ska köra kommandona:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Skapa rollen för anpassad Azure Active Directory

Följande skript skapar en Azure Active Directory roll tilldelning med "endast nyckel"-åtkomst för Azure Cosmos-konton. Rollen baseras på [Azures anpassade roller](../role-based-access-control/custom-roles.md) och [detaljerade åtgärder för Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Dessa roller och åtgärder är en del av `Microsoft.DocumentDB` Azure Active Directory-namnområdet.

1. Skapa först ett JSON-dokument `AzureCosmosKeyOnlyAccess.json` med namnet med följande innehåll:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Kör följande kommandon för att skapa roll tilldelningen och tilldela den till användaren:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Tillåt inte körning av åtgärder som inte är data

Följande kommandon tar bort möjligheten att använda nycklar för att:
- skapa, ändra eller ta bort resurser
- uppdatera behållar inställningar (inklusive indexerings principer, data flöde osv.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [Cosmos DB rollbaserad åtkomst kontroll](role-based-access-control.md)
- Få en översikt över [säker åtkomst till data i Cosmos DB](secure-access-to-data.md)
