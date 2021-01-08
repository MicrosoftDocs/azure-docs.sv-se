---
title: Azure Stream Analytics Edge-jobb i Visual Studio
description: I den här artikeln beskrivs hur du skapar, felsöker och skapar Stream Analytics för IoT Edge-jobb med hjälp av Stream Analytics verktyg för Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 09151ea0fe3d419401d576149f6655b8cdc09f8e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019966"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Utveckla Stream Analytics Edge-jobb med Visual Studio-verktyg

I den här självstudien får du lära dig hur du använder Stream Analytics verktyg för Visual Studio. Du får lära dig hur du skapar, felsöker och skapar dina Stream Analytics Edge-jobb. När du har skapat och testat jobbet kan du gå till Azure Portal för att distribuera det till dina enheter. 

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande krav för att slutföra den här kursen:

* Installera [Visual studio 2019](https://visualstudio.microsoft.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/)eller [Visual Studio 2013 uppdatering 4](https://www.microsoft.com/download/details.aspx?id=45326). Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Express-versionen stöds inte.  

* Följ [installations anvisningarna](stream-analytics-tools-for-visual-studio-edge-jobs.md) för att installera Stream Analytics verktyg för Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Skapa ett Stream Analytics Edge-projekt 

Välj **fil**  >  **nytt**  >  **projekt** i Visual Studio. Navigera till listan **mallar** till vänster > expandera **Azure Stream Analytics**  >  **Stream Analytics Edge**  >  **Azure Stream Analytics Edge-program**. Ange namn, plats och lösnings namn för projektet och välj **OK**.

![Nytt Stream Analytics Edge-projekt i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

När projektet har skapats går du till **Solution Explorer** för att Visa mapphierarkin.

![Solution Explorer-vy över Stream Analytics Edge-jobb](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Välj rätt prenumeration

1. Från Visual Studio **View** -menyn väljer du **Server Explorer**.  

2. Högerklicka på **Azure** > Välj **Anslut till Microsoft Azure prenumerations** > och logga sedan in med ditt Azure-konto.

## <a name="define-inputs"></a>Definiera indata

1. Från **Solution Explorer** expanderar **du noden indata.** du bör se indata med namnet **EdgeInput.jspå**. Dubbelklicka om du vill visa dess inställningar.  

2. Ange käll typen som **data ström**. Ange sedan källa till **Edge Hub**, format för händelse serialisering till **JSON** och kodning till **utf8**. Du kan också byta namn på det **angivna aliaset**, låt oss lämna det som det är för det här exemplet. Om du byter namn på Indataporten använder du det namn som du angav när du definierade frågan. Spara inställningarna genom att klicka på **Spara**.  
   ![Stream Analytics konfiguration av inmatade jobb](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definiera utmatningar

1. Från **Solution Explorer** expanderar du noden **utdata** . du bör se utdata som heter **EdgeOutput.jspå**. Dubbelklicka om du vill visa dess inställningar.  

2. Se till att ange mottagare för att välja **Edge Hub**, ange format för händelse serialisering till **JSON**, ange encoding till **utf8** och ange format **mat ris**. Om du vill kan du byta namn på **utdataporten** och låta det vara kvar i det här exemplet. Om du byter namn på utdataporten använder du det namn som du angav när du definierade frågan. Spara inställningarna genom att klicka på **Spara**. 
   ![Stream Analytics jobbets utdata-konfiguration](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

Stream Analytics jobb som distribueras i Stream Analytics IoT Edge-miljöer stöder de flesta [Stream Analytics frågespråk](/stream-analytics-query/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Följande åtgärder stöds dock ännu inte för Stream Analytics Edge-jobb: 


|**Kategori**  | **Kommando**  |
|---------|---------|
|Andra operatörer | <ul><li>PARTITION AV</li><li>TIDSSTÄMPEL ÖVER</li><li>UDF för JavaScript</li><li>Användardefinierade agg regeringar (UDA)</li><li>GetMetadataPropertyValue</li><li>Använda fler än 14 mängder i ett enda steg</li></ul>   |

När du skapar ett Stream Analytics Edge-jobb i portalen, varnar kompilatorn automatiskt om du inte använder en operatör som stöds.

I Visual Studio definierar du följande omvandlings fråga i Frågeredigeraren (**script. asaql-fil**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testa jobbet lokalt

Om du vill testa frågan lokalt ska du ladda upp exempel data. Du kan hämta exempel data genom att ladda ned registrerings data från [GitHub-lagringsplatsen](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) och spara den på din lokala dator. 

1. Om du vill ladda upp exempel data högerklickar du på **EdgeInput.jspå** fil och väljer **Lägg till lokal inmatning**  

2. I popup-fönstret > **Bläddra bland** exempel data från din lokala sökväg > väljer du **Spara**.
   ![Lokal indatamängds konfiguration i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. En fil med namnet **local_EdgeInput.jspå** läggs automatiskt till i mappen indata.  
4. Du kan antingen köra den lokalt eller skicka till Azure. Om du vill testa frågan väljer du **Kör lokalt**.  
   ![Stream Analytics jobb körnings alternativ i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. Kommando tolkens fönster visar jobbets status. När jobbet har körts skapas en mapp som ser ut som "2018-02-23-11-31-42" i sökvägen till projektmappen "Visual Studio 2015 \ Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Navigera till mappens sökväg för att visa resultaten i den lokala mappen:

   Du kan också logga in på Azure Portal och kontrol lera att jobbet har skapats. 

   ![Mappen Stream Analytics jobb resultat](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Skicka jobbet till Azure

1. Innan du skickar jobbet till Azure måste du ansluta till din Azure-prenumeration. Öppna **Server Explorer** > Högerklicka på **Azure**  >  **Connect för att Microsoft Azure prenumeration** > logga in på din Azure-prenumeration.  

2. Om du vill skicka jobbet till Azure navigerar du till Frågeredigeraren > väljer **Skicka till Azure**.  

3. Ett popup-fönster öppnas. Välj att uppdatera ett befintligt Stream Analytics Edge-jobb eller skapa ett nytt. När du uppdaterar ett befintligt jobb ersätts alla jobb konfigurationen i det här scenariot, och du kommer att publicera ett nytt jobb. Välj **skapa ett nytt Azure Stream Analytics jobb** > ange ett namn för jobbet något som **MyASAEdgeJob** > Välj den **prenumeration**, **resurs grupp** och **plats** som krävs > Välj **Skicka**.

   ![Skicka Stream Analytics jobb till Azure från Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   Nu har ditt Stream Analytics Edge-jobb skapats. Du kan läsa mer om hur du distribuerar det till dina enheter med hjälp av [självstudierna Kör jobb i IoT Edge](stream-analytics-edge.md) . 

## <a name="manage-the-job"></a>Hantera jobbet 

Du kan visa status för jobbet och jobb diagrammet från Server Explorer. Från **Stream Analytics** i **Server Explorer** expanderar du prenumerationen och resurs gruppen där du distribuerade Stream Analytics Edge-jobbet. Du kan visa MyASAEdgejob med status **skapad**. Expandera noden jobb och dubbelklicka på den för att öppna jobb visningen.

![Alternativ för jobb hantering i Server Explorer](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
I fönstret jobb visning visas åtgärder som att uppdatera jobbet, ta bort jobbet och öppna jobbet från Azure Portal.

![Jobb diagram och andra alternativ i Visual Studio](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Nästa steg

* [Mer information om Azure IoT Edge](../iot-edge/about-iot-edge.md)
* [ASA på IoT Edge självstudie](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Skicka feedback till teamet med hjälp av den här undersökningen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)