---
title: Skapa anpassade analys regler för att identifiera hot med Azure Sentinel | Microsoft Docs
description: Använd den här självstudien för att lära dig att skapa anpassade analys regler för att identifiera säkerhetshot med Azure Sentinel. Dra nytta av händelse gruppering och aviserings gruppering och förstå automatisk inaktive rad.
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 390d2c8488fd2b35c775eabe43677b9349b547a1
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401652"
---
# <a name="tutorial-create-custom-analytics-rules-to-detect-threats"></a>Självstudie: skapa anpassade analys regler för att identifiera hot

När du har [anslutit dina data källor](quickstart-onboard.md) till Azure Sentinel kan du skapa anpassade regler som kan söka efter vissa kriterier i din miljö och generera incidenter när villkoren matchas så att du kan undersöka dem. Den här självstudien hjälper dig att skapa anpassade regler för att identifiera hot med Azure Sentinel.

Den här självstudien hjälper dig att identifiera hot med Azure Sentinel.
> [!div class="checklist"]
> * Skapa analys regler
> * Automatisera hot svar

## <a name="create-custom-analytics-rules"></a>Skapa anpassade analys regler

Du kan skapa anpassade analys regler som hjälper dig att söka efter de typer av hot och avvikelser som är misstänkta i din miljö. Regeln säkerställer att du meddelas direkt, så att du kan prioritering, undersöka och åtgärda hoten.

1. I Azure Portal under Azure Sentinel väljer du **analys**.

1. I den översta meny raden väljer du **+ skapa** och väljer **schemalagd frågeregel**. Då öppnas **guiden Analytics-regel**.

    :::image type="content" source="media/tutorial-detect-threats-custom/create-scheduled-query.png" alt-text="Skapa schemalagd fråga":::

1. Ange ett unikt **namn** och en **Beskrivning** på fliken **Allmänt** . I fältet **taktiker** kan du välja bland de typer av attacker som regeln ska klassificeras efter. Ange **allvarlighets grad** för aviseringen vid behov. När du skapar regeln **aktive ras** **statusen** som standard, vilket innebär att den körs omedelbart när du har skapat den. Om du inte vill att den ska köras omedelbart väljer du **inaktive rad** och regeln läggs till på fliken **aktiva regler** och du kan aktivera den därifrån när du behöver den.

    ![Börja skapa en anpassad analys regel](media/tutorial-detect-threats-custom/general-tab.png)

