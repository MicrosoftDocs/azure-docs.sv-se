---
title: Översikts instrument panel för Azure Application Insights | Microsoft Docs
description: Övervaka program med instrument panels funktionerna Azure Application insikter och översikt.
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 1b0708fa70d3a3ecb406f1d974bb1f2b47e55b40
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504108"
---
# <a name="application-insights-overview-dashboard"></a>Instrument panel för Application Insights översikt

Application Insights har alltid fått ett översikts fönster som gör det möjligt att snabbt och enkelt utvärdera programmets hälso tillstånd och prestanda. Den nya översikts instrument panelen ger en snabbare mer flexibel upplevelse.

## <a name="how-do-i-test-out-the-new-experience"></a>Hur gör jag för att testa den nya upplevelsen?

Den nya översikts instrument panelen startas nu som standard:

![Översikt över gransknings fönstret](./media/overview-dashboard/overview.png)

## <a name="better-performance"></a>Bättre prestanda

Valet av tidsintervall har förenklats till ett enkelt gränssnitt med ett klick.

![Tidsintervall](./media/overview-dashboard/app-insights-overview-dashboard-03.png)

Den allmänna prestandan har ökat avsevärt. Du har åtkomst med ett klick till populära funktioner som **sökning** och **analys**. Varje standard KPI-panel med dynamiska uppdateringar ger inblick i motsvarande Application Insights funktioner. Om du vill veta mer om misslyckade förfrågningar väljer du **fel** under rubriken **Undersök** :

![Fel](./media/overview-dashboard/app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Instrumentpanel för program

Instrument panelen för program utnyttjar den befintliga instrument panels tekniken i Azure för att ge en helt anpassningsbar vy över programmets hälso tillstånd och prestanda.

Öppna standard instrument panelen genom att välja _program instrument panel_ i det övre vänstra hörnet.

![Skärm bild som visar knappen för instrument panelen för program markerat.](./media/overview-dashboard/app-insights-overview-dashboard-05.png)

Om det här är första gången du öppnar instrument panelen, startas en standardvy:

![Instrumentpanelsvy](./media/overview-dashboard/0001-dashboard.png)

Du kan behålla standardvyn om du vill. Du kan också lägga till och ta bort från instrument panelen för att passa behoven i ditt team.

> [!NOTE]
> Alla användare som har åtkomst till den Application Insights resursen delar samma program instrument panels upplevelse. Ändringar som görs av en användare kommer att ändra vyn för alla användare.

För att gå tillbaka till översikts upplevelsen väljer du bara:

![Knappen Översikt](./media/overview-dashboard/app-insights-overview-dashboard-07.png)

## <a name="troubleshooting"></a>Felsökning

Det finns för närvarande en gräns på 30 dagars data för data som visas på en instrument panel. Om du väljer ett tids filter på mer än 30 dagar, eller om du väljer **Konfigurera panel inställningar** och anger ett anpassat tidsintervall som överstiger 30 dagar, visas inte instrument panelen utöver 30 dagars data, även med Standardkvarhållning av data i 90 dagar. Det finns för närvarande ingen lösning på problemet.

## <a name="next-steps"></a>Nästa steg

- [Trattar](./usage-funnels.md)
- [Kvarhållning](./usage-retention.md)
- [Användarflöden](./usage-flows.md)

