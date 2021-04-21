---
title: Skapa anpassade analysregler för att identifiera hot med Azure Sentinel| Microsoft Docs
description: Använd den här självstudien för att lära dig hur du skapar anpassade analysregler för att identifiera säkerhetshot med Azure Sentinel. Dra nytta av händelsegruppering, aviseringsgruppering och berikande av aviseringar och förstå AUTOMATISK INAKTIVERAD.
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 180a5edd00b6085ffd91568471ca763f5e4e9711
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814864"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Självstudie: Skapa anpassade analysregler för att identifiera hot

Nu när du har anslutit [dina datakällor](quickstart-onboard.md) till Azure Sentinel kan du skapa anpassade analysregler som hjälper dig att identifiera hot och avvikande beteenden som finns i din miljö. De här reglerna söker efter specifika händelser eller uppsättningar av händelser i din miljö, varnar dig när vissa tröskelvärden eller villkor för händelsen nås, genererar incidenter som soc kan hantera och undersöka samt svara på hot med automatiserade spårnings- och reparationsprocesser. 

Den här självstudien hjälper dig att skapa anpassade regler för att identifiera hot med Azure Sentinel.

När du har slutfört den här självstudien kommer du att kunna göra följande:
> [!div class="checklist"]
> * Skapa analysregler
> * Definiera hur händelser och aviseringar bearbetas
> * Definiera hur aviseringar och incidenter genereras
> * Välj automatiserade hotsvar för dina regler

## <a name="create-a-custom-analytics-rule-with-a-scheduled-query"></a>Skapa en anpassad analysregel med en schemalagd fråga

1. Välj Azure Sentinel navigeringsmenyn i **navigeringsmenyn.**

1. I åtgärdsfältet högst upp väljer du **+Skapa och** sedan **Schemalagd frågeregel.** Då öppnas **guiden Analytics-regel.**

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query-small.png" alt-text="Skapa schemalagd fråga" lightbox="media/tutorial-detect-threats-custom/create-scheduled-query-full.png":::

### <a name="analytics-rule-wizard---general-tab"></a>Guide för analysregel – fliken Allmänt

- Ange ett unikt **namn** och en **Beskrivning.** 

