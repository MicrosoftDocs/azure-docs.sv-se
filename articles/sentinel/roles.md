---
title: Behörigheter i Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar hur Azure Sentinel använder rollbaserad åtkomst kontroll i Azure för att tilldela behörigheter till användare och identifierar tillåtna åtgärder för varje roll.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/11/2021
ms.author: yelevin
ms.openlocfilehash: b64adbb63efaa4ce4781474f732bc9509d51029e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310333"
---
# <a name="permissions-in-azure-sentinel"></a>Behörigheter i Azure Sentinel

Azure Sentinel använder [Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) för att tillhandahålla [inbyggda roller](../role-based-access-control/built-in-roles.md) som kan tilldelas till användare, grupper och tjänster i Azure.

Använd Azure RBAC för att skapa och tilldela roller i din säkerhets åtgärds grupp för att ge lämplig åtkomst till Azure Sentinel. De olika rollerna ger dig detaljerad kontroll över vilka användare av Azure Sentinel som kan se och göra. Azure-roller kan tilldelas i Azure Sentinel-arbetsytan direkt (se OBS! nedan) eller i en prenumeration eller resurs grupp som arbets ytan tillhör, som Azure Sentinel ärver.

## <a name="roles-for-working-in-azure-sentinel"></a>Roller för att arbeta i Azure Sentinel

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel-specifika roller

**Alla inbyggda Azure Sentinel-roller ger Läs åtkomst till data i Azure Sentinel-arbetsytan.**

- [Azure Sentinel-rollen Läsare](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) kan visa data, incidenter, arbetsböcker och andra Azure Sentinel-resurser.

- [Azure Sentinel-rollen Svarare](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) kan, förutom ovanstående, hantera incidenter (till exempel tilldela och stänga dem)

- [Azure Sentinel-rollen Deltagare](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) kan, förutom ovanstående, skapa och redigera arbetsböcker, analysregler och andra Azure Sentinel-resurser.

- [Azure Sentinel Automation-deltagare](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) gör det möjligt för Azure Sentinel att lägga till spel böcker i Automation-regler. Det är inte avsett för användar konton.

> [!NOTE]
>
> - För bästa resultat bör dessa roller tilldelas till den **resurs grupp** som innehåller Azure Sentinel-arbetsytan. På så sätt kommer rollerna att gälla för alla resurser som distribueras för att stödja Azure Sentinel, eftersom dessa resurser också bör placeras i samma resurs grupp.
>
> - Ett annat alternativ är att tilldela rollerna direkt i själva Azure Sentinel- **arbetsytan** . Om du gör detta måste du också tilldela samma roller på SecurityInsights- **lösnings resursen** på den arbets ytan. Du kan även behöva tilldela dem till andra resurser och du måste ständigt hantera roll tilldelningar för resurser.

### <a name="additional-roles-and-permissions"></a>Ytterligare roller och behörigheter

Användare med särskilda jobb krav kan behöva tilldelas ytterligare roller eller specifika behörigheter för att kunna utföra sina uppgifter.

- **Arbeta med spel böcker för att automatisera svar på hot**

    Azure Sentinel använder **spel böcker** för automatiserat hot svar. Spel böcker bygger på **Azure Logic Apps** och är en separat Azure-resurs. Du kanske vill tilldela till vissa medlemmar i säkerhets åtgärds teamet möjligheten att använda Logic Apps för åtgärder för säkerhets dirigering, automation och Response (SOAR). Du kan använda [Logic app Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor) -rollen för att tilldela uttrycklig behörighet för att använda spel böcker.

- **Ansluta data källor till Azure Sentinel**

    För att en användare ska kunna lägga till **data anslutningar** måste du tilldela användaren Skriv behörighet på Azure Sentinel-arbetsytan. Observera också de ytterligare behörigheter som krävs för varje koppling, enligt listan på relevant anslutnings sida.

