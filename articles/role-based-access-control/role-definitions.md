---
title: Förstå roll definitioner i Azure – Azure RBAC
description: Lär dig mer om definitioner av Azure-roller i rollbaserad åtkomst kontroll i Azure (Azure RBAC) för detaljerade åtkomst hantering av Azure-resurser.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: bc3640fecbe1138e46fd0d36975691740bc669dd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369267"
---
# <a name="understand-azure-role-definitions"></a>Förstå roll definitioner för Azure

Om du försöker förstå hur en Azure-roll fungerar eller om du skapar en egen Azure- [anpassad roll](custom-roles.md), är det bra att förstå hur roller definieras. I den här artikeln beskrivs information om roll definitioner och några exempel.

## <a name="role-definition"></a>Rolldefinition

En *rolldefinition* är en samling behörigheter. Den kallas ibland bara för en *roll*. En rolldefinition listar de åtgärder som kan utföras, till exempel läsa, skriva och ta bort. Den kan också visa en lista över de åtgärder som undantas från tillåtna åtgärder eller åtgärder som rör underliggande data.

Följande visar ett exempel på egenskaperna i en roll definition när du visas med hjälp av Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Följande visar ett exempel på egenskaperna i en roll definition när de visas med hjälp av Azure Portal, Azure CLI eller REST API:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

I följande tabell beskrivs vad roll egenskaperna innebär.

| Egenskap | Beskrivning |
| --- | --- |
| `Name`</br>`roleName` | Visnings namnet för rollen. |
| `Id`</br>`name` | Rollens unika ID. |
| `IsCustom`</br>`roleType` | Anger om det här är en anpassad roll. Ange till `true` eller `CustomRole` för anpassade roller. Ange till `false` eller `BuiltInRole` för inbyggda roller. |
| `Description`</br>`description` | Rollens beskrivning. |
| `Actions`</br>`actions` | En sträng mat ris som anger vilka hanterings åtgärder som rollen kan utföra. |
| `NotActions`</br>`notActions` | En sträng mat ris som anger vilka hanterings åtgärder som undantas från tillåten `Actions` . |
| `DataActions`</br>`dataActions` | En sträng mat ris som anger de data åtgärder som rollen kan utföra på dina data i objektet. |
| `NotDataActions`</br>`notDataActions` | En sträng mat ris som anger de data åtgärder som undantas från tillåten `DataActions` . |
| `AssignableScopes`</br>`assignableScopes` | En sträng mat ris som anger de omfång som rollen är tillgänglig för tilldelning. |

### <a name="operations-format"></a>Åtgärds format

Åtgärder anges med strängar som har följande format:

- `{Company}.{ProviderName}/{resourceType}/{action}`

`{action}`Delen av en åtgärds sträng anger vilken typ av åtgärder som du kan utföra på en resurs typ. Till exempel visas följande del strängar i `{action}` :

| Åtgärds del sträng    | Beskrivning         |
| ------------------- | ------------------- |
| `*` | Jokertecknet beviljar åtkomst till alla åtgärder som matchar strängen. |
| `read` | Aktiverar Läs åtgärder (GET). |
| `write` | Aktiverar Skriv åtgärder (placering eller korrigering). |
| `action` | Aktiverar anpassade åtgärder som starta om virtuella datorer (POST). |
| `delete` | Aktiverar borttagnings åtgärder (ta bort). |

### <a name="role-definition-example"></a>Exempel på roll definition

