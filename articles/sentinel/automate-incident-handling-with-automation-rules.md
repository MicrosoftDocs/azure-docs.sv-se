---
title: Automatisera incident hantering i Azure Sentinel | Microsoft Docs
description: Den här artikeln förklarar hur du använder Automation-regler för att automatisera incident hantering, för att maximera SOC effektivitet och effektivitet som svar på säkerhetshot.
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
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610100"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automatisera incident hantering i Azure Sentinel med automatiserings regler

I den här artikeln förklaras hur du kan använda Azure Sentinel Automation-regler och hur du använder dem för att implementera åtgärder för säkerhets dirigering, automatisering och svar (SOAR), vilket ökar din SOC effektivitet och sparar tid och resurser.

> [!IMPORTANT]
>
> - Funktionen **automatiserings regler** är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för hands versioner av ytterligare juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släppts till allmän tillgänglighet.

## <a name="what-are-automation-rules"></a>Vad är Automation-regler?

Automation-regler är ett nytt begrepp i Azure Sentinel. Med den här funktionen kan användarna centralt hantera automatisering av incident hantering. Förutom att du kan tilldela spel böcker till incidenter (inte bara aviseringar som tidigare), kan du med automatiserings regler också automatisera svar för flera analys regler samtidigt, tagga, tilldela eller stänga incidenter utan att behöva spel böcker och styra ordningen på de åtgärder som utförs. Automation-regler effektiviserar automatiserings användningen i Azure Sentinel och gör det möjligt att förenkla komplexa arbets flöden för dina incident Dirigerings processer.

## <a name="components"></a>Komponenter

Automation-regler består av flera komponenter:

### <a name="trigger"></a>Utlösare

Automation-regler utlöses genom att en incident skapas. 

För att granska – incidenter skapas från aviseringar efter analys regler, där det finns fyra typer, enligt beskrivningen i självstudien [identifiera hot med inbyggda analys regler i Azure Sentinel](tutorial-detect-threats-built-in.md).

### <a name="conditions"></a>Villkor

Komplexa uppsättningar med villkor kan definieras för att styra när åtgärder (se nedan) ska köras. Dessa villkor baseras vanligt vis på tillstånd eller värden för incidenter och deras entiteter, och de kan innehålla `AND` / `OR` / `NOT` / `CONTAINS` operatörer.

### <a name="actions"></a>Åtgärder

Åtgärder kan definieras för att köras när villkoren (se ovan) är uppfyllda. Du kan definiera många åtgärder i en regel och du kan välja i vilken ordning de ska köras (se nedan). Följande åtgärder kan definieras med hjälp av Automation-regler, utan behov [av avancerade funktioner i en Spelbok](automate-responses-with-playbooks.md):

