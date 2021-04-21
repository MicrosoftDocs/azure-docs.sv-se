---
title: Vad är nytt i versionen?
titleSuffix: Azure Machine Learning
description: Lär dig mer om de senaste uppdateringarna Azure Machine Learning och Python-VERSIONerna för maskininlärning och dataförberedelser.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 1de495253dacac5aeab7dcff95f74aeed11782a8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750742"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning information

I den här artikeln får du lära dig Azure Machine Learning versioner.  Det fullständiga SDK-referensinnehållet finns på Azure Machine Learning [](/python/api/overview/azure/ml/intro) sdk för Python-referenssidan.

__RSS-feed__: Håll dig informerad när den här sidan uppdateras genom att kopiera och klistra in följande webbadress i feed-läsaren: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-04-19"></a>2021-04-19

### <a name="azure-machine-learning-sdk-for-python-v1270"></a>Azure Machine Learning SDK för Python v1.27.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-core**
    + Möjligheten att åsidosätta standardvärdet för timeout för artefaktuppladdning via miljövariabeln "AZUREML_ARTIFACTS_DEFAULT_TIMEOUT" har lagts till.
    + En bugg har åtgärdats där Docker-inställningar i Miljö-objekt i ScriptRunConfig inte respekteras.
    + Tillåt partitionering av en datauppsättning när den kopieras till ett mål.
    + Ett anpassat läge har lagts till i OutputDatasetConfig för att aktivera skicka skapade datauppsättningar i pipelines via en länkfunktion. Dessa stödförbättringar har gjorts för att aktivera tabellpartitionering för PRS.
    + En ny KubernetesCompute-beräkningstyp har lagts till i azureml-core.
  + **azureml-pipeline-core**
    + Lägga till ett anpassat läge i OutputDatasetConfig och göra det möjligt för en användare att passera skapade datauppsättningar i pipelines via en länkfunktion. Filsökvägsmål stöder platshållare. Dessa stöder de förbättringar som gjorts för att aktivera tabellpartitionering för PRS.
    + Tillägg av ny KubernetesCompute-beräkningstyp till azureml-core.
  + **azureml-pipeline-steps**
    + Tillägg av ny KubernetesCompute-beräkningstyp till azureml-core.
  + **azureml-synapse**
    + Uppdatera URL:en för Spark-användargränssnittet i widgeten för azureml synapse
  + **azureml-train-automl-client**
    + STL-featurizern för prognosuppgiften använder nu en mer robust identifiering av säsongsberoende baserat på tidsseriens frekvens.
  + **azureml-train-core**
    + En bugg har åtgärdats där Docker-inställningar i Miljöobjekt inte respekteras.
    + Tillägg av ny KubernetesCompute-beräkningstyp till azureml-core.


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Azure Machine Learning SDK för Python v1.26.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdat ett problem där Naive-modeller rekommenderas i AutoMLStep körs och misslyckas med funktioner för fördröjning eller rullande fönster. Dessa modeller rekommenderas inte när målfördröjningar eller mål rullande fönsterstorlek har angetts.
    +  Konsolutdata har ändrats när en AutoML-körning skickas för att visa en portallänk till körningen.
  + **azureml-core**
    + HDFS-läge har lagts till i dokumentationen.
    + Stöd har lagts till för att förstå fildatamängdspartitioner baserat på glob-struktur.
    + Stöd har lagts till för uppdatering av containerregister som är associerade med AzureML-arbetsytan.
    + Inaktuella miljöattribut under DockerSection – "enabled", "shared_volume" och "arguments" är en del av DockerConfiguration i RunConfiguration nu.
    + Uppdaterad dokumentation om PIPELINE CLI-kloning
    + Portalens URI:er har uppdaterats för att inkludera klientorganisation för autentisering
    + Experimentnamnet har tagits bort från körnings-URI:er för att undvika omdirigeringar 
    + Uppdaterat experiment UPP FÖR att använda experiment-ID.
    + Felkorrigeringar för att ansluta fjärrbearbetning med AzureML CLI.
    + Portalens URI:er har uppdaterats för att inkludera klientorganisation för autentisering.
    + Uppdaterade experiment-URI för att använda experiment-ID.
  + **azureml-interpret**
    + azureml-interpret har uppdaterats för att använda interpret-community 0.17.0
  + **azureml-opendatasets**
    + Validering av startdatum och slutdatum för indata samt felindikering om det inte är datetime-typ.
  + **azureml-parallel-run**
    + [Experimentell funktion] Lägg `partition_keys` till parametern i ParallelRunConfig, om den anges, partitioneras indatauppsättningarna i minibatcherna av de nycklar som anges av den. Det kräver att alla indatauppsättningar är partitionerade datauppsättningar.
  + **azureml-pipeline-steps**
    + Felkorrigering – stöd för path_on_compute samtidigt som datauppsättningskonfigurationen kan laddas ned.
    + Inaktuell RScriptStep till förmån för att använda CommandStep för att köra R-skript i pipelines. 
    + Inaktuella EstimatorStep till förmån för att använda CommandStep för att köra ML-träning (inklusive distribuerad träning) i pipelines.
  + **azureml-sdk**
    + Uppdatera python_requires till < 3.9 för azureml-sdk
  + **azureml-train-automl-client**
    +  Konsolutdata har ändrats när en AutoML-körning skickas för att visa en portallänk till körningen.
  + **azureml-train-core**
    + DockerSections attribut "enabled", "shared_volume" och "arguments" har ersatts med DockerConfiguration med ScriptRunConfig.
    +  Använda Azure Open Datasets för MNIST-datauppsättning
    + Felmeddelanden för Hyperdrive har uppdaterats.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Azure Machine Learning SDK för Python v1.25.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    +  Konsolutdata har ändrats när en AutoML-körning skickas för att visa en portallänk till körningen.
  + **azureml-core**
    + Börjar stödja uppdatering av containerregistret för arbetsytan i SDK och CLI
    + Inaktuella attribut för DockerSection är "enabled", "shared_volume" och "arguments" för att använda DockerConfiguration med ScriptRunConfig.
    + Uppdaterad dokumentation om PIPELINE CLI-kloning
    + Portalens URI:er har uppdaterats för att inkludera klientorganisation för autentisering
    + Experimentnamnet har tagits bort från körnings-URI:er för att undvika omdirigeringar
    + Uppdaterat experiment UPP FÖR att använda experiment-ID.
    + Felkorrigeringar för att koppla fjärrbearbetning med az cli
    + Portalens URI:er har uppdaterats för att inkludera klientorganisation för autentisering.
    + Stöd har lagts till för att förstå fildatamängdspartitioner baserat på glob-struktur.
  + **azureml-interpret**
    + azureml-interpret har uppdaterats för att använda interpret-community 0.17.0
  + **azureml-opendatasets**
    + Validering av startdatum och slutdatum och felindikering om det inte är datetime-typ.
  + **azureml-pipeline-core**
    + Felkorrigering – stöd för path_on_compute medan datauppsättningskonfigurationen kan laddas ned.
  + **azureml-pipeline-steps**
    + Felkorrigering – stöd för path_on_compute medan datauppsättningskonfigurationen kan laddas ned.
    + Förfalla RScriptStep och använd CommandStep för att köra R-skript i pipelines. 
    + Inaktuell EstimatorStep till förmån för att använda CommandStep för att köra ML-träning (inklusive distribuerad träning) i pipelines.
  + **azureml-train-automl-runtime**
    +  Konsolutdata har ändrats när en AutoML-körning skickas för att visa en portallänk till körningen.
  + **azureml-train-core**
    + Inaktuella attribut för DockerSection är "enabled", "shared_volume" och "arguments" för att använda DockerConfiguration med ScriptRunConfig.
    + Använda Azure Open Datasets för MNIST-datauppsättning
    + Felmeddelanden för HyperDrive har uppdaterats.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning Studio Notebooks Experience (marsuppdatering)
+ **Nya funktioner**
  + Rendera CSV/TSV. Användarna kommer att kunna rendera och TSV/CSV-filen i ett rutnätsformat för enklare dataanalys. 
  + SSO-autentisering för Beräkningsinstans. Användare kan nu enkelt autentisera nya beräkningsinstanser direkt i notebook-användargränssnittet, vilket gör det enklare att autentisera och använda Azure-SDK:er direkt i AzureML. 
  + Beräkningsinstansmått. Användare kommer att kunna visa beräkningsmått som CPU-användning och minne via terminalen.
  + Filinformation. Användarna kan nu se filinformation inklusive tiden för senaste ändring och filstorlek genom att klicka på de tre punkterna bredvid en fil.

