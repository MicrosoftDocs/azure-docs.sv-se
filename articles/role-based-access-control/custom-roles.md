---
title: Anpassade Azure-roller – Azure RBAC
description: Lär dig hur du skapar anpassade Azure-roller med rollbaserad åtkomst kontroll i Azure (Azure RBAC) för detaljerade åtkomst hantering av Azure-resurser.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 79aaeee942a6d46243ee1c72d5904484b8698ebe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617331"
---
# <a name="azure-custom-roles"></a>Anpassade roller i Azure

> [!IMPORTANT]
> Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna anpassade roller. Precis som inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänstens huvud namn i hanterings grupp, prenumeration och resurs grupps omfång.

Anpassade roller kan delas mellan prenumerationer som har förtroende för samma Azure AD-katalog. Det finns en gräns på **5 000** anpassade roller per katalog. (För Azure Germany och Azure Kina är gränsen 2 000 anpassade roller.) Du kan skapa anpassade roller med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API.

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

Här är de grundläggande stegen för att skapa en anpassad roll.

1. Bestäm vilka behörigheter du behöver.

    När du skapar en anpassad roll måste du veta vilka åtgärder som är tillgängliga för att definiera dina behörigheter. Normalt börjar du med en befintlig inbyggd roll och ändrar den efter dina behov. Du kommer att lägga till åtgärder i `Actions` `NotActions` [roll definitions](role-definitions.md)-eller-egenskaperna. Om du har data åtgärder kommer du att lägga till dem i `DataActions` `NotDataActions` egenskaperna eller.

    Mer information finns i nästa avsnitt hur du [avgör vilka behörigheter du behöver](#how-to-determine-the-permissions-you-need).

1. Bestäm hur du vill skapa den anpassade rollen.

    Du kan skapa anpassade roller med hjälp av [Azure Portal](custom-roles-portal.md), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)eller [REST API](custom-roles-rest.md).

1. Skapa den anpassade rollen.

    Det enklaste sättet är att använda Azure Portal. Anvisningar för hur du skapar en anpassad roll med hjälp av Azure Portal finns i [skapa eller uppdatera Azure-anpassade roller med hjälp av Azure Portal](custom-roles-portal.md).

1. Testa den anpassade rollen.

    När du har en anpassad roll måste du testa den för att kontrol lera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

## <a name="how-to-determine-the-permissions-you-need"></a>Hur du avgör vilka behörigheter du behöver

Azure har tusentals behörigheter som du kan inkludera i din anpassade roll. Här följer några metoder som kan hjälpa dig att avgöra vilka behörigheter som du vill lägga till i din anpassade roll:

- Titta på befintliga [inbyggda roller](built-in-roles.md).

    Du kanske vill ändra en befintlig roll eller kombinera behörigheter som används i flera roller.

- Lista de Azure-tjänster som du vill bevilja åtkomst till.

- Bestäm vilka [resurs leverantörer som ska mappas till Azure-tjänsterna](../azure-resource-manager/management/azure-services-resource-providers.md).

    Azure-tjänster exponerar sina funktioner och behörigheter genom [resurs leverantörer](../azure-resource-manager/management/overview.md). Microsoft. Compute Resource-providern tillhandahåller till exempel virtuella dator resurser och Microsoft. Billing Resource Provider tillhandahåller prenumerations-och fakturerings resurser. Att känna till resurs leverantörerna kan hjälpa dig att begränsa och fastställa de behörigheter som du behöver för din anpassade roll.

    När du skapar en anpassad roll med hjälp av Azure Portal kan du också bestämma resurs leverantörerna genom att söka efter nyckelord. Den här Sök funktionen beskrivs i [skapa eller uppdatera Azure-anpassade roller med hjälp av Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Fönstret Lägg till behörigheter med Resource Provider](./media/custom-roles-portal/add-permissions-provider.png)

