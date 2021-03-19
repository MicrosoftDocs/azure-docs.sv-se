---
title: Förstå Azure Deny-tilldelningar – Azure RBAC
description: Lär dig mer om Azure Deny-tilldelningar i rollbaserad åtkomst kontroll i Azure (Azure RBAC).
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
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "82733783"
---
# <a name="understand-azure-deny-assignments"></a>Förstå Azure Deny-tilldelningar

I likhet med en roll tilldelning bifogar en *neka-tilldelning* en uppsättning neka-åtgärder till en användare, grupp eller tjänstens huvud namn i ett visst omfång för att neka åtkomst. Neka tilldelningar blockera användare från att utföra vissa åtgärder för Azure-resurser även om en roll tilldelning ger dem åtkomst.

I den här artikeln beskrivs hur neka-tilldelningar definieras.

## <a name="how-deny-assignments-are-created"></a>Hur neka-tilldelningar skapas

Neka-tilldelningar skapas och hanteras av Azure för att skydda resurser. Azure-ritningar och Azure-hanterade appar använder neka-tilldelningar för att skydda systemhanterade resurser. Azure-ritningar och Azure-hanterade appar är det enda sättet som nekar tilldelningar kan skapas. Du kan inte skapa egna neka-tilldelningar direkt. Mer information om hur ritningar använder neka-tilldelningar för att låsa resurser finns i [förstå resurs låsning i Azure-ritningar](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Du kan inte skapa egna neka-tilldelningar direkt.

## <a name="compare-role-assignments-and-deny-assignments"></a>Jämför roll tilldelningar och neka tilldelningar

Neka tilldelningar följer ett liknande mönster som roll tilldelningar, men har också vissa skillnader.

| Funktion | Rolltilldelning | Neka tilldelning |
| --- | --- | --- |
| Bevilja åtkomst | :heavy_check_mark: |  |
| Neka åtkomst |  | :heavy_check_mark: |
| Kan skapas direkt | :heavy_check_mark: |  |
| Tillämpa i ett omfång | :heavy_check_mark: | :heavy_check_mark: |
| Exkludera huvud konton |  | :heavy_check_mark: |
| Förhindra arv av underordnade omfattningar |  | :heavy_check_mark: |
| Tillämpa på [Administratörs](rbac-and-directory-admin-roles.md) tilldelningar för klassisk prenumeration |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Egenskaper för neka tilldelning

 En neka-tilldelning har följande egenskaper:

> [!div class="mx-tableFixed"]
> | Egenskap | Krävs | Typ | Beskrivning |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | Sträng | Visnings namnet för neka-tilldelningen. Namn måste vara unika för ett angivet omfång. |
> | `Description` | Inga | Sträng | Beskrivningen av neka-tilldelningen. |
> | `Permissions.Actions` | Minst en åtgärd eller en DataActions | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som neka tilldelning blockerar åtkomsten till. |
> | `Permissions.NotActions` | Inga | Sträng [] | En sträng mat ris som anger vilka hanterings åtgärder som ska undantas från neka-tilldelningen. |
> | `Permissions.DataActions` | Minst en åtgärd eller en DataActions | Sträng [] | En sträng mat ris som anger de data åtgärder som neka tilldelning blockerar åtkomsten till. |
> | `Permissions.NotDataActions` | Inga | Sträng [] | En sträng mat ris som anger de data åtgärder som ska undantas från neka-tilldelningen. |
> | `Scope` | Inga | Sträng | En sträng som anger omfattningen som neka-tilldelningen gäller för. |
> | `DoNotApplyToChildScopes` | Inga | Boolesk | Anger om neka-tilldelningen gäller för underordnade omfång. Standardvärdet är false. |
> | `Principals[i].Id` | Ja | Sträng [] | En matris med objekt-ID: n för Azure AD-huvudobjektet (användare, grupp, tjänstens huvud namn eller hanterad identitet) som neka-tilldelningen gäller. Ange ett tomt GUID `00000000-0000-0000-0000-000000000000` som representerar alla huvud konton. |
> | `Principals[i].Type` | Inga | Sträng [] | En matris med objekt typer som representeras av huvud konton [i]. ID. anges till `SystemDefined` som representerar alla huvud konton. |
> | `ExcludePrincipals[i].Id` | Inga | Sträng [] | En matris med objekt-ID: n för Azure AD-huvudobjektet (användare, grupp, tjänstens huvud namn eller hanterad identitet) som neka-tilldelningen inte gäller för. |
> | `ExcludePrincipals[i].Type` | Inga | Sträng [] | En matris med objekt typer som representeras av ExcludePrincipals [i]. ID. |
> | `IsSystemProtected` | Inga | Boolesk | Anger om den här nekande tilldelningen skapades av Azure och inte kan redige ras eller tas bort. För närvarande är alla nekade tilldelningar skyddade av systemet. |

## <a name="the-all-principals-principal"></a>Huvudobjektet alla huvud konton

För att stödja neka-tilldelningar har ett Systemdefinierat objekt med namnet *alla huvud konton* introducerats. Detta huvud konto representerar alla användare, grupper, tjänstens huvud namn och hanterade identiteter i en Azure AD-katalog. Om ägar-ID: t är noll GUID `00000000-0000-0000-0000-000000000000` och huvud typen är `SystemDefined` , representerar huvudobjektet alla huvud konton. I Azure PowerShell utdata ser alla huvud konton ut så här:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alla huvud konton kan kombineras med `ExcludePrincipals` för att neka alla huvud konton förutom vissa användare. Alla huvud konton har följande begränsningar:

- Kan endast användas i `Principals` och kan inte användas i `ExcludePrincipals` .
- `Principals[i].Type` måste anges till `SystemDefined` .

## <a name="next-steps"></a>Nästa steg

* [Självstudie: skydda nya resurser med resurs lås för Azure-ritningar](../governance/blueprints/tutorials/protect-new-resources.md)
* [Visa en lista över Azure Deny-tilldelningar med hjälp av Azure Portal](deny-assignments-portal.md)
