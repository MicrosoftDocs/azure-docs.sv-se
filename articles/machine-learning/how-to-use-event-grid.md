---
title: Utlösa händelser i ML-arbetsflöden (för hands version)
titleSuffix: Azure Machine Learning
description: Konfigurera händelse drivna program, processer eller CI/CD Machine Learning-arbetsflöden i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 9dc7c0a476f57048c7caefb6001918d314c991ee
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209704"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Utlös program, processer eller CI/CD-arbetsflöden baserat på Azure Machine Learning händelser (förhands granskning)

I den här artikeln får du lära dig hur du konfigurerar händelse drivna program, processer eller CI/CD-arbetsflöden som baseras på Azure Machine Learning händelser, till exempel e-postmeddelanden för fel meddelanden eller ML pipelines, när vissa villkor upptäcks av [Azure Event Grid](../event-grid/index.yml).

Azure Machine Learning hanterar hela livs cykeln för maskin inlärnings processen, inklusive modell utbildning, modell distribution och övervakning. Du kan använda Event Grid för att reagera på Azure Machine Learning händelser, t. ex. slut för ande av inlärnings körningar, registrering och distribution av modeller och identifiering av data genom att använda moderna serverbaserade arkitekturer. Du kan sedan Prenumerera på och använda händelser som t. ex. körnings status ändrad, körnings slut för ande, modell registrering, modell distribution och data avkänning i en arbets yta.

När du ska använda Event Grid för händelse drivna åtgärder:
* Skicka e-postmeddelanden vid körnings problem och kör slut för ande
* Använd en Azure-funktion när en modell har registrerats
* Strömma händelser från Azure Machine Learning till olika slut punkter
* Utlös en ML-pipeline när en avvikelse identifieras

## <a name="prerequisites"></a>Förutsättningar
Om du vill använda Event Grid behöver du deltagar-eller ägar åtkomst till arbets ytan Azure Machine Learning du skapar händelser för.

## <a name="the-event-model--types"></a>Händelse modell & typer

Azure Event Grid läser händelser från källor, till exempel Azure Machine Learning och andra Azure-tjänster. Dessa händelser skickas sedan till händelse hanterare som Azure Event Hubs, Azure Functions, Logic Apps och andra. Följande diagram visar hur Event Grid ansluter källor och hanterare, men är inte en omfattande lista över stödda integreringar.

