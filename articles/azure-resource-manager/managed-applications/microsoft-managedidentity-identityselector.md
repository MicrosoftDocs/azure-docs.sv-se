---
title: IdentitySelector-GRÄNSSNITTs element
description: Beskriver användar gränssnitts elementet Microsoft. ManagedIdentity. IdentitySelector för Azure Portal. Används för att tilldela hanterade identiteter till en resurs.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 91e3cd0b99825fd72eb342ce7a8555b046455538
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063346"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>GRÄNSSNITTs element för Microsoft. ManagedIdentity. IdentitySelector

En kontroll för att tilldela [hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) för en resurs i en distribution.

## <a name="ui-sample"></a>UI-exempel

Kontrollen består av följande element:

![Första steget i Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-1.png)

När användaren väljer **Lägg till** öppnas följande formulär. Användaren kan välja en eller flera användar-tilldelade identiteter för resursen.

![Andra steg för Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-2.png)

De valda identiteterna visas i tabellen. Användaren kan lägga till eller ta bort objekt från den här tabellen.

![Tredje steget Microsoft. ManagedIdentity. IdentitySelector](./media/managed-application-elements/microsoft-managedidentity-identityselector-3.png)

## <a name="schema"></a>Schema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Exempelutdata

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Kommentarer

- Använd **defaultValue.systemAssignedIdentity** för att ange ett ursprungligt värde för den tilldelade identitets alternativ kontrollen. Standardvärdet är **inaktiverat**. Följande värden är tillåtna:
  - **På** – en tilldelad dator identitet tilldelas till resursen.
  - **Av** – en tilldelad system identitet är inte tilldelad till resursen.
  - **OnOnly** – en tilldelad dator identitet tilldelas till resursen. Användare kan inte redigera det här värdet under distributionen.
  - **OffOnly** – en tilldelad system identitet är inte tilldelad till resursen. Användare kan inte redigera det här värdet under distributionen.

- Om **Options. hideSystemAssignedIdentity** har angetts till **True** visas inte gränssnittet för att konfigurera den tilldelade identiteten. Standardvärdet för det här alternativet är **false**.
- Om **Options. hideUserAssignedIdentity** har angetts till **True** visas inte användar gränssnittet för att konfigurera den tilldelade identiteten. Resursen har inte tilldelats någon tilldelad identitet. Standardvärdet för det här alternativet är **false**.

## <a name="next-steps"></a>Nästa steg

- En introduktion till att skapa GRÄNSSNITTs definitioner finns i [komma igång med CreateUiDefinition](create-uidefinition-overview.md).
- En beskrivning av gemensamma egenskaper i UI-element finns i [CreateUiDefinition-element](create-uidefinition-elements.md).