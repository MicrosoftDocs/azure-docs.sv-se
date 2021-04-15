---
title: Utlösa händelser i ML-arbetsflöden (förhandsversion)
titleSuffix: Azure Machine Learning
description: Konfigurera händelsedrivna program, processer eller CI/CD-arbetsflöden för maskininlärning i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 05/11/2020
ms.openlocfilehash: 09a541e784167251e0833f6fd3f6130a450e07d8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478853"
---
# <a name="trigger-applications-processes-or-cicd-workflows-based-on-azure-machine-learning-events-preview"></a>Utlösa program, processer eller CI/CD-arbetsflöden baserat på Azure Machine Learning (förhandsversion)

I den här artikeln får du lära dig hur du ställer in händelsedrivna program, processer eller CI/CD-arbetsflöden baserat på Azure Machine Learning-händelser, till exempel e-postmeddelanden om fel eller ML-pipelinekörningar när vissa villkor identifieras [av Azure Event Grid](../event-grid/index.yml).

Azure Machine Learning hanterar hela livscykeln för maskininlärningsprocessen, inklusive modellträning, modelldistribution och övervakning. Du kan använda Event Grid för att reagera på Azure Machine Learning-händelser, till exempel slutförande av träningskörningar, registrering och distribution av modeller samt identifiering av dataavdrift med hjälp av moderna serverlösa arkitekturer. Du kan sedan prenumerera på och använda händelser som ändrade körningsstatus, körningsslut, modellregistrering, modelldistribution och identifiering av dataavdrift på en arbetsyta.

När du ska Event Grid för händelsedrivna åtgärder:
* Skicka e-postmeddelanden vid körningsfel och slutförande av körning
* Använda en Azure-funktion när en modell har registrerats
* Strömma händelser från Azure Machine Learning till olika slutpunkter
* Utlösa en ML-pipeline när avdrift identifieras

## <a name="prerequisites"></a>Förutsättningar
För att Event Grid behöver du deltagar- eller ägaråtkomst till den Azure Machine Learning som du ska skapa händelser för.

## <a name="the-event-model--types"></a>Händelsemodellens & typer

Azure Event Grid läser händelser från källor, till exempel Azure Machine Learning och andra Azure-tjänster. Dessa händelser skickas sedan till händelsehanterare som Azure Event Hubs, Azure Functions, Logic Apps och andra. Följande diagram visar hur Event Grid ansluter källor och hanterare, men är inte en omfattande lista över integreringar som stöds.