![Azure Event Grid funktionell modell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Mer information om händelse källor och händelse hanterare finns i [Vad är event Grid?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Händelse typer för Azure Machine Learning

Azure Machine Learning innehåller händelser i olika punkter av Machine Learning-livscykel: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Utlöses när en körning av Machine Learning-experiment har slutförts |
| `Microsoft.MachineLearningServices.ModelRegistered` | Utlöses när en maskin inlärnings modell registreras i arbets ytan |
| `Microsoft.MachineLearningServices.ModelDeployed` | Utlöses när en distribution av en tjänst med en eller flera modeller har slutförts |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Utlöses när ett jobb för data avvikelse identifiering för två data uppsättningar har slutförts |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Aktive ras när en körnings status ändrades, för närvarande endast när körnings statusen är "misslyckades" |

### <a name="filter--subscribe-to-events"></a>Filtrera & prenumerera på händelser

Dessa händelser publiceras via Azure Event Grid. Med hjälp av Azure Portal, PowerShell eller Azure CLI kan kunder enkelt prenumerera på händelser genom att [Ange en eller flera händelse typer och filtrerings villkor](../event-grid/event-filtering.md). 

När du konfigurerar dina händelser kan du använda filter för att endast utlösa för vissa händelse data. I exemplet nedan kan du filtrera efter körnings status ändrade händelser med körnings typer. Händelsen utlöses endast när villkoret är uppfyllt. Se det [Azure Machine Learning Event Grid-schemat](../event-grid/event-schema-machine-learning.md) för att lära dig mer om händelse data som du kan filtrera efter. 

Prenumerationer för Azure Machine Learning händelser skyddas av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Endast [deltagare eller ägare](how-to-assign-roles.md#default-roles) av en arbets yta kan skapa, uppdatera och ta bort händelse prenumerationer.  Filter kan tillämpas på händelse prenumerationer antingen under [skapandet](/cli/azure/eventgrid/event-subscription) av händelse prenumerationen eller vid ett senare tillfälle. 


1. Gå till Azure Portal, Välj en ny prenumeration eller en befintlig. 

1. Välj fliken filter och rulla ned till avancerade filter. För **nyckeln** och **värdet** anger du de egenskaps typer som du vill filtrera efter. Här kan du se att händelsen endast utlöses när körnings typen är en pipeline-körning eller pipeline-steg körs.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="Filtrera händelser":::


+ **Filtrera efter händelse typ:** En händelse prenumeration kan ange en eller flera Azure Machine Learning händelse typer.

+ **Filtrera efter händelse ämne:** Azure Event Grid stöder ämnes filter som baseras på __börjar med__ och __slutar med__ matchningar, så att händelser med ett matchande ämne levereras till prenumeranten. Olika Machine Learning-händelser har olika ämnes format.

  | Händelsetyp | Ämnes format | Exempel ämne |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Avancerad filtrering**: Azure Event Grid stöder även avancerad filtrering baserat på ett publicerat händelse schema. Azure Machine Learning händelse schema information finns i [Azure Event Grid händelse schema för Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Några exempel på avancerade filter som du kan utföra är:

  För `Microsoft.MachineLearningServices.ModelRegistered` event, för att filtrera modellens taggvärde:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Mer information om hur du använder filter finns i [Filtrera händelser för Event Grid](../event-grid/how-to-filter-events.md).

## <a name="consume-machine-learning-events"></a>Använda Machine Learning händelser

Program som hanterar Machine Learning händelser bör följa några rekommenderade metoder:

> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att se till att det kommer från Machine Learning-arbetsytan som du förväntar dig.
> * På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
> * När meddelanden kan komma in i rätt ordning och efter en viss fördröjning använder du etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  Använd också sekvenserare-fälten för att förstå ordningen på händelser för ett visst objekt.
> * Ignorera fält som du inte förstår. Den här övningen hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
> * Misslyckade eller avbrutna Azure Machine Learning åtgärder utlöser inte en händelse. Om en modell distribution till exempel Miss lyckas, utlöses inte Microsoft. MachineLearningServices. ModelDeployed. Tänk på detta felläge när du utformar dina program. Du kan alltid använda Azure Machine Learning SDK, CLI eller portal för att kontrol lera status för en åtgärd och förstå de detaljerade orsakerna till problemet.

Azure Event Grid gör det möjligt för kunderna att bygga ut de sammankopplade meddelande hanteringarna, som kan utlösas av Azure Machine Learning händelser. Några viktiga exempel på Meddelande hanterare är:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory pipeline
* Allmänna webhookar som kan finnas på Azure-plattformen eller någon annan stans

## <a name="set-up-in-azure-portal"></a>Konfigurera i Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com) och gå till arbets ytan Azure Machine Learning.

1. Välj __händelser__ i det vänstra fältet och välj sedan **händelse prenumerationer**. 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Välj den händelse typ som ska konsumeras. Följande skärm bild har till exempel valt __modell registrerad__, __modell distribution__, __körning slutförd__ och __data uppsättnings avvikelse identifierad__:

    ![Lägg till händelse-typ](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Välj slut punkten att publicera händelsen till. I följande skärm bild är __Event Hub__ den valda slut punkten:

    ![Skärm bild som visar fönstret Skapa händelse prenumeration med Välj Händelsehubben öppen.](./media/how-to-use-event-grid/select-event-handler.png)

När du har bekräftat ditt val klickar du på __skapa__. Efter konfigurationen skickas de här händelserna till din slut punkt.


### <a name="set-up-with-the-cli"></a>Konfigurera med CLI

Du kan antingen installera den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli)eller använda Azure Cloud Shell som tillhandahålls som en del av din Azure-prenumeration.

Om du vill installera Event Grid-tillägget använder du följande kommando från CLI:

```azurecli-interactive
az add extension --name eventgrid
```

Följande exempel visar hur du väljer en Azure-prenumeration och skapar en ny händelse prenumeration för Azure Machine Learning:

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace. This example uses EventHub as a destination. 
az eventgrid event-subscription create --name {eventGridFilterName} \
  --source-resource-id /subscriptions/{subId}/resourceGroups/{RG}/providers/Microsoft.MachineLearningServices/workspaces/{wsName} \
  --endpoint-type eventhub \
  --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.EventHub/namespaces/n1/eventhubs/EH1 \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="examples"></a>Exempel

### <a name="example-send-email-alerts"></a>Exempel: skicka e-postaviseringar

Använd [Azure Logic Apps](../logic-apps/index.yml) för att konfigurera e-post för alla dina händelser. Anpassa med villkor och ange mottagare för att möjliggöra samarbete och medvetenhet mellan team som arbetar tillsammans.

1. Gå till arbets ytan Azure Machine Learning i Azure Portal och välj fliken händelser i det vänstra fältet. Härifrån väljer du __Logic Apps__. 

    ![Skärm bild som visar sidan för Machine Learning arbets ytans händelser med Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Logga in på Logic app UI och välj Machine Learning tjänst som typ av ämne. 

    ![Skärm bild som visar dialog rutan när en resurs händelse inträffar med Machine Learning valt som en resurs typ.](./media/how-to-use-event-grid/select-topic-type.png)

1. Välj vilken eller vilka händelser som ska meddelas för. Till exempel visas följande skärm bild __RunCompleted__.

    ![Skärm bild som visar dialog rutan när en resurs händelse inträffar med en händelse typ vald.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Du kan använda filtrerings metoden i avsnittet ovan eller lägga till filter för att endast utlösa Logic-appen på en delmängd av händelse typerna. I följande skärm bild används ett __prefix filter__ för __/datadriftID/Runs/__ .

    ![Filtrera händelser](./media/how-to-use-event-grid/filtering-events.png)

1. Lägg sedan till ett steg för att använda den här händelsen och Sök efter e-post. Det finns flera olika e-postkonton som du kan använda för att ta emot händelser. Du kan också konfigurera villkor för när en e-postavisering ska skickas.

    ![Skärm bild som visar dialog rutan Välj en åtgärd med e-post som anges i Sök raden.](./media/how-to-use-event-grid/select-email-action.png)

1. Välj __Skicka ett e-postmeddelande__ och fyll i parametrarna. I ämnet kan du inkludera __händelse typen__ och __ämnet__ för att filtrera händelser. Du kan också ta med en länk till sidan arbets yta för körningar i meddelande texten. 

    ![Skärm bild som visar dialog rutan Skicka ett e-postmeddelande med ämne och händelse typ som har lagts till i ämnes raden i listan till höger.](./media/how-to-use-event-grid/configure-email-body.png)

1. Om du vill spara den här åtgärden väljer du **Spara som** i det vänstra hörnet på sidan. I det högra fältet som visas bekräftar du att åtgärden har skapats.

    ![Skärm bild som visar knapparna Spara som och skapa i Logic Apps designer.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Exempel: omskolning av data utlösare

Modeller föråldras över tid och är inte användbara i den kontext som den körs i. Ett sätt att avgöra om det är dags att omträna modellen är att identifiera data drift. 

Det här exemplet visar hur du använder Event Grid med en Azure Logic-app för att utlösa omträning. Exemplet utlöser en Azure Data Factory pipeline när det uppstår en data avvikelse mellan en modells utbildning och betjänar data uppsättningar.

Innan du börjar utför du följande åtgärder:

* Konfigurera en data uppsättnings Övervakare för att [identifiera data drivgarn](how-to-monitor-datasets.md) i en arbets yta
* Skapa en pipeline för publicerade [Azure Data Factory](../data-factory/index.yml).

I det här exemplet används en enkel Data Factory pipeline för att kopiera filer till ett BLOB-lager och köra en publicerad Machine Learning-pipeline. Mer information om det här scenariot finns i så här konfigurerar du ett [Machine Learning steg i Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![Skärm bild som visar utbildnings pipelinen i fabriks resurser med kopiera fil1-matnings-och Pipeline1-körning.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Börja med att skapa Logic app. Gå till [Azure Portal](https://portal.azure.com), sök efter Logic Apps och välj Skapa.

    ![Sök efter logik – app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Fyll i den begärda informationen. För att förenkla upplevelsen kan du använda samma prenumeration och resurs grupp som Azure Data Factory pipeline och Azure Machine Learning arbets yta.

    ![Skärm bild som visar fönstret för att skapa logiska appar.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. När du har skapat Logic-appen väljer du __när en Event Grid resurs händelse inträffar__. 

    ![Skärm bild som visar Logic Apps designern med start med vanliga utlösnings alternativ, inklusive när en Event Grid resurs händelse inträffar.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Logga in och fyll i informationen om händelsen. Ange __resurs namnet__ till arbets ytans namn. Ange __händelse typen__ till __DatasetDriftDetected__.

    ![Skärm bild som visar när en resurs händelse inträffar med ett händelse typs objekt valt.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Lägg till ett nytt steg och Sök efter __Azure Data Factory__. Välj __skapa en pipeline-körning__. 

    ![Skärm bild som visar fönstret Välj en åtgärd med skapa en pipeline-körning vald.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Logga in och ange den publicerade Azure Data Factory pipelinen som ska köras.

    ![Skärm bild som visar fönstret Skapa en pipeline-körning med olika värden.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Spara och skapa Logic-appen med knappen **Spara** längst upp till vänster på sidan. Om du vill visa din app går du till din arbets yta i [Azure Portal](https://portal.azure.com) och klickar på **händelser**.

    ![Skärm bild som visar händelser med den markerade Logic-appen.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu utlöses Data Factory-pipelinen när en avvikelse inträffar. Visa information om data körnings-och maskin inlärnings pipelinen på den [nya arbets ytans Portal](https://ml.azure.com). 

![Skärm bild som visar pipelines slut punkter.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Exempel: Distribuera en modell baserat på Taggar

Ett Azure Machine Learning Model-objekt innehåller parametrar som du kan pivotera distributioner på, till exempel modell namn, version, tagg och egenskap. Modell registrerings händelsen kan utlösa en slut punkt och du kan använda en Azure-funktion för att distribuera en modell baserat på värdet för dessa parametrar.

Se till exempel [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) lagrings platsen och följ stegen i **README** -filen.

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure Machine Learning händelser ett försök:

- [Om Event Grid](../event-grid/overview.md)

- [Händelse schema för Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
