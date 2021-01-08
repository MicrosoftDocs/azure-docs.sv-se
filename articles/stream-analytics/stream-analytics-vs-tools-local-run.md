---
title: Testa Azure Stream Analytics frågor lokalt i Visual Studio
description: Den här artikeln beskriver hur du testar frågor lokalt med Azure Stream Analytics verktyg för Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: b856826761f355e896cae48aa4a6fb62f5947e0b
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019932"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testa Stream Analytics frågor lokalt med Visual Studio

Du kan använda Azure Stream Analytics verktyg för Visual Studio för att testa dina Stream Analytics-jobb lokalt med exempel data eller [real tids data](stream-analytics-live-data-local-testing.md). 

Använd den här [snabb](stream-analytics-quick-create-vs.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med Visual Studio.

## <a name="test-your-query"></a>Testa frågan

I Azure Stream Analytics-projektet dubbelklickar du på **script. asaql** för att öppna skriptet i redigeraren. Du kan kompilera frågan för att se om det finns några syntaxfel. Frågeredigeraren stöder IntelliSense, mönstermatchningssyntax och en fel markör.

![Frågeredigeraren](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Lägg till lokal indatamängd

Om du vill verifiera din fråga mot lokala statiska data högerklickar du på indata och väljer **Lägg till lokal inmatning**.
   
![Skärm bild som visar meny alternativet Lägg till lokal indatakälla.](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
I popup-fönstret väljer du exempel data från din lokala sökväg och **Spara**.
   
![Lägg till lokal indatamängd](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
En fil med namnet **local_EntryStream.jspå** läggs automatiskt till i mappen indata.
   
![Lista över lokala filer för indatafiler](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Välj **Kör lokalt** i Frågeredigeraren. Du kan också trycka på F5.
   
![Kör lokalt](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
Utdata kan visas i ett tabell format direkt från Visual Studio.

![Utdata i tabell format](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Du kan hitta sökvägen för utdata från konsolens utdata. Tryck på valfri tangent för att öppna mappen result.
   
![Lokal körning](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Kontrol lera resultaten i den lokala mappen.
   
![Resultat från lokal mapp](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Exempelindata
Du kan också samla in exempel indata från dina indata-källor till en lokal fil. Högerklicka på konfigurations filen för indata och välj **exempel data**. 

![Exempeldata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Du kan bara hämta data direkt från Event Hubs eller IoT-hubbar. Andra ingångs källor stöds inte. I dialog rutan popup fyller du i den lokala sökvägen för att spara exempel data och väljer **exempel**.

![Exempel på data konfiguration](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Du kan se förloppet i fönstret **utdata** . 

![Exempel data utdata](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Stream Analytics jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)