1. På fliken **Ange regel logik** kan du antingen skriva en fråga direkt i fältet **regel fråga** eller skapa frågan i Log Analytics och sedan kopiera och klistra in den där.
 
   ![Skapa fråga i Azure Sentinel](media/tutorial-detect-threats-custom/settings-tab.png)

   - Se avsnittet för **förhands granskning av resultat** till höger, där Azure Sentinel visar antalet resultat (logg händelser) som frågan genererar, ändras när du skriver och konfigurerar frågan. Diagrammet visar antalet resultat under den angivna tids perioden, vilket bestäms av inställningarna i avsnittet för **fråge schemaläggning** .
    - Om du ser att frågan skulle utlösa för många eller för frekventa aviseringar kan du ange en bas linje i avsnittet **aviserings tröskel** .

      Här är en exempel fråga som varnar dig när ett avvikande antal resurser skapas i Azure Activity.

      ```kusto
      AzureActivity
      | where OperationName == "Create or Update Virtual Machine" or OperationName =="Create Deployment"
      | where ActivityStatus == "Succeeded"
      | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller
      ```

        > [!NOTE]
        > - Frågans längd ska vara mellan 1 och 10 000 tecken och får inte innehålla "search \* " eller "union \* ".
        >
        > - Det **går inte** att använda ADX-funktioner för att skapa Azure datautforskaren frågor i Log Analytics frågefönstret.

    1. Använd avsnittet **Mappa entiteter** för att länka parametrar från frågeresultaten till Azure Sentinel-identifierade entiteter. Dessa entiteter utgör grunden för ytterligare analys, inklusive gruppering av aviseringar till incidenter på fliken **incident inställningar** .
  
    1. Ange följande parametrar i avsnittet **fråge schemaläggning** :

       1. Ställ in **Kör fråga var** för att kontrol lera hur ofta frågan körs – så ofta som var 5: e minut eller så sällan som en gång om dagen.

       1. Ange **Sök data från den sista** för att fastställa tids perioden för de data som omfattas av frågan – till exempel kan den fråga de senaste 10 minuterna data eller de senaste 6 timmarna med data.

          > [!NOTE]
          > **Frågeintervall och lookback-period**
          > - Dessa två inställningar är oberoende av varandra, upp till en punkt. Du kan köra en fråga på ett kort intervall som täcker en tids period som är längre än intervallet (som har överlappande frågor), men du kan inte köra en fråga i ett intervall som överskrider perioden, annars kommer du att ha luckor i den övergripande frågan.
          >
          > **Inmatnings fördröjning**
          > - För att kunna utföra **svars tider** som kan uppstå mellan en händelses generation vid källan och dess inmatning i Azure Sentinel, och för att säkerställa fullständig täckning utan dataduplicering, kör Azure Sentinel schemalagda analys regler på en **fem minuters fördröjning** från deras schemalagda tid.

    1. Använd avsnittet **aviserings tröskel** för att definiera en bas linje. Ange till exempel **generera avisering när antalet frågeresultat** **är större än** och ange numret 1000 om du vill att regeln endast ska generera en avisering om frågan returnerar fler än 1000 resultat varje gång den körs. Det här fältet är obligatoriskt, så om du inte vill ange en bas linje – det vill säga om du vill att din avisering ska registrera varje händelse – anger du 0 i fältet tal.
    
    1. Välj ett av två sätt att hantera gruppering av **händelser** i **aviseringar** under **händelse gruppering**: 

       - **Gruppera alla händelser i en enda avisering** (standardinställningen). Regeln genererar en enskild avisering varje gång den körs, så länge frågan returnerar fler resultat än det angivna **tröskelvärdet för aviseringar** ovan. Aviseringen innehåller en sammanfattning av alla händelser som returneras i resultaten. 

       - **Utlös en avisering för varje händelse**. Regeln genererar en unik avisering för varje händelse som returneras av frågan. Detta är användbart om du vill att händelser ska visas individuellt eller om du vill gruppera dem efter vissa parametrar – av användare, värdnamn eller något annat. Du kan definiera dessa parametrar i frågan.
    
       För närvarande är antalet aviseringar som en regel kan generera är ett tak på 20. Om **händelse gruppering** är inställd på att **utlösa en avisering för varje händelse** i en viss regel, och regelns fråga returnerar fler än 20 händelser, genererar var och en av de första 19 händelserna en unik avisering och den tjugonde aviseringen sammanfattar hela uppsättningen returnerade händelser. 1900-aviseringen är med andra ord det som skulle ha genererats under **gruppen alla händelser till ett enda aviserings** alternativ.

       > [!NOTE]
       > Vad är skillnaden mellan **händelser** och **aviseringar**?
       >
       > - En **händelse** är en beskrivning av en enskild förekomst. Till exempel kan en enskild post i en loggfil räknas som en händelse. I den här kontexten refererar en händelse till ett enda resultat som returneras av en fråga i en analys regel.
       >
       > - En **avisering** är en samling händelser som samlas, och som sammanfattas av säkerhets synpunkt. En avisering kan innehålla en enskild händelse om händelsen hade betydande säkerhets konsekvenser – en administrativ inloggning från ett främmande land utanför kontors tid, till exempel.
       >
       > - Vad är hur är **incidenter**? Azure Sentinels interna logik skapar **incidenter** från **aviseringar** eller grupper med aviseringar. Incident kön är fokus punkten för analytiker "prioritering, undersökning och reparation.
       > 
       > Azure Sentinel inhämtar rå händelser från vissa data källor och bearbetas redan bearbetade aviseringar från andra. Det är viktigt att du noterar vilken som helst som du hanterar när du vill.

       > [!IMPORTANT]
       > Event Grouping är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. I avsnittet under **tryckning** kan du aktivera inställningen **stoppa frågan när aviseringen har genererats** **om,** när du får en avisering, om du vill inaktivera åtgärden för den här regeln under en tids period som överstiger frågeintervallet. Om du aktiverar det här alternativet måste du ange att frågan ska **sluta köras för** den tid som frågan ska sluta köras, upp till 24 timmar.

