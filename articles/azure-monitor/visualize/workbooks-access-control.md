---
title: Åtkomst kontroll för Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med färdiga och anpassade parameterstyrda arbets böcker med rollbaserad åtkomst kontroll
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: dd01b7e57441d4e5d763a07647ee27f1eef53ea9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621533"
---
# <a name="access-control"></a>Åtkomstkontroll

Åtkomst kontroll i arbets böcker refererar till två saker:

* Åtkomst krävs för att läsa data i en arbets bok. Den här åtkomsten styrs av vanliga [Azure-roller](../../role-based-access-control/overview.md) på de resurser som används i arbets boken. Arbets böcker anger eller konfigurerar inte åtkomst till dessa resurser. Användarna får vanligt vis denna åtkomst till dessa resurser med hjälp av rollen som [övervaknings läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) på dessa resurser.

* Åtkomst krävs för att spara arbets böcker

    - Det `("My")` krävs inga ytterligare behörigheter för att spara privata arbets böcker. Alla användare kan spara privata arbets böcker och de kan bara se arbets böckerna.
    - Om du sparar delade arbets böcker krävs Skriv behörighet i en resurs grupp för att spara arbets boken. De här behörigheterna anges vanligt vis av rollen [övervaknings deltagare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) , men kan också ställas in via rollen arbets grupps *deltagare* .
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Standard roller med arbets boks-relaterade behörigheter

[Övervaknings läsaren](../../role-based-access-control/built-in-roles.md#monitoring-reader) innehåller standard/Read-privilegier som används av övervaknings verktyg (inklusive arbets böcker) för att läsa data från resurser.

[Övervaknings bidrags givare](../../role-based-access-control/built-in-roles.md#monitoring-contributor) innehåller allmänna `/write` privilegier som används av olika övervaknings verktyg för att spara objekt (inklusive `workbooks/write` behörighet att spara delade arbets böcker).
"Arbets bok deltagare" lägger till "arbets böcker/Skriv"-behörigheter till ett objekt för att spara delade arbets böcker.
Inga särskilda behörigheter krävs för att användare ska kunna spara privata arbets böcker som bara de kan se.

För anpassade roller:

Lägg till `microsoft.insights/workbooks/write` för att spara delade arbets böcker. Mer information finns i [arbets bokens deltagar](../../role-based-access-control/built-in-roles.md#monitoring-contributor) roll.

## <a name="next-steps"></a>Nästa steg

* [Kom igång](../platform/workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.