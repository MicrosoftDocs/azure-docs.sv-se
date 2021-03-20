---
title: Förstå resurs låsning
description: Lär dig mer om låsnings alternativen i Azure-ritningar för att skydda resurser när du tilldelar en skiss.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: b2004ad294ae0eec1b4f2fc6f49308efd32d652e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98920198"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Förstå resurs låsning i Azure-ritningar

Att skapa konsekventa miljöer i skala är bara riktigt värdefullt om det finns en mekanism för att upprätthålla denna konsekvens. Den här artikeln förklarar hur resurs låsning fungerar i Azure-ritningar. Om du vill se ett exempel på resurs låsning och tillämpning av _neka-tilldelningar_, se själv studie kursen [skydda nya resurser](../tutorials/protect-new-resources.md) .

> [!NOTE]
> Resurs lås som distribueras av Azure-ritningar tillämpas bara på resurser som distribueras av skiss tilldelningen. Befintliga resurser, till exempel de i resurs grupper som redan finns, har inte några låsta tillägg.

## <a name="locking-modes-and-states"></a>Lås lägen och tillstånd

Låsnings läget gäller för skiss tilldelningen och har tre alternativ: **Lås inte**, **skrivskyddad** eller **Ta inte bort**. Lås läget konfigureras under en artefakt distribution under en skiss tilldelning. Du kan ställa in ett annat lås läge genom att uppdatera skiss tilldelningen.
Lås lägen kan dock inte ändras utanför Azure-ritningar.

Resurser som har skapats av artefakter i en skiss tilldelning har fyra tillstånd: **inte låst**, **skrivskyddad**, **kan inte redigera/ta bort** eller **kan inte tas bort**. Varje artefakt typ kan vara i **låst** läge. Följande tabell kan användas för att fastställa en resurs status:

|Läge|Artefakt resurs typ|Stat|Beskrivning|
|-|-|-|-|
|Lås inte|*|Inte låst|Resurser skyddas inte av Azure-ritningar. Det här läget används också för resurser som läggs till i en **skrivskyddad** eller **inte tar bort** artefakten för resurs gruppen utanför en skiss tilldelning.|
|Skrivskydd|Resursgrupp|Det går inte att redigera/ta bort|Resurs gruppen är skrivskyddad och taggarna i resurs gruppen kan inte ändras. Det går inte att lägga till, flytta, ändra eller ta bort resurser som **inte är låsta** från den här resurs gruppen.|
|Skrivskydd|Icke-resurs grupp|Skrivskydd|Resursen kan inte ändras på något sätt. Inga ändringar och det går inte att ta bort den.|
|Ta inte bort|*|Kan inte ta bort|Resurserna kan ändras, men de kan inte tas bort. Det går inte att lägga till, flytta, ändra eller ta bort resurser som **inte är låsta** från den här resurs gruppen.|

## <a name="overriding-locking-states"></a>Åsidosätter lås tillstånd

Det är vanligt vis möjligt för någon med lämplig [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../../role-based-access-control/overview.md) i prenumerationen, till exempel rollen ägare, att tillåta att en resurs ändras eller tas bort. Den här åtkomsten är inte fallet när Azure-ritningar använder lås som en del av en distribuerad tilldelning. Om tilldelningen har angetts med alternativet **skrivskyddad** eller **Ta inte bort** , inte ens prenumerations ägaren kan utföra den blockerade åtgärden på den skyddade resursen.

Detta säkerhets mått skyddar konsekvensen för den definierade skissen och miljön som den har utformats för att skapa från oavsiktlig eller program mässig borttagning eller ändring.

### <a name="assign-at-management-group"></a>Tilldela i hanterings grupp

Det enda alternativet för att förhindra prenumerations ägare från att ta bort en skiss tilldelning är att tilldela skissen till en hanterings grupp. I det här scenariot har endast **ägare** av hanterings gruppen de behörigheter som krävs för att ta bort skiss tilldelningen.

Om du vill tilldela skissen till en hanterings grupp i stället för en prenumeration ändras REST API anropet så att det ser ut så här:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Hanterings gruppen som definieras av `{assignmentMG}` måste antingen ligga inom hierarkin för hanterings grupper eller vara samma hanterings grupp där skiss definitionen sparas.

