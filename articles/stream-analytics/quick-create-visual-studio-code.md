---
title: Snabb start – skapa ett Azure Stream Analytics jobb i Visual Studio Code
description: Den här snabb starten visar hur du kommer igång genom att skapa ett Stream Analytics jobb, konfigurera indata och utdata och definiera en fråga med Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 104dfe7b2ce253d7ae42c7484532cfd5b2993aec
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014619"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code (för hands version)

Den här snabb starten visar hur du skapar och kör ett Azure Stream Analytics jobb genom att använda tillägget Azure Stream Analytics-verktyg för Visual Studio Code. Exempel jobbet läser strömmande data från en Azure IoT Hub-enhet. Du definierar ett jobb som beräknar medeltemperaturen när den överstiger 27° och skriver utdatahändelser till en ny fil i Blob Storage.

> [!NOTE]
> Visual Studio-och Visual Studio Code-verktyg stöder inte jobb i regionerna Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra.

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure-portalen](https://portal.azure.com/).

* Installera [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Installera Azure Stream Analytics tools-tillägget

1. Öppna Visual Studio Code.

2. Från **tillägg** i den vänstra rutan söker du efter **Stream Analytics** och väljer **Installera** i tillägget **Azure Stream Analytics verktyg** .

3. När tillägget har installerats kontrollerar du att **Azure Stream Analytics verktyg** är synligt i **aktiverade tillägg**.

   ![Azure Stream Analytics verktyg under aktiverade tillägg i Visual Studio Code](./media/quick-create-visual-studio-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Aktivera Azure Stream Analytics verktygs tillägg

1. Välj **Azure** -ikonen i aktivitets fältet i Visual Studio Code. Under **Stream Analytics** på sido fältet väljer **du logga in på Azure**.

   ![Logga in på Azure i Visual Studio Code](./media/quick-create-visual-studio-code/azure-sign-in.png)

2. När du är inloggad visas namnet på ditt Azure-konto i statusfältet i det nedre vänstra hörnet i Visual Studio Code-fönstret.

> [!NOTE]
> Tillägget Azure Stream Analytics verktyg loggar automatiskt in dig nästa gången om du inte loggar ut. Om ditt konto har tvåfaktorautentisering, rekommenderar vi att du använder autentisering i stället för att använda en PIN-kod.
> Om du har problem med att visa en lista över resurser hjälper det vanligt vis att logga ut och logga in igen. Logga ut genom att ange kommandot `Azure: Sign Out` .

## <a name="prepare-the-input-data"></a>Förbereda indata

Innan du definierar Stream Analytics jobb bör du förbereda de data som senare är konfigurerade som indata för jobbet. Slutför följande steg för att förbereda de indata som krävs för jobbet:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **skapa en resurs**  >  **Sakernas Internet**  >  **IoT Hub**.

3. I rutan **IoT-hubb** anger du följande information:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Prenumeration  | \<Your subscription\> |  Välj den Azure-prenumeration som du vill använda. |
   |Resursgrupp   |   asaquickstart-resourcegroup  |   Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto. |
   |Region  |  \<Select the region that is closest to your users\> | Välj en geografisk plats där du kan vara värd för din IoT Hub. Använd den plats som är närmast dina användare. |
   |IoT-hubbnamn  | MyASAIoTHub  |   Välj ett namn för din IoT Hub.   |

   ![Skapa en IoT Hub](./media/quick-create-visual-studio-code/create-iot-hub.png)

4. Välj **Nästa: Ange storlek och skala**.

5. Välj **pris-och skalnings nivå**. För den här snabbstarten väljer du nivån **F1 – kostnadsfri** om den fortfarande är tillgänglig för din prenumeration. Om den kostnadsfria nivån inte är tillgänglig väljer du den lägsta tillgängliga nivån. Mer information finns i [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Storlek och skala din IoT Hub](./media/quick-create-visual-studio-code/iot-hub-size-and-scale.png)

6. Välj **Granska + skapa**. Granska informationen om IoT Hub och välj **skapa**. Det kan ta några minuter innan IoT-hubben har skapats. Du kan övervaka förloppet i **meddelande** fönstret.

7. På navigerings menyn i IoT Hub väljer du **Lägg till** under **IoT-enheter**. Lägg till ett ID för **enhets-ID** och välj **Spara**.

   ![Lägga till en enhet i IoT Hub](./media/quick-create-visual-studio-code/add-device-iot-hub.png)

8. När enheten har skapats öppnar du enheten från listan IoT- **enheter** . Kopiera strängen i **anslutnings strängen (primär nyckel)** och spara den i anteckningar om du vill använda den senare.

   ![Kopiera enhets anslutnings sträng för IoT Hub](./media/quick-create-visual-studio-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Köra IoT-simulatorn

1. Öppna [Raspberry Pi Azure IoT-onlinesimulatorn](https://azure-samples.github.io/raspberry-pi-web-simulator/) i en ny webbläsarflik eller ett fönster.

2. Ersätt plats hållaren på rad 15 med den IoT Hub enhets anslutnings sträng som du sparade tidigare.

3. Välj **Kör**. Utdata ska Visa sensor data och meddelanden som skickas till din IoT-hubb.

   ![Raspberry Pi Azure IoT online simulator med utdata](./media/quick-create-visual-studio-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Skala bloblagring

1. I det övre vänstra hörnet av Azure Portal väljer du **skapa ett resurs**  >  **lagrings**  >  **lagrings konto**.

2. I fönsterrutan **Skapa lagringskonto** anger du namn, plats och resursgrupp för lagringskonto. Välj samma plats och resurs grupp som den IoT-hubb som du skapade. Välj sedan **Granska och skapa** för att skapa kontot.

   ![Skapa lagringskonto](./media/quick-create-visual-studio-code/create-storage-account.png)

3. När du har skapat ditt lagrings konto väljer du panelen **blobbar** i **översikts** fönstret.

   ![Översikt över lagringskonto](./media/quick-create-visual-studio-code/blob-storage.png)

4. Från sidan **Blob Service** väljer du **Container** och anger ett namn för containern, till exempel **container1**. Lämna **offentlig åtkomst nivå** som **privat (ingen anonym åtkomst)** och välj **OK**.

   ![Skapa en blobcontainer](./media/quick-create-visual-studio-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. I Visual Studio Code väljer du **Ctrl + Shift + P** för att öppna kommando paletten. Ange sedan **ASA** och Select **ASA: skapa nytt projekt**.

   ![Skapa ett nytt projekt](./media/quick-create-visual-studio-code/create-new-project.png)

2. Ange ditt projekt namn, t. ex. **myASAproj**, och välj en mapp för projektet.

    ![Skapa ett projekt namn](./media/quick-create-visual-studio-code/create-project-name.png)

3. Det nya projektet läggs till i din arbets yta. Ett Stream Analytics projekt består av tre mappar: **indata**, **utdata** och **Functions**. Det innehåller även frågans skript **(*. asaql)**, en **JobConfig.jspå** fil och ett **asaproj.jsi** konfigurations filen.

    **asaproj.jsi** konfigurations filen innehåller information om indata, utdata och jobb konfigurations filen som krävs för att skicka Stream Analytics-jobbet till Azure.

    ![Stream Analytics projektfiler i Visual Studio Code](./media/quick-create-visual-studio-code/asa-project-files.png)

> [!Note]
> När du lägger till indata och utdata från paletten kommando, läggs motsvarande sökvägar till **asaproj.jspå** automatiskt. Om du lägger till eller tar bort indata eller utdata på disken direkt måste du manuellt lägga till eller ta bort dem från **asaproj.js**. Du kan välja att placera indata och utdata på en plats och sedan referera till dem i olika jobb genom att ange Sök vägarna i varje **asaproj.jsi** filen.

## <a name="define-the-transformation-query"></a>Definiera transformationsfrågan

1. Öppna **myASAproj. asaql** från projektmappen.

2. Lägg till följande fråga:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Definiera en Live-ingång

1. Högerklicka på mappen **indata** i Stream Analytics-projektet. Välj sedan **ASA: Lägg till inmatare** på snabb menyn.

    ![Lägg till indata från mappen Inputs](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

    Eller Välj **Ctrl + Shift + P** för att öppna kommando-paletten och ange **ASA: Lägg till inmatade**.

   ![Lägg till Stream Analytics inmatade objekt i Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Välj **IoT Hub** för indatatypen.

   ![Välj IoT Hub som indatamängds alternativ](./media/quick-create-visual-studio-code/iot-hub.png)

3. Om du har lagt till indatamängden från kommando paletten väljer du det Stream Analytics fråga-skript som ska använda indatamängden. Den ska fyllas i automatiskt med fil Sök vägen till **myASAproj. asaql**.

   ![Välj ett Stream Analytics skript i Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Välj **Välj från dina Azure-prenumerationer** på den nedrullningsbara menyn.

    ![Välj från prenumerationer](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Redigera den nyligen genererade **IoTHub1.jspå** filen med följande värden. Behåll standardvärdena för fält som inte nämns här.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Namn|Indata|Ange ett namn för att identifiera jobbets indatatyper.|
   |IotHubNamespace|MyASAIoTHub|Välj eller ange namnet på din IoT Hub. Namn på IoT Hub identifieras automatiskt om de har skapats i samma prenumeration.|
   |SharedAccessPolicyName|iothubowner| |

   Du kan använda funktionen CodeLens för att ange en sträng, välja i en nedrullningsbar listruta eller ändra texten direkt i filen. Följande skärm bild visar **val från dina prenumerationer** som exempel. Autentiseringsuppgifterna anges automatiskt och sparas i Hanteraren för lokal autentiseringsuppgift.

   ![Konfigurera indatatyper i Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

   ![Konfigurera indatamängds värde i Visual Studio Code](./media/quick-create-visual-studio-code/configure-input-value.png)

## <a name="preview-input"></a>Förhandsgranska ininformation

Välj **Förhandsgranska data** i **IoTHub1.jspå** den översta raden. Vissa indata hämtas från IoT Hub och visas i förhands gransknings fönstret. Den här processen kan ta en stund.

 ![Förhandsgranska Live-ingångar](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-an-output"></a>Definiera utdata

1. Välj **Ctrl + Shift + P** för att öppna kommando paletten. Ange sedan **ASA: Lägg till utdata**.

   ![Lägg till Stream Analytics utdata i Visual Studio Code](./media/quick-create-visual-studio-code/add-output.png)

2. Välj **Blob Storage** för typ av mottagare.

3. Välj det Stream Analytics fråga-skript som ska använda den här indatamängden.

4. Ange namnet på utdatafilen som **BlobStorage**.

5. Redigera **BlobStorage** med hjälp av följande värden. Behåll standardvärdena för fält som inte nämns här. Använd funktionen CodeLens för att hjälpa dig att välja i en listruta eller ange en sträng.

   |Inställning|Föreslaget värde|Beskrivning|
   |-------|---------------|-----------|
   |Namn|Resultat| Ange ett namn för att identifiera jobbets utdata.|
   |Lagringskonto|asaquickstartstorage|Välj eller ange namnet på ditt lagrings konto. Lagrings konto namn identifieras automatiskt om de har skapats i samma prenumeration.|
   |Container|container1|Välj en befintlig container som du skapade i ditt lagringskonto.|
   |Sökvägsmönster|utdata|Ange namnet för en sökväg som ska skapas i containern.|

   ![Konfigurera utdata i Visual Studio Code](./media/quick-create-visual-studio-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilera skriptet

Skript kompileringen kontrollerar syntaxen och genererar Azure Resource Manager mallar för automatisk distribution.

Det finns två sätt att utlösa skript kompilering:

- Välj skriptet från arbets ytan och kompilera sedan från kommando-paletten.

   ![Använd kommando paletten Visual Studio Code för att kompilera skriptet](./media/quick-create-visual-studio-code/compile-script1.png)

- Högerklicka på skriptet och välj **ASA: kompilera skript**.

    ![Högerklicka på Stream Analytics-skriptet för att kompilera](./media/quick-create-visual-studio-code/compile-script2.png)

Efter kompileringen kan du hitta de två genererade Azure Resource Manager mallarna i mappen **distribuera** i projektet. De här två filerna används för automatisk distribution.

![Mallar för Stream Analytics distribution i Utforskaren](./media/quick-create-visual-studio-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Skicka ett Stream Analytics jobb till Azure

1. I fönstret skript redigeraren i ditt fråge skript väljer **du skicka till Azure**.

   ![Välj från din prenumerations text i skript redigeraren](./media/quick-create-visual-studio-code/submit-job.png)

2. Välj din prenumeration i popup-listan.

3. Välj **Välj ett jobb**. Välj sedan **Skapa nytt jobb**.

4. Ange ditt jobb namn, **myASAjob**. Följ sedan anvisningarna för att välja resurs grupp och plats.

5. Välj **Skicka till Azure**. Du hittar loggarna i fönstret utdata. 

6. När jobbet har skapats kan du se det i **Stream Analytics Explorer**.

    ![Listat jobb i Stream Analytics Explorer](./media/quick-create-visual-studio-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Starta Stream Analytics-jobbet och kontrollera utdata

1. Öppna **Stream Analytics Explorer** i Visual Studio Code och hitta jobbet, **myASAJob**.

2. Högerklicka på jobb namnet. Välj sedan **Starta** från snabb menyn.

   ![Starta Stream Analytics jobb i Visual Studio Code](./media/quick-create-visual-studio-code/start-asa-job-vs-code.png)

3. Välj **nu** i popup-fönstret för att starta jobbet.

4. Observera att jobb statusen har ändrats till att **köras**. Högerklicka på jobb namnet och välj **Öppna jobb visning i portalen** för att se händelse mått för indata och utdata. Den här åtgärden kan ta några minuter.

5. Om du vill visa resultaten öppnar du blob-lagringen i Visual Studio Code-tillägget eller i Azure Portal.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resurs gruppen, direkt uppspelnings jobbet och alla relaterade resurser. Om du tar bort jobbet undviks faktureringen av de strömnings enheter som jobbet förbrukar. 

Om du planerar att använda jobbet i framtiden kan du stoppa det och starta om det senare. Om du inte kommer att använda det här jobbet igen använder du följande steg för att ta bort alla resurser som du skapade i den här snabb starten:

1. Välj **resurs grupper** på den vänstra menyn i Azure Portal och välj sedan namnet på den resurs som du har skapat.  

2. Välj **Ta bort** på din resursgruppssida. Ange namnet på resursen som ska tas bort i text rutan och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du distribuerat ett enkelt Stream Analytics jobb med hjälp av Visual Studio Code. Du kan också distribuera Stream Analytics jobb med hjälp av [Azure Portal](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)och [Visual Studio](stream-analytics-quick-create-vs.md).

Om du vill veta mer om Azure Stream Analytics Tools för Visual Studio Code fortsätter du till följande artiklar:

* [Testa Stream Analytics frågor lokalt med exempel data med Visual Studio Code](visual-studio-code-local-run.md)

* [Testa Azure Stream Analytics jobb lokalt mot Live-inflöde med Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Använd Visual Studio Code för att Visa Azure Stream Analytics-jobb](visual-studio-code-explore-jobs.md)

* [Konfigurera CI/CD-pipelines med hjälp av NPM-paketet](./cicd-overview.md)