+ **Felkorrigeringar och förbättringar**
  + Förbättrade sidinläsningstider.
  + Förbättrad prestanda.
  + Förbättrad hastighet och kerneltillförlitlighet.
  + Få vertikal egendom genom att permanent flytta upp notebook-filfönstret
  + Länkar är nu klickbara i Terminal
  + Förbättrade IntelliSense-prestanda


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK för Python v1.24.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Bakåtkompatibla importer har tagits bort från `azureml.automl.core.shared` . Det gick inte att hitta modulens `azureml.automl.core.shared` fel i namnområdet genom att importera från `azureml.automl.runtime.shared` .
  + **azureml-contrib-automl-dnn-vision**
    + Exponerade yolo-modellen för objektidentifiering.
  + **azureml-contrib-dataset**
    + Funktioner har lagts till för att filtrera tabelldatauppsättningar efter kolumnvärden och fildatauppsättningar efter metadata.
  + **azureml-contrib-fairness**
    + Inkludera JSON-schema i hjul för `azureml-contrib-fairness`
  + **azureml-ib-manipulation**
    + Med inställningen show_output till True när du distribuerar modeller, spelas inferenskonfigurationen och distributionskonfigurationen upp igen innan begäran skickas till servern.
  + **azureml-core**
    + Funktioner har lagts till för att filtrera tabelldatauppsättningar efter kolumnvärden och fildatauppsättningar efter metadata.
    + Tidigare var det möjligt att användare skapade etableringskonfigurationer för ComputeTarget som inte uppfyller kraven på lösenordsstyrka för fältet `admin_user_password` (d.v.s. att de måste innehålla minst 3 av följande: 1 gemen, 1 versal, 1 siffra och 1 specialtecken från följande uppsättning: ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` ). Om användaren skapade en konfiguration med ett svag lösenord och körde ett jobb med den konfigurationen misslyckas jobbet vid körning. Nu kommer `AmlCompute.provisioning_configuration` anropet till att skicka ett med ett tillhörande felmeddelande som förklarar kraven på `ComputeTargetException` lösenordsstyrka. 
    + Dessutom var det också möjligt att i vissa fall ange en konfiguration med ett negativt antal maximalt antal noder. Det går inte längre att göra detta. Kommer nu `AmlCompute.provisioning_configuration` att skapa ett om argumentet är ett negativt `ComputeTargetException` `max_nodes` heltal.
    + Om du show_output till Sant när du distribuerar modeller visas härledningskonfiguration och distributionskonfiguration.
    + När du show_output till Sant när du väntar tills modelldistributionen är klar visas distributionsåtgärdens förlopp.
    + Tillåt kundens angivna konfigurationskatalog för AzureML-autentisering via miljövariabeln: AZUREML_AUTH_CONFIG_DIR
    + Tidigare var det möjligt att skapa en etableringskonfiguration med det minsta antalet noder som är mindre än det högsta antalet noder. Jobbet skulle köras men misslyckas vid körning. Den här buggen har nu åtgärdats. Om du nu försöker skapa en etableringskonfiguration med `min_nodes < max_nodes` SDK:n skapas en `ComputeTargetException` .
  + **azureml-interpret**
    + instrumentpanelen för förklaring har inte visat aggregerade funktions prioriteter för glesa förklaringar
    + optimerad minnesanvändning av ExplanationClient i paketet azureml-interpret
  + **azureml-train-automl-client**
    +  Åtgärdade show_output =False för att returnera kontrollen till användaren när du kör med spark.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning Studio Notebooks Experience (februariuppdatering)
+ **Nya funktioner**
  + [Ursprunglig terminal (GA).](./how-to-access-terminal.md) Användarna kommer nu att ha åtkomst till en integrerad terminal och Git-drift via den integrerade terminalen.
  + Notebook-kodfragment (förhandsversion). Vanliga Azure ML-kodutdrag är nu tillgängliga. Gå till panelen med kodfragment, som kan nås via verktygsfältet, eller aktivera menyn kodfragment med Ctrl + Blanksteg.  
  + [Kortkommandon](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts). Fullständig paritet med kortkommandon som är tillgängliga i Jupyter. 
  + Ange cellparametrar. Visar användare vilka celler i en notebook-dator som är parameterceller och kan köra parametriserade anteckningsböcker via [Paper mill](https://github.com/nteract/papermill) på beräkningsinstansen.
  + Terminal- och Kernel-sessionshanterare: Användare kommer att kunna hantera alla kernels och terminalsessioner som körs på deras beräkning.
  + Delningsknapp. Användare kan nu dela valfri fil i Notebook-utforskaren genom att högerklicka på filen och använda delningsknappen.


+ **Felkorrigeringar och förbättringar**
  + Förbättrade sidinläsningstider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kerneltillförlitlighet
  + Snurrande hjul har lagts till för att visa förloppet för alla [pågående beräkningsinstansåtgärder](./how-to-run-jupyter-notebooks.md#status-indicators).
  + Högerklicka i Utforskaren. Om du högerklickar på en fil öppnas nu filåtgärder. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK för Python v1.23.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-core**
    + [Experimentell funktion] Lägg till stöd för att länka Synapse-arbetsyta till AML som en länkad tjänst
    + [Experimentell funktion] Lägg till stöd för att koppla Synapse Spark-pool till AML som en beräkning
    + [Experimentell funktion] Lägg till stöd för identitetsbaserad dataåtkomst. Användare kan registrera datalager eller datauppsättningar utan att ange autentiseringsuppgifter. I sådana fall används användarnas AAD-token eller den hanterade identiteten för beräkningsmålet för autentisering. Lär dig mer [här](./how-to-identity-based-data-access.md).
  + **azureml-pipeline-steps**
    + [Experimentell funktion] Lägg till stöd [för SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)
  + **azureml-synapse**
    + [Experimentell funktion] Lägg till stöd för Spark Magic för att köra en interaktiv session i Synapse Spark-poolen.
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + I den här uppdateringen har vi lagt till holt men exponentiell utjämning i verktygslådan för prognostiserade AutoML SDK. I en tidsserie väljs den bästa modellen av [AICc (Korrigerade Akaikes informationskriterium)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) och returneras.
    + AutoML genererar nu två loggfiler i stället för en. Loggutdrag går till den ena eller den andra beroende på vilken process loggutdraget genererades i.
    + Ta bort onödig förutsägelse i exemplet under modellträning med korsvalidering. Detta kan minska modellträningstiden i vissa fall, särskilt för prognosmodeller i tidsserier.
  + **azureml-contrib-fairness**
    + Lägg till ett JSON-schema för instrumentpanelenDictionary uploads (Dictionary uploads).
  + **azureml-contrib-interpret**
    + azureml-contrib-interpret README uppdateras för att återspegla att paketet tas bort i nästa uppdatering efter att ha blivit inaktuellt sedan oktober, använd azureml-interpret-paketet i stället
  + **azureml-core**
    + Tidigare var det möjligt att skapa en etableringskonfiguration med det minsta antalet noder som är mindre än det maximala antalet noder. Detta har nu åtgärdats. Om du nu försöker skapa en etableringskonfiguration med `min_nodes < max_nodes` SDK:n skapas en `ComputeTargetException` .
    +  Åtgärdar bugg i wait_for_completion i AmlCompute som gjorde att funktionen returnerade kontrollflödet innan åtgärden slutfördes
    + Run.fail() är nu inaktuell. Använd Run.tag() för att markera körningen som misslyckad eller använd Run.cancel() för att markera körningen som avbruten.
    + Visa felmeddelandet "Environment name expected str, {} found" när det angivna miljönamnet inte är en sträng.
  + **azureml-train-automl-client**
    + En bugg som förhindrade att AutoML-experiment som utfördes Azure Databricks i kluster avbröts.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK för Python v1.22.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + En bugg där ett extra pip-beroende lades till i conda yml-filen för visionsmodeller har åtgärdats.
  + **azureml-automl-runtime**
    + En bugg har åtgärdats där klassiska prognosmodeller (t.ex. AutoArima) kunde ta emot träningsdata där rader med imputerade målvärden inte fanns. Detta bryter mot datakontraktet för dessa modeller. * Åtgärdade olika buggar med beteendet för fördröjning efter förekomst i tidsserieoperatorn. Tidigare markeras inte alla imputerade rader korrekt av åtgärden för fördröjning och genererar därför inte alltid rätt förekomstfördröjning. Dessutom har vissa kompatibilitetsproblem åtgärdats mellan fördröjningsoperatorn och den rullande fönsteroperatorn med beteendet för fördröjning efter förekomst. Detta ledde tidigare till att operatorn för rullande fönster släppte några rader från träningsdata som den annars skulle använda.
  + **azureml-core**
    + Lägga till stöd för tokenautentisering av målgruppen.
    + Lägg `process_count` till [i PyTorchConfiguration för](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) att stödja PyTorch-jobb med flera processer med flera noder.
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) nu GA och inte längre experimentell.
    + [ParallelRunConfig: Lägg](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)till allowed_failed_count och allowed_failed_percent för att kontrollera tröskelvärdet för fel på minimibatchnivå. Feltröskelvärdet har nu 3 varianter:
       + error_threshold – antalet tillåtna misslyckade minibatchobjekt; 
       + allowed_failed_count – antalet tillåtna misslyckade minibatchar; 
       + allowed_failed_percent – procentandelen tillåtna misslyckade minibatchar. 
       
       Ett jobb stoppas om det överskrider något av dem. error_threshold krävs för att hålla den bakåtkompatibel. Ange värdet till -1 för att ignorera det.
    + Hantering av tomt utrymme i AutoMLStep-namn har åtgärdats.
    + ScriptRunConfig stöds nu av HyperDriveStep
  + **azureml-train-core**
    + HyperDrive-körningar som anropas från en ScriptRun betraktas nu som en underordnad körning.
    + Lägg `process_count` till [i PyTorchConfiguration för](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) att stödja PyTorch-jobb med flera processer med flera noder.
  + **azureml-widgets**
    + Lägg till widgeten ParallelRunStepDetails för att visualisera status för en ParallelRunStep.
    + Gör att hyperdrive-användare kan se ytterligare en axel i diagrammet med parallella koordinater som visar det måttvärde som motsvarar varje uppsättning hyperparametrar för varje underordnad körning.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning Studio Notebooks Experience (januariuppdatering)
+ **Nya funktioner**
  + Inbyggd Markdown-redigerare i AzureML. Användare kan nu återge och redigera Markdown-filer inbyggt i AzureML Studio.
  + [Kör-knappen för skript (.py, . R och .sh).](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script) Användare kan nu enkelt köra Python-, R- och Bash-skript i AzureML
  + [Variable Explorer](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook). Utforska innehållet i variabler och dataramar i en popup-panel. Användarna kan enkelt kontrollera datatyp, storlek och innehåll.
  + [Innehållstabell](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc). Gå till avsnitt i anteckningsboken, vilket anges av Markdown-huvuden.
  + Exportera din notebook-fil som Notebook/HTML/Py. Skapa lättanvända notebook-filer genom att exportera till LaTex, HTML eller .py
  + Intellicode. ML-drivna resultat ger en [förbättrad intelligent automatisk komplettering.](/visualstudio/intellicode/overview)

+ **Felkorrigeringar och förbättringar**
  + Förbättrade sidinläsningstider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kerneltillförlitlighet
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK för Python v1.21.0
+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + CLI-hjälptext har åtgärdats när AmlCompute med UserAssigned Identity används
  + **azureml-contrib-automl-dnn-vision**
    + Distribuera och ladda ned knappar blir synliga för AutoML-körningar och modeller kan distribueras eller laddas ned på samma sätt som andra AutoML-körningar. Det finns två nya filer (scoring_file_v_1_0_0.py och conda_env_v_1_0_0.yml) som innehåller ett skript för att köra inferens och en yml-fil för att återskapa conda-miljön. Filen model.pth har också bytt namn till att använda tillägget ".pt".
  + **azureml-core**
    + MSI-stöd för azure-cli-ml
    + Stöd för användar tilldelad hanterad identitet.
    + Med den här ändringen ska kunderna kunna ange en användartilldelade identitet som kan användas för att hämta nyckeln från kundens nyckelvalv för kryptering i vila.
    +  åtgärda row_count=0 för profilen för mycket stora filer – åtgärda fel i dubbel konvertering för avgränsade värden med utfyllnad av tomt utrymme
    + Ta bort experimentell flagga för utdatauppsättningens GA
    + Uppdatera dokumentationen om hur du hämtar en specifik version av en modell
    + Tillåt uppdatering av arbetsytan för åtkomst i blandat läge vid privat länk
    + Korrigering för att ta bort ytterligare registrering i datalager för funktionen för att återuppta körning
    + STÖD för CLI/SDK har lagts till för att uppdatera den primära användar tilldelade identiteten för arbetsytan
  + **azureml-interpret**
    + uppdaterat azureml-interpret till interpret-community 0.16.0
    + minnesoptimeringar för förklaring av klienten i azureml-interpret
  + **azureml-train-automl-runtime**
    + Aktiverad strömning för ADB-körningar
  + **azureml-train-core**
    + Korrigering för att ta bort ytterligare registrering i datalager för funktionen för att återuppta körning
  + **azureml-widgets**
    + Kunder bör inte se ändringar i befintlig visualisering av körningsdata med widgeten och har nu stöd om de vill använda villkorliga hyperparametrar.
    + Widgeten för användarkörning innehåller nu en detaljerad förklaring av varför en körning är i kö.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK för Python v1.20.0
+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + framework_version har lagts till i OptimizationConfig. Den används när modellen registreras med ramverket MULTI.
  + **azureml-contrib-optimization**
    + framework_version har lagts till i OptimizationConfig. Den används när modellen registreras med ramverket MULTI.
  + **azureml-pipeline-steps**
    + Introduktion till CommandStep som skulle ta kommandot för att bearbeta. Kommandot kan innehålla körbara filer, shell-kommandon, skript osv.
  + **azureml-core**
    + Nu stöder skapande av arbetsytor användar tilldelad identitet. Lägga till stöd från SDK/CLI
    + Ett problem har åtgärdats på service.reload() för att hämta ändringar score.py i lokal distribution.
    + `run.get_details()` har ett extra fält med namnet "submittedBy" som visar författarens namn för den här körningen.
    + Redigerad model.register-metoddokumentation för att nämna hur du registrerar modellen från kör direkt
    + Åtgärdat IOT-Server problem med ändringshantering av anslutningsstatus.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio Notebooks Experience (decemberuppdatering)
+ **Nya funktioner**
  + Sök efter användarfilnamn. Användare kan nu söka i alla filer som sparats på en arbetsyta.
  + Stöd för Markdown sida vid sida per notebook-cell. I en notebook-cell kan användarna nu välja att visa renderad markdown och markdownsyntax sida vid sida.
  + Cellstatusfält. Statusfältet visar i vilket tillstånd en kodcell finns, om en cellkörning lyckades och hur lång tid det tog att köra. 
   
+ **Felkorrigeringar och förbättringar**
  + Förbättrade sidinläsningstider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kerneltillförlitlighet

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK för Python v1.19.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Experimentellt stöd för testdata har lagts till i AutoMLStep.
    + Lade till den inledande kärnimplementering av funktionen för inmatning av testuppsättning.
    + Referenser till sklearn.externals.joblib har flyttats så att de är direkt beroende av joblib.
    + introducerar en ny AutoML-uppgiftstyp av typen "image-instance-segmentation".
  + **azureml-automl-runtime**
    + Lade till den inledande kärnimplementering av funktionen för inmatning av testuppsättning.
    + När alla strängar i en textkolumn har en längd på exakt 1 tecken fungerar inte TfIdf word-gram-featurizer eftersom dess tokeniserare ignorerar strängarna med färre än 2 tecken. Den aktuella kodändringen gör att AutoML kan hantera det här användningsfallet.
    + introducerar en ny AutoML-uppgiftstyp av typen "image-instance-segmentation".
  + **azureml-ib-automl-dnn-nlp**
    + Inledande PR för nytt dnn-nlp-paket
  + **azureml-ib-automl-dnn-vision**
    + introducerar en ny AutoML-uppgiftstyp av typen "image-instance-segmentation".
  + **azureml-contrib-automl-pipeline-steps**
    + Det här nya paketet ansvarar för att skapa steg som krävs för många modeller för att träna/dra slutsatser. – Den flyttar även train/inference-koden till paketet azureml.train.automl.runtime så att eventuella framtida korrigeringar blir tillgängliga automatiskt via de härdade miljöerna.
  + **azureml-contrib-dataset**
    + introducera en ny AutoML-uppgiftstyp av typen "image-instance-segmentation".
  + **azureml-core**
    + Lade till den inledande kärnimplementering av funktionen för inmatning av testuppsättning.
    + Åtgärda xref-varningar för dokumentation i paketet azureml-core
    + Doc-strängkorrigeringar för kommandosupportfunktionen i SDK
    + Lägger till kommandoegenskapen i RunConfiguration. Funktionen gör det möjligt för användare att köra ett faktiskt kommando eller körbara filer på beräkningen via AzureML SDK.
    + Användare kan ta bort ett tomt experiment givet ID:t för experimentet.
  + **azureml-dataprep**
    + Stöd för datauppsättningar har lagts till för Spark som skapats med Scala 2.12. Detta lägger till det befintliga 2.11-stödet.
  + **azureml-mlflow**
    + AzureML-MLflow lägger till säkra skydd i fjärrskript för att undvika tidig avslutning av skickade körningar.
  + **azureml-pipeline-core**
    + En bugg vid inställningen av en standardpipeline för pipelineslutpunkten som skapats via användargränssnittet har åtgärdats
  + **azureml-pipeline-steps**
    + Experimentellt stöd för testdata har lagts till i AutoMLStep.
  + **azureml-tensorboard**
    + Åtgärda xref-varningar för dokumentation i paketet azureml-core
  + **azureml-train-automl-client**
    + Experimentellt stöd för testdata har lagts till i AutoMLStep.
    + Lade till den inledande kärnimplementering av funktionen för inmatning av testuppsättning.
    + introducera en ny AutoML-uppgiftstyp av typen "image-instance-segmentation".
  + **azureml-train-automl-runtime**
    + Lade till den inledande kärnimplementering av funktionen för inmatning av testuppsättning.
    + Åtgärda beräkningen av rådataförklaringarna för den bästa AutoML-modellen om AutoML-modellerna tränas med hjälp validation_size inställningen.
    + Referenser till sklearn.externals.joblib har flyttats så att de är direkt beroende av joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric() bör slutföras snabbare nu
    + Förbättrad felhantering i HyperDrive SDK.
    +  Inaktuella alla beräkningsklasser till förmån för att använda ScriptRunConfig för att konfigurera experimentkörningar. Inaktuella klasser är:
        + MMLBase
        + Estimator
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Inaktuella användningen av Scriptl och Guci som giltiga indatatyper för Estimator-klasser till förmån för att använda PyTorchConfiguration med ScriptRunConfig.
    + Användningen av Mpi som en giltig indatatyp för Estimator-klasser har ersatts med MpiConfiguration med ScriptRunConfig.
    + Lägga till kommandoegenskapen i runconfiguration. Funktionen gör det möjligt för användare att köra ett faktiskt kommando eller körbara filer på beräkningen via AzureML SDK.

    +  Inaktuella alla beräkningsklasser till förmån för att använda ScriptRunConfig för att konfigurera experimentkörningar. Inaktuella klasser är: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + Inaktuell användning av Scriptl och Gnod som en giltig typ av indata för Estimator-klasser till förmån för att använda PyTorchConfiguration med ScriptRunConfig. 
    + Användningen av Mpi som en giltig typ av indata för Estimator-klasser har ersatts med MpiConfiguration med ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio Notebooks Experience (novemberuppdatering)
+ **Nya funktioner**
   + Intern terminal. Användarna kommer nu att ha åtkomst till en integrerad terminal samt Git-drift via den [integrerade terminalen.](./how-to-access-terminal.md)
  + Dubblettmapp 
  + Kostnadsberäkning för listrutan Compute 
  + Offline Compute Pylance 

+ **Felkorrigeringar och förbättringar**
  + Förbättrade sidinläsningstider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kerneltillförlitlighet
  + Uppladdning av stora filer. Du kan nu ladda upp >95 MB

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK för Python v1.18.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    +  Förbättrad hantering av korta tidsserier genom att tillåta utfyllnad av dem med gaussiskt brus.
  + **azureml-automl-runtime**
    + Throw ConfigException om en DateTime-kolumn har OutOfBoundsDatetime-värde
    + Förbättrad hantering av korta tidsserier genom att tillåta utfyllnad av dem med gaussiskt brus.
    + Se till att varje textkolumn kan utnyttja char-gram-transform med n-gramintervallet baserat på längden på strängarna i textkolumnen
    + Ge förklaringar av rådatafunktionen för bästa läge för AutoML-experiment som körs på användarens lokala beräkning
  + **azureml-core**
    + Fäst paketet: pyjwt för att undvika att hämta in äldre versioner i kommande versioner.
    + När du skapar ett experiment returneras det aktiva eller senast arkiverade experimentet med samma förnamn om experimentet finns eller ett nytt experiment.
    + Om get_experiment anropas efter namn returneras det aktiva eller senast arkiverade experimentet med det angivna namnet.
    + Användarna kan inte byta namn på ett experiment när de återaktiverar det.
    + Förbättrat felmeddelande för att inkludera möjliga korrigeringar när en datauppsättning felaktigt skickas till ett experiment (t.ex. ScriptRunConfig). 
    + Förbättrad dokumentation `OutputDatasetConfig.register_on_complete` för att inkludera beteendet för vad som händer när namnet redan finns.
    + Om du anger indata- och utdatanamn för datauppsättningar som kan krocka med vanliga miljövariabler resulterar det nu i en varning
    + Parametern har om `grant_workspace_access` syfte vid registrering av datalager. Ställ in den på `True` för att komma åt data bakom det virtuella nätverket från Machine Learning Studio.
      [Läs mer](./how-to-enable-studio-virtual-network.md)
    + Api:et för länkad tjänst förfinas. I stället för att ange resurs-ID har vi tre separata parametrar sub_id, rg och namn som definierats i konfigurationen.
    + Aktivera synkronisering av arbetsytans token som en offentlig metod för att göra det möjligt för kunder att lösa problem med tokenkorruption på egen sätt.
    + Den här ändringen gör att en tom sträng kan användas som ett värde för en script_param
  + **azureml-train-automl-client**
    +  Förbättrad hantering av korta tidsserier genom att tillåta utfyllnad av dem med gaussiskt brus.
  + **azureml-train-automl-runtime**
    + Throw ConfigException om en DateTime-kolumn har OutOfBoundsDatetime-värde
    + Stöd har lagts till för att tillhandahålla raw-funktionsförklaringar för bästa modell för AutoML-experiment som körs på användarens lokala beräkning
    + Förbättrad hantering av korta tidsserier genom att tillåta utfyllnad av dem med gaussiskt brus.
  + **azureml-train-core**
    + Den här ändringen gör att en tom sträng kan användas som ett värde för en script_param
  + **azureml-train-restclients-hyperdrive**
    + README har ändrats för att ge mer kontext
  + **azureml-widgets**
    + Lägg till stöd för strängar i diagram/parallella koordinater-biblioteket för widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Dataetiketter för bildinstanssegmentering (polygonanteckningar) (förhandsversion)

Projekttypen för bildinstanssegmentering (polygonanteckningar) i dataetiketteringen är nu tillgänglig, så att användarna kan rita och kommentera med polygoner runt bildernas objekt. Användare kommer att kunna tilldela en klass och en polygon till varje objekt som är av intresse i en bild.

Läs mer om [bildinstanssegmenteringsetiketter.](how-to-label-images.md)



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK för Python v1.17.0
+ **nya exempel**
  + En ny community-driven lagringsplats med exempel finns på https://github.com/Azure/azureml-examples
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdat ett problem där get_output kan skapa ett XGBoostError.
  + **azureml-automl-runtime**
    + Tids-/kalenderbaserade funktioner som skapats av AutoML har nu prefixet .
    + Ett IndexError som inträffar under träningen av StackEnsemble har åtgärdats för klassificeringsdatauppsättningar med ett stort antal klasser och underexempel aktiverat.
    + Ett problem har åtgärdats där VotingRegressor-förutsägelser kan vara felaktiga efter att modellen har omanpassats.
  + **azureml-core**
    + Ytterligare information om relationen mellan AKS-distributionskonfiguration och Azure Kubernetes Service begrepp.
    + Stöd för miljöklientetiketter. Användaren kan märka Miljöer och referera till dem efter etikett.
  + **azureml-dataprep**
    + Bättre felmeddelande när du använder Spark som för närvarande inte stöds med Scala 2.12.
  + **azureml-explain-model**
    + Paketet azureml-explain-model är officiellt inaktuellt
  + **azureml-mlflow**
    + En bugg i mlflow.projects.run har åtgärdats mot azureml-backend där statusen Slutför inte hanterades korrekt.
  + **azureml-pipeline-core**
    + Lägg till stöd för att skapa, visa och hämta pipelineschema baserat på en pipelineslutpunkt.
    +  Förbättrade dokumentationen för PipelineData.as_dataset med ett exempel på ogiltig användning – Om du använder PipelineData.as_dataset felaktigt kommer nu ett ValueException-fel att uppstå
    + Notebook-filer för HyperDriveStep-pipelines har ändrats för att registrera den bästa modellen i en PipelineSteg direkt efter HyperDriveStegkörningen.
  + **azureml-pipeline-steps**
    + Notebook-filer för HyperDriveStep-pipelines har ändrats för att registrera den bästa modellen i en PipelineSteg direkt efter att HyperDriveStep har körts.
  + **azureml-train-automl-client**
    + Åtgärdat ett problem där get_output kan skapa ett XGBoostError.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio Notebooks Experience (oktoberuppdatering)
+ **Nya funktioner**
  + [Fullständigt stöd för virtuella nätverk](./how-to-enable-studio-virtual-network.md)
  + [Fokusläge](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Spara notebook-datorer Ctrl-S
  + Radnummer

+ **Felkorrigeringar och förbättringar**
  + Förbättring av hastighet och kerneltillförlitlighet
  + Uppdateringar av användargränssnittet för Jupyter Widget

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK för Python v1.16.0
+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + AKSWebservice och AKSEndpoints har nu stöd för gränser för processor- och minnesresurser på poddnivå. Dessa valfria gränser kan användas genom att ange `--cpu-cores-limit` och `--memory-gb-limit` flaggor i tillämpliga CLI-anrop
  + **azureml-core**
    + Fästa större versioner av direkta beroenden av azureml-core
    + AKSWebservice och AKSEndpoints har nu stöd för gränser för processor- och minnesresurser på poddnivå. Mer information om [Kubernetes-resurser och begränsningar](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Uppdaterade run.log_table att tillåta att enskilda rader loggas.
    + Statisk metod har lagts `Run.get(workspace, run_id)` till för att hämta en körning endast med hjälp av en arbetsyta 
    + Instansmetoden har lagts `Workspace.get_run(run_id)` till för att hämta en körning i arbetsytan
    + Introduktion till kommandoegenskapen i körningskonfigurationen som gör att användarna kan skicka kommandot i stället för & argument.
  + **azureml-interpret**
    + fixed explanation client is_raw flag behavior in azureml-interpret
  + **azureml-sdk**
    + `azureml-sdk` har officiellt stöd för Python 3.8.
  + **azureml-train-core**
    + Lägga till TensorFlow 2.3-miljö
    + Introduktion av kommandoegenskap i körningskonfigurationen som gör att användarna kan skicka kommandot i stället för & argument.
  + **azureml-widgets**
    + Omdesignat gränssnitt för skriptkörningswidget.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK för Python v1.15.0
+ **Felkorrigeringar och förbättringar**
  + **azureml-contrib-interpret**
    + MANIPULATION-förklararen har flyttats från azureml-contrib-interpret till interpret-community-paketet och bildförklararen har tagits bort från paketet azureml-contrib-interpret
    + visualiseringsinstrumentpanelen har tagits bort från paketet azureml-contrib-interpret, förklaringsklienten har flyttats till azureml-interpret-paketet och är inaktuell i paketet azureml-contrib-interpret och notebooks har uppdaterats för att återspegla bättre API
    + korrigera pypi-paketbeskrivningar för azureml-interpret, azureml-explain-model, azureml-contrib-interpret och azureml-tensorboard
  + **azureml-contrib-notebook**
    + Fäst nbcovert-beroendet < 6 så att pappersbruk 1.x fortsätter att fungera.
  + **azureml-core**
    + Parametrar har lagts till i TensorflowConfiguration- och MpiConfiguration-konstruktorn för att möjliggöra en mer effektiv initiering av klassattributen utan att användaren behöver ange varje enskilt attribut. En PyTorchConfiguration-klass har lagts till för att konfigurera distribuerade PyTorch-jobb i ScriptRunConfig.
    + Fäst versionen av azure-mgmt-resource för att åtgärda autentiseringsfelet.
    + Stöd för Triton No Code Deploy
    + utdatakataloger som anges i Run.start_logging() spåras nu när du använder kör i interaktiva scenarier. De spårade filerna visas i ML Studio när Run.complete() anropas
    + Filkodning kan nu anges när datauppsättningen skapas `Dataset.Tabular.from_delimited_files` med och genom att skicka argumentet `Dataset.Tabular.from_json_lines_files` `encoding` . De kodningar som stöds är "utf8", "iso88591", "latin1", "ascii", utf16", "utf32", "utf8bom" och "windows1252".
    + Felkorrigering när miljöobjekt inte skickas till ScriptRunConfig-konstruktorn.
    + Run.cancel() har uppdaterats för att tillåta att en lokal körning avbryts från en annan dator.
  + **azureml-dataprep**
    +  Problem med timeout för montering av datauppsättningar har åtgärdats.
  + **azureml-explain-model**
    + korrigera pypi-paketbeskrivningar för azureml-interpret, azureml-explain-model, azureml-ib-interpret och azureml-tensorboard
  + **azureml-interpret**
    + visualiseringsinstrumentpanelen har tagits bort från paketet azureml-ib-interpret, förklaringsklienten har flyttats till azureml-interpret-paketet och är inaktuell i paketet azureml-ib-interpret och notebooks har uppdaterats för att återspegla bättre API
    + azureml-interpret-paketet har uppdaterats för att vara beroende av interpret-community 0.15.0
    + korrigera pypi-paketbeskrivningar för azureml-interpret, azureml-explain-model, azureml-ib-interpret och azureml-tensorboard
  + **azureml-pipeline-core**
    +  Ett pipelineproblem med där systemet kan sluta svara när anropas med `OutputFileDatasetConfig` `register_on_complete` `name` parametern inställd på ett befintligt datauppsättningsnamn har åtgärdats.
  + **azureml-pipeline-steps**
    + Inaktuella Databricks-anteckningsböcker har tagits bort.
  + **azureml-tensorboard**
    + korrigera pypi-paketbeskrivningar för azureml-interpret, azureml-explain-model, azureml-ib-interpret och azureml-tensorboard
  + **azureml-train-automl-runtime**
    + visualiseringsinstrumentpanelen har tagits bort från paketet azureml-ib-interpret, förklaringsklienten har flyttats till azureml-interpret-paketet och är inaktuell i paketet azureml-ib-interpret och notebooks har uppdaterats för att återspegla bättre API
  + **azureml-widgets**
    + visualiseringsinstrumentpanelen har tagits bort från paketet azureml-ib-interpret, förklaringsklienten har flyttats till azureml-interpret-paketet och är inaktuell i paketet azureml-ib-interpret och notebooks har uppdaterats för att återspegla bättre API

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK för Python v1.14.0
+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Rutnätsprofilering tas bort från SDK och stöds inte längre.
  + **azureml-accel-models**
    + paketet azureml-accel-models stöder nu Tensorflow 2.x
  + **azureml-automl-core**
    + Felhantering i get_output fall när lokala versioner av Pandas/sklearn inte matchar de som används under träningen har lagts till
  + **azureml-automl-runtime**
    + Åtgärdat en bugg där AutoArima-iterationer misslyckades med en PredictionException och meddelandet: "Tyst fel inträffade under förutsägelsen".
  + **azureml-cli-common**
    + Rutnätsprofilering har tagits bort från SDK och stöds inte längre.
  + **azureml-contrib-server**
    + Uppdatera beskrivningen av översiktssidan för paketet för pypi.
  + **azureml-core**
    + Rutnätsprofilering tas bort från SDK och stöds inte längre.
    + Minska antalet felmeddelanden när det inte går att hämta arbetsytan.
    + Visa inte en varning när det inte går att hämta metadata
    + Nytt Kusto-steg och Kusto-beräkningsmål.
    + Uppdatera dokumentet för SKU-parametern. Ta bort SKU i arbetsytans uppdateringsfunktioner i CLI och SDK.
    + Uppdatera beskrivningen av översiktssidan för paketet för pypi.
    + Uppdaterad dokumentation för AzureML-miljöer.
    + Exponera inställningar för tjänst hanterade resurser för AML-arbetsyta i SDK.
  + **azureml-dataprep**
    + Aktivera körningsbehörighet för filer för montering av datamängd.
  + **azureml-mlflow**
    + Uppdaterad azureML MLflow-dokumentation och notebook-exempel 
    + Nytt stöd för MLflow-projekt med AzureML-backend
    + Stöd för MLflow-modellregister
    + Azure RBAC-stöd för AzureML-MLflow åtgärder har lagts till 
    
  + **azureml-pipeline-core**
    + Förbättrade dokumentationen för PipelineOutputFileDataset.parse_*-metoderna.
    + Nytt Kusto-steg och Kusto Compute Target.
    + Den angivna Swaggerurl-egenskapen för entiteten pipeline-slutpunkt via den användaren kan se schemadefinitionen för slutpunkten för den publicerade pipelinen.
  + **azureml-pipeline-steps**
    + Nytt Kusto-steg och Kusto Compute Target.
  + **azureml-telemetry**
    + Uppdatera beskrivningen av paketet för pypi-översiktssidan.
  + **azureml-train**
    + Uppdatera beskrivningen av paketet för pypi-översiktssidan.
  + **azureml-train-automl-client**
    + Felhantering i get_output fall när lokala versioner av Pandas/sklearn inte matchar de som används under träningen
  + **azureml-train-core**
    + Uppdatera beskrivningen av paketet för pypi-översiktssidan.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK för Python v1.13.0
+ **Förhandsgranskningsfunktioner**
  + **azureml-core** Med den nya funktionen för utdatauppsättningar kan du skriva tillbaka till molnlagring, inklusive Blob, ADLS Gen 1, ADLS Gen 2 och FileShare. Du kan konfigurera var utdata ska matas ut, hur du matar ut data (via montering eller uppladdning), om du vill registrera utdata för framtida återanvändning och delning och skicka mellanliggande data mellan pipelinesteg sömlöst. Detta möjliggör reproducerbarhet, delning, förhindrar duplicering av data och resulterar i kostnadseffektivitet och produktivitetsförbättringar. [Lär dig hur du använder det](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Filen validated_{platform}_requirements.txt har lagts till för att fästa alla pip-beroenden för AutoML.
    + Den här versionen stöder modeller som är större än 4 Gb.
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1) `numpy` (nu 1.18.2).
  + **azureml-automl-runtime**
    + Ange horovod för text DNN att alltid använda fp16-komprimering.
    + Den här versionen stöder modeller som är större än 4 Gb.
    + Ett problem har åtgärdats där AutoML misslyckas med ImportError: det går inte att importera namnet `RollingOriginValidator` .
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1) `numpy` (nu 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1) `numpy` (nu 1.18.2).
  + **azureml-contrib-fairness**
    + Ange en kort beskrivning av azureml-contrib-fairness.
  + **azureml-contrib-pipeline-steps**
    + Ett meddelande som anger att det här paketet är inaktuellt har lagts till och användaren bör använda azureml-pipeline-steps i stället.
  + **azureml-core**
    + Kommandot listnyckel för arbetsytan har lagts till.
    + Lägg till parametern tags i Workspace SDK och CLI.
    + Buggen där en underordnad körning med datauppsättningen skulle skickas misslyckas på grund av `TypeError: can't pickle _thread.RLock objects` .
    + Lägga page_count standard/dokumentation för Modelllista().
    + Ändra CLI&SDK för att ta parametern adbworkspace och Lägg till arbetsyta adb lin/unlink runner.
    + Åtgärda bugg i Dataset.update som gjorde att den senaste datauppsättningsversionen uppdaterades, inte att versionen av datauppsättningsuppdateringen anropades. 
    + Åtgärda bugg i Dataset.get_by_name som skulle visa taggarna för den senaste versionen av datauppsättningen även när en specifik äldre version hämtades.
  + **azureml-interpret**
    + Sannolikhetsutdata har lagts till för att shap-bedömningsförklarare i azureml-interpret baserat shap_values_output parametern från den ursprungliga förklaringen.
  + **azureml-pipeline-core**
    + Förbättrad `PipelineOutputAbstractDataset.register` dokumentation.
  + **azureml-train-automl-client**
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1) `numpy` (nu 1.18.2).
  + **azureml-train-automl-runtime**
    + AutoML-beroenden har uppgraderats: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1) `numpy` (nu 1.18.2).
  + **azureml-train-core**
    + Användarna måste nu ange en giltig hyperparameter_sampling arg när de skapar en HyperDriveConfig. Dessutom har dokumentationen för HyperDriveRunConfig redigerats för att informera användarna om utfasningen av HyperDriveRunConfig.
    + Återställa PyTorch standardversion till 1.4.
    + Lägga till PyTorch 1.6 & Tensorflow 2.2-avbildningar och den curated miljön.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Notebooks-upplevelsen i Studio (augustiuppdatering)
+ **Nya funktioner**
  + Ny landningssida för Komma igång 
  
+ **Förhandsgranskningsfunktioner**
    + Insamlingsfunktionen i Notebooks. Med funktionen [Samla in](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) kan användarna nu enkelt rensa notebook-filer med. Gather använder en automatiserad beroendeanalys av din notebook, vilket säkerställer att den grundläggande koden behålls, men tar bort irrelevanta delar.

