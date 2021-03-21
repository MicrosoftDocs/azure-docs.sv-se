---
title: Felsöka Azure Stream Analytics att använda resurs loggar
description: Den här artikeln beskriver hur du analyserar resurs loggar i Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: troubleshooting
ms.custom: contperf-fy21q1
ms.date: 06/18/2020
ms.openlocfilehash: 93d881419c4854b8e46608e150b55072267e0347
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574415"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>Felsöka Azure Stream Analytics med hjälp av resurs loggar

Ibland kan ett Azure Stream Analytics-jobb oväntat avbryta bearbetningen. Det är viktigt att kunna felsöka den här typen av händelse. Fel kan inträffa på grund av ett oväntat frågeresultat, enhetsanslutningar eller ett oväntat tjänstavbrott. Resurs loggarna i Stream Analytics kan hjälpa dig att identifiera orsaken till problem när de inträffar och minska återställnings tiden.

Vi rekommenderar starkt att du aktiverar resurs loggar för alla jobb eftersom detta kommer att vara mycket hjälp med fel sökning och övervakning.

## <a name="log-types"></a>Logg typer

Stream Analytics erbjuder två typer av loggar:

* [Aktivitets loggar](../azure-monitor/essentials/platform-logs-overview.md) (alltid på), vilket ger insikter om åtgärder som utförs på jobb.

* [Resurs loggar](../azure-monitor/essentials/platform-logs-overview.md) (kan konfigureras), vilket ger bättre insikter om allt som händer med ett jobb. Resurs loggar startar när jobbet skapas och avslutas när jobbet tas bort. De behandlar händelser när jobbet uppdateras och när det körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Monitor loggar för att analysera data som inte överensstämmer. Du debiteras enligt pris sättnings modellen för dessa tjänster.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Felsöka med aktivitets loggar

Aktivitets loggarna är aktiverat som standard och ger höga insikter om åtgärder som utförs av ditt Stream Analytics jobb. Information som finns i aktivitets loggar kan hjälpa dig att hitta rotor saken till problem som påverkar ditt jobb. Utför följande steg för att använda aktivitets loggar i Stream Analytics:

