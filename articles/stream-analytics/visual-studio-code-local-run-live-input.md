---
title: Testa Azure Stream Analytics frågor lokalt mot Real tids indata med hjälp av Visual Studio Code
description: Den här artikeln beskriver hur du testar frågor lokalt mot Real tids indata med hjälp av Azure Stream Analytics verktyg för Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: dd4966ee75e9dc0ff401823e4291f8d299c8893c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93122902"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testa Stream Analytics frågor lokalt mot Real tids indata med hjälp av Visual Studio Code

Du kan använda Azure Stream Analytics verktyg för Visual Studio Code för att testa dina Stream Analytics-jobb lokalt mot Real tids indata. Indatamängden kan komma från en källa som Azure Event Hubs eller Azure IoT Hub. Resultatet skickas som JSON-filer till en mapp i projektet med namnet **LocalRunOutputs**.

## <a name="prerequisites"></a>Förutsättningar

* Installera [.net Core SDK](https://dotnet.microsoft.com/download) och starta om Visual Studio Code.

* Använd [den här snabb](quick-create-visual-studio-code.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med hjälp av Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definiera en real tids indata

1. Högerklicka på mappen **indata** i Stream Analytics-projektet. Välj sedan **ASA: Lägg till inmatare** på snabb menyn.

   ![Lägg till indata från mappen Inputs](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Du kan också välja **Ctrl + Shift + P** för att öppna kommando paletten och ange **ASA: Lägg till inmatade**.

   ![Lägg till Stream Analytics inmatade objekt i Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Välj en typ av indatakälla från den nedrullningsbara listan.

   ![Välj IoT Hub som indatamängds alternativ](./media/quick-create-visual-studio-code/iot-hub.png)

3. Om du har lagt till indatamängden från kommando paletten väljer du det Stream Analytics fråga-skript som ska använda indatamängden. Den ska fyllas i automatiskt med fil Sök vägen till **myASAproj. asaql**.

   ![Välj ett Stream Analytics skript i Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Välj **Välj från dina Azure-prenumerationer** på den nedrullningsbara menyn.

    ![Välj från prenumerationer](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Konfigurera den nyligen genererade JSON-filen. Du kan använda funktionen CodeLens för att ange en sträng, välja i en nedrullningsbar listruta eller ändra texten direkt i filen. Följande skärm bild visar **val från dina prenumerationer** som exempel.

   ![Konfigurera indatatyper i Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Förhandsgranska ininformation

Se till att indata kommer genom att välja **Förhandsgranska data** i konfigurations filen för Live-indata från den översta raden. Vissa indata kommer från en IoT-hubb och visas i förhands gransknings fönstret. Det kan ta några sekunder innan för hands versionen visas.

 ![Förhandsgranska Live-ingångar](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Kör frågor lokalt

Återgå till Frågeredigeraren och välj **Kör lokalt**. Välj sedan **Använd Live-inmatare** i list rutan.

![Välj "kör lokalt" i Frågeredigeraren](./media/vscode-local-run/run-locally.png)

![Välj Använd Live-indatamängden](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Resultatet visas i det högra fönstret och uppdateras var tredje sekund. Du kan välja **Kör** för att testa igen. Du kan också välja **Öppna i mapp** för att se resultatet i Utforskaren och öppna dem med Visual Studio Code eller ett verktyg som Excel. Observera att resultat-filerna bara är tillgängliga i JSON-format.

Standard tiden för jobbet att börja skapa utdata har angetts till **nu**. Du kan anpassa tiden genom att välja **Start tiden för utdata** i resultat fönstret.

![Visa lokalt körnings resultat](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Nästa steg

* [Utforska Azure Stream Analytics-jobb med Visual Studio Code (för hands version)](visual-studio-code-explore-jobs.md)

* [Konfigurera CI/CD-pipelines med hjälp av NPM-paketet](./cicd-overview.md)