+ **Felkorrigeringar och förbättringar**
  + Förbättring av hastighet och tillförlitlighet
  + Buggar i mörkt läge har åtgärdats
  + Korrigerade buggar i utrullningsrullningen
  + Exempelsökning söker nu igenom allt innehåll i alla filer på lagringsplatsen Azure Machine Learning exempel på notebook-filer
  + R-celler med flera linjer kan nu köras
  + "Jag litar på innehållet i den här filen" kontrolleras nu automatiskt efter första gången
  + Förbättrad dialogruta för konfliktlösning med det nya alternativet "Skapa en kopia"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK för Python v1.12.0

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Lägg image_name och image_label till Model.package() för att aktivera namnbyte på den skapade paketavbildningen.
  + **azureml-automl-core**
    + AutoML höja en ny felkod från dataprep när innehållet ändras medan det läses.
  + **azureml-automl-runtime**
    + Aviseringar har lagts till för användaren när data innehåller saknade värden men featurisering är inaktiverat.
    + Åtgärdade underordnade körningsfel när data innehåller och featurisering är inaktiverade.
    + AutoML höja en ny felkod från dataprep när innehållet ändras medan det läses.
    + Normaliseringen för prognosmått har uppdaterats så att den inträffar per kornighet.
    + Förbättrad beräkning av prognoskvantiler när lookback-funktioner är inaktiverade.
    + Åtgärdade hantering av bool-gles matris vid beräkning av förklaringar efter AutoML.
  + **azureml-core**
    + En ny metod `run.get_detailed_status()` visar nu en detaljerad förklaring av aktuell körningsstatus. Den visar för närvarande bara en förklaring av `Queued` status.
    + Lägg image_name och image_label till Model.package() för att aktivera namnbyte för den skapade paketavbildningen.
    + Ny metod `set_pip_requirements()` för att ange hela pip-avsnittet på samma [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) gång.
    + Aktivera registrering av autentiseringsuppgifter utan ADLS Gen2 datalager.
    + Förbättrat felmeddelande vid försök att ladda ned eller montera en felaktig datauppsättningstyp.
    + Uppdatera notebook-exempelfiltreren för tidsseriedatauppsättning med fler exempel partition_timestamp som ger filteroptimering.
    + Ändra sdk och CLI för att acceptera subscriptionId, resourceGroup, workspaceName, peConnectionName som parametrar i stället för ArmResourceId när du tar bort den privata slutpunktsanslutningen.
    + Experimentell decorator visar klassnamn för enklare identifiering.
    + Beskrivningar för tillgångar i modeller genereras inte längre automatiskt baserat på en körning.
  + **azureml-datadrift**
    + Markera create_from_model API i DataDriftDetector som inaktuell.
  + **azureml-dataprep**
    + Förbättrat felmeddelande vid försök att ladda ned eller montera en felaktig datauppsättningstyp.
  + **azureml-pipeline-core**
    + En bugg vid deserialisering av pipelinediagram som innehåller registrerade datamängder har åtgärdats.
  + **azureml-pipeline-steps**
    + RScriptStep stöder RSection från azureml.core.environment.
    + Tog bort passthru_automl_config från det offentliga `AutoMLStep` API:et och konverterade den till en enda intern parameter.
  + **azureml-train-automl-client**
    + Den lokala asynkrona, hanterade miljön körs från AutoML har tagits bort. Alla lokala körningar körs i miljön som körningen startades från.
    + Problem med ögonblicksbilder när AutoML skickas körs utan skript som tillhandahålls av användaren har åtgärdats.
    + Underkörningsfel har åtgärdats när data som innehåller svikt och featurisering är inaktiverade.
  + **azureml-train-automl-runtime**
    + AutoML höja en ny felkod från dataprep när innehållet ändras när det läses.
    + Problem med ögonblicksbilder när AutoML skickas körs utan skript som tillhandahålls av användaren har åtgärdats.
    + Underkörningsfel har åtgärdats när data som innehåller svikt och featurisering är inaktiverade.
  + **azureml-train-core**
    + Stöd har lagts till för att ange pip-alternativ (till exempel --extra-index-url) i den pip-kravfil som skickas till en [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) `pip_requirements_file` through-parameter.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK för Python v1.11.0

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Åtgärda modellramverk och modellramverk som inte skickades i körningsobjekt i CLI-modellregistreringssökvägen
    + Åtgärda CLI amlcompute identity show-kommandot för att visa klientorganisations-ID och huvudnamns-ID 
  + **azureml-train-automl-client**
    + Lade get_best_child () till AutoMLRun för att hämta den bästa underordnade körningen för en AutoML-körning utan att ladda ned den associerade modellen.
    + ModelProxy-objekt har lagts till som gör att förutsägelser eller prognoser kan köras i en fjärrträningsmiljö utan att modellen laddas ned lokalt.
    + Ohanterade undantag i AutoML pekar nu på en HTTP-sida med kända problem, där mer information om felen finns.
  + **azureml-core**
    + Modellnamn kan vara 255 tecken långa.
    + Environment.get_image_details() objekttypen har ändrats. `DockerImageDetails` klass ersatt `dict` , avbildningsinformation är tillgänglig från de nya klassegenskaperna. Ändringarna är bakåtkompatibla.
    + Åtgärda fel för Environment.from_pip_requirements() för att bevara beroendestrukturen
    + Åtgärdat en bugg log_list skulle misslyckas om ett int och double ingick i samma lista.
    + När du aktiverar privat länk på en befintlig arbetsyta bör du observera att om det finns beräkningsmål associerade med arbetsytan, fungerar inte dessa mål om de inte ligger bakom samma virtuella nätverk som arbetsytans privata slutpunkt.
    + Gjort `as_named_input` valfritt när du använder datauppsättningar i experiment och lagt `as_mount` till och i `as_download` `FileDataset` . Indatanamnet genereras automatiskt om `as_mount` eller `as_download` anropas.
  + **azureml-automl-core**
    + Ohanterade undantag i AutoML pekar nu på en HTTP-sida med kända problem, där mer information om felen finns.
    + Lade get_best_child () till AutoMLRun för att hämta den bästa underordnade körningen för en AutoML-körning utan att ladda ned den associerade modellen.
    + ModelProxy-objekt har lagts till som gör att förutsägelser eller prognoser kan köras i en fjärrträningsmiljö utan att modellen laddas ned lokalt.
  + **azureml-pipeline-steps**
    + Flaggor `enable_default_model_output` och har lagts till i `enable_default_metrics_output` `AutoMLStep` . Dessa flaggor kan användas för att aktivera/inaktivera standardutdata.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK för Python v1.10.0

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Om en sökväg skickas till AutoMLConfig-objektet och den inte redan finns när du använder AutoML skapas den automatiskt.
    + Användare kan nu ange en tidsseriefrekvens för prognostiserade uppgifter med hjälp av `freq` parametern .
  + **azureml-automl-runtime**
    + När du använder AutoML skapas en sökväg automatiskt om den skickas till AutoMLConfig-objektet och den inte redan finns.
    + Användare kan nu ange en tidsseriefrekvens för prognostiserade uppgifter med hjälp av `freq` parametern .
    + AutoML-prognosteringen har nu stöd för löpande utvärdering, vilket gäller för användningsfall där längden på en test- eller valideringsuppsättning är längre än indataprognosen, och det kända y_pred värdet används som prognoskontext.
  + **azureml-core**
    + Varningsmeddelanden skrivs ut om inga filer har laddats ned från datalagringen under en körning.
    + Dokumentation för har lagts `skip_validation` till för i `Datastore.register_azure_sql_database method` .
    + Användare måste uppgradera till sdk v1.10.0 eller högre för att skapa en automatiskt godkänd privat slutpunkt. Detta inkluderar notebook-resursen som kan användas bakom det virtuella nätverket.
    + Exponera NotebookInfo som svar på hämta arbetsyta.
    + Ändringar för att få anrop för att lista beräkningsmål och få beräkningsmål lyckas på en fjärrkörning. Sdk-funktioner för att hämta beräkningsmål och lista beräkningsmål för arbetsytan fungerar nu i fjärrkörningar.
    + Lägg till utfasningsmeddelanden i klassbeskrivningarna för azureml.core.image-klasserna.
    + Utlöst undantag och rensa arbetsyta och beroende resurser om det inte går att skapa arbetsytans privata slutpunkt.
    + Stöd för uppgradering av arbetsyte-SKU i metoden för arbetsyteuppdatering.
  + **azureml-datadrift**
    + Uppdatera matplotlib-versionen från 3.0.2 till 3.2.1 för att stödja python 3.8.
  + **azureml-dataprep**
    + Stöd har lagts till för url-datakällor för webben `Range` med eller `Head` begäran. 
    + Förbättrad stabilitet för montering och nedladdning av fildatauppsättningar.
  + **azureml-train-automl-client**
    + Problem som rör borttagning av från `RequirementParseError` installationsverktyg har åtgärdats.
    + Använd docker i stället för conda för lokala körningar som skickas med "compute_target='local'"
    + Iterationens varaktighet som skrivs ut till konsolen har korrigerats. Tidigare skrevs iterationens varaktighet ibland ut som körningens sluttid minus körningens skapandetid. Den har korrigerats för att vara lika med körningssluttid minus körningsstarttid.
    + Om en sökväg skickas till AutoMLConfig-objektet och den inte redan finns när du använder AutoML skapas den automatiskt.
    + Användare kan nu ange en tidsseriefrekvens för prognostiserade uppgifter med hjälp av `freq` parametern .
  + **azureml-train-automl-runtime**
    + Förbättrade konsolutdata när bästa modellförklaringar misslyckas.
    + Indataparametern har bytt namn till "blocked_models" för att ta bort en känslig term.
      + Indataparametern har bytt namn till "allowed_models" för att ta bort en känslig term.
    + Användare kan nu ange en tidsseriefrekvens för prognostiserade uppgifter med hjälp av `freq` parametern .

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK för Python v1.9.0

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Ersatt get_model_path() med AZUREML_MODEL_DIR miljövariabel i autogenererade autogenererade bedömningsskript. Lade även till telemetri för att spåra fel under init().
    + Möjligheten att ange som en `enable_cache` del av AutoMLConfig har tagits bort
    + En bugg där körningar kan misslyckas med tjänstfel under specifika prognoskörningar har åtgärdats
    + Förbättrad felhantering runt specifika modeller under `get_output`
    + Anrop till fitted_model.fit(X, y) för klassificering med y- transformer har åtgärdats
    + Aktiverade anpassad framåtfyllningsimputer för prognostiserade uppgifter
    + En ny ForecastingParameters-klass används i stället för prognostiserade parametrar i ett dict-format
    + Förbättrad automatisk identifiering av målfördröjning
    + Begränsad tillgänglighet har lagts till för multi-noded, multi-gpu-distribuerad funktionalisering medLING
  + **azureml-automl-runtime**
    + Nu modellerar man additiva säsongsvariationer i stället för multiplikativt.
    + Problemet har åtgärdats när kort kornighet, med frekvenser som skiljer sig från dem av de långa kornigheterna, resulterar i misslyckade körningar.
  + **azureml-ib-automl-dnn-vision**
    + Samla in system-/GPU-statistik och logggenomsnitt för träning och bedömning
  + **azureml-ib-manipulation**
    + Stöd har lagts till för flaggan enable-app-insights i ManagedInferencing
  + **azureml-core**
    + En valideringsparameter till dessa API:er genom att tillåta att validering hoppas över när datakällan inte är tillgänglig från den aktuella beräkningen.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + Stöd för ramverksfiltrering har lagts till för modelllista och NCD AutoML-exempel i notebook-fil igen
    + För Datastore.register_azure_blob_container och Datastore.register_azure_file_share (endast alternativ som stöder SAS-token) har vi uppdaterat dokumentsträngarna för fältet med minimikraven för behörigheter för vanliga läs- och `sas_token` skrivscenarier.
    + Inaktuella _with_auth i ws.get_mlflow_tracking_uri()
  + **azureml-mlflow**
    + Lägg till stöd för att distribuera lokala file:// modeller med AzureML-MLflow
    + Inaktuella _with_auth i ws.get_mlflow_tracking_uri()
  + **azureml-opendatasets**
    + Nyligen publicerade spårningsdatauppsättningar för Covid-19 är nu tillgängliga med SDK
  + **azureml-pipeline-core**
    + Logga ut varning när "azureml-defaults" inte ingår som en del av pip-dependency
    + Förbättra återgivningen av anteckningar.
    + Stöd har lagts till för citerade radbrytningar vid parsning av avgränsade filer till PipelineOutputFileDataset.
    + Klassen PipelineDataset är inaktuell. Mer information finns i https://aka.ms/dataset-deprecation. Lär dig hur du använder datauppsättning med pipeline, se https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Dokumentuppdateringar av azureml-pipeline-steps.
    +  Stöd har lagts till i ParallelRunConfig för användare för att definiera miljöer infogade med resten av config eller `load_yaml()` i en separat fil
  + **azureml-train-automl-client**.
    + Möjligheten att ange som en `enable_cache` del av AutoMLConfig har tagits bort
  + **azureml-train-automl-runtime**
    + Begränsad tillgänglighet har lagts till för flernods distribuerad funktionalisering med flera gpu:er med HJÄLP AV.
    + Felhantering har lagts till för inkompatibla paket i ADB-baserade automatiserade maskininlärningskörningar.
  + **azureml-widgets**
    + Dokumentuppdateringar av azureml-widgetar.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK för Python v1.8.0
  
  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-fairness** Paketet `azureml-contrib-fairness` ger integrering mellan rättviseutvärderingen med öppen källkod och paketet [fairlearn](https://fairlearn.github.io) och Azure Machine Learning-studio. I synnerhet gör paketet att instrumentpaneler för utvärdering av modell rättviseutvärdering kan laddas upp som en del av en AzureML-körning och visas i Azure Machine Learning-studio

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Stöd för att hämta loggar för init-containern.
    + Nya CLI-kommandon har lagts till för att hantera ComputeInstance
  + **azureml-automl-core**
    + Användare kan nu aktivera stackens ensemble-iteration för Tidsserieuppgifter med en varning om att den potentiellt kan överanpassas.
    + En ny typ av användarfel har lagts till som utlöses om cachelagringsinnehållet har manipulerats
  + **azureml-automl-runtime**
    + Avsökning av klassbalansering aktiveras inte längre om användaren inaktiverar funktionen.  
  + **azureml-ib-itp**
    + CmAks-beräkningstyp stöds. Du kan koppla ditt eget AKS-kluster till arbetsytan för träningsjobb.
  + **azureml-contrib-notebook**
    + Dokumentförbättringar av paketet azureml-ib-notebook.
  + **azureml-contrib-pipeline-steps**
    + Doc-förbättringar av paketet azureml-ib--pipeline-steps.
  + **azureml-core**
    + Lägg set_connection, get_connection, list_connections och delete_connection funktioner så att kunden kan arbeta med resursen för arbetsyteanslutning
    + Dokumentationsuppdateringar för paketet azureml-coore/azureml.exceptions.
    + Dokumentationsuppdateringar till azureml-core-paketet.
    + Dokumentuppdateringar till ComputeInstance-klassen.
    + Dokumentförbättringar av paketet azureml-core/azureml.core.compute.
    + Dokumentförbättringar för webbtjänstrelaterade klasser i azureml-core.
    + Stöd för användarvalt datalager för att lagra profileringsdata
    + Expandera och skapa page_count för API:et för modelllista har lagts till
    + En bugg har åtgärdats där borttagning av egenskapen overwrite gör att den skickade körningen misslyckas med deserialiseringsfel.
    + En inkonsekvent mappstruktur vid nedladdning eller montering av en FileDataset som refererar till en enda fil har åtgärdats.
    + Inläsningen av en datamängd med parquet-to_spark_dataframe är nu snabbare och stöder alla parquet- och Spark SQL-datatyper.
    + Stöd för att hämta loggar för init-containern.
    + AutoML-körningar markeras nu som underordnade körningar av Parallel Run Step.
  + **azureml-datadrift**
    + Dokumentförbättringar av paketet azureml-ib-notebook.
  + **azureml-dataprep**
    + Inläsningen av en datamängd med parquet-to_spark_dataframe är nu snabbare och stöder alla parquet- och Spark SQL-datatyper.
    + Bättre minneshantering för OutOfMemory-problem för to_pandas_dataframe.
  + **azureml-interpret**
    + Uppgraderad azureml-interpret för att använda interpret-community version 0.12.*
  + **azureml-mlflow**
    + Dokumentförbättringar för azureml-mlflow.
    + Lägger till stöd för AML-modellregister med MLFlow.
  + **azureml-opendatasets**
    + Stöd för Python 3.8 har lagts till
  + **azureml-pipeline-core**
    + Dokumentationen `PipelineDataset` har uppdaterats för att göra det tydligt att det är en intern klass.
    + ParallelRunStep uppdateras för att acceptera flera värden för ett argument, till exempel: "--group_column_names", "Col1", "Col2", "Col3"
    + Kravet passthru_automl_config mellanliggande dataanvändning med AutoMLStep i pipelines har tagits bort.
  + **azureml-pipeline-steps**
    + Dokumentförbättringar av paketet azureml-pipeline-steps.
    + Kravet passthru_automl_config mellanliggande dataanvändning med AutoMLStep i pipelines har tagits bort.
  + **azureml-telemetry**
    + Dokumentförbättringar av azureml-telemetri.
  + **azureml-train-automl-client**
    + En bugg som `experiment.submit()` anropades två gånger på ett `AutoMLConfig` objekt resulterade i ett annat beteende.
    + Användare kan nu aktivera stackens ensemble-iteration för Tidsserieuppgifter med en varning om att den potentiellt kan överanpassas.
    + AutoML-körningsbeteendet har ändrats för att aktivera UserErrorException om tjänsten kastar användarfel
    + Åtgärdar ett fel som gjorde att azureml_automl.log inte genererades eller saknade loggar när ett AutoML-experiment utfördes på ett fjärrbearbetningsmål.
    + För klassificeringsdatauppsättningar med obalanserade klasser tillämpar vi viktbalansering. Om funktionsavsökningsfunktionen fastställer att viktbalansering förbättrar prestandan för klassificeringsuppgiften med ett visst tröskelvärde för underordnade data.
    + AutoML-körningar markeras nu som underordnad körning av Parallel Run Step.
  + **azureml-train-automl-runtime**
    + AutoML-körningsbeteendet har ändrats för att aktivera UserErrorException om tjänsten kastar användarfel
    + AutoML-körningar markeras nu som underordnad körning av Parallel Run Step.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK för Python v1.7.0

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Borttagningen av modellprofilering har slutförts från "fel" genom att rensa CLI-kommandon och paketberoenden. Modellprofilering är tillgängligt i kärnan.
    + Uppgraderar den minsta Azure CLI-versionen till 2.3.0
  + **azureml-automl-core**
    + Bättre undantagsmeddelande om fit_transform() på grund av anpassade transformeringsparametrar.
    + Lägg till stöd för flera språk för modeller för djupinlärningsomvandl, till exempel ML i automatiserad ML.
    + Ta bort inaktuella lag_length parameter från dokumentationen.
    + Dokumentationen om prognosparametrar har förbättrats. Parametern lag_length är inaktuell.
  + **azureml-automl-runtime**
    + Åtgärdat det fel som uppstod när en av kategorikolumnerna var tom i prognos/testtid.
    + Åtgärda körningsfel som inträffar när lookback-funktionerna är aktiverade och data innehåller kort kornighet.
    + Åtgärdat problemet med felmeddelandet för duplicerat tidsindex när fördröjningar eller rullande fönster var inställda på "auto".
    + Åtgärdat problemet med Models- och Arima-modeller på datauppsättningar som innehåller lookback-funktioner.
    + Stöd har lagts till för datum före 1677-09-21 eller efter 2262-04-11 i kolumner, förutom datum/tid i prognosuppgifterna. Förbättrade felmeddelanden.
    + Dokumentationen om prognosparametrar har förbättrats. Parametern lag_length är inaktuell.
    + Bättre undantagsmeddelande om fit_transform() på grund av anpassade transformeringsparametrar.
    + Lägg till stöd för flera språk för modeller för djupinlärningsomvandl, till exempel ML i automatiserad ML.
    + Cacheåtgärder som resulterar i vissa OSErrors ger upphov till användarfel.
    + Kontroller har lagts till för att säkerställa att tränings- och valideringsdata har samma antal och uppsättning kolumner
    + Problem med autogenererat AutoML-bedömningsskript har åtgärdats när data innehåller citattecken
    + Aktivera förklaringar för AutoML-dräkter och ensemblemodeller som innehåller en uppspelad modell.
    + Ett nytt kundproblem visade en bugg på en live-webbplats där vi loggar meddelanden längs klassbalanseringsavsökning även om logiken för klassbalansering inte är korrekt aktiverad. Ta bort dessa loggar/meddelanden med denna PR.
  + **azureml-cli-common**
    + Borttagningen av modellprofilering har slutförts genom att CLI-kommandon och paketberoenden rensas. Modellprofilering är tillgängligt i kärnan.
  + **azureml-contrib-reinforcementlearning**
    + Belastningstestverktyg
  + **azureml-core**
    + Dokumentationsändringar i Script_run_config.py
    + Åtgärdar en bugg med att skriva utdata från CLI för att köra submit-pipeline
    + Dokumentationsförbättringar för azureml-core/azureml.data
    + Åtgärdar problem med att hämta lagringskonto med hdfs getconf-kommandot
    + Förbättrad register_azure_blob_container och register_azure_file_share dokumentation
  + **azureml-datadrift**
    + Förbättrad implementering för inaktivering och aktivering av övervakare av datamängdsavdrift
  + **azureml-interpret**
    + Som förklaring av klienten tar du bort NaNs eller Infs före json-serialisering vid uppladdning från artefakter
    + Uppdatera till den senaste versionen av interpret-community för att förbättra minnesfel för globala förklaringar med många funktioner och klasser
    + Lägg true_ys valfri parameter för förklaringsöverföring för att aktivera ytterligare funktioner i studiogränssnittet
    + Förbättra download_model_explanations() och list_model_explanations() prestanda
    + Små justeringar av notebook-datorer för att underlätta felsökning
  + **azureml-opendatasets**
    + azureml-opendatasets behöver azureml-dataprep version 1.4.0 eller senare. En varning har lagts till om en lägre version har identifierats
  + **azureml-pipeline-core**
    + Den här ändringen gör att användaren kan ange en valfri runconfig till moduleVersion när modulen anropas. Publish_python_script.
    + Aktivera nodkonto kan vara en pipelineparameter i ParallelRunStep i azureml.pipeline.steps
  + **azureml-pipeline-steps**
    + Den här ändringen gör att användaren kan ange en valfri runconfig till moduleVersion när modulen anropas. Publish_python_script.
  + **azureml-train-automl-client**
    + Lägg till stöd för flera språk för modeller för djupinlärningsomvandl, till exempel ML i automatiserad ML.
    + Ta bort inaktuella lag_length parameter från dokumentationen.
    + Dokumentationen om prognosparametrar har förbättrats. Parametern lag_length är inaktuell.
  + **azureml-train-automl-runtime**
    + Aktivera förklaringar för AutoML-dräkter och ensemblemodeller som innehåller en modell av typ 1.
    + Dokumentationsuppdateringar för azureml-train-automl-* paket.
  + **azureml-train-core**
    + Stöd för TensorFlow version 2.1 i PyTorch-beräknaren
    + Förbättringar av paketet azureml-train-core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK för Python v1.6.0

+ **Nya funktioner**
  + **azureml-automl-runtime**
    + AutoML-prognostiserade nu har stöd för kundernas prognoser utöver den föringivna max enligt sperioden utan omträning av modellen. När prognosmålet är längre in i framtiden än den angivna maximala horisonten kommer funktionen forecast() fortfarande att göra punktförutsägelser fram till det senare datumet med hjälp av ett rekursivt åtgärdsläge. Bilden av den nya funktionen finns i avsnittet "Prognostiserade längre än den maximala horisonten" i anteckningsboken "forecasting-forecast-function" i [mappen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)."
  
  + **azureml-pipeline-steps**
    + ParallelRunStep släpps nu och är en del av **paketet azureml-pipeline-steps.** Befintliga ParallelRunStep i **paketet azureml-ib-pipeline-steps** är inaktuella. Ändringar från den offentliga förhandsversionen:
      + En valfri konfigurerbar parameter har lagts till för att styra högsta anrop till körningsmetod för en `run_max_try` viss batch. Standardvärdet är 3.
      + Inga PipelineParameters har genererats automatiskt längre. Följande konfigurerbara värden kan anges som PipelineParameter explicit.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Standardvärdet för process_count_per_node ändras till 1. Användaren bör finjustera det här värdet för bättre prestanda. Bästa praxis är att ange som antalet GPU- eller CPU-noder.
      + ParallelRunStep matar inte in några paket. Användaren måste inkludera **paketen azureml-core** **och azureml-dataprep[pandas, fuse]** i miljödefinitionen. Om anpassad Docker-avbildning används user_managed_dependencies måste användaren installera conda på avbildningen.
      
+ **Icke-bakåtkompatibla ändringar**
  + **azureml-pipeline-steps**
    + Användningen av azureml.dprep.Dataflow är inaktuell som giltig typ av indata för AutoMLConfig
  + **azureml-train-automl-client**
    + Användningen av azureml.dprep.Dataflow är inaktuell som giltig typ av indata för AutoMLConfig

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Buggen där en varning kan skrivas ut under den `get_output` uppmanade användaren att nedgradera klienten har åtgärdats.
    + Mac har uppdaterats så att den förlitar sig på cudatoolkit=9.0 eftersom den inte är tillgänglig i version 10 ännu.
    + Ta bort begränsningar för fjärr- och xgboost-modeller när de tränas med fjärrbearbetning.
    + Förbättrad loggning i AutoML
    + Felhanteringen för anpassad funktionering i prognosuppgifter har förbättrats.
    + Funktioner har lagts till för att tillåta användare att inkludera fördröjningsfunktioner för att generera prognoser.
    + Uppdateringar av felmeddelande för korrekt visning av användarfel.
    + Stöd för cv_split_column_names som ska användas med training_data
    + Uppdatera loggning av undantagsmeddelandet och traceback.
  + **azureml-automl-runtime**
    + Aktivera skyddsräcken för prognostiserade imputering av saknade värden.
    + Förbättrad loggning i AutoML
    + Lade till mer information om felhantering för undantag vid dataförberedelser
    + Ta bort begränsningar för phrophet- och xgboost-modeller när de tränas med fjärrbearbetning.
    + `azureml-train-automl-runtime` och `azureml-automl-runtime` har uppdaterade beroenden för , och `pytorch` `scipy` `cudatoolkit` . vi stöder nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` och `cudatoolkit==10.1.243` .
    + Felhanteringen för anpassad funktionering i prognosuppgifter har förbättrats.
    + Mekanismen för identifiering av prognosdatauppsättningsfrekvens har förbättrats.
    + Ett problem med modellträning för modellen har åtgärdats på vissa datauppsättningar.
    + Den automatiska identifieringen av maximal horisont under prognostiserades förbättrades.
    + Funktioner har lagts till för att tillåta användare att inkludera fördröjningsfunktioner för att generera prognoser.
    +  Lägger till funktioner i prognosfunktionen för att göra det möjligt att tillhandahålla prognoser utöver den tränade horisonten utan att träna om prognosmodellen.
    + Stöd för cv_split_column_names som ska användas med training_data
  + **azureml-contrib-automl-dnn-forecasting**
    + Förbättrad loggning i AutoML
  + **azureml-contrib-manipulation**
    + Stöd har lagts till för Windows-tjänster i ManagedInferencing
    + Ta bort gamla WORKFLOW-arbetsflöden, till exempel attachERING-beräkning, SingleModelMirWebservice-klass – Rensa ut modellprofilering som placerats iib-package
  + **azureml-contrib-pipeline-steps**
    + Mindre korrigering för YAML-stöd
    + ParallelRunStep släpps till allmän tillgänglighet – azureml.contrib.pipeline.steps har ett meddelande om utfasning och flyttas till azureml.pipeline.steps
  + **azureml-contrib-reinforcementlearning**
    + Belastningstestverktyget för RL
    + RL-beräknaren har smarta standardvärden
  + **azureml-core**
    + Ta bort gamla WORKFLOW-arbetsflöden, till exempel attach MANIPULATION-beräkning, SingleModelMirWebservice-klass – Rensa bort modellprofilering som placerats iib-package
    + Informationen som angavs för användaren vid profileringsfel har åtgärdats: inkluderade begärande-ID och omformulerade meddelandet så att det var mer meningsfullt. Nytt profileringsarbetsflöde har lagts till för profileringsprofileringsprofiler
    + Förbättrad feltext vid körningsfel för datauppsättningen.
    + CLI-stöd för privat länk för arbetsyta har lagts till.
    + En valfri parameter har `invalid_lines` lagts till som gör det möjligt att ange hur rader som innehåller ogiltig `Dataset.Tabular.from_json_lines_files` JSON ska anges.
    + Vi kommer att ta slut på den körningsbaserade skapandet av beräkning i nästa version. Vi rekommenderar att du skapar ett faktiskt Amlcompute-kluster som ett beständigt beräkningsmål och använder klusternamnet som beräkningsmål i körningskonfigurationen. Se exempelanteckningsboken här: aka.ms/amlcomputenb
    + Förbättrade felmeddelanden vid körningsfel för datauppsättningen.
  + **azureml-dataprep**
    + Gjorde en varning om att uppgradera pyarrow-versionen mer explicit.
    + Förbättrad felhantering och meddelande som returneras vid fel vid körning av dataflöde.
  + **azureml-interpret**
    + Dokumentationsuppdateringar för azureml-interpret-paketet.
    + Paket och anteckningsböcker för tolkning har åtgärdats för att vara kompatibla med den senaste sklearn-uppdateringen
  + **azureml-opendatasets**
    + returnera Ingen när inga data returneras.
    + Förbättra prestanda för to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Snabbkorrigering för ParallelRunStep där inläsningen från YAML bröts
    + ParallelRunStep släpps till allmän tillgänglighet – azureml.ib.pipeline.steps har ett utfasningsmeddelande och flyttas till azureml.pipeline.steps – nya funktioner är: 1. Datauppsättningar som PipelineParameter 2. Ny parameter run_max_retry 3. Konfigurerbar append_row namnet på utdatafilen
  + **azureml-pipeline-steps**
    + Inaktuellt azureml.dprep.Dataflow som en giltig typ för indata.
    + Snabbkorrigering för ParallelRunStep där inläsningen från YAML bröts
    + ParallelRunStep släpps till allmän tillgänglighet – azureml.ib.pipeline.steps har ett utfasningsmeddelande och flyttas till azureml.pipeline.steps – nya funktioner omfattar:
      + Datauppsättningar som PipelineParameter
      + Ny parameter run_max_retry
      + Konfigurerbar append_row namnet på utdatafilen
  + **azureml-telemetry**
    + Uppdatera loggning av undantagsmeddelandet och traceback.
  + **azureml-train-automl-client**
    + Förbättrad loggning i AutoML
    + Uppdateringar av felmeddelande för korrekt visning av användarfel.
    + Stöd för cv_split_column_names som ska användas med training_data
    + Inaktuell azureml.dprep.Dataflow som en giltig typ för indata.
    + Mac har uppdaterats så att den förlitar sig på cudatoolkit=9.0 eftersom den inte är tillgänglig i version 10 ännu.
    + Ta bort begränsningar för phrophet- och xgboost-modeller när de tränas på fjärrbearbetning.
    + `azureml-train-automl-runtime` och `azureml-automl-runtime` har uppdaterade beroenden för , och `pytorch` `scipy` `cudatoolkit` . vi stöder nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` och `cudatoolkit==10.1.243` .
    + Funktioner har lagts till för att tillåta användare att inkludera fördröjningsfunktioner för att generera prognoser.
  + **azureml-train-automl-runtime**
    + Förbättrad loggning i AutoML
    + Lade till mer information om felhantering för undantag vid dataförberedelser
    + Ta bort begränsningar för phrophet- och xgboost-modeller när de tränas på fjärrbearbetning.
    + `azureml-train-automl-runtime` och `azureml-automl-runtime` har uppdaterade beroenden för , och `pytorch` `scipy` `cudatoolkit` . vi stöder nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` och `cudatoolkit==10.1.243` .
    + Uppdateringar av felmeddelande för korrekt visning av användarfel.
    + Stöd för cv_split_column_names som ska användas med training_data
  + **azureml-train-core**
    + Lade till en ny uppsättning HyperDrive-specifika undantag. azureml.train.hyperdrive kommer nu att skapa detaljerade undantag.
  + **azureml-widgets**
    + AzureML-widgetar visas inte i JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK för Python v1.5.0

+ **Nya funktioner**
  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning släpper förhandsversionsstöd för förstärkt inlärning med hjälp av [Ray-ramverket.](https://ray.io) möjliggör `ReinforcementLearningEstimator` träning av förstärkta inlärningsagenter över GPU- och CPU-beräkningsmål i Azure Machine Learning.

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Åtgärdar en av misstag lämnad varningslogg i min tidigare PR. Loggen användes för felsökning och lämnades av misstag kvar.
    + Felkorrigering: informera klienter om partiella fel under profilering
  + **azureml-automl-core**
    + Snabba upp modellen Förtids-/AutoArima-modellen i AutoML-prognostiserade data genom att aktivera parallell anpassning för tidsserierna när datauppsättningar har flera tidsserier. Om du vill dra nytta av den här nya funktionen rekommenderar vi att du anger "max_cores_per_iteration = -1" (d.v.s. med alla tillgängliga cpu-kärnor) i AutoMLConfig.
    + Åtgärda KeyError vid utskrift av skyddsräcken i konsolgränssnittet
    + Felmeddelande för experimentation_timeout_hours har åtgärdats
    + Inaktuella Tensorflow-modeller för AutoML.
  + **azureml-automl-runtime**
    + Felmeddelande för experimentation_timeout_hours har åtgärdats
    + Ett oklassificerat undantag vid försök att deserialisera från cachelagring har åtgärdats
    + Snabba upp modellen Förtids-/AutoArima-modellen i AutoML-prognostiserade data genom att aktivera parallell anpassning för tidsserierna när datauppsättningar har flera tidsserier.
    + Prognostiserade med aktiverat rullande fönster i datauppsättningarna där test-/förutsägelseuppsättningen inte innehåller något av kornigheterna från träningsuppsättningen.
    + Förbättrad hantering av saknade data
    + Problem med förutsägelseintervall vid prognostiserade datauppsättningar som innehåller tidsserier som inte justeras i tid har åtgärdats.
    + Bättre validering av dataformen har lagts till för prognostiserade uppgifter.
    + Förbättrad frekvensidentifiering.
    + Ett bättre felmeddelande visas om korsvalideringsdecket för prognostiserade uppgifter inte kan genereras.
    + Åtgärda konsolgränssnittet för att skriva ut saknade värdeskyddsräcken på rätt sätt.
    + Framtvinga kontroller av cv_split_indices indata i AutoMLConfig.
  + **azureml-cli-common**
    + Felkorrigering: Informera klienter om partiella fel under profileringen
  + **azureml-contrib-manipulation**
    + Lägger till klassen azureml.contrib.manipulation.RevisionStatus som vidarebefordrar information om den aktuella distribuerade REVISIONS-revisionen och den senaste versionen som anges av användaren. Den här klassen ingår i ObjektetWebservice under attributet "deployment_status".
    + Aktiverar uppdatering för webbtjänster av typenWebbtjänst och dess underordnade klass SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Stöd har lagts till för Ray 0.8.3
    + AmlWindowsCompute stöder endast Azure Files som monterad lagring
    + Har bytt namn health_check_timeout till health_check_timeout_seconds
    + Åtgärdade några klass-/metodbeskrivningar.
  + **azureml-core**
    + Aktiverade WASB -> Blob-konverteringar i Azure Government- och Kina-moln.
    + Åtgärdar bugg som gör att läsarroller kan använda CLI-kommandon för az ml run för att hämta körningsinformation
    + Onödig loggning vid Azure ML-fjärrkörningar med indatauppsättningar har tagits bort.
    + RCranPackage stöder nu parametern "version" för CRAN-paketversionen.
    + Felkorrigering: Informera klienter om partiella fel under profileringen
    + Flyttalshantering i europaformat har lagts till för azureml-core.
    + Aktiverade funktioner för privat länk för arbetsyta i Azure ml sdk.
    + När du skapar en TabularDataset med kan du ange om tomma värden ska läsas in som Ingen eller som tom sträng genom att ange `from_delimited_files` det booleska argumentet `empty_as_string` .
    + Flyttalshantering i europaformat har lagts till för datauppsättningar.
    + Förbättrade felmeddelanden om monteringsfel för datauppsättningar.
  + **azureml-datadrift**
    + Data Drift-resultatfråga från SDK hade en bugg som inte särskiljde de minsta, högsta och genomsnittliga funktionsmåtten, vilket resulterade i dubblettvärden. Vi har åtgärdat felet genom att lägga till måttnamnen som prefix på mål eller baslinje. Före: duplicera min, max, medelvärde. Efter: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Förbättra hanteringen av skrivningsbegränsade Python-miljöer när du säkerställer att .NET-beroenden krävs för dataleverans.
    + Skapande av dataflöde på fil med inledande tomma poster har åtgärdats.
    + Alternativ för felhantering för liknande `to_partition_iterator` har lagts `to_pandas_dataframe` till.
  + **azureml-interpret**
    + Minskade längdgränser för förklaringssökvägen för att minska sannolikheten för att gå över Windows-gränsen
    + Felkorrigering för glesa förklaringar som skapats med imiteraren med hjälp av en linjär surrogatmodell.
  + **azureml-opendatasets**
    + Åtgärda problemet med MNIST:s kolumner parsas som sträng, som ska vara int.
  + **azureml-pipeline-core**
    + Gör att du kan regenerate_outputs när du använder en modul som är inbäddad i en ModuleStep.
  + **azureml-train-automl-client**
    + Inaktuella Tensorflow-modeller för AutoML.
    + Åtgärda att användare tillåter att algoritmer som inte stöds visas i lokalt läge
    + Dokumentkorrigeringar av AutoMLConfig.
    + Framtvinga datatypskontroller cv_split_indices indata i AutoMLConfig.
    + Problem med autoML-körningar misslyckades i show_output
  + **azureml-train-automl-runtime**
    + Åtgärda en bugg i Ensemble-iterationer som förhindrade att tidsgränsen för modellnedladdning lyckades.
  + **azureml-train-core**
    + Korrigera stavfel i klassen azureml.train.dnn.Korrigeringl.
    + Stöd för PyTorch version 1.5 i PyTorch-beräkning
    + Åtgärda problemet med att ramverksavbildningen inte kan hämtas i Azure Government när du använder beräkningsramverk för träning

  
## <a name="2020-05-04"></a>2020-05-04
**Ny notebook-upplevelse**

Nu kan du skapa, redigera och dela notebook-filer för maskininlärning och filer direkt i studiowebbupplevelsen för Azure Machine Learning. Du kan använda alla klasser och metoder som är tillgängliga [i Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) inifrån dessa notebook-filer Kom igång [här](./how-to-run-jupyter-notebooks.md)

**Nya funktioner introducerades:**

+ Förbättrad redigerare (Monaco-redigeraren) som används av VS Code 
+ Förbättringar av användargränssnitt/UX
+ Cellverktygsfält
+ Nytt notebook-verktygsfält och beräkningskontroller
+ Statusfält för notebook-dator 
+ Infogade kernelväxlingar
+ R-stöd
+ Förbättringar av tillgänglighet och lokalisering
+ Kommandopalett
+ Ytterligare kortkommandon
+ Autospara
+ Förbättrad prestanda och tillförlitlighet

Få åtkomst till följande webbaserade redigeringsverktyg från studio:
    
| Webbaserat verktyg  |     Description  |
|---|---|
| Azure ML Studio Notebooks   |     Den första redigeringen i klassen för notebook-filer och stöder all åtgärd som är tillgänglig i Azure ML Python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK för Python v1.4.0

+ **Nya funktioner**
  + AmlCompute-kluster har nu stöd för att konfigurera en hanterad identitet i klustret vid tidpunkten för etableringen. Ange bara om du vill använda en system tilldelad identitet eller en användar tilldelad identitet och skicka ett identityId för den senare. Du kan sedan konfigurera behörigheter för åtkomst till olika resurser som Storage eller ACR på ett sätt som beräkningsidentiteten används för att få säker åtkomst till data, i stället för en tokenbaserad metod som AmlCompute använder idag. Mer information om parametrarna finns i vår SDK-referens.
  

+ **Icke-bakåtkompatibla ändringar**
  + AmlCompute-kluster hade stöd för en förhandsgranskningsfunktion kring körningsbaserad generering, som vi planerar att bli inaktuella om två veckor. Du kan fortsätta att skapa beständiga beräkningsmål som alltid genom att använda klassen Amlcompute, men den specifika metoden för att ange identifieraren "amlcompute" som beräkningsmål i körningskonfigurationen stöds inte inom en snar framtid. 

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Aktivera stöd för oskadlig typ vid beräkning av antalet unika värden i en kolumn.
  + **azureml-core**
    + Förbättrad stabilitet vid läsning från Azure Blob Storage med hjälp av en TabularDataset.
    + Förbättrad dokumentation för `grant_workspace_msi` parametern för `Datastore.register_azure_blob_store` .
    + Bugg med har `datastore.upload` åtgärdats för att stödja `src_dir` argumentet som slutar med en eller `/` `\` .
    + Ett åtgärdsbart felmeddelande har lagts till när du försöker ladda upp Azure Blob Storage ett datalager som inte har en åtkomstnyckel eller SAS-token.
  + **azureml-interpret**
    + En övre gräns har lagts till för filstorlek för visualiseringsdata på uppladdade förklaringar.
  + **azureml-train-automl-client**
    + Kontrollera uttryckligen label_column_name & weight_column_name parametrar för AutoMLConfig ska vara av typen sträng.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep stöder nu datauppsättning som pipelineparameter. Användaren kan konstruera en pipeline med exempeldatauppsättning och ändra indatauppsättning av samma typ (fil eller tabell) för ny pipelinekörning.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK för Python v1.3.0

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Ytterligare telemetri kring åtgärder efter träning har lagts till.
    + Påskyndar automatisk ARIMA-träning genom att använda villkorsstyrd summa av kvadratträning (CSS) för serier med en längd som är längre än 100. Längden som används lagras som konstanten ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/i klassen TimeSeriesInternal på /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + Användarloggningen för prognoskörningar har förbättrats. Nu visas mer information om vilken fas som körs i loggen
    + Tillåts target_rolling_window_size anges till värden som är mindre än 2
  + **azureml-automl-runtime**
    + Förbättrade felmeddelandet som visas när duplicerade tidsstämplar hittas.
    + Tillåts target_rolling_window_size anges till värden som är mindre än 2.
    + Åtgärdade fördröjningsimputeringsfelet. Problemet orsakades av det otillräckliga antalet observationer som krävs för att säsongsberoende delar upp en serie. De "säsongsberoende" data används för att beräkna en partiell autokorrelationsfunktion (PACF) för att fastställa fördröjningslängden.
    + Funktionaliseringsanpassning för kolumnsyfte har aktiverats för prognostiseringsuppgifter genom funktionaliseringskonfiguration. Numeriska och kategoriska som kolumnsyfte för prognostiserade uppgifter stöds nu.
    + Aktiverad anpassning av listkolumn med anpassning av prognostiseringsuppgifter genom konfiguration av funktioner.
    + Imputeringsanpassning har aktiverats för prognostiseringsuppgifter genom konfiguration av funktioner. Konstant värdeimputering för målkolumn och medelvärde, medianvärde, most_frequent och konstant värdeimputering för träningsdata stöds nu.
  + **azureml-contrib-pipeline-steps**
    + Acceptera strängbearbetningsnamn som ska skickas till ParallelRunConfig
  + **azureml-core**
    +  API:et Environment.clone(new_name) har lagts till för att skapa en kopia av miljöobjektet
    +  Environment.docker.base_dockerfile accepterar filepath. Om det går att matcha en fil läses innehållet in i base_dockerfile miljöegenskap
    + Automatiskt återställa ömsesidigt uteslutande värden för base_image och base_dockerfile när användaren anger ett värde manuellt i Environment.docker
    + Lade user_managed flagga i RSection som anger om miljön hanteras av användaren eller av AzureML.
    + Datauppsättning: Nedladdningsfel för datauppsättning har åtgärdats om datasökvägen innehåller Unicode-tecken.
    + Datauppsättning: Förbättrad cachelagringsmekanism för montering av datamängd för att respektera minimikraven för diskutrymme i Azure Machine Learning Compute, vilket gör att noden inte kan användas och gör att jobbet avbryts.
    + Datauppsättning: Vi lägger till ett index för tidsseriekolumnen när du kommer åt en tidsseriedatamängd som en Pandas-dataram, som används för att påskynda åtkomsten till tidsseriebaserad dataåtkomst.  Tidigare fick indexet samma namn som tidsstämpelkolumnen, vilket förvirrar användare om vilket som är den faktiska tidsstämpelkolumnen och vilket är indexet. Nu ger vi inte något specifikt namn till indexet eftersom det inte ska användas som en kolumn. 
    + Datauppsättning: Problem med datauppsättningsautentisering i nationella moln har åtgärdats.
    + Datauppsättning: `Dataset.to_spark_dataframe` Ett fel har åtgärdats för datauppsättningar som skapats från Azure PostgreSQL-datalager.
  + **azureml-interpret**
    + Globala poäng har lagts till i visualiseringen om lokala prioritetsvärden är glesa
    + Azureml-interpret har uppdaterats för att använda interpret-community 0.9.*
    + Problem med nedladdningsförklaring som hade glesa utvärderingsdata har åtgärdats
    + Stöd har lagts till för glesa format för förklaringsobjektet i AutoML
  + **azureml-pipeline-core**
    + Stöd för ComputeInstance som beräkningsmål i pipelines
  + **azureml-train-automl-client**
    + Ytterligare telemetri kring åtgärder efter träning har lagts till.
    + Regressionen vid tidigt stopp har åtgärdats
    + Inaktuellt azureml.dprep.Dataflow som en giltig typ för indata.
    +  Ändring av standardtid för AutoML-experiment till sex dagar.
  + **azureml-train-automl-runtime**
    + Ytterligare telemetri kring åtgärder efter träning har lagts till.
    + lade till stöd för gles AutoML från start till slut
  + **azureml-opendatasets**
    + Ytterligare telemetri har lagts till för tjänstövervakaren.
    + Aktivera ytterdörren för blob för att öka stabiliteten 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK för Python v1.2.0

+ **Icke-bakåtkompatibla ändringar**
  + Stöd för Python 2.7 har sjunker

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Lägger till "--subscription-id" i `az ml model/computetarget/service` kommandon i CLI
    + Lägga till stöd för att skicka kund hanterad nyckel (CMK) vault_url, key_name och key_version för ACI-distribution
  + **azureml-automl-core** 
    + Har aktiverat anpassad imputering med konstant värde för både X- och y-dataprognosuppgifter.
    + Problemet i med att visa felmeddelanden för användaren har åtgärdats.    
  + **azureml-automl-runtime**
    + Åtgärdat problemet med prognostiserade datamängder, som innehåller korn med endast en rad
    + Minskat mängden minne som krävs av prognostiserade uppgifter.
    + Bättre felmeddelanden har lagts till om tidskolumnen har felaktigt format.
    + Har aktiverat anpassad imputering med konstant värde för både X- och y-dataprognosuppgifter.
  + **azureml-core**
    + Stöd har lagts till för inläsning av ServicePrincipal från miljövariabler: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID och AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + En ny parameter har introducerats som : Som standard ( ), tolkas alla radbrytningar, inklusive de i värden inom `support_multi_line` `Dataset.Tabular.from_delimited_files` `support_multi_line=False` citattecken, som en postbrytning. Läsning av data på det här sättet är snabbare och mer optimerad för parallell körning på flera CPU-kärnor. Det kan dock resultera i tyst produktion av fler poster med feljusterade fältvärden. Detta ska anges till `True` när de avgränsade filerna är kända för att innehålla citerade radbrytningar.
    + Möjligheten att registrera en ADLS Gen2 i Azure Machine Learning CLI har lagts till
    + Parametern "fine_grain_timestamp" har bytt namn till "timestamp" och parametern "coarse_grain_timestamp" till "partition_timestamp" för metoden with_timestamp_columns() i TabularDataset för att bättre återspegla användningen av parametrarna.
    + Ökad maxlängd för experimentnamn till 255.
  + **azureml-interpret**
    + Azureml-interpret har uppdaterats för interpret-community 0.7.*
  + **azureml-sdk**
    + Ändra till beroenden med kompatibel version Tilde för stöd av korrigering i tidigare versioner och stabila versioner.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK för Python v1.1.5

+ **Utfasning av funktioner**
  + **Python 2.7**
    + Senaste version som stöder Python 2.7

+ **Icke-bakåtkompatibla ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1.1 använder Azure ML Python SDK semantisk versionshantering 2.0.0. [Läs mer här.](https://semver.org/) Alla efterföljande versioner följer det nya numreringsschemat och det semantiska versionskontraktet. 

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Ändra cli-kommandonamnet för slutpunkten från "az ml endpoint aks" till "az ml endpoint real time" för konsekvens.
    + uppdatera INSTALLATIONSanvisningar för CLI för stabil och experimentell gren CLI
    + Profileringen av en instans har åtgärdats för att skapa en rekommendation och gjordes tillgänglig i Core SDK.
  + **azureml-automl-core**
    + Aktiverade batchlägesferensen (tar flera rader en gång) för AutoML ONNX-modeller
    + Förbättrad identifiering av frekvensen för datauppsättningar, saknade data eller innehåller oregelbundna datapunkter
    + Möjligheten att ta bort datapunkter som inte uppfyller den dominerande frekvensen har lagts till.
    + Indata för konstruktorn har ändrats så att en lista med alternativ används för att tillämpa imputeringsalternativen för motsvarande kolumner.
    + Felloggningen har förbättrats.
  + **azureml-automl-runtime**
    + Problemet med felet som uppstod om kornigheten inte fanns i träningsuppsättningen visades i testuppsättningen har åtgärdats
    + Kravet på y_query vid bedömning av prognostjänsten har tagits bort
    + Problemet med prognostiserade när datauppsättningen innehåller korta korn med långa tidsluckor har åtgärdats.
    + Problemet har åtgärdats när den automatiska max-stapeln är aktiverad och datumkolumnen innehåller datum i form av strängar. Korrekt konvertering och felmeddelanden har lagts till för när konvertering till datum inte är möjlig
    + Använda inbyggda NumPy och SciPy för serialisering och deserialisering av mellanliggande data för FileCacheStore (används för lokala AutoML-körningar)
    + En bugg där misslyckade underordnade körningar kan fastna i körningstillstånd har åtgärdats.
    + Ökad hastighet för featurisering.
    + Frekvenskontrollen har åtgärdats under bedömning, nu kräver prognosuppgifterna inte strikt överensstämmelsefrekvens mellan träna och testuppsättningen.
    + Indata för konstruktorn har ändrats så att en lista med alternativ används för att tillämpa imputeringsalternativen för motsvarande kolumner.
    + Fel som rör valet av fördröjningstyp har åtgärdats.
    + Åtgärdat det oklassificerade fel som upphöjts på datauppsättningarna, med kornighet med den enskilda raden
    + Problemet med långsam frekvensidentifiering har åtgärdats.
    + Åtgärdar en bugg i AutoML-undantagshantering som gjorde att den verkliga orsaken till träningsfelet ersattes av ett AttributeError.
  + **azureml-cli-common**
    + Profileringen för en enskild instans har åtgärdats för att skapa en rekommendation och har gjorts tillgänglig i Core SDK.
  + **azureml-ib-manipulation**
    + Lägger till funktioner i klassen WebWebservice för att hämta åtkomsttoken
    + Använd tokenauth för WebWebservice som standard vid AnropetWebservice.run() – Uppdatera endast om anropet misslyckas
    + Distribution av WebService kräver nu rätt SKU:er [Standard_DS2_v2, Standard_F16, Standard_A2_v2] i stället för [Ds2v2, A2v2 respektive F16].
  + **azureml-contrib-pipeline-steps**
    + Valfria parametervärden side_inputs till i ParallelRunStep. Den här parametern kan användas för att montera mappen på containern. Typer som stöds för närvarande är DataReference och PipelineData.
    + Parametrar som skickas i ParallelRunConfig kan skrivas över genom att skicka pipelineparametrar nu. Nya pipelineparametrar som stöds aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count och aml_process_count_per_node ingår redan i en tidigare version).
  + **azureml-core**
    + Distribuerade AzureML-webbtjänster använder nu loggning som `INFO` standard. Detta kan styras genom att ange `AZUREML_LOG_LEVEL` miljövariabeln i den distribuerade tjänsten.
    + Python sdk använder identifieringstjänsten för att använda api-slutpunkten i stället för "pipelines".
    + Växla till de nya vägarna i alla SDK-anrop.
    + Ändrade routning av anrop till ModelManagementService till en ny enhetlig struktur.
      + Gjorde metoden för arbetsyteuppdatering offentligt tillgänglig.
      + Parametern image_build_compute i metoden för arbetsyteuppdatering så att användaren kan uppdatera beräkningen för avbildningsbygget.
    + Utfasningsmeddelanden har lagts till i det gamla profileringsarbetsflödet. Profilering av processor- och minnesgränser har åtgärdats.
    + RSection har lagts till som en del av Miljön för att köra R-jobb.
    + Verifiering har lagts `Dataset.mount` till för för att skapa fel när datauppsättningens källa inte är tillgänglig eller inte innehåller några data.
    + Har lagts till som ytterligare en parameter för Datastore CLI för registrering av Azure Blob-container som gör att du kan registrera `--grant-workspace-msi-access` blobcontainer som finns bakom ett VNet.
    + Profileringen för en enskild instans har åtgärdats för att skapa en rekommendation och har gjorts tillgänglig i Core SDK.
    + Åtgärdat problemet i aks.py _deploy.
    + Verifierar integriteten för modeller som laddas upp för att undvika tyst lagringsfel.
    + Användaren kan nu ange ett värde för autentiseringsnyckeln när nycklar återskapas för webbtjänster.
    + En bugg där versaler inte kan användas som datauppsättningens indatanamn har åtgärdats.
  + **azureml-defaults**
    + `azureml-dataprep` installeras nu som en del av `azureml-defaults` . Det krävs inte längre att installera dataförberedelser [fuse] manuellt på beräkningsmål för att montera datauppsättningar.
  + **azureml-interpret**
    + Azureml-interpret har uppdaterats för interpret-community 0.6.*
    + Azureml-interpret har uppdaterats för att vara beroende av interpret-community 0.5.0
    + Undantag i azureml-format har lagts till för azureml-interpret
    + DeepScoringExplainer-serialisering för keras-modeller har åtgärdats
  + **azureml-mlflow**
    + Lägg till stöd för nationella moln i azureml.mlflow
  + **azureml-pipeline-core**
    + Notebook-datorn för batchbedömning för pipeline använder nu ParallelRunStep
    + En bugg där PythonScriptStep-resultat kunde återanvändas felaktigt trots att argumentlistan ändrades har åtgärdats
    + Möjligheten att ange kolumners typ när du anropar parse_*-metoderna har lagts till `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Flyttade till `AutoMLStep` `azureml-pipeline-steps` paketet. inaktuell i `AutoMLStep` `azureml-train-automl-runtime` .
    + Dokumentationsexempel för datauppsättning har lagts till som PythonScriptStegindata
  + **azureml-tensorboard**
    + azureml-tensorboard har uppdaterats för att stödja tensorflow 2.0
    + Visa rätt portnummer när du använder en anpassad Tensorboard-port på en beräkningsinstans
  + **azureml-train-automl-client**
    + Åtgärdat ett problem där vissa paket kan installeras på felaktiga versioner vid fjärrkörningar.
    + åtgärdat att FeaturizationConfig åsidosätter problem som filtrerar anpassade konfigurationer.
  + **azureml-train-automl-runtime**
    + Problemet med frekvensidentifiering i fjärrkörningarna har åtgärdats
    + Flyttade `AutoMLStep` i `azureml-pipeline-steps` paketet. inaktuella `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Stöd för PyTorch version 1.4 i PyTorch-beräkning
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK för Python v1.1.2rc0 (förhandsutgåv)

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Aktiverade batchlägesferensen (tar flera rader en gång) för AutoML ONNX-modeller
    + Förbättrad identifiering av frekvensen för datauppsättningar, saknade data eller innehåller oregelbundna datapunkter
    + Möjligheten att ta bort datapunkter som inte uppfyller den dominerande frekvensen har lagts till.
  + **azureml-automl-runtime**
    + Problemet med felet som uppstod om kornigheten inte fanns i träningsuppsättningen visades i testuppsättningen har åtgärdats
    + Kravet på y_query vid bedömning av prognostjänsten har tagits bort
  + **azureml-ib-manipulation**
    + Lägger till funktioner i klassen WebWebservice för att hämta åtkomsttoken
  + **azureml-core**
    + Distribuerade AzureML-webbtjänster använder nu loggning som `INFO` standard. Detta kan styras genom att ange `AZUREML_LOG_LEVEL` miljövariabeln i den distribuerade tjänsten.
    + Åtgärda iterering på `Dataset.get_all` för att returnera alla datauppsättningar som registrerats med arbetsytan.
    + Förbättra felmeddelandet när ogiltig typ skickas till argumentet `path` för API:er för skapande av datamängd.
    + Python SDK använder identifieringstjänsten för att använda api-slutpunkten i stället för "pipelines".
    + Växla till de nya vägarna i alla SDK-anrop
    + Ändrar routning av anrop till ModelManagementService till en ny enhetlig struktur
      + Gjorde metoden för arbetsyteuppdatering offentligt tillgänglig.
      + Parametern image_build_compute i metoden för arbetsyteuppdatering så att användaren kan uppdatera beräkningen för avbildningsbygget
    +  Utfasningsmeddelanden har lagts till i det gamla profileringsarbetsflödet. Begränsningar för profilering av processor och minne har åtgärdats
  + **azureml-interpret**
    + uppdatera azureml-interpret till interpret-community 0.6.*
  + **azureml-mlflow**
    + Lägg till stöd för nationella moln i azureml.mlflow
  + **azureml-pipeline-steps**
    + Flyttade till `AutoMLStep` `azureml-pipeline-steps package` . inaktuell i `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Åtgärdat ett problem där vissa paket kan installeras på felaktiga versioner på fjärrkörningar.
  + **azureml-train-automl-runtime**
    + Problemet med frekvensidentifiering i fjärrkörningarna har åtgärdats
    + Flyttade till `AutoMLStep` `azureml-pipeline-steps package` . inaktuell i `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Flyttade till `AutoMLStep` `azureml-pipeline-steps package` . inaktuell i `AutoMLStep` `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK för Python v1.1.1rc0 (förhandsutgåv)

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Profileringen av en instans har åtgärdats för att skapa en rekommendation och gjordes tillgänglig i Core SDK.
  + **azureml-automl-core**
    + Felloggningen har förbättrats.
  + **azureml-automl-runtime**
    + Problemet med prognostiserade data när datauppsättningen innehåller korta korn med långa tidsluckor har åtgärdats.
    + Problemet har åtgärdats när den automatiska max stapeln är aktiverad och datumkolumnen innehåller datum i form av strängar. Vi har lagt till korrekt konvertering och lämpligt fel om konvertering till datum inte är möjlig
    + Använda inbyggd NumPy och SciPy för serialisering och deserialisering av mellanliggande data för FileCacheStore (används för lokala AutoML-körningar)
    + En bugg där misslyckade underordnade körningar kan fastna i körningstillstånd har åtgärdats.
  + **azureml-cli-common**
    + Profileringen av en instans har åtgärdats för att skapa en rekommendation och gjordes tillgänglig i Core SDK.
  + **azureml-core**
    + Har lagts till som ytterligare en parameter för Datastore CLI för registrering av Azure Blob-container som gör att du kan registrera `--grant-workspace-msi-access` blobcontainer som finns bakom ett VNet
    + Profileringen av en instans har åtgärdats för att skapa en rekommendation och gjordes tillgänglig i Core SDK.
    + Problemet har åtgärdats i aks.py _deploy
    + Verifierar integriteten hos modeller som laddas upp för att undvika tyst lagringsfel.
  + **azureml-interpret**
    + lade till undantag i azureml-format till azureml-interpret
    + åtgärdat DeepScoringExplainer-serialisering för keras-modeller
  + **azureml-pipeline-core**
    + Notebook-datorn för batchbedömning för pipeline använder nu ParallelRunStep
  + **azureml-pipeline-steps**
    + Flyttade `AutoMLStep` i `azureml-pipeline-steps` paketet. inaktuell i `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Valfri parameter side_inputs till i ParallelRunStep. Den här parametern kan användas för att montera mappen på containern. Typer som stöds för närvarande är DataReference och PipelineData.
  + **azureml-tensorboard**
    + uppdaterade azureml-tensorboard för att stödja tensorflow 2.0
  + **azureml-train-automl-client**
    + åtgärdat att FeaturizationConfig åsidosätter problem som filtrerar anpassad konfiguration av featurization.
  + **azureml-train-automl-runtime**
    + Flyttade `AutoMLStep` i `azureml-pipeline-steps` paketet. inaktuell i `AutoMLStep` `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Stöd för PyTorch version 1.4 i PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK för Python v1.1.0rc0 (förhandsutgåv)

+ **Icke-bakåtkompatibla ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1.1 använder Azure ML Python SDK semantisk version 2.0.0. [Läs mer här.](https://semver.org/) Alla efterföljande versioner följer det nya numreringsschemat och det semantiska versionskontraktet. 
  
+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Ökad hastighet för featurisering.
    + Frekvenskontrollen har åtgärdats under bedömning, nu i prognosuppgifterna kräver vi inte strikt överensstämmelsefrekvens mellan träna och testuppsättning.
  + **azureml-core**
    + Användaren kan nu ange ett värde för autentiseringsnyckeln när nycklar återskapas för webbtjänster.
  + **azureml-interpret**
    + Azureml-interpret har uppdaterats för att vara beroende av interpret-community 0.5.0
  + **azureml-pipeline-core**
    + En bugg där PythonScriptStep-resultat kunde återanvändas felaktigt trots att argumentlistan ändrades har åtgärdats
  + **azureml-pipeline-steps**
    + Dokumentationsexempel för datauppsättning har lagts till som PythonScriptStegindata
  + **azureml-contrib-pipeline-steps**
    + Parametrar som skickas i ParallelRunConfig kan skrivas över genom att skicka pipelineparametrar nu. Nya pipelineparametrar som stöds aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count och aml_process_count_per_node ingår redan i en tidigare version).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK för Python v1.0.85

+ **Nya funktioner**
  + **azureml-core**
    + Hämta den aktuella begränsningen för kärnanvändning och kvoter för AmlCompute-resurser på en viss arbetsyta och prenumeration
  
  + **azureml-contrib-pipeline-steps**
    + Gör det möjligt för användaren att skicka tabelldatauppsättningen som ett mellanliggande resultat från föregående steg till parallelrunstep

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Kravet för att y_query i begäran till den distribuerade prognostjänsten har tagits bort. 
    + "y_query" har tagits bort från Avsnittet om notebook-tjänsten Orange Orange.
    + Buggen som förhindrar prognostiserade modeller som fungerar på datauppsättningar med datum/tid-kolumner har åtgärdats.
    + Lade till Matthews korrelationskoefficient som ett klassificeringsmått för både binär och multiklassklassificering.
  + **azureml-contrib-interpret**
    + Borttagna textförklarare från azureml-ib-interpret eftersom textförklaring har flyttats till lagringsplatsen interpret-text som snart kommer att släppas.
  + **azureml-core**
    + Datauppsättning: användningar för fildatauppsättningen är inte längre beroende av att numpy och Pandas installeras i Python-miljö.
    + Ändrade LocalWebservice.wait_for_deployment() för att kontrollera status för den lokala Docker-containern innan du försöker pinga dess hälsoslutpunkt, vilket minskar den tid det tar att rapportera en misslyckad distribution.
    + Initieringen av en intern egenskap som används i LocalWebservice.reload() har åtgärdats när tjänstobjektet skapas från en befintlig distribution med hjälp av konstruktorn LocalWebservice().
    + Redigerade felmeddelande för förtydligande.
    + En ny metod med namnet get_access_token() har lagts till i AksWebservice som returnerar AksServiceAccessToken-objektet, som innehåller åtkomsttoken, uppdaterar efter tidsstämpel, upphör att gälla för tidsstämpel och tokentyp. 
    + Inaktuell befintlig get_token()-metod i AksWebservice eftersom den nya metoden returnerar all information som den här metoden returnerar.
    + Ändrade utdata för kommandot az ml service get-access-token. Token har bytt namn till accessToken och refreshBy till refreshAfter. Egenskaperna expiryOn och tokenType har lagts till.
    + Åtgärdade get_active_runs
  + **azureml-explain-model**
    + uppdaterat shap till 0.33.0 och interpret-community till 0.4.*
  + **azureml-interpret**
    + uppdaterat shap till 0.33.0 och interpret-community till 0.4.*
  + **azureml-train-automl-runtime**
    + Matthews-korrelationskoefficienten har lagts till som ett klassificeringsmått för både binär och multiklassklassificering.
    + Inaktuell förbearbetningsflagga från kod och ersatt med featurization -featurization är på som standard

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK för Python v1.0.83

+ **Nya funktioner**
  + Datauppsättning: Lägg till två `on_error` alternativ och för att misslyckas när data har `out_of_range_datetime` `to_pandas_dataframe` felvärden i stället för att fylla dem med `None` .
  + Arbetsyta: Flaggan `hbi_workspace` har lagts till för arbetsytor med känsliga data som möjliggör ytterligare kryptering och inaktiverar avancerad diagnostik på arbetsytor. Vi har också lagt till stöd för att ta med dina egna nycklar för den associerade Cosmos DB-instansen genom att ange parametrarna och när du skapar en arbetsyta, vilket skapar en Cosmos DB-instans i din prenumeration när du etablerar `cmk_keyvault` `resource_cmk_uri` arbetsytan. [Läs mer här.](./concept-data-encryption.md#azure-cosmos-db)

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + En regression som gjorde att typeError uthöjdes när AutoML kördes på Python-versioner under 3.5.4 har åtgärdats.
  + **azureml-core**
    + Bugg i `datastore.upload_files` har åtgärdats där relativ sökväg som inte började `./` med inte kunde användas.
    + Utfasningsmeddelanden har lagts till för alla sökvägar för bildklasskod
    + Url-Modellhantering har åtgärdats för Azure China 21Vianet region.
    + Problem där modeller som använder source_dir inte kunde paketeras för Azure Functions har åtgärdats.    
    + Ett alternativ har lagts till för [att Environment.build_local() för att](/python/api/azureml-core/azureml.core.environment.environment) skicka en avbildning till containerregistret för AzureML-arbetsyta
    + SDK har uppdaterats för att använda det nya tokenbiblioteket på Azure Synapse på ett tillbakakompatibelt sätt.
  + **azureml-interpret**
    + En bugg har åtgärdats där None (Ingen) returnerades när det inte fanns några förklaringar till nedladdningen. Höjer nu ett undantag som matchar beteendet någon annanstans.
  + **azureml-pipeline-steps**
    + Tillåts inte att `DatasetConsumptionConfig` skicka `Estimator` s till `inputs` -parametern när `Estimator` ska användas i en `EstimatorStep` .
  + **azureml-sdk**
    + AutoML-klienten har lagts till i paketet azureml-sdk, vilket gör det möjligt att skicka autoML-fjärrkörningar utan att installera det fullständiga AutoML-paketet.
  + **azureml-train-automl-client**
    + Justering av konsolutdata för AutoML-körningar har korrigerats
    + Åtgärdat en bugg där felaktig version av Pandas kan installeras på fjärransluten amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK för Python v1.0.81

+ **Felkorrigeringar och förbättringar**
  + **azureml-contrib-interpret**
    + skjuta upp shap-beroende till interpret-community från azureml-interpret
  + **azureml-core**
    + Beräkningsmålet kan nu anges som en parameter till motsvarande konfigurationsobjekt för distribution. Detta är specifikt namnet på beräkningsmålet att distribuera till, inte SDK-objektet.
    + CreatedBy-information har lagts till i modell- och tjänstobjekt. Kan nås through.created_by
    + ContainerImage.run(), som inte konfigurerade Docker-containerns HTTP-port korrekt, har åtgärdats.
    + Gör `azureml-dataprep` valfritt för `az ml dataset register` CLI-kommando
    + Åtgärdat en bugg `TabularDataset.to_pandas_dataframe` där felaktigt skulle gå tillbaka till en alternativ läsare och skriva ut en varning.
  + **azureml-explain-model**
    + skjuta upp shap-beroendet för interpret-community från azureml-interpret
  + **azureml-pipeline-core**
    + Ett nytt pipelinesteg har `NotebookRunnerStep` lagts till för att köra en lokal notebook-dator som ett steg i pipelinen.
    + Inaktuella get_all för PublishedPipelines, Schedules och PipelineEndpoints har tagits bort
  + **azureml-train-automl-client**
    + Utfasning av data_script som indata till AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK för Python v1.0.79

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Tog bort featurizationConfig för att loggas
      + Loggning har uppdaterats för att endast logga "auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Stöd för Pandas har lagts till. Series och Pandas. Kategoriskt för identifiering av kolumndatatyp. Numpy.ndarray som stöds tidigare
      + Relaterade kodändringar har lagts till för att hantera kategorisk dtype korrekt.
    + Prognosfunktionsgränssnittet har förbättrats: y_pred parametern gjordes valfri. -Docstrings har förbättrats.
  + **azureml-contrib-dataset**
    + En bugg där märkta datauppsättningar inte kunde monteras har åtgärdats.
  + **azureml-core**
    + Felkorrigering för `Environment.from_existing_conda_environment(name, conda_environment_name)` . Användaren kan skapa en miljöinstans som är exakt replik av den lokala miljön
    + Tidsserierelaterade metoder för datauppsättningar har ändrats `include_boundary=True` till som standard.
  + **azureml-train-automl-client**
    + Ett problem har åtgärdats där valideringsresultat inte skrivs ut när visa utdata har angetts till false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK för Python v1.0.76

+ **Icke-bakåtkompatibla ändringar**
  + Problem med Azureml-Train-AutoML-uppgradering
    + Uppgradering till azureml-train-automl>=1.0.76 från azureml-train-automl<1.0.76 kan orsaka partiella installationer, vilket gör att vissa AutoML-importer misslyckas. Du kan lösa detta genom att köra installationsskriptet som finns på https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Eller om du använder pip direkt kan du:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + eller så kan du avinstallera den gamla versionen innan du uppgraderar
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + AutoML tar nu hänsyn till både true- och false-klasser vid beräkning av genomsnittliga skalära mått för binära klassificeringsuppgifter.
    + Flyttat Maskininlärnings- och träningskod i AzureML-AutoML-Core till ett nytt paket, AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + När du anropar på en etiketterad datauppsättning med nedladdningsalternativet kan du nu ange `to_pandas_dataframe` om du vill skriva över befintliga filer eller inte.
    + När du anropar eller som resulterar i att en tidsserie, etikett eller bildkolumn tas bort tas även motsvarande funktioner `keep_columns` `drop_columns` bort för datauppsättningen.
    + Ett problem med pytorch-inläsaren för objektidentifieringsaktiviteten har åtgärdats.
  + **azureml-contrib-interpret**
    + Widgeten förklaringsinstrumentpanelen har tagits bort från azureml-ib-interpret, ändrat paket för att referera till den nya i interpret_community
    + Uppdaterad version av interpret-community till 0.2.0
  + **azureml-core**
    + Förbättra prestanda för `workspace.datasets` .
    + Möjligheten att registrera datalager Azure SQL Database med autentisering med användarnamn och lösenord har lagts till
    + Korrigering för inläsning av RunConfigurations från relativa sökvägar.
    + När du anropar eller som resulterar i att en tidsseriekolumn tas bort tas även motsvarande funktioner `keep_columns` `drop_columns` bort för datauppsättningen.
  + **azureml-interpret**
    + uppdaterad version av interpret-community till 0.2.0
  + **azureml-pipeline-steps**
    + Dokumenterade värden som stöds för `runconfig_pipeline_params` pipelinestegen för Azure Machine Learning.
  + **azureml-pipeline-core**
    + CLI-alternativet för att ladda ned utdata i json-format för pipelinekommandon har lagts till.
  + **azureml-train-automl**
    + Dela upp AzureML-Train-AutoML i två paket, ett klientpaket AzureML-Train-AutoML-Client och ett ML-träningspaket AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Lade till en tunn klient för att skicka AutoML-experiment utan att behöva installera några maskininlärningsberoenden lokalt.
    + Loggning av automatiskt identifierade fördröjningar, rullande fönsterstorlekar och maximal horisont i fjärrkörningarna har åtgärdats.
  + **azureml-train-automl-runtime**
    + Lade till ett nytt AutoML-paket för att isolera maskininlärnings- och körningskomponenter från klienten.
  + **azureml-contrib-train-rl**
    + Stöd för förstärkt inlärning har lagts till i SDK.
    + Stöd för AmlWindowsCompute har lagts till i RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK för Python v1.0.74

  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-dataset**
      + När du har importerat azureml-contrib-dataset kan du anropa `Dataset.Labeled.from_json_lines` i stället för att skapa en `._Labeled` etiketterad datauppsättning.
      + När du anropar på en etiketterad datauppsättning med nedladdningsalternativet kan du nu ange om `to_pandas_dataframe` du vill skriva över befintliga filer eller inte.
      + När du anropar eller som resulterar i att en tidsserie, etikett eller bildkolumn tas bort tas även motsvarande funktioner `keep_columns` `drop_columns` bort för datauppsättningen.
      + Problem med PyTorch-inläsaren vid anrop av `dataset.to_torchvision()` har åtgärdats.

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Modellprofilering har lagts till i förhandsversionen av CLI.
    + Åtgärdar den senaste ändringen Azure Storage orsakar att AzureML CLI misslyckas.
    + Lade Load Balancer typ till MLC för AKS-typer
  + **azureml-automl-core**
    + Åtgärdat problemet med identifiering av maximal horisont för tidsserier, med saknade värden och flera kornighet.
    + Åtgärdat problemet med fel vid generering av delningar av korsvalidering.
    + Ersätt det här avsnittet med ett meddelande i markdown-format som ska visas i den nya informationen: -Förbättrad hantering av kortsiktiga korn i prognostiserade datamängder.
    + Åtgärdat problemet med maskering av viss användarinformation under loggning. – Förbättrad loggning av fel under prognoskörningar.
    + Lägger till psutil som ett conda-beroende till den automatiskt genererade yml-distributionsfilen.
  + **azureml-ib-manipulation**
    + Åtgärdar den senaste ändringen Azure Storage orsakar att AzureML CLI misslyckas.
  + **azureml-core**
    + Åtgärdar ett fel som gjorde att modeller som distribuerades Azure Functions genererade 500-tal.
    + Åtgärdat ett problem där amlignore-filen inte tillämpades på ögonblicksbilder.
    + En ny API-amlcompute.get_active_runs returnerar en generator för körning och kökörningar på en viss amlcompute.
    + Lade Load Balancer typ till MLC för AKS-typer.
    + Parametern append_prefix bool har lagts download_files i run.py och download_artifacts_from_prefix i artifacts_client. Den här flaggan används för att selektivt platta ut ursprungsfilökvägen så att endast fil- eller mappnamnet läggs till i output_directory
    + Åtgärda deserialiseringsproblem för med `run_config.yml` datamängdsanvändning.
    + När du anropar eller som resulterar i att en tidsseriekolumn tas bort tas även motsvarande funktioner bort `keep_columns` `drop_columns` för datauppsättningen.
  + **azureml-interpret**
    + Interpret-community-versionen har uppdaterats till 0.1.0.3
  + **azureml-train-automl**
    + Åtgärdat ett problem där automl_step kanske inte skriver ut verifieringsproblem.
    + Åtgärdade register_model att lyckas även om modellens miljö saknar beroenden lokalt.
    + Åtgärdat ett problem där vissa fjärrkörningar inte var Docker-aktiverade.
    + Lägg till loggning av undantaget som gör att en lokal körning misslyckas i förtid.
  + **azureml-train-core**
    + Överväg resume_from körningar i beräkningen av automatisk hyperparameterjustering av bästa underordnade körningar.
  + **azureml-pipeline-core**
    + Parameterhantering vid konstruktion av pipelineargument har åtgärdats.
    + Pipelinebeskrivning och yaml-parameter för stegtyp har lagts till.
    + Nytt yaml-format för Pipeline-steg och utfasningsvarning för gammalt format har lagts till.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webbupplevelse

Landningssidan för [https://ml.azure.com](https://ml.azure.com) samarbetsarbetsytan på har förbättrats och omprofilerats som Azure Machine Learning-studio.

Från studio kan du träna, testa, distribuera och hantera Azure Machine Learning till exempel datauppsättningar, pipelines, modeller, slutpunkter med mera.

Få åtkomst till följande webbaserade redigeringsverktyg från studio:

| Webbaserat verktyg | Description | 
|-|-|-|
| Notebook VM (förhandsversion) | Fullständigt hanterad molnbaserad arbetsstation | 
| [Automatiserad maskininlärning](tutorial-first-experiment-automated-ml.md) (förhandsversion) | Ingen kodupplevelse för att automatisera utveckling av maskininlärningsmodeller | 
| [Designer](concept-designer.md) | Dra och släpp-modelleringsverktyget för maskininlärning kallades tidigare det visuella gränssnittet | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning designerförbättringar

+ Kallades tidigare visuellt gränssnitt 
+    11 nya [moduler,](algorithm-module-reference/module-reference.md) inklusive rekommenderare, klassificerare och utbildningsverktyg, inklusive funktionsteknik, korsvalidering och dataomvandling.

### <a name="r-sdk"></a>R SDK 
 
Dataforskare och AI-utvecklare använder [Azure Machine Learning SDK för R för](https://github.com/Azure/azureml-sdk-for-r) att skapa och köra maskininlärningsarbetsflöden med Azure Machine Learning.

Den Azure Machine Learning SDK för R använder `reticulate` paketet för att binda till Python SDK. Genom att binda direkt till Python ger SDK för R dig åtkomst till kärnobjekt och metoder som implementeras i Python SDK från valfri R-miljö.

Huvudfunktionerna i SDK:n är:

+    Hantera molnresurser för övervakning, loggning och organisering av Machine Learning-experiment.
+    Träna modeller med hjälp av molnresurser, inklusive GPU-accelererad modellträning.
+    Distribuera dina modeller som webbtjänster på Azure Container Instances (ACI) och Azure Kubernetes Service (AKS).

Se [paketwebbplatsen](https://azure.github.io/azureml-sdk-for-r) för fullständig dokumentation.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integrering med Event Grid 

Azure Machine Learning nu är en resursprovider för Event Grid kan du konfigurera maskininlärningshändelser via Azure Portal eller Azure CLI. Användare kan skapa händelser för körningsslut, modellregistrering, modelldistribution och dataavdrift som har identifierats. Dessa händelser kan dirigeras till händelsehanterare som stöds av Event Grid för förbrukning. Mer information finns i artiklarna [om scheman](../event-grid/event-schema-machine-learning.md) för [maskininlärningshändelse](how-to-use-event-grid.md) och självstudier.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK för Python v1.0.72

+ **Nya funktioner**
  + Datauppsättningsövervakare har lagts till via [**paketet azureml-datadrift,**](/python/api/azureml-datadrift) vilket gör det möjligt att övervaka tidsseriedatauppsättningar för dataavdrift eller andra statistiska ändringar över tid. Aviseringar och händelser kan utlösas om avdrift identifieras eller andra villkor i data uppfylls. Mer [information finns i](how-to-monitor-datasets.md) vår dokumentation.
  + Vi presenterar två nya utgåvor (kallas även för en SKU utan vidare) i Azure Machine Learning. Med den här versionen kan du nu skapa antingen en Basic- eller Enterprise Azure Machine Learning arbetsyta. Alla befintliga arbetsytor får standardversionen Basic och du kan gå till Azure Portal eller till studio för att uppgradera arbetsytan när som helst. Du kan skapa antingen en Basic- eller Enterprise-arbetsyta från Azure Portal. Läs [vår dokumentation om](./how-to-manage-workspace.md) du vill veta mer. Från SDK:n kan du fastställa versionen av din arbetsyta med hjälp av egenskapen "sku" för ditt arbetsyteobjekt.
  + Vi har också gjort förbättringar av Azure Machine Learning Compute – nu kan du visa mått för dina kluster (till exempel totalt antal noder, noder som körs, total kärnkvot) i Azure Monitor, förutom att visa diagnostikloggar för felsökning. Dessutom kan du även visa körningar som körs eller körs i kö i klustret och information som IP-adresser för de olika noderna i klustret. Du kan visa dessa antingen i portalen eller med hjälp av motsvarande funktioner i SDK eller CLI.

  + **Förhandsgranskningsfunktioner**
    + Vi lanserar förhandsversionsstöd för diskkryptering av din lokala SSD i Azure Machine Learning Compute. Skapa en teknisk supportbiljett för att få din prenumeration att tillåta att den här funktionen används i listan.
    + Offentlig förhandsversion av Azure Machine Learning Batch-inferens. Azure Machine Learning Batch Inference riktar in sig på stora härledningsjobb som inte är tidskänsliga. Batch-inferens ger kostnadseffektiv beräkningsskalning med ojämförligt dataflöde för asynkrona program. Den är optimerad för stora datasamlingar med fire-and-forget-inferens.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Aktiverade funktioner för etiketterad datauppsättning
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + CLI stöder nu modellpaketering.
    + Lade till datauppsättnings-CLI. Mer information: `az ml dataset --help`
    + Stöd har lagts till för att distribuera och paketera modeller som stöds (ONNX, scikit-learn och TensorFlow) utan en InferenceConfig-instans.
    + Lade till överskrivningsflagga för tjänstdistribution (ACI och AKS) i SDK och CLI. Om detta anges skriver den befintliga tjänsten över om det redan finns en tjänst med namnet . Om tjänsten inte finns skapar en ny tjänst.
    + Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. – Modellregistreringen accepterar exempel på indata, exempel på utdata och resurskonfiguration för modellen.
  + **azureml-automl-core**
    + Träning av en iteration skulle bara köras i en underordnad process när körningsbegränsningar anges.
    + En skyddsräcke för prognostiserade uppgifter har lagts till för att kontrollera om en angiven max_horizon orsakar ett minnesproblem på den angivna datorn eller inte. Om det gör det visas ett meddelande om skyddsräcken.
    + Stöd har lagts till för komplexa frekvenser som två år och en månad. – Ett lättförståeligt felmeddelande om frekvensen inte kan fastställas har lagts till.
    + Lägg till azureml-defaults till automatiskt genererad conda-env för att lösa modelldistributionsfelet
    + Tillåt att mellanliggande data Azure Machine Learning Pipeline konverteras till tabelldatauppsättning och användas i `AutoMLStep` .
    + Implementerat kolumnuppdatering för strömning.
    + Implementerat uppdatering av transformersparametern för Imputer och HashOneHotEncoder för strömning.
    + Den aktuella datastorleken och den minsta nödvändiga datastorleken har lagts till i valideringsfelmeddelandena.
    + Den minsta datastorleken som krävs för korsvalidering har uppdaterats för att garantera minst två exempel i varje valideringsdeck.
  + **azureml-cli-common**
    + CLI stöder nu modellpaketering.
    + Modeller kan registreras med två nya ramverk, Onnx och Tensorflow.
    + Modellregistreringen accepterar exempel på indata, exempel på utdata och resurskonfiguration för modellen.
  + **azureml-contrib-gbdt**
    + har åtgärdat lanseringskanalen för notebook-datorn
    + En varning för beräkningsmål som inte är AmlCompute har lagts till som vi inte stöder
    + LightGMB-beräknaren har lagts till i paketet azureml-contrib-gbdt
  + [**azureml-core**](/python/api/azureml-core)
    + CLI stöder nu modellpaketering.
    + Lägg till utfasningsvarning för inaktuella DATAUPPSÄTTNINGS-API:er. Mer information finns i Ändringsmeddelande för datauppsättnings-API på https://aka.ms/tabular-dataset .
    + Ändra [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) för att returnera registreringsnamn och version om datauppsättningen är registrerad.
    + Åtgärda ett fel som gör att ScriptRunConfig med datauppsättning som argument inte kan användas upprepade gånger för att skicka experimentkörningar.
    + Datauppsättningar som hämtas under en körning spåras och kan visas på sidan med körningsinformation eller genom att [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) anropas när körningen är klar.
    + Tillåt att mellanliggande data Azure Machine Learning Pipeline konverteras till tabelldatauppsättning och användas i [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Stöd har lagts till för att distribuera och paketera modeller som stöds (ONNX, scikit-learn och TensorFlow) utan en InferenceConfig-instans.
    + Lade till överskrivningsflagga för tjänstdistribution (ACI och AKS) i SDK och CLI. Om detta anges skriver den befintliga tjänsten över om det redan finns en tjänst med namnet . Om tjänsten inte finns skapar en ny tjänst.
    +  Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. Modellregistreringen accepterar exempel på indata, exempel på utdata och resurskonfiguration för modellen.
    + Ett nytt datalager för Azure Database for MySQL har lagts till. Exempel har lagts till för Azure Database for MySQL i DataTransferStep Azure Machine Learning Pipelines.
    + Funktioner för att lägga till och ta bort taggar från experiment Har lagts till funktioner för att ta bort taggar från körningar
    + Lade till överskrivningsflagga för tjänstdistribution (ACI och AKS) i SDK och CLI. Om detta anges skriver den befintliga tjänsten över om det redan finns en tjänst med namnet . Om tjänsten inte finns skapar en ny tjänst.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Har flyttats `azureml-contrib-datadrift` från till `azureml-datadrift`
    + Stöd har lagts till för övervakning av tidsseriedatauppsättningar för drift och andra statistiska mått
    + Nya `create_from_model()` metoder `create_from_dataset()` och till klassen [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) . Metoden `create()` kommer att bli inaktuell.
    + Justeringar av visualiseringarna i Python och användargränssnittet i Azure Machine Learning-studio.
    + Stöd för schemaläggning av veckovisa och månatliga övervakningar, förutom dagligen för datamängdsövervakare.
    + Stöd för återfyllning av dataövervakningsmått för att analysera historiska data för datamängdsövervakare.
    + Olika felkorrigeringar
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-dataprep behövs inte längre för att skicka en Azure Machine Learning pipelinekörning från `yaml` pipelinefilen.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Lägg till azureml-defaults till automatiskt genererad conda-env för att lösa modelldistributionsfelet
    + AutoML-fjärrträning innehåller nu azureml-standardvärden för att tillåta återanvändning av träningsenv för härledning.
  + **azureml-train-core**
    + PyTorch 1.3-stöd har lagts till [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) i beräkning

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuellt gränssnitt (förhandsversion)

+ Det Azure Machine Learning visuella gränssnittet (förhandsversion) har gjorts om för att köras [Azure Machine Learning pipelines](concept-ml-pipelines.md). Pipelines (tidigare kallade experiment) som har skrivits i det visuella gränssnittet är nu helt integrerade med kärnan Azure Machine Learning upplevelsen.
  + Enhetlig hanteringsupplevelse med SDK-tillgångar
  + Versionshantering och spårning för modeller, pipelines och slutpunkter för visuellt gränssnitt
  + Omdesignat användargränssnitt
  + Batch-inferensdistribution har lagts till
  + Stöd Azure Kubernetes Service (AKS) för beräkningsmål för slutsatsledning har lagts till
  + Nytt arbetsflöde för redigering av Python-stegspipeline
  + Ny [landningssida](https://ml.azure.com) för redigeringsverktyg för visuella objekt

+ **Nya moduler**
  + Tillämpa matematisk åtgärd
  + Använda SQL-transformering
  + Clip-värden
  + Sammanfatta data
  + Importera från SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK för Python v1.0.69

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Begränsa modellförklaringar till bästa körning i stället för att beräkna förklaringar för varje körning. Ändra det här beteendet för lokal, fjärransluten och ADB.
    + Stöd har lagts till för modellförklaringar på begäran för användargränssnitt
    + Lade till psutil som ett beroende för `automl` och inkluderade psutil som ett conda-beroende i amlcompute.
    + Åtgärdat problemet med heuristikfördröjningar och rullande fönsterstorlekar på prognosdatauppsättningarna, varav vissa serier kan orsaka linjära algebrafel
      + Utskrift har lagts till för de heurisistiskt bestäms parametrarna i prognoskörningarna.
  + **azureml-contrib-datadrift**
    + Skydd har lagts till när utdatamått skapas om datamängdens nivåavdrift inte finns i det första avsnittet.
  + **azureml-contrib-interpret**
    + paketet azureml-ib-explain-model har bytt namn till azureml-ib-interpret
  + **azureml-core**
    + API har lagts till för att avregistrera datauppsättningar. `dataset.unregister_all_versions()`
    + paketet azureml-ib-explain-model har bytt namn till azureml-ib-interpret.
  + **[azureml-core](/python/api/azureml-core)**
    + API har lagts till för att avregistrera datauppsättningar. Datamängd. [unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Datamängds-API har lagts till för att kontrollera data ändrad tid. `dataset.data_changed_time`.
    + Kunna använda och `FileDataset` som `TabularDataset` indata till `PythonScriptStep` , och i `EstimatorStep` Azure Machine Learning `HyperDriveStep` pipeline
    + Prestanda `FileDataset.mount` för har förbättrats för mappar med ett stort antal filer
    + Kunna använda [FileDataset](/python/api/azureml-core/azureml.data.filedataset) och [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) som indata för [PythonScriptStega](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)och [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) i Azure Machine Learning pipelinen.
    + Prestanda för FileDataset. [mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) har förbättrats för mappar med ett stort antal filer
    + Url har lagts till för kända felrekommendationer i körningsinformation.
    + Åtgärdat en bugg i run.get_metrics där begäranden skulle misslyckas om en körning hade för många underordnade
    + Åtgärdat en bugg [i run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) där begäranden skulle misslyckas om en körning hade för många underordnade
    + Stöd har lagts till för autentisering på Arcadia-kluster.
    + När du skapar ett experimentobjekt hämtar eller skapar du experimentet i Azure Machine Learning för spårning av körningshistorik. Experiment-ID:t och den arkiverade tiden fylls i i experimentobjektet när det skapas. Exempel: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() och reactivate() är funktioner som kan anropas i ett experiment för att dölja och återställa experimentet från att visas i UX eller returneras som standard i ett anrop för att lista experiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett visst namn. Exempel: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Skapa ett nytt aktivt experiment med samma namn som det arkiverade. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") Den statiska metoden list() i Experiment kan ta ett namnfilter och Ett ViewType-filter. ViewType-värden är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "ALL" Exempel: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Stöd för användning av miljö för modelldistribution och tjänstuppdatering
  + **azureml-datadrift**
    + Show-attributet för klassen DataDriftDector stöder inte längre det valfria argumentet "with_details". Show-attributet visar endast bidrag för dataavdriftskoefficienter och dataavdrift i egenskapskolumner.
    + DataDriftDetector-attributet "get_output"-beteendeändringar:
      + Indataparametern start_time, end_time är valfria i stället för obligatoriska.
      + Indataspecifika start_time och/eller end_time med en specifik run_id i samma anrop resulterar i undantag för värdefel eftersom de utesluter varandra
      + Efter indata start_time och/eller end_time returneras endast resultat av schemalagda körningar.
      + Parametern "daily_latest_only" är inaktuell.
    + Stöd för hämtning av datamängdsbaserade Data Drift-utdata.
  + **azureml-explain-model**
    + Byter namn på paketet AzureML-explain-model till AzureML-interpret, vilket gör det gamla paketet bakåtkompatibelt tills nu
    + bugg `automl` med rådataförklaringar inställda på klassificeringsuppgift i stället för regression som standard vid nedladdning från ExplanationClient
    + Lägg till stöd för `ScoringExplainer` som ska skapas direkt med hjälp av `MimicWrapper`
  + **azureml-pipeline-core**
    + Bättre prestanda vid skapande av stora pipelines
  + **azureml-train-core**
    + Stöd för TensorFlow 2.0 har lagts till i TensorFlow Estimator
  + **azureml-train-automl**
    + När du [skapar ett experimentobjekt](/python/api/azureml-core/azureml.core.experiment.experiment) hämtar eller skapar du experimentet Azure Machine Learning arbetsytan för spårning av körningshistorik. Experiment-ID och arkiverad tid fylls i i experimentobjektet när det skapas. Exempel:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) och [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) är funktioner som kan anropas i ett experiment för att dölja och återställa experimentet från att visas i UX eller returneras som standard i ett anrop för att lista experiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett visst namn. Exempel:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Den statiska [metoden list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) i Experiment kan ta ett namnfilter och Ett ViewType-filter. ViewType-värden är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "ALL". Exempel:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Stöd för användning av miljö för modelldistribution och tjänstuppdatering.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Show-attributet [för klassen DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) stöder inte det valfria argumentet "with_details" längre. Attributet show visar endast bidrag för dataavdriftskoefficienter och dataavdrift i funktionskolumner.
    + Funktionsändringar för DataDriftDetector [get_output]python/api/azureml-datadrift/azureml.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-):
      + Indataparametern start_time, end_time är valfria i stället för obligatoriska.
      + Indataspecifika start_time och/eller end_time med en specifik run_id i samma anrop resulterar i undantag för värdefel eftersom de är ömsesidigt uteslutande.
      + Efter indata start_time och/eller end_time returneras endast resultat av schemalagda körningar.
      + Parametern "daily_latest_only" är inaktuell.
    + Stöd för hämtning av datamängdsbaserade Data Drift-utdata.
  + **azureml-explain-model**
    + Lägg till stöd [för ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) som ska skapas direkt med MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Bättre prestanda vid skapande av stora pipelines.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + TensorFlow 2.0-stöd har lagts till [i TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Den överordnade körningen misslyckas inte längre när installationen av iterationen misslyckades, eftersom orkestreringen redan tar hand om den.
    + Stöd för local-docker och local-conda har lagts till för AutoML-experiment
    + Stöd för local-docker och local-conda har lagts till för AutoML-experiment.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webbupplevelse (förhandsversion) för Azure Machine Learning arbetsytor

Fliken Experiment i den nya [arbetsyteportalen](https://ml.azure.com) har uppdaterats så att dataexperter kan övervaka experiment på ett mer bra sätt. Du kan utforska följande funktioner:
+ Experimentera med metadata för att enkelt filtrera och sortera din lista över experiment
+ Förenklade och presterande experimentinformationssidor som gör att du kan visualisera och jämföra dina körningar
+ Ny design för att köra informationssidor för att förstå och övervaka dina träningskörningar

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK för Python v1.0.65

  + **Nya funktioner**
    + Lade till curated environments (curated environments). De här miljöerna har förkonfigureras med bibliotek för vanliga maskininlärningsuppgifter och har förbyggts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard i arbetsytans lista över miljöer med prefixet "AzureML".
    + Lade till curated environments (curated environments). De här miljöerna har förkonfigureras med bibliotek för vanliga maskininlärningsuppgifter och har förbyggts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard [i arbetsytans](/python/api/azureml-core/azureml.core.workspace%28class%29)lista över miljö, med prefixet "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Stöd för ONNX-konvertering har lagts till för ADB och HDI

+ **Förhandsgranskningsfunktioner**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + STÖDDA och BiLSTM som text-featurizer (endast förhandsversion)
    + Stöd för funktionaliseringsanpassning för kolumnsyfte och transformeringsparametrar (endast förhandsversion)
    + Råförklaringar som stöds när användaren aktiverar modellförklaring under träning (endast förhandsversion)
    + Tillagd `timeseries` för prognostiserade som en trainable-pipeline (endast förhandsversion)

  + **azureml-contrib-datadrift**
    + Paket som flyttats från azureml-ib-datadrift till azureml-datadrift; paketet `contrib` tas bort i en framtida version

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Introducerade FeaturizationConfig till AutoMLConfig och AutoMLBaseSettings
    + Introducerade FeaturizationConfig för [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) och AutoMLBaseSettings
      + Åsidosätt kolumnsyfte för funktionalisering med en viss kolumn och funktionstyp
      + Åsidosätta transformeringsparametrar
    + Utfasningsmeddelande för explain_model() och retrieve_model_explanations() har lagts till
    + Har lagts till som en trainable pipeline (endast förhandsversion)
    + Ett utfasningsmeddelande för explain_model() och retrieve_model_explanations() har lagts till.
    + Added Added Added as a trainable pipeline (preview only) (Jag har lagt till Förlopp som en trainable pipeline (endast förhandsversion).
    + Stöd har lagts till för automatisk identifiering av målfördröjningar, rullande fönsterstorlek och maximal horisont. Om någon target_lags, target_rolling_window_size eller max_horizon har angetts till "auto" tillämpas heuristiken för att uppskatta värdet för motsvarande parameter baserat på träningsdata.
    + Prognostiserades i fallet när datauppsättningen innehåller en kornighetskolumn, den här kornigheten är av en numerisk typ och det finns en lucka mellan träna och testuppsättningen
    + Felmeddelandet om det duplicerade indexet i fjärrkörningen i prognostiserade uppgifter har åtgärdats
    + Prognostiserades i fallet när datauppsättningen innehåller en kornighetskolumn. Den här kornigheten är av en numerisk typ och det finns en lucka mellan träna och testuppsättningen.
    + Åtgärdade felmeddelandet om det duplicerade indexet i fjärrkörningen i prognosuppgifter.
    + En skyddsräcke har lagts till för att kontrollera om en datauppsättning är obalanserad eller inte. I så fall skrivs ett meddelande om skyddsräcke till konsolen.
  + **azureml-core**
    + Lade till möjlighet att hämta SAS-URL:en till modellen i lagringen via modellobjektet. Exempel: model.get_sas_url()
    + Introducera `run.get_details()['datasets']` för att hämta datauppsättningar som är associerade med den skickade körningen
    + Lägg till API `Dataset.Tabular.from_json_lines_files` för att skapa en TabularDataset från JSON Lines-filer. Mer information om dessa tabelldata i JSON Lines-filer på TabularDataset finns i den [här artikeln för](how-to-create-register-datasets.md) dokumentation.
    + Ytterligare vm-storleksfält (OS-disk, antal GPU:er) har lagts till i supported_vmsizes () funktionen
    + Ytterligare fält har lagts till i list_nodes () för att visa körningen, den privata och offentliga IP-adressen, porten osv.
    + Möjlighet att ange ett nytt fält under klusteretablering --remotelogin_port_public_access som kan anges som aktiverat eller inaktiverat beroende på om du vill lämna SSH-porten öppen eller stängd när klustret skapas. Om du inte anger det öppnar eller stänger tjänsten porten smart beroende på om du distribuerar klustret i ett VNet.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Lade till möjligheten att hämta SAS-URL:en till modellen i lagringen via modellobjektet. Exempel: modell. [get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introducera körning. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] för att hämta datauppsättningar som är associerade med den skickade körningen
    + Lägg till API `Dataset.Tabular` .[ from_json_lines_files() för](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) att skapa en TabularDataset från JSON Lines-filer. Mer information om dessa tabelldata i JSON Lines-filer på TabularDataset finns i https://aka.ms/azureml-data dokumentationen.
    + Ytterligare vm-storleksfält (OS-disk, antal GPU:er) har lagts till [i supported_vmsizes()-funktionen](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Ytterligare fält har lagts till [i list_nodes()-funktionen](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) för att visa körningen, den privata och offentliga IP-adressen, porten osv.
    + Möjlighet att ange ett [](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) nytt fält under klusteretablering som kan anges som aktiverat eller inaktiverat beroende på om du vill lämna SSH-porten öppen eller stängd när klustret skapas. Om du inte anger det öppnar eller stänger tjänsten porten smart beroende på om du distribuerar klustret i ett VNet.
  + **azureml-explain-model**
    + Förbättrad dokumentation för förklaringsutdata i klassificeringsscenariot.
    + Lade till möjligheten att ladda upp de förutsagda y-värdena i förklaringen till utvärderingsexempel. Låser upp mer användbara visualiseringar.
    + Förklaringsegenskapen har lagts till i MimicWrapper så att den underliggande MimicExplainer kan hämtas.
  + **azureml-pipeline-core**
    + Anteckningsboken har lagts till för att beskriva Module, ModuleVersion och ModuleStep
  + **azureml-pipeline-steps**
    + RScriptStep har lagts till för att stödja R-skriptkörning via AML-pipeline.
    + Metadataparametrar som parsas i AzureBatchStep som orsakade felmeddelandet "tilldelning för parametern SubscriptionId har inte angetts" har åtgärdats.
  + **azureml-train-automl**
    + Stöd training_data, validation_data, label_column_name, weight_column_name som indataformat
    + Utfasningsmeddelande för explain_model() och retrieve_model_explanations() har lagts till
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + En anteckningsbok [har lagts](https://aka.ms/pl-modulestep) till för [att beskriva](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29)Modul , [ModuleVersion och [ModuleStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + [RScriptStep har lagts till för](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) att stödja R-skriptkörning via AML-pipeline.
    + Metadataparametrar parsning i [AzureBatchStep som orsakade felmeddelandet "tilldelning för parametern SubscriptionId har inte angetts" har åtgärdats.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Stöds training_data, validation_data, label_column_name, weight_column_name som datainmatningsformat.
    + Ett utfasningsmeddelande för [explain_model() och](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) [retrieve_model_explanations() har lagts till.](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK för Python v1.0.62

+ **Nya funktioner**
  + Introducerade `timeseries`  -funktionen på TabularDataset. Den här funktionen möjliggör enkel tidsstämpelfiltrering på data som tabularDataset, till exempel att ta alla data mellan ett tidsintervall eller de senaste data.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb för en exempelanteckningsbok.
  + Träning med TabularDataset och FileDataset har aktiverats. 

  + **azureml-train-core**
      + Stöd `Nccl` för och har lagts till i `Gloo` PyTorch-beräknaren

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + AutoML-inställningen "lag_length" och LaggingTransformer har föraktats.
    + Rätt validering av indata har åtgärdats om de anges i dataflödesformat
    + Ändrade fit_pipeline.py för att generera graph json och ladda upp till artefakter.
    + Renderade diagrammet under `userrun` med hjälp av `Cytoscape` .
  + **azureml-core**
    + Vi har tittat på undantagshanteringen i ADB-koden och gjort ändringar i enligt ny felhantering
    + Automatisk MSI-autentisering har lagts till för virtuella notebook-datorer.
    + Åtgärdar fel där skadade eller tomma modeller kunde laddas upp på grund av misslyckade återförsök.
    + En bugg har åtgärdats `DataReference` där namnet ändras när läget ändras `DataReference` (till exempel när du `as_upload` anropar , eller `as_download` `as_mount` ).
    + Gör `mount_point` och `target_path` valfritt för och `FileDataset.mount` `FileDataset.download` .
    + Undantag som gör att det inte går att hitta en tidsstämpelkolumn om det tidsserierelaterade API:et anropas utan att en fin tilldelad tidsstämpelkolumn har tilldelats eller om de tilldelade tidsstämpelkolumnerna tas bort.
    + Tidsseriekolumner ska tilldelas till en kolumn vars typ är Datum, annars förväntas undantag
    + Tidsseriekolumner som tilldelar API:with_timestamp_columns värdet None (Ingen) kan ha ett fin/grovt kolumnnamn för tidsstämpeln, vilket rensar tidigare tilldelade tidsstämpelkolumner.
    + Undantag utlöstes när antingen grov kornighet eller en finkornig tidsstämpelkolumn utelämnas med en indikation för användaren att det går att släppa efter att antingen exkludera tidsstämpelkolumnen i listan eller anropa with_time_stamp med inget värde för att frigöra kolumner för tidsstämpel
    + Undantag utlöstes när antingen en grov eller en kornig tidsstämpelkolumn inte ingår i listan över behåll kolumner med en indikation för användaren om att hållning kan göras efter att antingen inkludera en tidsstämpelkolumn i kolumnlistan eller anropa with_time_stamp med inget värde för att frigöra tidsstämpelkolumner.
    + Loggning för storleken på en registrerad modell har lagts till.
  + **azureml-explain-model**
    + Varning har åtgärdats som skrivs ut till konsolen när "paketering" python-paketet inte är installerat: "Uppgradera till version större än 2.2.1 med en äldre version av lightgbm som stöds"
    + Förklaring av nedladdningsmodell med horisontell partitionering för globala förklaringar med många funktioner har åtgärdats
    + Imiteringsförklararen saknade initieringsexempel på utdataförklaring
    + Ett oföränderligt fel har åtgärdats för uppsättningsegenskaper vid uppladdning med förklaringsklienten med två olika typer av modeller
    + Lade till get_raw parameter till bedömningsförklararen.explain() så att en bedömningsförklarare kan returnera både utformade och rådatavärden.
  + **azureml-train-automl**
    + Introducerade offentliga API:er från AutoML för att ge förklaringar från förklara SDK – Nyare sätt att stödja AutoML-förklaringar genom att frikoppla AutoML-funktionalisering och förklara SDK – Integrerat stöd för raw-förklaringar från azureml förklarar SDK för `automl` AutoML-modeller.
    + Ta bort azureml-defaults från fjärranslutna träningsmiljöer.
    + Standardplatsen för cachelagring har ändrats från FileCacheStore baserat på en till AzureFileCacheStore en för AutoML Azure Databricks sökvägen.
    + Rätt validering av indata har åtgärdats om de anges i dataflödesformat
  + **azureml-train-core**
    + Återställd source_directory_data_store utfasning.
    + Möjligheten att åsidosätta installerade paketversioner för Azureml har lagts till.
    + Stöd för dockerfile har lagts `environment_definition` till i parametern i beräknare.
    + Förenklade distribuerade träningsparametrar i beräknare.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webbupplevelse (förhandsversion) för Azure Machine Learning arbetsytor
Den nya webbupplevelsen gör det möjligt för dataforskare och datatekniker att slutföra livscykeln för maskininlärning från att förbereda och visualisera data till att träna och distribuera modeller på en enda plats.

![Azure Machine Learning användargränssnitt för arbetsyta (förhandsversion)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Viktiga funktioner:**

Med det här Azure Machine Learning gränssnittet kan du nu:
+ Hantera dina notebook-datorer eller länka till Jupyter
+ [Köra automatiserade ML-experiment](tutorial-first-experiment-automated-ml.md)
+ [Skapa datauppsättningar från lokala filer, datalager & webbfiler](how-to-create-register-datasets.md)
+ Utforska & förbereder datauppsättningar för modellskapande
+ Övervaka dataavdrift för dina modeller
+ Visa de senaste resurserna från en instrumentpanel

I den här versionen stöds följande webbläsare: Chrome, Firefox, Safari och Microsoft Edge förhandsversion.

**Kända problem:**

1. Uppdatera webbläsaren om du ser "Något gick fel! Fel vid inläsning av segmentfiler" när distributionen pågår.

1. Det går inte att ta bort eller byta namn på filen i Notebooks och Files. Under den offentliga förhandsversionen kan du använda Jupyter UI eller Terminal i notebook-dator för att utföra åtgärder för uppdateringsfiler. Eftersom det är ett monterat nätverksfilsystem återspeglas alla ändringar som du gör på den virtuella notebook-datorn direkt i notebook-arbetsytan.

1. Så här använder du SSH för den virtuella notebook-datorn:
   1. Leta reda på de SSH-nycklar som skapades under installationen av den virtuella datorn. Du kan också hitta nycklarna i Azure Machine Learning-arbetsytan > öppna fliken Compute > leta upp den virtuella datorn Notebook i listan > öppna dess egenskaper: kopiera nycklarna från dialogrutan.
   1. Importera de offentliga och privata SSH-nycklarna till den lokala datorn.
   1. Använd dem för att använda SSH i den virtuella notebook-datorn.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK för Python v1.0.60

+ **Nya funktioner**
  + Introducerade FileDataset, som refererar till en eller flera filer i dina datalager eller offentliga URL:er. Filerna kan ha vilket format som helst. FileDataset ger dig möjlighet att ladda ned eller montera filerna till din beräkning. 
  + Pipeline-yaml-stöd har lagts till för PythonScript Step, Adla Step, Databricks Step, DataTransferStep och AzureBatch Step

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + AutoArima är nu en förslagsbar pipeline endast för förhandsversion.
    + Förbättrad felrapportering för prognostiserade prognoser.
    + Förbättrade loggning med hjälp av anpassade undantag i stället för allmänna i prognostiserade uppgifter.
    + Kontrollen på den max_concurrent_iterations vara mindre än det totala antalet iterationer har tagits bort.
    + AutoML-modeller returnerar nu AutoMLExceptions
    + Den här versionen förbättrar körningsprestanda för automatiserade lokala maskininlärningskörningar.
  + **azureml-core**
    + Introducera Dataset.get_all(workspace), som returnerar en ordlista med `TabularDataset` objekten `FileDataset` och som nyckelats av deras registreringsnamn.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Introducera `parition_format` som argument för och `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . Partitionsinformationen för varje datasökväg extraheras till kolumner baserat på det angivna formatet. {column_name} skapar strängkolumn och {column_name:yyyy/MM/dd/HH/mm/ss} skapar datetime-kolumn, där "yyyy", "MM", "dd", "HH", "mm" och "ss" används för att extrahera år, månad, dag, timme, minut och sekund för datetime-typen. Den partition_format bör starta från positionen för den första partitionsnyckeln till slutet av filsökvägen. Till exempel med sökvägen ".. /USA/2019/01/01/data.csv" där partitionen är efter land och tid skapar partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv" strängkolumnen "Country" med värdet "USA" och datetime-kolumnen "PartitionDate" med värdet "2019-01-01".
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Introducera `partition_format` som argument för och `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . Partitionsinformationen för varje datasökväg extraheras till kolumner baserat på det angivna formatet. {column_name} skapar strängkolumn och {column_name:yyyy/MM/dd/HH/mm/ss} skapar datetime-kolumn, där "yyyy", "MM", "dd", "HH", "mm" och "ss" används för att extrahera år, månad, dag, timme, minut och sekund för datetime-typen. Den partition_format bör börja från positionen för den första partitionsnyckeln till slutet av filsökvägen. Till exempel med sökvägen ".. /USA/2019/01/01/data.csv" där partitionen är efter land och tid skapar partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv" strängkolumnen "Country" med värdet "USA" och datetime-kolumnen "PartitionDate" med värdet "2019-01-01".
    + `to_csv_files` metoderna `to_parquet_files` och har lagts till i `TabularDataset` . Dessa metoder möjliggör konvertering mellan `TabularDataset` en och en genom att konvertera data till filer i det angivna `FileDataset` formatet.
    + Logga automatiskt in i basavbildningsregistret när du sparar en Dockerfile som genererats av Model.package().
    + "gpu_support" är inte längre nödvändigt. AML identifierar och använder nu automatiskt nvidia docker-tillägget när det är tillgängligt. Den kommer att tas bort i en framtida version.
    + Stöd har lagts till för att skapa, uppdatera och använda PipelineDrafts.
    + Den här versionen förbättrar körningsprestanda för automatiserade lokala maskininlärningskörningar.
    + Användare kan köra frågor mot mått från körningshistoriken efter namn.
    + Förbättrade loggning med hjälp av anpassade undantag i stället för allmänna i prognostiserade uppgifter.
  + **azureml-explain-model**
    + Lade feature_maps parameter till den nya MimicWrapper så att användarna kan få förklaringar till råa funktioner.
    + Uppladdningar av datauppsättningar är nu inaktiverade som standard för förklaringsuppladdning och kan återaktiveras med upload_datasets= Sant
    + Filtreringsparametrarna "is_law" har lagts till i förklaringslistan och nedladdningsfunktioner.
    + Lägger till `get_raw_explanation(feature_maps)` metoden till både globala och lokala förklaringsobjekt.
    + Versionskontroll har lagts till på lightgbm med en tryckt varning om versionen som stöds nedan
    + Optimerad minnesanvändning vid batchbearbetning av förklaringar
    + AutoML-modeller returnerar nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Stöd har lagts till för att skapa, uppdatera och använda PipelineDrafts – kan användas för att underhålla föränderliga pipelinedefinitioner och använda dem interaktivt för att köra
  + **azureml-train-automl**
    + Skapad funktion för att installera specifika versioner av gpu-kompatibla pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0, pytorch-transformers, som krävs för att aktivera GPU/ XLNet i den fjärranslutna Python-körningsmiljön.
  + **azureml-train-core**
    + Tidigt fel med vissa definitionsfel för hyperparameterutrymme direkt i SDK:n i stället för på serversidan.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning SDK för dataförberedelser v1.1.14
+ **Felkorrigeringar och förbättringar**
  + Aktiverat skrivning till ADLS/ADLSGen2 med råsökväg och autentiseringsuppgifter.
  + En bugg som gjorde att inte fungerade för har `include_path=True` `read_parquet` åtgärdats.
  + Ett `to_pandas_dataframe()` fel som orsakats av undantaget "Ogiltigt egenskapsvärde: hostSecret" har åtgärdats.
  + En bugg där filer inte kunde läsas på DBFS i Spark-läge har åtgärdats.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK för Python v1.0.57
+ **Nya funktioner**
  + Har `TabularDataset` aktiverats för att användas av AutomatedML. Mer information om `TabularDataset` finns i https://aka.ms/azureml/howto/createdatasets .

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + Nu kan du uppdatera TLS/SSL-certifikatet för bedömningsslutpunkten som distribuerats i AKS-klustret för både Microsoft-genererat certifikat och kundcertifikat.
  + **azureml-automl-core**
    + Ett problem i AutoML har åtgärdats som gjorde att rader med saknade etiketter inte togs bort korrekt.
    + Förbättrad felloggning i AutoML; fullständiga felmeddelanden skrivs nu alltid till loggfilen.
    + AutoML har uppdaterat sitt paket fäst till att inkludera `azureml-defaults` `azureml-explain-model` , och `azureml-dataprep` . AutoML varnar inte längre om paketmatchningar (förutom `azureml-train-automl` paket).
    + Ett problem har åtgärdats `timeseries`  i där CV-delningar har olika storlek vilket gör att lagerplatsens beräkning misslyckas.
    + När vi körde ensemble-iteration för träningstypen Korsvalidering hade vi, om vi hade problem med att ladda ned de modeller som tränades på hela datauppsättningen, en inkonsekvens mellan modellvikterna och de modeller som matades in i röstnings ensemblen.
    + Åtgärdat felet som uppstod när tränings- och/eller valideringsetiketter (y och y_valid) angavs i form av Pandas-dataram men inte som numpy-matris.
    + Problemet med prognostiserade uppgifter när None (Ingen) påträffades i de booleska kolumnerna i indatatabellerna har åtgärdats.
    + Tillåt Att AutoML-användare tar bort träningsserier som inte är tillräckligt långa vid prognostiserade prognoser. – Tillåt Att AutoML-användare släpper korn från testuppsättningen som inte finns i träningsuppsättningen vid prognostiserade.
  + **azureml-core**
    + Problem med att blob_cache_timeout parameterordning har åtgärdats.
    + Externa passnings- och transformeringsfeltyper har lagts till för systemfel.
    + Stöd har lagts till för Key Vault hemligheter för fjärrkörningar. Lägg till klassen azureml.core.keyvault.Keyvault för att lägga till, hämta och lista hemligheter från det nyckelvalv som är associerat med din arbetsyta. Åtgärder som stöds är:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(namn, värde)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(namn)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Ytterligare metoder för att hämta standardnyckelvalv och hämta hemligheter under fjärrkörning:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(namn)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Ytterligare åsidosättningsparametrar har lagts till för CLI-kommandot submit-hyperdrive.
    + Förbättra tillförlitligheten för API-anrop genom att utöka återförsök till vanliga undantag i begärandebiblioteket.
    + Lägg till stöd för att skicka körningar från en skickad körning.
    + Ett problem med SAS-token som upphör att gälla i FileWatcher har åtgärdats, vilket gjorde att filerna inte längre laddades upp efter att deras ursprungliga token hade upphört att gälla.
    + Stöd för import av HTTP csv/tsv-filer i python SDK för datauppsättning.
    + Metoden Workspace.setup() har föraktats. Varningsmeddelande som visas för användarna föreslår att du använder create() eller get()/from_config() i stället.
    + Lade Environment.add_private_pip_wheel(), som gör det möjligt att ladda upp privata anpassade Python-paket till arbetsytan och på ett säkert sätt använda dem för `whl` att skapa/materialisera miljön.
    + Nu kan du uppdatera TLS/SSL-certifikatet för bedömningsslutpunkten som distribuerats i AKS-klustret för både Microsoft-genererat certifikat och kundcertifikat.
  + **azureml-explain-model**
    + Parametern har lagts till för att lägga till ett modell-ID i förklaringar vid uppladdning.
    + Taggning `is_raw` har lagts till i förklaringar i minnet och uppladdning.
    + Stöd och tester för pytorch har lagts till för paketet azureml-explain-model.
  + **azureml-opendatasets**
    + Stöd för identifiering och loggning av automatisk testmiljö.
    + Klasser har lagts till för att hämta befolkning i USA efter län och zip.
  + **azureml-pipeline-core**
    + Etikettegenskap har lagts till för portdefinitioner för indata och utdata.
  + **azureml-telemetry**
    + En felaktig telemetrikonfiguration har åtgärdats.
  + **azureml-train-automl**
    + En bugg har åtgärdats som gjorde att felet vid installationsfelet inte loggades i fältet "errors" för installationskörningen och därför inte lagrades i den överordnade körningen "errors".
    + Ett problem i AutoML har åtgärdats som gjorde att rader med saknade etiketter inte togs bort korrekt.
    + Tillåt Att AutoML-användare släpper träningsserier som inte är tillräckligt långa vid prognostiserade prognoser.
    + Tillåt Att AutoML-användare släpper korn från testuppsättningen som inte finns i träningsuppsättningen vid prognostiserade.
    + Nu passerar AutoMLStep konfiguration till backend för att undvika eventuella problem med `automl` ändringar eller tillägg av nya konfigurationsparametrar.
    + AutoML Data Guardrail är nu i offentlig förhandsversion. Användaren ser en Data Guardrail-rapport (för klassificerings-/regressionsuppgifter) efter träningen och kan även komma åt den via SDK API.
  + **azureml-train-core**
    + Stöd för 1.2 har lagts till i PyTorch Estimator.
  + **azureml-widgets**
    + Förbättrade felmatrisdiagram för klassificeringsträning.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning SDK för dataförberedelser v1.1.12
+ **Nya funktioner**
  + Listor med strängar kan nu skickas som indata till `read_*` metoder.

+ **Felkorrigeringar och förbättringar**
  + Prestandan för `read_parquet` har förbättrats när du kör i Spark.
  + Åtgärdat ett problem `column_type_builder` där misslyckades i händelse av en enda kolumn med tvetydiga datumformat.

### <a name="azure-portal"></a>Azure Portal
+ **Förhandsgranskningsfunktion**
  + Logg- och utdatafilströmning är nu tillgänglig för sidor med körningsinformation. Filerna strömmar uppdateringar i realtid när växlingsknappen för förhandsgranskning är aktiverad.
  + Möjligheten att ange en kvot på arbetsytenivå släpps i förhandsversion. AmlCompute-kvoter allokeras på prenumerationsnivå, men nu kan du distribuera kvoten mellan arbetsytor och allokera den för rättvis delning och styrning. Klicka bara på **bladet Användning + kvoter** i det vänstra **navigeringsfältet** på arbetsytan och välj fliken Konfigurera kvoter. Du måste vara prenumerationsadministratör för att kunna ange kvoter på arbetsytenivå eftersom det här är en åtgärd mellan arbetsytor.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK för Python v1.0.55

+ **Nya funktioner**
  + Tokenbaserad autentisering stöds nu för anrop till bedömningsslutpunkten som distribueras i AKS. Vi kommer att fortsätta att stödja den aktuella nyckelbaserade autentiseringen och användarna kan använda någon av dessa autentiseringsmekanismer i taget.
  + Möjlighet att registrera en bloblagring som finns bakom det virtuella nätverket (VNet) som ett datalager.

+ **Felkorrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdar ett fel där valideringsstorleken för CV-delningar är liten och resulterar i dåliga förutsagda kontra sanna diagram för regression och prognostiserade data.
    + Loggning av prognostiserade uppgifter på fjärrkörningarna förbättrades, nu får användaren ett omfattande felmeddelande om körningen misslyckades.
    + Fel för if `Timeseries` preprocess-flaggan är True har åtgärdats.
    + Gjort vissa prognostiserade felmeddelanden för dataverifiering mer användbara.
    + Minskad minnesförbrukning för AutoML-körningar genom att släppa och/eller lat inläsning av datauppsättningar, särskilt mellan process-uppsättningar
  + **azureml-contrib-explain-model**
    + Lade model_task-flagga till förklaring för att tillåta att användaren åsidosätter standardlogik för automatisk inferens för modelltyp
    + Widgetändringar: Installeras automatiskt med , ingen `contrib` mer `nbextension` installation/aktivera – stödförklaring med global egenskaps prioritet (till exempel permutativ)
    + Ändringar i instrumentpanelen: – Box-diagram och plottdiagram utöver ritytan på sammanfattningssidan – Mycket snabbare återendering av ritytan vid skjutreglageändringen "Top -k" – användbart meddelande som förklarar hur `beeswarm` top-k beräknas – Användbara anpassningsbara meddelanden i stället för diagram när data inte tillhandahålls `beeswarm`
  + **azureml-core**
    + Metoden Model.package() har lagts till för att skapa Docker-avbildningar och Dockerfiles som kapslar in modeller och deras beroenden.
    + Uppdaterade lokala webbtjänster för att acceptera InferenceConfigs som innehåller miljöobjekt.
    + Åtgärdat Model.register() som producerar ogiltiga modeller när "." (för den aktuella katalogen) skickas som model_path parameter.
    + Lägg Run.submit_child speglar funktionen Experiment.submit när körningen anges som överordnad för den skickade underordnade körningen.
    + Stöd för konfigurationsalternativ från Model.register i Run.register_model.
    + Möjlighet att köra JAR-jobb på ett befintligt kluster.
    + Nu kan instance_pool_id och cluster_log_dbfs_path parametrar.
    + Stöd har lagts till för att använda ett miljöobjekt vid distribution av en modell till en webbtjänst. Miljöobjektet kan nu anges som en del av InferenceConfig-objektet.
    + Lägg till appinsifht-mappning för nya regioner – centralus – westus – northcentralus
    + Dokumentation har lagts till för alla attribut i alla datalagerklasser.
    + Parametern blob_cache_timeout har lagts till i `Datastore.register_azure_blob_container` .
    + Lade save_to_directory och load_from_directory-metoder till azureml.core.environment.Environment.
    + Kommandona "az ml environment download" och "az ml environment register" har lagts till i CLI.
    + Lade Environment.add_private_pip_wheel metod.
  + **azureml-explain-model**
    + Datamängdsspårning har lagts till i Explanations med tjänsten Dataset (förhandsversion).
    + Minskad standard batchstorlek vid strömning av globala förklaringar från 10 000 till 100.
    + Lade model_task flagga till förklaring så att användaren kan åsidosätta standardlogik för automatisk slutsatsledning för modelltyp.
  + **azureml-mlflow**
    + En bugg i mlflow.azureml.build_image där kapslade kataloger ignoreras har åtgärdats.
  + **azureml-pipeline-steps**
    + Lade till möjlighet att köra JAR-jobb på Azure Databricks kluster.
    + Stöd har lagts instance_pool_id och cluster_log_dbfs_path parametrar för DatabricksStep-steget.
    + Stöd har lagts till för pipelineparametrar i DatabricksStep-steget.
  + **azureml-train-automl**
    + Har `docstrings` lagts till för de Ensemble-relaterade filerna.
    + Uppdaterade dokument till ett lämpligare språk för `max_cores_per_iteration` och `max_concurrent_iterations`
    + Loggning av prognosuppgifter på fjärrkörningarna förbättrades, nu får användaren ett omfattande felmeddelande om körningen misslyckades.
    + Har get_data från `automlstep` pipeline-notebook-datorn.
    + Startade `dataprep` supporten i `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning SDK för dataförberedelser v1.1.10

+ **Nya funktioner**
  + Nu kan du begära att specifika kontrollanter ska köras (till exempel histogram, punktdiagram osv.) på specifika kolumner.
  + Ett parallelliseringsargument har lagts till i `append_columns` . Om sant läses data in i minnet, men körningen körs parallellt. Om det är Falskt strömmas körningen men med en tråd.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK för Python v1.0.53

+ **Nya funktioner**
  + Automatiserad Machine Learning stöder nu träning av ONNX-modeller på fjärrbearbetningsmålet
  + Azure Machine Learning nu möjlighet att återuppta träningen från en tidigare körning, kontrollpunkt eller modellfiler.
    + Lär dig hur [du använder beräknare för att återuppta träningen från en tidigare körning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Felkorrigeringar och förbättringar**
  + **azure-cli-ml**
    + CLI-kommandona "model deploy" och "service update" accepterar nu parametrar, konfigurationsfiler eller en kombination av de två. Parametrar har företräde framför attribut i filer.
    + Modellbeskrivningen kan nu uppdateras efter registreringen
  + **azureml-automl-core**
    + Uppdatera NimbusML-beroende till version 1.2.0 (aktuell senaste).
    + Lägga till stöd för NimbusML-& som ska användas i AutoML-beräknare.
    + Åtgärda en bugg i urvalsproceduren i Ensemble som i onödan ökade den resulterande ensemblen även om poängen var konstant.
    + Aktivera återanvändning av vissa funktioner mellan CV-delningar för prognostiseringsuppgifter. Detta påskyndar körningen av installationsprogrammet med ungefär en faktor som n_cross_validations för dyra featuraliseringar som fördröjningar och rullande fönster.
    + Åtgärda ett problem om tiden ligger utanför det tidsperioder som stöds av Pandas. Nu höja vi ett DataException om tiden är mindre än pd. Timestamp.min eller större än pd. Timestamp.max
    + Prognostiserade data tillåter nu olika frekvenser i uppsättningar för träna och testa om de kan justeras. Till exempel kan "kvartalsvis med start i januari" och "kvartalsvis med start i oktober" justeras.
    + Egenskapen "parameters" har lagts till i TimeSeriesTransformer.
    + Ta bort gamla undantagsklasser.
    + I prognosuppgifter accepterar `target_lags` parametern nu ett enda heltalsvärde eller en lista med heltal. Om heltal har angetts skapas bara en fördröjning. Om en lista anges tas de unika värdena för fördröjningar. target_lags=[1, 2, 2, 4] skapar fördröjningar på en, två och fyra perioder.
    + Åtgärda buggen med att förlora kolumntyper efter omvandlingen (länkad bugg);
    + I kan y_query vara en objekttyp som innehåller `model.forecast(X, y_query)` None(s) i början (#459519).
    + Lägga till förväntade värden i `automl` utdata
  + **azureml-contrib-datadrift**
    +  Förbättringar av notebook-exempel, inklusive byte till azureml-opendatasets i stället för azureml-ib-opendatasets och prestandaförbättringar vid berikande av data
  + **azureml-ib-explain-model**
    + Argumentet transformationer har åtgärdats för ENDR-förklaring för råfunktions prioritet i paketet azureml-transformib-explain-model
    + Segmenteringar har lagts till i bildförklaringar i bildförklararen för paketet AzureML-ib-explain-model
    + Lägg till scipy-glesa-stöd för Felexplainer
    + har lagts till för att imitera förklaring när , för strömmande globala förklaringar i batchar för att förbättra körningstiden för `batch_size` `include_local=False` DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Korrigering för anrop set_featurizer_timeseries_params(): ändring av dict-värdetyp och null-kontroll – Lägg till notebook-fil för `timeseries`  featurizer
    + Uppdatera NimbusML-beroende till version 1.2.0 (aktuell senaste).
  + **azureml-core**
    + Möjligheten att koppla DBFS-datalager i AzureML CLI har lagts till
    + Buggen med uppladdning av datalager där en tom mapp skapades om den startades med har `target_path` åtgärdats `/`
    + Ett `deepcopy` problem i ServicePrincipalAuthentication har åtgärdats.
    + Kommandona "az ml environment show" och "az ml environment list" har lagts till i CLI.
    + Miljöer stöder nu att ange base_dockerfile som ett alternativ till en redan skapad base_image.
    + Den oanvända RunConfiguration-auto_prepare_environment har markerats som inaktuell.
    + Modellbeskrivningen kan nu uppdateras efter registreringen
    + Felkorrigering: Modell- och avbildnings borttagning innehåller nu mer information om att hämta överordnade objekt som är beroende av dem om borttagningen misslyckas på grund av ett uppströmsberoende.
    + En bugg som skrev ut tom varaktighet för distributioner som inträffar när en arbetsyta skapas för vissa miljöer har åtgärdats.
    + Förbättrade felundantag vid skapande av arbetsyta. Det innebär att användarna inte ser "Det går inte att skapa arbetsytan. Det gick inte att hitta..." som meddelandet och ser i stället det faktiska genereringsfelet.
    + Lägg till stöd för tokenautentisering i AKS-webbtjänster.
    + Lägg `get_token()` till -metod i `Webservice` -objekt.
    + CLI-stöd har lagts till för att hantera maskininlärningsdatauppsättningar.
    + `Datastore.register_azure_blob_container` tar nu eventuellt ett värde (i sekunder) som konfigurerar blobfuse-monteringsparametrarna för att möjliggöra `blob_cache_timeout` förfallotid för det här datalagringsminnet. Standardvärdet är ingen tidsgräns, till exempel när en blob läses, den finns kvar i den lokala cachen tills jobbet har slutförts. De flesta jobb föredrar den här inställningen, men vissa jobb behöver läsa mer data från en stor datauppsättning än vad som får plats på deras noder. För de här jobben hjälper det att justera den här parametern. Var försiktig när du finjusterar den här parametern: om du anger värdet för lågt kan det resultera i dåliga prestanda, eftersom data som används i en epok kan upphöra att gälla innan de används igen. Alla läsningar görs från bloblagring/nätverk i stället för den lokala cachen, vilket påverkar träningstiderna negativt.
    + Modellbeskrivningen kan nu uppdateras korrekt efter registreringen
    + Modell- och bildborttagning ger nu mer information om överordnade objekt som är beroende av dem, vilket gör att borttagningen misslyckas
    + Förbättra resursanvändningen för fjärrkörningar med hjälp av azureml.mlflow.
  + **azureml-explain-model**
    + Argumentet transformationer har åtgärdats för ENDR-förklaring för råfunktions prioritet i paketet azureml-transformib-explain-model
    + lägg till scipy-glesa-stöd för Felexplainer
    + lade till form linjär förklarare wrapper, samt en annan nivå till tabellförklarare för att förklara linjära modeller
    + for mimic explainer in explainer in explain model library, fixed error when include_local=False for sparse data input
    + lägga till förväntade värden i `automl` utdata
    + fast permutationsfunktions prioritet när transformeringsargument anges för att hämta råfunktions prioritet
    + har lagts till för att efterlikna förklaringen när , för strömmande globala förklaringar i batchar för att förbättra körningstiden för `batch_size` `include_local=False` DecisionTreeExplainableModel
    + för modellförklarbarhetsbiblioteket, fasta svartbox-förklarare där pandas-dataframe-indata krävs för förutsägelse
    + Åtgärdat en bugg `explanation.expected_values` där ibland skulle returnera ett flyttal i stället för en lista med ett flyttal i den.
  + **azureml-mlflow**
    + Förbättra prestanda för mlflow.set_experiment(experiment_name)
    + Åtgärda fel vid användning av InteractiveLoginAuthentication för mlflow-tracking_uri
    + Förbättra resursanvändningen för fjärrkörningar med hjälp av azureml.mlflow.
    + Förbättra dokumentationen för paketet azureml-mlflow
    + Korrigeringsfel där mlflow.log_artifacts("my_dir") sparar artefakter under "my_dir/<artifact-paths>" i stället för "<artefaktsökvägar>"
  + **azureml-opendatasets**
    + Fäst `pyarrow` på gamla versioner `opendatasets` (<0.14.0) på grund av minnesproblemet som nyligen introducerades där.
    + Flytta azureml-contrib-opendatasets till azureml-opendatasets.
    + Tillåt att öppna datamängdsklasser registreras på Azure Machine Learning arbetsyta och utnyttja AML-datauppsättningsfunktioner sömlöst.
    + Förbättra NoaaIsdWeather-prestandan i icke-SPARK-versionen avsevärt.
  + **azureml-pipeline-steps**
    + DBFS-datalager stöds nu för indata och utdata i DatabricksStep.
    + Uppdaterad dokumentation för Azure Batch Step med avseende på indata/utdata.
    + I AzureBatchStep har *delete_batch_job_after_finish* standardvärdet till *true*.
  + **azureml-telemetry**
    +  Flytta azureml-contrib-opendatasets till azureml-opendatasets.
    + Tillåt att öppna datamängdsklasser registreras på Azure Machine Learning arbetsyta och utnyttja AML-datauppsättningsfunktioner sömlöst.
    + Förbättra NoaaIsdWeather-prestandan i icke-SPARK-versionen avsevärt.
  + **azureml-train-automl**
    + Uppdaterad dokumentation om get_output återspeglar den faktiska returtypen och ger ytterligare information om hämtning av nyckelegenskaper.
    + Uppdatera NimbusML-beroende till version 1.2.0 (aktuell senaste).
    + lägga till förväntade värden i `automl` utdata
  + **azureml-train-core**
    + Strängar accepteras nu som beräkningsmål för automatisk justering av hyperparametrar
    + Den oanvända RunConfiguration-auto_prepare_environment har markerats som inaktuell.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning SDK för dataförberedelser v1.1.9

+ **Nya funktioner**
  + Stöd har lagts till för att läsa en fil direkt från en http- eller https-URL.

+ **Felkorrigeringar och förbättringar**
  + Förbättrat felmeddelande vid försök att läsa en Parquet-datauppsättning från en fjärrkälla (som inte stöds för närvarande).
  + En bugg har åtgärdats vid skrivning till Parquet-filformatet i ADLS Gen 2 och uppdatering av ADLS Gen2-containernamnet i sökvägen.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuellt gränssnitt
+ **Förhandsgranskningsfunktioner**
  + Lade till modulen "Kör R-skript" i det visuella gränssnittet.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK för Python v1.0.48

+ **Nya funktioner**
  + **azureml-opendatasets**
    + **azureml-ib-opendatasets** är nu tillgängligt som **azureml-opendatasets**. Det gamla paketet kan fortfarande fungera, men vi rekommenderar att du använder **azureml-opendatasets** framöver för bättre funktioner och förbättringar.
    + Med det här nya paketet kan du registrera öppna datauppsättningar som datauppsättningar Azure Machine Learning en arbetsyta och utnyttja de funktioner som Dataset erbjuder.
    + Den innehåller också befintliga funktioner som att använda öppna datauppsättningar som Pandas/SPARK-dataramar och platskopplingar för vissa datauppsättningar, till exempel väder.

+ **Förhandsgranskningsfunktioner**
    + HyperDriveConfig kan nu acceptera pipeline-objekt som en parameter för att stödja hyperparameterjustering med hjälp av en pipeline.

+ **Felkorrigeringar och förbättringar**
  + **azureml-train-automl**
    + Buggen om att förlora kolumntyper efter transformeringen har åtgärdats.
    + Buggen har åtgärdats så y_query att den är en objekttyp som innehåller None(s) i början.
    + Åtgärdade problemet i urvalsproceduren i Ensemble som i onödan ökade den resulterande ensemblen även om poängen var konstant.
    + Åtgärdat problemet med tillåt list_models blockera list_models i AutoMLStep.
    + Åtgärdat problemet som förhindrade användningen av förbearbetning när AutoML skulle ha använts i Azure ML-pipelines.
  + **azureml-opendatasets**
    + Flyttade azureml-ib-opendatasets till azureml-opendatasets.
    + Tillåtet att öppna datauppsättningsklasser registreras på Azure Machine Learning arbetsyta och utnyttja AML-datauppsättningsfunktioner sömlöst.
    + Förbättrade NoaaIsdWeather utökar prestandan i icke-SPARK-versionen avsevärt.
  + **azureml-explain-model**
    + Uppdaterad onlinedokumentation för tolkningsobjekt.
    + Har lagts till för att efterlikna förklaring när , för strömning av globala förklaringar i batchar för att förbättra körningstiden för `batch_size` `include_local=False` DecisionTreeExplainableModel för modellförklarbarhetsbiblioteket.
    + Åtgärdat problemet där `explanation.expected_values` ibland returnerade ett flyttal i stället för en lista med ett flyttal i det.
    + Förväntade värden har lagts till för `automl` utdata för imiteraren i förklara modellbiblioteket.
    + Permutationsfunktions prioritet har åtgärdats när transformationsargument har angetts för att hämta råfunktions prioritet.
  + **azureml-core**
    + Möjligheten att koppla DBFS-datalager i AzureML CLI har lagts till.
    + Åtgärdat problemet med uppladdning av datalager där en tom mapp skapades om `target_path` den startades med `/` .
    + Aktiverad jämförelse av två datauppsättningar.
    + Modell- och avbildnings borttagning innehåller nu mer information om att hämta överordnade objekt som är beroende av dem om borttagningen misslyckas på grund av ett uppströmsberoende.
    + Inaktuella den oanvända RunConfiguration-inställningen i auto_prepare_environment.
  + **azureml-mlflow**
    + Förbättrad resursanvändning för fjärrkörningar som använder azureml.mlflow.
    + Förbättrade dokumentationen för paketet azureml-mlflow.
    + Åtgärdat problemet där mlflow.log_artifacts("my_dir") skulle spara artefakter under "my_dir/artifact-paths" i stället för "artifact-paths".
  + **azureml-pipeline-core**
    + Parameter hash_paths för alla pipelinesteg är inaktuell och kommer att tas bort i framtiden. Som standard hashas innehållet i source_directory (förutom filer som anges i `.amlignore` eller `.gitignore` )
    + Fortsatt förbättring av modul- och modulsteg för att stödja beräkningstypsspecifika moduler, för att förbereda för RunConfiguration-integrering och andra ändringar för att låsa upp beräkningstypsspecifik modulanvändning i pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Förbättrad dokumentation för indata/utdata.
    + AzureBatchStep: Delete_batch_job_after_finish standardvärdet till true.
  + **azureml-train-core**
    + Strängar accepteras nu som beräkningsmål för automatisk justering av hyperparametrar.
    + Inaktuella den oanvända RunConfiguration-inställningen i auto_prepare_environment.
    + Inaktuella `conda_dependencies_file_path` parametrar `pip_requirements_file_path` och till förmån för `conda_dependencies_file` respektive `pip_requirements_file` .
  + **azureml-opendatasets**
    + Förbättra NoaaIsdWeather-berika prestanda i icke-SPARK-versionen avsevärt.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK för Python v1.0.33.

+ Azure ML Maskinvaruaccelererade modeller på [FPGA är](how-to-deploy-fpga-web-service.md) allmänt tillgängligt.
  + Nu kan du [använda paketet azureml-accel-models för](how-to-deploy-fpga-web-service.md) att:
    + Träna vikterna för ett djupt neuralt nätverk som stöds (ResNet 50, ResNet 152, DenseNet-121, VGG-16 och SSD-VGG)
    + Använda överföringsutbildning med DNN som stöds
    + Registrera modellen med Modellhantering Service och containerisera modellen
    + Distribuera modellen till en virtuell Azure-dator med ett FPGA i ett Azure Kubernetes Service-kluster (AKS)
  + Distribuera containern till en [Azure Data Box Edge-serverenhet](../databox-online/azure-stack-edge-overview.md)
  + Poängse dina data med gRPC-slutpunkten med det här [exemplet](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

+ Funktionssökning för att aktivera dynamiskt tillägg för :::no-loc text="featurizers"::: prestandaoptimering. Ny: :::no-loc text="featurizers"::: arbetsinbäddningar, bevisvikt, målkodningar, textmålkodning, klusteravstånd
+ Smart CV för att hantera träna/giltiga delningar i automatiserad ML
+ Få ändringar i minnesoptimering och prestandaförbättringar för körning
+ Prestandaförbättring i modellförklaring
+ ONNX-modellkonvertering för lokal körning
+ Stöd för underexempel har lagts till
+ Intelligent stopp när inga avslutningsvillkor har definierats
+ Staplade ensembler

+ Prognostisering för tidsserier
  + Ny funktion för förutsägelseprognos
  + Nu kan du använda korsvalidering med rullande ursprung för tidsseriedata
  + Nya funktioner har lagts till för att konfigurera tidsseriefördröjningar
  + Nya funktioner har lagts till för att stödja funktioner för rullande fönsteraggregat
  + Ny helgdagsidentifiering och -funktionerare när landskod har definierats i experimentinställningarna

+ Azure Databricks
  + Aktiverade tidsserieprognoser och modellförklarings-/tolkningsfunktioner
  + Nu kan du avbryta och återuppta (fortsätta) automatiserade ML-experiment
  + Stöd har lagts till för bearbetning med flera kärnor

### <a name="mlops"></a>MLOps
+ **Lokal distribution & felsökning för bedömning av containrar**<br/> Nu kan du distribuera en ML-modell lokalt och iterera snabbt på din bedömningsfil och dina beroenden så att de fungerar som förväntat.

+ **Introducerade InferenceConfig & Model.deploy()**<br/> Modelldistribution har nu stöd för att ange en källmapp med ett startskript, samma som en RunConfig.  Dessutom har modelldistribution förenklats till ett enda kommando.

+ **Git-referensspårning**<br/> Kunder har begärt grundläggande Git-integreringsfunktioner under en viss tid eftersom det bidrar till att upprätthålla en fullständig granskningslogg. Vi har implementerat spårning över större entiteter i Azure ML för Git-relaterade metadata (repo, commit, clean state). Den här informationen samlas in automatiskt av SDK och CLI.

+ **Modellprofilering & valideringstjänst**<br/> Kunder klagar ofta på att det är svårt att ändra storlek på beräkningen som är associerad med inferenstjänsten. Med vår modellprofileringstjänst kan kunden tillhandahålla exempelindata och vi profilerar över 16 olika processor-/minneskonfigurationer för att fastställa optimal storlek för distributionen.

+ **Bring your own base image for inference (Ta med din egen basavbildning för slutsatsledning)**<br/> Ett annat vanligt klagomål var att det var svårt att flytta från experimentering till slutsatsliga RE-delningsberoenden. Med vår nya funktion för delning av basavbildningar kan du nu återanvända dina basavbildningar för experimentering, beroenden och allt, för slutsatsledning. Detta bör påskynda distributioner och minska avståndet från den inre till den yttre loopen.

+ **Förbättrad upplevelse för generering av Swagger-schema**<br/> Vår tidigare swagger-genereringsmetod var felbenägen och omöjligt att automatisera. Vi har ett nytt in-line sätt att generera swagger-scheman från valfri Python-funktion via -decorators. Vi har öppen källkod för den här koden och vårt schemagenereringsprotokoll är inte kopplat till Azure ML-plattformen.

+ **Azure ML CLI är allmänt tillgänglig (GA)**<br/> Modeller kan nu distribueras med ett enda CLI-kommando. Vi har fått vanlig kundfeedback om att ingen distribuerar en ML-modell från en Jupyter Notebook. [**CLI-referensdokumentationen**](./reference-azure-machine-learning-cli.md) har uppdaterats.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK för Python v1.0.30.

introducerades [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) för att lägga till en ny version av en publicerad pipeline samtidigt som samma slutpunkt bibehålls.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Du kan nu skicka om en befintlig skriptkörning i ett befintligt fjärrbearbetningskluster.
  + Du kan nu köra en publicerad pipeline med nya parametrar på fliken Pipeliner.
  + Kör information har nu stöd för ett nytt visningsprogram för ögonblicksbilder. Du kan visa en ögonblicksbild av katalogen när du skickade en specifik körning. Du kan också ladda ned anteckningsboken som skickades för att starta körningen.
  + Nu kan du avbryta överordnade körningar från Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK för Python v1.0.23

+ **Nya funktioner**
  + Sdk Azure Machine Learning stöder nu Python 3.7.
  + Azure Machine Learning DNN-beräknare har nu inbyggt stöd för flera versioner. Till exempel accepterar beräknaren nu en parameter och användarna kan ange `TensorFlow` `framework_version` version 1.10 eller 1.12. Om du vill se en lista över de versioner som stöds av din aktuella SDK-version anropar du för `get_supported_versions()` önskad framework-klass (till exempel `TensorFlow.get_supported_versions()` ).
  En lista över de versioner som stöds av den senaste SDK-versionen finns i [DNN-beräkningsdokumentationen.](/python/api/azureml-train-core/azureml.train.dnn)

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK för Python v1.0.21

+ **Nya funktioner**
  + Med *azureml.core.Run.create_children-metoden* kan du skapa flera underordnade körningar med korta svarstider med ett enda anrop.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK för Python v1.0.18

 + **Ändringar**
   + Paketet azureml-tensorboard ersätter azureml-contrib-tensorboard.
   + Med den här versionen kan du konfigurera ett användarkonto i ditt hanterade beräkningskluster (amlcompute) när du skapar det. Detta kan göras genom att skicka dessa egenskaper i etableringskonfigurationen. Mer information finns i [SDK-referensdokumentationen.](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning SDK för dataförberedelser v1.0.17

+ **Nya funktioner**
  + Stöder nu tillägg av två numeriska kolumner för att generera en resulterande kolumn med hjälp av uttrycksspråket.

+ **Felkorrigeringar och förbättringar**
  + Förbättrade dokumentationen och parameterkontrollen för random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning SDK för dataförberedelser v1.0.16

+ **Felkorrigering**
  + Ett autentiseringsproblem med tjänstens huvudnamn som orsakades av en API-ändring har åtgärdats.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK för Python v1.0.17

+ **Nya funktioner**
  + Azure Machine Learning har nu förstklassigt stöd för populära DNN Framework Chainer. Med [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) hjälp av klassanvändare kan du enkelt träna och distribuera Chainer-modeller.
    + Lär dig hur [du kör distribuerad träning med ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Lär dig hur [du kör justering av hyperparametrar med Chainer med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines har ytterligare möjlighet att utlösa en pipelinekörning baserat på ändringar i datalager. Anteckningsboken för [pipelineschemat](https://aka.ms/pl-schedule) uppdateras för att demonstrera den här funktionen.

+ **Felkorrigeringar och förbättringar**
  + Vi har lagt till stöd i Azure Machine Learning-pipelines för att ställa in source_directory_data_store-egenskapen till ett önskat datalager (till exempel en bloblagring) på [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) som levereras till [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep). Som standard använder Steg Azure File Store som ett datalager som backing, vilket kan leda till begränsningsproblem när ett stort antal steg körs samtidigt.

### <a name="azure-portal"></a>Azure Portal

+ **Nya funktioner**
  + Ny dra och släpp-tabellredigeringsupplevelse för rapporter. Användare kan dra en kolumn från brunnen till det tabellområde där en förhandsgranskning av tabellen visas. Kolumnerna kan ordnas om.
  + Nytt loggfilvisningsprogram
  + Länkar till experimentkörningar, beräkning, modeller, avbildningar och distributioner från fliken Aktiviteter

## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](overview-what-is-azure-ml.md).
