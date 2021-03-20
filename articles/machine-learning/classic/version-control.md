---
title: 'ML Studio (klassisk): program livs cykel hantering – Azure'
description: Använd metod tips för program livs cykel hantering i Azure Machine Learning Studio (klassisk)
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.date: 10/27/2016
ms.openlocfilehash: 82d6f1fd250621ec34015970e1339ccfbd134675
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518410"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio-classic"></a>Hantering av program livs cykel i Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Azure Machine Learning Studio (klassisk) är ett verktyg för att utveckla maskin inlärnings experiment som används i Azure Cloud Platform. Det är precis som Visual Studio IDE och skalbar moln tjänst som är kopplad till en enda plattform. Du kan införliva ALM-metoder (standard Application Lifecycle Management) från versioner av olika till gångar till automatiserad körning och distribution i Azure Machine Learning Studio (klassisk). I den här artikeln beskrivs några av alternativen och metoderna.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Versions experiment
Det finns två rekommenderade sätt att version av experiment. Du kan antingen förlita dig på den inbyggda körnings historiken eller exportera experimentet i ett JSON-format så att det externt hanteras. Varje metod levereras med dess-och nack delar.

### <a name="experiment-snapshots-using-run-history"></a>Experiment-ögonblicksbilder med körnings historik
I körnings modellen för det Azure Machine Learning Studio (klassiska) inlärnings experimentet skickas en oföränderlig ögonblicks bild av experimentet till jobbschemat när du klickar på **Kör** i experiment redigeraren. Om du vill visa listan över ögonblicks bilder klickar du på **Kör historik** i kommando fältet i vyn experiment redigeraren.

![Knappen Kör historik](./media/version-control/runhistory.png)

Du kan sedan öppna ögonblicks bilden i låst läge genom att klicka på namnet på experimentet vid den tidpunkt då experimentet skickades för att köra och ögonblicks bilden togs. Observera att endast det första objektet i listan, som representerar det aktuella experimentet, är i ett redigerbart tillstånd. Observera också att varje ögonblicks bild också kan ha olika status tillstånd, inklusive avslutad (delvis körning), misslyckades, misslyckades (partiell körning) eller utkast.

![Kör historik lista](./media/version-control/runhistorylist.png)

När den har öppnats kan du spara ögonblicks bild experimentet som ett nytt experiment och sedan ändra det. Om din experiment-ögonblicksbild innehåller till gångar, till exempel utbildade modeller, transformeringar eller data uppsättningar som har uppdaterade versioner, behåller ögonblicks bilden referenser till den ursprungliga versionen när ögonblicks bilden togs. Om du sparar den låsta ögonblicks bilden som ett nytt experiment identifierar Azure Machine Learning Studio (klassisk) förekomsten av en nyare version av dessa till gångar och uppdaterar dem automatiskt i det nya experimentet.

Om du tar bort experimentet raderas alla ögonblicks bilder av experimentet.