1. På fliken **incident inställningar** kan du välja om och hur Azure-kontroll aktiverar aviseringar till åtgärds bara incidenter. Om den här fliken lämnas ensam, kommer Azure Sentinel att skapa en enskild, separat incident från varje avisering. Du kan välja att inte skapa några incidenter eller gruppera flera aviseringar i en enda incident genom att ändra inställningarna på den här fliken.

   > [!IMPORTANT]
   > Fliken incident inställningar är för närvarande en offentlig för hands version. Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
    
    1. I avsnittet **incident inställningar** kan du **skapa incidenter från aviseringar som utlöses av den här analys regeln** anges som standard till **aktive rad**, vilket innebär att Azure Sentinel skapar en enskild, separat incident från var och en av de aviseringar som aktive ras av regeln.
       - Om du inte vill att regeln ska leda till att några incidenter skapas (till exempel om den här regeln bara samlar in information för efterföljande analyser), ställer du in den på **inaktive rad**.

    1. I avsnittet **aviserings gruppering** , om du vill att en enda incident ska genereras från en grupp upp till 150 liknande eller återkommande aviseringar (se OBS!) anger du **grupprelaterade aviseringar, utlöses av den här analys regeln, i incidenter** till **aktive rad** och anger följande parametrar.

    - **Begränsa gruppen till aviseringar som skapats inom den valda tids ramen**: Bestäm den tidsram inom vilken de liknande eller återkommande aviseringarna ska grupperas tillsammans. Alla motsvarande aviseringar inom den här tids ramen genererar en incident eller en uppsättning incidenter gemensamt (beroende på inställningarna för gruppering nedan). Aviseringar utanför den här tids ramen genererar en separat incident eller en uppsättning incidenter.

    - **Gruppera aviseringar som har utlösts av den här analys regeln i en enda incident genom**: Välj den grund som aviseringarna ska grupperas på:

        - **Gruppera aviseringar i en enda incident om alla entiteter matchar**: aviseringar grupperas tillsammans om de delar identiska värden för var och en av de mappade entiteterna (definieras på fliken ange regel logik ovan). Detta är den rekommenderade inställningen.

        - **Gruppera alla aviseringar som har utlösts av den här regeln i en enda incident**: alla aviseringar som genereras av den här regeln grupperas tillsammans även om de inte delar några identiska värden.

        - **Gruppera aviseringar i en enda incident om de valda entiteterna matchar**: aviseringar grupperas tillsammans om de delar identiska värden för några av de mappade entiteter (som du kan välja i list rutan). Du kanske vill använda den här inställningen om du till exempel vill skapa separata incidenter baserat på käll-eller mål-IP-adresser.

    - **Öppna stängda matchnings incidenter på nytt**: om en incident har lösts och stängts, och senare i en annan avisering skapas som ska tillhöra den incidenten, ställer du in inställningen på **aktive rad** om du vill att den stängda incidenten ska öppnas igen och lämnar den **inaktive rad** om du vill att aviseringen ska skapa en ny incident.
    
        > [!NOTE]
        > Upp till 150-aviseringar kan grupperas i en enda incident. Om fler än 150 aviseringar genereras av en regel som grupperar dem till en enda incident, genereras en ny incident med samma incident information som originalet och de överflödiga aviseringarna grupperas i den nya incidenten.

1. På fliken **automatiserade svar** väljer du de spel böcker som du vill köra automatiskt när en avisering genereras av den anpassade regeln. Mer information om hur du skapar och automatiserar spel böcker finns i [svara på hot](tutorial-respond-threats-playbook.md).

1. Välj **Granska och skapa** för att granska alla inställningar för den nya varnings regeln och välj sedan **skapa för att initiera aviserings regeln**.
  
1. När aviseringen har skapats läggs en anpassad regel till i tabellen under **aktiva regler**. I den här listan kan du aktivera, inaktivera eller ta bort varje regel.

1. Om du vill visa resultaten av de aviserings regler som du skapar går du till sidan **incidenter** där du kan prioritering, [undersöka incidenter](tutorial-investigate-cases.md)och åtgärda hoten.


