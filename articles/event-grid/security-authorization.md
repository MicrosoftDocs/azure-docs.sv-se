---
title: Azure Event Grid säkerhet och autentisering
description: Beskriver Azure Event Grid och dess begrepp.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601048"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Auktorisera åtkomst till Event Grid resurser
Med Azure Event Grid kan du kontrol lera åtkomst nivån som ges till olika användare för att utföra olika **hanterings åtgärder** , till exempel lista händelse prenumerationer, skapa nya och generera nycklar. Event Grid använder rollbaserad åtkomst kontroll i Azure (Azure RBAC).

> [!NOTE]
> EventGrid stöder inte Azure RBAC för publicering av händelser till Event Grid ämnen eller domäner. Använd en signatur för delad åtkomst (SAS) eller token för att autentisera klienter som publicerar händelser. Mer information finns i [autentisera publicerings klienter](security-authenticate-publishing-clients.md). 

## <a name="operation-types"></a>Åtgärds typer
Kör följande Azure CLI-kommando om du vill ha en lista över åtgärder som stöds av Azure Event Grid: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Följande åtgärder returnerar potentiellt hemlig information som filtreras bort från normala Läs åtgärder. Vi rekommenderar att du begränsar åtkomsten till dessa åtgärder. 

* Microsoft. EventGrid/eventSubscriptions/getFullUrl/Action
* Microsoft. EventGrid/ämnen/Listnycklar/åtgärd
* Microsoft. EventGrid/ämnen/regenerateKey/åtgärd


## <a name="built-in-roles"></a>Inbyggda roller
Event Grid tillhandahåller följande tre inbyggda roller. 

Rollerna Event Grid prenumerations läsare och Event Grid prenumerations deltagare används för att hantera händelse prenumerationer. De är viktiga när du implementerar [händelse domäner](event-domains.md) eftersom de ger användarna de behörigheter de behöver för att prenumerera på ämnen i din händelse domän. De här rollerna fokuserar på händelse prenumerationer och beviljar inte åtkomst för åtgärder som att skapa ämnen.

Med rollen Event Grid Contributor kan du skapa och hantera Event Grid-resurser. 


| Roll | Beskrivning |
| ---- | ----------- | 
| [Event Grid prenumerations läsare](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Låter dig hantera Event Grid händelse prenumerations åtgärder. |
| [Event Grid prenumerations deltagare](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Låter dig läsa Event Grid händelse prenumerationer. |
| [Event Grid deltagare](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Gör att du kan skapa och hantera Event Grid-resurser. |


> [!NOTE]
> Välj länkar i den första kolumnen för att navigera till en artikel som innehåller mer information om rollen. Instruktioner för hur du tilldelar användare eller grupper till RBAC-roller finns i [den här artikeln](../role-based-access-control/quickstart-assign-role-user-portal.md).


## <a name="custom-roles"></a>Anpassade roller

Om du behöver ange behörigheter som skiljer sig från de inbyggda rollerna kan du skapa anpassade roller.

Följande är exempel på Event Grid roll definitioner som gör det möjligt för användarna att vidta olika åtgärder. De här anpassade rollerna skiljer sig från de inbyggda rollerna eftersom de ger bredare åtkomst än bara händelse prenumerationer.

**EventGridReadOnlyRole.jspå**: Tillåt endast skrivskyddade åtgärder.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.jspå**: Tillåt åtgärder med begränsad post men tillåt inte borttagnings åtgärder.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.jspå**: tillåter alla Event Grid-åtgärder.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

Du kan skapa anpassade roller med [PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md)och [rest](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Kryptering i vila

Alla händelser eller data som skrivs till disk av tjänsten Event Grid krypteras av en Microsoft-hanterad nyckel som garanterar att den är krypterad i vila. Dessutom är den längsta tids perioden som händelser eller data kvarhålls vara 24 timmar i enlighet med principen för [Event Grid återförsök](delivery-and-retry.md). Event Grid tar automatiskt bort alla händelser eller data efter 24 timmar, eller händelsens tids till Live, beroende på vilket som är mindre.

## <a name="permissions-for-event-subscriptions"></a>Behörigheter för händelse prenumerationer
Om du använder en händelse hanterare som inte är en webhook (till exempel en händelsehubben eller Queue Storage) behöver du Skriv åtkomst till den resursen. Med den här behörighets kontrollen förhindrar du att obehöriga användare skickar händelser till din resurs.

Du måste ha behörigheten **Microsoft. EventGrid/EventSubscriptions/Write** på den resurs som är händelse källa. Du behöver den här behörigheten eftersom du skriver en ny prenumeration i resursens omfattning. Vilken resurs som krävs varierar beroende på om du prenumererar på ett system ämne eller ett anpassat ämne. Båda typerna beskrivs i det här avsnittet.

### <a name="system-topics-azure-service-publishers"></a>System ämnen (Azure Service Publisher)
Om du inte är ägare eller deltagare för käll resursen i system avsnitt måste du ha behörighet att skriva en ny händelse prenumeration i omfånget för resurs publicering av händelsen. Formatet för resursen är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Om du till exempel vill prenumerera på en händelse på ett lagrings konto med namnet **acct**, behöver du behörigheten Microsoft. EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Anpassade ämnen
För anpassade ämnen måste du ha behörighet att skriva en ny händelse prenumeration i avsnittet Event Grid. Formatet för resursen är: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Om du till exempel vill prenumerera på ett anpassat ämne med namnet " **ämne**" måste du ha behörigheten Microsoft. EventGrid/EventSubscriptions/Write på: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Nästa steg

* En introduktion till Event Grid finns i [About Event Grid](overview.md)
