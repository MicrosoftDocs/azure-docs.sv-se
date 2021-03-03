---
title: Microsoft 365 Defender-integrering med Azure Sentinel | Microsoft Docs
description: Lär dig mer om hur Microsoft 365 Defender-integrering med Azure Sentinel ger dig möjlighet att använda Azure Sentinel som din universella incident köer samtidigt som du bevarar M365D's-styrkor som hjälper dig att undersöka M365-säkerhetsincidenter och också hur du tar in Defender-komponenters avancerade jakt data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747002"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Microsoft 365 Defender-integrering med Azure Sentinel

> [!IMPORTANT]
> Microsoft 365 Defender-anslutaren är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

> [!IMPORTANT]
>
> **Microsoft 365 Defender** kallades tidigare **Microsoft Threat Protection** eller **MTP**.
>
> **Microsoft Defender för slut punkten** kallades tidigare **Microsoft Defender Advanced Threat Protection** eller **MDATP**.
>
> Du kan se att de gamla namnen fortfarande används under en viss tids period.

## <a name="incident-integration"></a>Incident integrering

Med incident integrering med Azure Sentinels [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) kan du strömma alla M365D-incidenter till Azure Sentinel och hålla dem synkroniserade mellan båda portalerna. Incidenter från M365D (tidigare kallat Microsoft Threat Protection eller MTP) inkluderar alla associerade aviseringar, entiteter och relevant information, vilket ger dig tillräckligt med sammanhang för att utföra prioritering och förberedande undersökningar i Azure Sentinel. Incidenter i Sentinel förblir i dubbelriktat läge med M365D, så att du kan dra nytta av fördelarna med båda portalerna i incident undersökningen.

Den här integrationen ger Microsoft 365 säkerhets incidenter att de kan hanteras från Azure Sentinel, som en del av den primära incident kön i hela organisationen, så att du kan se – och korrelera – M365 incidenter tillsammans med dem från alla andra molnbaserade och lokala system. På samma gång kan du dra nytta av de unika fördelarna och funktionerna i M365D för djupgående undersökningar och en M365-specifika upplevelse i M365 eko system. M365 Defender berikar och grupperar aviseringar från flera M365-produkter, både för att minska storleken på SOCs incident kön och förkorta tiden för att lösa problemet. Komponent tjänsterna som ingår i M365 Defender-stacken är:

- **Microsoft Defender för slut punkt** (MDE, tidigare MDATP)
- **Microsoft Defender för identitet** (MDI, tidigare AATP)
- **Microsoft Defender för Office 365** (MDO, tidigare O365ATP)
- **Microsoft Cloud App Security** (MCAS)

Förutom att samla in aviseringar från dessa komponenter genererar M365 Defender själva aviseringar. Den skapar incidenter från alla dessa aviseringar och skickar dem till Azure Sentinel.

### <a name="common-use-cases-and-scenarios"></a>Vanliga användnings fall och scenarier

- Ansluta till M365 Defender-incidenter med ett klick, inklusive alla aviseringar och entiteter från M365 Defender-komponenter i Azure Sentinel.

- Dubbelriktad synkronisering mellan Sentinel-och M365D-incidenter om status, ägare och stängnings orsak.

- Utnyttja M365 Defender-avisering och anriknings funktioner i Azure Sentinel, vilket minskar tiden för att lösa problemet.

- Djupgående länk i sammanhang mellan en Azure Sentinel-incident och dess parallella M365 Defender-incident, för att under lätta undersökningar i båda portalerna.

### <a name="connecting-to-microsoft-365-defender"></a>Ansluter till Microsoft 365 Defender

När du har aktiverat Microsoft 365 Defender data Connector för att [samla in incidenter och aviseringar](connect-microsoft-365-defender.md)visas M365D-incidenter i kön för Azure Sentinel-incidenter, med **Microsoft 365 Defender** i fältet **produkt namn** strax efter att de har genererats i M365 Defender.
- Det kan ta upp till 10 minuter innan en incident genereras i M365 Defender till den tidpunkt då den visas i Azure Sentinel.

- Incidenter kommer att matas in och synkroniseras utan extra kostnad.

När M365 Defender-integreringen är ansluten, ansluts alla komponent varnings anslutningar (MDE, MDI, MDO, MCAS) automatiskt i bakgrunden om de inte redan hade gjort det. Om några komponent licenser har köpts efter att M365 Defender var ansluten kommer aviseringarna och incidenterna från den nya produkten fortfarande att flöda till Azure Sentinel utan ytterligare konfiguration eller debitering.

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender-incidenter och Microsoft incident regler för skapande

- Incidenter som genereras av M365 Defender, baserat på aviseringar som kommer från M365 säkerhets produkter, skapas med anpassad M365-logik.