Begär ande texten för skiss tilldelningen ser ut så här:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
        "parameters": {
            "storageAccountType": {
                "value": "Standard_LRS"
            },
            "costCenter": {
                "value": "Contoso/Online/Shopping/Production"
            },
            "owners": {
                "value": [
                    "johnDoe@contoso.com",
                    "johnsteam@contoso.com"
                ]
            }
        },
        "resourceGroups": {
            "storageRG": {
                "name": "defaultRG",
                "location": "eastus"
            }
        }
    }
}
```

Den viktigaste skillnaden i denna begär ande text och en som tilldelas en prenumeration är `properties.scope` egenskapen. Den här obligatoriska egenskapen måste anges till den prenumeration som skiss tilldelningen avser. Prenumerationen måste vara direkt underordnad till den hanterings grupp hierarki där skiss tilldelningen lagras.

> [!NOTE]
> En skiss som är tilldelad hanterings gruppens omfattning fungerar fortfarande som en skiss tilldelning på prenumerations nivå. Den enda skillnaden är att skiss tilldelningen lagras för att förhindra prenumerations ägare från att ta bort tilldelningen och associerade lås.

## <a name="removing-locking-states"></a>Tar bort lås tillstånd

Om det blir nödvändigt att ändra eller ta bort en resurs som skyddas av en tilldelning, finns det två sätt att göra det.

- Uppdatera skiss tilldelningen till låsnings läge med **Lås inte**
- Ta bort skiss tilldelningen

När tilldelningen tas bort tas låsen som skapats av Azure-ritningar bort. Resursen är dock kvar bakom och måste tas bort på vanligt sätt.

## <a name="how-blueprint-locks-work"></a>Hur skissen låser sig

En åtgärd för [att neka en Azure RBAC-](../../../role-based-access-control/deny-assignments.md) avsökning tillämpas på artefakt resurser under tilldelningen av en skiss om tilldelningen har valt alternativet **skrivskyddad** eller **Ta inte bort** . Neka-åtgärden läggs till av den hanterade identiteten för skiss tilldelningen och kan bara tas bort från artefakt resurserna av samma hanterade identitet. Det här säkerhets måttet tillämpar låsnings funktionen och förhindrar att skiss låset tas bort utanför Azure-ritningar.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Skärm bild av sidan åtkomst kontroll (I A M) och fliken neka tilldelningar för en resurs grupp." border="false":::

[Egenskaperna för neka tilldelning](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) för varje läge är följande:

|Läge |Behörigheter. åtgärder |Behörigheter. NotActions |Huvud konton [i]. Bastyp |ExcludePrincipals [i]. Identitet | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Skrivskydd |**\** _ |_ *\* /Read **<br />** Microsoft. Authorization/lock/Delete **<br />** Microsoft. Network/virtualNetwork/subnets/JOIN/åtgärd** |SystemDefined (alla) |skiss tilldelning och användardefinierad i **excludedPrincipals** |Resurs grupp- _Sant_; Resurs- _falskt_ |
|Ta inte bort |**\*/Delete** | **Microsoft. Authorization/lock/Delete**<br />**Microsoft. Network/virtualNetwork/subnets/Join/Action** |SystemDefined (alla) |skiss tilldelning och användardefinierad i **excludedPrincipals** |Resurs grupp- _Sant_; Resurs- _falskt_ |

> [!IMPORTANT]
> Azure Resource Manager cachelagrar roll tilldelnings information i upp till 30 minuter. Till följd av detta kan neka-tilldelningar neka åtgärder på skiss resurser inte omedelbart tillämpas fullständigt. Under den här tids perioden kan det vara möjligt att ta bort en resurs som är avsedd att skyddas av skiss lås.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Undanta ett huvud konto från en neka-tilldelning

I vissa design-eller säkerhets scenarier kan det vara nödvändigt att undanta ett huvud konto från den [neka-tilldelning](../../../role-based-access-control/deny-assignments.md) som skiss tilldelningen skapar. Det här steget görs i REST API genom att lägga till upp till fem värden i **excludedPrincipals** -matrisen i **Lås** -egenskapen när [tilldelningen skapas](/rest/api/blueprints/assignments/createorupdate). Följande tilldelnings definition är ett exempel på en begär ande text som innehåller **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="exclude-an-action-from-a-deny-assignment"></a>Undanta en åtgärd från en neka-tilldelning

På samma sätt som för att [utesluta ett huvud konto](#exclude-a-principal-from-a-deny-assignment) för en [neka-tilldelning](../../../role-based-access-control/deny-assignments.md) i en skiss tilldelning kan du undanta vissa [Azure Resource Provider-åtgärder](../../../role-based-access-control/resource-provider-operations.md). I blocket **Properties. låsen** , på samma plats som **excludedPrincipals** , kan du lägga till en **excludedActions** :

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

**ExcludedPrincipals** måste vara explicit, men **excludedActions** -poster kan användas `*` för att matcha jokertecken för resurs leverantörs åtgärder.

## <a name="next-steps"></a>Nästa steg

- Följ själv studie kursen [skydda nya resurser](../tutorials/protect-new-resources.md) .
- Mer information om [livscykeln för en skiss](./lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](./parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](./sequencing-order.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).