- I fältet **Taktiker** kan du välja bland olika typer av attacker som du vill klassificera regeln med. Dessa baseras på mitre [ATT-&CK-ramverket.](https://attack.mitre.org/)

- Ange **allvarlighetsgrad för aviseringen** efter behov. 

- När du skapar regeln är dess **Status** **Aktiverad som** standard, vilket innebär att den körs direkt när du har skapat den. Om du inte vill att den ska köras direkt väljer du  Inaktiverad så läggs regeln till på fliken Aktiva regler och du kan aktivera den därifrån när du behöver den.

   :::image type="content" source="media/tutorial-detect-threats-custom/general-tab.png" alt-text="Börja skapa en anpassad analysregel":::

## <a name="define-the-rule-query-logic-and-configure-settings"></a>Definiera regelfrågelogiken och konfigurera inställningar

På fliken **Ange regellogik** kan du antingen  skriva en fråga direkt i fältet Regelfråga eller skapa frågan i Log Analytics och sedan kopiera och klistra in den här.

- Frågor skrivs med Kusto Query Language (KQL). Läs mer om [](/azure/data-explorer/kusto/concepts/) KQL-begrepp [och frågor](/azure/data-explorer/kusto/query/)och se den här praktiska [snabbreferensguiden](/azure/data-explorer/kql-quick-reference).

- Exemplet som visas i den här skärmbilden frågar *Tabellen SecurityEvent* för att visa en typ av [misslyckade Windows-inloggningshändelser.](/windows/security/threat-protection/auditing/event-4625)

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-1-new.png" alt-text="Konfigurera logik och inställningar för frågeregel" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-1-new.png":::

- Här är en annan exempelfråga, en som varnar dig när ett avvikande antal resurser skapas i [Azure-aktiviteten](../azure-monitor/essentials/activity-log.md).

    ```kusto
    AzureActivity
    | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
    | where ActivityStatus == "Succeeded"
    | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
    ```

    > [!NOTE]
    > #### <a name="rule-query-best-practices"></a>Metodtips för regelfrågor
    > - Frågelängden ska vara mellan 1 och 10 000 tecken och får inte innehålla " `search *` " eller `union *` " ". Du kan använda [användardefinierade funktioner för att](/azure/data-explorer/kusto/query/functions/user-defined-functions) lösa begränsningen av frågelängden.
    >
    > - Du kan inte använda ADX Azure Data Explorer för att skapa Azure Data Explorer frågor i Log **Analytics-frågefönstret.**
    >
    > - När du använder funktionen i en fråga kommer frågan att misslyckas om du projicerar kolumnerna som fält med hjälp av " " och **`bag_unpack`** `project field1` kolumnen inte finns. För att skydda dig mot detta måste du projicera kolumnen på följande sätt:
    >   - `project field1 = column_ifexists("field1","")`

### <a name="alert-enrichment"></a>Aviseringsberikning

> [!IMPORTANT]
> Funktionerna för aviseringsberikning finns för närvarande i **FÖRHANDSVERSION.** Se kompletterande [användningsvillkor för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Microsoft Azure-förhandsversioner för ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts i allmän tillgänglighet.
    
- Använd **konfigurationsavsnittet** Entitetsmappning för att mappa parametrar från dina frågeresultat till Azure Sentinel identifierade entiteter. Entiteter utökar reglernas utdata (aviseringar och incidenter) med viktig information som fungerar som byggstenar i alla undersökande processer och åtgärdsåtgärder som följer. De är också de kriterier som du kan använda för att gruppera aviseringar i incidenter på **fliken Incidentinställningar.**

    Läs mer om [entiteter i Azure Sentinel](entities-in-azure-sentinel.md).

    Se [Mappa datafält till entiteter i Azure Sentinel](map-data-fields-to-entities.md) fullständiga instruktioner för entitetsmappning, tillsammans med viktig information om [bakåtkompatibilitet.](map-data-fields-to-entities.md#notes-on-the-new-version)

- Använd **konfigurationsavsnittet** Anpassad information för att extrahera händelsedataobjekt från din fråga och visa dem i aviseringarna som skapas av den här regeln, vilket ger dig omedelbar innehållssynlighet för händelser i dina aviseringar och incidenter.

    Läs mer om att visa anpassad information i aviseringar och se de [fullständiga anvisningarna.](surface-custom-details-in-alerts.md)

### <a name="query-scheduling-and-alert-threshold"></a>Schemaläggning av frågor och tröskelvärde för avisering

- I avsnittet **Frågeschemaläggning** anger du följande parametrar:

   :::image type="content" source="media/tutorial-detect-threats-custom/set-rule-logic-tab-2.png" alt-text="Ange frågeschema och händelsegruppering" lightbox="media/tutorial-detect-threats-custom/set-rule-logic-tab-all-2-new.png":::

    - Ange **Kör fråga varje** för att styra hur ofta frågan körs – så ofta som var 5:e minut eller så sällan som en gång var 14:e dag.

    - Ange **Uppslagsdata från** den sista för att fastställa tidsperioden för de data som omfattas av frågan – till exempel kan den fråga de senaste 10 minuterna med data eller de senaste 6 timmarna med data. Maxvärdet är 14 dagar.

        > [!NOTE]
        > **Frågeintervall och återställningsperiod**
        >
        >  De här två inställningarna är oberoende av varandra, upp till en punkt. Du kan köra en fråga med ett kort intervall som omfattar en tidsperiod som är längre än intervallet (i praktiken med överlappande frågor), men du kan inte köra en fråga med ett intervall som överskrider täckningsperioden, annars har du luckor i den övergripande frågetäckningen.
        >
        > **Inmatningsfördröjning**
        >
        > För att  ta hänsyn till svarstider som kan uppstå mellan en händelses generation vid källan och dess inmatning i Azure Sentinel, och för att säkerställa fullständig täckning utan dataduplicering, kör Azure Sentinel schemalagda analysregler på en femminutersfördröjning från den schemalagda tiden. 
        >
        > En detaljerad teknisk förklaring av varför den här fördröjningen är nödvändig och hur den löser det här problemet finns i Ron Marstrapps utmärkta blogginlägg om ämnet " Hantering av inmatningsfördröjning i Azure Sentinel schemalagda[aviseringsregler](https://techcommunity.microsoft.com/t5/azure-sentinel/handling-ingestion-delay-in-azure-sentinel-scheduled-alert-rules/ba-p/2052851)".

- Använd avsnittet **Aviseringströskel** för att definiera regelns känslighetsnivå. Ange till exempel **Generera** avisering när  antalet frågeresultat är större än och ange numret 1000 om du vill att regeln endast ska generera en avisering om frågan returnerar fler än 1 000 resultat varje gång den körs. Det här är ett obligatoriskt fält, så om du inte vill ange ett tröskelvärde – det vill säga om du vill att aviseringen ska registrera varje händelse – anger du 0 i nummerfältet.
    
### <a name="results-simulation"></a>Resultatsimulering

I området **Resultatsimulering** till höger i guiden väljer du Testa med aktuella **data** så visar Azure Sentinel ett diagram över resultaten (logghändelser) som frågan skulle ha genererat under de senaste 50 gånger som den skulle ha körts, enligt det aktuella definierade schemat. Om du ändrar frågan väljer du **Testa med aktuella data** igen för att uppdatera diagrammet. Diagrammet visar antalet resultat under den definierade tidsperioden, vilket bestäms av inställningarna i avsnittet **Frågeschemaläggning.**
  
Så här kan resultatsimuleringen se ut för frågan i skärmbilden ovan. Den vänstra sidan är standardvyn och den högra sidan är det du ser när du hovrar över en tidpunkt i diagrammet.

:::image type="content" source="media/tutorial-detect-threats-custom/results-simulation.png" alt-text="Skärmbilder av resultatsimulering":::

Om du ser att din fråga utlöser för många eller för  frekventa  [](#query-scheduling-and-alert-threshold) aviseringar kan du experimentera med inställningarna i avsnitten Schemaläggning av fråga och Tröskelvärde för avisering och välja Testa med **aktuella data** igen.

### <a name="event-grouping-and-rule-suppression"></a>Händelsegruppering och regelundertryckning

> [!IMPORTANT]
> Händelsegruppering är för närvarande i **FÖRHANDSVERSION.** Se kompletterande [användningsvillkor för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts för allmän tillgänglighet.
    
- Under **Händelsegruppering** väljer du ett av två sätt att hantera gruppering av **händelser** i **aviseringar:** 

    - **Gruppera alla händelser i en enda avisering** (standardinställningen). Regeln genererar en enskild avisering varje gång den körs, så länge frågan returnerar fler resultat än det angivna **tröskelvärdet för aviseringen** ovan. Aviseringen innehåller en sammanfattning av alla händelser som returneras i resultatet. 

    - **Utlös en avisering för varje händelse**. Regeln genererar en unik avisering för varje händelse som returneras av frågan. Detta är användbart om du vill att händelser ska visas individuellt, eller om du vill gruppera dem efter vissa parametrar – efter användare, värdnamn eller något annat. Du kan definiera dessa parametrar i frågan.
    
        För närvarande är maxgränsen 20 för antalet aviseringar som en regel kan generera. Om **händelsegruppering** i en viss regel är inställt på Utlösa en avisering för varje händelse **och** regelns fråga returnerar fler än 20 händelser genererar var och en av de första 19 händelserna en unik avisering och den 20:e aviseringen sammanfattar hela uppsättningen returnerade händelser. Med andra ord är den 20:e aviseringen det som skulle ha genererats under alternativet **Gruppera alla händelser i en enda avisering.**

    > [!NOTE]
    > Vad är skillnaden mellan händelser **och** **aviseringar?**
    >
    > - En **händelse** är en beskrivning av en enskild förekomst av en åtgärd. En enda post i en loggfil kan till exempel räknas som en händelse. I det här sammanhanget refererar en händelse till ett enskilt resultat som returneras av en fråga i en analysregel.
    >
    > - En **avisering** är en samling händelser som tillsammans är viktiga ur säkerhetssynpunkt. En avisering kan innehålla en enda händelse om händelsen har betydande säkerhetskonsekvenser – till exempel en administrativ inloggning från ett främmande land utanför kontorstid.
    >
    > - Vad är **incidenter?** Azure Sentinel interna logik skapar incidenter **från** **aviseringar eller** grupper av aviseringar. Incidentkön är huvudpunkten i SOC-analytikernas arbete – att undersöka, undersöka och åtgärda.
    > 
    > Azure Sentinel matar in råhändelser från vissa datakällor och redan bearbetade aviseringar från andra. Det är viktigt att notera vilken du arbetar med när som helst.

- I avsnittet **Undertryckning** kan  du ställa in inställningen Stoppa körning efter att aviseringen har genererats **På** om du vill inaktivera åtgärden för den här regeln under en tidsperiod som överstiger frågeintervallet när du får en avisering. Om du aktiverar det här  måste du ställa in Frågan Sluta köra under till hur lång tid frågan ska sluta köras, upp till 24 timmar.

## <a name="configure-the-incident-creation-settings"></a>Konfigurera inställningarna för att skapa incidenter

På fliken **Incidentinställningar** kan du välja om och hur aviseringar Azure Sentinel till åtgärdsbara incidenter. Om den här fliken lämnas Azure Sentinel skapar en enda, separat incident från varje avisering. Du kan välja att inte skapa några incidenter eller gruppera flera aviseringar i en enda incident genom att ändra inställningarna på den här fliken.

> [!IMPORTANT]
> Fliken incidentinställningar är för närvarande i **FÖRHANDSVERSION.** Se kompletterande [användningsvillkor för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Microsoft Azure-förhandsversioner för ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts i allmän tillgänglighet.

:::image type="content" source="media/tutorial-detect-threats-custom/incident-settings-tab.png" alt-text="Definiera inställningar för att skapa incidenter och gruppera aviseringar":::

### <a name="incident-settings"></a>Incidentinställningar

I avsnittet **Incidentinställningar** är Skapa incidenter från aviseringar som utlöses av den här analysregeln som standard inställt på **Aktiverad,** vilket innebär att Azure Sentinel skapar en enda, separat incident från varje avisering som utlöses av regeln. 
    
- Om du inte vill att den här regeln ska leda till att några incidenter skapas (till exempel om den här regeln bara är att samla in information för efterföljande analys) anger du detta till **Inaktiverad**.

- Om du vill att en enskild incident ska skapas från en grupp med aviseringar, i stället för en för varje enskild avisering, kan du gå till nästa avsnitt.

### <a name="alert-grouping"></a>Aviseringsgrupp

Om  du vill att en enskild incident ska genereras från en grupp med upp till 150 liknande eller återkommande aviseringar (se anmärkning) i avsnittet Aviseringsgrupp anger du Grupprelaterade aviseringar **som** utlöses av den här analysregeln till Incidenter till **Aktiverad** och anger följande parametrar.

- **Begränsa gruppen till aviseringar som skapats inom** den valda tidsramen: Bestäm inom vilken tidsram liknande eller återkommande aviseringar ska grupperas. Alla motsvarande aviseringar inom den här tidsramen genererar tillsammans en incident eller en uppsättning incidenter (beroende på gruppinställningarna nedan). Aviseringar utanför den här tidsramen genererar en separat incident eller uppsättning incidenter.

- **Gruppera aviseringar som utlöses av den här analysregeln i en enda incident av**: Välj på vilken grund aviseringarna ska grupperas:

    - **Gruppera aviseringar i en enda incident** om alla entiteter matchar : Aviseringar grupperas tillsammans om de delar identiska värden för var och en av de mappade entiteterna (definieras på fliken Ange regellogik ovan). Detta är den rekommenderade inställningen.

    - **Gruppera alla aviseringar som utlöses av den** här regeln i en enda incident: Alla aviseringar som genereras av den här regeln grupperas tillsammans även om de inte delar några identiska värden.

    - **Gruppera aviseringar i en enda incident** om de valda entiteterna matchar : Aviseringar grupperas tillsammans om de delar identiska värden för vissa av de mappade entiteterna (som du kan välja i listrutan). Du kanske vill använda den här inställningen om du till exempel vill skapa separata incidenter baserat på källans eller målets IP-adresser.

- **Öppna** stängda matchande incidenter igen: Om en incident har lösts och stängts, och senare genereras  en annan avisering som ska tillhöra  incidenten, ställer du in den här inställningen på Aktiverad om du vill att den stängda incidenten ska öppnas igen och lämnar som Inaktiverad om du vill att aviseringen ska skapa en ny incident.
    
    > [!NOTE]
    > **Upp till 150 aviseringar** kan grupperas i en enda incident. Om fler än 150 aviseringar genereras av en regel som grupperar dem i en enda incident genereras en ny incident med samma incidentinformation som den ursprungliga, och de överflödiga aviseringarna grupperas i den nya incidenten.

## <a name="set-automated-responses-and-create-the-rule"></a>Ange automatiserade svar och skapa regeln

1. På fliken **Automatiserade svar** väljer du alla spelböcker som du vill köra automatiskt när en avisering genereras av den anpassade regeln. Mer information om hur du skapar och automatiserar spelböcker finns [i Svara på hot.](tutorial-respond-threats-playbook.md)

    :::image type="content" source="media/tutorial-detect-threats-custom/automated-response-tab.png" alt-text="Definiera inställningarna för automatiska svar":::

1. Välj **Granska och skapa för** att granska alla inställningar för den nya aviseringsregeln. När meddelandet "Valideringen har godkänts" visas väljer du **Skapa för** att initiera aviseringsregeln.

    :::image type="content" source="media/tutorial-detect-threats-custom/review-and-create-tab.png" alt-text="Granska alla inställningar och skapa regeln":::

## <a name="view-the-rule-and-its-output"></a>Visa regeln och dess utdata
  
- Du hittar din nyligen skapade anpassade regel (av typen "Schemalagd") i tabellen under fliken **Aktiva** regler på **huvudskärmen i** Analytics. I den här listan kan du aktivera, inaktivera eller ta bort varje regel.

- Om du vill visa resultatet av de  aviseringsregler som du skapar går du till sidan Incidenter, där du kan [granska,](tutorial-investigate-cases.md)undersöka incidenter och åtgärda hoten.

> [!NOTE]
> Aviseringar som genereras i Azure Sentinel är tillgängliga [via Microsoft Graph Security](/graph/security-concept-overview). Mer information finns i dokumentationen [Microsoft Graph säkerhetsaviseringar.](/graph/api/resources/security-api-overview)

## <a name="troubleshooting"></a>Felsökning

### <a name="issue-no-events-appear-in-query-results"></a>Problem: Inga händelser visas i frågeresultat

Om **händelsegruppering** är inställt på att utlösa en avisering för varje händelse är det i vissa fall möjligt att inga frågeresultat visas när du visar frågeresultatet vid ett senare tillfälle (till exempel när du pivoterar tillbaka till aviseringar från en incident). Det beror på att händelsens anslutning till aviseringen åstadkoms genom hashing av den specifika händelsens information och inkluderingen av hashen i frågan. Om frågeresultatet har ändrats sedan aviseringen genererades är hashen inte längre giltig och inga resultat visas. 

Om du vill se händelserna tar du bort raden med hashen manuellt från regelns fråga och kör frågan.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problem: En schemalagd regel kunde inte köras eller visas med AUTOMATISK INAKTIVERAD tillagd i namnet

Det är en sällsynt förekomst att en schemalagd frågeregel inte kan köras, men det kan inträffa. Azure Sentinel fel direkt som tillfälliga eller permanenta, baserat på den specifika typen av fel och de omständigheter som ledde till det.

#### <a name="transient-failure"></a>Tillfälligt fel

Ett tillfälligt fel inträffar på grund av en tillfällig situation som snart kommer att återgå till det normala, då regelkörningen lyckas. Några exempel på fel som Azure Sentinel klassificerar som tillfälliga är:

- En regelfråga tar för lång tid att köra och tar för lång tid.
- Anslutningsproblem mellan datakällor och Log Analytics, eller mellan Log Analytics och Azure Sentinel.
- Alla andra nya och okända fel betraktas som tillfälliga.

Vid ett tillfälligt fel fortsätter Azure Sentinel att försöka köra regeln igen efter förbestämda och ständigt ökande intervall, upp till en punkt. Efter det körs regeln bara igen vid nästa schemalagda tidpunkt. En regel inaktiveras aldrig automatiskt på grund av ett tillfälligt fel.

#### <a name="permanent-failure---rule-auto-disabled"></a>Permanent fel – regeln inaktiveras automatiskt

Ett permanent fel inträffar på grund av en ändring av villkoren som tillåter att regeln körs, som utan mänsklig inblandning inte återgår till sin tidigare status. Följande är några exempel på fel som klassificeras som permanenta:

- Målarbetsytan (som regelfrågan körs på) har tagits bort.
- Måltabellen (som regelfrågan körs på) har tagits bort.
- Azure Sentinel har tagits bort från målarbetsytan.
- En funktion som används av regelfrågan är inte längre giltig. Den har antingen ändrats eller tagits bort.
- Behörigheter till en av datakällorna för regelfrågan har ändrats.
- En av datakällorna för regelfrågan har tagits bort eller kopplats från.

**I händelse av ett förbestämt antal på varandra följande permanenta fel, av samma typ och i samma regel,** Azure Sentinel slutar att försöka köra regeln och utför även följande steg:

- Inaktiverar regeln.
- Lägger till **orden "INAKTIVERAD AUTOMATISKT"** i början av regelns namn.
- Lägger till orsaken till felet (och inaktiveringen) i regelns beskrivning.

Du kan enkelt fastställa förekomsten av automatiskt inaktiverade regler genom att sortera regellistan efter namn. Reglerna för automatiskt inaktiverade är högst upp i listan eller nära dem.

SOC-hanterare bör se till att kontrollera regellistan regelbundet för att se om det finns regler för automatiskt inaktiverade.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med hjälp av Azure Sentinel.

- Lär dig hur du [undersöker incidenter i Azure Sentinel](tutorial-investigate-cases.md).
- Läs mer om [entiteter i Azure Sentinel](entities-in-azure-sentinel.md).
- Lär dig hur [du ställer in automatiserade hotsvar i Azure Sentinel](tutorial-respond-threats-playbook.md).