### <a name="exportimport-experiment-in-json-format"></a>Exportera/importera experiment i JSON-format
Ögonblicks bilder av körnings historik behåller en oföränderlig version av experimentet i Azure Machine Learning Studio (klassisk) varje gång den skickas för körning. Du kan också spara en lokal kopia av experimentet och kontrol lera det i ditt favorit system för käll kontroll, till exempel Team Foundation Server, och senare när du återskapar ett experiment från den lokala filen. Du kan använda [Azure Machine Learning PowerShell](https://aka.ms/amlps) -cmdletarna [*export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) och [*import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) för att åstadkomma detta.

JSON-filen är en text representation av experiment grafen, som kan innehålla en referens till till gångar i arbets ytan, till exempel en data uppsättning eller en utbildad modell. Den innehåller inte en serialiserad version av till gången. Om du försöker importera JSON-dokumentet tillbaka till arbets ytan måste refererade till gångar redan finnas med samma till gångs-ID: n som refereras till i experimentet. Annars kan du inte komma åt det importerade experimentet.

## <a name="versioning-trained-model"></a>Version av tränad modell
En utbildad modell i Azure Machine Learning Studio (klassisk) serialiseras i ett format som kallas en iLearner-fil ( `.iLearner` ) och lagras i Azure Blob Storage-kontot som är kopplat till arbets ytan. Ett sätt att hämta en kopia av iLearner-filen är genom omtränings-API: et. I [den här artikeln](./retrain-machine-learning-model.md) förklaras hur du arbetar med retraining-API. Steg på hög nivå:

1. Konfigurera ditt utbildnings experiment.
2. Lägg till en utgående port för webb tjänst i modulen träna modell, eller den modul som skapar den tränade modellen, till exempel en fin modell för att finjustera modell eller skapa R-modell.
3. Kör ditt utbildnings experiment och distribuera det som en modell utbildnings webb tjänst.
4. Anropa BES-slutpunkten för utbildnings webb tjänsten och ange önskat iLearner-filnamn och platsen för Blob Storage-kontot där det ska lagras.
5. Skörda den producerade iLearner-filen när BES-anropet har slutförts.

Ett annat sätt att hämta iLearner-filen är via PowerShell [*-kommandot Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput). Detta kan vara enklare om du bara vill hämta en kopia av iLearner-filen utan att behöva träna modellen program mässigt.

När du har iLearner-filen som innehåller den tränade modellen kan du sedan använda din egen versions strategi. Strategin kan vara så enkelt som att tillämpa en för-/postfix som en namngivnings konvention och bara lämna iLearner-filen i Blob Storage eller kopiera/importera den till versions kontroll systemet.

Den sparade iLearner-filen kan sedan användas för att välja poäng genom distribuerade webb tjänster.

## <a name="versioning-web-service"></a>Webb tjänst för versions hantering
Du kan distribuera två typer av webb tjänster från ett Azure Machine Learning Studio (klassisk) experiment. Den klassiska webb tjänsten är nära kopplad till experimentet och arbets ytan. Den nya webb tjänsten använder Azure Resource Manager Framework och är inte längre kopplad till det ursprungliga experimentet eller arbets ytan.

### <a name="classic-web-service"></a>Klassisk webb tjänst
Om du vill ha en klassisk webb tjänst version kan du dra nytta av webb tjänstens slut punkts konstruktion. Här är ett typiskt flöde:

1. Från ditt förutsägelse experiment distribuerar du en ny klassisk webb tjänst som innehåller en standard slut punkt.
2. Du skapar en ny slut punkt med namnet EP2, som visar den aktuella versionen av experimentet/den tränade modellen.
3. Du går tillbaka och uppdaterar ditt förutsägelse experiment och den tränade modellen.
4. Du omdistribuerar det förutsägelse experiment som sedan uppdaterar standard slut punkten. Men detta kommer inte att förändra EP2.
5. Du skapar ytterligare en slut punkt med namnet EP3, som visar den nya versionen av experimentet och den tränade modellen.
6. Gå tillbaka till steg 3 om det behövs.

Med tiden kan du ha många slut punkter som skapats i samma webb tjänst. Varje slut punkt representerar en tidpunkts kopia av experimentet som innehåller den tränade modellens tidpunkts version. Du kan sedan använda extern logik för att avgöra vilken slut punkt som ska anropas, vilket innebär att du väljer en version av den tränade modellen för poängsättnings körningen.

Du kan också skapa många identiska slut punkter för webb tjänster och sedan korrigera olika versioner av iLearner-filen till slut punkten för att uppnå liknande påverkan. I [den här artikeln](create-models-and-endpoints-with-powershell.md) beskrivs mer information hur du utför det.

### <a name="new-web-service"></a>Ny webb tjänst
Om du skapar en ny Azure Resource Manager-baserad webb tjänst är slut punkts konstruktionen inte längre tillgänglig. I stället kan du generera WSD-filer (Web Service definition) i JSON-format från ditt förutsägande experiment genom att använda [AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell-kommandot, eller genom att använda [*export-AzMlWebservice*](/powershell/module/az.machinelearning/export-azmlwebservice) PowerShell-kommandot från en distribuerad Resource Manager-baserad webb tjänst.

När du har exporterat WSD-filen och-versionen kan du även distribuera WSD som en ny webb tjänst i en annan webb tjänst plan i en annan Azure-region. Se bara till att du anger rätt lagrings konto konfiguration och det nya ID: t för webb tjänst planen. Om du vill korrigera i olika iLearner-filer kan du ändra WSD-filen och uppdatera plats referensen för den tränade modellen och distribuera den som en ny webb tjänst.

## <a name="automate-experiment-execution-and-deployment"></a>Automatisera experiment körning och distribution
En viktig aspekt av ALM är att kunna automatisera programmets körnings-och distributions process. I Azure Machine Learning Studio (klassisk) kan du göra detta med hjälp av [PowerShell-modulen](https://aka.ms/amlps). Här är ett exempel på åtgärder från slut punkt till slut punkt som är relevanta för en standard process för automatisk ALM-körning/distribution med hjälp av [PowerShell-modulen Azure Machine Learning Studio (klassisk)](https://aka.ms/amlps). Varje steg är länkat till en eller flera PowerShell-cmdletarna som du kan använda för att utföra det steget.

1. [Ladda upp en data uppsättning](https://github.com/hning86/azuremlps#upload-amldataset).
2. Kopiera ett utbildnings experiment till arbets ytan från en [arbets yta](https://github.com/hning86/azuremlps#copy-amlexperiment) eller från [galleriet](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)eller [Importera](https://github.com/hning86/azuremlps#import-amlexperimentgraph) ett [exporterat](https://github.com/hning86/azuremlps#export-amlexperimentgraph) experiment från den lokala disken.
3. [Uppdatera data uppsättningen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i övnings experimentet.
4. [Kör övnings experimentet](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Höj upp den tränade modellen](https://github.com/hning86/azuremlps#promote-amltrainedmodel).
6. [Kopiera ett förutsägelse experiment](https://github.com/hning86/azuremlps#copy-amlexperiment) till arbets ytan.
7. [Uppdatera den tränade modellen](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) i förutsägande experimentet.
8. [Kör förutsägelse experimentet](https://github.com/hning86/azuremlps#start-amlexperiment).
9. [Distribuera en webb tjänst](https://github.com/hning86/azuremlps#new-amlwebservice) från förutsägelse experimentet.
10. Testa webb tjänst [resurs posterna](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) eller [bes](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) -slutpunkten.

## <a name="next-steps"></a>Nästa steg
* Hämta [PowerShell-modulen Azure Machine Learning Studio (klassisk)](https://aka.ms/amlps) och börja automatisera dina Alm-uppgifter.
* Lär dig hur du [skapar och hanterar ett stort antal ml-modeller genom att bara använda ett enda experiment](create-models-and-endpoints-with-powershell.md) via PowerShell-och retraining-API.
* Lär dig mer om att [distribuera Azure Machine Learning-webbtjänster](deploy-a-machine-learning-web-service.md).