---
title: Schemalägga återkommande aktiviteter och arbets flöden i Azure Logic Apps
description: En översikt över schemaläggning av återkommande automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, jonfan, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 27763536b859b7bc3e9aa0a7c490cb510c0fda41
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588462"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Schemalägg och kör återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps

Logic Apps hjälper dig att skapa och köra automatiserade återkommande uppgifter och processer enligt ett schema. Genom att skapa ett Logic app-arbetsflöde som börjar med en inbyggd upprepnings utlösare eller en överordnad fönster utlösare, som är schema-typ utlösare, kan du köra aktiviteter direkt, vid ett senare tillfälle eller på ett återkommande intervall. Du kan anropa tjänster i och utanför Azure, till exempel HTTP-eller HTTPS-slutpunkter, skicka meddelanden till Azure-tjänster som Azure Storage och Azure Service Bus eller hämta filer som laddats upp till en fil resurs. Med upprepnings utlösaren kan du också ställa in komplexa scheman och avancerade upprepningar för aktiviteter som körs. Mer information om inbyggda schema utlösare och åtgärder finns i [schema utlösare](#schedule-triggers) och [schema åtgärder](#schedule-actions). 

> [!TIP]
> Du kan schemalägga och köra återkommande arbets belastningar utan att skapa en separat logisk app för varje schemalagt jobb och köra [arbets flöden per region och prenumeration](../logic-apps/logic-apps-limits-and-config.md#definition-limits). I stället kan du använda Det Logic app-mönster som skapats av [Azure snabb starts mal len: Logic Apps Job Scheduler](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> Mallen för Logic Apps jobb schema skapar en CreateTimerJob Logic-app som anropar en TimerJob Logic-app. Du kan sedan anropa CreateTimerJob Logic-appen som ett API genom att göra en HTTP-begäran och skicka ett schema som inpasset för begäran. Varje anrop till CreateTimerJob Logic-appen anropar också TimerJob Logic-appen, som skapar en ny TimerJob-instans som kontinuerligt körs baserat på det angivna schemat eller tills en angiven gräns har uppnåtts. På så sätt kan du köra så många TimerJob-instanser som du vill utan att oroa dig för arbets flödes gränser eftersom instanser inte är enskilda logik definitioner eller resurser för Logic app-arbetsflöden.

I den här listan visas några exempel uppgifter som du kan köra med de inbyggda schema utlösarna:

* Hämta interna data, till exempel köra en SQL-lagrad procedur varje dag.

* Hämta externa data, till exempel pull-väder rapporter från NOAA var 15: e minut.

* Skicka rapport data, till exempel e-posta en sammanfattning för alla beställningar som är större än en angiven mängd under den senaste veckan.

* Bearbeta data, till exempel komprimera idag överförda bilder varje vardag under låg belastnings tider.

* Rensa data, till exempel ta bort alla Tweets som är äldre än tre månader.

* Arkivera data, till exempel skicka fakturor till en säkerhets kopierings tjänst vid 1:00 varje dag under de kommande nio månaderna.

Du kan också använda schemalagda inbyggda åtgärder för att pausa arbets flödet innan nästa åtgärd körs, till exempel:

* Vänta till en veckodag och skicka en status uppdatering via e-post.

* Fördröj arbets flödet tills ett HTTP-anrop har tid att slutföra innan du återupptar och hämtar resultatet.

I den här artikeln beskrivs funktionerna i de inbyggda schema utlösarna och åtgärderna.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Schemalägg utlösare

Du kan starta ditt Logic app-arbetsflöde med utlösaren upprepnings utlösare eller glidande fönster, som inte är associerad med någon speciell tjänst eller något annat system. Dessa utlösare startar och kör ditt arbets flöde baserat på din angivna upprepning där du väljer intervall och frekvens, till exempel antalet sekunder, minuter, timmar, dagar, veckor eller månader. Du kan också ange start datum och-tid samt tidszon. Varje gång en utlösare utlöses skapar Logic Apps och kör en ny arbets flödes instans för din Logic app.

Här följer skillnaderna mellan dessa utlösare:

* **Upprepning**: kör arbets flödet med jämna tidsintervall baserat på det angivna schemat. Om upprepningar saknas, till exempel på grund av avbrott eller inaktiverade arbets flöden, bearbetar upprepnings utlösaren inte de missade upprepningarna, men startar om upprepningar med nästa schemalagda intervall. Du kan ange start datum och-tid samt tidszon. Om du väljer dag kan du ange timmar på dagen och minuterna i timmen, till exempel varje dag vid 2:30. Om du väljer "vecka" kan du också välja vecko dagar, till exempel onsdag och lördag. Mer information finns i [skapa, schemalägga och köra återkommande uppgifter och arbets flöden med upprepnings utlösaren](../connectors/connectors-native-recurrence.md).

* **Glidande fönster**: kör arbets flödet med regelbundna tidsintervall som hanterar data i kontinuerliga segment. Om upprepningar saknas, till exempel på grund av avbrott eller inaktiverade arbets flöden, utlöses den glidande fönster utlösaren tillbaka och bearbetar de missade upprepningarna. Du kan ange start datum och tid, tidszon och varaktighet för att försena varje upprepning i arbets flödet. Den här utlösaren stöder inte avancerade scheman, till exempel vissa timmar på dagen, minuter i timmen och dagar i veckan. Mer information finns i [skapa, schemalägga och köra återkommande uppgifter och arbets flöden med den glidande fönster utlösaren](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Schemalägg åtgärder

Efter en åtgärd i ditt Logic app-arbetsflöde kan du använda fördröjningen och fördröjningen tills åtgärder för att göra arbets flödet vänta innan nästa åtgärd körs.

* **Fördröjning**: vänta på att köra nästa åtgärd för det angivna antalet tidsenheter, till exempel sekunder, minuter, timmar, dagar, veckor eller månader. Mer information finns i [försena nästa åtgärd i arbets flöden](../connectors/connectors-native-delay.md).

* **Fördröjning till**: vänta på att köra nästa åtgärd tills angivet datum och tid. Mer information finns i [försena nästa åtgärd i arbets flöden](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Mönster för start datum och-tid

<a name="start-time"></a>

Här följer några mönster som visar hur du kan styra upprepningen av start datum och start tid och hur Logic Apps tjänsten kör dessa upprepningar:

| Starttid | Upprepning utan schema | Upprepning med schema (endast upprepnings utlösare) |
|------------|-----------------------------|----------------------------------------------------|
| alternativet | Kör den första arbets belastningen direkt. <p>Kör framtida arbets belastningar baserat på den senaste körnings tiden. | Kör den första arbets belastningen direkt. <p>Kör framtida arbets belastningar baserat på det angivna schemat. |
| Start tid tidigare | **Upprepnings** utlösare: beräknar körnings tider baserat på den angivna start tiden och tar bort de senaste körnings tiderna. Kör den första arbets belastningen vid nästa framtida körnings tillfälle. <p>Kör framtida arbets belastningar baserat på beräkningar från den senaste körnings tiden. <p><p>Utlösare för **glidning** : beräknar körnings tider baserat på den angivna start tiden och följer de senaste körnings tiderna. <p>Kör framtida arbets belastningar baserat på beräkningar från den angivna start tiden. <p><p>Mer förklaringar finns i exemplet som följer efter den här tabellen. | Kör den första arbets belastningen *tidigare* än start tiden, baserat på schemat som beräknas från start tiden. <p>Kör framtida arbets belastningar baserat på det angivna schemat. <p>**Obs:** Om du anger en upprepning med ett schema, men inte anger timmar eller minuter för schemat, beräknas framtida körnings tider med timmarna eller minuterna från den första körnings tiden. |
| Start tid för närvarande eller i framtiden | Kör den första arbets belastningen vid den angivna start tiden. <p>Kör framtida arbets belastningar baserat på beräkningar från den senaste körnings tiden. | Kör den första arbets belastningen *tidigare* än start tiden, baserat på schemat som beräknas från start tiden. <p>Kör framtida arbets belastningar baserat på det angivna schemat. <p>**Obs:** Om du anger en upprepning med ett schema, men inte anger timmar eller minuter för schemat, beräknas framtida körnings tider med timmarna eller minuterna från den första körnings tiden. |
||||

> [!IMPORTANT]
> När upprepningar inte anger avancerade alternativ för schemaläggning baseras framtida upprepningar på den senaste körnings tiden.
> Start tiderna för dessa upprepningar kan uppstå på grund av faktorer som svars tid under lagrings anrop. För att se till att din Logi Kap par inte saknar upprepning, särskilt när frekvensen är i dagar eller längre, använder du ett av följande alternativ:
> 
> * Ange en start tid för upprepningen.
> 
> * Ange i hur många timmar och minuter som upprepningen ska köras med hjälp av alternativen **vid följande tidpunkter** och **i minuter** .
> 
> * Använd den [glidande fönster utlösaren](../connectors/connectors-native-sliding-window.md)i stället för upprepnings utlösaren.

*Exempel på tidigare start tid och upprepning, men inget schema*

Anta att aktuellt datum och tid är 8 september 2017 till 1:00 PM. Du anger start datum och-tid som 7 september 2017 vid 2:00 PM, som är i det förflutna och en upprepning som körs varannan dag.

| Starttid | Aktuell tid | Upprepning | Schema |
|------------|--------------|------------|----------|
| 2017-09-**07** T14:00:00Z <br>(2017-09 –**07** vid 2:00 PM) | 2017-09-**08** t13:00:00Z <br>(2017-09 –**08** vid 1:00 PM) | Varannan dag | alternativet |
|||||

För upprepnings utlösaren beräknar Logic Apps motor kör tid baserat på Start tiden, tar bort de senaste körnings tiderna, använder nästa framtida start tid för den första körningen och beräknar framtida körningar baserat på den senaste körnings tiden.

Så här ser den här upprepningen ut:

| Starttid | Första körnings tid | Framtida körnings tider |
|------------|----------------|------------------|
| 2017-09 –**07** vid 2:00 PM | 2017-09 –**09** vid 2:00 PM | 2017-09 –**11** vid 2:00 PM </br>2017-09 –**13** vid 2:00 PM </br>2017-09 –**15** vid 2:00 PM </br>och så vidare... |
||||

Det innebär att du alltid har angett start tiden, till exempel 2017-09 –**05** vid 2:00 PM eller 2017-09-**01** vid 2:00 PM, och att din första körning alltid använder nästa framtida start tid.

För den glidande fönster utlösaren beräknar Logic Appss motorn körnings tider baserat på Start tiden, följer tidigare körnings tider, använder start tiden för den första körningen och beräknar framtida körningar baserat på Start tiden.

Så här ser den här upprepningen ut:

| Starttid | Första körnings tid | Framtida körnings tider |
|------------|----------------|------------------|
| 2017-09 –**07** vid 2:00 PM | 2017-09 –**08** vid 1:00 PM (aktuell tid) | 2017-09 –**09** vid 2:00 PM </br>2017-09 –**11** vid 2:00 PM </br>2017-09 –**13** vid 2:00 PM </br>2017-09 –**15** vid 2:00 PM </br>och så vidare... |
||||

Så, oavsett hur långt tidigare du angav start tiden, till exempel 2017-09 –**05** på 2:00 PM eller 2017-09-**01** vid 2:00 PM, använder din första körning alltid den angivna start tiden.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Exempel upprepningar

Här är olika exempel upprepningar som du kan ställa in för utlösare som stöder alternativen:

| Utlösare | Upprepning | Intervall | Frekvens | Starttid | Dessa dagar | Vid dessa timmar | Vid dessa minuter | Anteckning |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Mönster <br>Skjutfönster | Kör var 15: e minut (inget start datum och-tid) | 15 | Minut | alternativet | otillgänglig | alternativet | alternativet | Schemat startar omedelbart och beräknar sedan framtida upprepningar baserat på den senaste körnings tiden. |
| Mönster <br>Skjutfönster | Kör var 15: e minut (med start datum och-tid) | 15 | Minut | *StartDate* T *StartTime* Z | otillgänglig | alternativet | alternativet | Det här schemat startar inte *tidigare* än angivet start datum och-tid och beräknar sedan framtida upprepningar baserat på den senaste körnings tiden. |
| Mönster <br>Skjutfönster | Kör varje timme, på timmen (med start datum och-tid) | 1 | Tid | *StartDate* THH: 00:00Z | otillgänglig | alternativet | alternativet | Det här schemat startar inte *tidigare* än angivet start datum och-tid. Framtida upprepningar körs varje timme vid "00" minut angivelse, vilket beräknas från start tiden. <p>Om frekvensen är "vecka" eller "månad", kör det här schemat bara en dag per vecka eller en dag per månad. |
| Mönster <br>Skjutfönster | Kör varje timme, varje dag (inget start datum och-tid) | 1 | Tid | alternativet | otillgänglig | alternativet | alternativet | Schemat startar omedelbart och beräknar framtida upprepningar baserat på den senaste körnings tiden. <p>Om frekvensen är "vecka" eller "månad", kör det här schemat bara en dag per vecka eller en dag per månad. |
| Mönster <br>Skjutfönster | Kör varje timme, varje dag (med start datum och-tid) | 1 | Tid | *StartDate* T *StartTime* Z | otillgänglig | alternativet | alternativet | Det här schemat startar inte *tidigare* än angivet start datum och-tid och beräknar sedan framtida upprepningar baserat på den senaste körnings tiden. <p>Om frekvensen är "vecka" eller "månad", kör det här schemat bara en dag per vecka eller en dag per månad. |
| Mönster <br>Skjutfönster | Körs var 15: e minut efter timmen, varje timme (med start datum och-tid) | 1 | Tid | *StartDate* T00:15:00Z | otillgänglig | alternativet | alternativet | Det här schemat startar inte *tidigare* än angivet start datum och-tid. Framtida upprepningar körs vid "15"-minuten, som beräknas från start tiden, så kl. 00:15, 1:15, 2:15 AM, och så vidare. |
| Upprepning | Körs var 15: e minut efter timmen, varje timme (inget start datum och-tid) | 1 | Dag | alternativet | otillgänglig | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15, 16, 17, 19 | 15 | Det här schemat körs kl. 00:15, 1:15, 2:15 AM, och så vidare. Detta schema motsvarar också en frekvens på "timme" och en start tid med "15" minuter. |
| Upprepning | Kör var 15: e minut vid de angivna minut tecknen (inget start datum och tid). | 1 | Dag | alternativet | otillgänglig | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15, 16, 17, 19 | 0, 15, 30, 45 | Det här schemat startar inte förrän nästa angivna 15-minuters tecken. |
| Upprepning | Kör varje dag kl. 8, *plus* minuten – Markera när du sparar din Logic app | 1 | Dag | alternativet | otillgänglig | 8 | alternativet | Utan start datum och start tid körs det här schemat utifrån den tidpunkt då du sparar Logi Kap par (PLACERINGs åtgärd). |
| Upprepning | Kör dagligen vid 8:00 AM (med start datum och-tid) | 1 | Dag | *StartDate* T08:00:00Z | otillgänglig | alternativet | alternativet | Det här schemat startar inte *tidigare* än angivet start datum och-tid. Framtida förekomster körs dagligen vid 8:00. | 
| Upprepning | Kör dagligen vid 8:00 AM (inget start datum och-tid) | 1 | Dag | alternativet | otillgänglig | 8 | 00 | Det här schemat körs vid 8:00 varje dag. |
| Upprepning | Kör dagligen kl. 8:00 och 4:00 PM | 1 | Dag | alternativet | otillgänglig | 8, 16 | 0 | |
| Upprepning | Kör dagligen kl. 8:30 AM, 8:45, 4:30 PM och 4:45 PM | 1 | Dag | alternativet | otillgänglig | 8, 16 | 30, 45 | |
| Upprepning | Kör varje lördag vid 5:00 PM (inget start datum och-tid) | 1 | Vecka | alternativet | Söndag | 17 | 0 | Det här schemat körs varje lördag kl 5:00 PM. |
| Upprepning | Kör varje lördag vid 5:00 PM (med start datum och-tid) | 1 | Vecka | *StartDate* T17:00:00Z | Söndag | alternativet | alternativet | Det här schemat startar inte *tidigare* än det angivna start datumet och-tiden, i det här fallet den 9 september 2017 vid 5:00 PM. Framtida upprepningar körs varje lördag vid 5:00 PM. |
| Upprepning | Kör varje tisdag, torsdag 5 PM *plus* minuten – Markera när du sparar din Logic app| 1 | Vecka | alternativet | "Tisdag", "torsdag" | 17 | alternativet | |
| Upprepning | Kör varje timme under arbets tid. | 1 | Vecka | alternativet | Välj alla dagar förutom lördag och söndag. | Välj de timmar på dagen som du vill ha. | Välj några minuter i timmen som du vill ha. | Om dina arbets timmar till exempel är 8:00 till 5:00 PM väljer du "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" som timmar på dagen *plus* "0" som minuter i timmen. |
| Upprepning | Kör en gång varje dag på helger | 1 | Vecka | alternativet | "Lördag", "söndag" | Välj de timmar på dagen som du vill ha. | Välj några minuter i timmen efter behov. | Det här schemat körs varje lördag och söndag enligt det angivna schemat. |
| Upprepning | Kör var 15: e minut varannan vecka på enbart måndagar | 2 | Vecka | alternativet | Kl | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15, 16, 17, 19 | 0, 15, 30, 45 | Det här schemat körs varannan måndag vid varje 15-minuters markering. |
| Upprepning | Kör varje månad | 1 | Månad | *StartDate* T *StartTime* Z | otillgänglig | otillgänglig | otillgänglig | Det här schemat startar inte *tidigare* än angivet start datum och-tid och beräknar framtida upprepningar på start datum och start tid. Om du inte anger start datum och start tid använder det här schemat skapande datum och-tid. |
| Upprepning | Kör varje timme för en dag per månad | 1 | Månad | {Se Obs!} | otillgänglig | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 15, 16, 17, 19 | {Se Obs!} | Om du inte anger start datum och start tid använder det här schemat skapande datum och-tid. Om du vill kontrol lera minuterna för upprepnings schemat anger du antalet minuter i timmen, en start tid eller när du använder skapande tiden. Om start tiden eller skapande tiden är 8:25 AM, körs det här schemat vid 8:25 AM, 9:25 AM, 10:25 och så vidare. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Kör endast en tid

Om du bara vill köra din Logi Kap par på en gång i framtiden kan du använda mallen **Scheduler: kör en gång jobb** . När du har skapat en ny Logic-app men innan du öppnar Logic Apps designer går du till avsnittet **mallar** , i listan **kategori** , väljer **schema** och väljer sedan den här mallen:

![Välj mallen Schemaläggaren: kör när jobb](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Eller, om du kan starta din Logi Kap par med **när en http-begäran tas emot – begär** utlösare och skicka start tiden som en parameter för utlösaren. För den första åtgärden använder du åtgärden **fördröj tills-Schedule** och anger hur lång tid det tar innan nästa åtgärd börjar köras.

## <a name="next-steps"></a>Nästa steg

* [Skapa, schemalägga och köra återkommande uppgifter och arbets flöden med upprepnings utlösaren](../connectors/connectors-native-recurrence.md)
* [Skapa, schemalägga och köra återkommande uppgifter och arbets flöden med den glidande fönster utlösaren](../connectors/connectors-native-sliding-window.md)
* [Pausa arbets flöden med fördröjnings åtgärder](../connectors/connectors-native-delay.md)