- **Gäst användare som tilldelar incidenter**

    Om en gäst användare behöver kunna tilldela incidenter, kommer användaren också att behöva tilldelas rollen som [katalog läsare](../active-directory/roles/permissions-reference.md#directory-readers), förutom rollen Azure Sentinel responder. Observera att den här rollen *inte* är en Azure-roll, men en **Azure Active Directory** -roll och att vanliga (icke-gäst) användare har den här rollen tilldelad som standard.

- **Skapa och ta bort arbets böcker**

    För att en användare ska kunna skapa och ta bort en Azure Sentinel-arbetsbok måste användaren också tilldelas Azure Monitor rollen som [övervaknings deltagare](../role-based-access-control/built-in-roles.md#monitoring-contributor). Den här rollen behövs inte för att *använda* arbets böcker, utan bara för att skapa och ta bort.

### <a name="other-roles-you-might-see-assigned"></a>Andra roller som du kan se tilldelade

Vid tilldelning av Azure Sentinel-/regionsspecifika Azure-roller kan du komma över andra Azure-och Log Analytics Azure-roller som kan ha tilldelats till användare för andra orsaker. Tänk på att dessa roller ger en bredare uppsättning behörigheter som innehåller åtkomst till din Azure Sentinel-arbetsyta och andra resurser:

- **Azure-roller:** [ägare](../role-based-access-control/built-in-roles.md#owner), [deltagare](../role-based-access-control/built-in-roles.md#contributor)och [läsare](../role-based-access-control/built-in-roles.md#reader). Azure-roller beviljar åtkomst över alla dina Azure-resurser, inklusive Log Analytics arbets ytor och Azure Sentinel-resurser.

- **Log Analytics roller:** [Log Analytics bidrags givare](../role-based-access-control/built-in-roles.md#log-analytics-contributor) och [Log Analytics läsare](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics roller beviljar åtkomst till dina Log Analytics arbets ytor.

Till exempel kan en användare som har tilldelats rollen **Azure Sentinel Reader** , men inte rollen **Azure Sentinel Contributor** , redigera objekt i Azure Sentinel om de tilldelas rollen som **deltagare** på Azure-nivå. Om du vill bevilja behörigheter enbart till en användare i Azure Sentinel bör du därför noggrant ta bort den här användarens tidigare behörigheter och se till att du inte bryter mot nödvändig åtkomst till en annan resurs.

## <a name="azure-sentinel-roles-and-allowed-actions"></a>Azure Sentinel-roller och tillåtna åtgärder

I följande tabell sammanfattas Azure Sentinel-roller och deras tillåtna åtgärder i Azure Sentinel.

| Roll | Skapa och köra spelböcker| Skapa och redigera analys regler och andra Azure Sentinel-resurser [*](#workbooks) | Hantera incidenter (Stäng, tilldela osv.) | Visa data, incidenter, arbets böcker och andra Azure Sentinel-resurser |
|---|---|---|---|---|
| Azure Sentinel-läsare | -- | -- | -- | &#10003; |
| Azure Sentinel-svarare | -- | -- | &#10003; | &#10003; |
| Azure Sentinel-deltagare | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel Contributor + Logic app-deltagare | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* För att skapa och ta bort arbets böcker krävs den ytterligare [övervaknings deltagar](../role-based-access-control/built-in-roles.md#monitoring-contributor) rollen. Mer information finns i [ytterligare roller och behörigheter](#additional-roles-and-permissions).
## <a name="custom-roles-and-advanced-azure-rbac"></a>Anpassade roller och avancerad Azure RBAC

- **Anpassade roller**. Förutom, eller i stället för, med hjälp av inbyggda Azure-roller kan du skapa Azure-anpassade roller för Azure Sentinel. Azures anpassade roller för Azure Sentinel skapas på samma sätt som du skapar andra [Azure-anpassade roller](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), baserat på [vissa behörigheter för Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) och [Azure Log Analytics-resurser](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- **Log Analytics RBAC**. Du kan använda Log Analytics avancerad Azure rollbaserad åtkomst kontroll över data i Azure Sentinel-arbetsytan. Detta inkluderar både data typen-baserad Azure RBAC och resurs kontext Azure RBAC. Mer information finns i:

    - [Hantera loggdata och arbets ytor i Azure Monitor](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Resurs kontexten RBAC för Azure Sentinel](resource-context-rbac.md)
    - [RBAC på tabellnivå](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    RBAC för resurs kontext och tabell nivå är två metoder för att ge åtkomst till vissa data i din Azure Sentinel-arbetsyta utan att tillåta åtkomst till hela Azure Sentinel-upplevelsen.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du arbetar med roller för Azure Sentinel-användare och vad varje roll gör det möjligt för användare att göra.

Hitta blogg inlägg om Azure-säkerhet och efterlevnad på [Azure Sentinel-bloggen](https://aka.ms/azuresentinelblog).
