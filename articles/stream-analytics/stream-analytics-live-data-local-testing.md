---
title: Testa Live-data med Azure Stream Analytics för Visual Studio
description: Lär dig hur du testar ditt Azure Stream Analytics jobb lokalt med Live streaming-data.
author: ajetasin
ms.author: ajetasi
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 99e9ce85bb1e59b5016e836ff16d03cb3326ec8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020374"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)

Med Azure Stream Analytics verktyg för Visual Studio kan du testa jobb lokalt från IDE med hjälp av live event Streams från Azure Event Hub, IoT Hub och Blob Storage. Data lokal testning i real tid kan inte ersätta [prestanda-och skalbarhets testning](stream-analytics-streaming-unit-consumption.md) som du kan utföra i molnet, men du kan spara tid under funktionell testning genom att inte behöva skicka till molnet varje gången du vill testa ditt Stream Analytics jobb. Den här funktionen är i för hands version och bör inte användas för produktions arbets belastningar.

## <a name="testing-options"></a>Test alternativ

Följande lokala test alternativ stöds:

|**Indata**  |**Resultat**  |**Jobbtyp**  |
|---------|---------|---------|
|Lokala statiska data   |  Lokala statiska data   |   Moln/kant |
|Direktsända indata   |  Lokala statiska data   |   Moln |
|Direktsända indata   |  Live output-data   |   Moln |

## <a name="local-testing-with-live-data"></a>Lokal testning med real tids data

1. När du har skapat ett [Azure Stream Analytics Cloud-projekt i Visual Studio](stream-analytics-quick-create-vs.md)öppnar du **script. asaql**. Den lokala testningen använder som standard lokala indata och lokala utdata.

   ![Azure Stream Analytics Visual Studio lokal indata och lokal utdata](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Om du vill testa Live-data väljer du **Använd indata från molnet** i list rutan.

   ![Azure Stream Analytics Visual Studio Live Cloud-ingången](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Ange **Start tid** för att definiera när jobbet ska börja bearbeta indata. Jobbet kan behöva läsa indata i förväg för att säkerställa korrekta resultat. Standard tiden är inställt på 30 minuter före den aktuella tiden.

   ![Start tid för Azure Stream Analytics Visual Studio Live data](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klicka på **Kör lokalt**. Ett konsol fönster visas med pågående förlopp och jobb mått. Om du vill stoppa processen kan du göra det manuellt. 

   ![Azure Stream Analytics Visual Studio Live data process-fönstret](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Resultatet av utdata uppdateras var tredje sekund med de första 500 utmatnings raderna i fönstret för det lokala körnings resultatet och utdatafilerna placeras i mappen **ASALocalRun** för projekt Sök väg. Du kan också öppna utdatafilerna genom att klicka på knappen **Öppna resultat** i fönstret lokala körnings resultat.

   ![Azure Stream Analytics mapp för öppna resultat i Visual Studio Live data](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Om du vill spara resultatet i dina moln mottagare väljer du **utdata till molnet** i den andra List rutan. Power BI och Azure Data Lake Storage stöds inte av utgående mottagare.

   ![Azure Stream Analytics Visual Studio Live data output till molnet](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Begränsningar

* Power BI och Azure Data Lake Storage stöds inte utgående mottagare på grund av begränsningar för autentiserings modell.

* Endast moln indatatyper har stöd för [tids principer](./stream-analytics-time-handling.md) , medan alternativ för lokal indatamängd inte gör det.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktygen för Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)