Här är roll definitionen [deltagare](built-in-roles.md#contributor) som visas i Azure PowerShell och Azure CLI. Jokertecknet (`*`) under `Actions` anger att huvudnamnet som tilldelats den här rollen kan utföra alla åtgärder, eller med andra ord sköta all administration. Det här inbegriper även åtgärder som definieras i framtiden när Azure lägger till nya resurstyper. Åtgärderna under `NotActions` dras bort från `Actions`. När det gäller [deltagar](built-in-roles.md#contributor) rollen `NotActions` tar bort rollens möjlighet att hantera åtkomst till resurser och även hantera Azure Blueprint tilldelningar.

Deltagar rollen som visas i Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Deltagar rollen som visas i Azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Hanterings-och data åtgärder

Rollbaserad åtkomst kontroll för hanterings åtgärder anges i-och- `Actions` `NotActions` egenskaperna för en roll definition. Här följer några exempel på hanterings åtgärder i Azure:

- Hantera åtkomst till ett lagrings konto
- Skapa, uppdatera eller ta bort en BLOB-behållare
- Ta bort en resurs grupp och alla dess resurser

Hanterings åtkomst ärvs inte till dina data förutsatt att autentiseringsmetoden för behållar metoden är inställd på "Azure AD-användarkonto" och inte "åtkomst nyckel". Denna separation förhindrar att roller med jokertecken ( `*` ) får obegränsad åtkomst till dina data. Om en användare till exempel har en [läsar](built-in-roles.md#reader) roll för en prenumeration kan de Visa lagrings kontot, men som standard kan de inte Visa underliggande data.

Tidigare användes inte rollbaserad åtkomst kontroll för data åtgärder. Auktorisering för data åtgärder som varierar mellan olika resurs leverantörer. Samma rollbaserade auktoriserings modell för åtkomst kontroll som används för hanterings åtgärder har utökats till data åtgärder.

För att stödja data åtgärder har nya data egenskaper lagts till i roll definitionen. Dataåtgärder anges i egenskaperna `DataActions` och `NotDataActions`. Genom att lägga till dessa data egenskaper upprätthålls separationen mellan hantering och data. På så sätt kan du förhindra att rolltilldelningar med jokertecken (`*`) plötsligt får åtkomst till data. Här är några dataåtgärder du kan ange i `DataActions` och `NotDataActions`:

- Läsa en lista med blobar i en container
- Skriva till en lagringsblob i en container
- Ta bort ett meddelande i en kö

Här är roll definitionen [Storage BLOB data Reader](built-in-roles.md#storage-blob-data-reader) , som innehåller åtgärder i `Actions` `DataActions` egenskaperna och. Med den här rollen kan du läsa BLOB-behållaren och även underliggande BLOB-data.

Rollen Storage BLOB data Reader som visas i Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rollen Storage BLOB data Reader som visas i Azure CLI:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Du kan bara lägga till dataåtgärder i egenskaperna `DataActions` och `NotDataActions`. Resurs leverantörer identifierar vilka åtgärder som är data åtgärder genom att ange `isDataAction` egenskapen till `true` . Om du vill se en lista över de åtgärder som finns `isDataAction` `true` , se [Resource Provider-åtgärder](resource-provider-operations.md). Roller som inte har data åtgärder krävs inte för att ha `DataActions` och `NotDataActions` Egenskaper i roll definitionen.

Auktorisering för alla API-anrop för hanterings åtgärder hanteras av Azure Resource Manager. Auktorisering för API-anrop för data åtgärder hanteras av antingen en resurs leverantör eller Azure Resource Manager.

### <a name="data-operations-example"></a>Exempel på data åtgärder

Vi rekommenderar ett särskilt exempel för att bättre förstå hur hanterings-och data åtgärder fungerar. Alice har tilldelats [ägar](built-in-roles.md#owner) rollen i prenumerations omfånget. Bob har tilldelats rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) i ett lagrings konto omfång. Följande diagram visar det här exemplet.

![Rollbaserad åtkomst kontroll har utökats för att stödja både hanterings-och data åtgärder](./media/role-definitions/rbac-management-data.png)

[Ägar](built-in-roles.md#owner) rollen för Alice och rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) för Bob har följande åtgärder:

Ägare

&nbsp;&nbsp;&nbsp;&nbsp;Action<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage Blob Data-deltagare

&nbsp;&nbsp;&nbsp;&nbsp;Action<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Eftersom Alice har en jokertecken ( `*` )-åtgärd i ett prenumerations omfång, ärver deras behörigheter nedåt för att de ska kunna utföra alla hanterings åtgärder. Alice kan läsa, skriva och ta bort behållare. Alice kan dock inte utföra data åtgärder utan att vidta ytterligare åtgärder. Som standard kan Alice till exempel inte läsa blobarna i en behållare. För att läsa blobarna måste Alice Hämta lagrings åtkomst nycklar och använda dem för att få åtkomst till Blobbarna.

Bobs behörigheter begränsas till bara `Actions` och `DataActions` anges i rollen [Storage BLOB data Contributor](built-in-roles.md#storage-blob-data-contributor) . Med hjälp av rollen kan Bob utföra både hanterings-och data åtgärder. Bob kan till exempel läsa, skriva och ta bort behållare i det angivna lagrings kontot och kan också läsa, skriva och ta bort blobbar.

Mer information om hantering och data Plans säkerhet för lagring finns i [säkerhets guiden för Azure Storage](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Vilka verktyg stöder användning av Azure-roller för data åtgärder?

Om du vill visa och arbeta med data åtgärder måste du ha rätt versioner av verktygen eller SDK: erna:

| Verktyg  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 eller senare |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 eller senare |
| [Azure för .NET](/dotnet/azure/) | 2.8.0 – för hands version eller senare |
| [Azure SDK för Go](/azure/go/azure-sdk-go-install) | 15.0.0 eller senare |
| [Azure för Java](/java/azure/) | 1.9.0 eller senare |
| [Azure för python](/azure/python/) | 0.40.0 eller senare |
| [Azure SDK för Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 eller senare |

Om du vill visa och använda data åtgärder i REST API måste du ange parametern för **API-version** till följande version eller senare:

- 2018-07-01

## <a name="actions"></a>Åtgärder

`Actions`Behörigheten anger vilka hanterings åtgärder som rollen kan utföra. Det är en samling åtgärds strängar som identifierar skydds bara åtgärder hos Azure Resource providers. Här följer några exempel på hanterings åtgärder som kan användas i `Actions` .

> [!div class="mx-tableFixed"]
> | Åtgärds sträng    | Beskrivning         |
> | ------------------- | ------------------- |
> | `*/read` | Ger åtkomst till Läs åtgärder för alla resurs typer för alla Azure Resource providers.|
> | `Microsoft.Compute/*` | Beviljar åtkomst till alla åtgärder för alla resurs typer i Microsoft. Compute Resource-providern.|
> | `Microsoft.Network/*/read` | Ger åtkomst till Läs åtgärder för alla resurs typer i Microsoft. Network Resource-providern.|
> | `Microsoft.Compute/virtualMachines/*` | Ger åtkomst till alla åtgärder för virtuella datorer och dess underordnade resurs typer.|
> | `microsoft.web/sites/restart/Action` | Beviljar åtkomst för att starta om en webbapp.|

## <a name="notactions"></a>NotActions

`NotActions`Behörigheten anger vilka hanterings åtgärder som undantas från tillåten `Actions` . Använd `NotActions` behörigheten om den uppsättning åtgärder som du vill tillåta är enklare att definiera genom att exkludera begränsade åtgärder. Åtkomsten som beviljats av en roll (gällande behörigheter) beräknas genom att subtrahera `NotActions` åtgärderna från `Actions` åtgärderna.

> [!NOTE]
> Om en användare tilldelas en roll som undantar en åtgärd i `NotActions` och tilldelas en andra roll som beviljar åtkomst till samma åtgärd, tillåts användaren utföra åtgärden. `NotActions` är inte en regel för neka – det är bara ett bekvämt sätt att skapa en uppsättning tillåtna åtgärder när vissa åtgärder behöver undantas.
>

## <a name="dataactions"></a>DataActions

`DataActions`Behörigheten anger de data åtgärder som rollen kan utföra för dina data i objektet. Om en användare till exempel har Läs BLOB-dataåtkomst till ett lagrings konto kan de läsa blobarna i det lagrings kontot. Här följer några exempel på data åtgärder som kan användas i `DataActions` .

> [!div class="mx-tableFixed"]
> | Åtgärds sträng    | Beskrivning         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Returnerar en BLOB eller en lista över blobbar. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Returnerar resultatet av att skriva en blob. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Returnerar ett meddelande. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Returnerar ett meddelande eller resultatet av att skriva eller ta bort ett meddelande. |

## <a name="notdataactions"></a>NotDataActions

`NotDataActions`Behörigheten anger de data åtgärder som undantas från tillåten `DataActions` . Åtkomsten som beviljats av en roll (gällande behörigheter) beräknas genom att subtrahera `NotDataActions` åtgärderna från `DataActions` åtgärderna. Varje resurs leverantör tillhandahåller sin respektive uppsättning API: er för att uppfylla data åtgärder.

> [!NOTE]
> Om en användare har tilldelats en roll som undantar en data åtgärd i `NotDataActions` och har tilldelats en andra roll som beviljar åtkomst till samma data åtgärd, tillåts användaren att utföra denna data åtgärd. `NotDataActions` är inte en regel för neka – det är bara ett bekvämt sätt att skapa en uppsättning tillåtna data åtgärder när vissa data åtgärder måste undantas.
>

## <a name="assignablescopes"></a>AssignableScopes

`AssignableScopes`Egenskapen anger de omfattningar (hanterings grupper, prenumerationer eller resurs grupper) som har den här roll definitionen tillgänglig. Du kan bara göra rollen tillgänglig för tilldelning i de hanterings grupper, prenumerationer eller resurs grupper som kräver det. Du måste använda minst en hanterings grupp, prenumeration eller resurs grupp.

Inbyggda roller har `AssignableScopes` angetts till rot omfånget ( `"/"` ). Rot omfånget indikerar att rollen är tillgänglig för tilldelning i alla omfattningar. Exempel på giltiga tilldelnings bara omfång är:

> [!div class="mx-tableFixed"]
> | Rollen är tillgänglig för tilldelning | Exempel |
> |----------|---------|
> | En prenumeration | `"/subscriptions/{subscriptionId1}"` |
> | Två prenumerationer | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Nätverks resurs grupp | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | En hanterings grupp | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Hanterings grupp och en prenumeration | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Alla omfattningar (gäller endast inbyggda roller) | `"/"` |

För information om `AssignableScopes` anpassade roller, se [Azure-anpassade roller](custom-roles.md).

## <a name="next-steps"></a>Nästa steg

* [Inbyggda roller i Azure](built-in-roles.md)
* [Anpassade roller i Azure](custom-roles.md)
* [Azure Resource Provider-åtgärder](resource-provider-operations.md)
