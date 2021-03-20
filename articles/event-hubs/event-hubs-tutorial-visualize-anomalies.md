---
title: Azure Event Hubs – visualisera data avvikelser i real tids händelser
description: 'Självstudie: visualisera data avvikelser i real tids händelser som skickas till Microsoft Azure Event Hubs'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: bd08d611761e125fdd03173ce6027c60e82baed6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98623466"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Självstudie: Visualisera dataavvikelser i realtidshändelser som skickats till Azure Event Hubs

Du kan använda Azure Stream Analytics med Azure Event Hubs för att kontrollera inkommande data och extrahera avvikelser, som du sedan kan visualisera i Power BI. Anta att du har tusentals enheter som kontinuerligt skickar data i realtid till en händelsehubb. I så fall kan det röra sig om miljontals händelser per sekund. Hur kontrollerar du om det finns avvikelser, eller fel, i sådana mängder data? Vad händer om enheterna skickar kredit korts transaktioner och du måste samla in var du än har flera transaktioner i flera länder/regioner inom ett intervall om 5 sekunder? Detta kan inträffa om någon stjäl kreditkort och sedan använder dem för att köpa saker samtidigt från olika delar av världen. 

I den här självstudien får du simulera det här exemplet. Du kör ett program som skapar och skickar kreditkortstransaktioner till en händelsehubb. Sedan läser du data strömmen i real tid med Azure Stream Analytics, som avgränsar giltiga transaktioner från ogiltiga transaktioner och använder sedan Power BI för att visuellt identifiera de transaktioner som är märkta som ogiltiga.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Skapa en händelsehubb
> * Köra appen som skickar kreditkortstransaktioner
> * Konfigurera ett Stream Analytics-jobb för att bearbeta de här transaktionerna
> * Konfigurera en Power BI-visualisering för att visa resultatet

