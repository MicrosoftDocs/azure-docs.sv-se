---
title: Anpassade Azure-roller – Azure RBAC
description: Lär dig hur du skapar anpassade Azure-roller med rollbaserad åtkomstkontroll i Azure (Azure RBAC) för mer information om åtkomsthantering av Azure-resurser.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 9779c2a269902d856d1639ce78028d0e658656bb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479839"
---
# <a name="azure-custom-roles"></a>Anpassade roller i Azure

> [!IMPORTANT]
> Att lägga till en hanteringsgrupp `AssignableScopes` i är för närvarande i förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om [de inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa egna anpassade roller. Precis som med inbyggda roller kan du tilldela anpassade roller till användare, grupper och tjänsthuvudnamn i hanteringsgrupper (endast förhandsversion), prenumerations- och resursgruppomfång.

Anpassade roller kan delas mellan prenumerationer som litar på samma Azure AD-katalog. Det finns en gräns på **5 000** anpassade roller per katalog. (För Azure Tyskland Azure China 21Vianet är gränsen 2 000 anpassade roller.) Anpassade roller kan skapas med hjälp av Azure Portal, Azure PowerShell, Azure CLI eller REST API.

## <a name="steps-to-create-a-custom-role"></a>Steg för att skapa en anpassad roll

Här är de grundläggande stegen för att skapa en anpassad roll.

1. Fastställ vilka behörigheter du behöver.

    När du skapar en anpassad roll måste du känna till vilka åtgärder som är tillgängliga för att definiera dina behörigheter. Normalt börjar du med en befintlig inbyggd roll och ändrar den sedan efter dina behov. Du lägger till åtgärderna i egenskaperna `Actions` `NotActions` eller för [rolldefinitionen](role-definitions.md). Om du har dataåtgärder lägger du till dem i `DataActions` egenskaperna `NotDataActions` eller .

    Mer information finns i nästa avsnitt Så här [fastställer du de behörigheter du behöver.](#how-to-determine-the-permissions-you-need)

1. Bestäm hur du vill skapa den anpassade rollen.

    Du kan skapa anpassade roller [med Azure Portal](custom-roles-portal.md), [Azure PowerShell,](custom-roles-powershell.md) [Azure CLI](custom-roles-cli.md)eller [REST API](custom-roles-rest.md).

1. Skapa den anpassade rollen.

    Det enklaste sättet är att använda Azure Portal. Anvisningar om hur du skapar en anpassad roll med hjälp av Azure Portal finns i Skapa eller uppdatera [anpassade Azure-roller med hjälp av Azure Portal](custom-roles-portal.md).

1. Testa den anpassade rollen.

    När du har din anpassade roll måste du testa den för att kontrollera att den fungerar som förväntat. Om du behöver göra justeringar senare kan du uppdatera den anpassade rollen.

## <a name="how-to-determine-the-permissions-you-need"></a>Så här fastställer du de behörigheter du behöver

Azure har tusentals behörigheter som du potentiellt kan inkludera i din anpassade roll. Här är några metoder som kan hjälpa dig att avgöra vilka behörigheter du vill lägga till i din anpassade roll:

- Titta på befintliga [inbyggda roller](built-in-roles.md).

    Du kanske vill ändra en befintlig roll eller kombinera behörigheter som används i flera roller.

- Visa en lista över de Azure-tjänster som du vill bevilja åtkomst till.

- Fastställ [de resursproviders som mappar till Azure-tjänsterna](../azure-resource-manager/management/azure-services-resource-providers.md).

    Azure-tjänster exponerar sina funktioner och behörigheter via [resursproviders.](../azure-resource-manager/management/overview.md) Resursprovidern Microsoft.Compute tillhandahåller till exempel virtuella datorresurser och resursprovidern Microsoft.Billing tillhandahåller prenumerations- och faktureringsresurser. Att känna till resursproviders kan hjälpa dig att begränsa och fastställa vilka behörigheter du behöver för din anpassade roll.

    När du skapar en anpassad roll med Azure Portal kan du också fastställa resursproviders genom att söka efter nyckelord. Den här sökfunktionen beskrivs i [Skapa eller uppdatera anpassade Azure-roller med hjälp av Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Fönstret Lägg till behörigheter med resursprovidern](./media/custom-roles-portal/add-permissions-provider.png)

- Sök efter [de tillgängliga behörigheterna](resource-provider-operations.md) för att hitta behörigheter som du vill inkludera.

    När du skapar en anpassad roll med Azure Portal kan du söka efter behörigheter efter nyckelord. Du kan till exempel söka efter behörigheter *för virtuell dator* *eller* fakturering. Du kan också ladda ned alla behörigheter som en CSV-fil och sedan söka i den här filen. Den här sökfunktionen beskrivs i [Skapa eller uppdatera anpassade Azure-roller med hjälp av Azure Portal](custom-roles-portal.md#step-4-permissions).

    ![Lägg till behörighetslista](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Exempel på anpassad roll

Följande visar hur en anpassad roll ser ut som den visas med hjälp Azure PowerShell JSON-format. Den här anpassade rollen kan användas för att övervaka och starta om virtuella datorer.

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

Följande visar samma anpassade roll som visas med Hjälp av Azure CLI.

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

När du skapar en anpassad roll visas den i Azure Portal med en orange resursikon.

![Ikon för anpassad roll](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Anpassade rollegenskaper

I följande tabell beskrivs vad egenskaperna för den anpassade rollen innebär.

| Egenskap | Krävs | Typ | Beskrivning |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Ja | Sträng | Visningsnamnet för den anpassade rollen. Även om en rolldefinition är en hanteringsgrupps- eller prenumerationsnivåresurs kan en rolldefinition användas i flera prenumerationer som delar samma Azure AD-katalog. Det här visningsnamnet måste vara unikt i omfånget för Azure AD-katalogen. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Maximalt antal tecken är 128. |
| `Id`</br>`name` | Ja | Sträng | Unikt ID för den anpassade rollen. För Azure PowerShell och Azure CLI genereras det här ID:t automatiskt när du skapar en ny roll. |
| `IsCustom`</br>`roleType` | Ja | Sträng | Anger om det här är en anpassad roll. Ange till `true` eller `CustomRole` för anpassade roller. Ange till `false` `BuiltInRole` eller för inbyggda roller. |
| `Description`</br>`description` | Ja | Sträng | Beskrivning av den anpassade rollen. Kan innehålla bokstäver, siffror, blanksteg och specialtecken. Det maximala antalet tecken är 1 024. |
| `Actions`</br>`actions` | Ja | Sträng[] | En matris med strängar som anger de hanteringsåtgärder som rollen tillåter att utföras. Mer information finns i [Åtgärder](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | Inga | Sträng[] | En matris med strängar som anger de hanteringsåtgärder som undantas från den tillåtna `Actions` . Mer information finns i [NotActions](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | Inga | Sträng[] | En matris med strängar som anger de dataåtgärder som rollen tillåter att utföras på dina data i objektet. Om du skapar en anpassad roll med `DataActions` kan den rollen inte tilldelas i hanteringsgruppens omfång. Mer information finns i [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | Inga | Sträng[] | En matris med strängar som anger de dataåtgärder som undantas från den tillåtna `DataActions` . Mer information finns i [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Ja | Sträng[] | En matris med strängar som anger de omfång som den anpassade rollen är tillgänglig för tilldelning. Du kan bara definiera en hanteringsgrupp i `AssignableScopes` en anpassad roll. Att lägga till en hanteringsgrupp `AssignableScopes` i är för närvarande i förhandsversion. Mer information finns i [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Behörigheter med jokertecken

`Actions`, `NotActions` , och har stöd för `DataActions` `NotDataActions` jokertecken ( ) för att definiera `*` behörigheter. Ett jokertecken ( `*` ) utökar en behörighet till allt som matchar den åtgärdssträng som du anger. Anta till exempel att du vill lägga till alla behörigheter som rör Azure Cost Management och exporter. Du kan lägga till alla dessa åtgärdssträngar:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

I stället för att lägga till alla dessa strängar kan du lägga till en sträng med jokertecken. Följande jokerteckenssträng motsvarar till exempel de föregående fem strängarna. Detta omfattar även eventuella framtida exportbehörigheter som kan läggas till.

```
Microsoft.CostManagement/exports/*
```

Du kan också ha flera jokertecken i en sträng. Följande sträng representerar till exempel alla frågebehörigheter för Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Vem kan skapa, ta bort, uppdatera eller visa en anpassad roll

Precis som inbyggda roller anger `AssignableScopes` egenskapen de omfång som rollen är tillgänglig för tilldelning. Egenskapen `AssignableScopes` för en anpassad roll styr också vem som kan skapa, ta bort, uppdatera eller visa den anpassade rollen.

| Uppgift | Åtgärd | Beskrivning |
| --- | --- | --- |
| Skapa/ta bort en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som beviljas den här åtgärden för alla anpassade roller kan skapa `AssignableScopes` (eller ta bort) anpassade roller för användning i dessa omfång. Till exempel Ägare [och](built-in-roles.md#owner) [Administratörer av användaråtkomst](built-in-roles.md#user-access-administrator) för hanteringsgrupper, prenumerationer och resursgrupper. |
| Uppdatera en anpassad roll | `Microsoft.Authorization/ roleDefinitions/write` | Användare som beviljas den här åtgärden för alla `AssignableScopes` anpassade roller kan uppdatera anpassade roller i dessa omfång. Till exempel Ägare [och](built-in-roles.md#owner) [Administratörer av användaråtkomst](built-in-roles.md#user-access-administrator) för hanteringsgrupper, prenumerationer och resursgrupper. |
| Visa en anpassad roll | `Microsoft.Authorization/ roleDefinitions/read` | Användare som beviljas den här åtgärden i ett omfång kan visa de anpassade roller som är tillgängliga för tilldelning i det omfånget. Alla inbyggda roller tillåter att anpassade roller är tillgängliga för tilldelning. |

## <a name="custom-role-limits"></a>Anpassade rollbegränsningar

I följande lista beskrivs gränserna för anpassade roller.

- Varje katalog kan ha upp till **5 000** anpassade roller.
- Azure Tyskland och Azure China 21Vianet kan ha upp till 2 000 anpassade roller för varje katalog.
- Du kan inte `AssignableScopes` ange rotomfånget ( `"/"` ).
- Du kan inte använda jokertecken ( `*` ) i `AssignableScopes` . Den här begränsningen med jokertecken hjälper till att säkerställa att en användare inte kan få åtkomst till ett omfång genom att uppdatera rolldefinitionen.
- Du kan bara definiera en hanteringsgrupp i `AssignableScopes` en anpassad roll. Att lägga till en hanteringsgrupp `AssignableScopes` i är för närvarande i förhandsversion.
- Anpassade roller med `DataActions` kan inte tilldelas i hanteringsgruppomfånget.
- Azure Resource Manager verifierar inte att hanteringsgruppen finns i rolldefinitionens tilldelningsbara omfång.

Mer information om anpassade roller och hanteringsgrupper finns i [Organisera dina resurser med Azure-hanteringsgrupper.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="input-and-output-formats"></a>Indata- och utdataformat

Om du vill skapa en anpassad roll med hjälp av kommandoraden använder du vanligtvis JSON för att ange de egenskaper som du vill använda för den anpassade rollen. Beroende på vilka verktyg du använder ser indata- och utdataformaten lite annorlunda ut. I det här avsnittet visas indata- och utdataformaten beroende på verktyget.

### <a name="azure-powershell"></a>Azure PowerShell

Om du vill skapa en anpassad Azure PowerShell måste du ange följande indata.

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

Om du vill uppdatera en anpassad roll Azure PowerShell måste du ange följande indata. Observera att egenskapen `Id` har lagts till. 

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

Nedan visas ett exempel på utdata när du listar en anpassad roll med hjälp Azure PowerShell kommandot [ConvertTo-Json.](/powershell/module/microsoft.powershell.utility/convertto-json) 

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

Om du vill skapa eller uppdatera en anpassad roll med hjälp av Azure CLI måste du ange följande indata. Det här formatet är samma format när du skapar en anpassad roll med hjälp av Azure PowerShell.

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

Nedan visas ett exempel på utdata när du visar en anpassad roll med hjälp av Azure CLI.

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

Om du vill skapa eller uppdatera en anpassad roll med REST API måste du ange följande indata. Det här formatet är samma format som genereras när du skapar en anpassad roll med hjälp av Azure Portal.

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

Nedan visas ett exempel på utdata när du visar en anpassad roll med hjälp av REST API.

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

- [Självstudie: Skapa en anpassad Azure-roll med Azure PowerShell](tutorial-custom-role-powershell.md)
- [Självstudie: Skapa en anpassad Azure-roll med Hjälp av Azure CLI](tutorial-custom-role-cli.md)
- [Förstå azure-rolldefinitioner](role-definitions.md)
- [Felsöka Azure RBAC](troubleshooting.md)
