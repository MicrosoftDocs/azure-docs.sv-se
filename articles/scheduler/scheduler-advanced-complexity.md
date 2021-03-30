---
title: Bygg avancerade jobb scheman och upprepningar
description: Lär dig hur du skapar avancerade scheman och upprepningar för jobb i Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368187"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Bygg avancerade scheman och upprepningar för jobb i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt. 
>
> Scheduler är inte längre tillgänglig i Azure Portal, men [PowerShell-cmdletarna](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) och Azure Scheduler är tillgängliga just nu så att du kan hantera jobb och jobb samlingar.

I ett [Azure Scheduler](../scheduler/scheduler-intro.md) -jobb är schemat den kärna som avgör när och hur tjänsten Scheduler kör jobbet. Du kan konfigurera flera engångs-och återkommande scheman för ett jobb med Scheduler. Ett engångs schema körs bara en gång vid en angiven tidpunkt och är i princip återkommande scheman som bara körs en gång. Återkommande scheman körs enligt en angiven frekvens. Med den här flexibiliteten kan du använda Scheduler för olika affärs scenarier, till exempel:

* **Rensa data regelbundet**: skapa ett dagligt jobb som tar bort alla Tweets som är äldre än tre månader.

* **Arkivera data**: skapa ett månatligt jobb som skickar faktura historiken till en säkerhets kopierings tjänst.

* **Begär externa data**: skapa ett jobb som körs var 15: e minut och hämtar en ny väderleks rapport från NOAA.

* **Bearbeta avbildningar**: skapa ett arbets dags jobb som körs under låg belastnings tider och använder molnbaserad data behandling för att komprimera avbildningar som laddats upp under dagen.

I den här artikeln beskrivs exempel jobb som du kan skapa med hjälp av Scheduler och [Azure scheduler REST API](/rest/api/scheduler)och inkluderar JavaScript Object Notation (JSON)-definitionen för varje schema. 

## <a name="supported-scenarios"></a>Scenarier som stöds

I de här exemplen visas ett intervall med scenarier som Azure Scheduler stöder och hur du skapar scheman för olika beteende mönster, till exempel:

* Kör en gång vid ett visst datum och en angiven tidpunkt.
* Kör och upprepa ett angivet antal gånger.
* Kör omedelbart och upprepa.
* Kör och upprepa var *n:te* minut, timmar, dagar, veckor eller månader med början vid en angiven tidpunkt.
* Kör och upprepa varje vecka eller månad, men endast på vissa dagar i veckan eller på vissa dagar i månaden.
* Kör och upprepa mer än en gång under en viss period. Till exempel varje månad den senaste fredagen och måndagen, eller varje dag kl. 5:15 och 5:15 PM.

Den här artikeln beskriver senare de här scenarierna mer detaljerat.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Skapa ett schema med REST API

Följ dessa steg om du vill skapa ett grundläggande schema med [Azure scheduler REST API](/rest/api/scheduler):

1. Registrera din Azure-prenumeration med en resurs leverantör genom att använda [REST API för register åtgärd – Resource Manager](/rest/api/resources/providers). Leverantörs namnet för tjänsten Azure Scheduler är **Microsoft. Scheduler**. 

1. Skapa en jobb samling med hjälp av [åtgärden Skapa eller uppdatera för jobb samlingar](/rest/api/scheduler/jobcollections) i Scheduler REST API. 