Du behöver en Azure-prenumeration för att kunna utföra stegen i den här självstudiekursen. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto][] innan du börjar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Installera [Visual Studio](https://www.visualstudio.com/). 
- Du behöver ett Power BI-konto för att kunna analysera utdata från ett Stream Analytics-jobb. Du kan [prova Power BI utan kostnad](https://app.powerbi.com/signupredirect?pbi_source=web).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-resources"></a>Konfigurera resurser

För den här självstudien behöver du ett Event Hubs-namnområde och en händelsehubb. Du kan skapa dessa resurser med Azure CLI eller Azure PowerShell. Använd samma resursgrupp och plats för alla resurser. I slutet kan du ta bort allt i ett steg genom att ta bort resursgruppen.

Följande avsnitt beskriver hur du utför de steg som krävs. Utför följande steg genom att följa instruktionerna för CLI *eller* PowerShell:

1. Skapa en [resurs grupp](../azure-resource-manager/management/overview.md). 

2. Skapa ett Event Hubs-namnområde. 

3. Skapa en händelsehubb.

> [!NOTE]
> Det finns angivna variabler i varje skript som du behöver senare i den här självstudien. Det gäller namn på resursgrupp ($resourceGroup), namnområde för händelsehubb (**$eventHubNamespace**) och namn på händelsehubb (**$eventHubName**). Dessa är markerade med ett dollarteckenprefix ($) senare i den här artikeln så att du vet var de har angetts i skriptet.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Konfigurera resurser med Azure CLI

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad.

`$RANDOM` har sammanfogats med de variabler som måste vara globalt unika. När skriptet körs och variablerna har angetts genereras en slumpmässig numerisk sträng som sammanfogas i slutet av den fasta strängen så att den blir unik.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Konfigurera resurser med Azure PowerShell

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad.

`$(Get-Random)` har sammanfogats med de variabler som måste vara globalt unika. När skriptet körs och variablerna har angetts genereras en slumpmässig numerisk sträng som sammanfogas i slutet av den fasta strängen så att den blir unik.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Kör appen för att generera testhändelsedata

Event Hubs [exemplen på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) innehåller en app för avvikelse detektor som producerar test data åt dig. Appen simulerar användning av kreditkort genom att skriva kreditkortstransaktioner till händelsehubben, och ibland skrivs flera transaktioner för samma kreditkort på flera platser så att de blir märkta som avvikelser. Följ dessa steg om du vill köra den här appen: 

1. Ladda ned [Azure Event Hubs samples](https://github.com/Azure/azure-event-hubs/archive/master.zip) (Azure Event Hubs-exempel) från GitHub och packa upp filen lokalt.
2. Navigera till mappen **\azure-Event-Hubs-master\samples\DotNet \\** -mappen. 
3. Växla till **Azure. Messaging. EventHubs\AnomalyDetector \\** -mappen och dubbelklicka på **AnomalyDetector. SLN** för att öppna lösningen i Visual Studio. 

    Om du vill använda den gamla versionen av exemplet som använder det gamla Microsoft. Azure. EventHubs-paketet öppnar du lösningen från mappen **Microsoft. Azure. EventHubs\AnomalyDetector** . 
3. Öppna Program.cs och ersätt **Event Hubs-anslutningssträngen** med den anslutningssträng som du sparade när du körde skriptet. 
4. Ersätt **namnet på händelsehubben** med namnet på din händelsehubb. Klicka på F5 för att köra programmet. Därmed skickas händelser till din händelsehubb tills 1 000 händelser har skickats. I vissa fall måste appen köras för att du ska kunna hämta data. Dessa fall anges i följande instruktioner, där det behövs.

## <a name="set-up-azure-stream-analytics"></a>Konfigurera Azure Stream Analytics

Nu kan du strömma data till din händelsehubb. För att kunna använda dessa data i en Power BI-visualisering måste du först konfigurera ett Stream Analytics-jobb för att hämta data som sedan matas in i Power BI-visualiseringen.

### <a name="create-the-stream-analytics-job"></a>Skapa Stream Analytics-jobbet

1. Klicka på **Skapa en resurs** i Azure Portal. Skriv **stream analytics** i sökrutan och tryck på **Retur**. Välj **Stream Analytics-jobb**. Klicka på **Skapa** i Stream Analytics-jobbfönstret. 

2. Ange följande information för jobbet:

   **Jobbnamn**: Använd **contosoEHjob**. Det här fältet är namnet på jobbet och det måste vara globalt unikt.

   **Prenumeration**: Välj din prenumeration.

   **Resursgrupp**: Använd samma resursgrupp som användes av din händelsehubb (**ContosoResourcesEH**).

   **Plats**: Använd samma plats som användes i installationsskriptet (**USA, västra**).

   ![Skärmbild som visar hur du skapar ett nytt Azure Stream Analytics-jobb.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    För resten av fälten accepterar du standardvärdena. Klicka på **Skapa**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

Om du inte är i fönstret **Stream Analytics-jobb** i Portal kan du gå tillbaka till ditt Stream Analytics-jobb genom att klicka på **Resursgrupper** i Portal och sedan välja din resursgrupp (**ContosoResourcesEH**). Den här åtgärden visar alla resurser i gruppen. Sedan kan du välja ditt stream analytics-jobb. 

Indata för Steam Analytics-jobbet är kreditkortstransaktioner från händelsehubben.

> [!NOTE]
> Värdena för variabler som börjar med dollartecken ($) anges i startskriptet i föregående avsnitt. Du måste använda samma värden här när du anger dessa fält, det vill säga Event Hubs-namnområdet och namnet på händelsehubben.

1. Under **Jobbtopologi** klickar du på **Indata**.

2. Klicka på **Lägg till strömindata** i fönstret **Indata** och välj Event Hubs. På skärmen som visas fyller du i följande fält:

   **Utdataalias**: Använd **contosooutputs**. Det här fältet är namnet på den indataström som används vid definiering av frågan efter data.

   **Prenumeration**: Välj din prenumeration.

   **Event Hubs-namnområde**: Välj ditt Event Hub-namnområde ($**eventHubNamespace**). 

   **Namn på händelsehubb**: Klicka på **Använd befintlig** och välj din händelsehubb ($**eventHubName**).

   **Namn på Event Hubs-princip**: Välj **RootManageSharedAccessKey**.

   **Event Hubs-konsumentgrupp**: Använd standardkonsumentgruppen genom att lämna det här fältet tomt.

   För resten av fälten accepterar du standardvärdena.

   ![Skärmbild som visar hur du lägger till en indataström till Stream Analytics-jobbet.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Klicka på **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**. Det här fältet är namnet på den utdataström som används vid definiering av frågan efter data.

2. I fönstret **Utdata** klickar du på **Lägg till** och väljer **Power BI**. På skärmen som visas fyller du i följande fält:

   **Utdataalias**: Använd **contosooutputs**. Det här fältet är utdatas unika alias. 

   **Namn på datauppsättning**: Använd **contosoehdataset**. Det här fältet är namnet på datauppsättningen som ska användas i Power BI. 

   **Tabellnamn**: Använd **contosoehtable**. Det här fältet är namnet på tabellen som ska användas i Power BI. 

   För resten av fälten accepterar du standardvärdena.

   ![Skärmbild som visar hur du konfigurerar utdata för ett Stream Analytics-jobb.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Klicka på **Autentisera** och logga in på Power BI-kontot.

4. För resten av fälten accepterar du standardvärdena.

5. Klicka på **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

Den här frågan används för att hämta data som slutligen skickas till Power BI-visualiseringen. I frågan används **contosoinputs** och **contosooutputs**, som du tidigare definierade när du konfigurerade jobbet. Frågan hämtar de kreditkortstransaktioner som bedöms vara bedrägliga, det vill säga transaktioner där det förekommer flera transaktioner för samma kreditkortsnummer på olika platser inom fem sekunder.

1. Under **Jobbtopologi** klickar du på **Fråga**.

2. Ersätt frågan med följande fråga: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klicka på **Spara**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Testa frågan för Stream Analytics-jobbet 

1. Kör appen Anomaly Detector (Avvikelseidentifiering) för att skicka data till händelsehubben medan du konfigurerar och kör testet. 

2. I fönstret Fråga klickar du på punkterna bredvid **contosoinputs**-indata och väljer sedan **Exempeldata från indata**.

3. Ange att du vill ha tre minuters data, och klicka sedan **OK**. Vänta tills du får ett meddelande om att data har samplats.

4. Klicka på **Test** och kontrollera att du får resultat. Resultat visas i avsnittet **Resultat** i det nedre fönstret till höger direkt under frågan.

5. Stäng rutan Fråga.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobbet klickar du på **Starta**, på **Nu** och sedan på **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurera Power BI-visualiseringarna

1. Kör appen Anomaly Detector (Avvikelseidentifiering) för att skicka data till händelsehubben medan du konfigurerar Power BI-visualiseringen. Du kan behöva köra appen flera gånger eftersom den bara genererar 1 000 transaktioner varje gång den körs.

2. Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto.

3. Gå till **Min arbetsyta**.

4. Klicka på **Datauppsättningar**.

   Du bör se datauppsättningen som du angav när du skapade utdata för Stream Analytics-jobbet (**contosoehdataset**). Det kan ta 5–10 minuter innan datauppsättningen visas första gången.

5. Klicka på **Instrumentpaneler**, klicka på **Skapa** och välj sedan **Instrumentpanel**.

   ![Skärmbild av instrumentpanelerna och Skapa-knapparna.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Ange namnet på instrumentpanelen och klicka sedan på **Skapa**. Använd **Credit Card Anomalies** (Kreditkortsavvikelser).

   ![Skärmbild av hur du anger instrumentpanelens namn.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. På instrument panels sidan klickar du på **Lägg till panel**, väljer **anpassade strömmande data** i **real tids data** avsnittet och klickar sedan på **Nästa**.

   ![Skärmbild av hur du anger källa för panelen.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Välj din datauppsättning (**contosoehdataset**) och klicka på **Nästa**.

   ![Skärmbild av hur du anger datauppsättning.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Välj **Kort** som visualiseringstyp. Under **fält** klickar du på **Lägg till värde** och väljer sedan `fraudulentuses` .

   ![Skärmbild av hur du anger visualiseringstyp och fält.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Klicka på **Nästa**.

10. Ange **Fraudulent uses** (Bedräglig användning) som rubrik och **Sum in last few minutes** (Summa de senaste minuterna) som underrubrik. Klicka på **Applicera**. Panelen sparas på instrumentpanelen.

    ![Skärmbild av rubrik och underrubrik för panelen på instrumentpanelen.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > När du kör exempelprogrammet och strömmar data till händelsehubben ändras numret på den här panelen snabbt (varje sekund). Det beror på att Stream Analytics-frågan faktiskt uppdaterar värdet **varje sekund**. Uppdatera frågan till ett rullande fönster på 3 minuter för att se summan av de senaste minuterna. 
11. Lägg till ytterligare en visualisering. Upprepa de första stegen igen:

    * Klicka på **Lägg till panel**.
    * Välj **Anpassade strömmande data**. 
    * Klicka på **Nästa**.
    * Välj din datauppsättning och klicka sedan på **Nästa**. 

12. Välj **Linjediagram** under **Visualiseringstyp**.

13. Under **axel** klickar du på **Lägg till värde** och väljer `windowend` . 

14. Under **värden** klickar du på **Lägg till värde** och väljer `fraudulentuses` .

15. Under **Tidsfönster att visa** väljer du de senaste fem minuterna. Klicka på **Nästa**.

16. Ange **Show fraudulent uses over time** (Visa bedräglig användning över tid) som rubrik och lämna underrubriken tom. Klicka sedan på **Verkställ**. Du kommer tillbaka till instrumentpanelen.

17. Kör appen Anomaly Detector (Avvikelseidentifiering) igen för att skicka data till händelsehubben. Du kan se att panelen **Fraudulent uses** (Bedräglig användning) ändras när data analyseras och att data visas i linjediagrammet. 

    ![Skärmbild på Power BI-resultat](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser du har skapat tar du bort Power BI-visualiseringsdata och sedan resursgruppen. Alla resurser som ingår i gruppen tas bort om resursgruppen tas bort. I det här fallet tas händelsehubben, Event Hub-namnområdet, Stream Analytics-jobbet och själva resursgruppen bort. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Rensa resurser i Power BI-visualiseringen

Logga in på ditt Power BI-konto. Gå till **Min arbetsyta**. Klicka på raden med namnet på din instrumentpanel och klicka på ikonen för papperskorgen. Gå sedan till **DataSets** och klicka på ikonen för papperskorgen för att ta bort datauppsättningen (**contosoehdataset**).

### <a name="clean-up-resources-using-azure-cli"></a>Rensa resurser med hjälp av Azure CLI

Om du vill ta bort resursgruppen använder du kommandot [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Rensa resurser med hjälp av PowerShell

Om du vill ta bort resursgruppen använder du kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Skapa en händelsehubb
> * Köra appen som simulerar händelser och skickar dem till händelsehubben
> * Konfigurera ett Stream Analytics-jobb för att bearbeta händelser som skickats till hubben
> * Konfigurera en Power BI-visualisering för att visa resultatet

Gå vidare till nästa artikel om du vill lära dig mer om Azure Event Hubs.

> [!div class="nextstepaction"]
> [Komma igång med att skicka meddelanden till Azure Event Hubs med .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[skapa ett kostnads fritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
