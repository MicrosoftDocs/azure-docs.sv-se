---
title: Skapa Azure Lab Services scheman inom team
description: Lär dig hur du skapar scheman för labb tjänster i Teams.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 202b202f99868875a51d13e95fbcac677246cc05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92042345"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Skapa och hantera labb tjänst scheman i team

Med scheman kan du konfigurera ett klass rums labb så att virtuella datorer i labbet startar och stängs av automatiskt vid en viss tidpunkt. Du kan definiera ett eng ång slö schema eller ett återkommande schema. Följande procedurer ger dig anvisningar om hur du skapar och hanterar scheman för ett klass rums labb: 

Så här påverkar schemat virtuella labb datorer: 

- Den virtuella datorns mall ingår inte i scheman. 
- Endast tilldelade virtuella datorer har startats. Det innebär att om en dator inte begärs av en slutanvändare (student) kommer datorn inte att starta under de schemalagda timmarna. 
- Alla virtuella datorer (om de begärs av en användare eller inte) stoppas baserat på labb schema. 

> [!IMPORTANT]
> Den schemalagda körningen av virtuella datorer räknas inte mot den kvot som tilldelats till en användare. Kvoten är för tiden utanför de schema timmar som en student tillbringar på virtuella datorer. 

Användare kan skapa, redigera och ta bort labb scheman i team precis som på [labb webbplatsen](https://labs.azure.com). Läs artikeln om hur du [skapar och hanterar scheman](how-to-create-schedules-within-teams.md).

## <a name="automatic-shutdown-and-disconnect-settings"></a>Inställningar för automatisk avstängning och från koppling

Du kan aktivera flera kostnads kontroll funktioner för automatisk avstängning för att proaktivt förhindra ytterligare kostnader när de virtuella datorerna inte används aktivt. Kombinationen av följande tre funktioner för automatisk avstängning och från koppling fångar upp de flesta fall där användare av misstag lämnar sina virtuella datorer som kör:
 
- Koppla automatiskt bort användare från virtuella datorer som operativ systemet bedömer inaktivt.
- Stäng virtuella datorer automatiskt när användarna kopplar från.
- Stäng av virtuella datorer som har startats automatiskt, men som inte ansluter till dem.

Mer information finns i artikeln om hur du [konfigurerar inställningar för automatisk avstängning för ett labb](how-to-enable-shutdown-disconnect.md).

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Kom igång och skapa ett labb i Teams](how-to-get-started-create-lab-within-teams.md)
- [Hantera användar listor för labb i team](how-to-manage-user-lists-within-teams.md)
- [Hantera Labbets VM-pool i Teams](how-to-manage-vm-pool-within-teams.md)
- [Åtkomst till en virtuell dator i Teams – elev visning](how-to-access-vm-for-students-within-teams.md)
