---
title: Snabb start – skapa ett Azure Stream Analytics jobb med Visual Studio
description: Den här snabbstarten visar hur du kommer igång genom att skapa ett Stream Analytics-jobb, konfigurerar indata och utdata samt definierar en fråga med Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.openlocfilehash: 7a02d0914ae111d6e2e00493fe27613e27d836b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012367"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-visual-studio"></a>Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio

Den här snabbstarten visar hur du skapar och kör ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktyg för Visual Studio. Exempeljobbet läser strömmande data från en IoT Hub-enhet. Du definierar ett jobb som beräknar medeltemperaturen när den överstiger 27° och skriver utdatahändelser till en ny fil i Blob Storage.

> [!NOTE]
> Visual Studio-och Visual Studio Code-verktyg stöder inte jobb i regionerna Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure-portalen](https://portal.azure.com/).

* Installera Visual Studio 2019, Visual Studio 2015 eller Visual Studio 2013 uppdatering 4. Versionerna Enterprise (Ultimate/Premium), Professional och Community stöds. Versionen Express stöds inte.

* Följ [installationsanvisningarna](./stream-analytics-tools-for-visual-studio-install.md) för att installera Stream Analytics-verktygen för Visual Studio.

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics-jobbet bör du förbereda de data som senare konfigureras som jobbindata. Förbered de indata som krävs för jobbet genom att utföra följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **skapa en resurs**  >  **Sakernas Internet**  >  **IoT Hub**.

3. I rutan **IoT-hubb** anger du följande information:
   
   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Prenumeration  | \<Your subscription\> |  Välj den Azure-prenumeration som du vill använda. |
   |Resursgrupp   |   asaquickstart-resourcegroup  |   Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto. |
   |Region  |  \<Select the region that is closest to your users\> | Välj en geografisk plats där du kan hantera din IoT-hubb. Använd den plats som är närmast dina användare. |
   |IoT-hubbnamn  | MyASAIoTHub  |   Välj ett namn för din IoT-hubb.   |

   ![Skapa en IoT Hub](./media/stream-analytics-quick-create-vs/create-iot-hub.png)

4. Välj **Nästa: Ange storlek och skala**.

5. Välj **pris- och skalningsnivå**. För den här snabbstarten väljer du nivån **F1 – kostnadsfri** om den fortfarande är tillgänglig för din prenumeration. Om den kostnadsfria nivån inte är tillgänglig väljer du den lägsta tillgängliga nivån. Mer information finns i [Prissättning för IoT-hubb](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Bestäm storlek och skala för din IoT-hubb](./media/stream-analytics-quick-create-vs/iot-hub-size-and-scale.png)

6. Välj **Granska + skapa**. Gå igenom informationen om IoT-hubben och klicka på **Skapa**. Det kan ta några minuter innan IoT-hubben skapas. Du kan övervaka förloppet i **meddelandefönstret**.

7. I navigeringsmenyn för din IoT-hubb klickar du på **Lägg till** under **IoT-enheter**. Lägg till ett **Enhets-ID** och klicka på **Spara**.

   ![Lägg till en enhet i din IoT-hubb](./media/stream-analytics-quick-create-vs/add-device-iot-hub.png)

8. När enheten har skapats öppnar du enheten från listan över **IoT-enheter**. Kopiera **Anslutningssträng – primärnyckel** och spara den i en anteckningsfil för senare användning.

   ![Kopiera anslutningssträngen för IoT-hubbenhet](./media/stream-analytics-quick-create-vs/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Skala bloblagring

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**.

2. I fönsterrutan **Skapa lagringskonto** anger du namn, plats och resursgrupp för lagringskonto. Välj samma plats och resursgrupp som den IoT-hubb som du skapade. Klicka sedan på **Granska + skapa** för att skapa kontot.

   ![Skapa lagringskonto](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. När ditt lagringskonto har skapats väljer du panelen **Blobar** på panelen **Översikt**.

   ![Översikt över lagringskonto](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Från sidan **Blob Service** väljer du **Container** och anger ett namn för containern, till exempel *container1*. Låt **Offentlig åtkomstnivå** vara **Privat (ingen anonym åtkomst)** och välj **OK**.

   ![Skapa blobcontainer](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Starta Visual Studio.

2. Välj **Arkiv > Nytt projekt**.  

3. I listan över mallar till vänster väljer du **Stream Analytics** och sedan **Azure Stream Analytics-programmet**.  

4. Ange projektets **namn**, **plats** och **lösningsnamn**. Välj sedan **OK**.

   ![Skapa ett Stream Analytics-projekt](./media/stream-analytics-quick-create-vs/create-stream-analytics-project.png)

Lägg märke till de element som ingår i ett Azure Stream Analytics-projekt.

   <img src="./media/stream-analytics-quick-create-vs/stream-analytics-project.png" alt="Azure Stream Analytics project elements" width="300px"/>


## <a name="choose-the-required-subscription"></a>Välj den prenumeration som krävs

1. Gå till menyn **Visa** och välj **Server Explorer** i Visual Studio.

2. Högerklicka på **Azure**, välj **Anslut till Microsoft Azure-prenumeration** och logga in med ditt Azure-konto.

## <a name="define-input"></a>Definiera indata

1. I **Solution Explorer** expanderar du noden för **indata** och dubbelklickar på **Input.json**.

2. Fyll i informationen för **indatakonfiguration av Stream Analytics** med följande värden:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**   |
   |---------|---------|---------|
   |Indataalias  |  Indata   |  Ange ett namn som identifierar jobbets indata.   |
   |Källtyp   |  Dataström |  Välj lämplig indatakälla: dataström eller referensdata.   |
   |Källa  |  IoT Hub |  Välj lämplig indatakälla.   |
   |Resurs  | Välj datakälla från det aktuella kontot | Välj att ange data manuellt eller välj ett befintligt konto.   |
   |Prenumeration  |  \<Your subscription\>   | Välj den Azure-prenumeration som innehåller den IoT-hubb du skapade.   |
   |IoT Hub  |  MyASAIoTHub   |  Välj eller ange namnet på IoT-hubben. IoT-hubbnamn identifieras automatiskt om de skapas i samma prenumeration.   |
   
3. Låt standardvärdena stå kvar för övriga alternativ och välj **Spara** för att spara inställningarna.  

   ![Konfigurera indata](./media/stream-analytics-quick-create-vs/stream-analytics-vs-input.png)

## <a name="define-output"></a>Definiera utdata

1. I **Solution Explorer** expanderar du noden för **utdata** och dubbelklickar på **Output.json**.

2. Fyll i informationen för **utdatakonfiguration av Stream Analytics** med följande värden:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**   |
   |---------|---------|---------|
   |Utdataalias  |  Utdata   |  Ange ett namn som identifierar jobbets utdata.   |
   |Kanalmottagare   |  Blob Storage |  Välj lämplig kanalmottagare.    |
   |Resurs  |  Ange inställningar för datakälla manuellt |  Välj att ange data manuellt eller välj ett befintligt konto.   |
   |Prenumeration  |  \<Your subscription\>   | Välj den Azure-prenumeration där det lagringskonto som du skapade finns. Lagringskontot kan vara i samma eller en annan prenumeration. I det här exemplet förutsätts att du har skapat lagringskontot i samma prenumeration.   |
   |Lagringskonto  |  asaquickstartstorage   |  Välj eller ange lagringskontots namn. Lagringskontonamn identifieras automatiskt om de skapas i samma prenumeration.   |
   |Container  |  container1   |  Välj en befintlig container som du skapade i ditt lagringskonto.   |
   |Sökvägsmönster  |  utdata   |  Ange namnet för en sökväg som ska skapas i containern.   |
   
3. Låt standardvärdena stå kvar för övriga alternativ och välj **Spara** för att spara inställningarna.  

   ![Konfigurera utdata](./media/stream-analytics-quick-create-vs/stream-analytics-vs-output.png)

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

1. Öppna **Script.asaql** från **Solution Explorer** i Visual Studio.

2. Lägg till följande fråga:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="submit-a-stream-analytics-query-to-azure"></a>Skicka en Stream Analytics-fråga till Azure

1. I **frågeredigeraren** väljer du **Submit to Azure** (Skicka till Azure) i skriptredigeraren.

2. Välj **Create a New Azure Stream Analytics job** (Skapa ett nytt Azure Stream Analytics-jobb) och ange ett **jobbnamn**. Välj den **prenumeration**, **resursgrupp** och **plats** som du använde i början av snabbstarten.

   ![Skicka jobbet till Azure](./media/stream-analytics-quick-create-vs/stream-analytics-job-to-azure.png)

## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/) i en ny webbläsarflik eller ett fönster.

2. Ersätt platshållaren på rad 15 med Azure IoT Hub-enhetens anslutningssträng, som du sparade i föregående avsnitt.

3. Klicka på **Kör**. Utdata bör visas de sensordata och meddelanden som skickas till din IoT-hubb.

   ![Raspberry Pi Azure IoT-onlinesimulator](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

1. Jobbvyn öppnas automatiskt när jobbet har skapats. Välj den gröna pilknappen för att starta jobbet.

   ![Starta Stream Analytics-jobb](./media/stream-analytics-quick-create-vs/start-stream-analytics-job-vs.png)

2. Ändra **Job output start mode** (Startläge för jobbutdata) till **JobStartTime** och välj **Starta**.

   ![Starta jobbkonfiguration](./media/stream-analytics-quick-create-vs/stream-analytics-start-configuration.png)

3. Observera att jobbstatusen har ändrats till **Körs** och att det finns händelser med indata/utdata. Det kan ta några minuter.

   ![Köra Stream Analytics-jobb](./media/stream-analytics-quick-create-vs/stream-analytics-job-running.png)

4. För att visa resultatet, gå till menyn **Visa** och välj **Cloud Explorer**. Navigera till lagringskontot i resursgruppen. Under **Blob-behållare** dubbelklickar du på **container1** och sedan på filsökvägen för **utdata**.

   ![Visa resultat](./media/stream-analytics-quick-create-vs/stream-analytics-vs-results.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, strömningsjobbet och alla relaterade resurser när de inte längre behövs. Om du tar bort jobbet undviker du att bli fakturerad för de strömmande enheter som används av jobbet. Om du planerar att använda jobbet i framtiden kan du stoppa det och sedan starta det igen när du behöver det. Om du inte tänker fortsätta använda det här jobbet tar du bort alla resurser som skapades i snabbstarten med följande steg:

1. Klicka på **Resursgrupper** på den vänstra menyn i Azure-portalen och välj sedan namnet på den resurs du skapade.  

2. På sidan med resursgrupper klickar du på **Ta bort**, skriver in namnet på resursen som ska tas bort i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat ett enkelt Stream Analytics-jobb med Visual Studio. Du kan även distribuera Stream Analytics-jobb med [Azure-portalen](stream-analytics-quick-create-portal.md) och [PowerShell](stream-analytics-quick-create-powershell.md). 

Fortsätt till följande artikel om du vill veta mer om Azure Stream Analytics-verktyg för Visual Studio:

> [!div class="nextstepaction"]
> [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)