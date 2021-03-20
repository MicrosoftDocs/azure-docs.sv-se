---
title: Använd jobb webbläsare & jobb Visa Azure Data Lake Analytics
description: I den här artikeln beskrivs hur du använder vyn jobb webbläsare och jobb för Azure Data Lake Analytics jobb.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 08/02/2017
ms.openlocfilehash: a1e9a9df4c2ec57dfeec8cf5ddd5348228b9cc3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018570"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Använda jobbwebbläsaren och jobbvyn för Azure Data Lake Analytics
Azure Data Lake Analytics tjänst arkiverar jobb som skickats i ett frågearkivet. I den här artikeln får du lära dig hur du använder jobb webbläsare och jobb visning i Azure Data Lake verktyg för Visual Studio för att hitta den historiska jobb informationen. 

Som standard arkiveras jobben i Data Lake Analyticss tjänsten i 30 dagar. Du kan konfigurera förfallo perioden från Azure Portal genom att konfigurera den anpassade utgångs principen. Du kommer inte att kunna komma åt jobb informationen efter förfallo datum. 

## <a name="prerequisites"></a>Förutsättningar
Se [data Lake verktyg för Visual Studio-krav](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Öppna jobb webbläsaren
Få åtkomst till jobb läsaren via **Server Explorer>Azure>Data Lake Analytics>jobb** i Visual Studio.  Med hjälp av jobb läsaren kan du komma åt frågearkivet för ett Data Lake Analytics-konto. I jobb webbläsaren visas Frågearkivet till vänster, som visar grundläggande jobb information och jobb visning till höger som visar detaljerad jobb information.

## <a name="job-view"></a>Jobb visning
Vyn jobb visar detaljerad information om ett jobb. Du kan öppna ett jobb genom att dubbelklicka på ett jobb i jobb webbläsaren eller öppna det från Data Lake-menyn genom att klicka på jobb visning. Du bör se en dialog ruta med jobb-URL: en.

![Data Lake verktyg Visual Studio Job Browser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Vyn jobb innehåller:

* Jobbsammanfattning
  
    Uppdatera vyn jobb om du vill se mer information om pågående jobb.
  
  * Jobb status (graf):
    
      Jobb status beskriver jobb faserna:
    
      ![Skärm bild som visar Azure Data Lake Analytics jobb faserna.](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Förbereda: Ladda upp skriptet till molnet, kompilera och optimera skriptet med compile-tjänsten.
    * Köade: jobb placeras i kö när de väntar på tillräckligt med resurser, eller så överskrider jobben högsta antalet samtidiga jobb per konto begränsning. Prioritets inställningen bestämmer ordningen för köade jobb – ju lägre siffra, desto högre prioritet.
    * Körs: jobbet körs faktiskt i ditt Data Lake Analytics-konto.
    * Slutför: jobbet slutförs (till exempel Slutför filen).
      
      Jobbet kan inte köras i varje fas. Till exempel, kompileringsfel i fasen förberedelse, timeout-fel i den köade fasen och körnings fel i fasen körs osv.
  * Grundläggande information
    
      Den grundläggande jobb informationen visas i den nedre delen av panelen jobb Sammanfattning.
    
      ![Skärm bild som visar jobb sammanfattningen med beskrivningar i text rutor.](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Jobb resultat: lyckades eller misslyckades. Jobbet kan Miss lyckas i varje fas.
    * Total varaktighet: väggens klock slag (varaktighet) mellan att skicka in tid och slut tid.
    * Total beräknings tid: summan av varje hörn körnings tid, du kan betrakta det som den tid som jobbet körs i endast ett hörn. Se det totala antalet hörn för att hitta mer information om vertex.
    * Sändnings-/start-/slut tid: den tidpunkt då Data Lake Analytics tjänsten tar emot jobb sändningen/börjar köra jobbet/avslutar jobbet eller inte.
    * Kompilering/köade/aktiva: vägg Clock-tid som tillbringas under fasen för förberedelse/köade/körning.
    * Konto: det Data Lake Analytics konto som används för att köra jobbet.
    * Författare: användaren som skickade jobbet kan vara en riktig persons konto eller ett system konto.
    * Prioritet: jobbets prioritet. Ju lägre siffra, desto högre prioritet. Den påverkar bara ordningen på jobben i kön. Om du anger en högre prioritet åsidosätts inte jobb som körs.
    * Parallelitet: det begärda maximala antalet samtidiga Azure Data Lake Analyticss enheter (ADLAUs), även kallat formhörn. För närvarande är ett hörn lika med en virtuell dator med två virtuella kärnor och sex GB RAM-minne, men det kan uppgraderas i framtida Data Lake Analytics uppdateringar.
    * Byte kvar: byte som måste bearbetas tills jobbet har slutförts.
    * Lästa byte/skrivna byte: byte som har lästs/skrivits sedan jobbet startades.
    * Totalt antal hörn: jobbet är uppdelat i många olika arbets delar kallas varje arbets plats. Det här värdet beskriver hur många delar av arbetet som jobbet består av. Du kan betrakta ett formhörn som en Basic process Unit, även kallat Azure Data Lake Analytics Unit (ADLAU) och formhörn kan köras parallellt. 
    * Slutförd/körs/misslyckades: antalet slutförda/aktiva/misslyckade hörn. Hörnen kan Miss lyckas på grund av både användar kod och systemfel, men systemet försöker igen formhörn automatiskt några gånger. Om hörnen fortfarande Miss lyckas efter ett nytt försök kommer hela jobbet att Miss lyckas.
* Jobb diagram
  
    Ett U-SQL-skript representerar logiken för att transformera indata till utdata. Skriptet kompileras och optimeras till en fysisk körnings plan vid förberedelse fasen. Jobb diagram är att visa den fysiska körnings planen.  Följande diagram illustrerar processen:
  
    ![Status för Azure Data Lake Analytics jobb faser](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Ett jobb är uppdelat i många olika arbets delar. Varje arbets del kallas för ett formhörn. Hörnen är grupperade som överordnade hörn (även kallade Stage) och visualiserade som jobb diagram. Den gröna scenen placards i jobb diagrammet visar stegen.
  
    Varje hörn i en fas gör samma typ av arbete med olika delar av samma data. Om du till exempel har en fil med en TB-data, och det finns hundratals hörn som läser från den, så läser var och en av dem ett segment. Dessa hörn är grupperade i samma steg och utför samma arbete på olika delar av samma indatafil.
  
  * <a name="state-information"></a>Information om steg
    
      I ett visst skede visas vissa tal i Placard.
    
      ![Diagram steg för Azure Data Lake Analytics jobb](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1-utdrag: namnet på en fas, med ett tal och en åtgärds metod.
    * 84 hörn: det totala antalet hörn i det här steget. Bilden visar hur många delar av arbetet som delas i det här steget.
    * 12,90 s/hörn: genomsnittlig körnings tid för hörn för det här steget. Den här bilden beräknas som SUM (varje hörn körnings tid)/(totalt antal hörn). Det innebär att om du kan tilldela alla formhörn som körs parallellt, slutförs hela fasen i 12,90 s. Det innebär också att om allt arbete i det här steget görs seriellt, blir kostnaden #vertices * genomsnittlig tid.
    * 850 895 rader skrivna: totalt antal rader skrivet i det här steget.
    * R/W: mängden data som läses/skrivs i det här skedet i byte.
    * Färger: färger används i scenen för att ange olika hörn status.
      
      * Grönt anger att toppen är klar.
      * Orange anger att hörnen har gjorts om. Det nya försöket att markera Bryt punkten misslyckades, men ett nytt försök görs automatiskt och lyckas av systemet, och det övergripande steget har slutförts. Om du har försökt att ändra hörnen men ändå har misslyckats, blir färgen röd och hela jobbet misslyckades.
      * Röd indikerar misslyckad, vilket innebär att ett visst formhörn har återtestats några gånger av systemet men fortfarande misslyckats. Det här scenariot gör att hela jobbet Miss fungerar.
      * Blått innebär att ett visst formhörn körs.
      * White anger att hörnen väntar. Bryt punkten kan vänta att schemaläggas när en ADLAU blir tillgänglig, eller så kan den vänta på indata eftersom indata kanske inte är klara.
      
      Du kan hitta mer information om scenen genom att hovra mus markören med ett tillstånd:
      
      ![Information om Azure Data Lake Analytics jobb diagram steg](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Hörn: beskriver information om hörnen, t. ex. hur många hörn som sammanlagts, hur många formhörn som har slutförts, är de misslyckade eller fortfarande körs/väntar, osv.
  * Data läsning mellan/intra pod: filer och data lagras i flera poddar i DFS (Distributed File System). Värdet här beskriver hur mycket data som har lästs in i samma POD eller kors pod.
  * Total beräknings tid: summan av varje hörn körnings tid i fasen. du kan betrakta det som den tid det skulle ta om allt arbete i scenen körs i endast ett hörn.
  * Skrivna/lästa data och rader: anger hur mycket data eller rader som har lästs/skrivits eller behöver läsas.
  * Hörn läsnings fel: beskriver hur många formhörn som misslyckades vid läsning av data.
  * Dubbletter av hörn: om ett hörn körs för långsamt kan systemet schemalägga flera hörn för att köra samma arbets typ. Återdragnings hörnen tas bort när ett av hörnen har slutförts. Dubbletter av formhörn registrerar antalet hörn som tas bort som dupliceringar i scenen.
  * Hörn åter kallelser: hörnen lyckades, men kom igång igen senare på grund av några skäl. Om till exempel den underordnade Bryt punkten förlorar mellanliggande indata, kommer den att be överordnade Bryt punkten att köras igen.
  * Schema körningar hörn: den totala tiden som hörnen har schemalagts.
  * Minsta/Genomsnittligt/högsta antal hörn data lästa: minsta/medelvärdet/maximum för varje formhörn läsa data.
  * Varaktighet: väggens klock frekvens som en fas tar, måste du läsa in profilen för att se det här värdet.
  * Jobbuppspelning
    
      Data Lake Analytics Kör jobb och arkiverar hörnen som kör information om jobben, t. ex. När hörnen startas, stoppas, misslyckades och hur de provas igen osv. All information loggas automatiskt i frågearkivet och lagras i jobb profilen. Du kan ladda ned jobb profilen via "Läs in profil" i projektvyn och du kan visa jobb uppspelningen när du har laddat ned jobb profilen.
    
      Jobb uppspelning är en Epitome visualisering av vad som hände i klustret. Det hjälper dig att titta på jobb körnings förlopp och visuellt identifiera prestanda avvikelser och Flask halsar på ett mycket kort klock slag (mindre än 30 s vanligt vis).
  * Visa termisk karta för jobb 
    
      Du kan välja jobb värme karta genom att Visa List rutan i jobb diagrammet. 
    
      ![Visa bild visning för Azure Data Lake Analytics jobb diagrams heap](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Den visar i/O, tid och flödes värme karta för ett jobb, som du kan använda för att ta reda på var jobbet tillbringar mest av tiden, eller om ditt jobb är ett I/O-gräns jobb, och så vidare.
    
      ![Exempel på Azure Data Lake Analytics jobb diagrams heap-karta](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Förlopp: jobb körnings processen finns i information i steg-för-steg-information.
    * Lästa/skrivna data: värme kartan över total data läsning/skrivning i varje steg.
    * Beräknings tid: den termiska kartan för SUM (varje hörn körnings tid), du kan tänka på detta så länge det tar om allt arbete i scenen körs med endast 1 hörn.
    * Genomsnittlig körnings tid per nod: den termiska kartan för SUM (varje hörn körnings tid)/(hörn nummer). Vilket innebär att om du kan tilldela alla formhörn som körs parallellt, utförs hela fasen under den här tids perioden.
    * Data flöde för indata/utdata: värme kartan över data flödet för indata/utdata i varje steg, du kan bekräfta om jobbet är ett I/O-kopplat jobb genom detta.
* Metadata-åtgärder
  
    Du kan utföra vissa metadata åtgärder i U-SQL-skriptet, till exempel skapa en databas, ta bort en tabell osv. De här åtgärderna visas vid metadata-åtgärd efter kompilering. Du kan se om det finns kontroller, skapa entiteter och ta bort entiteter här.
  
    ![Åtgärder för metadata för Azure Data Lake Analytics jobb visning](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Tillstånds historik
  
    Tillstånds historiken visualiseras också i jobb sammanfattning, men du kan få mer information här. Du hittar den detaljerade informationen, till exempel när jobbet förbereds, i kö, startas, körs, avslutas. Du kan också se hur många gånger jobbet har kompilerats (CcsAttempts: 1) när jobbet skickas till klustret faktiskt (Detaljer: skicka jobb till kluster) osv.
  
    ![Azure Data Lake Analytics tillstånds historik för jobb visning](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostik
  
    Verktyget diagnostiserar jobb körningen automatiskt. Du får aviseringar när det finns fel eller prestanda problem i dina jobb. Observera att du måste ladda ned profilen för att få fullständig information här. 
  
    ![Azure Data Lake Analytics diagnostik för jobb visning](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Varningar: en avisering visas här med en kompilator varning. Du kan klicka på länken "x Issue (s)" om du vill ha mer information när aviseringen visas.
  * Vertex-körningen är för lång: om ett formhörn tar slut i tiden (t. ex. 5 timmar) kommer du att hitta problem här.
  * Resursanvändning: om du har tilldelat mer eller inte tillräckligt med parallellitet än behov kommer du att hitta problem här. Du kan också klicka på resursanvändning om du vill se mer information och utföra konsekvens scenarier för att hitta en bättre resursallokering (mer information finns i den här hand boken).
  * Minnes kontroll: om ett formhörn använder mer än 5 GB minne, kommer du att hitta problem här. Jobb körningen kan komma att avslutas av systemet om det använder mer minne än system begränsningen.

## <a name="job-detail"></a>Jobb information
Jobb information visar detaljerad information om jobbet, inklusive skript, resurser och hörn körnings vy.

![Information om Azure Data Lake Analytics jobb](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    U-SQL-skriptet för jobbet lagras i frågearkivet. Du kan visa det ursprungliga U-SQL-skriptet och skicka det igen om det behövs.
* Resurser
  
    Du hittar de utdata för jobb kompilering som lagras i frågearkivet via resurser. Du kan till exempel hitta "algebra.xml" som används för att visa jobb diagrammet, de sammansättningar som du har registrerat osv.
* Körnings vy för hörn
  
    Den visar information om körning av formhörn. Jobb profilen arkiverar varje form av körnings logg, till exempel total läsning/skrivning av data, körning, tillstånd osv. I den här vyn kan du få mer information om hur ett jobb körs. Mer information finns i [Använd vyn hörn körning i data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Nästa steg
* Information om hur du loggar diagnostikinformation finns i [Åtkomst till diagnostikloggar för Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Om du vill se en mer komplex fråga, se [Analysera webbplatsloggar med hjälp av Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Om du vill använda körnings visning för hörn, se [Använd vyn hörn körning i data Lake verktyg för Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

