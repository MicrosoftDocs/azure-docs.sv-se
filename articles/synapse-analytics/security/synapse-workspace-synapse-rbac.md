---
title: Synapse-rollbaserad åtkomst kontroll
description: En artikel som förklarar rollbaserad åtkomst kontroll i Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105155"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Vad är Synapse-rollbaserad åtkomst kontroll (RBAC)?

Synapse RBAC utökar funktionerna i [Azure RBAC](../../role-based-access-control/overview.md) för Synapse-arbetsytor och deras innehåll. 

Azure RBAC används för att hantera vem som kan skapa, uppdatera eller ta bort arbets ytan Synapse och dess SQL-pooler, Apache Spark pooler och integrerings körningar.

Synapse RBAC används för att hantera vem som kan:
- Publicera kod artefakter och lista eller komma åt publicerade kod artefakter, 
- Kör kod på Apache Spark-pooler och integrerings körningar,
- Åtkomst till länkade (data) tjänster som skyddas av autentiseringsuppgifter 
- Övervaka eller Avbryt jobb körning, granska utdata från jobb och körnings loggar.  

>[!Note]
>Medan Synapse RBAC används för att hantera åtkomst till publicerade SQL-skript ger den endast begränsad åtkomst kontroll till Server fri SQL-pooler och används _inte_ för att kontrol lera åtkomsten till DEDIKERADe SQL-pooler.  Åtkomst till SQL-pooler kontrol leras i första hand med SQL-säkerhet.

## <a name="what-can-i-do-with-synapse-rbac"></a>Vad kan jag göra med Synapse RBAC?

Här följer några exempel på vad du kan göra med Synapse RBAC:
  - Tillåt att en användare publicerar ändringar som gjorts i Apache Spark bärbara datorer och jobb till Live-tjänsten.
  - Tillåt en användare att köra och avbryta antecknings böcker och Spark-jobb på en speciell Apache Spark pool.
  - Tillåt att en användare använder vissa autentiseringsuppgifter så att de kan köra pipelines som skyddas av arbets ytans system identitet och komma åt data i länkade tjänster som skyddas med autentiseringsuppgifter. 
  - Låta en administratör hantera, övervaka och avbryta jobb körning på vissa spark-pooler.    

## <a name="how-synapse-rbac-works"></a>Så här fungerar Synapse RBAC
Som Azure RBAC fungerar Synapse RBAC genom att skapa roll tilldelningar. En rolltilldelning består av tre element: ett säkerhetsobjekt, en rolldefinition och ett omfång.  

### <a name="security-principals"></a>Säkerhets objekt

Ett _säkerhets objekt_ är en användare, grupp, tjänstens huvud namn eller hanterad identitet.

### <a name="roles"></a>Roller
 
En _roll_ är en samling behörigheter eller åtgärder som kan utföras på vissa resurs typer eller artefakt typer.

Synapse innehåller inbyggda roller som definierar samlingar med åtgärder som matchar behoven hos olika personer:
- Administratörer kan få fullständig åtkomst för att skapa och konfigurera en arbets yta 
- Utvecklare kan skapa, uppdatera och felsöka SQL-skript, antecknings böcker, pipeliner och data flöden, men inte kunna publicera eller köra den här koden på produktions beräknings resurser/data
- Operatörer kan övervaka och hantera system status, program körning och gransknings loggar, utan åtkomst till kod eller utdata från körningen.
- Säkerhets personal kan hantera och konfigurera slut punkter utan att ha åtkomst till kod, beräknings resurser eller data.

[Lär dig mer](./synapse-workspace-synapse-rbac-roles.md) om de inbyggda Synapse-rollerna. 

### <a name="scopes"></a>Omfattningar

Ett _omfång_ definierar de resurser eller artefakter som åtkomsten gäller för.  Synapse stöder hierarkiska omfång.  Behörigheter som beviljats på ett högre nivå område ärvs av objekt på en lägre nivå.  I Synapse RBAC är omfånget på den översta nivån en arbets yta.  Om du tilldelar en roll med området arbets yta beviljar du behörigheter till alla tillämpliga objekt i arbets ytan.  

Aktuella omfång som stöds inom en arbets yta är: Apache Spark pool, integration runtime, länkad tjänst och autentiseringsuppgift. 

Åtkomst till kod artefakter beviljas med scope för arbets yta.  Att bevilja åtkomst till samlingar av artefakter inom en arbets yta kommer att stödjas i en senare version.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Lösa roll tilldelningar för att fastställa behörigheter

En roll tilldelning ger huvudobjektet de behörigheter som definieras av rollen i det angivna omfånget.

Synapse RBAC är en additiv modell som Azure RBAC. Flera roller kan tilldelas till ett enda huvud konto och i olika omfång. När du beräknar behörigheterna för ett säkerhets objekt, tar systemet med alla roller som tilldelats huvud kontot och grupper som direkt eller indirekt inkluderar huvud kontot.  Den beaktar också omfattningen av varje tilldelning när du fastställer de behörigheter som gäller.  

## <a name="enforcing-assigned-permissions"></a>Tvinga tilldelade behörigheter

I Synapse Studio kan vissa knappar eller alternativ vara nedtonade eller också kan ett behörighets fel returneras när du försöker utföra en åtgärd om du inte har de behörigheter som krävs. 

Om en knapp eller ett alternativ är inaktiverat visas en knapp beskrivning med den behörighet som krävs för att hovra över knappen eller alternativet.  Kontakta en Synapse-administratör för att tilldela en roll som ger den behörighet som krävs. Du kan se de roller som innehåller vissa åtgärder [här](./synapse-workspace-synapse-rbac-roles.md).

## <a name="who-can-assign-synapse-rbac-roles"></a>Vem kan tilldela Synapse RBAC-roller?

Endast en Synapse-administratör kan tilldela Synapse RBAC-roller.  En Synapse-administratör på arbets ytans nivå kan bevilja åtkomst i valfri omfattning.  En Synapse-administratör på en lågnivå omfattning kan bara bevilja åtkomst i det omfånget. 

När en ny arbets yta skapas får skaparen automatiskt rollen som Synapse-administratör vid arbets ytans omfång.   

## <a name="where-do-i-manage-synapse-rbac"></a>Var hanterar jag Synapse RBAC?

Synapse RBAC hanteras inifrån Synapse Studio med hjälp av verktygen för åtkomst kontroll i hantera hubben. 

## <a name="next-steps"></a>Nästa steg

Förstå de inbyggda RBAC- [rollerna i Synapse](./synapse-workspace-synapse-rbac-roles.md).

Lär dig [hur du granskar SYNAPSE RBAC-roll tilldelningar](./how-to-review-synapse-rbac-role-assignments.md) för en arbets yta.

Lär dig [hur du tilldelar SYNAPSE RBAC-roller](./how-to-manage-synapse-rbac-role-assignments.md)