1. Logga in på Azure Portal och välj **aktivitets logg** under **Översikt**.

   ![Stream Analytics aktivitets logg](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Du kan se en lista över åtgärder som har utförts. Alla åtgärder som gjorde att jobbet misslyckades har en röd informations bubbla.

3. Klicka på en åtgärd för att visa dess sammanfattningsvy. Informationen här är ofta begränsad. Om du vill ha mer information om åtgärden klickar du på **JSON**.

   ![Sammanfattning av Stream Analytics aktivitets loggs åtgärd](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Rulla ned till avsnittet **Egenskaper** i JSON, där det finns information om felet som orsakade åtgärden. I det här exemplet berodde felet på ett körnings fel från de kopplade Latitude-värdena. En avvikelse i data som bearbetas av ett Stream Analytics jobb orsakar ett data fel. Du kan lära dig mer om olika [indata och fel i utdata och varför de inträffar](./data-errors.md).

   ![JSON-fel information](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Du kan vidta korrigerande åtgärder baserat på fel meddelandet i JSON. I det här exemplet, kontrollerar att värdet på latitud är mellan-90 grader och 90 grader måste läggas till i frågan.

6. Om fel meddelandet i aktivitets loggarna inte är till hjälp vid identifiering av rotor Saks aktiverar du resurs loggar och använder Azure Monitor loggar.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Skicka diagnostik till Azure Monitor loggar

Det rekommenderas starkt att du aktiverar resurs loggar och skickar dem till Azure Monitor loggar. De är **inaktiverade** som standard. Gör så här för att aktivera dem:

1.  Skapa en Log Analytics arbets yta om du inte redan har en. Vi rekommenderar att du har din Log Analytics arbets yta i samma region som ditt Stream Analytics-jobb.

2.  Logga in på Azure Portal och navigera till ditt Stream Analytics-jobb. Under **övervakning** **väljer du diagnostikloggar.** Välj sedan **Aktivera diagnostik**.

    ![Blad navigering till resurs loggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Ange ett **namn** i **namn på diagnostikinställningar** och markera kryss rutorna för att **köra** och **Redigera** under **logg** och **AllMetrics** under **mått**. Välj sedan **Skicka till Log Analytics** och välj din arbets yta. Klicka på **Spara**.

    ![Inställningar för resurs loggar](./media/stream-analytics-job-diagnostic-logs/logs-setup.png)

3. När ditt Stream Analytics jobb startar dirigeras resurs loggar till Log Analytics-arbetsytan. Om du vill visa resurs loggar för ditt jobb väljer du **loggar** i avsnittet **övervakning** .

   ![Skärm bild som visar menyn allmänt med loggar valt.](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Stream Analytics innehåller fördefinierade frågor som gör det enkelt att söka efter de loggar som du är intresse rad av. Du kan välja valfri fördefinierad fråga i den vänstra rutan och sedan välja **Kör**. Resultatet av frågan visas i det nedre fönstret. 

   ![Skärm bild som visar loggar för ett Stream Analytics jobb.](./media/stream-analytics-job-diagnostic-logs/logs-example.png)

## <a name="resource-log-categories"></a>Resurs loggs kategorier

Azure Stream Analytics fångar två kategorier av resurs loggar:

* **Redigering**: fångar logg händelser som är relaterade till jobb redigerings åtgärder, till exempel skapande av jobb, lägga till och ta bort indata och utdata, lägga till och uppdatera frågan och starta eller stoppa jobbet.

* **Körning**: fångar händelser som inträffar under jobb körningen.
    * Anslutningsfel
    * Fel vid data bearbetning, inklusive:
        * Händelser som inte följer frågedefinitionen (felmatchade fält typer och värden, saknade fält osv.)
        * Utvärderings fel för uttryck
    * Andra händelser och fel

## <a name="resource-logs-schema"></a>Schema för resurs loggar

Alla loggar lagras i JSON-format. Varje post har följande gemensamma sträng fält:

Name | Beskrivning
------- | -------
time | Tidsstämpel (i UTC) för loggen.
resourceId | ID för den resurs som åtgärden ägde rum i, i versaler. Den innehåller prenumerations-ID, resurs grupp och jobb namn. Till exempel **/Subscriptions/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/My-Resource-Group/providers/Microsoft. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Logg kategori, antingen **körning** eller **redigering**.
operationName | Namnet på den åtgärd som loggas. Till exempel **skicka händelser: skrivning av SQL-utdata har misslyckats till mysqloutput**.
status | Status för åtgärden. Till exempel **misslyckad** eller **lyckad**.
nivå | Loggnings nivå. Till exempel **fel**, **Varning** eller **information**.
properties | Logg post-detaljerad information, serialiserad som en JSON-sträng. Mer information finns i följande avsnitt i den här artikeln.

### <a name="execution-log-properties-schema"></a>Schema för körnings logg egenskaper

Körnings loggarna innehåller information om händelser som har inträffat under Stream Analytics jobb körningen. Schemat för egenskaper varierar beroende på om händelsen är ett data fel eller en allmän händelse.

### <a name="data-errors"></a>Datafel

Eventuella fel som inträffar när jobbet bearbetar data finns i den här kategorin av loggar. Loggarna skapas oftast när data läses, serialiseras och skrivs. Dessa loggar innehåller inte anslutnings fel. Anslutnings fel behandlas som allmänna händelser. Du kan lära dig mer om orsaken till olika [fel i indata och utdata](./data-errors.md).

Name | Beskrivning
------- | -------
Källa | Namnet på det indata eller utdata där felet uppstod.
Meddelande | Meddelande som är kopplat till felet.
Typ | Typ av fel. Till exempel **DataConversionError**, **CsvParserError** eller **ServiceBusPropertyColumnMissingError**.
Data | Innehåller data som är användbara för att korrekt hitta orsaken till felet. Omfattas av trunkering, beroende på storlek.

I väntan på värdet **operationName** har data fel följande schema:

* **Serialisering av händelser** sker under händelse Läs åtgärder. De inträffar när data i indata inte uppfyller synkroniseringsschemat av något av följande skäl:

   * *Typ matchnings fel vid händelse (de) serialisering*: identifierar det fält som orsakar felet.

   * *Det går inte att läsa en händelse, ogiltig serialisering*: visar information om platsen i indata där felet uppstod. Inkluderar BLOB-namn för BLOB-indata, förskjutning och ett exempel på data.

* **Sändnings händelser** inträffar under Skriv åtgärder. De identifierar den strömmande händelse som orsakade felet.

### <a name="generic-events"></a>Allmänna händelser

Allmänna händelser behandlar allt annat.

Name | Beskrivning
-------- | --------
Fel | valfritt Fel information. Detta är vanligt vis undantags information om det är tillgängligt.
Meddelande| Logg meddelande.
Typ | Typ av meddelande. Mappar till intern kategorisering av fel. Till exempel **JobValidationError** eller **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | GUID som unikt identifierar jobb körningen. Alla körnings logg poster från den tidpunkt då jobbet startar tills jobbet stoppas har samma **korrelations-ID-** värde.

## <a name="next-steps"></a>Nästa steg

* [Stream Analytics data fel](./data-errors.md)
* [Språk referens för Stream Analytics-fråga](/stream-analytics-query/stream-analytics-query-language-reference)