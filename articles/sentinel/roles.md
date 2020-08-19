---
title: Behörigheter i Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar hur Azure Sentinel använder rollbaserad åtkomst kontroll för att tilldela behörigheter till användare och identifierar tillåtna åtgärder för varje roll.
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
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b48ff1043ae8128a5cbfdcbba0548d89b5af2624
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565850"
---
# <a name="permissions-in-azure-sentinel"></a>Behörigheter i Azure Sentinel

Azure Sentinel använder [Azures rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) för att tillhandahålla [inbyggda roller](../role-based-access-control/built-in-roles.md)   som kan tilldelas till användare, grupper och tjänster i Azure.

Använd RBAC för att skapa och tilldela roller i din säkerhets åtgärds grupp för att ge lämplig åtkomst till Azure Sentinel. De olika rollerna ger dig detaljerad kontroll över vilka användare av Azure Sentinel som kan se och göra. Azure-roller kan tilldelas i Azure Sentinel-arbetsytan direkt (se OBS! nedan) eller i en prenumeration eller resurs grupp som arbets ytan tillhör, som Azure Sentinel ärver.

## <a name="roles-for-working-in-azure-sentinel"></a>Roller för att arbeta i Azure Sentinel

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel-/regionsspecifika roller

Det finns tre dedikerade inbyggda Azure Sentinel-roller.

**Alla inbyggda Azure Sentinel-roller ger Läs åtkomst till data i Azure Sentinel-arbetsytan.**

- [Azure Sentinel Reader](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) kan visa data, incidenter, arbets böcker och andra Azure Sentinel-resurser.

- [Azure Sentinel-svarare](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) kan, förutom ovanstående, hantera incidenter (tilldela, ignorera osv.)

- [Azure Sentinel-deltagare](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) kan, förutom ovanstående, skapa och redigera arbets böcker, analys regler och andra Azure Sentinel-resurser.

> [!NOTE]
>
> - För bästa resultat bör dessa roller tilldelas till den **resurs grupp** som innehåller Azure Sentinel-arbetsytan. På så sätt kommer rollerna att gälla för alla resurser som distribueras för att stödja Azure Sentinel, eftersom dessa resurser också bör placeras i samma resurs grupp.
>
> - Ett annat alternativ är att tilldela rollerna direkt i själva Azure Sentinel- **arbetsytan** . Om du gör detta måste du också tilldela samma roller på SecurityInsights- **lösnings resursen** på den arbets ytan. Du kan även behöva tilldela dem till andra resurser och du måste ständigt hantera roll tilldelningar för resurser.

### <a name="additional-roles-and-permissions"></a>Ytterligare roller och behörigheter

Användare med särskilda jobb krav kan behöva tilldelas ytterligare roller eller specifika behörigheter för att kunna utföra sina uppgifter.

- Arbeta med spel böcker för att automatisera svar på hot

    Azure Sentinel använder **spel böcker** för automatiserat hot svar. Spel böcker bygger på **Azure Logic Apps**och är en separat Azure-resurs. Du kanske vill tilldela till vissa medlemmar i säkerhets åtgärds teamet möjligheten att använda Logic Apps för åtgärder för säkerhets dirigering, automation och Response (SOAR). Du kan använda [Logic app Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor) -rollen eller [Logic app-operatören](../role-based-access-control/built-in-roles.md#logic-app-operator) för att tilldela uttrycklig behörighet för att använda spel böcker.

- Ansluta data källor till Azure Sentinel

    För att en användare ska kunna lägga till **data anslutningar**måste du tilldela användaren Skriv behörighet på Azure Sentinel-arbetsytan. Observera också de ytterligare behörigheter som krävs för varje koppling, enligt listan på relevant anslutnings sida.

- Gäst användare som tilldelar incidenter

    Om en gäst användare behöver kunna tilldela incidenter, kommer användaren också att behöva tilldelas rollen som [katalog läsare](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers), förutom rollen Azure Sentinel responder. Observera att den här rollen *inte* är en Azure RBAC-roll, men en **Azure Active Directory** roll och att vanliga (icke-gäst) användare har den här rollen som standard. 

En jämförelse sida vid sida finns i [tabellen nedan](#roles-and-allowed-actions).

### <a name="other-roles-you-might-see-assigned"></a>Andra roller som du kan se tilldelade

Vid tilldelning av Azure Sentinel-/regionsspecifika Azure-roller kan du komma över andra Azure-och Log Analytics Azure-roller som kan ha tilldelats till användare för andra orsaker. Tänk på att dessa roller ger en bredare uppsättning behörigheter som innehåller åtkomst till din Azure Sentinel-arbetsyta och andra resurser:

- **Azure-roller:** [ägare](../role-based-access-control/built-in-roles.md#owner), [deltagare](../role-based-access-control/built-in-roles.md#contributor)och [läsare](../role-based-access-control/built-in-roles.md#reader). Azure-roller beviljar åtkomst över alla dina Azure-resurser, inklusive Log Analytics arbets ytor och Azure Sentinel-resurser.

- **Log Analytics roller:** [Log Analytics bidrags givare](../role-based-access-control/built-in-roles.md#log-analytics-contributor) och [Log Analytics läsare](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics roller beviljar åtkomst till dina Log Analytics arbets ytor. 

Till exempel kan en användare som har tilldelats rollen **Azure Sentinel Reader** , men inte rollen **Azure Sentinel Contributor** , redigera objekt i Azure Sentinel om de tilldelas rollen som **deltagare** på Azure-nivå. Om du vill bevilja behörigheter enbart till en användare i Azure Sentinel bör du därför noggrant ta bort den här användarens tidigare behörigheter och se till att du inte bryter mot nödvändig åtkomst till en annan resurs.

## <a name="roles-and-allowed-actions"></a>Roller och tillåtna åtgärder

I följande tabell sammanfattas rollerna och tillåtna åtgärder i Azure Sentinel. 

| Roll | Skapa och kör spel böcker| Skapa och redigera arbets böcker, analys regler och andra Azure Sentinel-resurser | Hantera incidenter (Stäng, tilldela osv.) | Visa data, incidenter, arbets böcker och andra Azure Sentinel-resurser |
|---|---|---|---|---|
| Azure Sentinel-läsare | -- | -- | -- | &#10003; |
| Azure Sentinel-svarare | -- | -- | &#10003; | &#10003; |
| Azure Sentinel-deltagare | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel Contributor + Logic app-deltagare | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>Anpassade roller och avancerad RBAC

- Förutom, eller i stället för, med hjälp av inbyggda Azure-roller kan du skapa Azure-anpassade roller för Azure Sentinel. Azures anpassade roller för Azure Sentinel skapas på samma sätt som du skapar andra [anpassade Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) -roller, baserat på [vissa behörigheter för Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) och [Azure Log Analytics-resurser](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- Du kan använda Log Analytics avancerad rollbaserad åtkomst kontroll över data i Azure Sentinel-arbetsytan. Detta inkluderar både datatyp-baserad RBAC och resurs-koncentrisk RBAC. Mer information om Log Analytics-roller finns [i hantera loggdata och arbets ytor i Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du arbetar med roller för Azure Sentinel-användare och vad varje roll gör det möjligt för användare att göra.

* [Azure Sentinel-blogg](https://aka.ms/azuresentinelblog). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