- Ändra status för en incident och hålla ditt arbets flöde uppdaterat.

  - När du ändrar till "stängt" anger du [stängnings orsaken](tutorial-investigate-cases.md#closing-an-incident) och lägger till en kommentar. Detta hjälper dig att hålla koll på prestanda och effektivitet och finjustera för att minska antalet falska positiva identifieringar.

- Ändra allvarlighets graden för en incident – du kan utvärdera och prioritera igen baserat på närvaro, frånvaro, värden eller attribut för entiteter som ingår i incidenten.

- Tilldela en incident till en ägare – det hjälper dig att dirigera typer av incidenter till den personal som passar bäst för att hantera dem eller till den mest tillgängliga personalen.

- Lägga till en tagg till en incident – detta är användbart för att klassificera incidenter efter ämne, angripare eller andra vanliga nämnare.

Du kan också definiera en åtgärd för att köra en [Spelbok](tutorial-respond-threats-playbook.md), så att du kan utföra mer komplexa svars åtgärder, inklusive de som ingår i externa system. Endast spel böcker som Aktiver ATS av [incident utlösaren](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) är tillgängliga för användning i Automation-regler. Du kan definiera en åtgärd för att inkludera flera spel böcker, eller kombinationer av spel böcker och andra åtgärder, samt i vilken ordning de ska köras.

### <a name="expiration-date"></a>Förfallodatum

Du kan definiera ett förfallo datum för en Automation-regel. Regeln kommer att inaktive ras efter det datumet. Detta är användbart för att hantera (d.v.s. stänga) "brus" incidenter som orsakas av planerade, tidsbegränsade aktiviteter, till exempel inträngande tester.

### <a name="order"></a>Beställning

Du kan definiera i vilken ordning Automation-regler ska köras. Senare regler för Automation utvärderar incidenternas villkor i enlighet med tillståndet efter att ha åtgärd ATS enligt tidigare Automation-regler.

Till exempel, om "första Automation-regeln" ändrade en incidents allvarlighets grad från medel till låg, och "andra Automation-regel" har definierats för att endast köras på incidenter med medelhög eller högre allvarlighets grad, körs den inte på den incidenten.

## <a name="common-use-cases-and-scenarios"></a>Vanliga användnings fall och scenarier

### <a name="incident-triggered-automation"></a>Incident – utlöst automatisering

Fram till nu kan endast aviseringar utlösa ett automatiserat svar genom användning av spel böcker. Med automatiserings regler kan incidenter nu utlösa automatiska svars kedjor som kan innehålla nya incidenter som utlöses av spel böcker ([särskilda behörigheter krävs](#permissions-for-automation-rules-to-run-playbooks)) när en incident skapas. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Utlös spel böcker för Microsoft-leverantörer

Automation-regler är ett sätt att automatisera hanteringen av Microsofts säkerhets aviseringar genom att tillämpa dessa regler på incidenter som skapats från aviseringarna. Automation-reglerna kan anropa spel böcker ([särskilda behörigheter krävs](#permissions-for-automation-rules-to-run-playbooks)) och skicka incidenter till dem med all information, inklusive aviseringar och entiteter. I allmänhet är bästa praxis för Azure Sentinel att använda incident kön som fokus till säkerhets åtgärder.

Microsofts säkerhets aviseringar omfattar följande:

- Microsoft Cloud App Security (MCAS)
- Azure AD Identity Protection
- Azure Defender (ASC)
- Defender för IoT (tidigare ASC för IoT)
- Microsoft Defender för Office 365 (tidigare Office 365 ATP)
- Microsoft Defender för slutpunkter (tidigare MDATP)
- Microsoft Defender för identiteter (tidigare Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Flera sekvenserade spel böcker/åtgärder i en enskild regel

Nu kan du få en nästan fullständig kontroll över ordningen för körning av åtgärder och spel böcker i en enda Automation-regel. Du styr också ordningen för körning av Automation-reglerna själva. På så sätt kan du avsevärt förenkla din spel böcker, minska dem till en enda aktivitet eller en liten, enkel serie uppgifter och kombinera dessa små spel böcker i olika kombinationer i olika Automation-regler.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Tilldela en Spelbok till flera analys regler på en gång

Om du har en uppgift som du vill automatisera i alla analys regler, så kan du skapa ett support ärende i ett externt biljett system – du kan använda en enda Spelbok för alla eller alla analys regler – inklusive eventuella framtida regler – i taget. Detta gör enkla men upprepade underhålls-och underhåll uppgifter som är mycket mindre av en sysslor.

### <a name="automatic-assignment-of-incidents"></a>Automatisk tilldelning av incidenter

Du kan automatiskt tilldela incidenter till rätt ägare. Om din SOC har en analytiker som specialiserar sig på en viss plattform, kan alla incidenter som är relaterade till denna plattform tilldelas automatiskt till den analytikern.

### <a name="incident-suppression"></a>Incident under tryckning

Du kan använda regler för att automatiskt lösa incidenter som är kända falska/ofarliga positiva identifieringar utan att använda spel böcker. Till exempel, när du kör inträngande tester, utför schemalagt underhåll eller uppgraderingar eller testar automatiserings procedurer, kan många falska positiva incidenter skapas som SOC vill ignorera. En tidsbegränsad Automation-regel kan automatiskt stänga dessa incidenter när de skapas och tagga dem med en beskrivning av orsaken till sin generation.

### <a name="time-limited-automation"></a>Tidsbegränsad automatisering

Du kan lägga till förfallo datum för dina Automation-regler. Det kan finnas andra fall än incident undertryck som garanterar tidsbegränsad automatisering. Du kanske vill tilldela en viss typ av incident till en viss användare (t. ex. en intern eller konsult) under en viss tids period. Om tids ramen är känd i förväg kan du effektivt göra så att regeln inaktive ras i slutet av dess relevanta, utan att behöva komma ihåg att göra det.

### <a name="automatically-tag-incidents"></a>Tagga incidenter automatiskt

Du kan automatiskt lägga till sökkoder utan text till incidenter för att gruppera eller klassificera dem enligt eventuella kriterier som du väljer.

## <a name="automation-rules-execution"></a>Körning av automatiserings regler

Automation-regler körs sekventiellt, enligt den ordning som du bestämmer. Varje Automation-regel utförs efter att den tidigare slutfört körningen. I en Automation-regel körs alla åtgärder sekventiellt i den ordning som de är definierade.

För Spelbok-åtgärder finns det en fördröjning på två minuter mellan början av Spelbok-åtgärden och nästa åtgärd i listan.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Behörigheter för Automation-regler för att köra spel böcker

När en Azure Sentinel Automation-regel kör en Spelbok används ett särskilt Azure Sentinel-tjänstkonto som är specifikt auktoriserat för den här åtgärden. Användningen av det här kontot (istället för ditt användar konto) ökar tjänstens säkerhets nivå.

För att en Automation-regel ska kunna köra en Spelbok måste det här kontot beviljas explicita behörigheter till resurs gruppen där Spelbok finns. I det här läget kommer alla automatiserings regler att kunna köra alla Spelbok i den resurs gruppen.

När du konfigurerar en Automation-regel och lägger till en **Kör Spelbok** -åtgärd visas en nedrullningsbar listruta med spel böcker. Spel böcker som Azure Sentinel inte har behörighet till visas som otillgänglig ("nedtonat"). Du kan bevilja Azure Sentinel-behörighet till spel böcker resurs grupper på platsen genom att välja länken **Hantera Spelbok-behörigheter** .

> [!NOTE]
> **Behörigheter i en arkitektur med flera innehavare**
>
> Automation-regler har fullt stöd för distributioner mellan arbets ytor och flera klienter (om flera innehavare använder [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)).
>
> Om din Azure Sentinel-distribution använder en arkitektur för flera innehavare (till exempel om du är en MSSP, till exempel) kan du ha en Automation-regel i en klient som kör en Spelbok som finns i en annan klient, men behörigheter för Sentinel att köra spel böcker måste definieras i klient organisationen där spel böcker finns, inte i klient organisationen där automatiserings reglerna definieras.

## <a name="creating-and-managing-automation-rules"></a>Skapa och hantera automatiserings regler

Du kan skapa och hantera automatiserings regler från olika punkter i Azure Sentinel-upplevelsen, beroende på ditt specifika behov och användnings fall.

- **Automation-blad**

    Automation-regler kan hanteras centralt på det nya **Automation** -bladet (som ersätter bladet **spel böcker** ) under fliken **Automation-regler** . (du kan också hantera spel böcker på det här bladet under fliken **spel böcker** .) Därifrån kan du skapa nya Automation-regler och redigera befintliga. Du kan också dra Automation-regler för att ändra ordningen på körningen och aktivera eller inaktivera dem.

    I **Automation** -bladet ser du alla regler som har definierats på arbets ytan, tillsammans med deras status (aktive rad/inaktive rad) och vilka analys regler de tillämpas på.

    När du behöver en Automation-regel som gäller för många analys regler skapar du den direkt på **Automation** -bladet. Klicka på **skapa** på den översta menyn och **Lägg till en ny regel** som öppnar panelen **Skapa ny Automation-regel** . Härifrån har du fullständig flexibilitet när du konfigurerar regeln: du kan tillämpa den på alla analys regler (inklusive framtida) och definiera det största antalet villkor och åtgärder.

- **Guiden Analytics-regel**

    På fliken **automatiserat svar** i guiden Analytics-regel kan du se, hantera och skapa automatiserings regler som gäller för den specifika analys regeln som skapas eller redige ras i guiden.

    När du klickar på **skapa** och en av regel typerna (**schemalagd frågeregel** eller **regel för skapande av Microsoft-incidenter**) på den översta menyn i **Analytics** -bladet, eller om du väljer en befintlig analys regel och klickar på **Redigera**, öppnas regel guiden. När du väljer fliken **automatiserat svar** visas ett avsnitt som kallas **incident automatisering**, under vilka de automatiserings regler som gäller för den här regeln visas. Du kan välja en befintlig Automation-regel som du vill redigera eller klicka på **Lägg till ny** för att skapa en ny.

    När du skapar Automation-regeln härifrån visar rutan **Skapa ny Automation-regel** villkoret **analys regel** som otillgängligt, eftersom den här regeln redan är inställd på att endast gälla för den analys regel som du redigerar i guiden. Alla andra konfigurations alternativ är fortfarande tillgängliga för dig.

- **Bladet incidenter**

    Du kan också skapa en Automation-regel från bladet **incidenter** , för att svara på en enda återkommande incident. Detta är användbart när du skapar en [undertrycks regel](#incident-suppression) för att automatiskt stänga "brus"-incidenter. Välj en incident från kön och klicka på **skapa Automation-regel** på den översta menyn.

    Observera att panelen **Skapa ny Automation-regel** har fyllt i alla fält med värden från incidenten. Den ger regeln samma namn som incidenten, tillämpar den på den analys regel som genererade incidenten och använder alla tillgängliga entiteter i incidenten som villkor i regeln. Det föreslår också en undertrycks åtgärd (stängning) som standard och föreslår ett utgångs datum för regeln. Du kan lägga till eller ta bort villkor och åtgärder och ändra utgångs datumet som du vill.

## <a name="auditing-automation-rule-activity"></a>Granska automatiserings regel aktivitet

Du kanske är intresse rad av att veta vad som hände med en viss incident och vad en viss Automation-regel kan eller kanske inte har gjort. Du har en fullständig post med incident Chronicles tillgängliga i tabellen *säkerhets incident* på bladet **loggar** . Använd följande fråga för att se all din Automation-regel aktivitet:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Automation-regler för att hantera Azure Sentinel incidents-kön och implementerar en grundläggande incident hanterings automatisering.

- Läs mer om avancerade automatiserings alternativ i [Automatisera hot svar med spel böcker i Azure Sentinel](automate-responses-with-playbooks.md).
- Information om hur du implementerar Automation-regler och spel böcker finns i [Självstudier: använda spel böcker för att automatisera hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).
