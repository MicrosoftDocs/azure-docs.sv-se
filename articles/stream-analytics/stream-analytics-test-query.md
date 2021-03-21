---
title: Testa ett Azure Stream Analytics jobb med exempel data
description: I den här artikeln beskrivs hur du använder Azure Portal för att testa ett Azure Stream Analytics jobb, exempel på indata och ladda upp exempel data.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: eff9103f476e6074ab46198ff8cc78588675569f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020051"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testa ett Azure Stream Analytics jobb i portalen

I Azure Stream Analytics kan du testa din fråga utan att starta eller stoppa jobbet. Du kan testa frågor på inkommande data från dina strömmande källor eller ladda upp exempel data från en lokal fil på Azure Portal. Du kan också testa frågor lokalt från dina lokala exempel data eller real tids data i [Visual Studio](stream-analytics-live-data-local-testing.md) och [Visual Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Sampla automatiskt inkommande data från indata

Azure Stream Analytics hämtar automatiskt händelser från dina strömmande indata. Du kan köra frågor på standardprovet eller ange en bestämd tidsram för exemplet.

1. Logga in på Azure-portalen.

2. Leta upp och välj ditt befintliga Stream Analytics-jobb.

3. På sidan Stream Analytics jobb går du till rubriken **jobb Topology** och väljer **fråga** för att öppna fönstret Frågeredigeraren. 

4. Om du vill se en exempel lista över inkommande händelser väljer du ikonen indata med fil och exempel händelser visas automatiskt i **förhands granskningen**.

   a. Serialiserings typen för dina data identifieras automatiskt om dess JSON eller CSV. Du kan manuellt ändra det och till JSON, CSV, AVRO genom att ändra alternativet i list menyn.
    
   b. Använd väljaren för att visa dina data i **tabell** -eller **RAW** -format.
    
   c. Om data som visas inte är aktuella väljer du **Uppdatera** för att se de senaste händelserna.

   Följande tabell är ett exempel på data i **tabell format**:

   ![Azure Stream Analytics exempel på indatatyp i tabell format](./media/stream-analytics-test-query/asa-sample-table.png)

   Följande tabell är ett exempel på data i RAW- **format**:

   ![Azure Stream Analytics exempel på indata i RAW-format](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Om du vill testa din fråga med inkommande data väljer du **test fråga**. Resultaten visas på fliken **test resultat** . Du kan också välja **Hämta resultat** för att hämta resultatet.

   ![Exempel på test frågeresultaten för Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Om du vill testa din fråga mot ett speciellt tidsintervall för inkommande händelser väljer du **Välj tidsintervall**.
   
   ![Azure Stream Analytics tidsintervall för inkommande exempel händelser](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Ange tidsintervallet för de händelser som du vill använda för att testa frågan och välj **exempel**. Inom denna tidsram kan du hämta upp till 1000 händelser eller 1 MB, beroende på vilket som kommer först.

   ![Azure Stream Analytics ange tidsintervall för inkommande exempel händelser](./media/stream-analytics-test-query/asa-set-time-range.png)

8. När händelserna samplas för det valda tidsintervallet visas de på fliken **förhands granskning av indatakälla** .

   ![Azure Stream Analytics Visa test resultat](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Välj **Återställ** om du vill se exempel listan för inkommande händelser. Om du väljer **Återställ** går valet för tidsintervall förlorade. Välj **test fråga** för att testa frågan och granska resultaten på fliken **test resultat** .

10. När du gör ändringar i frågan väljer du **Spara fråga** för att testa den nya fråge logiken. Detta gör att du kan ändra din fråga upprepade gånger och testa den igen för att se hur utdata ändras.

11. När du har kontrollerat resultaten som visas i webbläsaren är du redo att **Starta** jobbet.

## <a name="upload-sample-data-from-a-local-file"></a>Ladda upp exempel data från en lokal fil

I stället för att använda real tids data kan du använda exempel data från en lokal fil för att testa din Azure Stream Analytics fråga.

1. Logga in på Azure-portalen.
   
2. Leta upp ditt befintliga Stream Analytics-jobb och markera det.

3. På sidan Stream Analytics jobb går du till rubriken **jobb Topology** och väljer **fråga** för att öppna fönstret Frågeredigeraren.

4. Om du vill testa din fråga med en lokal fil väljer du **Ladda upp exempel indata** på fliken **förhands granskning av indata** . 

   ![Skärm bild som visar alternativet Ladda upp exempel indatamängd.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Överför din lokala fil för att testa frågan. Du kan bara ladda upp filer med JSON-, CSV-eller AVRO-format. Välj **OK**.

   ![Skärm bild som visar dialog rutan Ladda upp exempel data där du kan välja en fil.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. När du överför filen kan du också se filens innehåll i formuläret som en tabell eller i formatet RAW. Om du väljer **Återställ** kommer exempel data att återgå till den inkommande inmatnings informationen i föregående avsnitt. Du kan ladda upp andra filer för att testa frågan när som helst.

7. Välj **test fråga** för att testa din fråga mot den uppladdade exempel filen.

8. Test resultaten visas baserat på din fråga. Du kan ändra frågan och välja **Spara fråga** för att testa den nya fråge logiken. Detta gör att du kan ändra din fråga upprepade gånger och testa den igen för att se hur utdata ändras.

9. När du använder flera utdata i frågan visas resultaten baserat på valda utdata. 

   ![Azure Stream Analytics valda utdata](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. När du har kontrollerat resultaten som visas i webbläsaren kan du **Starta** jobbet.

## <a name="limitations"></a>Begränsningar

1.  Tids principen stöds inte i Portal testning:

    * Föråldrad: alla inkommande händelser kommer att beställas.
    * Sen införsel: det kommer inte att finnas någon sen införsel eftersom Stream Analytics endast kan använda befintliga data för testning.
   
2.  C# UDF stöds inte.

3.  Alla tester kommer att köras med ett jobb som har en enhet för strömning.

4.  Timeout-storleken är en minut. Alla frågor med en fönster storlek som är större än en minut kan inte hämta några data.

5.  Machine Learning stöds inte.

6. API: et för exempel data begränsas efter fem förfrågningar i ett 15-minuters fönster. Efter slutet av 15-minuters perioden kan du göra fler exempel på data förfrågningar. Den här begränsningen tillämpas på prenumerations nivån.

## <a name="troubleshooting"></a>Felsökning

1.  Om det här felet uppstår visas ett problem med nätverks anslutningen när resultaten hämtades. Kontrol lera inställningarna för nätverk och brand vägg. ", Följ stegen nedan:

  * Om du vill kontrol lera anslutningen till tjänsten öppnar du [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) i en webbläsare. Uppdatera brand Väggs inställningarna om du inte kan öppna den här länken.
  
2. Om du får det här fel meddelandet är storleken för stor. Minska storleken på indata och försök igen. Följ stegen nedan om du vill:

  * Minska indata-storlek – testa frågan med mindre storleks exempel fil eller med ett kortare tidsintervall.
  * Minska frågans storlek – om du vill testa ett urval av fråga väljer du en del av frågan och klickar sedan på **testa vald fråga**.


## <a name="next-steps"></a>Nästa steg
* [Bygg en IoT-lösning med hjälp av Stream Analytics: i](./stream-analytics-build-an-iot-solution-using-stream-analytics.md)den här självstudien får du hjälp att bygga en komplett lösning med en data generator som simulerar trafik i en väg LED Ande.

* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)

* [Fråge exempel för vanliga Stream Analytics användnings mönster](stream-analytics-stream-analytics-query-patterns.md)

* [Förstå indata för Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