- Sök efter de behörigheter som du vill inkludera i de [tillgängliga behörigheterna](resource-provider-operations.md) .

    När du skapar en anpassad roll med hjälp av Azure Portal kan du söka efter behörigheter med hjälp av nyckelord. Du kan till exempel söka efter *virtuella datorer* eller *fakturerings* behörigheter. Du kan också hämta alla behörigheter som en CSV-fil och sedan söka i den här filen. Den här Sök funktionen beskrivs i [skapa eller uppdatera Azure-anpassade roller med hjälp av Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Lägg till behörighets lista](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Exempel på anpassade roller

Följande visar hur en anpassad roll ser ut som om den visas med Azure PowerShell i JSON-format. Den här anpassade rollen kan användas för övervakning och omstart av virtuella datorer.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

Följande visar samma anpassade roll som visas med hjälp av Azure CLI.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

När du skapar en anpassad roll visas den i Azure Portal med en orange resurs ikon.

![Ikon för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Egenskaper för anpassad roll

I följande tabell beskrivs vad de anpassade roll egenskaperna innebär.

| Egenskap | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Ja | Sträng | Visnings namnet för den anpassade rollen. Även om en roll definition är en hanterings grupp eller en resurs på en prenumerations nivå kan en roll definition användas i flera prenumerationer som delar samma Azure AD-katalog. Det här visnings namnet måste vara unikt i Azure AD-katalogens omfång. Kan innehålla bokstäver, siffror, blank steg och specialtecken. Maximalt antal tecken är 128. |
| `Id`</br>`name` | Ja | Sträng | Det unika ID: t för den anpassade rollen. För Azure PowerShell och Azure CLI genereras detta ID automatiskt när du skapar en ny roll. |
| `IsCustom`</br>`roleType` | Ja | Sträng | Anger om det här är en anpassad roll. Ange till `true` eller `CustomRole` för anpassade roller. Ange till `false` eller `BuiltInRole` för inbyggda roller. |
| `Description`</br>`description` | Ja | Sträng | Beskrivningen av den anpassade rollen. Kan innehålla bokstäver, siffror, blank steg och specialtecken. Maximalt antal tecken är 1024. |
| `Actions`</br>`actions` | Ja | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som rollen kan utföra. Mer information finns i [åtgärder](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Inga | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som undantas från tillåten `Actions` . Mer information finns i [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Inga | Sträng [] | En sträng mat ris som anger de data åtgärder som rollen kan utföra på dina data i objektet. Om du skapar en anpassad roll med kan `DataActions` den rollen inte tilldelas i hanterings gruppens definitions område. Mer information finns i [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Inga | Sträng [] | En sträng mat ris som anger de data åtgärder som undantas från tillåten `DataActions` . Mer information finns i [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Ja | Sträng [] | En sträng mat ris som anger de omfång som den anpassade rollen är tillgänglig för tilldelning. Du kan bara definiera en hanterings grupp i `AssignableScopes` för en anpassad roll. Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version. Mer information finns i [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Behörigheter för jokertecken

`Actions`, `NotActions` , `DataActions` och `NotDataActions` stöder jokertecken ( `*` ) för att definiera behörigheter. Ett jokertecken ( `*` ) utökar en behörighet till allt som matchar åtgärds strängen som du anger. Anta till exempel att du vill lägga till alla behörigheter som är relaterade till Azure Cost Management och export. Du kan lägga till alla dessa åtgärds strängar:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

I stället för att lägga till alla dessa strängar kan du bara lägga till en jokertecken sträng. Till exempel motsvarar följande jokertecken samma sträng som de föregående fem strängarna. Detta inkluderar även eventuella framtida export behörigheter som kan läggas till.

```
Microsoft.CostManagement/exports/*
```

Du kan också ha flera jokertecken i en sträng. Följande sträng representerar till exempel alla frågeinställningar för Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Vem kan skapa, ta bort, uppdatera eller Visa en anpassad roll

Precis som inbyggda roller `AssignableScopes` anger egenskapen de omfattningar som rollen är tillgänglig för tilldelning. `AssignableScopes`Egenskapen för en anpassad roll styr också vem som kan skapa, ta bort, uppdatera eller Visa den anpassade rollen.

| Uppgift | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som beviljas den här åtgärden på hela den `AssignableScopes` anpassade rollen kan skapa (eller ta bort) anpassade roller för användning i dessa omfång. Till exempel [ägare](built-in-roles.md#owner) och [användar åtkomst administratörer](built-in-roles.md#user-access-administrator) för hanterings grupper, prenumerationer och resurs grupper. |
| Uppdatera en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som har tilldelats den här åtgärden för `AssignableScopes` en anpassad roll kan uppdatera anpassade roller i dessa omfång. Till exempel [ägare](built-in-roles.md#owner) och [användar åtkomst administratörer](built-in-roles.md#user-access-administrator) för hanterings grupper, prenumerationer och resurs grupper. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinitions/read` | Användare som har tilldelats den här åtgärden i ett omfång kan visa de anpassade roller som är tillgängliga för tilldelning i det aktuella omfånget. Alla inbyggda roller tillåter att anpassade roller är tillgängliga för tilldelning. |

## <a name="custom-role-limits"></a>Anpassade roll gränser

I följande lista beskrivs gränserna för anpassade roller.

- Varje katalog kan ha upp till **5000** anpassade roller.
- Azure Germany och Azure Kina 21Vianet kan ha upp till 2000 anpassade roller för varje katalog.
- Du kan inte ange `AssignableScopes` rot omfånget ( `"/"` ).
- Du kan inte använda jokertecken ( `*` ) i `AssignableScopes` . Den här begränsningen för jokertecken hjälper till att se till att en användare inte kan få åtkomst till ett omfång genom att uppdatera roll definitionen.
- Du kan bara definiera en hanterings grupp i `AssignableScopes` för en anpassad roll. Att lägga till en hanterings grupp i `AssignableScopes` är för närvarande en för hands version.
- `DataActions`Det går inte att tilldela anpassade roller med hanterings grupps omfånget.
- Azure Resource Manager validerar inte hanterings gruppens existens i roll definitionens tilldelnings omfång.

Mer information om anpassade roller och hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Format för indata och utdata

Om du vill skapa en anpassad roll med hjälp av kommando raden använder du vanligt vis JSON för att ange de egenskaper som du vill använda för den anpassade rollen. Beroende på vilka verktyg du använder ser indata och utdata lite annorlunda ut. I det här avsnittet visas in-och utdata-format beroende på verktyget.

### <a name="azure-powershell"></a>Azure PowerShell

Om du vill skapa en anpassad roll med hjälp av Azure PowerShell måste du ange följande ingångs värde.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Om du vill uppdatera en anpassad roll med hjälp av Azure PowerShell måste du ange följande ingångar. Observera att `Id` egenskapen har lagts till. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Följande visar ett exempel på utdata när du listar en anpassad roll med hjälp av Azure PowerShell och [ConvertTo-JSON-](/powershell/module/microsoft.powershell.utility/convertto-json) kommandot. 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Om du vill skapa eller uppdatera en anpassad roll med Azure CLI måste du ange följande ininformation. Det här formatet har samma format när du skapar en anpassad roll med hjälp av Azure PowerShell.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Följande visar ett exempel på utdata när du listar en anpassad roll med hjälp av Azure CLI.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST-API

Om du vill skapa eller uppdatera en anpassad roll med hjälp av REST API måste du ange följande ininformation. Det här formatet har samma format som genereras när du skapar en anpassad roll med hjälp av Azure Portal.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Följande visar ett exempel på utdata när du listar en anpassad roll med hjälp av REST API.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skapa en anpassad Azure-roll med hjälp av Azure PowerShell](tutorial-custom-role-powershell.md)
- [Självstudie: skapa en anpassad Azure-roll med hjälp av Azure CLI](tutorial-custom-role-cli.md)
- [Förstå roll definitioner för Azure](role-definitions.md)
- [Felsöka Azure RBAC](troubleshooting.md)
