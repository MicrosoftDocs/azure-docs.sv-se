---
title: Självstudie – Visa resultat från Azure IoT Hub meddelanderoutning (.NET) | Microsoft Docs
description: Självstudie – när du har konfigurerat alla resurser som använder del 1 av självstudien lägger du till möjligheten att dirigera meddelanden till Azure Stream Analytics och visa resultaten i Power BI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f441a1cf97c069c4755b436bbb8cb9268b469eb3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199755"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Självstudie: del 2 – Visa dirigerade meddelanden

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regler för routning av meddelanden

Reglerna för meddelanderoutning, dessa konfigurerades i del 1 av den här självstudien och du ser dem i den här andra delen.

|Värde |Resultat|
|------|------|
|level="storage" |Skriv till Azure Storage.|
|level="critical" |Skriv till en Service Bus-kö. En logikapp hämtar meddelandet från kön och använder Office 365 för att skicka meddelandet via e-post.|
|standard |Visa dessa data med Power BI.|

Nu skapar du de resurser som meddelandena ska dirigeras till, kör en app för att skicka meddelanden till hubben och se hur routningen fungerar.

## <a name="create-a-logic-app"></a>Skapa en logikapp  

Service Bus-kön ska användas för att ta emot meddelanden som har angetts som kritiska. Konfigurera en logikapp som ska övervaka Service Bus-kön och som skickar e-post när ett meddelande läggs till i Service Bus-kön.