![Azure Event Grid funktionsmodell](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

Mer information om händelsekällor och händelsehanterare finns i [Vad är Event Grid?](../event-grid/overview.md).

### <a name="event-types-for-azure-machine-learning"></a>Händelsetyper för Azure Machine Learning

Azure Machine Learning händelser i de olika punkterna i livscykeln för maskininlärning: 

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | Utlöses när en maskininlärningsexperimentkörning har slutförts |
| `Microsoft.MachineLearningServices.ModelRegistered` | Utlöses när en maskininlärningsmodell registreras på arbetsytan |
| `Microsoft.MachineLearningServices.ModelDeployed` | Utlöses när en distribution av härledningstjänsten med en eller flera modeller har slutförts |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | Utlöses när ett jobb för dataavdriftsidentifiering för två datauppsättningar har slutförts |
| `Microsoft.MachineLearningServices.RunStatusChanged` | Utlöses när en körningsstatus ändras, som för närvarande endast utlöses när körningsstatusen är "misslyckad" |

### <a name="filter--subscribe-to-events"></a>Filtrera & prenumerera på händelser

Dessa händelser publiceras via Azure Event Grid. Med Azure Portal, PowerShell eller Azure CLI kan kunder enkelt prenumerera på händelser genom att ange en eller flera [händelsetyper och filtrera villkor.](../event-grid/event-filtering.md) 

När du ställer in händelser kan du använda filter för att endast utlösa för specifika händelsedata. I exemplet nedan kan du filtrera efter körningstyper för ändrade körningsstatushändelser. Händelsen utlöses bara när villkoret uppfylls. Se schemat Azure Machine Learning [event grid om du](../event-grid/event-schema-machine-learning.md) vill veta mer om händelsedata som du kan filtrera efter. 

Prenumerationer för Azure Machine Learning-händelser skyddas av rollbaserad åtkomstkontroll i Azure (Azure RBAC). Endast [deltagare eller ägare av](how-to-assign-roles.md#default-roles) en arbetsyta kan skapa, uppdatera och ta bort händelseprenumerationer.  Filter kan tillämpas på händelseprenumerationer [antingen när](/cli/azure/eventgrid/event-subscription) händelseprenumerationen skapas eller vid ett senare tillfälle. 


1. Gå till Azure Portal och välj en ny prenumeration eller en befintlig. 

1. Välj fliken Filter och rulla ned till Avancerade filter. För Nyckel **och** **Värde anger** du de egenskapstyper som du vill filtrera efter. Här kan du se att händelsen endast utlöses när körningstypen är en pipelinekörning eller pipelinestegkörning.  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="filtrera händelser":::


+ **Filtrera efter händelsetyp:** En händelseprenumeration kan ange en eller Azure Machine Learning olika händelsetyper.

+ **Filtrera efter händelseämne:** Azure Event Grid stöder ämnesfilter baserat  på börjar med och slutar med __matchningar,__ så att händelser med ett matchande ämne levereras till prenumeranten. Olika maskininlärningshändelser har olika ämnesformat.

  | Händelsetyp | Ämnesformat | Exempelämne |
  | ---------- | ----------- | ----------- |
  | `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
  | `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
  | `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
  | `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
  | `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

+ **Avancerad filtrering:** Azure Event Grid också stöd för avancerad filtrering baserat på publicerat händelseschema. Azure Machine Learning information om händelseschemat finns i [Azure Event Grid händelseschema för Azure Machine Learning](../event-grid/event-schema-machine-learning.md).  Här är några exempel på avancerade filtreringar som du kan utföra:

  För `Microsoft.MachineLearningServices.ModelRegistered` händelse, för att filtrera modellens taggvärde:

  ```
  --advanced-filter data.ModelTags.key1 StringIn ('value1')
  ```

  Mer information om hur du tillämpar filter finns i [Filtrera händelser för Event Grid](../event-grid/how-to-filter-events.md).

## <a name="consume-machine-learning-events"></a>Använda Machine Learning händelser

Program som hanterar Machine Learning händelser bör följa några rekommenderade metoder:

> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, är det viktigt att inte anta att händelser kommer från en viss källa, utan att kontrollera ämnet i meddelandet för att säkerställa att det kommer från den machine learning-arbetsyta som du förväntar dig.
> * Kontrollera på samma sätt att eventType är en som du är beredd att bearbeta och förutsätta inte att alla händelser som du tar emot är de typer som du förväntar dig.
> * När meddelanden kan tas emot i rätt ordning och efter en viss fördröjning kan du använda etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  Använd även sekvenseringsfälten för att förstå ordningen på händelser på ett visst objekt.
> * Ignorera fält som du inte förstår. Den här övningen hjälper dig att hålla dig motståndskraftig mot nya funktioner som kan läggas till i framtiden.
> * Misslyckade eller annullerade Azure Machine Learning utlöser inte en händelse. Om en modelldistribution misslyckas utlöses till exempel inte Microsoft.MachineLearningServices.ModelDeployed. Överväg ett sådant felläge när du utformar dina program. Du kan alltid använda Azure Machine Learning SDK, CLI eller portalen för att kontrollera statusen för en åtgärd och förstå de detaljerade felorsakerna.

Azure Event Grid kan kunder skapa de-kopplade meddelandehanterare, som kan utlösas av Azure Machine Learning händelser. Några exempel på meddelandehanterare är:
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory Pipeline
* Allmänna webhooks som kan finnas på Azure-plattformen eller någon annanstans

## <a name="set-up-in-azure-portal"></a>Konfigurera i Azure Portal

1. Öppna [Azure Portal](https://portal.azure.com) och gå till din Azure Machine Learning arbetsyta.

1. I det vänstra fältet väljer du __Händelser__ och sedan **Händelseprenumerationer.** 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. Välj den händelsetyp som ska förbrukas. Följande skärmbild har till exempel valt __Modellregistrerad,__ Modell __distribuerad,__ __Körning slutförd och__ __Datauppsättningsavdrift har identifierats:__

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. Välj den slutpunkt som händelsen ska publiceras till. I följande skärmbild är __Händelsehubb__ den valda slutpunkten:

    ![Skärmbild som visar fönstret Skapa händelseprenumeration med Välj händelsehubb öppet.](./media/how-to-use-event-grid/select-event-handler.png)

När du har bekräftat ditt val klickar du på __Skapa.__ Efter konfigurationen skickas dessa händelser till slutpunkten.


### <a name="set-up-with-the-cli"></a>Konfigurera med CLI

Du kan antingen installera den [senaste versionen av Azure CLI](/cli/azure/install-azure-cli)eller använda Azure Cloud Shell som tillhandahålls som en del av din Azure-prenumeration.

Om du vill Event Grid tillägget använder du följande kommando från CLI:

```azurecli-interactive
az add extension --name eventgrid
```

Följande exempel visar hur du väljer en Azure-prenumeration och skapar en ny händelseprenumeration för Azure Machine Learning:

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

### <a name="example-send-email-alerts"></a>Exempel: Skicka e-postaviseringar

Använd [Azure Logic Apps för](../logic-apps/index.yml) att konfigurera e-postmeddelanden för alla dina händelser. Anpassa med villkor och ange mottagare för att möjliggöra samarbete och medvetenhet mellan team som arbetar tillsammans.

1. I Azure Portal du till arbetsytan Azure Machine Learning och väljer fliken Händelser i det vänstra fältet. Härifrån väljer du __Logikappar.__ 

    ![Skärmbild som visar en Machine Learning för arbetsytans händelser med Logic Apps.](./media/how-to-use-event-grid/select-logic-ap.png)

1. Logga in i logikappens användargränssnitt och välj Machine Learning tjänst som ämnestyp. 

    ![Skärmbild som visar dialogrutan When a resource event occurs (När en resurshändelse inträffar) med Machine Learning valt som resurstyp.](./media/how-to-use-event-grid/select-topic-type.png)

1. Välj vilka händelser som du vill meddelas om. Följande skärmbild visar till exempel __RunCompleted__.

    ![Skärmbild som visar dialogrutan När en resurshändelse inträffar med en händelsetyp vald.](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. Du kan använda filtreringsmetoden i avsnittet ovan eller lägga till filter för att endast utlösa logikappen på en delmängd av händelsetyperna. I följande skärmbild används __ett prefixfilter__ __för /datadriftID/runs/__ .

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. Lägg sedan till ett steg för att använda den här händelsen och sök efter e-post. Det finns flera olika e-postkonton som du kan använda för att ta emot händelser. Du kan också konfigurera villkor för när du ska skicka en e-postavisering.

    ![Skärmbild som visar dialogrutan Välj en åtgärd med e-post angivet på sökraden.](./media/how-to-use-event-grid/select-email-action.png)

1. Välj __Skicka ett e-postmeddelande__ och fyll i parametrarna. I ämnet kan du inkludera Händelsetyp __och Ämne för__ att __filtrera__ händelser. Du kan också inkludera en länk till arbetsytans sida för körningar i meddelandetexten. 

    ![Skärmbild som visar dialogrutan Skicka ett e-postmeddelande med Ämne och Händelsetyp tillagda på ämnesraden från listan till höger.](./media/how-to-use-event-grid/configure-email-body.png)

1. Om du vill spara den här **åtgärden väljer du Spara** som i det vänstra hörnet på sidan. Bekräfta att åtgärden har skapats från det högra fältet som visas.

    ![Skärmbild som visar knapparna Spara som och Skapa i Logic Apps Designer.](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="example-data-drift-triggers-retraining"></a>Exempel: Omträning av dataavdriftsutlösare

Modeller blir inaktuella med tiden och är inte användbara i den kontext som de körs i. Ett sätt att se om det är dags att träna om modellen är att identifiera dataavdrift. 

Det här exemplet visar hur du använder event grid med en Azure-logikapp för att utlösa omträning. Exemplet utlöser en Azure Data Factory pipeline när dataavledning inträffar mellan en modells tränings- och betjänande datauppsättningar.

Innan du börjar utför du följande åtgärder:

* Konfigurera en datauppsättningsövervakare för [att identifiera dataavdrift](how-to-monitor-datasets.md) i en arbetsyta
* Skapa en publicerad [Azure Data Factory pipeline](../data-factory/index.yml).

I det här exemplet används en Data Factory pipeline för att kopiera filer till ett bloblager och köra en publicerad Machine Learning pipeline. Mer information om det här scenariot finns i konfigurera ett [Machine Learning steg i Azure Data Factory](../data-factory/transform-data-machine-learning-service.md)

![Skärmbild som visar träningspipelinen i fabriksresurser med Kopiera data1 som matar in M L Execute Pipeline1.](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. Börja med att skapa logikappen. Gå till [Azure Portal,](https://portal.azure.com)sök efter Logic Apps och välj Skapa.

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. Fyll i den begärda informationen. För att förenkla upplevelsen använder du samma prenumeration och resursgrupp som din Azure Data Factory Pipeline och Azure Machine Learning arbetsyta.

    ![Skärmbild som visar fönstret Skapa logikapp.](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. När du har skapat logikappen väljer du __När en Event Grid-resurshändelse inträffar.__ 

    ![Skärmbild som visar Logic Apps Designer med Start med ett vanligt utlösaralternativ, inklusive När en Event Grid-resurshändelse inträffar.](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. Logga in och fyll i informationen för händelsen. Ange namnet __på arbetsytans__ resursnamn. Ange __Händelsetyp till__ __DatasetDriftDetected__.

    ![Skärmbild som visar när en resurshändelse inträffar med ett objekt av händelsetyp valt.](./media/how-to-use-event-grid/login-and-add-event.png)

1. Lägg till ett nytt steg och sök efter __Azure Data Factory__. Välj __Skapa en pipelinekörning.__ 

    ![Skärmbild som visar fönstret Välj en åtgärd med Skapa en pipelinekörning markerad.](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. Logga in och ange den Azure Data Factory pipeline som ska köras.

    ![Skärmbild som visar fönstret Skapa en pipelinekörning med olika värden.](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. Spara och skapa logikappen med **knappen** Spara längst upp till vänster på sidan. Om du vill visa din app går du till arbetsytan i [Azure Portal](https://portal.azure.com) och klickar på **Händelser.**

    ![Skärmbild som visar händelser med logikappen markerad.](./media/how-to-use-event-grid/show-logic-app-webhook.png)

Nu utlöses datafabrikspipelinen när drift inträffar. Visa information om din dataavdriftskörning och maskininlärningspipeline på den [nya arbetsyteportalen.](https://ml.azure.com) 

![Skärmbild som visar pipelineslutpunkter.](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="example-deploy-a-model-based-on-tags"></a>Exempel: Distribuera en modell baserat på taggar

Ett Azure Machine Learning-modellobjekt innehåller parametrar som du kan pivotera distributioner på, till exempel modellnamn, version, tagg och egenskap. Modellregistreringshändelsen kan utlösa en slutpunkt och du kan använda en Azure-funktion för att distribuera en modell baserat på värdet för dessa parametrar.

Ett exempel finns i [https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) lagringsplatsen och följer stegen i **readme-filen.**

## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Azure Machine Learning händelser ett försök:

- [Om Event Grid](../event-grid/overview.md)

- [Händelseschema för Azure Machine Learning](../event-grid/event-schema-machine-learning.md)