1. Skapa ett jobb med hjälp av [åtgärden Skapa eller uppdatera för jobb](/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Jobb schema element

Den här tabellen ger en översikt på hög nivå för de viktigaste JSON-element som du kan använda när du ställer in upprepningar och scheman för jobb. 

| Element | Krävs | Beskrivning | 
|---------|----------|-------------|
| **/St** | Inga | Ett DateTime-sträng värde i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) som anger när jobbet först startar i ett Basic-schema. <p>För komplexa scheman startar jobbet ingen tidigare än **StartTime**. | 
| **mönster** | Inga | Reglerna för upprepning för när jobbet körs. Objektet **upprepning** stöder följande element: **frekvens**, **intervall**, **schema**, **antal** och slut tid **.** <p>Om du använder **upprepnings** elementet måste du också använda **frekvens** elementet, medan andra **upprepnings** element är valfria. |
| **frequency** | Ja, när du använder **upprepning** | Tidsenheten mellan förekomster och stöder dessa värden: "minut", "timme", "dag", "vecka", "månad" och "år" | 
| **intervall** | Inga | Ett positivt heltal som fastställer antalet tidsenheter mellan förekomster baserat på **frekvens**. <p>Om **intervallet** till exempel är 10 och **frekvensen** är "vecka", upprepas jobbet var 10: e vecka. <p>Här är det mest antal intervallen för varje frekvens: <p>– 18 månader <br>– 78 veckor <br>– 548 dagar <br>– I timmar och minuter är intervallet 1 <= <*intervall*> <= 1000. | 
| **Ange** | Inga | Definierar ändringar i upprepningen baserat på angivna minuter, timmar, vecko dagar och dagar i månaden | 
| **count** | Inga | Ett positivt heltal som anger antalet gånger som jobbet körs innan det slutförs. <p>Om till exempel ett dagligt **jobb har värdet** 7 och start datumet är måndag, slutförs jobbet på söndag. Om start datumet redan har passerat beräknas den första körningen från skapande tiden. <p>Utan **slut** tid eller **antal** körs jobbet oändligt. Du kan inte använda både **antal** och slut **tid i samma** jobb, men regeln som slutförs först. | 
| **Slut** | Inga | Ett datum-eller DateTime-sträng-värde i [ISO 8601-format](https://en.wikipedia.org/wiki/ISO_8601) som anger när jobbet slutar köras. Du kan ange ett **värde för slut** tid som redan har infallit. <p>Utan **slut** tid eller **antal** körs jobbet oändligt. Du kan inte använda både **antal** och slut **tid i samma** jobb, men regeln som slutförs först. |
|||| 

Detta JSON-schema beskriver till exempel ett grundläggande schema och upprepning för ett jobb: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Datum-och DateTime-värden*

* Datum i Scheduler-jobb inkluderar endast datumet och följer [ISO 8601-specifikationen](https://en.wikipedia.org/wiki/ISO_8601).

* Datum-och tids angivelser i Scheduler-jobb inkluderar både datum och tid, enligt [ISO 8601-specifikationen](https://en.wikipedia.org/wiki/ISO_8601)och antas vara UTC när ingen UTC-förskjutning anges. 

Mer information finns i [begrepp, terminologi och entiteter](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Information: StartTime

I den här tabellen beskrivs hur **StartTime** styr hur ett jobb körs:

| startTime | Ingen upprepning | Upprepning, inget schema | Upprepning med schema |
|-----------|---------------|-------------------------|--------------------------|
| **Ingen start tid** | Kör en gång direkt. | Kör en gång direkt. Kör senare körningar beräknat från den senaste körnings tiden. | Kör en gång direkt. Kör senare körningar baserat på ett upprepnings schema. | 
| **Start tid tidigare** | Kör en gång direkt. | Beräkna den första framtida körnings tiden efter start tiden och kör vid den tiden. <p>Kör senare körningar beräknat från den senaste körnings tiden. <p>Se exemplet efter den här tabellen. | Starta jobb *ingen tidigare än* den angivna start tiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör senare körningar baserat på ett upprepnings schema. | 
| **Start tid i framtiden eller aktuell tid** | Kör en gång vid den angivna start tiden. | Kör en gång vid den angivna start tiden. <p>Kör senare körningar beräknat från den senaste körnings tiden. | Starta jobb *ingen tidigare än* den angivna start tiden. Den första förekomsten baseras på schemat som beräknas från starttiden. <p>Kör senare körningar baserat på ett upprepnings schema. |
||||| 

Anta att du har det här exemplet med följande villkor: en start tid tidigare med en upprepning, men inget schema.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Aktuellt datum och tid är den 08 april 2015 vid 1:00 PM.

* Start datumet och start tiden är april 07 2015 kl. 2:00 PM, som är före aktuellt datum och tid.

* Upprepningen är varannan dag.

1. Under dessa villkor är den första körningen den 9 april 2015 vid 2:00 PM. 

   Scheduler beräknar körnings förekomsterna baserat på Start tiden, tar bort alla instanser tidigare och använder nästa instans i framtiden. 
   I det här fallet är **StartTime** den 7 april 2015 vid 2:00 PM, så nästa instans är två dagar från den tiden, vilket är 09, 2015 vid 2:00 PM.

   Den första körningen är densamma om **StartTime** är 2015-04-05 14:00 eller 2015-04-01 14:00. Efter den första körningen beräknas senare körningar baserat på schemat. 
   
1. Körningarna följer sedan i följande ordning: 
   
   1. 2015-04-11 vid 2:00 PM
   1. 2015-04-13 vid 2:00 PM 
   1. 2015-04-15 vid 2:00 PM
   1. Och så vidare...

1. Slutligen, när ett jobb har ett schema men inte har några angivna timmar och minuter, används dessa värden som standard för timmar och minuter i den första körningen.

<a name="schedule"></a>

## <a name="details-schedule"></a>Information: schema

Du kan använda **Schedule** för att *begränsa* antalet jobb körningar. Om till exempel ett jobb med en **frekvens** på "månad" har ett schema som bara körs dag 31, körs jobbet bara i månader som har en 31: a dag.

Du kan också använda **Schedule** för att *expandera* antalet jobb körningar. Till exempel, om ett jobb med en **frekvens** på "månad" har ett schema som körs på månads dagar 1 och 2, körs jobbet på den första och andra dagen i månaden istället för bara en gång i månaden.

Om du anger fler än ett schema element är utvärderings ordningen från störst till minsta: vecko nummer, månads dag, veckodag, timme och minut.

I följande tabell beskrivs schemaelement i detalj:

| JSON-namn | Beskrivning | Giltiga värden |
|:--- |:--- |:--- |
| **fördröjning** |Minuter i timmen då jobbet körs. |En matris med heltal. |
| **timmarna** |Timmar på dagen då jobbet körs. |En matris med heltal. |
| **weekDays** |Vecko dagar som jobbet körs. Kan bara anges med en vecko frekvens. |En matris med något av följande värden (maximal mat ris storlek är 7):<br />– "Måndag"<br />– "Tisdag"<br />– "Onsdag"<br />– "Torsdag"<br />– "Fredag"<br />– "Lördag"<br />– "Söndag"<br /><br />Inte Skift läges känsligt. |
| **monthlyOccurrences** |Anger vilka dagar i månaden som jobbet körs. Kan bara anges med en månads frekvens. |En matris med **monthlyOccurrences** -objekt:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dag** är den veckodag som jobbet körs. Till exempel är *{söndag}* varje söndag i månaden. Krävs.<br /><br />**förekomst** är förekomsten av dagen under månaden. Till exempel är  *{söndag,-1}* den sista söndagen i månaden. Valfritt. |
| **monthDays** |Dag i månaden då jobbet körs. Kan bara anges med en månads frekvens. |En matris med följande värden:<br />– Ett värde <= -1 och >= -31<br />– Ett värde > = 1 och < = 31|

## <a name="examples-recurrence-schedules"></a>Exempel: upprepnings scheman

I följande exempel visas olika scheman för upprepning. Exemplen fokuserar på schemaobjektet och dess under element.

Dessa scheman förutsätter att **intervallet** är inställt på 1\. I exemplen förutsätts även de korrekta **frekvens** värdena för värdena i **schemat**. Du kan till exempel inte använda en **frekvens** på "dag" och ha en **monthDays** modifiering i **Schedule**. Vi beskriver de här begränsningarna tidigare i artikeln.

| Exempel | Beskrivning |
|:--- |:--- |
| `{"hours":[5]}` |Kör kl. 5 varje dag.<br /><br />Scheduler matchar upp varje värde i timmar med varje värde i minuter, ett i taget, för att skapa en lista över alla tider då jobbet körs. |
| `{"minutes":[15], "hours":[5]}` |Kör kl. 05.15 varje dag. |
| `{"minutes":[15], "hours":[5,17]}` |Kör kl. 05.15 och 17.15 varje dag |
| `{"minutes":[15,45], "hours":[5,17]}` |Kör kl. 05.15, 5.45, 17.15 och 17.45 varje dag. |
| `{"minutes":[0,15,30,45]}` |Kör var 15:e minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Kör varje timme.<br /><br />Jobbet körs varje timme. Minuten styrs av värdet för **StartTime**, om det har angetts. Om inget **StartTime** -värde har angetts styrs minuten av skapande tiden. Till exempel, om start tiden eller skapande tiden (beroende på vad som gäller) är 12:25 PM, körs jobbet vid 00:25, 01:25, 02:25,..., 23:25.<br /><br />Schemat är detsamma som ett jobb med en **frekvens** på "timme", ett **intervall** på 1 och inget **schema** värde. Skillnaden är att du kan använda det här schemat med olika **frekvens** -och **intervall** värden för att skapa andra jobb. Om t. ex. **frekvens** är "månad" körs schemat bara en gång i månaden i stället för varje dag (om **frekvens** är "dag"). |
| `{minutes:[0]}` |Körs varje hel timme.<br /><br />Det här jobbet körs också varje timme, men på timmen (12, 1 AM, 2 och så vidare). Det här schemat är detsamma som ett jobb med **frekvensen** "timme", ett **StartTime** -värde på noll minuter, och inget **schema**, om frekvensen är "dag". Men om **frekvensen** är "vecka" eller "månad" körs schemat bara en dag i veckan eller en dag i månaden. |
| `{"minutes":[15]}` |Kör vid 15 minuter efter timmen varje timma.<br /><br />Körs varje timme, med början kl. 00:15, 1:15, 2:15 AM, och så vidare. Den avslutas med 11:15 PM. |
| `{"hours":[17], "weekDays":["saturday"]}` |Kör 5 EM på lördag varje vecka. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Kör 5 PM på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Körs 17.15 och 17.45 varje måndag, onsdag och fredag. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör kl. 5 och 17:00 på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Kör kl. 5:15, 5:45 AM, 5:15 PM och 5:45 PM på måndag, onsdag och fredag varje vecka. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Körs var 15:e minut på vardagar. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Kör var 15: e minut på vardagar, mellan 9 AM och 4:45 PM. |
| `{"weekDays":["sunday"]}` |Kör på söndagar vid start tiden. |
| `{"weekDays":["tuesday", "thursday"]}` |Kör på tisdagar och torsdag vid start tiden. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Kör kl. 6 den 28 dagen i varje månad (förutsatt att månads **frekvensen** är "månad"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Kör kl. 6 den sista dagen i månaden.<br /><br />Om du vill köra ett jobb den sista dagen i månaden använder du-1 istället för dag 28, 29, 30 eller 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Kör kl. 6 den första och sista dagen i varje månad. |
| `{monthDays":[1,-1]}` |Kör den första och sista dagen i varje månad vid start tiden. |
| `{monthDays":[1,14]}` |Kör den första och 14 dagen i varje månad vid start tiden. |
| `{monthDays":[2]}` |Kör den andra dagen i månaden vid start tiden. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad kl. 5. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Kör den första fredagen i varje månad vid start tiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Kör den tredje fredagen från slutet av månaden, varje månad, vid start tiden. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Körs den första och sista fredagen i varje månad 05.15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Kör den första och sista fredagen i varje månad vid start tiden. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Kör den femte fredagen i varje månad vid start tiden.<br /><br />Om det inte finns någon femte fredag i månaden körs inte jobbet. Du kan överväga att använda-1 istället för 5 för att köra jobbet på den senaste fredagen i månaden. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Körs var 15:e minut den sista fredagen i månaden. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Körs 05.15, 05.45, 17.15 och 17.45 den tredje onsdagen varje månad. |

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)
* [Gränser, standardinställningar och felkoder i Azure Scheduler](scheduler-limits-defaults-errors.md)