> [!NOTE]
> Aviseringar som genereras i Azure Sentinel är tillgängliga via [Microsoft Graph säkerhet](/graph/security-concept-overview). Mer information finns i dokumentationen för [Microsoft Graph säkerhets aviseringar](/graph/api/resources/security-api-overview).

## <a name="troubleshooting"></a>Felsökning

### <a name="issue-no-events-appear-in-query-results"></a>Problem: inga händelser visas i frågeresultaten

Om **händelse gruppering** är inställt på att **utlösa en avisering för varje händelse**, kan det hända att inga frågeresultat visas i vissa situationer när du visar frågeresultaten vid ett senare tillfälle (till exempel vid pivotering av aviseringar från en incident). Detta beror på att händelsens anslutning till aviseringen uppnås genom hashing av den specifika händelsens information och inkludering av hash i frågan. Om frågeresultaten har ändrats sedan aviseringen genererades, kommer hashvärdet inte längre att vara giltigt och inga resultat visas. 

Om du vill se händelserna tar du bort raden manuellt med hash-värdet från regelns fråga och kör frågan.

### <a name="issue-a-scheduled-rule-failed-to-execute-or-appears-with-auto-disabled-added-to-the-name"></a>Problem: en schemalagd regel kunde inte köras eller visas med automatiskt inaktive rad tillagd till namnet

Det är en sällsynt förekomst att en schemalagd frågeregel inte kan köras, men det kan inträffa. Azure Sentinel klassificerar ett problem som antingen är tillfälligt eller permanent, baserat på den särskilda typen av haveri och de förhållanden som ledde till den.

#### <a name="transient-failure"></a>Tillfälligt haveri

Ett tillfälligt fel uppstår på grund av en omständighet som är temporär och kommer snart att gå tillbaka till normal, och då kommer regel körningen att lyckas. Några exempel på problem som Azure Sentinel klassificerar som tillfälliga är:

- En regel fråga tar för lång tid att köra och tids gränsen uppnås.
- Anslutnings problem mellan data källor och Log Analytics, eller mellan Log Analytics och Azure Sentinel.
- Alla andra nya och okända haverier betraktas som tillfälliga.

I händelse av ett tillfälligt haveri fortsätter Azure Sentinel att försöka köra regeln igen efter att ha fördefinierat och ständigt ökande intervall, upp till en punkt. Därefter körs regeln bara igen vid nästa schemalagda tidpunkt. En regel kommer aldrig att inaktive ras automatiskt på grund av ett tillfälligt haveri.

#### <a name="permanent-failure---rule-auto-disabled"></a>Permanent haveri-regel automatiskt inaktive rad

Ett permanent fel inträffar på grund av en ändring i villkoren som tillåter att regeln körs, vilket utan mänsklig inblandning inte kommer att återgå till sin tidigare status. Följande är några exempel på problem som klassificeras som permanenta:

- Mål arbets ytan (som regel frågan använder) har tagits bort.
- Mål tabellen (som regeln frågan använder) har tagits bort.
- Azure Sentinel har tagits bort från mål arbets ytan.
- En funktion som används av regel frågan är inte längre giltig. den har antingen ändrats eller tagits bort.
- Behörigheter till en av data källorna i regel frågan ändrades.
- En av regel frågans data källor har tagits bort eller kopplats från.

**I händelse av ett förbestämt antal permanenta avbrott i följd, av samma typ och i samma regel,** Azure Sentinel slutar att försöka köra regeln och utför även följande steg:

- Inaktiverar regeln.
- Lägger till ordet **"automatiskt INaktive rad"** i början av regelns namn.
- Lägger till orsaken till problemet (och inaktive ring) i regelns beskrivning.

Du kan enkelt bestämma förekomsten av eventuella regler som är automatiskt inaktiverade genom att sortera regel listan efter namn. De automatiskt inaktiverade reglerna kommer att finnas i eller längst upp i listan.

SOC-ansvariga bör kontrol lera regel listan regelbundet för förekomst av regler för automatisk inaktive rad.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kommer igång med att identifiera hot med Azure Sentinel.

Om du vill lära dig hur du automatiserar dina svar på hot [ställer du in automatiserade hot svar i Azure Sentinel](tutorial-respond-threats-playbook.md).