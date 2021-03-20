---
title: Konfigurera din miljö för skiss operatör
description: Lär dig hur du konfigurerar din Azure-miljö för användning med skiss operatörens inbyggda roll.
ms.date: 02/05/2021
ms.topic: how-to
ms.openlocfilehash: b43a33f4bac92903bd07454041d11850f217d480
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558731"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Konfigurera din miljö för en skissoperatör

Hanteringen av dina skiss-och skiss uppgifter kan tilldelas olika team. Det är vanligt att en arkitekt eller styrnings grupp ansvarar för livs cykel hanteringen av dina skiss definitioner medan en drifts grupp ansvarar för att hantera tilldelningar av de centralt kontrollerade skiss definitionerna.

Den inbyggda rollen **skiss operator** är särskilt utformad för användning i den här typen av scenario. Rollen kan användas av åtgärder för att hantera tilldelning av organisationens skiss definitioner, men inte möjligheten att ändra dem. Detta kräver en del konfiguration i din Azure-miljö och den här artikeln förklarar de steg som krävs.

## <a name="grant-permission-to-the-blueprint-operator"></a>Bevilja behörighet till skiss operatören

Det första steget är att ge **skiss operatörs** rollen till kontot eller säkerhets gruppen (rekommenderas) som ska tilldelas ritningar. Den här åtgärden bör utföras på den högsta nivån i hierarkin för hanterings grupper som omfattar alla hanterings grupper och prenumerationer som drift teamet ska ha behörighet för skiss tilldelning till. Vi rekommenderar att du följer principen om minsta behörighet när du beviljar dessa behörigheter.

1. Rekommenderas [Skapa en säkerhets grupp och Lägg till medlemmar](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Tilldela](../../../role-based-access-control/role-assignments-portal.md) kontot eller säkerhets gruppen rollen som ansvarar för rollen **skiss**

## <a name="user-assign-managed-identity"></a>Användare – tilldela hanterad identitet

En skiss definition kan använda antingen systemtilldelade eller användarspecifika hanterade identiteter. När du använder rollen **skiss operatör** måste du dock konfigurera ritnings definitionen för att använda en hanterad identitet som tilldelats av användare. Dessutom måste kontot eller säkerhets gruppen som tilldelats rollen **skiss operatör** beviljas rollen **hanterad identitets operatör** på den användare som tilldelats den hanterade identiteten. Utan den här behörigheten fungerar inte skiss tilldelningar på grund av otillräckliga behörigheter.

1. [Skapa en användardefinierad hanterad identitet](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) för användning av en tilldelad skiss.

1. Bevilja den användare som tilldelats hanterad identitet alla roller eller behörigheter som krävs av skiss definitionen för det avsedda omfånget.

1. [Tilldela](../../../role-based-access-control/role-assignments-portal.md) kontot eller säkerhets gruppen rollen **hanterad identitets operatör med rollen hanterad identitet** . Omfattningen av roll tilldelningen till den nya användarspecifika hanterade identiteten.

1. Som **skiss operatör** tilldelar du [en skiss](../create-blueprint-portal.md#assign-a-blueprint) som använder den nya användarspecifika hanterade identiteten.

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).