---
title: 'Snabbstart: Kom igång med Azure Sentinel'
description: Använd den här snabb starten för att lära dig att snabbt visa och övervaka vad som händer i din miljö med hjälp av Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 384c6df86b4aadb117bb31b4604fa4c2ffcda09a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570582"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Snabbstart: Kom igång med Azure Sentinel




I den här snabb starten får du lära dig hur du snabbt kan visa och övervaka vad som händer i din miljö med hjälp av Azure Sentinel. När du har anslutit dina data källor till Azure Sentinel får du direkt visualisering och analys av data så att du vet vad som händer i alla dina anslutna data källor. Med Azure Sentinel får du en arbets bok med alla verktyg som redan är tillgängliga i Azure samt tabeller och diagram som är inbyggda i och som ger dig analyser för dina loggar och frågor. Du kan antingen använda inbyggda arbets böcker eller skapa en ny arbets bok enkelt från början eller baserat på en befintlig arbets bok. 

## <a name="get-visualization"></a>Hämta visualisering

Om du vill visualisera och få en analys av vad som händer i din miljö kan du först ta en titt på översikts instrument panelen för att få en uppfattning om organisationens säkerhets position. Du kan klicka på varje element i dessa paneler för att öka detalj nivån till de rå data som de skapats från. För att hjälpa dig att minska bruset och minimera antalet aviseringar som du måste granska och undersöka, använder Azure Sentinel en fusions teknik för att korrelera aviseringar till incidenter. **Incidenter** är grupper med relaterade aviseringar som tillsammans skapar en åtgärds bara incident som du kan undersöka och lösa.

- I Azure Portal väljer du Azure Sentinel och väljer sedan den arbets yta som du vill övervaka.

  ![Översikt över Azure Sentinel](./media/qs-get-visibility/overview.png)

- Verktygsfältet högst upp visar hur många händelser du fick under den valda tids perioden och jämför det med de föregående 24 timmarna. I verktygsfältet får du information om de aviseringar som har utlösts (det lilla värdet representerar de senaste 24 timmarna) och anger sedan för dessa händelser, hur många som är öppna, pågår och stängda. Kontrol lera att det inte finns någon dramatisk ökning eller ta bort antalet händelser. Om det finns ett släpp, kan det vara en anslutning som slutade rapportera till Azure Sentinel. Om det uppstår en ökning kan något misstänkt inträffa. Kontrol lera om du har nya aviseringar.

   ![Azure Sentinel-räknare](./media/qs-get-visibility/funnel.png)

Huvud delen av översikts sidan ger en överblick över en överskådlig säkerhets status för din arbets yta:

- **Händelser och aviseringar över tid**: visar antalet händelser och hur många aviseringar som har skapats från dessa händelser. Om du ser en topp som är ovanlig bör du se aviseringar för IT – om det är något ovanligt att det finns en insamling i händelser, men du inte ser aviseringar, kan det vara en risk.

- **Potentiella skadliga händelser**: när trafik identifieras från källor som är kända för att bli skadlig, varnar Azure Sentinel dig på kartan. Om du ser orange är det inkommande trafik: någon försöker komma åt din organisation från en känd skadlig IP-adress. Om du ser utgående (röd) aktivitet innebär det att data från nätverket strömmas ut från din organisation till en känd skadlig IP-adress.

   ![Karta över skadlig trafik](./media/qs-get-visibility/map.png)

- **Senaste incidenter**: om du vill visa dina senaste incidenter, deras allvarlighets grad och antalet aviseringar som är associerade med incidenten. Om du ser en plötslig topp i en speciell typ av avisering, kan det betyda att det finns ett aktivt angrepp som körs. Om du till exempel har en plötslig topp på 20 pass-The-hash-händelser från Microsoft Defender för identitet (tidigare Azure ATP), är det möjligt att någon för närvarande försöker attackera dig.