1. I [Azure Portal](https://portal.azure.com)väljer du **+ skapa en resurs**. Skriv **logikapp** i sökrutan och klicka på Retur. Välj Logic app i Sök resultaten som visas och välj sedan **skapa** för att fortsätta till fönstret **skapa Logic app** . Fyll i fälten.

   **Namn**: Det här fältet är logikappens namn. I självstudien används **ContosoLogicApp**.

   **Prenumeration**: Välj din Azure-prenumeration.

   **Resurs grupp**: Välj **Använd befintlig** och välj din resurs grupp. I den här självstudien används **ContosoResources**.

   **Plats**: Använd din plats. I den här självstudien används **USA, västra**.

   **Aktivera Log Analytics**: den här växlingen ska vara avstängd.

   ![Skärmen skapa Logic app](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Välj **Skapa**. Det kan ta några minuter innan appen distribueras.

2. Gå nu till logikappen. Det enklaste sättet att komma till logi Kap par är att välja **resurs grupper**, välja resurs grupp (den här självstudien använder **ContosoResources**) och sedan välja Logic-appen i listan över resurser. 

    Sidan Logikappdesigner visas (du kanske måste rulla över till höger för att se hela sidan). På sidan Logic Apps designer bläddrar du nedåt tills du ser panelen som säger **Tom Logic app +** och markerar den. Standard-fliken är "för dig". Om fönstret är tomt väljer du **alla** för att se alla anslutningar och utlösare som är tillgängliga.

3. Välj **Service Bus** i listan över kopplingar.

   ![Listan över kopplingar](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. En lista med utlösare visas. Välj **när ett meddelande tas emot i en kö (automatisk komplettering)/Service Bus**.

   ![Listan över utlösare för Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Fyll i namnet på anslutningen på nästa skärm. I den här självstudien används **ContosoConnection**.

   ![Konfigurera anslutningen för Service Bus kön](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Välj Service Bus namn området. I den här självstudien används **ContosoSBNamespace**. När du väljer namnrymd ber portalen Service Bus-namnrymden att hämta nycklarna. Välj **RootManageSharedAccessKey** och välj **skapa**.

   ![Konfigurationen av anslutningen slutförs](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. På nästa skärm väljer du namnet på kön (den här självstudien använder **contososbqueue**) i listrutan. Du kan använda standardvärdena för resten av fälten.

   ![Kös alternativ](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Konfigurera nu att åtgärden ska skicka ett e-postmeddelande när ett meddelande kommer till kön. I Logic Apps designer väljer du **+ nytt steg** för att lägga till ett steg och väljer sedan **alla** för att se alla tillgängliga alternativ. I fönstret **Välj en åtgärd** letar du reda på och väljer **Office 365 Outlook**. På sidan åtgärder väljer du **skicka en e-post/Office 365 Outlook**.  

   ![Alternativen för Office365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Logga in på ditt arbets-eller skol konto för att konfigurera anslutningen. Försök igen om den här tiden är slut. Ange e-postadresser för mottagare av e-postmeddelanden. Ange även ämne och skriv vilket meddelande du vill att mottagaren ska se i brödtexten. För att testa fyller du i din egen e-postadress som mottagare.

   Välj **Lägg till dynamiskt innehåll** om du vill visa innehållet från meddelandet som du kan ta med. Välj **Innehåll** – det inkluderar meddelandet i e-postmeddelandet.

   ![E-postalternativ för Logic app](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Välj **Spara**. Logikappdesignern öppnas.

## <a name="set-up-azure-stream-analytics"></a>Konfigurera Azure Stream Analytics

Om du vill se data i Power BI-visualiseringen konfigurerar du först ett Stream Analytics-jobb för att hämta data. Kom ihåg att endast meddelanden där **nivå** är satt till **normal** skickas till standardslutpunkten och hämtas av Stream Analytics-jobbet för Power BI-visualiseringen.

### <a name="create-the-stream-analytics-job"></a>Skapa Stream Analytics-jobbet

1. I [Azure Portal](https://portal.azure.com)väljer du **skapa en resurs**  >  **Sakernas Internet**  >  **Stream Analytics jobb**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt. I självstudien används **contosoJob**.

   **Prenumeration**: Azure-prenumerationen som du använder för självstudien.

   **Resursgrupp**: Använd samma resursgrupp som användes av IoT-hubben. I den här självstudien används **ContosoResources**.

   **Plats**: Använd samma plats som användes i installationsskriptet. I den här självstudien används **USA, västra**.

   ![Skapa Stream Analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Välj **skapa** för att skapa jobbet. Det kan ta några minuter att distribuera.

    Om du vill återgå till jobbet väljer du **resurs grupper**. I den här självstudien används **ContosoResources**. Välj resurs gruppen och välj sedan Stream Analytics jobb i listan över resurser.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Under **jobb sto pol Ogin** väljer du **indata**.

2. I rutan **indata** väljer du **Lägg till Stream-indata** och väljer IoT Hub. På skärmen som visas fyller du i följande fält:

   **Inmatat alias**: Den här självstudien använder **contosoinputs**.

   **Välj IoT Hub från din prenumeration**: Välj alternativet för alternativ knapp.

   **Prenumeration**: Välj den Azure-prenumeration som du använder för den här självstudien.

   **IoT Hub**: Välj IoT Hub. I självstudien används **ContosoTestHub**.

   **Slutpunkt**: Välj **Meddelanden**. (Om du väljer Åtgärdsövervakning får du telemetridata om IoT-hubben istället för de data du skickar.) 

   **Princip namn för delad åtkomst**: Välj **tjänst**. Portalen fyller i Nyckel för princip för delad åtkomst åt dig.

   **Konsument grupp**: Välj den konsument grupp som ställts in i del 1 av den här självstudien. I den här självstudien används **contosoconsumers**.
   
   För resten av fälten accepterar du standardvärdena. 

   ![Konfigurera indata för Stream Analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **jobb sto pol Ogin** väljer du **utdata**.

2. I fönstret **utdata** väljer du **Lägg till** och väljer sedan **Power BI**. På skärmen som visas fyller du i följande fält:

   **Utdataalias**: Utdatas unika alias. I självstudien används **contosooutputs**. 

   **Namn på datauppsättning**: Namnet på datauppsättningen som ska användas i Power BI. I självstudien används **contosodataset**. 

   **Tabellnamn**: Namnet på tabellen som ska användas i Power BI. I självstudien används **contosotable**.

  **Autentiseringsläge**: Välj det läge som ska användas.

   För resten av fälten accepterar du standardvärdena.

3. Välj **auktorisera** och logga in på ditt Power BI-konto. (Det kan ta mer än ett try).

   ![Konfigurera utdata för Stream Analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Under **jobb sto pol Ogin** väljer du **fråga**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet. I självstudien används **contosoinputs**.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet. I självstudien används **contosooutputs**.

   ![Ställ in frågan för Stream Analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Välj **Spara**.

5. Stäng rutan Fråga. Du återgår till vyn över resurserna i resurs gruppen. Välj Stream Analytics jobb. I den här självstudiekursen kallas det **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics jobb väljer du **Starta**  >  **nu**  >  . När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

För att konfigurera Power BI-rapporten behöver du data, så du måste konfigurera Power BI när du har skapat enheten och kör enhetssimuleringsprogrammet.

## <a name="run-simulated-device-app"></a>Kör den simulerade Device-appen

I del 1 av den här självstudien konfigurerar du en enhet för att simulera användning av en IoT-enhet. I det här avsnittet hämtar du .NET-konsolen som simulerar att enheten skickar meddelanden från enhet till moln till en IoT-hubb (förutsatt att du inte redan har laddat ned appen och resurserna i del 1).

Det här programmet skickar meddelanden för var och en av de olika metoderna för meddelanderoutning. Det finns också en mapp i nedladdningen som innehåller den fullständiga Azure Resource Manager mall-och parameter filen, samt Azure CLI-och PowerShell-skript.

Om du inte hämtade filerna från lagrings platsen i del 1 av den här självstudien kan du gå vidare och ladda ned dem från [IoT Device simulering](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Om du väljer den här länken hämtas en lagrings plats med flera program. lösningen du söker efter är IoT-Hub/självstudier/routing/IoT_SimulatedDevice. SLN. 

Dubbelklicka på lösnings filen (IoT_SimulatedDevice. SLN) för att öppna koden i Visual Studio och öppna sedan Program.cs. Ersätt `{your hub name}` med IoT-hubbens värdnamn. Formatet för IoT-hubbens värdnamn är **{iot-hub-name}.azure-devices.net**. För den här självstudien är hubbens värdnamn **ContosoTestHub.azure-devices.net**. Ersätt därefter `{your device key}` med enhetsnyckeln du sparade tidigare när du konfigurerade den simulerade enheten. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Kör och testa

Kör konsolprogrammet. Vänta några minuter. Du kan se meddelandena som skickas på konsolskärmen i programmet.

Appen skickar ett nytt enhet till molnet-meddelande till IoT-hubben varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID, temperatur, luftfuktighet och meddelandenivå, där standardinställningen är `normal`. Slumpmässigt tilldelas en nivå på `critical` eller `storage`, vilket orsakar att meddelandet dirigeras till lagringskontot eller till Service Bus-kön (som utlöser att logikappen skickar ett e-postmeddelande). Standardavläsningarna (`normal`) ska visas i BI-rapporten som du ställer in sedan.

Om allt är korrekt konfigurerat bör då se följande resultat:

1. Du börjar få e-postmeddelanden om kritiska meddelanden.

   ![De resulterande e-postmeddelandena](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Det här resultatet innebär att följande påståenden är sanna. 

   * Routningen till Service Bus-kön fungerar korrekt.
   * Logikappen som hämtar meddelandet från Service Bus-kön fungerar som den ska.
   * Logic App-anslutningsprogrammet till Outlook fungerar som det ska. 

2. I [Azure Portal](https://portal.azure.com)väljer du **resurs grupper** och väljer sedan din resurs grupp. I den här självstudien används **ContosoResources**. 

    Välj lagrings kontot, Välj **behållare** och välj sedan behållaren. I självstudien används **contosoresults**. Du bör se en mapp, och du kan öka detaljnivån mellan kataloger tills du ser en eller flera filer. Öppna en av dessa filer. De innehåller poster som dirigeras till lagringskontot. 

   ![Resultat filen i lagring](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Det här resultatet innebär att följande uttryck är sant.

   * Routningen till lagringskontot fungerar korrekt.

Nu när programmet fortfarande körs ställer du in Power BI visualiseringen för att se de meddelanden som kommer genom standardroutningen.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurera Power BI-visualiseringarna

1. Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto.

2. Gå till **Arbetsytor** och välj arbetsytan som du angav när du skapade utdata för Stream Analytics-jobbet. Den här självstudien använder **Min arbetsyta**. 

3. Välj **data uppsättningar**. Om du inte har några data uppsättningar väntar du några minuter och kontrollerar igen.

   Du bör se den listade datauppsättningen som du angav när du skapade utdata för Stream Analytics-jobbet. I självstudien används **contosodataset**. (Det kan ta 5–10 minuter innan datauppsättningen visas första gången.)

4. Under **åtgärder** väljer du den första ikonen för att skapa en rapport.

   ![Power BI arbets yta med åtgärder och rapport ikon markerad](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   * På sidan Skapa rapport lägger du till ett linje diagram genom att välja linje diagrams ikonen.

     ![Visualiseringar och fält](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet. I självstudien används **contosotable**.

   * Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   * Dra **temperatur** till **Värden**.

   Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

6. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. Om du vill ställa in det andra diagrammet följer du samma process för det första diagrammet, placerar **EventEnqueuedUtcTime** på x-axeln (**axeln**) och **fuktighet** på y-axeln (**värden**).

   ![Den slutgiltiga Power BI rapporten med de två diagrammen](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Välj **Spara** för att spara rapporten och ange ett namn för rapporten om du uppmanas att göra det.

Du bör kunna se data i båda diagrammen. Det här resultatet innebär att följande påståenden är sanna:

   * Routningen till standardslutpunkten fungerar korrekt.
   * Azure Stream Analytics-jobbet strömmar som det ska.
   * Power BI-visualiseringen är korrekt konfigurerad.

Du kan uppdatera diagrammen för att se de senaste data genom att välja knappen Uppdatera överst i Power BIs fönstret. 

## <a name="clean-up-resources"></a>Rensa resurser 

Ta bort resurs gruppen om du vill ta bort alla Azure-resurser som du har skapat via båda delarna av den här självstudien. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar det bort IoT-hubben, Service Bus-namnrymden och kön, logikappen, lagringskontot och själva resursgruppen. Du kan också ta bort Power BI resurser och ta bort e-postmeddelandena som skickas under självstudien.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Rensa resurser i Power BI-visualiseringen

Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto. Gå till din arbetsyta. Den här självstudien använder **Min arbetsyta**. Om du vill ta bort Power BI visualiseringen går du till data uppsättningar och väljer pappers korgs ikonen för att ta bort data uppsättningen. I självstudien används **contosodataset**. När du tar bort datauppsättningen tas även rapporten bort.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Rensa resurser med hjälp av Azure CLI

Om du vill ta bort resursgruppen använder du kommandot [az group delete](/cli/azure/group#az-group-delete). `$resourceGroup` var inställd på att **ContosoResources** tillbaka i början av den här självstudien.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Använd PowerShell för att rensa resurser

Om du vill ta bort resursgruppen använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` var inställd på att **ContosoResources** tillbaka i början av den här självstudien.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Rensa test-e-postmeddelanden

Du kanske också vill ta bort antalet e-postmeddelanden i Inkorgen som genererades via Logic app medan enhets programmet kördes.

## <a name="next-steps"></a>Nästa steg

I den här självstudien i två delar har du lärt dig hur du använder meddelanderoutning för att dirigera IoT Hub meddelanden till olika mål genom att utföra följande uppgifter.  

**Del I: skapa resurser, konfigurera meddelanderoutning**
> [!div class="checklist"]
> * Skapa resurserna – en IoT-hubb, ett lagrings konto, en Service Bus kö och en simulerad enhet.
> * Konfigurera slut punkterna och meddelande vägarna i IoT Hub för lagrings kontot och Service Bus kön.

**Del II: skicka meddelanden till hubben, Visa dirigerade resultat**
> [!div class="checklist"]
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
> [Konfigurera och använda mått och diagnostik med en IoT-hubb](tutorial-use-metrics-and-diags.md)