- Microsofts regel för att skapa incidenter i Azure Sentinel skapar även incidenter från samma aviseringar med hjälp av (en annan) anpassad Azure Sentinel-logik.

- Att använda båda mekanismerna tillsammans stöds helt och den här konfigurationen kan användas för att under lätta över gången till den nya M365 Defender-incidentens skapande logik. Detta kommer dock att skapa **dubbla incidenter** för samma aviseringar.

- För att undvika att skapa dubbla incidenter för samma aviseringar, rekommenderar vi att kunderna stänger av alla **regler för skapande av Microsoft-incidenter** för M365-produkter (MDE, MDI och MDO-se MCAS nedan) när du ansluter M365 Defender. Detta kan göras genom att markera kryss rutan relevant på kopplings sidan. Tänk på att om du gör detta kommer alla filter som tillämpades av reglerna för skapande av incident inte att tillämpas på M365 Defender incident integration.

- För Microsoft Cloud App Security-aviseringar (MCAS) är inte alla aviserings typer för närvarande aktiva för M365 Defender. För att se till att du fortfarande får incidenter för alla MCAS-aviseringar måste du behålla eller skapa **regler för skapande av Microsoft-incidenter** för aviserings typerna som *inte* har registrerats i M365D.

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>Arbeta med M365 Defender-incidenter i Azure Sentinel och dubbelriktad synkronisering

M365 Defender-incidenter visas i kön för Azure Sentinel-incidenter med produkt namnet **Microsoft 365 Defender** och med liknande information och funktioner till alla andra Sentinel-incidenter. Varje incident innehåller en länk tillbaka till parallell incidenten i M365 Defender-portalen.

När incidenten utvecklas i M365 Defender, och fler aviseringar eller entiteter läggs till, kommer Azure Sentinel-incidenten att uppdateras i enlighet med detta.

Ändringar som görs i statusen, stängnings orsaken eller tilldelningen av en M365-incident, i antingen M365D eller Azure Sentinel, uppdateras också i enlighet med de andra ärende köerna. Synkroniseringen sker i båda portalerna omedelbart efter att ändringen av incidenten har tillämpats, utan fördröjning. Det kan krävas en uppdatering för att se de senaste ändringarna.

I M365 Defender kan alla aviseringar från en incident överföras till en annan, vilket leder till att incidenterna slås samman. När detta inträffar kommer Azure Sentinel-incidenterna att avspegla ändringarna. En incident innehåller alla aviseringar från både ursprungliga incidenter och den andra incidenten stängs automatiskt, med en tagg för "Omdirigerad" tillagd.

> [!NOTE]
> Incidenter i Azure Sentinel kan innehålla högst 150 aviseringar. M365D-incidenter kan ha mer än så. Om en M365D-incident med fler än 150 aviseringar synkroniseras till Azure Sentinel, visar Sentinel-incidenten "150 +"-varningar och innehåller en länk till den parallella incidenten i M365D där du kommer att se en fullständig uppsättning aviseringar.

## <a name="advanced-hunting-event-collection"></a>Avancerad jakt händelse insamling

Med Microsoft 365 Defender-anslutaren kan du också strömma **avancerade jakt** händelser – en typ av rå händelse data – från Microsoft 365 Defender och dess komponent tjänster till Azure Sentinel. Du kan för närvarande samla in [Microsoft Defender för slut punkt (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [avancerade jakt](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) händelser och strömma dem direkt till syftes inbyggda tabeller i Azure Sentinel-arbetsytan. Tabellerna bygger på samma schema som används i Microsoft 365 Defender-portalen, vilket ger dig fullständig åtkomst till hela uppsättningen avancerade jakt händelser och gör att du kan göra följande:

- Kopiera enkelt dina befintliga Microsoft Defender för slut punkts avancerade jakt frågor till Azure Sentinel.

- Använd obehandlade händelse loggar för att ge ytterligare insikter om dina aviseringar, jakt och undersökning och korrelera dessa händelser med andra från ytterligare data källor i Azure Sentinel.

- Lagra loggarna med ökad kvarhållning bortom Microsoft Defender för slut punkt eller Microsoft 365 Defender standard kvarhållning på 30 dagar. Du kan göra det genom att konfigurera kvarhållning av din arbets yta eller genom att konfigurera kvarhållning per tabell i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du kan dra nytta av Microsoft 365 Defender tillsammans med Azure Sentinel med hjälp av Microsoft 365 Defender-anslutaren.

- Hämta instruktioner för [att aktivera Microsoft 365 Defender-anslutaren](connect-microsoft-365-defender.md).
- Skapa [anpassade aviseringar](tutorial-detect-threats-custom.md) och [Undersök incidenter](tutorial-investigate-cases.md).