- **Avvikelser i data källan**: Microsofts dataanalytiker skapade modeller som kontinuerligt söker efter avvikelser i data källor. Om det inte finns några avvikelser visas inget. Om avvikelser upptäcks bör du ta en djup inblick i dem för att se vad som hände. Klicka till exempel på insamling i Azure-aktivitet. Du kan klicka på **diagrammet** för att se när insamling skedde och sedan filtrera efter aktiviteter som inträffade under den tids perioden för att se vad som orsakade insamling.

   ![Avvikande data källor](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Använda inbyggda arbets böcker<a name="dashboards"></a>

Inbyggda arbets böcker ger integrerade data från dina anslutna data källor så att du kan få en djup inblick i de händelser som genereras i dessa tjänster. De inbyggda arbets böckerna omfattar Azure AD, Azure Activity events och on-premises, som kan vara data från Windows-händelser från servrar, från första part-aviseringar från alla utomstående, inklusive brand Väggs trafik loggar, Office 365 och oskyddade protokoll baserade på Windows-händelser. Arbets böckerna baseras på Azure Monitor arbets böcker för att ge dig bättre anpassningsbarhet och flexibilitet i utformningen av din egen arbets bok. Mer information finns i [arbets böcker](../azure-monitor/visualize/workbooks-overview.md).

1. Under **Inställningar** väljer du **arbets böcker**. Under **installerad** kan du se all din installerade arbets bok. Under **Alla** kan du se hela galleriet med inbyggda arbetsböcker som är tillgängliga för installation. 
2. Sök efter en speciell arbets bok för att se hela listan och en beskrivning av vad varje erbjudande har. 
3. Förutsatt att du använder Azure AD för att komma igång med Azure Sentinel rekommenderar vi att du installerar minst följande arbets böcker:
   - **Azure AD**: Använd antingen eller båda av följande:
       - I **Azure AD-inloggningar** analyseras inloggningar över tid för att se om det finns avvikelser. Arbets böckerna innehåller misslyckade inloggningar av program, enheter och platser så att du snabbt kan se om något händer. Var uppmärksam på flera misslyckade inloggningar. 
       - **Azure AD audit-loggar** analyserar administrations aktiviteter, till exempel ändringar i användare (Lägg till, ta bort, osv.), grupp skapande och ändringar.  

   - Lägg till en arbets bok för brand väggen. Lägg till exempel till Palo-arbets boken. Arbets boken analyserar brand Väggs trafiken och ger dig korrelationer mellan brand Väggs data och hot händelser och markerar misstänkta händelser i flera entiteter. Arbets böcker ger dig information om trender i trafiken och gör det möjligt att öka detalj nivån och filtrera resultaten. 

      ![Palo-instrument panel](./media/qs-get-visibility/palo-alto-week-query.png)


Du kan anpassa arbets böckerna antingen genom att redigera huvud knappen fråga ![ ](./media/qs-get-visibility/edit-query-button.png) . Du kan klicka på knappen ![ Log Analytics ](./media/qs-get-visibility/go-to-la-button.png) för att gå till [Log Analytics för att redigera frågan där](../azure-monitor/logs/log-analytics-tutorial.md), och du kan välja ellipsen (...) och välja **Anpassa panel data**, vilket gör att du kan redigera huvud tids filtret, eller ta bort de enskilda panelerna från arbets boken.

Mer information om hur du arbetar med frågor finns i [Självstudier: visuella data i Log Analytics](../azure-monitor/visualize/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Lägg till en ny panel

Om du vill lägga till en ny panel kan du lägga till den i en befintlig arbets bok, antingen en som du skapar eller en inbyggd Azure Sentinel-arbetsbok. 
1. I Log Analytics skapar du en panel med hjälp av anvisningarna i [Självstudier: visuella data i Log Analytics](../azure-monitor/visualize/tutorial-logs-dashboards.md). 
2. När panelen har skapats väljer du den arbets bok som du vill att panelen ska visas under **PIN-kod**.

## <a name="create-new-workbooks"></a>Skapa nya arbets böcker
Du kan skapa en ny arbets bok från början eller använda en inbyggd arbets bok som grund för din nya arbets bok.

1. Om du vill skapa en ny arbets bok från grunden väljer du **arbets böcker** och sedan **+ ny arbets bok**.
2. Välj den prenumeration som arbets boken skapas i och ge den ett beskrivande namn. Varje arbets bok är en Azure-resurs på samma sätt som andra, och du kan tilldela IT-roller (Azure RBAC) för att definiera och begränsa vem som har åtkomst till den. 
3. Om du vill att det ska visas i dina arbets böcker för att fästa visualiseringar i måste du dela den. Klicka på **dela** och **Hantera användare**. 
 
1. Använd **kontrol lera åtkomst** -och **roll tilldelningar** på samma sätt som för andra Azure-resurser. Mer information finns i [dela Azure-arbetsböcker med hjälp av Azure RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Exempel på nya arbetsböcker

I följande exempel fråga kan du jämföra trender för trafik över flera veckor. Du kan enkelt byta enhets leverantör och data källa som du kör frågan på. I det här exemplet används SecurityEvent från Windows, du kan växla den till att köras på AzureActivity eller CommonSecurityLog på någon annan brand vägg.

```console
// week over week query
SecurityEvent
| where TimeGenerated > ago(14d)
| summarize count() by bin(TimeGenerated, 1d)
| extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

Du kanske vill skapa en fråga som införlivar data från flera källor. Du kan skapa en fråga som tittar på Azure Active Directory gransknings loggar för nya användare som precis har skapats och sedan kontrollerar dina Azure-loggar för att se om användaren har börjat göra ändringar i roll tilldelningen inom 24 timmar från skapandet. Den misstänkta aktiviteten visas på den här instrument panelen:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

Du kan skapa olika arbets böcker baserat på roll för person som tittar på data och vad de letar efter. Du kan till exempel skapa en arbets bok för nätverks administratören som innehåller brand Väggs data. Du kan också skapa arbets böcker baserat på hur ofta du vill titta på dem, om det finns saker som du vill granska varje dag och andra objekt som du vill kontrol lera en gång i timmen, till exempel kanske du vill titta på dina Azure AD-inloggningar varje timme för att söka efter avvikelser. 

## <a name="create-new-detections"></a>Skapa nya identifieringar

Generera identifieringar av [data källor som du har anslutit till Azure Sentinel](connect-data-sources.md) för att undersöka hot i din organisation.

När du skapar en ny identifiering kan du utnyttja de inbyggda identifieringarna som är utformade av Microsofts säkerhets forskare som är skräddarsydda för de data källor som du har anslutit.

Om du vill visa alla färdiga identifieringar går du till **analyser** och sedan **regel mallar**. Den här fliken innehåller alla inbyggda regler för Azure-kontroll.

   ![Använd inbyggda identifieringar för att hitta hot med Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Mer information om hur du hämtar välkomst identifieringar finns i [Självstudier: skapa inbyggda analyser](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du lärt dig hur du kommer igång med Azure Sentinel. Fortsätt till självstudien för [att identifiera hot](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Skapa anpassade hot identifierings regler](tutorial-detect-threats-custom.md) som automatiserar dina svar på hot.
