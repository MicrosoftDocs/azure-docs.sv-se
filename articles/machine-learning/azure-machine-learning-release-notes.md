---
title: Vad är nytt i versionen?
titleSuffix: Azure Machine Learning
description: 'Lär dig mer om de senaste uppdateringarna för Azure Machine Learning och Machine Learning och data prepare SDK: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 13ef91af5f7ed8398e3d23400fa5e9df467ce6bc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210902"
---
# <a name="azure-machine-learning-release-notes"></a>Viktig information om Azure Machine Learning

I den här artikeln får du lära dig mer om Azure Machine Learning-versioner.  Information om fullständiga SDK-referenser finns på Azure Machine Learning huvud sidan [**för SDK för python**](/python/api/overview/azure/ml/intro) -referens.

__RSS-feed__: Håll dig informerad när den här sidan uppdateras genom att kopiera och klistra in följande webbadress i feed-läsaren: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Azure Machine Learning SDK för python v-1.26.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Ett problem har åtgärd ATS där Naive-modeller rekommenderas i AutoMLStep-körningar och fungerar inte med fördröjnings-eller rullande fönster funktioner. Dessa modeller rekommenderas inte när du har angett en mål storlek för lags eller målets rullande fönster.
    +  Ändrade konsol utdata när du skickar en AutoML-körning för att visa en Portal länk till körningen.
  + **azureml-core**
    + HDFS-läge har lagts till i dokumentationen.
    + Stöd har lagts till för att förstå fil data uppsättning partitioner baserat på BLOB-struktur.
    + Stöd har lagts till för uppdatering av container register som är kopplat till AzureML-arbetsytan.
    + Föråldrade miljöattribut under DockerSection-"Enabled", "shared_volume" och "argument" är en del av DockerConfiguration i RunConfiguration nu.
    + Dokumentation om uppdatering av pipeline CLI-klon
    + Uppdaterade Portal-URI: er som innehåller klient för autentisering
    + Experiment namnet har tagits bort från körnings-URI: er för att undvika omdirigeringar 
    + Ett experiment-URO har uppdaterats för användning av experiment-ID.
    + Fel korrigeringar för att bifoga fjärrberäkning med AzureML CLI.
    + Uppdaterade Portal-URI: er för autentisering av klienter.
    + Uppdaterad experiment-URI för att använda experiment-ID.
  + **azureml-interpret**
    + azureml – tolka uppdaterad för att använda 0.17.0 för tolkning – community
  + **azureml-opendatasets**
    + Start datum och slutdatum typ verifiering och fel indikation om det inte är en datetime-typ.
  + **azureml – parallell-körning**
    + [Experimentell funktion] Lägg till `partition_keys` parametern till ParallelRunConfig, om den anges, partitioneras indata-dataset i mini-batchar med de nycklar som anges av den. Det kräver att alla indata-datauppsättningar är partitionerade data uppsättningar.
  + **azureml-pipeline-steps**
    + Bugfix-stöd path_on_compute när du skickar data uppsättnings konfigurationen som nedladdning.
    + Föråldrade RScriptStep i stället för att använda CommandStep för att köra R-skript i pipeliner. 
    + Föråldrade EstimatorStep till förmån för att använda CommandStep för att köra ML-utbildning (inklusive distribuerad utbildning) i pipelines.
  + **azureml – SDK**
    + Uppdatera python_requires till < 3,9 för azureml-SDK
  + **azureml-train-automl-client**
    +  Ändrade konsol utdata när du skickar en AutoML-körning för att visa en Portal länk till körningen.
  + **azureml-train-core**
    + Föråldrade DockerSection ' Enabled ', ' shared_volume ' och ' argument ' i stället för att använda DockerConfiguration med ScriptRunConfig.
    +  Använd Azures öppna data uppsättningar för MNIST-datauppsättning
    + HyperDrive-fel meddelanden har uppdaterats.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Azure Machine Learning SDK för python v-1.25.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    +  Ändrade konsol utdata när du skickar en AutoML-körning för att visa en Portal länk till körningen.
  + **azureml-core**
    + Börjar stödja uppdatering av container Registry för arbets ytan i SDK och CLI
    + Föråldrade DockerSection ' Enabled ', ' shared_volume ' och ' argument ' i stället för att använda DockerConfiguration med ScriptRunConfig.
    + Dokumentation om uppdatering av pipeline CLI-klon
    + Uppdaterade Portal-URI: er som innehåller klient för autentisering
    + Experiment namnet har tagits bort från körnings-URI: er för att undvika omdirigeringar
    + Ett experiment-URO har uppdaterats för användning av experiment-ID.
    + Fel korrigeringar för att ansluta fjärrberäkning med AZ cli
    + Uppdaterade Portal-URI: er för autentisering av klienter.
    + Stöd har lagts till för att förstå fil data uppsättning partitioner baserat på BLOB-struktur.
  + **azureml-interpret**
    + azureml – tolka uppdaterad för att använda 0.17.0 för tolkning – community
  + **azureml-opendatasets**
    + Start datum och slutdatum typ verifiering och fel indikation om det inte är en datetime-typ.
  + **azureml-pipeline-core**
    + Bugfix-stöd path_on_compute när du skickar data uppsättnings konfigurationen som nedladdning.
  + **azureml-pipeline-steps**
    + Bugfix-stöd path_on_compute när du skickar data uppsättnings konfigurationen som nedladdning.
    + Föråldrade RScriptStep i stället för att använda CommandStep för att köra R-skript i pipeliner. 
    + Föråldrade EstimatorStep till förmån för att använda CommandStep för att köra ML-utbildning (inklusive distribuerad utbildning) i pipelines.
  + **azureml-train-automl-runtime**
    +  Ändrade konsol utdata när du skickar en AutoML-körning för att visa en Portal länk till körningen.
  + **azureml-train-core**
    + Föråldrade DockerSection ' Enabled ', ' shared_volume ' och ' argument ' i stället för att använda DockerConfiguration med ScriptRunConfig.
    + Använd Azures öppna data uppsättningar för MNIST-datauppsättning
    + HyperDrive-fel meddelanden har uppdaterats.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning Studio Notebooks Experience (mars Update)
+ **Nya funktioner**
  + Rendera CSV/TSV. Användarna kommer att kunna återge och TSV/CSV-filen i ett rutnäts format för enklare data analys. 
  + SSO-autentisering för beräknings instans. Användare kan nu enkelt autentisera alla nya beräknings instanser direkt i anteckningsbok-gränssnittet, vilket gör det enklare att autentisera och använda Azure SDK: er direkt i AzureML. 
  + Beräknings instans mått. Användarna kan visa beräknings mått som processor användning och minne via Terminal.
  + Fil information. Användarna kan nu se fil information, inklusive senast ändrad tid och fil storlek genom att klicka på de tre punkterna bredvid en fil.

+ **Fel korrigeringar och förbättringar**
  + Förbättrad sid inläsnings tid.
  + Förbättrad prestanda.
  + Förbättrad hastighet och kernel-tillförlitlighet.
  + Få vertikal fast egendom genom att permanent flytta anteckningsbok-filfönstret uppåt
  + Länkar kan nu klickas bara i Terminal
  + Förbättrade IntelliSense-prestanda


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK för python v-1.24.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Bakåtkompatibla importer har tagits bort från `azureml.automl.core.shared` . Modulen hittades inte fel i `azureml.automl.core.shared` namn området kan lösas genom att importera från `azureml.automl.runtime.shared` .
  + **azureml-contrib-automl-DNN-vision**
    + Yolo modell med exponerad objekt identifiering.
  + **azureml-contrib-dataset**
    + Tillagda funktioner för att filtrera tabell data uppsättningar efter kolumn värden och fil data uppsättningar efter metadata.
  + **azureml-contrib-fairness**
    + Ta med JSON-schema i hjul för `azureml-contrib-fairness`
  + **azureml-contrib-Mir**
    + Om du anger show_output till sant när du distribuerar modeller, kommer konfiguration av konfiguration och distribution att spelas upp innan begäran skickas till servern.
  + **azureml-core**
    + Tillagda funktioner för att filtrera tabell data uppsättningar efter kolumn värden och fil data uppsättningar efter metadata.
    + Tidigare var det möjligt för användare att skapa etablerings inställningar för ComputeTarget som inte uppfyller lösen ords styrke kraven för `admin_user_password` fältet (dvs. det måste innehålla minst tre av följande: 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken från följande uppsättning: ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` ). Om användaren har skapat en konfiguration med ett svagt lösen ord och kört ett jobb med den konfigurationen, kunde inte jobbet köras vid körning. Anropet till kommer nu att skicka `AmlCompute.provisioning_configuration` ett `ComputeTargetException` meddelande med ett fel meddelande som förklarar kraven på lösen ords säkerhet. 
    + Dessutom var det också möjligt att ange en konfiguration med ett negativt antal maximalt antal noder. Det är inte längre möjligt att göra detta. Nu `AmlCompute.provisioning_configuration` kommer att utlösa ett `ComputeTargetException` om `max_nodes` argumentet är ett negativt heltal.
    + Med inställningen show_output till sant när du distribuerar modeller visas konfigurations konfiguration och distributions konfiguration.
    + Om du anger show_output true när du ska slutföra modell distributionen visas förloppet för distributions åtgärden.
    + Tillåt Kunden angiven AzureML auth config Directory via miljö variabeln: AZUREML_AUTH_CONFIG_DIR
    + Tidigare var det möjligt att skapa en etablerings konfiguration med minsta antal noder som är mindre än det maximala antalet noder. Jobbet kördes men fungerar inte vid körning. Den här buggen har nu åtgärd ATS. Om du nu försöker skapa en etablerings konfiguration med `min_nodes < max_nodes` SDK: n kommer att generera en `ComputeTargetException` .
  + **azureml-interpret**
    + korrigera förklarings instrument panel visar inte mängd funktions prioriteter för sparse-utformade förklaringar
    + optimerad minnes användning för ExplanationClient i azureml-tolknings paket
  + **azureml-train-automl-client**
    +  Fixed show_output = false för att returnera kontroll till användaren vid körning med Spark.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning Studio Notebooks Experience (februari Update)
+ **Nya funktioner**
  + [Ursprunglig Terminal (ga)](./how-to-access-terminal.md). Användarna kommer nu att ha åtkomst till en integrerad Terminal och git-åtgärd via den integrerade terminalen.
  + [Antecknings kods tycken (för hands version)](https://azure.github.io/azureml-web/docs/vs-code-snippets/snippets). Vanliga utdrag för Azure ML-kod är nu tillgängliga. Navigera till panelen kodfragment, som är tillgänglig via verktygsfältet eller aktivera menyn infragmentets kod avsnitt med Ctrl + blank steg.  
  + [Kortkommandon.](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts) Fullständig paritet med kortkommandon som är tillgängliga i Jupyter. 
  + Ange cell parametrar. Visar de användare som har celler i en antecknings bok som är parameter celler och kan köra parameterstyrda antecknings böcker via [Papermill](https://github.com/nteract/papermill) på beräknings instansen.
  + Terminal-och kernel-sessionshanteraren: användare kommer att kunna hantera alla kärnor och Terminal-sessioner som körs på sin beräkning.
  + Delnings knapp. Användarna kan nu dela en fil i anteckningsbok-Utforskaren genom att högerklicka på filen och använda knappen Dela.


+ **Fel korrigeringar och förbättringar**
  + Förbättrade sid inläsnings tider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kernel-tillförlitlighet
  + Lagt till snurrande hjul för att visa förloppet för alla pågående [beräknings instans åtgärder](./how-to-run-jupyter-notebooks.md#status-indicators).
  + Högerklicka på Utforskaren. Om du högerklickar på en fil öppnas fil åtgärder nu. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK för python v-1.23.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-core**
    + [Experimentell funktion] Lägg till stöd för att länka Synapse-arbetsytan till AML som en länkad tjänst
    + [Experimentell funktion] Lägg till stöd för att koppla Synapse Spark-poolen till AML som en beräkning
    + [Experimentell funktion] Lägg till stöd för identitets baserad data åtkomst. Användare kan registrera data lager eller data uppsättningar utan att ange autentiseringsuppgifter. I sådana fall används användarens AAD-token eller hanterad identitet för Compute Target för autentisering. Lär dig mer [här](./how-to-identity-based-data-access.md).
  + **azureml-pipeline-steps**
    + [Experimentell funktion] Lägg till stöd för [SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)
  + **azureml-synapse**
    + [Experimentell funktion] Lägg till stöd för Spark Magic för att köra interaktiv session i Synapse Spark-poolen.
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + I den här uppdateringen har vi lagt till Holt vinter till Exponentiell utjämning till Prognosticering Toolbox i AutoML SDK. Med en viss tids serie är den bästa modellen vald av [AICc (korrigerade Akaike)](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) och returneras.
    + AutoML kommer nu att generera två loggfiler i stället för en. Logg instruktioner skickas till en eller flera beroende på vilken process som logg satsen genererades i.
    + Ta bort onödig förutsägelse i exempel under modell träning med kors valideringar. Detta kan minska modell inlärnings tiden i vissa fall, särskilt för tids serie prognos modeller.
  + **azureml-contrib-fairness**
    + Lägg till ett JSON-schema för dashboardDictionary-uppladdningar.
  + **azureml-contrib-interpret**
    + azureml-contrib-tolka README uppdateras för att avspegla att paketet tas bort i nästa uppdatering efter att ha varit inaktuellt sedan oktober använder du azureml-tolka paket i stället
  + **azureml-core**
    + Tidigare var det möjligt att skapa en etablerings konfiguration med minsta antal noder som är mindre än det maximala antalet noder. Detta har nu åtgärd ATS. Om du nu försöker skapa en etablerings konfiguration med `min_nodes < max_nodes` SDK: n kommer att generera en `ComputeTargetException` .
    +  Korrigerar fel i wait_for_completion i AmlCompute som orsakade att funktionen returnerade kontroll flödet innan åtgärden slutfördes
    + Run. Failed () är nu föråldrad, Använd Run. tag () för att markera kör som misslyckad eller Använd Run. Cancel () för att markera körningen som avbruten.
    + Visa fel meddelandets "miljö namn förväntad Str {} ", hittades när det angivna miljö namnet inte är en sträng.
  + **azureml-train-automl-client**
    + En bugg har åtgärd ATS som hindrade AutoML-experiment som utfördes på Azure Databricks-kluster från att avbrytas.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK för python v-1.22.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdat fel där ett extra pip-beroende har lagts till i Conda YML-filen för visioner-modeller.
  + **azureml-automl-runtime**
    + Ett fel har åtgärd ATS där klassiska prognos modeller (t. ex. AutoArima) kan ta emot tränings data där rader med imputerade mål värden inte fanns. Detta bröt mot data kontraktet för dessa modeller. * Korrigerade olika buggar med beteende vid fördröjning i tids seriernas isolerings operator. Tidigare markerades inte alla imputerade rader på rätt sätt, vilket innebär att inte alltid genererar rätt värde för förekomst fördröjning. Även vissa kompatibilitetsproblem har åtgärd ATS mellan fördröjnings operatorn och den rullande fönster operatorn med beteendet vänte-vid-förekomst. Detta ledde tidigare till att den rullande fönster operatorn tar bort några rader från tränings data som den annars bör använda.
  + **azureml-core**
    + Lägger till stöd för token-autentisering per mål grupp.
    + Lägg till `process_count` i [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) för att stödja PyTorch-jobb med flera processer.
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) nu till GA och inte längre experiment.
    + [ParallelRunConfig](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig): Lägg till argumentet allowed_failed_count och allowed_failed_percent för att kontrol lera fel tröskeln på Mini batchnivå. Fel tröskeln har 3 varianter nu:
       + error_threshold-antalet tillåtna misslyckade mini batch-objekt. 
       + allowed_failed_count-antalet tillåtna Felaktiga mini-batchar; 
       + allowed_failed_percent-procent tillåtna misslyckade mini batchar. 
       
       Ett jobb stoppas om det överskrider något av dem. error_threshold krävs för att hålla den bakåtkompatibilitet. Ange värdet-1 för att ignorera det.
    + Fast utrymmes hantering i AutoMLStep-namn.
    + ScriptRunConfig stöds nu av HyperDriveStep
  + **azureml-train-core**
    + HyperDrive-körningar som anropas från en ScriptRun kommer nu att anses vara en underordnad körning.
    + Lägg till `process_count` i [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) för att stödja PyTorch-jobb med flera processer.
  + **azureml-widgets**
    + Lägg till widgeten ParallelRunStepDetails för att visualisera status för en ParallelRunStep.
    + Tillåter HyperDrive-användare att se ytterligare en axel i det parallella koordinat diagrammet som visar det mått värde som motsvarar varje uppsättning med för varje underordnad körning.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning Studio Notebooks Experience (januari Update)
+ **Nya funktioner**
  + Inbyggd markdown-redigerare i AzureML. Användare kan nu återge och redigera markdown-filer internt i AzureML Studio.
  + [Knappen Kör för skript (. py,. R och. sh)](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script). Användarna kan nu enkelt köra python-, R-och bash-skript i AzureML
  + [Variabel Utforskaren](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook). Utforska innehållet i variabler och data ramar i en popup-panel. Användare kan enkelt kontrol lera datatyp, storlek och innehåll.
  + [Innehålls](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc)förteckning. Navigera till avsnitt i din antecknings bok, som anges av markdown-rubriker.
  + Exportera din antecknings bok som latex/HTML/py. Skapa filer som är enkla att dela i anteckningsbok-format genom att exportera till LaTex, HTML eller. py
  + Intellicode. Med de ML-baserade resultaten får du en förbättrad [intelligent autokomplettering](/visualstudio/intellicode/overview).

+ **Fel korrigeringar och förbättringar**
  + Förbättrade sid inläsnings tider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kernel-tillförlitlighet
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK för python v-1.21.0
+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Hjälp text för fast CLI när du använder AmlCompute med UserAssigned-identitet
  + **azureml-contrib-automl-DNN-vision**
    + Distributions-och nedladdnings knapparna blir synliga för AutoML visioner och modeller kan distribueras eller hämtas på liknande sätt som andra AutoML-körningar. Det finns två nya filer (scoring_file_v_1_0_0. py och conda_env_v_1_0_0. yml) som innehåller ett skript för att köra inferencing och en YML-fil för att återskapa Conda-miljön. Filen Model. PTH har också bytt namn till att använda tillägget ". PT".
  + **azureml-core**
    + MSI-stöd för Azure-CLI-ml
    + Användare tilldelade hanterad identitets support.
    + Med den här ändringen ska kunderna kunna ange en tilldelad identitet som kan användas för att hämta nyckeln från kund nyckel valvet för kryptering i vila.
    +  korrigera row_count = 0 för profilen av mycket stora filer – korrigera fel i dubbel konvertering för avgränsade värden med blank stegs utfyllnad
    + Ta bort experimentell flagga för utdata-datauppsättning GA
    + Uppdaterings dokumentation om hur du hämtar en speciell version av en modell
    + Tillåt uppdatering av arbets yta för blandat läge åtkomst i händelse av privat länk
    + Korrigera för att ta bort ytterligare registrering av data lager för funktionen återuppta körning
    + Stöd för CLI/SDK har lagts till för att uppdatera primär användares tilldelade identitet för arbets ytan
  + **azureml-interpret**
    + Uppdaterad azureml-tolkning till tolkning – community-0.16.0
    + minnes optimering för förklarings klient i azureml-tolka
  + **azureml-train-automl-runtime**
    + Aktive rad strömning för ADB-körningar
  + **azureml-train-core**
    + Korrigera för att ta bort ytterligare registrering av data lager för funktionen återuppta körning
  + **azureml-widgets**
    + Kunderna bör inte se ändringar i den befintliga data visualiseringen för körning med hjälp av widgeten och har nu stöd om de använder villkorsstyrda disponeringsparametrarna.
    + Widgeten användar körning innehåller nu en detaljerad förklaring av varför en körning är i kö-tillstånd.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK för python v-1.20.0
+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + framework_version lagts till i OptimizationConfig. Den kommer att användas när modellen registreras med Framework MULTI.
  + **azureml – contrib-optimering**
    + framework_version lagts till i OptimizationConfig. Den kommer att användas när modellen registreras med Framework MULTI.
  + **azureml-pipeline-steps**
    + Vi presenterar CommandStep som tar kommandot att bearbeta. Kommandot kan innehålla körbara filer, gränssnitts kommandon, skript osv.
  + **azureml-core**
    + Nu har du skapat en arbets yta som stöder användarens tilldelade identitet. Lägga till UAI-stöd från SDK/CLI
    + Åtgärdat problem för tjänsten. load () för att hämta ändringar på score.py i lokal distribution.
    + `run.get_details()` innehåller ett extra fält med namnet "submittedBy" som visar författarens namn för den här körningen.
    + Redigerad modell. registrera metod dokumentation för att ange hur modellen ska registreras från att köras direkt
    + Problem med att ändra hantering av anslutnings status för fasta IOT-Server.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio Notebooks Experience (december-uppdatering)
+ **Nya funktioner**
  + Sök efter användar namn. Användarna kan nu söka igenom alla filer som har sparats i en arbets yta.
  + Markdown sida-vid-sida-stöd per Notebook-cell. I en Notebook-cell kan användarna nu ha möjlighet att Visa renderad markdown och markdown-syntax sida vid sida.
  + Cell status fält. Statusfältet visar vilket tillstånd en kod cell är i, om en cell körning lyckades och hur lång tid det tog att köra. 
   
+ **Fel korrigeringar och förbättringar**
  + Förbättrade sid inläsnings tider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kernel-tillförlitlighet

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK för python v-1.19.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Stöd för test data har lagts till i AutoMLStep.
    + Lade till den initiala kärn implementeringen av funktionen för inmatning av test uppsättning.
    + Flyttade referenser till sklearn. externals. joblib till är beroende av direkt på joblib.
    + presentera en ny AutoML-aktivitets typ för "bild instans segmentering".
  + **azureml-automl-runtime**
    + Lade till den initiala kärn implementeringen av funktionen för inmatning av test uppsättning.
    + När alla strängar i en text kolumn har en längd på exakt 1 tecken fungerar inte TfIdf Word-gram-upplärda eftersom dess tokenizer ignorerar strängarna med färre än 2 tecken. Den aktuella kod ändringen gör att AutoML kan hantera det här användnings fallet.
    + presentera en ny AutoML-aktivitets typ för "bild instans segmentering".
  + **azureml-contrib-automl-DNN-NLP**
    + Första PR för nytt DNN-NLP-paket
  + **azureml-contrib-automl-DNN-vision**
    + presentera en ny AutoML-aktivitets typ för "bild instans segmentering".
  + **azureml-contrib-automl-pipeline – steg**
    + Det nya paketet ansvarar för att skapa steg som krävs för många modeller träna/härlednings scenario. – Den flyttar också träna/härlednings koden till azureml. träna. automl. Runtime-paketet så att eventuella framtida korrigeringar blir tillgängliga automatiskt genom de miljö versioner som granskas.
  + **azureml-contrib-dataset**
    + presentera en ny AutoML-aktivitets typ för "bild instans segmentering".
  + **azureml-core**
    + Lade till den initiala kärn implementeringen av funktionen för inmatning av test uppsättning.
    + Åtgärda XREF-varningar för dokumentation i azureml-Core-paketet
    + Dokument Strängs korrigeringar för Command Support-funktionen i SDK
    + Lägger till kommando egenskap i RunConfiguration. Funktionen gör det möjligt för användare att köra ett faktiskt kommando eller körbara filer på Compute-AzureML SDK.
    + Användare kan ta bort ett tomt experiment med ID för det experimentet.
  + **azureml-dataprep**
    + Stöd för data uppsättning har lagts till för Spark byggd med Scala 2,12. Detta lägger till i det befintliga 2,11-stödet.
  + **azureml-mlflow**
    + AzureML-MLflow lägger till säkra skydd i fjärrskript för att undvika tidig avslutning av skickade körningar.
  + **azureml-pipeline-core**
    + En bugg har åtgärd ATS i att ange en standard-pipeline för pipeline-slutpunkt som skapats via UI
  + **azureml-pipeline-steps**
    + Stöd för test data har lagts till i AutoMLStep.
  + **azureml-tensorboard**
    + Åtgärda XREF-varningar för dokumentation i azureml-Core-paketet
  + **azureml-train-automl-client**
    + Stöd för test data har lagts till i AutoMLStep.
    + Lade till den initiala kärn implementeringen av funktionen för inmatning av test uppsättning.
    + presentera en ny AutoML-aktivitets typ för "bild instans segmentering".
  + **azureml-train-automl-runtime**
    + Lade till den initiala kärn implementeringen av funktionen för inmatning av test uppsättning.
    + Åtgärda beräkningen av de råa förklaringarna för den bästa AutoML-modellen om AutoML-modellerna tränas med validation_size-inställningen.
    + Flyttade referenser till sklearn. externals. joblib till är beroende av direkt på joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric () bör bli snabbare nu
    + Förbättrad fel hantering i HyperDrive SDK.
    +  Föråldrade alla uppskattnings klasser i förmån att använda ScriptRunConfig för att konfigurera experiment körningar. Inaktuella klasser är:
        + MMLBase
        + Uppskattning
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Föråldrade användningen av Nccl och gloo som giltiga indatatyper för uppskattnings klasser i förmån att använda PyTorchConfiguration med ScriptRunConfig.
    + Föråldrade användningen av MPI som en giltig Indatatyp för uppskattnings klasser i förmån att använda MpiConfiguration med ScriptRunConfig.
    + Lägger till kommando egenskap i runconfiguration. Funktionen gör det möjligt för användare att köra ett faktiskt kommando eller körbara filer på Compute-AzureML SDK.

    +  Föråldrade alla uppskattnings klasser i förmån att använda ScriptRunConfig för att konfigurera experiment körningar. Inaktuella klasser är: + MMLBaseEstimator + uppskattar + PyTorch + TensorFlow + Chainer + SKLearn
    + Föråldrade användningen av Nccl och gloo som en giltig typ av inmatare för uppskattnings klasser som prioriteras med hjälp av PyTorchConfiguration med ScriptRunConfig. 
    + Föråldrade användningen av MPI som en giltig typ av inmatare för uppskattnings klasser som prioriteras med hjälp av MpiConfiguration med ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio Notebooks-upplevelse (november Update)
+ **Nya funktioner**
   + Intern Terminal. Användarna kommer nu att ha åtkomst till en integrerad Terminal och git-åtgärd via den [integrerade terminalen.](./how-to-access-terminal.md)
  + Duplicera mapp 
  + Kostnads redovisning för Compute-listruta 
  + Pylance för offline-beräkning 

+ **Fel korrigeringar och förbättringar**
  + Förbättrade sid inläsnings tider
  + Förbättrade prestanda 
  + Förbättrad hastighet och kernel-tillförlitlighet
  + Stor fil uppladdning. Du kan nu ladda upp filen >95mb

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK för python v-1.18.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    +  Förbättrad hantering av kort tids serier genom att tillåta utfyllnad av dem med Gaussisk brus.
  + **azureml-automl-runtime**
    + Utlös ConfigException om en DateTime-kolumn har OutOfBoundsDatetime-värde
    + Förbättrad hantering av kort tids serier genom att tillåta utfyllnad av dem med Gaussisk brus.
    + Se till att varje text kolumn kan använda char-gram-transformering med n-gram-intervallet baserat på längden på strängarna i text kolumnen
    + Tillhandahålla förklaringar av RAW-funktionen för bästa möjliga läge för AutoML experiment som körs på användarens lokala beräkning
  + **azureml-core**
    + Fäst paketet: pyjwt för att undvika att hämta versioner i kommande versioner.
    + Om du skapar ett experiment returneras det aktiva eller senaste arkiverade experimentet med samma namn om det finns ett sådant experiment eller ett nytt experiment.
    + Om du anropar get_experiment efter namn returneras det aktiva eller senaste arkiverade experimentet med det namnet.
    + Användare kan inte byta namn på ett experiment när det återaktiveras.
    + Förbättrat fel meddelande som innehåller möjliga korrigeringar när en data uppsättning felaktigt skickas till ett experiment (t. ex. ScriptRunConfig). 
    + Förbättrad dokumentation för `OutputDatasetConfig.register_on_complete` att inkludera beteendet vad som händer när namnet redan finns.
    + Om du anger indata-och utdatafiler som är möjliga att krocka med vanliga miljövariabler kommer nu att resultera i en varning
    + Omtillämpnings `grant_workspace_access` parameter vid registrering av data lager. Ställ in den på `True` för att komma åt data bakom det virtuella nätverket från Machine Learning Studio.
      [Läs mer](./how-to-enable-studio-virtual-network.md)
    + Det länkade tjänst-API: t är raffinerat. I stället för att tillhandahålla resurs-ID har vi tre separata parametrar sub_id, rg och namn som definieras i konfigurationen.
    + För att göra det möjligt för kunder att själv lösa problem med token som är skadade kan du aktivera synkronisering av tidstoken för synkronisering till en offentlig metod.
    + Den här ändringen gör att en tom sträng kan användas som ett värde för en script_param
  + **azureml-train-automl-client**
    +  Förbättrad hantering av kort tids serier genom att tillåta utfyllnad av dem med Gaussisk brus.
  + **azureml-train-automl-runtime**
    + Utlös ConfigException om en DateTime-kolumn har OutOfBoundsDatetime-värde
    + Stöd har lagts till för att tillhandahålla rå funktions förklaringar för bästa modell för AutoML experiment som körs på användarens lokala beräkning
    + Förbättrad hantering av kort tids serier genom att tillåta utfyllnad av dem med Gaussisk brus.
  + **azureml-train-core**
    + Den här ändringen gör att en tom sträng kan användas som ett värde för en script_param
  + **azureml-träna-restclients-HyperDrive**
    + VIKTIGT-filen har ändrats för att erbjuda mer kontext
  + **azureml-widgets**
    + Lägg till sträng stöd för diagram/Parallel-koordinater-bibliotek för widget.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Data etiketter för bild instans segment (Polygon) (för hands version)

Bild instans segmenten (polygoner) projekt typen i data etiketter är tillgänglig nu, så att användarna kan rita och kommentera med polygoner runt objektens konturer i bilderna. Användarna kan tilldela en klass och en polygon till varje objekt som är av intresse i en bild.

Läs mer om [etiketter för bild instans segment](how-to-label-images.md).



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK för python v-1.17.0
+ **nya exempel**
  + Det finns en ny community-driven databas med exempel på https://github.com/Azure/azureml-examples
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Ett problem har åtgärd ATS där get_output kan utlösa en XGBoostError.
  + **azureml-automl-runtime**
    + Tid/kalender-baserade funktioner som skapats av AutoML kommer nu att ha prefixet.
    + Fast en IndexError som inträffar under utbildning av StackEnsemble för klassificering av data uppsättningar med ett stort antal klasser och under sampling aktive rad.
    + Ett problem har åtgärd ATS där VotingRegressor förutsägelser kan vara felaktigt efter omanpassning av modellen.
  + **azureml-core**
    + Ytterligare information som lagts till om relationen mellan AKS distributions konfiguration och Azure Kubernetes service-koncept.
    + Stöd för miljöns klient etiketter. Användaren kan etikettera miljöer och referera dem efter etikett.
  + **azureml-dataprep**
    + Bättre fel meddelande vid användning av Spark med Scala 2,12 som inte stöds.
  + **azureml-explain-model**
    + Azureml-förklaring – modell paketet är officiellt inaktuellt
  + **azureml-mlflow**
    + Löst ett fel i mlflow. projects. Run to azureml Server del där det slutliga status inte hanterades korrekt.
  + **azureml-pipeline-core**
    + Lägg till stöd för att skapa, lista och hämta pipeline-schema baserat på en pipeline-slutpunkt.
    +  Bättre dokumentation av PipelineData.as_dataset med ett ogiltigt användnings exempel – om du använder PipelineData.as_dataset felaktigt kommer det att resultera i att en ValueException genereras
    + Ändrade antecknings boken för HyperDriveStep-pipelinen för att registrera den bästa modellen i en PipelineStep direkt efter HyperDriveStep-körningen.
  + **azureml-pipeline-steps**
    + Ändrade antecknings boken för HyperDriveStep-pipelinen för att registrera den bästa modellen i en PipelineStep direkt efter HyperDriveStep-körningen.
  + **azureml-train-automl-client**
    + Ett problem har åtgärd ATS där get_output kan utlösa en XGBoostError.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio Notebooks Experience (oktober-uppdatering)
+ **Nya funktioner**
  + [Fullständigt stöd för virtuella nätverk](./how-to-enable-studio-virtual-network.md)
  + [Fokus läge](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Spara antecknings böcker CTRL-S
  + Rad nummer

+ **Fel korrigeringar och förbättringar**
  + Förbättringar av hastighet och kernel-tillförlitlighet
  + Uppdateringar för Jupyter-widget

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK för python v-1.16.0
+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + AKSWebservice och AKSEndpoints har nu stöd för processor-och minnes resurs gränser på pod-nivå. Dessa valfria gränser kan användas genom att ställa in `--cpu-cores-limit` och `--memory-gb-limit` flagga i tillämpliga CLI-anrop
  + **azureml-core**
    + Fästa huvud versioner av direkta beroenden för azureml-Core
    + AKSWebservice och AKSEndpoints har nu stöd för processor-och minnes resurs gränser på pod-nivå. Mer information om [Kubernetes-resurser och begränsningar](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Uppdaterade run.log_table för att tillåta att enskilda rader loggas.
    + Statisk metod har lagts `Run.get(workspace, run_id)` till för att hämta en körning med en arbets yta 
    + Instans metod har lagts `Workspace.get_run(run_id)` till för att hämta en körning inom arbets ytan
    + Introduktion av kommando egenskap i kör konfiguration som gör att användarna kan skicka kommandon i stället för skript & argument.
  + **azureml-interpret**
    + fast förklaring klient is_raw flagga beteende i azureml-tolka
  + **azureml – SDK**
    + `azureml-sdk` officiellt stöd för python 3,8.
  + **azureml-train-core**
    + Lägger till TensorFlow 2,3-granskad miljö
    + Introduktion av kommando egenskap i kör konfiguration som gör att användarna kan skicka kommandon i stället för skript & argument.
  + **azureml-widgets**
    + Omdesignat gränssnitt för widgeten körning av skript.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK för python v-1.15.0
+ **Fel korrigeringar och förbättringar**
  + **azureml-contrib-interpret**
    + LIME-förklaring har flyttats från azureml-contrib-tolka till tolka-community-paket och bild förklaring borttagen från azureml-contrib-tolka paket
    + instrument panelen för visualiseringar har tagits bort från azureml-contrib-tolka paket, förklarings klient som flyttats till azureml-tolka paket och föråldras i azureml-contrib-tolka paket och antecknings böcker som är uppdaterade för att återspegla
    + åtgärda pypi-paketets beskrivningar för azureml-tolka, azureml-förklara-Model, azureml-contrib-tolka och AzureML-tensorboard
  + **azureml-contrib-notebook**
    + Fäst nbcovert-beroendet till < 6 så att Papermill 1. x fortfarande fungerar.
  + **azureml-core**
    + Parametrar har lagts till i konstruktorn TensorflowConfiguration och MpiConfiguration för att möjliggöra en mer effektiv initiering av klassens attribut utan att användaren måste ange varje enskilt attribut. En PyTorchConfiguration-klass har lagts till för att konfigurera distribuerade PyTorch-jobb i ScriptRunConfig.
    + Fäst versionen av Azure-MGMT-Resource för att åtgärda autentiseringsfel.
    + Support Triton utan kod distribution
    + kataloger för utdata som anges i Run.start_logging () spåras nu när du använder kör i interaktiva scenarier. De spårade filerna visas på ML Studio när du anropar Run. Complete ()
    + Fil kodning kan nu anges när data uppsättningen skapas med `Dataset.Tabular.from_delimited_files` och `Dataset.Tabular.from_json_lines_files` genom att `encoding` argumentet skickas. Kodningarna som stöds är "utf8", "iso88591", "Latin1", "ASCII", UTF16 "," utf32 "," utf8bom "och" windows1252 ".
    + Fel korrigering när miljö objekt inte skickas till ScriptRunConfig-konstruktorn.
    + Update Run. Cancel () har uppdaterats för att avbryta en lokal körning från en annan dator.
  + **azureml-dataprep**
    +  Timeout-fel vid montering av fast data mängd.
  + **azureml-explain-model**
    + åtgärda pypi-paketets beskrivningar för azureml-tolka, azureml-förklara-Model, azureml-contrib-tolka och AzureML-tensorboard
  + **azureml-interpret**
    + instrument panelen för visualiseringar har tagits bort från azureml-contrib-tolka paket, förklarings klient som flyttats till azureml-tolka paket och föråldras i azureml-contrib-tolka paket och antecknings böcker som är uppdaterade för att återspegla
    + azureml-tolka paket uppdaterat med är beroende av 0.15.0 för tolkning – community
    + åtgärda pypi-paketets beskrivningar för azureml-tolka, azureml-förklara-Model, azureml-contrib-tolka och AzureML-tensorboard
  + **azureml-pipeline-core**
    +  Fast pipeline-problem med `OutputFileDatasetConfig` var systemet kan sluta svara när `register_on_complete` anropas med `name` parametern angivet till ett redan befintligt data uppsättnings namn.
  + **azureml-pipeline-steps**
    + Tog bort inaktuella antecknings böcker för databricks.
  + **azureml-tensorboard**
    + åtgärda pypi-paketets beskrivningar för azureml-tolka, azureml-förklara-Model, azureml-contrib-tolka och AzureML-tensorboard
  + **azureml-train-automl-runtime**
    + instrument panelen för visualiseringar har tagits bort från azureml-contrib-tolka paket, förklarings klient som flyttats till azureml-tolka paket och föråldras i azureml-contrib-tolka paket och antecknings böcker som är uppdaterade för att återspegla
  + **azureml-widgets**
    + instrument panelen för visualiseringar har tagits bort från azureml-contrib-tolka paket, förklarings klient som flyttats till azureml-tolka paket och föråldras i azureml-contrib-tolka paket och antecknings böcker som är uppdaterade för att återspegla

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK för python v-1.14.0
+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Rutnäts profilering som tagits bort från SDK och som inte längre stöds.
  + **azureml-accel-models**
    + azureml-acceleration-Models-paketet stöder nu Tensorflow 2. x
  + **azureml-automl-core**
    + Fel hantering lades till i get_output för fall när lokala versioner av Pandas/sklearn inte matchar de som används under utbildningen
  + **azureml-automl-runtime**
    + En bugg har åtgärd ATS där AutoArima-iterationer skulle Miss lyckas med en PredictionException och meddelandet: "tyst fel uppstod under förutsägelsen".
  + **azureml-cli-common**
    + Rutnäts profilering som tagits bort från SDK och som inte längre stöds.
  + **azureml-contrib-Server**
    + Uppdaterings Beskrivning av pypi-sidan för paket för översikt.
  + **azureml-core**
    + Rutnäts profilering som tagits bort från SDK och som inte längre stöds.
    + Minska antalet fel meddelanden när det inte går att hämta arbets ytan.
    + Visa inte varning när metadata hämtas
    + Nytt Kusto-steg och Kusto Compute Target.
    + Uppdatera dokument för SKU-parameter. Ta bort SKU i uppdaterings funktionen för arbets ytan i CLI och SDK.
    + Uppdaterings Beskrivning av pypi-sidan för paket för översikt.
    + Uppdaterad dokumentation för AzureML-miljöer.
    + Exponera inställningar för tjänst hanterade resurser för AML-arbetsyta i SDK.
  + **azureml-dataprep**
    + Aktivera kör behörighet för filer för data uppsättnings montering.
  + **azureml-mlflow**
    + Uppdaterad AzureML MLflow-dokumentation och exempel på bärbara datorer 
    + Nytt stöd för MLflow-projekt med AzureML-backend
    + Stöd för MLflow-modell registret
    + Azure RBAC-stöd har lagts till för AzureML-MLflow åtgärder 
    
  + **azureml-pipeline-core**
    + Förbättrad dokumentation av PipelineOutputFileDataset.parse_ * metoder.
    + Nytt Kusto-steg och Kusto Compute Target.
    + Angiven Swaggerurl-egenskap för pipeline – slut punkts enhet via den användaren kan se schema definitionen för den publicerade pipelinen.
  + **azureml-pipeline-steps**
    + Nytt Kusto-steg och Kusto Compute Target.
  + **azureml-telemetry**
    + Uppdaterings Beskrivning av pypi-sidan för paket för översikt.
  + **azureml-träna**
    + Uppdaterings Beskrivning av pypi-sidan för paket för översikt.
  + **azureml-train-automl-client**
    + Fel hantering lades till i get_output för fall när lokala versioner av Pandas/sklearn inte matchar de som används under utbildningen
  + **azureml-train-core**
    + Uppdaterings Beskrivning av pypi-sidan för paket för översikt.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK för python v-1.13.0
+ **Förhandsgranskningsfunktioner**
  + **azureml – kärna** Med den nya funktionen för data uppsättnings data uppsättningar kan du skriva tillbaka till moln lagring, inklusive BLOB, ADLS gen 1, ADLS gen 2 och FileShare. Du kan konfigurera var data ska matas ut, hur de ska matas ut (via montering eller uppladdning), om utdata ska registreras för framtida åter användning och delning och överföring av mellanliggande data mellan pipeline-steg sömlöst. Detta möjliggör reproducerbarhet, delning, förhindrar duplicering av data och ger kostnads effektivitet och produktivitets vinster. [Lär dig hur du använder den](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Lade till validated_ {Platform} _requirements.txt fil för att fästa alla pip-beroenden för AutoML.
    + Den här versionen stöder modeller som är större än 4 GB.
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-automl-runtime**
    + Ange horovod för text DNN att alltid använda FP16-komprimering.
    + Den här versionen stöder modeller som är större än 4 GB.
    + Ett problem har åtgärd ATS där AutoML Miss lyckas med ImportError: det går inte att importera namn `RollingOriginValidator` .
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-contrib-automl-DNN-Forecasting**
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-contrib-fairness**
    + Ange en kort beskrivning för azureml-contrib-skälighet.
  + **azureml-contrib-pipeline-steps**
    + Tillagt meddelande som anger att det här paketet är föråldrat och användaren bör använda azureml-pipeline – steg i stället.
  + **azureml-core**
    + En lista med nyckel kommando har lagts till för arbets ytan.
    + Lägg till taggar-parameter i SDK för arbets yta och CLI.
    + Det gick inte att skicka en underordnad körning med data uppsättning på grund av ett fel `TypeError: can't pickle _thread.RLock objects` .
    + Lägga till page_count standard/dokumentation för modell lista ().
    + Ändra CLI-&SDK för att ta adbworkspace-parametern och Lägg till arbets ytans ADB-/Unlink-löpare.
    + Korrigera fel i data uppsättning. uppdatering som gjorde att den senaste data uppsättnings versionen uppdaterades, men versionen av data uppsättnings uppdateringen anropades. 
    + Åtgärda fel i Dataset.get_by_name som visar taggar för den senaste data uppsättnings versionen även när en tidigare version hämtades.
  + **azureml-interpret**
    + Har lagt till sannolikhets utmatningar för att Shap bedömnings förklaringar i azureml-tolkning baserat på shap_values_output parameter från den ursprungliga förklaringen.
  + **azureml-pipeline-core**
    + Förbättrad `PipelineOutputAbstractDataset.register` dokumentation.
  + **azureml-train-automl-client**
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-train-automl-runtime**
    + Uppgraderade AutoML-beroenden: `scikit-learn` (nu 0.22.1), `pandas` (nu 0.25.1), `numpy` (nu 1.18.2).
  + **azureml-train-core**
    + Användarna måste nu ange ett giltigt hyperparameter_sampling arg när de skapar en HyperDriveConfig. Dessutom har dokumentationen för HyperDriveRunConfig redigerats för att informera användarna om utfasningen av HyperDriveRunConfig.
    + Återställer standard versionen av PyTorch till 1,4.
    + Lägga till PyTorch 1,6 & Tensorflow 2,2-bilder och granskad miljö.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio Notebooks Experience (augusti Update)
+ **Nya funktioner**
  + Ny landnings sida för komma igång 
  
+ **Förhandsgranskningsfunktioner**
    + Samla in funktion i antecknings böcker. Med funktionen [samla in](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) kan användarna nu enkelt rensa antecknings böcker med, samla in använder en automatiserad beroende analys av din bärbara dator, vilket säkerställer att den nödvändiga koden behålls, men tar bort eventuella irrelevanta delar.

+ **Fel korrigeringar och förbättringar**
  + Förbättrad hastighet och tillförlitlighet
  + Mörkt läges fel har åtgärd ATS
  + Rullnings fel i utdata har åtgärd ATS
  + Sök funktionen genomsöker nu allt innehåll för alla filer i Azure Machine Learning exempel antecknings böcker lagrings platsen
  + Flera rader R-celler kan nu köras
  + "Jag har nu kontrollerat att innehållet i den här filen är markerat automatiskt efter första gången
  + Förbättrad konflikt lösnings dialog ruta med nytt alternativ för att göra en kopia
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK för python v-1.12.0

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Lägg till image_name-och image_label-parametrar i Model. Package () för att aktivera namnbyte för den inbyggda paket avbildningen.
  + **azureml-automl-core**
    + AutoML genererar en ny felkod från nu när innehållet ändras under läsning.
  + **azureml-automl-runtime**
    + Aviseringar har lagts till för användaren när data innehåller värden som saknas men funktionalisering har inaktiverats.
    + Fasta, underordnade körnings problem när data innehåller Nan och funktionalisering har inaktiverats.
    + AutoML genererar en ny felkod från nu när innehållet ändras under läsning.
    + En uppdaterad normalisering för prognos mått som ska ske efter kornig het.
    + Förbättrad beräkning av prognos quantiles när lookback funktioner har inaktiverats.
    + Fast bool-optimerad mat ris hantering vid beräkning av förklaringar efter AutoML.
  + **azureml-core**
    + En ny metod `run.get_detailed_status()` visar nu en detaljerad förklaring av aktuell körnings status. Den visar för närvarande endast förklaring för `Queued` status.
    + Lägg till image_name-och image_label-parametrar i Model. Package () för att aktivera namnbyte för den inbyggda paket avbildningen.
    + Ny metod `set_pip_requirements()` för att ställa in hela pip-avsnittet på [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) en gång.
    + Aktivera registrering av autentiseringsuppgifter – mindre ADLS Gen2 data lager.
    + Förbättrat fel meddelande vid försök att ladda ned eller montera en felaktig data uppsättnings typ.
    + Uppdatera data uppsättning filter för Time Series-exempel med fler exempel på partition_timestamp som tillhandahåller filter optimering.
    + Ändra SDK och CLI för att acceptera subscriptionId, resourceGroup, workspaceName, peConnectionName som parametrar i stället för ArmResourceId när du tar bort privat slut punkts anslutning.
    + Experimentell decorator visar klass namn för enklare identifiering.
    + Beskrivningar av till gångar i modeller skapas inte längre automatiskt baserat på en körning.
  + **azureml-datadrift**
    + Markera create_from_model API i DataDriftDetector som inaktuellt.
  + **azureml-dataprep**
    + Förbättrat fel meddelande vid försök att ladda ned eller montera en felaktig data uppsättnings typ.
  + **azureml-pipeline-core**
    + Åtgärdat fel vid avserialisering av Pipeline-diagram som innehåller registrerade data uppsättningar.
  + **azureml-pipeline-steps**
    + RScriptStep stöder RSection från azureml. Core. Environment.
    + Den passthru_automl_config parametern har tagits bort från det `AutoMLStep` offentliga API: t och konverterat den till en intern parameter.
  + **azureml-train-automl-client**
    + Lokal asynkron, hanterad miljö körs från AutoML. Alla lokala körningar körs i miljön som körningen startades från.
    + Problem med fasta ögonblicks bilder vid sändning av AutoML-körningar utan användardefinierade skript.
    + Fasta, underordnade körnings problem när data innehåller Nan och funktionalisering har inaktiverats.
  + **azureml-train-automl-runtime**
    + AutoML genererar en ny felkod från nu när innehållet ändras under läsning.
    + Problem med fasta ögonblicks bilder vid sändning av AutoML-körningar utan användardefinierade skript.
    + Fasta, underordnade körnings problem när data innehåller Nan och funktionalisering har inaktiverats.
  + **azureml-train-core**
    + Stöd har lagts till för att ange pip-alternativ (till exempel--extra-index-URL) i PIP-krav filen som skickas till en- [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) `pip_requirements_file` parameter.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK för python v-1.11.0

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Korrigera modell ramverk och modell ramverk överförs inte i körnings objekt i en registrerings Sök väg för CLI-modellen
    + Åtgärda kommandot CLI amlcompute Identity show för att visa klient-ID och ägar-ID 
  + **azureml-train-automl-client**
    + Lade till get_best_child () i AutoMLRun för att hämta den bästa underordnade körningen för en AutoML-körning utan att hämta den associerade modellen.
    + ModelProxy-objekt som tillåter förutsägelse eller prognos kan köras i en fjärran sluten tränings miljö utan att modellen laddas ned lokalt.
    + Ohanterade undantag i AutoML pekar nu på en känd HTTP-sida med kända problem, där mer information om felen kan hittas.
  + **azureml-core**
    + Modell namn får innehålla 255 tecken.
    + Environment.get_image_details () returnerad objekt typ har ändrats. `DockerImageDetails` klass ersatt `dict` , bild information är tillgänglig från de nya klass egenskaperna. Ändringarna är bakåtkompatibla.
    + Åtgärda bugg för Environment.from_pip_requirements () för att bevara beroende struktur
    + Ett fel uppstod där log_list skulle uppstå om en int-och Double-åtgärd inkluderades i samma lista.
    + Tänk på att om det finns beräknings mål som är kopplade till arbets ytan när du aktiverar privat länk på en befintlig arbets yta, kommer dessa mål inte att fungera om de inte ligger bakom samma virtuella nätverk som den privata slut punkten för arbets ytan.
    + Görs `as_named_input` valfri när du använder data uppsättningar i experiment och `as_mount` lagt `as_download` till och till `FileDataset` . Indatamängden skapas automatiskt om `as_mount` eller `as_download` anropas.
  + **azureml-automl-core**
    + Ohanterade undantag i AutoML pekar nu på en känd HTTP-sida med kända problem, där mer information om felen kan hittas.
    + Lade till get_best_child () i AutoMLRun för att hämta den bästa underordnade körningen för en AutoML-körning utan att hämta den associerade modellen.
    + ModelProxy-objektet har lagts till som gör det möjligt att köra predict eller forecast i en fjärran sluten tränings miljö utan att modellen laddas ned lokalt.
  + **azureml-pipeline-steps**
    + Tillagda `enable_default_model_output` och `enable_default_metrics_output` flaggor till `AutoMLStep` . Dessa flaggor kan användas för att aktivera/inaktivera standardutdata.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK för python v-1.10.0

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + När du använder AutoML, om en sökväg skickas till AutoMLConfig-objektet och den inte redan finns, skapas den automatiskt.
    + Användarna kan nu ange en tids serie frekvens för prognostisering av aktiviteter med hjälp av- `freq` parametern.
  + **azureml-automl-runtime**
    + När du använder AutoML, om en sökväg skickas till AutoMLConfig-objektet och den inte redan finns, skapas den automatiskt.
    + Användarna kan nu ange en tids serie frekvens för prognostisering av aktiviteter med hjälp av- `freq` parametern.
    + AutoML-Prognosticering har nu stöd för rullande utvärdering, som gäller för användnings fall som längden på ett test-eller verifierings uppsättning är längre än den angivna horisonten och känt y_pred värde används som Prognosticering kontext.
  + **azureml-core**
    + Varnings meddelanden skrivs ut om inga filer hämtades från data lagret i en körning.
    + Lade till dokumentation för `skip_validation` i `Datastore.register_azure_sql_database method` .
    + Användare måste uppgradera till SDK v-1.10.0 eller senare för att skapa en automatiskt godkänd privat slut punkt. Detta inkluderar den Notebook-resurs som kan användas bakom VNet.
    + Exponera NotebookInfo i svaret på Hämta arbets yta.
    + Ändringar som ska utföras för att lista beräknings mål och få beräknings mål lyckas på en fjärran sluten körning. SDK-funktioner för att hämta beräknings mål och Visa lista med beräknings mål för arbets ytor kommer nu att fungera i fjärrkörningar.
    + Lägg till utfasnings meddelanden i klass beskrivningar för azureml. Core. image-klasser.
    + Utlös undantag och rensa arbets ytan och beroende resurser om det inte går att skapa arbets ytans privata slut punkt.
    + Uppdatera SKU-uppgradering i arbets ytan uppdaterings metod.
  + **azureml-datadrift**
    + Uppdatera matplotlib-versionen från 3.0.2 till 3.2.1 för att stödja python 3,8.
  + **azureml-dataprep**
    + Stöd för webb adress data källor med eller begäran har lagts till `Range` `Head` . 
    + Förbättrad stabilitet för fil data uppsättnings montering och nedladdning.
  + **azureml-train-automl-client**
    + Korrigerade problem som rör borttagning av `RequirementParseError` från installations verktyg.
    + Använd Docker i stället för Conda för lokala körningar som skickas med "compute_target =" lokal "
    + Upprepnings tiden som skrivs till konsolen har korrigerats. Tidigare skrevs upprepnings tiden ibland ut som körnings slut tid minus körnings skapande tid. Den har korrigerats till samma körnings slut tid minus körningens start tid.
    + När du använder AutoML, om en sökväg skickas till AutoMLConfig-objektet och den inte redan finns, skapas den automatiskt.
    + Användarna kan nu ange en tids serie frekvens för prognostisering av aktiviteter med hjälp av- `freq` parametern.
  + **azureml-train-automl-runtime**
    + Förbättrade konsol utdata när bästa modell förklaringar inte fungerar.
    + Parametern har bytt namn till "blocked_models" för att ta bort en känslig term.
      + Parametern har bytt namn till "allowed_models" för att ta bort en känslig term.
    + Användarna kan nu ange en tids serie frekvens för prognostisering av aktiviteter med hjälp av- `freq` parametern.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK för python v-1.9.0

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Ersattes get_model_path () med miljövariabeln AZUREML_MODEL_DIR AutoML i det automatiskt genererade bedömnings skriptet. Även tillagd telemetri för att spåra felen under init ().
    + Har tagit bort möjligheten att ange `enable_cache` som en del av AutoMLConfig
    + Ett fel uppstod där körningar kan Miss lyckas med fel i tjänsten under vissa Prognosticering-körningar
    + Förbättrad fel hantering kring vissa modeller under `get_output`
    + Fast anrop till fitted_model. passa (X, y) för klassificering med y-transformator
    + Aktive rad anpassad fyllnads rad för att lägga till prognos uppgifter
    + En ny ForecastingParameters-klass kommer att användas i stället för prognos parametrar i ett dict-format
    + Förbättrad automatisk identifiering av mål fördröjning
    + Begränsad tillgänglighet för multi-nodeed, multi-GPU distribuerade funktionalisering med BERT har lagts till
  + **azureml-automl-runtime**
    + Prophet har nu additiv säsongs beroende Modeling i stället för multiplicative.
    + När man har fastställt att en kort kornig het, med olika frekvenser än de långa kärnorna, leder till misslyckade körningar.
  + **azureml-contrib-automl-DNN-vision**
    + Samla in system/GPU-statistik och logg genomsnitt för utbildning och Poäng
  + **azureml-contrib-Mir**
    + Stöd har lagts till för Enable-App-Insights-flaggan i ManagedInferencing
  + **azureml-core**
    + En validate-parameter till dessa API: er genom att tillåta att verifiering hoppas över när data källan inte är tillgänglig från den aktuella beräkningen.
      + TabularDataset.time_before (end_time, include_boundary = True, validate = true)
      + TabularDataset.time_after (start_time, include_boundary = True, validate = true)
      + TabularDataset.time_recent (time_delta, include_boundary = True, validate = true)
      + TabularDataset.time_between (start_time, end_time, include_boundary = True, validate = true)
    + Stöd för Ramverks filtrering för modell lista har lagts till och NCD AutoML-exemplet har lagts till i Notebook tillbaka
    + För Datastore.register_azure_blob_container och Datastore.register_azure_file_share (endast alternativ som stöder SAS-token) har vi uppdaterat dokument strängarna för `sas_token` fältet som innehåller minimi kraven för behörighet för vanliga Läs-och skriv scenarier.
    + Föråldrad _with_auth param i ws.get_mlflow_tracking_uri ()
  + **azureml-mlflow**
    + Lägg till stöd för distribution av lokala file://-modeller med AzureML-MLflow
    + Föråldrad _with_auth param i ws.get_mlflow_tracking_uri ()
  + **azureml-opendatasets**
    + Nyligen publicerade Covid-19 spårnings data uppsättningar är nu tillgängliga med SDK: n
  + **azureml-pipeline-core**
    + Varning om utloggning när "azureml-defaults" inte ingår som en del av pip-beroendet
    + Förbättra antecknings åter givning.
    + Stöd för citerade rad brytningar har lagts till vid parsning av avgränsade filer till PipelineOutputFileDataset.
    + PipelineDataset-klassen är föråldrad. Mer information finns i https://aka.ms/dataset-deprecation. Lär dig hur du använder data uppsättning med pipeline, se https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Dok uppdateringar av azureml-pipeline – steg.
    +  Stöd har lagts till i ParallelRunConfig `load_yaml()` för att användare ska kunna definiera miljöer infogade i resten av konfigurationen eller i en separat fil
  + **azureml-träna-automl-client**.
    + Har tagit bort möjligheten att ange `enable_cache` som en del av AutoMLConfig
  + **azureml-train-automl-runtime**
    + Begränsad tillgänglighet för multi-nodeed, multi-GPU distribuerade funktionalisering med BERT har lagts till.
    + Fel hantering lades till för inkompatibla paket i ADB-baserade automatiserade maskin inlärnings körningar.
  + **azureml-widgets**
    + Doc-uppdateringar av azureml-widgetar.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK för python v-1.8.0
  
  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-skälighet** `azureml-contrib-fairness` Paketet ger integrering mellan skälighet-utvärderingen med öppen källkod och [Fairlearn](https://fairlearn.github.io) och Azure Machine Learning Studio. Paketet gör det särskilt möjligt att ladda upp instrument paneler för modell skälighet-utvärdering som en del av en AzureML-körning och visas i Azure Machine Learning Studio

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Stöd för att hämta loggar för init-behållare.
    + Nya CLI-kommandon har lagts till för hantering av ComputeInstance
  + **azureml-automl-core**
    + Nu kan användarna aktivera stack-Ensemble-iteration för Time Series-aktiviteter med en varning om att det kan overfit.
    + Lade till en ny typ av användar undantag som aktive ras om cachens innehåll har ändrats
  + **azureml-automl-runtime**
    + Rensning av klass balansering aktive ras inte längre om användaren inaktiverar funktionalisering.  
  + **azureml-contrib-ITP**
    + Beräknings typ för CmAk stöds. Du kan koppla ditt eget AKS-kluster till arbets ytan för utbildnings jobbet.
  + **azureml-contrib-notebook**
    + Dokument förbättringar av azureml-contrib-Notebook-paketet.
  + **azureml-contrib-pipeline-steps**
    + Dokument förbättringar av azureml-contrib--pipeline-steg-paketet.
  + **azureml-core**
    + Lägg till set_connection, get_connection, list_connections, delete_connection funktioner för kund som ska fungera på arbets ytans anslutnings resurs
    + Dokumentations uppdateringar av azureml-coore/azureml. Exceptions-paketet.
    + Dokumentations uppdateringar till azureml-Core-paketet.
    + Doc-uppdateringar av klassen ComputeInstance.
    + Dokument förbättringar av azureml-Core/azureml. Core. Compute-paketet.
    + Dokument förbättringar för WebService-relaterade klasser i azureml-Core.
    + Stöd för användare – valt data lager för lagring av profilerings data
    + Utökad och page_count egenskap har lagts till i API för modell lista
    + Åtgärdat fel där den överskrivnings egenskapen tas bort medför att den skickade körningen Miss fungerar med fel vid deserialisering.
    + Fast inkonsekvent mappstruktur vid hämtning eller montering av en FileDataset som refererar till en enskild fil.
    + Inläsning av en data uppsättning av Parquet-filer to_spark_dataframe är nu snabbare och har stöd för alla Parquet-och Spark SQL-datatyper.
    + Stöd för att hämta loggar för init-behållare.
    + AutoML-körningar är nu markerade som underordnad körning av parallellt körnings steg.
  + **azureml-datadrift**
    + Dokument förbättringar av azureml-contrib-Notebook-paketet.
  + **azureml-dataprep**
    + Inläsning av en data uppsättning av Parquet-filer to_spark_dataframe är nu snabbare och har stöd för alla Parquet-och Spark SQL-datatyper.
    + Bättre minnes hantering för OutOfMemory-problem för to_pandas_dataframe.
  + **azureml-interpret**
    + Uppgraderade azureml – tolka för att använda tolka-community version 0,12. *
  + **azureml-mlflow**
    + Dokument förbättringar av azureml-mlflow.
    + Lägger till stöd för AML-modell registret med MLFlow.
  + **azureml-opendatasets**
    + Stöd har lagts till för python 3,8
  + **azureml-pipeline-core**
    + Uppdaterade `PipelineDataset` dokumentationen för att göra det tydligt att det är en intern klass.
    + ParallelRunStep uppdateringar för att acceptera flera värden för ett argument, till exempel: "--group_column_names", "Col1", "Col2", "Col3"
    + Passthru_automl_configs kravet för användning av mellanliggande data med AutoMLStep i pipelines har tagits bort.
  + **azureml-pipeline-steps**
    + Dokument förbättringar av azureml-pipeline – steg-paket.
    + Passthru_automl_configs kravet för användning av mellanliggande data med AutoMLStep i pipelines har tagits bort.
  + **azureml-telemetry**
    + Dokument förbättringar för azureml-telemetri.
  + **azureml-train-automl-client**
    + Ett fel som `experiment.submit()` anropades två gånger på ett `AutoMLConfig` objekt resulterade i olika beteenden.
    + Nu kan användarna aktivera stack-Ensemble-iteration för Time Series-aktiviteter med en varning om att det kan overfit.
    + Ändrade AutoML körnings beteende för att utlösa UserErrorException om tjänsten genererar användar fel
    + Åtgärdar en bugg som orsakade azureml_automl. log till att inte generera eller saknade loggar när ett AutoML-experiment utförs på ett fjärrberäknings mål.
    + För klassificerings data uppsättningar med obalanserade klasser, kommer vi att tillämpa vikt utjämning, om funktionen Sweeper avgör att Viktnings aktiviteten ska förbättra prestandan för klassificerings aktiviteten med ett visst tröskelvärde.
    + AutoML-körningar är nu markerade som underordnad körning av parallellt körnings steg.
  + **azureml-train-automl-runtime**
    + Ändrade AutoML körnings beteende för att utlösa UserErrorException om tjänsten genererar användar fel
    + AutoML-körningar är nu markerade som underordnad körning av parallellt körnings steg.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK för python v-1.7.0

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Borttagningen av modell profilering från Mir-contrib har rensats genom att CLI-kommandon och paket beroenden rensas, och modell profilering är tillgängligt i Core.
    + Uppgraderar den minsta Azure CLI-versionen till 2.3.0
  + **azureml-automl-core**
    + Ett bättre undantags meddelande på funktionalisering steg fit_transform () på grund av anpassade transformator parametrar.
    + Lägg till stöd för flera språk för modeller för djup inlärnings transformatorer som BERT i automatiserad ML.
    + Ta bort inaktuell lag_length parameter från dokumentationen.
    + Dokumentation om prognos parametrar har förbättrats. Parametern lag_length är föråldrad.
  + **azureml-automl-runtime**
    + Åtgärdade det fel som uppstod när en av kategoriska-kolumnerna är tom i prognos/test-tid.
    + Åtgärda de körnings problem som inträffar när lookback-funktionerna är aktiverade och att data innehåller korta kärnor.
    + Ett fel meddelande med duplicerad tids index åtgärdades när lags eller rullande Windows angavs till Auto.
    + Korrigerade problemet med Prophet-och Arima-modeller på data uppsättningar som innehåller lookback-funktionerna.
    + Stöd för datum har lagts till före 1677-09-21 eller efter 2262-04-11 i kolumner andra än datum och tid i prognos aktiviteterna. Förbättrade fel meddelanden.
    + Dokumentation om prognos parametrar har förbättrats. Parametern lag_length är föråldrad.
    + Ett bättre undantags meddelande på funktionalisering steg fit_transform () på grund av anpassade transformator parametrar.
    + Lägg till stöd för flera språk för modeller för djup inlärnings transformatorer som BERT i automatiserad ML.
    + Cache-åtgärder som leder till att vissa OSErrors kommer att utlösa användar fel.
    + Kontroller har lagts till för att säkerställa att inlärnings-och verifierings data har samma antal och kolumn uppsättningar
    + Åtgärds skript för automatiskt genererad AutoML när data innehåller citat tecken
    + Aktivera förklaringar för AutoML-Prophet och Ensemble-modeller som innehåller Prophet-modellen.
    + Ett nytt kund problem påträffade en fel sökning i real tid där vi loggar meddelanden utmed klass balansering – svepande även om klass balanserings logiken inte är korrekt aktive rad. Tar bort dessa loggar/meddelanden med detta PR.
  + **azureml-cli-common**
    + Borttagningen av modell profilering från Mir-contrib har rensats genom att CLI-kommandon och paket beroenden rensas, och modell profilering är tillgängligt i Core.
  + **azureml-contrib-reinforcementlearning**
    + Belastnings test verktyget
  + **azureml-core**
    + Dokumentations ändringar på Script_run_config. py
    + Åtgärdar en bugg med att skriva ut utdata från kör Submit-pipeline CLI
    + Dokumentations förbättringar för azureml-Core/azureml. data
    + Åtgärdar problem med att hämta lagrings konto med HDFS getconf-kommandot
    + Förbättrad register_azure_blob_container-och register_azure_file_share-dokumentation
  + **azureml-datadrift**
    + Förbättrad implementering för inaktive ring och aktivering av data uppsättnings drift övervakare
  + **azureml-interpret**
    + I förklarings klient tar du bort NaNs eller INF före JSON-serialisering vid uppladdning från artefakter
    + Uppdatera till den senaste versionen av tolka-community för att förbättra minnes fel i globala förklaringar med många funktioner och klasser
    + Lägg till true_ys valfri parameter till förklarings överföring för att aktivera ytterligare funktioner i Studio UI
    + Förbättra prestanda för download_model_explanations () och list_model_explanations ()
    + Små justeringar av antecknings böcker, för att hjälpa till med fel sökning
  + **azureml-opendatasets**
    + azureml-opendatasets behöver azureml-nu version 1.4.0 eller högre. Varning har lagts till om lägre version identifieras
  + **azureml-pipeline-core**
    + Den här ändringen gör att användaren kan ange en valfri runconfig till moduleVersion vid anrop till modulen. Publish_python_script.
    + Aktivera Node-konto kan vara en pipeline-parameter i ParallelRunStep i azureml. pipeline. steg
  + **azureml-pipeline-steps**
    + Den här ändringen gör att användaren kan ange en valfri runconfig till moduleVersion vid anrop till modulen. Publish_python_script.
  + **azureml-train-automl-client**
    + Lägg till stöd för flera språk för modeller för djup inlärnings transformatorer som BERT i automatiserad ML.
    + Ta bort inaktuell lag_length parameter från dokumentationen.
    + Dokumentation om prognos parametrar har förbättrats. Parametern lag_length är föråldrad.
  + **azureml-train-automl-runtime**
    + Aktivera förklaringar för AutoML-Prophet och Ensemble-modeller som innehåller Prophet-modellen.
    + Dokumentations uppdateringar av azureml-träna-automl-*-paket.
  + **azureml-train-core**
    + Stöd för TensorFlow version 2,1 i PyTorch-uppskattningen
    + Förbättringar av azureml-träna-Core-paketet.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK för python v-1.6.0

+ **Nya funktioner**
  + **azureml-automl-runtime**
    + AutoML-Prognosticering stöder nu kunders prognoser utanför den förangivna Max horisonten utan att omträna modellen. När prognos målet ligger längre fram i framtiden än den angivna maximala horisonten kommer funktionen prediktion () fortfarande att göra en punkt som utvärderar till senare datum med ett rekursivt funktions läge. En illustration av den nya funktionen finns i avsnittet "prognoser längre än den maximala horisonten" i "prediktion-prediktion-Function"-anteckningsbok i [mappen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning). "
  
  + **azureml-pipeline-steps**
    + ParallelRunStep lanseras nu och ingår i **azureml-pipeline-steg-** paketet. Befintliga ParallelRunStep i **azureml-contrib-pipeline-steg-** paketet är inaktuellt. Ändringar från offentlig för hands version:
      + `run_max_try`Valfri konfigurerbar parameter har lagts till för att kontrol lera högsta anrop till körnings metoden för en specifik batch. standardvärdet är 3.
      + Inga PipelineParameters skapas automatiskt längre. Följande konfigurerbara värden kan anges som PipelineParameter explicit.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Standardvärdet för process_count_per_node ändras till 1. Användaren bör justera det här värdet för bättre prestanda. Bästa praxis är att ange som antalet GPU-eller CPU-noder.
      + ParallelRunStep matar inte in några paket, användaren måste inkludera **azureml-Core-** och **azureml-nu [Pandas, säkring]** -paket i miljö definitionen. Om anpassad Docker-avbildning används med user_managed_dependencies måste användaren installera Conda på avbildningen.
      
+ **Icke-bakåtkompatibla ändringar**
  + **azureml-pipeline-steps**
    + Föråldrad användning av azureml. dprep. data flöde som en giltig typ av indata för AutoMLConfig
  + **azureml-train-automl-client**
    + Föråldrad användning av azureml. dprep. data flöde som en giltig typ av indata för AutoMLConfig

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdade felet där en varning kan skrivas ut under `get_output` den frågade användaren att nedgradera klienten.
    + Uppdaterad Mac som förlitar sig på cudatoolkit = 9.0 eftersom den inte är tillgänglig i version 10 ännu.
    + Ta bort begränsningar för Prophet-och xgboost-modeller när de tränas på fjärrberäkning.
    + Förbättrad loggning i AutoML
    + Fel hanteringen för anpassade funktionalisering i prognos aktiviteter har förbättrats.
    + Nya funktioner som gör att användarna kan lägga till nedsatta funktioner för att generera prognoser.
    + Uppdaterar fel meddelandet för att Visa användar felet korrekt.
    + Stöd för cv_split_column_names som ska användas med training_data
    + Uppdatera loggning av undantags meddelandet och traceback.
  + **azureml-automl-runtime**
    + Aktivera guardrails för Prognosticering av värde imputations som saknas.
    + Förbättrad loggning i AutoML
    + Ökad detaljerad fel hantering för data förberedelse undantag
    + Ta bort begränsningar för phrophet-och xgboost-modeller när de tränas på fjärrberäkning.
    + `azureml-train-automl-runtime` och `azureml-automl-runtime` har uppdaterade beroenden för `pytorch` , `scipy` , och `cudatoolkit` . Vi stöder nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` och `cudatoolkit==10.1.243` .
    + Fel hanteringen för anpassade funktionalisering i prognos aktiviteter har förbättrats.
    + Mekanismen för frekvens identifiering av prognos data uppsättning har förbättrats.
    + Åtgärdat problem med Prophet modell träning för vissa data mängder.
    + Den automatiska identifieringen av maximal Horisont under prognosen har förbättrats.
    + Nya funktioner som gör att användarna kan lägga till nedsatta funktioner för att generera prognoser.
    +  Lägger till funktioner i funktionen prediktion för att ge prognoser utöver den tränade horisonten utan att behöva träna prognos modellen.
    + Stöd för cv_split_column_names som ska användas med training_data
  + **azureml-contrib-automl-DNN-Forecasting**
    + Förbättrad loggning i AutoML
  + **azureml-contrib-Mir**
    + Stöd har lagts till för Windows-tjänster i ManagedInferencing
    + Ta bort gamla MIR-arbetsflöden, till exempel Attach MIR Compute, SingleModelMirWebservice Class-rensa modell profileringen som placerats i contrib-Mir-paketet
  + **azureml-contrib-pipeline-steps**
    + Mindre korrigering för YAML-support
    + ParallelRunStep släpps till allmän tillgänglighet – azureml. contrib. pipeline. steg har ett meddelande om förvarning och flyttar till azureml. pipeline. steg
  + **azureml-contrib-reinforcementlearning**
    + Verktyget HUVUDWEBBADRESS Load Testing
    + HUVUDWEBBADRESS-uppskattning har smarta standardinställningar
  + **azureml-core**
    + Ta bort gamla MIR-arbetsflöden, till exempel Attach MIR Compute, SingleModelMirWebservice Class-rensa modell profileringen som placerats i contrib-Mir-paketet
    + Fast den information som ges till användaren vid profilerings fel: inkluderat förfrågnings-ID och formulerat om meddelandet för att vara mer meningsfullt. Nya profilerings arbets flödet har lagts till för profilering av utlöpare
    + Förbättrad feltext vid körning av data uppsättnings fel.
    + Stöd för privat länk CLI för arbets ytan har lagts till.
    + En valfri parameter har lagts `invalid_lines` till `Dataset.Tabular.from_json_lines_files` som gör det möjligt att ange hur rader som innehåller ogiltig JSON ska hanteras.
    + Vi kommer att bli föråldrad med körningen av data bearbetning i nästa version. Vi rekommenderar att du skapar ett faktiskt Amlcompute-kluster som ett beständigt beräknings mål och använder kluster namnet som beräknings mål i din körnings konfiguration. Se exempel på antecknings boken här: aka.ms/amlcomputenb
    + Förbättrade fel meddelanden i händelse av körnings fel för data mängder.
  + **azureml-dataprep**
    + Varning för att uppgradera pyarrow-versionen mer explicit.
    + Förbättrad fel hantering och meddelande som returneras om du inte kan köra data flöde.
  + **azureml-interpret**
    + Dokumentations uppdateringar av azureml-tolknings paketet.
    + Fasta tolknings paket och antecknings böcker är kompatibla med den senaste sklearn-uppdateringen
  + **azureml-opendatasets**
    + returnera ingen när inga data returneras.
    + Förbättra prestanda för to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Snabb korrigering för ParallelRunStep där inläsning från YAML var bruten
    + ParallelRunStep släpps till allmän tillgänglighet – azureml. contrib. pipeline. steg har ett meddelande om förvarning och flyttar till azureml. pipeline. steg – nya funktioner är: 1. Data uppsättningar som PipelineParameter 2. Ny parameter run_max_retry 3. Konfigurerbar append_row utdatafilens fil namn
  + **azureml-pipeline-steps**
    + Föråldrad azureml. dprep. data flöde som en giltig typ för indata.
    + Snabb korrigering för ParallelRunStep där inläsning från YAML var bruten
    + ParallelRunStep släpps till allmän tillgänglighet – azureml. contrib. pipeline. steg har ett meddelande om förvarning och flyttar till azureml. pipeline. steg – nya funktioner är:
      + Data uppsättningar som PipelineParameter
      + Ny parameter run_max_retry
      + Konfigurerbar append_row utdatafilens fil namn
  + **azureml-telemetry**
    + Uppdatera loggning av undantags meddelandet och traceback.
  + **azureml-train-automl-client**
    + Förbättrad loggning i AutoML
    + Uppdaterar fel meddelandet för att Visa användar felet korrekt.
    + Stöd för cv_split_column_names som ska användas med training_data
    + Föråldrad azureml. dprep. data flöde som en giltig typ för indata.
    + Uppdaterad Mac som förlitar sig på cudatoolkit = 9.0 eftersom den inte är tillgänglig i version 10 ännu.
    + Ta bort begränsningar för phrophet-och xgboost-modeller när de tränas på fjärrberäkning.
    + `azureml-train-automl-runtime` och `azureml-automl-runtime` har uppdaterade beroenden för `pytorch` , `scipy` , och `cudatoolkit` . Vi stöder nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` och `cudatoolkit==10.1.243` .
    + Nya funktioner som gör att användarna kan lägga till nedsatta funktioner för att generera prognoser.
  + **azureml-train-automl-runtime**
    + Förbättrad loggning i AutoML
    + Ökad detaljerad fel hantering för data förberedelse undantag
    + Ta bort begränsningar för phrophet-och xgboost-modeller när de tränas på fjärrberäkning.
    + `azureml-train-automl-runtime` och `azureml-automl-runtime` har uppdaterade beroenden för `pytorch` , `scipy` , och `cudatoolkit` . Vi stöder nu `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` och `cudatoolkit==10.1.243` .
    + Uppdaterar fel meddelandet för att Visa användar felet korrekt.
    + Stöd för cv_split_column_names som ska användas med training_data
  + **azureml-train-core**
    + En ny uppsättning HyperDrive-undantag har lagts till. azureml. träna. HyperDrive kommer nu att utlösa detaljerade undantag.
  + **azureml-widgets**
    + AzureML-widgetar visas inte i JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK för python v-1.5.0

+ **Nya funktioner**
  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning frigör för hands versions stöd med hjälp av [Ray](https://ray.io) -ramverket. Med `ReinforcementLearningEstimator` kan du använda utbildning av förstärka inlärnings agenter mellan GPU-och processor beräknings mål i Azure Machine Learning.

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Åtgärdar ett misstag kvar bakom varnings loggen i min tidigare PR. Loggen användes för fel sökning och av misstag var kvar bakom.
    + Fel korrigering: informera klienter om partiellt fel under profilering
  + **azureml-automl-core**
    + Påskynda Prophet/AutoArima-modellen i AutoML-prognoser genom att aktivera parallell montering för tids serien när data mängder har flera tids serier. För att du ska kunna dra nytta av den här nya funktionen rekommenderar vi att du anger "max_cores_per_iteration =-1" (det vill säga med alla tillgängliga processor kärnor) i AutoMLConfig.
    + Åtgärda fel vid utskrift guardrails i konsol gränssnittet
    + Fast fel meddelande för experimentation_timeout_hours
    + Föråldrade Tensorflow-modeller för AutoML.
  + **azureml-automl-runtime**
    + Fast fel meddelande för experimentation_timeout_hours
    + Fast icke-klassificerat undantag vid försök att deserialisera från cache Store
    + Påskynda Prophet/AutoArima-modellen i AutoML-prognoser genom att aktivera parallell montering för tids serien när data mängder har flera tids serier.
    + Har åtgärdat prognosen med aktiverat rullande fönster på data uppsättningar där test/förutsägelse-uppsättningen inte innehåller någon av kärnorna från inlärnings uppsättningen.
    + Förbättrad hantering av data som saknas
    + Fast problem med förutsägelse intervall under prognoser på data uppsättningar, som innehåller tids serier, som inte är justerade i tid.
    + En bättre validering av data form för prognos aktiviteter har lagts till.
    + Förbättrade frekvens identifieringen.
    + Ett bättre fel meddelande har skapats om kors validerings vikningen för prognos uppgifter inte kan genereras.
    + Korrigera konsol gränssnittet för att skriva ut det saknade värdet guardrail korrekt.
    + Tvingar datatype-kontroller på cv_split_indices indata i AutoMLConfig.
  + **azureml-cli-common**
    + Fel korrigering: informera klienter om partiellt fel under profilering
  + **azureml-contrib-Mir**
    + Lägger till en klass azureml. contrib. Mir. RevisionStatus som vidarebefordrar information om den för tillfället distribuerade MIR revision och den senaste versionen som anges av användaren. Den här klassen ingår i MirWebservice-objektet under attributet deployment_status.
    + Aktiverar uppdatering på webbtjänster av typen MirWebservice och dess underordnade klass SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Stöd har lagts till för Ray-0.8.3
    + AmlWindowsCompute stöder endast Azure Files som monterad lagring
    + Har bytt namn till health_check_timeout health_check_timeout_seconds
    + Vissa beskrivningar av klass/metod har åtgärd ATS.
  + **azureml-core**
    + Aktiverade WASB-> BLOB-konverteringar i Azure Government-och Kina-moln.
    + Korrigerar bugg för att tillåta att läsar roller använder AZ ml kör CLI-kommandon för att hämta körnings information
    + Onödig loggning har tagits bort under Azure ML-fjärrkörning med indata-datauppsättningar.
    + RCranPackage stöder nu "version"-parametern för CRAN-paketets version.
    + Fel korrigering: informera klienter om partiellt fel under profilering
    + Har lagt till flytt ALS hantering från europeisk typ för azureml-Core.
    + Aktiverade privat länk funktioner i Azure ml SDK.
    + När du skapar en TabularDataset med hjälp av `from_delimited_files` kan du ange om tomma värden ska läsas in som none eller en tom sträng genom att ange det booleska argumentet `empty_as_string` .
    + Tillagd flyt hantering i europeisk stil för data uppsättningar.
    + Förbättrade fel meddelanden vid monterings fel för data mängd.
  + **azureml-datadrift**
    + Frågan om data avvikelser från SDK hade ett fel som inte särskiljer de lägsta, högsta och genomsnittliga funktions måtten, vilket resulterar i dubblettvärden. Vi har åtgärdat den här buggen genom att förkorrigera målet eller bas linjen med mått namnen. Före: duplicera min, Max, medelvärde. Efter: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Förbättra hanteringen av Skriv begränsade python-miljöer när du säkerställer att .NET-beroenden krävs för data leverans.
    + Ett fast data flöde skapas i filen med inledande tomma poster.
    + Fel hanterings alternativ har lagts till på `to_partition_iterator` liknande sätt `to_pandas_dataframe` .
  + **azureml-interpret**
    + Minskad förklarings Sök väg längd gränser för att minska sannolikheten för att gå över Windows-gränsen
    + Bugfix för sparse-förklaringar som skapats med härma-förklaringen med en linjär surrogat modell.
  + **azureml-opendatasets**
    + Åtgärds frågan för MNIST-kolumner parsas som en sträng som ska vara int.
  + **azureml-pipeline-core**
    + Möjlighet att regenerate_outputs när du använder en modul som är inbäddad i en ModuleStep.
  + **azureml-train-automl-client**
    + Föråldrade Tensorflow-modeller för AutoML.
    + Korrigera användare Tillåt att algoritmer som inte stöds i lokalt läge listas
    + Dokument korrigeringar till AutoMLConfig.
    + Tvingar datatype-kontroller på cv_split_indices indata i AutoMLConfig.
    + Det gick inte att åtgärda problemet med AutoML-körningar i show_output
  + **azureml-train-automl-runtime**
    + Åtgärda ett fel i Ensemble-iterationer som förhindrade att tids gränsen för modell hämtningen skulle köras.
  + **azureml-train-core**
    + Korrigera skrivning i azureml. träna. DNN. Nccl-klassen.
    + Stöd för PyTorch version 1,5 i PyTorch-uppskattningen
    + Åtgärda problemet att det inte går att hämta Ramverks avbildningen i Azure Government region när du använder utbildnings Ramverks uppskattningar

  
## <a name="2020-05-04"></a>2020-05-04
**Ny Notebook-upplevelse**

Nu kan du skapa, redigera och dela Machine Learning-anteckningsböcker och filer direkt i Studio-webbupplevelsen av Azure Machine Learning. Du kan använda alla klasser och metoder som är tillgängliga i [Azure Machine Learning python SDK](/python/api/overview/azure/ml/intro) inifrån dessa antecknings böcker kom igång [här](./how-to-run-jupyter-notebooks.md)

**Nya funktioner som introducerats:**

+ Förbättrad redaktör (Monaco-redigerare) som används av VS Code 
+ Användar gränssnitt/UX-förbättringar
+ Cell verktygsfält
+ Verktygsfältet ny Notebook och beräknings kontroller
+ Antecknings statusfält 
+ Infogad kernel-växling
+ R-support
+ Förbättringar av hjälpmedel och lokalisering
+ Kommando palett
+ Ytterligare kortkommandon
+ Autosave
+ Bättre prestanda och tillförlitlighet

Få åtkomst till följande webbaserade redigerings verktyg från Studio:
    
| Webbaserat verktyg  |     Beskrivning  |
|---|---|
| Azure ML Studio-anteckningsböcker   |     Första redigering i klass för notebook-filer och stöd för alla åtgärder som är tillgängliga i Azure ML python SDK. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK för python v-1.4.0

+ **Nya funktioner**
  + AmlCompute-kluster har nu stöd för att konfigurera en hanterad identitet i klustret vid tidpunkten för etablering. Ange bara om du vill använda en tilldelad identitet eller en tilldelad identitet och skicka en identityId för den senare. Du kan sedan konfigurera behörigheter för att få åtkomst till olika resurser, t. ex. lagrings-eller ACR på ett sätt som data bearbetningen används för säker åtkomst till data, i stället för en tokenbaserad metod som AmlCompute använder idag. Kolla in vår SDK-referens för mer information om parametrarna.
  

+ **Icke-bakåtkompatibla ändringar**
  + AmlCompute-kluster har stöd för en förhands gransknings funktion med körnings skapande, som vi planerar om föråldrade i två veckor. Du kan fortsätta att skapa fasta beräknings mål som alltid genom att använda klassen Amlcompute, men den specifika metoden för att ange identifieraren "Amlcompute" som beräknings mål i Run config stöds inte inom en snar framtid. 

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Aktivera stöd för en typ som inte stöder hash när du beräknar antalet unika värden i en kolumn.
  + **azureml-core**
    + Förbättrad stabilitet vid läsning från Azure Blob Storage med hjälp av en TabularDataset.
    + Förbättrad dokumentation för- `grant_workspace_msi` parametern för `Datastore.register_azure_blob_store` .
    + Åtgärdat fel med `datastore.upload` som stöd för `src_dir` argumentet som slutar med en `/` eller `\` .
    + Ett åtgärds bara fel meddelande har lagts till vid överföring till ett Azure Blob Storage-datalager som inte har någon åtkomst nyckel eller SAS-token.
  + **azureml-interpret**
    + En övre bindning till fil storleken för visualiserings data har lagts till vid uppladdade förklaringar.
  + **azureml-train-automl-client**
    + Söker uttryckligen efter label_column_name & weight_column_name parametrar för AutoMLConfig vara av typen sträng.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep stöder nu data uppsättning som pipeline-parameter. Användaren kan skapa pipeline med exempel data uppsättning och kan ändra indata-datauppsättningen av samma typ (fil eller tabell) för ny pipeline-körning.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK för python v-1.3.0

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Ytterligare telemetri kring åtgärder efter utbildning har lagts till.
    + Påskyndar automatisk ARIMA-utbildning med hjälp av en villkorlig summering av kvadratmetoden (CSS) för en serie med längd som är längre än 100. Längden som används lagras som en konstant ARIMA_TRIGGER_CSS_TRAINING_LENGTH med TimeSeriesInternal-klassen på/src/azureml-automl-Core/azureml/automl/Core/Shared/constants.py
    + Användar loggningen av prognos körningar har förbättrats. mer information om vilken fas som körs för närvarande visas i loggen
    + Otillåten target_rolling_window_size anges till värden som är mindre än 2
  + **azureml-automl-runtime**
    + Förbättrat fel meddelandet som visas när dubbla tidsstämplar hittades.
    + Otillåten target_rolling_window_size anges till värden som är mindre än 2.
    + Korrigerade fördröjnings imputations felet. Problemet orsakades av otillräckligt antal observationer som krävdes för att skapa en serie på lång tids period. De "insäsongsiska" data används för att beräkna en partiell autokorrelations funktion (PACF) för att fastställa fördröjningens längd.
    + Aktive rad kolumn syfte funktionalisering anpassning för prognos uppgifter av funktionalisering config. Numeriska och kategoriska som kolumn syfte för prognos uppgifter stöds nu.
    + Funktionalisering anpassning av prognos uppgifter har Aktiver ATS av funktionalisering config.
    + Aktiverade Imputation-anpassning för prognos uppgifter efter funktionalisering-konfiguration. Konstant värde Imputation för mål kolumn och medelvärde, median, most_frequent och konstant värde Imputation för tränings data stöds nu.
  + **azureml-contrib-pipeline-steps**
    + Godkänn sträng beräknings namn som ska skickas till ParallelRunConfig
  + **azureml-core**
    +  API för att skapa en kopia av miljö objekt har lagts till i miljö. clone (new_name)
    +  Environment.docker.base_dockerfile accepterar sökväg. Om det går att matcha en fil kommer innehållet att läsas in i base_dockerfile miljö egenskap
    + Återställ automatiskt ömsesidigt uteslutande värden för base_image och base_dockerfile när användaren manuellt anger ett värde i Environment.docker
    + Lade till user_managed flagga i RSection som anger om miljön hanteras av användare eller av AzureML.
    + Data uppsättning: det gick inte att ladda fast data mängd om data Sök vägen innehåller Unicode-tecken.
    + Data uppsättning: förbättrad datauppsättning montering av cachelagring för att respektera det minsta disk utrymme som krävs i Azure Machine Learning Compute, vilket förhindrar att noden blir oanvändbar och gör att jobbet avbryts.
    + Data uppsättning: vi lägger till ett index för kolumnen Time Series när du har åtkomst till en Time Series-datauppsättning som en Pandas-dataframe, som används för att påskynda åtkomsten till data åtkomst med Time-serien.  Tidigare har indexet samma namn som kolumnen tidsstämpelkolumn, förvirrande användare om vilka är den faktiska tidsstämpelkolumn och som är indexet. Nu ger vi inte något speciellt namn till indexet eftersom det inte ska användas som en kolumn. 
    + Data uppsättning: problem med autentisering av fast data mängd i det suveräna molnet.
    + Data uppsättning: fast- `Dataset.to_spark_dataframe` felet för data uppsättningar som skapats från Azure postgresql-datalager.
  + **azureml-interpret**
    + Globala resultat har lagts till i visualiseringen om lokala prioritets värden är sparse
    + Uppdaterade azureml-tolkar för att använda tolka-community 0,9. *
    + Åtgärdat problem med hämtning av förklaring som hade sparse-utvärderings data
    + Stöd har lagts till för förklarings objektets sparse-format i AutoML
  + **azureml-pipeline-core**
    + Stöd för ComputeInstance som beräknings mål i pipelines
  + **azureml-train-automl-client**
    + Ytterligare telemetri kring åtgärder efter utbildning har lagts till.
    + Fast regressionen vid tidig avstängning
    + Föråldrad azureml. dprep. data flöde som en giltig typ för indata.
    +  Att ändra standard tiden för AutoML experiment ut till sex dagar.
  + **azureml-train-automl-runtime**
    + Ytterligare telemetri kring åtgärder efter utbildning har lagts till.
    + har lagt till stöd för sparse-AutoML slut punkt till slut punkt
  + **azureml-opendatasets**
    + Ytterligare telemetri för tjänst övervakaren har lagts till.
    + Aktivera front dörren för BLOB för att öka stabiliteten 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK för python v-1.2.0

+ **Icke-bakåtkompatibla ändringar**
  + Släpp stöd för python 2,7

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Lägger till "--Subscription-ID" till `az ml model/computetarget/service` kommandon i CLI
    + Lägga till stöd för att skicka CMK (Customer Managed Key) vault_url key_name och key_version för ACI-distribution
  + **azureml-automl-core** 
    + Aktiverade anpassade Imputation med konstant värde för både X-och y-dataprognosticering-uppgifter.
    + Löst problemet i med att visa fel meddelanden till användaren.    
  + **azureml-automl-runtime**
    + Löst problemet i med Prognosticering på data uppsättningarna som innehåller kärnor med bara en rad
    + Minskat mängden minne som krävs för prognos aktiviteterna.
    + Förbättrade fel meddelanden har lagts till om tids kolumnen har fel format.
    + Aktiverade anpassade Imputation med konstant värde för både X-och y-dataprognosticering-uppgifter.
  + **azureml-core**
    + Stöd för inläsning av ServicePrincipal från miljövariabler har lagts till: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID och AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introducerade en ny parameter `support_multi_line` för `Dataset.Tabular.from_delimited_files` : som standard ( `support_multi_line=False` ) tolkas alla rad brytningar, inklusive de i värdena för citerade fält, som en post brytning. Att läsa data på det här sättet är snabbare och mer optimerad för parallell körning på flera processor kärnor. Det kan dock leda till tyst produktion av fler poster med feljusterade fält värden. Detta ska vara inställt på `True` när de avgränsade filerna är kända för att innehålla citerade rad brytningar.
    + Har lagt till möjligheten att registrera ADLS Gen2 i Azure Machine Learning CLI
    + Parametern fine_grain_timestamp har bytt namn till timestamp och parametern coarse_grain_timestamp till partition_timestamp för metoden with_timestamp_columns () i TabularDataset för att bättre återspegla användningen av parametrarna.
    + Ökat Max experiment namnets längd till 255.
  + **azureml-interpret**
    + Uppdaterade azureml-tolkning till tolkning – community 0,7. *
  + **azureml – SDK**
    + Ändra till beroenden med kompatibel version Tilde för att stödja korrigering i för hands versionen och stabila versioner.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK för python v-1.1.5

+ **Funktions utfasning**
  + **Python 2,7**
    + Senaste versionen som stöd för python 2,7

+ **Icke-bakåtkompatibla ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1,1 Azure ML python SDK antar 2.0.0 för semantisk versions hantering. [Läs mer här](https://semver.org/). Alla efterföljande versioner kommer att följa det nya numreringsschema och det semantiska versions kontraktet. 

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Ändra slut punktens CLI-kommando namn från AZ ml-AKS till AZ ml-slutpunkt real tid för konsekvens.
    + uppdatera CLI-Installationsinstruktioner för stabil och experimentell gren CLI
    + En enskild instans profilering har åtgärd ATS för att skapa en rekommendation och gjorts tillgänglig i Core SDK.
  + **azureml-automl-core**
    + Aktive rad i batch-lägets härledning (tar flera rader en gång) för AutoML ONNX-modeller
    + Förbättrad identifiering av frekvenser för data uppsättningar, data som saknas eller innehåller oregelbundna data punkter
    + Har lagt till möjligheten att ta bort data punkter som inte uppfyller den dominerande frekvensen.
    + Har ändrat inmataren för att ta en lista med alternativ för att tillämpa Imputation-alternativen för motsvarande kolumner.
    + Fel loggningen har förbättrats.
  + **azureml-automl-runtime**
    + Löst problemet med det fel som har utlösts om kornig het saknas i inlärnings uppsättningen visades i test uppsättningen
    + Y_querys kravet har tagits bort under poängsättning i prognos tjänsten
    + Har åtgärdat problemet med prognoser när data uppsättningen innehåller korta kärnor med långa tids luckor.
    + Löst problemet när den automatiska Max horisonten är aktive rad och datum kolumnen innehåller datum i form av strängar. Rätt konvertering och fel meddelanden lades till för när det inte går att konvertera till datum
    + Använda inbyggd NumPy och SciPy för serialisering och deserialisering av mellanliggande data för FileCacheStore (används för lokala AutoML-körningar)
    + Ett fel har åtgärd ATS där underordnade körningar kunde fastna i körnings tillstånd.
    + Ökad hastighet på funktionalisering.
    + Fast frekvens kontrollen under poängsättningen kräver nu inte prognos uppgifterna strikt frekvens mellan tåg och test uppsättning.
    + Har ändrat inmataren för att ta en lista med alternativ för att tillämpa Imputation-alternativen för motsvarande kolumner.
    + Fasta fel relaterade till val av slacktid.
    + Fast det oklassificerade fel som har Aktiver ATS i data uppsättningarna, med kärnor med den enskilda raden
    + Löst problemet med frekvens identifieringens långsammahet.
    + Åtgärdar en bugg i AutoML-undantags hanteringen som orsakade att det faktiska skälet till att träningen inte har bytts ut av en AttributeError.
  + **azureml-cli-common**
    + En enskild instans profilering har åtgärd ATS för att skapa en rekommendation och gjorts tillgänglig i Core SDK.
  + **azureml-contrib-Mir**
    + Lägger till funktioner i MirWebservice-klassen för att hämta åtkomsttoken
    + Använd token auth för MirWebservice som standard under MirWebservice. Run () endast anrops uppdatering om anropet Miss lyckas
    + Mir WebService-distributionen kräver nu korrekt SKU: er [Standard_DS2_v2, Standard_F16, Standard_A2_v2] i stället för [Ds2v2, A2v2 och F16].
  + **azureml-contrib-pipeline-steps**
    + Valfri parameter side_inputs lagts till i ParallelRunStep. Den här parametern kan användas för att montera en mapp på behållaren. Typer som stöds för närvarande är DataReference och PipelineData.
    + Parametrar som skickas i ParallelRunConfig kan skrivas över genom att skicka pipeline-parametrar nu. Nya pipeline-parametrar som stöds aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count och aml_process_count_per_node ingår redan i den tidigare versionen).
  + **azureml-core**
    + Distribuerade AzureML-webbtjänster kommer nu att `INFO` loggas som standard. Detta kan styras genom att ställa in `AZUREML_LOG_LEVEL` miljövariabeln i den distribuerade tjänsten.
    + Python SDK använder identifierings tjänsten för att använda "API"-slut punkten i stället för "pipeliner".
    + Växla till de nya vägarna i alla SDK-anrop.
    + Routning av anrop till ModelManagementService har ändrats till en ny enhetlig struktur.
      + Uppdaterings metoden för arbets ytan har gjorts offentligt tillgänglig.
      + Image_build_compute parameter har lagts till i uppdaterings metoden för arbets ytan så att användaren kan uppdatera beräkningarna för avbildnings versionen.
    + Utfasnings meddelanden har lagts till i det gamla profilerings flödet. Fast profilering av processor-och minnes gränser.
    + Lade till RSection som en del av miljön för att köra R-jobb.
    + Validering har lagts till `Dataset.mount` för att generera ett fel när data källans källa inte kan nås eller inte innehåller några data.
    + Tillagt `--grant-workspace-msi-access` som en ytterligare parameter för data lagrets CLI för registrering av Azure Blob-behållare som gör att du kan registrera BLOB-behållare som ligger bakom ett VNet.
    + En enskild instans profilering har åtgärd ATS för att skapa en rekommendation och gjorts tillgänglig i Core SDK.
    + Korrigerade problemet i aks.py _deploy.
    + Validerar integriteten för modeller som överförs för att undvika tyst lagrings problem.
    + Användaren kan nu ange ett värde för auth-nyckeln när nycklar för webtjänster återskapas.
    + Åtgärdat fel där versaler inte kan användas som data uppsättnings namn.
  + **azureml – standardvärden**
    + `azureml-dataprep` kommer nu att installeras som en del av `azureml-defaults` . Du behöver inte längre installera data prepare [säkring] manuellt på beräknings mål för att montera data mängder.
  + **azureml-interpret**
    + Uppdaterade azureml-tolkning till tolkning – community 0,6. *
    + Uppdaterad azureml-tolkning till är beroende av 0.5.0 för tolka-community
    + Azureml-undantag har lagts till i azureml-tolka
    + Fast DeepScoringExplainer-serialisering för keras-modeller
  + **azureml-mlflow**
    + Lägg till stöd för suveräna moln till azureml. mlflow
  + **azureml-pipeline-core**
    + I pipeline för batch-Poäng för batch används nu ParallelRunStep
    + Ett fel har åtgärd ATS där PythonScriptStep resultat kan återanvändas felaktigt trots att argument listan ändrades
    + Har lagt till möjligheten att ange kolumner typ när du anropar parse_ * metoder på `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Har flyttat `AutoMLStep` till `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
    + Exempel på dokumentation som lagts till för data uppsättning som PythonScriptStep-indata
  + **azureml-tensorboard**
    + uppdaterade azureml-tensorboard för att stödja tensorflow 2,0
    + Visa rätt port nummer när du använder en anpassad Tensorboard-port på en beräknings instans
  + **azureml-train-automl-client**
    + Ett problem har åtgärd ATS där vissa paket kan installeras vid felaktiga versioner av fjärrkörningar.
    + fast FeaturizationConfig åsidosätter problem som filtrerar anpassad funktionalisering-konfiguration.
  + **azureml-train-automl-runtime**
    + Åtgärdade problemet med frekvens identifiering i fjärrkörningar
    + Har flyttat `AutoMLStep` i `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Stöd för PyTorch version 1,4 i PyTorch-uppskattningen
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK för python v 1.1.2 RC0 (för hands version)

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Aktive rad i batch-lägets härledning (tar flera rader en gång) för AutoML ONNX-modeller
    + Förbättrad identifiering av frekvenser för data uppsättningar, data som saknas eller innehåller oregelbundna data punkter
    + Har lagt till möjligheten att ta bort data punkter som inte uppfyller den dominerande frekvensen.
  + **azureml-automl-runtime**
    + Löst problemet med det fel som har utlösts om kornig het saknas i inlärnings uppsättningen visades i test uppsättningen
    + Y_querys kravet har tagits bort under poängsättning i prognos tjänsten
  + **azureml-contrib-Mir**
    + Lägger till funktioner i MirWebservice-klassen för att hämta åtkomsttoken
  + **azureml-core**
    + Distribuerade AzureML-webbtjänster kommer nu att `INFO` loggas som standard. Detta kan styras genom att ställa in `AZUREML_LOG_LEVEL` miljövariabeln i den distribuerade tjänsten.
    + Korrigera iterera för `Dataset.get_all` att returnera alla data uppsättningar som är registrerade i arbets ytan.
    + Förbättra fel meddelandet när ogiltig typ skickas till `path` argument för att skapa API: er för data uppsättning.
    + Python SDK använder identifierings tjänsten för att använda "API"-slut punkten i stället för "pipeliner".
    + Växla till de nya vägarna i alla SDK-anrop
    + Ändrar routning av anrop till ModelManagementService till en ny enhetlig struktur
      + Uppdaterings metoden för arbets ytan har gjorts offentligt tillgänglig.
      + Image_build_compute parameter har lagts till i uppdaterings metoden för arbets ytan så att användaren kan uppdatera beräkningarna för avbildnings versionen
    +  Utfasnings meddelanden har lagts till i det gamla profilerings flödet. Fast profilering av processor-och minnes gränser
  + **azureml-interpret**
    + uppdatera azureml – tolka till tolka – community 0,6. *
  + **azureml-mlflow**
    + Lägg till stöd för suveräna moln till azureml. mlflow
  + **azureml-pipeline-steps**
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package` . Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Ett problem har åtgärd ATS där vissa paket kan installeras vid felaktiga versioner av fjärrkörningar.
  + **azureml-train-automl-runtime**
    + Åtgärdade problemet med frekvens identifiering i fjärrkörningar
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package` . Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package` . Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK för python v 1.1.1 RC0 (för hands version)

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + En enskild instans profilering har åtgärd ATS för att skapa en rekommendation och gjorts tillgänglig i Core SDK.
  + **azureml-automl-core**
    + Fel loggningen har förbättrats.
  + **azureml-automl-runtime**
    + Har åtgärdat problemet med prognoser när data uppsättningen innehåller korta kärnor med långa tids luckor.
    + Löst problemet när den automatiska Max horisonten är aktive rad och datum kolumnen innehåller datum i form av strängar. Vi har lagt till korrekt konverterings-och lämpliga-fel om det inte går att konvertera till datum
    + Använda inbyggd NumPy och SciPy för serialisering och deserialisering av mellanliggande data för FileCacheStore (används för lokala AutoML-körningar)
    + Ett fel har åtgärd ATS där underordnade körningar kunde fastna i körnings tillstånd.
  + **azureml-cli-common**
    + En enskild instans profilering har åtgärd ATS för att skapa en rekommendation och gjorts tillgänglig i Core SDK.
  + **azureml-core**
    + Tillagt `--grant-workspace-msi-access` som en ytterligare parameter för data lagrets CLI för registrering av Azure Blob-behållare som gör att du kan registrera BLOB-behållare som ligger bakom ett VNet
    + En enskild instans profilering har åtgärd ATS för att skapa en rekommendation och gjorts tillgänglig i Core SDK.
    + Korrigerade problemet i aks.py _deploy
    + Validerar integriteten för modeller som överförs för att undvika tyst lagrings problem.
  + **azureml-interpret**
    + azureml-undantag har lagts till i azureml-tolka
    + fast DeepScoringExplainer-serialisering för keras-modeller
  + **azureml-pipeline-core**
    + I pipeline för batch-Poäng för batch används nu ParallelRunStep
  + **azureml-pipeline-steps**
    + Har flyttat `AutoMLStep` i `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Valfri parameter side_inputs lagts till i ParallelRunStep. Den här parametern kan användas för att montera en mapp på behållaren. Typer som stöds för närvarande är DataReference och PipelineData.
  + **azureml-tensorboard**
    + uppdaterade azureml-tensorboard för att stödja tensorflow 2,0
  + **azureml-train-automl-client**
    + fast FeaturizationConfig åsidosätter problem som filtrerar anpassad funktionalisering-konfiguration.
  + **azureml-train-automl-runtime**
    + Har flyttat `AutoMLStep` i `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Stöd för PyTorch version 1,4 i PyTorch-uppskattningen
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK för python v 1.1.0 RC0 (för hands version)

+ **Icke-bakåtkompatibla ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1,1 Azure ML python SDK antar 2.0.0 för semantisk versions hantering. [Läs mer här](https://semver.org/). Alla efterföljande versioner kommer att följa det nya numreringsschema och det semantiska versions kontraktet. 
  
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Ökad hastighet på funktionalisering.
    + Fast frekvens kontrollen under poängsättningen är nu i prognos uppgifterna att vi inte kräver strikt frekvens mellan tåg och test uppsättning.
  + **azureml-core**
    + Användaren kan nu ange ett värde för auth-nyckeln när nycklar för webtjänster återskapas.
  + **azureml-interpret**
    + Uppdaterad azureml-tolkning till är beroende av 0.5.0 för tolka-community
  + **azureml-pipeline-core**
    + Ett fel har åtgärd ATS där PythonScriptStep resultat kan återanvändas felaktigt trots att argument listan ändrades
  + **azureml-pipeline-steps**
    + Exempel på dokumentation som lagts till för data uppsättning som PythonScriptStep-indata
  + **azureml-contrib-pipeline-steps**
    + Parametrar som skickas i ParallelRunConfig kan skrivas över genom att skicka pipeline-parametrar nu. Nya pipeline-parametrar som stöds aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count och aml_process_count_per_node ingår redan i den tidigare versionen).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK för python v-1.0.85

+ **Nya funktioner**
  + **azureml-core**
    + Hämta den aktuella kärn användningen och kvot begränsningen för AmlCompute-resurser på en specifik arbets yta och prenumeration
  
  + **azureml-contrib-pipeline-steps**
    + Tillåt användare att skicka tabell data uppsättning som mellanliggande resultat från föregående steg till parallelrunstep

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Kravet på y_query kolumn i begäran till den distribuerade prognos tjänsten har tagits bort. 
    + Y_query har tagits bort från Dominick för tjänsten orange juice Notebook.
    + Åtgärdade fel som förhindrar prognoser i distribuerade modeller, körs på data uppsättningar med datum tid kolumner.
    + Matthews korrelations faktor har lagts till som klassificerings mått för både binära och multiklass-klassificering.
  + **azureml-contrib-interpret**
    + Borttagna text förklaringar från azureml-contrib-tolka som text förklaring har flyttats till tolknings texten lagrings platsen som kommer att lanseras snart.
  + **azureml-core**
    + Data uppsättning: användningen av fil data uppsättningen är inte längre beroende av att numpy och Pandas har installerats i python-miljö.
    + Ändra LocalWebservice.wait_for_deployment () för att kontrol lera status för den lokala Docker-behållaren innan du försöker pinga dess hälso slut punkt, vilket minskar den tid det tar att rapportera en misslyckad distribution.
    + Initieringen av en intern egenskap som används i LocalWebservice har åtgärd ATS. Läs in igen () när serviceobjektet skapas från en befintlig distribution med hjälp av LocalWebservice ()-konstruktorn.
    + Redigerat fel meddelande för klargörande.
    + En ny metod som kallas get_access_token () har lagts till i AksWebservice som kommer att returnera AksServiceAccessToken-objekt, som innehåller åtkomsttoken, uppdateras efter tidsstämpel, förfallo datum för timestamp och tokentyp. 
    + Föråldrad befintlig get_token ()-metod i AksWebservice som den nya metoden returnerar all information som den här metoden returnerar.
    + Ändrade utdata från AZ ml-kommandot Get-Access-token. Token har bytt namn till accessToken och refreshBy till refreshAfter. Har lagt till egenskaperna expiryOn och tokenType.
    + Fast get_active_runs
  + **azureml-explain-model**
    + uppdaterade Shap till 0.33.0 och tolkning – community till 0,4. *
  + **azureml-interpret**
    + uppdaterade Shap till 0.33.0 och tolkning – community till 0,4. *
  + **azureml-train-automl-runtime**
    + Matthews korrelations faktor har lagts till som klassificerings mått för både binära och multiklass-klassificering.
    + Förbearbeta flagga från kod och ersatt med funktionalisering-funktionalisering är aktiverat som standard

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK för python v-1.0.83

+ **Nya funktioner**
  + Data uppsättning: Lägg till två alternativ `on_error` och `out_of_range_datetime` för `to_pandas_dataframe` att rapportera fel när data innehåller fel värden i stället för att fylla dem med `None` .
  + Arbets yta: har lagt till `hbi_workspace` flaggan för arbets ytor med känsliga data som möjliggör ytterligare kryptering och inaktiverar avancerad diagnostik på arbets ytor. Vi har också lagt till stöd för att ta med dina egna nycklar för den associerade Cosmos DB-instansen genom att ange `cmk_keyvault` `resource_cmk_uri` parametrarna och när du skapar en arbets yta, vilket skapar en Cosmos DB instans i prenumerationen när du skapar din arbets yta. [Läs mer här.](./concept-data-encryption.md#azure-cosmos-db)

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + En regression har åtgärd ATS som gjorde att en TypeError skulle höjas när AutoML kördes på python-versioner under 3.5.4.
  + **azureml-core**
    + Det gick `datastore.upload_files` inte att använda den relativa sökvägen som inte startades med en fast bugg i `./` .
    + Har lagt till utfasnings meddelanden för alla bild klass kod Sök vägar
    + Fast Modellhantering URL-konstruktion för Azure Kina 21Vianet-region.
    + Ett problem har åtgärd ATS där modeller som använder source_dir inte kan paketeras för Azure Functions.    
    + Lade till ett alternativ till [Environment.build_local ()](/python/api/azureml-core/azureml.core.environment.environment) för att skicka en avbildning till azureml-arbetsyta behållare register
    + SDK har uppdaterats för att använda ett nytt token-bibliotek på Azure Synapse på ett sätt som är kompatibelt.
  + **azureml-interpret**
    + Ett fel upptäcktes där ingen returnerades när det inte gick att hämta några förklaringar. Genererar nu ett undantag som matchar beteendet någon annan stans.
  + **azureml-pipeline-steps**
    + Otillåten överföring `DatasetConsumptionConfig` s till- `Estimator` `inputs` parameter när `Estimator` ska användas i en `EstimatorStep` .
  + **azureml – SDK**
    + AutoML-klienten har lagts till i azureml-SDK-paketet, vilket gör det möjligt att skicka fjärrAutoML körningar utan att installera det fullständiga AutoML-paketet.
  + **azureml-train-automl-client**
    + Korrigerad justering av konsol utdata för AutoML-körningar
    + En bugg har åtgärd ATS där felaktig version av Pandas kan installeras på fjärran sluten amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK för python v-1.0.81

+ **Fel korrigeringar och förbättringar**
  + **azureml-contrib-interpret**
    + skjuta upp Shap-beroendet till tolka-community från azureml-tolka
  + **azureml-core**
    + Compute Target kan nu anges som en parameter för motsvarande distributions konfigurations objekt. Detta är i synnerhet namnet på det beräknings mål som ska distribueras till, inte SDK-objektet.
    + Lade till CreatedBy-information till modell-och tjänst objekt. Kan nås through.created_by
    + Fast ContainerImage. Run (), som inte konfigurerades på rätt sätt i Docker-behållarens HTTP-port.
    + Gör `azureml-dataprep` valfritt för `az ml dataset register` CLI-kommando
    + En bugg har åtgärd ATS som `TabularDataset.to_pandas_dataframe` felaktigt skulle återgå till en alternativ läsare och skriva ut en varning.
  + **azureml-explain-model**
    + skjuta upp Shap-beroendet till tolka-community från azureml-tolka
  + **azureml-pipeline-core**
    + Ett nytt pipeline-steg har lagts `NotebookRunnerStep` till för att köra en lokal antecknings bok som ett steg i pipeline.
    + Borttagna inaktuella get_all-funktioner för PublishedPipelines, scheman och PipelineEndpoints
  + **azureml-train-automl-client**
    + Startade utfasningen av data_script som inAutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK för python v-1.0.79

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Borttagna featurizationConfig för att loggas
      + Loggningen har uppdaterats till loggen "Auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Stöd har lagts till för Pandas. Serie-och Pandas. Kategoriska för att identifiera kolumn data typ. Tidigare stödde endast numpy. ndarray som stöds
      + Tillagda relaterade kod ändringar för hantering av kategoriska-dtype på rätt sätt.
    + Prognos funktions gränssnittet har förbättrats: parametern y_pred gjordes valfri. -Docstrings har förbättrats.
  + **azureml-contrib-dataset**
    + Ett fel har åtgärd ATS där det inte gick att montera etiketterade data uppsättningar.
  + **azureml-core**
    + Fel korrigering för `Environment.from_existing_conda_environment(name, conda_environment_name)` . Användaren kan skapa en miljö instans som är en exakt replik av den lokala miljön
    + Ändrade Time Series-relaterade data uppsättnings metoder till `include_boundary=True` som standard.
  + **azureml-train-automl-client**
    + Ett problem har åtgärd ATS där verifierings resultaten inte skrivs ut när Visa utdata är inställt på falskt.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK för python v-1.0.76

+ **Icke-bakåtkompatibla ändringar**
  + Azureml-träna-AutoML uppgraderings problem
    + Uppgradering till azureml-träna-automl>= 1.0.76 från azureml-träna-automl<1.0.76 kan orsaka partiella installationer, vilket gör att vissa AutoML-importer inte kan köras. Du kan lösa detta genom att köra installations skriptet som finns på https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Eller om du använder pip direkt kan du:
      + "pip install--Upgrade azureml-träna-automl"
      + "pip install--IGNORE-installerat azureml-träna-automl-Client"
    + eller så kan du avinstallera den gamla versionen innan du uppgraderar
      + "pip Uninstall azureml-träna-automl"
      + "pip install azureml-träna-automl"

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + AutoML kommer nu att ta hänsyn till både sanna och falska klasser när du beräknar Genomsnittligt skalära mått för binära klassificerings aktiviteter.
    + Maskin inlärnings-och tränings kod har flyttats i AzureML-AutoML-Core till ett nytt paket AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + När du anropar `to_pandas_dataframe` en etikettad data uppsättning med alternativet Hämta kan du nu ange om du vill skriva över befintliga filer eller inte.
    + När du anropar `keep_columns` eller `drop_columns` som resulterar i en tids serie, etikett eller bild kolumn som släpps, tas även motsvarande funktioner bort för data uppsättningen.
    + Åtgärdade ett problem med pytorch-inläsaren för objekt identifiering.
  + **azureml-contrib-interpret**
    + Widgeten förklarings instrument panel togs bort från azureml-contrib-tolka, ändrade paket för att referera till den nya i interpret_community
    + Uppdaterad version av tolka-community till 0.2.0
  + **azureml-core**
    + Förbättra prestanda för `workspace.datasets` .
    + Har lagt till möjligheten att registrera Azure SQL Database data lager med hjälp av användar namn och lösenordsautentisering
    + Korrigering för inläsning av RunConfigurations från relativa sökvägar.
    + När du anropar `keep_columns` eller `drop_columns` som resulterar i en Time Series-kolumn som släpps, tas även motsvarande funktioner bort för data uppsättningen.
  + **azureml-interpret**
    + uppdaterad version av tolka-community till 0.2.0
  + **azureml-pipeline-steps**
    + Dokumenterade värden för `runconfig_pipeline_params` i pipeline-steg för Azure Machine Learning.
  + **azureml-pipeline-core**
    + CLI-alternativet har lagts till för att hämta utdata i JSON-format för pipeline-kommandon.
  + **azureml-träna-automl**
    + Dela AzureML-träna-AutoML i två paket, ett klient paket AzureML-träna-AutoML-Client och ett ML-utbildnings paket AzureML-träna-AutoML-runtime
  + **azureml-train-automl-client**
    + Lade till en tunn klient för att skicka AutoML experiment utan att behöva installera några maskin inlärnings beroenden lokalt.
    + Korrigerad loggning av automatiskt identifierade lags, rullande fönster storlekar och maximala horisonter i fjärrkörningar.
  + **azureml-train-automl-runtime**
    + Ett nytt AutoML-paket har lagts till för att isolera maskin inlärnings-och körnings komponenter från klienten.
  + **azureml-contrib-träna-huvudwebbadress**
    + Stöd för förstärka inlärning i SDK.
    + Stöd för AmlWindowsCompute har lagts till i HUVUDWEBBADRESS SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK för python v-1.0.74

  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-dataset**
      + När du har importerat azureml-contrib-dataset kan du anropa `Dataset.Labeled.from_json_lines` i stället för `._Labeled` att skapa en etikettad data uppsättning.
      + När du anropar `to_pandas_dataframe` en etikettad data uppsättning med alternativet Hämta kan du nu ange om du vill skriva över befintliga filer eller inte.
      + När du anropar `keep_columns` eller `drop_columns` som resulterar i en tids serie, etikett eller bild kolumn som släpps, tas även motsvarande funktioner bort för data uppsättningen.
      + Fasta problem med PyTorch-inläsaren vid anrop `dataset.to_torchvision()` .

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Modell profilering har lagts till för Preview CLI.
    + Korrigerings ändringar i Azure Storage orsaka att AzureML CLI kraschar.
    + Load Balancer typ har lagts till i MLC för AKS-typer
  + **azureml-automl-core**
    + Korrigerade problemet med identifiering av maximal Horisont i tids serien, med saknade värden och flera kärnor.
    + Löst problemet med fel under genereringen av kors validerings delningar.
    + Ersätt det här avsnittet med ett meddelande i markdown-format som visas i viktig information: – förbättrad hantering av korta kärnor i prognos data uppsättningar.
    + Har åtgärdat problemet med maskering av viss användar information under loggningen. – Förbättrad loggning av felen under prognos körningar.
    + Lägga till psutil som ett Conda-beroende av den automatiskt genererade YML-distributions filen.
  + **azureml-contrib-Mir**
    + Korrigerings ändringar i Azure Storage orsaka att AzureML CLI kraschar.
  + **azureml-core**
    + Åtgärdar en bugg som orsakade modeller som har distribuerats på Azure Functions att skapa 500.
    + Ett problem har åtgärd ATS där amlignore-filen inte tillämpades på ögonblicks bilder.
    + Lade till en ny API-amlcompute.get_active_runs som returnerar en generator för körning och köade körningar på en specifik amlcompute.
    + Load Balancer typ har lagts till i MLC för AKS-typer.
    + Parametern append_prefix bool har lagts till för att download_files i run.py och download_artifacts_from_prefix i artifacts_client. Den här flaggan används för att selektivt förenkla den ursprungliga sökvägen så att bara fil-eller mappnamnet läggs till i output_directory
    + Åtgärda deserialiserings problem för `run_config.yml` med data uppsättnings användning.
    + När du anropar `keep_columns` eller `drop_columns` som resulterar i en Time Series-kolumn som släpps, tas även motsvarande funktioner bort för data uppsättningen.
  + **azureml-interpret**
    + Uppdaterad tolkning – community-version till 0.1.0.3
  + **azureml-träna-automl**
    + Ett problem har åtgärd ATS där automl_step kanske inte kan skriva ut validerings problem.
    + Fast register_model att lyckas även om modellens miljö saknar beroenden lokalt.
    + Ett problem har åtgärd ATS där vissa fjärrkörningar inte hade dock ATS aktiverat.
    + Lägg till loggning av undantaget som orsakar att en lokal körning upphör för tidigt.
  + **azureml-train-core**
    + Överväg resume_from körningar i beräkningen av automatisk inställning av den automatiska inställningen för parameter justering med bästa underordnade körning.
  + **azureml-pipeline-core**
    + Fast parameter hantering i uppförande av pipeline-argument.
    + Pipeline-beskrivning och typ av yaml-parameter har lagts till.
    + Nytt yaml-format för pipeline-steget och tillägg av utfasnings varning för gammalt format.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webb upplevelse

Den gemensamma arbets ytans landnings sida på [https://ml.azure.com](https://ml.azure.com) har förbättrats och anpassats som Azure Machine Learning Studio.

Från Studio kan du träna, testa, distribuera och hantera Azure Machine Learning till gångar, till exempel data uppsättningar, pipeliner, modeller, slut punkter och mycket annat.

Få åtkomst till följande webbaserade redigerings verktyg från Studio:

| Webbaserat verktyg | Beskrivning | 
|-|-|-|
| Notebook VM (för hands version) | Helt hanterad molnbaserad arbets Station | 
| [Automatisk maskin inlärning](tutorial-first-experiment-automated-ml.md) (för hands version) | Ingen kod upplevelse för automatisering av maskin inlärnings modell utveckling | 
| [Designer](concept-designer.md) | Verktyget dra och släpp Machine Learning-modellering som tidigare kallades det visuella gränssnittet | 


### <a name="azure-machine-learning-designer-enhancements"></a>Förbättringar i Azure Machine Learning designer

+ Tidigare känt som det visuella gränssnittet 
+    11 nya [moduler](algorithm-module-reference/module-reference.md) , inklusive rekommendationer, klassificerare och utbildnings verktyg, inklusive funktions teknik, kors validering och data omvandling.

### <a name="r-sdk"></a>R SDK 
 
Data forskare och AI-utvecklare använder [Azure Machine Learning SDK för R](https://github.com/Azure/azureml-sdk-for-r) för att skapa och köra Machine Learning-arbetsflöden med Azure Machine Learning.

Azure Machine Learning SDK för R använder paketet för att `reticulate` binda till python SDK. Genom att binda direkt till python ger SDK för R dig åtkomst till kärn objekt och metoder som implementeras i python SDK från valfri R-miljö som du väljer.

Huvud funktionerna i SDK: n är:

+    Hantera molnresurser för övervakning, loggning och organisering av Machine Learning-experiment.
+    Träna modeller med moln resurser, inklusive GPU-accelererad modell träning.
+    Distribuera dina modeller som webservices på Azure Container Instances (ACI) och Azure Kubernetes service (AKS).

Fullständig dokumentation finns på [webbplatsen för paket](https://azure.github.io/azureml-sdk-for-r) .

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integration med Event Grid 

Azure Machine Learning är nu en resurs leverantör för Event Grid kan du konfigurera maskin inlärnings händelser via Azure Portal eller Azure CLI. Användare kan skapa händelser för slut för ande, modell registrering, modell distribution och data avvikelse. Dessa händelser kan dirigeras till händelse hanterare som stöds av Event Grid för förbrukning. Mer information finns i artikeln om händelse [scheman](../event-grid/event-schema-machine-learning.md) och [självstudier](how-to-use-event-grid.md) i Machine Learning.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK för python v-1.0.72

+ **Nya funktioner**
  + Data uppsättningen har lagts till genom [**azureml-datadrift-**](/python/api/azureml-datadrift) paketet, vilket möjliggör övervakning av Time Series-datauppsättningar för data drift eller andra statistiska förändringar över tid. Aviseringar och händelser kan utlösas om avvikelser upptäcks eller andra villkor för data är uppfyllda. Se [vår dokumentation](how-to-monitor-datasets.md) för mer information.
  + Vi presenterar två nya versioner (kallas även SKU: er utbytbara) i Azure Machine Learning. I den här versionen kan du nu skapa en standard-eller Enterprise Azure Machine Learning-arbetsyta. Alla befintliga arbets ytor är standard versionen och du kan gå till Azure Portal eller Studio för att uppgradera arbets ytan när som helst. Du kan skapa antingen en Basic-eller Enterprise-arbetsyta från Azure Portal. Läs [vår dokumentation](./how-to-manage-workspace.md) om du vill veta mer. Från SDK kan du bestämma vilken version av din arbets yta som ska användas med hjälp av egenskapen "SKU" för ditt arbets objekt.
  + Vi har också gjort förbättringar för Azure Machine Learning beräkning – nu kan du visa mått för dina kluster (som till exempel totala antalet noder, köra noder, total kärn kvot) i Azure Monitor, förutom att Visa diagnostikloggar för fel sökning. Dessutom kan du också visa för tillfället pågående eller köade körningar i klustret och information, till exempel IP-adresser för de olika noderna i klustret. Du kan visa dessa antingen i portalen eller genom att använda motsvarande funktioner i SDK eller CLI.

  + **Förhandsgranskningsfunktioner**
    + Vi frigör för hands versions stöd för disk kryptering av din lokala SSD i Azure Machine Learning Compute. Skapa ett support ärende för att få din prenumeration att tillåta att den här funktionen används.
    + Offentlig för hands version av Azure Machine Learning batch-härledning. Azure Machine Learning batch-härledning riktas mot stora jobb som inte är tids känsliga. Med batch-härledning får du en kostnads effektiv skalnings beräkning med oöverträffade data flöden för asynkrona program. Den är optimerad för hög genom strömning, brand-och-glömma-härledning över stora data mängder.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Aktiverade funktioner för etikettad data mängd
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

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + CLI stöder nu modell paketering.
    + Data mängds-CLI har lagts till. Mer information: `az ml dataset --help`
    + Stöd har lagts till för distribution och paketering av modeller som stöds (ONNX, scikit-och TensorFlow) utan en InferenceConfig-instans.
    + Överskriven flagga för tjänst distribution (ACI och AKS) har lagts till i SDK och CLI. Om det här alternativet anges skrivs den befintliga tjänsten över om det redan finns en tjänst med namnet. Om tjänsten inte finns, skapas en ny tjänst.
    + Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. – Modell registreringen accepterar exempel indata, exempel på utdata och resurs konfiguration för modellen.
  + **azureml-automl-core**
    + Träna en iteration kan bara köras i en underordnad process när körnings begränsningar anges.
    + En guardrail för prognos aktiviteter har lagts till för att kontrol lera om en angiven max_horizon kommer att orsaka minnes problem på den angivna datorn eller inte. Om det kommer visas ett guardrail-meddelande.
    + Stöd har lagts till för komplexa frekvenser som två år och en månad. – Extra förståeligt fel meddelande om frekvensen inte kan fastställas.
    + Lägg till azureml – standardvärdet för automatiskt skapade Conda-kuvert för att lösa modell distributions problemet
    + Tillåt att mellanliggande data i Azure Machine Learning pipeline konverteras till data uppsättning i tabell format och används i `AutoMLStep` .
    + Implementerad uppdatering av kolumn syfte för strömning.
    + Implementerad transformator parameter uppdatering för imputerade och HashOneHotEncoder för strömning.
    + Den aktuella data storleken och den minsta data storlek som krävs för verifierings fel meddelandena har lagts till.
    + Den minsta data storlek som krävs för kors validering har uppdaterats för att garantera minst två exempel i varje validerings vikning.
  + **azureml-cli-common**
    + CLI stöder nu modell paketering.
    + Modeller kan registreras med två nya ramverk, Onnx och Tensorflow.
    + Modell registreringen tillåter indata från indata, exempel på utdata och resurs konfiguration för modellen.
  + **azureml-contrib-gbdt**
    + korrigerade versions kanalen för antecknings boken
    + En varning har lagts till för icke-AmlCompute beräknings mål som vi inte stöder
    + LightGMB-uppskattning har lagts till i azureml-contrib-gbdt-paketet
  + [**azureml-core**](/python/api/azureml-core)
    + CLI stöder nu modell paketering.
    + Lägg till utfasnings varning för inaktuella API: er för indata. Se meddelande om API-ändring för data uppsättning i https://aka.ms/tabular-dataset .
    + Ändra [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) till namn och version för returnerad registrering om data uppsättningen är registrerad.
    + Åtgärda ett fel som ScriptRunConfig med data uppsättning som argument kan inte användas upprepade gånger för att skicka experiment körning.
    + Data uppsättningar som hämtas under en körning spåras och visas på sidan körnings information eller genom att anropa [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) när körningen har slutförts.
    + Tillåt att mellanliggande data i Azure Machine Learning pipeline konverteras till data uppsättning i tabell format och används i [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Stöd har lagts till för distribution och paketering av modeller som stöds (ONNX, scikit-och TensorFlow) utan en InferenceConfig-instans.
    + Överskriven flagga för tjänst distribution (ACI och AKS) har lagts till i SDK och CLI. Om det här alternativet anges skrivs den befintliga tjänsten över om det redan finns en tjänst med namnet. Om tjänsten inte finns, skapas en ny tjänst.
    +  Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. Modell registreringen tillåter indata från indata, exempel på utdata och resurs konfiguration för modellen.
    + Nya data lager har lagts till för Azure Database for MySQL. Exempel har lagts till för att använda Azure Database for MySQL i DataTransferStep i Azure Machine Learning pipelines.
    + Nya funktioner för att lägga till och ta bort taggar från experiment som lagts till för att ta bort taggar från körningar
    + Överskriven flagga för tjänst distribution (ACI och AKS) har lagts till i SDK och CLI. Om det här alternativet anges skrivs den befintliga tjänsten över om det redan finns en tjänst med namnet. Om tjänsten inte finns, skapas en ny tjänst.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Flyttad från `azureml-contrib-datadrift` i `azureml-datadrift`
    + Stöd har lagts till för övervakning av tids serie uppsättningar för drift och andra statistiska åtgärder
    + Nya metoder `create_from_model()` och `create_from_dataset()` till- [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) klassen. `create()`Metoden är föråldrad.
    + Justeringar av visualiseringar i python och UI i Azure Machine Learning Studio.
    + Stöd för schemaläggning per vecka och månatlig övervakning, förutom dagligen för data uppsättnings övervakare.
    + Stöd för bakfyllning av data övervaknings mått för att analysera historiska data för data uppsättnings övervakare.
    + Olika fel korrigeringar
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + azureml-nu behövs inte längre för att skicka en Azure Machine Learning pipeline-körning från pipelin `yaml` filen.
  + [**azureml-träna-automl**](/python/api/azureml-train-automl-runtime/)
    + Lägg till azureml – standardvärdet för automatiskt skapade Conda-kuvert för att lösa modell distributions problemet
    + AutoML-fjärrutbildningen innehåller nu azureml – standard för att tillåta åter användning av utbildnings miljö för en sådan härledning.
  + **azureml-train-core**
    + Stöd för PyTorch 1,3 har lagts till i [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) uppskattningen

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Visuellt gränssnitt (för hands version)

+ Azure Machine Learning Visual Interface (för hands version) har övergått för att köras på [Azure Machine Learning pipeliner](concept-ml-pipelines.md). Pipelines (tidigare experiment som experiment) som skapats i det visuella gränssnittet är nu helt integrerade med kärnan Azure Machine Learning-upplevelsen.
  + Enhetlig hanterings upplevelse med SDK-tillgångar
  + Versions hantering och spårning för Visual Interface-modeller, pipeliner och slut punkter
  + Användar gränssnittet har återskapats
  + Distribution av batch-härledning har lagts till
  + Stöd för Azure Kubernetes service (AKS) har lagts till för beräknings mål för härledning
  + Nytt arbets flöde för pipeline-steg för pipeline-redigering
  + Ny [landnings sida](https://ml.azure.com) för visuella redigerings verktyg

+ **Nya moduler**
  + Använd matematik åtgärd
  + Använd SQL-transformering
  + Klipp värden
  + Sammanfatta data
  + Importera från SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK för python v-1.0.69

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Att begränsa modell förklaringar till bästa körning snarare än att beräkna förklaringar för varje körning. Gör den här funktionen ändring för lokal, fjärr-och ADB.
    + Stöd har lagts till för modell förklaringar på begäran för användar gränssnittet
    + Lade till psutil som ett beroende av `automl` och inkluderade psutil som ett Conda-beroende i amlcompute.
    + Ett problem har åtgärd ATS med heuristisk lags och rullande fönster storlekar i prognos data uppsättningarna, en serie som kan orsaka linjära algebra-fel
      + Lade till utskrift för de heuristiskt bestämda parametrarna i prognos körningarna.
  + **azureml-contrib-datadrift**
    + Skydd har lagts till när du skapar utmatnings mått om data uppsättnings nivån inte är i det första avsnittet.
  + **azureml-contrib-interpret**
    + azureml-contrib-förklara-modell paketet har bytt namn till azureml-contrib-tolka
  + **azureml-core**
    + API har lagts till för att avregistrera data uppsättningar. `dataset.unregister_all_versions()`
    + azureml-contrib-förklara-modell paketet har bytt namn till azureml-contrib-tolka.
  + **[azureml-core](/python/api/azureml-core)**
    + API har lagts till för att avregistrera data uppsättningar. data uppsättning. [unregister_all_versions ()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Har lagt till data mängds-API för att kontrol lera ändrad data tid. `dataset.data_changed_time`.
    + Att kunna använda `FileDataset` och `TabularDataset` som indata till `PythonScriptStep` , `EstimatorStep` och `HyperDriveStep` i Azure Machine Learning pipeline
    + Prestanda hos `FileDataset.mount` har förbättrats för mappar med ett stort antal filer
    + Att kunna använda [FileDataset](/python/api/azureml-core/azureml.data.filedataset) och [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) som indata till [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)och [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) i Azure Machine Learning pipelinen.
    + Prestanda för FileDataset. [montering ()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) har förbättrats för mappar med ett stort antal filer
    + URL: en har lagts till för kända fel rekommendationer i körnings information.
    + En bugg har åtgärd ATS i run.get_metrics där förfrågningar skulle uppstå om en körning hade för många underordnade
    + En bugg har åtgärd ATS i [Run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) där förfrågningar skulle uppstå om en körning hade för många underordnade
    + Stöd har lagts till för autentisering i Arcadia-kluster.
    + När du skapar ett experiment objekt hämtas eller skapas experimentet i Azure Machine Learning-arbetsytan för körning av historik spårning. Experiment-ID: t och den arkiverade tiden fylls i experiment-objektet när det skapas. Exempel: experiment = experiment (arbets yta, "nytt experiment") experiment_id = experiment.id-arkiv () och reaktive ring () är funktioner som kan anropas för ett experiment för att dölja och återställa experimentet som ska visas i UX eller som returneras som standard i ett anrop till List experiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment, kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett angivet namn. Exempel: experiment1 = experiment (arbets yta, "aktivt experiment") experiment1. Archive () # skapa nytt aktivt experiment med samma namn som det arkiverade. experiment2. = Experiment (arbets yta, "aktivt experiment") experiment1. reaktive ring (new_name = "tidigare aktivt experiment") den statiska metod listan () i experimentet kan ta ett namn filter och ViewType-filter. ViewType-värden är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "alla" exempel: archived_experiments = experiment. list (arbets yta, view_type = "ARCHIVED_ONLY") all_first_experiments = experiment. list (arbets yta, namn = "första experiment", view_type = "alla")
    + Stöd för användning av miljö för modell distribution och tjänst uppdatering
  + **azureml-datadrift**
    + Attributet show i klassen DataDriftDector stöder inte det valfria argumentet with_details längre. Attributet show visar bara data drifts-och data drifts bidrag i funktions kolumnerna.
    + DataDriftDetector-get_output attributets beteende ändringar:
      + Indataparametern start_time, end_time är valfria i stället för obligatorisk;
      + Genom att skriva in särskilda start_time och/eller end_time med en speciell run_id i samma anrop leder det till ett värde fel undantag eftersom de är ömsesidigt uteslutande
      + Genom att skriva in en speciell start_time och/eller end_time returneras endast resultaten av schemalagda körningar.
      + Parametern daily_latest_only är föråldrad.
    + Stöd för hämtning av data uppsättningar-baserade datautdata.
  + **azureml-explain-model**
    + Byter namn på AzureML-förklarar-Model-paket till AzureML-tolka, och behåller det gamla paketet för bakåtkompatibilitet för tillfället
    + fast `automl` bugg med obehandlade förklaringar har angetts till klassificerings uppgift i stället för regression som standard vid hämtning från ExplanationClient
    + Lägg till stöd för `ScoringExplainer` som ska skapas direkt med `MimicWrapper`
  + **azureml-pipeline-core**
    + Bättre prestanda för att skapa stora pipeliner
  + **azureml-train-core**
    + Stöd för TensorFlow 2,0 har lagts till i TensorFlow-uppskattningen
  + **azureml-träna-automl**
    + När du skapar ett [experiment](/python/api/azureml-core/azureml.core.experiment.experiment) objekt hämtas eller skapas experimentet i Azure Machine Learning-arbetsytan för körning av historik spårning. Experiment-ID: t och den arkiverade tiden fylls i experiment-objektet när det skapas. Exempel:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [Arkiv ()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) och [återaktivera ()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) är funktioner som kan anropas på ett experiment för att dölja och återställa experimentet som visas i UX eller som returneras som standard i ett anrop till List experiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment, kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett angivet namn. Exempel:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Den statiska metod [listan ()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) i experimentet kan ta ett namn filter och ViewType-filter. ViewType-värden är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "alla". Exempel:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Stöd för användning av miljö för modell distribution och tjänst uppdatering.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Attributet show i klassen [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) stöder inte det valfria argumentet with_details längre. Attributet show visar bara data drifts-och data drifts bidrag i funktions kolumnerna.
    + Funktionen DataDriftDetector [get_output] python/API/azureml-datadrift/azureml. datadrift. DataDriftDetector. DataDriftDetector # get-output-Start-Time-none--End-Time-none--Run-ID-none-):
      + Indataparametern start_time, end_time är valfria i stället för obligatorisk;
      + Genom att skriva in särskilda start_time och/eller end_time med en speciell run_id i samma anrop leder det till ett värde fel undantag eftersom de är ömsesidigt uteslutande.
      + Genom att skriva in en speciell start_time och/eller end_time returneras endast resultaten av schemalagda körningar.
      + Parametern daily_latest_only är föråldrad.
    + Stöd för hämtning av data uppsättningar-baserade datautdata.
  + **azureml-explain-model**
    + Lägg till stöd för [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) som ska skapas direkt med MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Bättre prestanda för att skapa stora pipeliner.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Stöd för TensorFlow 2,0 har lagts till i [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) -uppskattningen.
  + **[azureml-träna-automl](/python/api/azureml-train-automl-runtime/)**
    + Den överordnade körningen kommer inte längre att Miss lyckas när installationen iterationen misslyckades eftersom det redan tar hand om dirigeringen.
    + Local-Docker och Local-Conda-stöd har lagts till för AutoML-experiment
    + Local-Docker och Local-Conda-stöd har lagts till för AutoML experiment.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webb miljö (för hands version) för Azure Machine Learning arbets ytor

Fliken experiment i den [nya arbets ytans Portal](https://ml.azure.com) har uppdaterats så att data experter kan övervaka experiment på ett mer presterande sätt. Du kan utforska följande funktioner:
+ Experimentera metadata för att enkelt filtrera och sortera din lista över experiment
+ Förenklade och utförda sidor med experiment information som gör att du kan visualisera och jämföra dina körningar
+ Ny design för att köra informations sidor för att förstå och övervaka dina utbildnings körningar

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK för python v-1.0.65

  + **Nya funktioner**
    + Tillagda granskade miljöer. Dessa miljöer har förkonfigurerats med bibliotek för vanliga Machine Learning-uppgifter och har för beretts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard i arbets ytans lista över miljöer med prefixet "AzureML".
    + Tillagda granskade miljöer. Dessa miljöer har förkonfigurerats med bibliotek för vanliga Machine Learning-uppgifter och har för beretts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard i [arbets ytans](/python/api/azureml-core/azureml.core.workspace%28class%29)lista över miljöer med prefixet "azureml".

  + **azureml-träna-automl**
  + **[azureml-träna-automl](/python/api/azureml-train-automl-runtime/)**
    + Stöd har lagts till för konvertering av ONNX för ADB och HDI

+ **Förhandsgranskningsfunktioner**
  + **azureml-träna-automl**
  + **[azureml-träna-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT och BiLSTM som stöds som text upplärda (endast för hands version)
    + Funktionalisering-anpassning som stöds för kolumn syfte och transformator parametrar (endast för hands version)
    + Obehandlade förklaringar som stöds när användaren aktiverar modell förklaring under utbildningen (endast för hands version)
    + Prophet har lagts till för `timeseries` Prognosticering som en fordonsbaserad pipeline (endast för hands version)

  + **azureml-contrib-datadrift**
    + Paket som har flyttats från azureml-contrib-datadrift till azureml-datadrift; `contrib` paketet kommer att tas bort i en framtida version

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Introducerade FeaturizationConfig till AutoMLConfig och AutoMLBaseSettings
    + Introducerade FeaturizationConfig till [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) och AutoMLBaseSettings
      + Åsidosätt kolumn syfte för funktionalisering med den aktuella kolumnen och funktions typen
      + Åsidosätt transformator parametrar
    + Ett utfasnings meddelande har lagts till för explain_model () och retrieve_model_explanations ()
    + Lade till Prophet som en fordonsbaserad pipeline (endast för hands version)
    + Ett utfasnings meddelande har lagts till för explain_model () och retrieve_model_explanations ().
    + Lade till Prophet som en fordonsbaserad pipeline (endast för hands version).
    + Stöd har lagts till för automatisk identifiering av mål lags, rullande fönster storlek och maximal horisont. Om någon av target_lags, target_rolling_window_size eller max_horizon har värdet Auto, används heuristiken för att beräkna värdet för motsvarande parameter baserat på tränings data.
    + Fast Prognosticering i fallet när data uppsättningen innehåller en kornig kolumn är denna kornig het av en numerisk typ och det finns ett mellanrum mellan tåg och test uppsättning
    + Korrigerade fel meddelandet om det duplicerade indexet i aktiviteter för fjärrkörning i prognos
    + Fast Prognosticering i fallet när data uppsättningen innehåller en korn-kolumn är denna kornig het av en numerisk typ och det finns ett mellanrum mellan tåg-och test uppsättningen.
    + Korrigerade fel meddelandet om det duplicerade indexet i prognos aktiviteter för fjärrkörning.
    + En guardrail har lagts till för att kontrol lera om en data uppsättning är obalanserad eller inte. Om så är fallet skrivs ett guardrail-meddelande till-konsolen.
  + **azureml-core**
    + Möjlighet att hämta SAS-URL till modellen i lagring via Model-objektet har lagts till. Ex: model.get_sas_url ()
    + Introducera `run.get_details()['datasets']` för att hämta data uppsättningar som är associerade med den skickade körningen
    + Lägg till API `Dataset.Tabular.from_json_lines_files` för att skapa en TabularDataset från JSON-filer. Information om dessa tabell data i JSON-filer på TabularDataset finns i [den här artikeln](how-to-create-register-datasets.md) för dokumentation.
    + Ytterligare fält för VM-storlek (OS-disk, antal GPU: er) har lagts till i supported_vmsizes ()-funktionen
    + Ytterligare fält har lagts till i funktionen list_nodes () för att Visa körningen, den privata och den offentliga IP-adressen, porten osv.
    + Möjlighet att ange ett nytt fält under kluster etableringen – remotelogin_port_public_access som kan ställas in på aktive rad eller inaktive ras beroende på om du vill lämna SSH-porten öppen eller stängd vid tidpunkten för att skapa klustret. Om du inte anger det öppnas tjänsten Smartt, eller så stängs porten beroende på om du distribuerar klustret i ett virtuellt nätverk.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Möjlighet att hämta SAS-URL till modellen i lagring via Model-objektet har lagts till. Ex: modell. [get_sas_url ()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introducera körning. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' dataset '] för att hämta data uppsättningar som är associerade med den skickade körningen
    + Lägg till API `Dataset.Tabular` .[ from_json_lines_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) för att skapa en TabularDataset från JSON-filer. Information om dessa tabell data i JSON-filer på TabularDataset finns i https://aka.ms/azureml-data dokumentationen.
    + Ytterligare fält för VM-storlek (OS-disk, antal GPU: er) har lagts till i [supported_vmsizes ()-](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) funktionen
    + Ytterligare fält har lagts till i funktionen [list_nodes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) för att Visa körning, privat och offentlig IP-adress, Port osv.
    + Möjlighet att ange ett nytt fält under kluster [etablering](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  som kan ställas in på aktive rad eller inaktive ras beroende på om du vill lämna SSH-porten öppen eller stängd vid tidpunkten för att skapa klustret. Om du inte anger det öppnas tjänsten Smartt, eller så stängs porten beroende på om du distribuerar klustret i ett virtuellt nätverk.
  + **azureml-explain-model**
    + Förbättrad dokumentation för förklarings resultat i klassificerings scenariot.
    + Har lagt till möjligheten att överföra de förutsagda y-värdena i förklaringen till utvärderings exemplen. Låser upp användbara visualiseringar.
    + Har lagt till förklarings egenskapen till MimicWrapper för att aktivera hämtning av underliggande MimicExplainer.
  + **azureml-pipeline-core**
    + En antecknings bok har lagts till för att beskriva modulen, ModuleVersion och ModuleStep
  + **azureml-pipeline-steps**
    + RScriptStep har lagts till för R-skript som körs via AML-pipeline.
    + Parametrar för fast metadata parsas i AzureBatchStep som orsakade fel meddelandet "tilldelningen av parametern SubscriptionId har inte angetts".
  + **azureml-träna-automl**
    + Stöd för training_data, validation_data, label_column_name weight_column_name som indata-utdataformat
    + Ett utfasnings meddelande har lagts till för explain_model () och retrieve_model_explanations ()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + En [antecknings bok](https://aka.ms/pl-modulestep) har lagts till för att beskriva [modulen](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion och [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) har lagts till för R-skript som körs via AML-pipeline.
    + Parametrar för fast metadata parsas i [AzureBatchStep som orsakade fel meddelandet "tilldelningen av parametern SubscriptionId har inte angetts".
  + **[azureml-träna-automl](/python/api/azureml-train-automl-runtime/)**
    + Training_data validation_data label_column_name weight_column_name som indata.
    + Ett utfasnings meddelande har lagts till för [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) och [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK för python v-1.0.62

+ **Nya funktioner**
  + Introducerade `timeseries`  traiten på TabularDataset. Denna egenskap möjliggör enkel tidsstämpel-filtrering på data a TabularDataset, till exempel för att ta alla data mellan ett tidsintervall eller de senaste data.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb för ett exempel på en bärbar dator.
  + Träning med TabularDataset och FileDataset har Aktiver ATS. 

  + **azureml-train-core**
      + Tillagt `Nccl` och `Gloo` stöd i PyTorch-uppskattning

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Föråldrade AutoML-inställningen lag_length och LaggingTransformer.
    + Korrigerad korrekt validering av indata om de anges i ett data flödes format
    + Ändrade fit_pipeline. py för att generera graf-JSON och ladda upp till artefakter.
    + Återger grafen under `userrun` Använd `Cytoscape` .
  + **azureml-core**
    + Har återhämtat undantags hanteringen i ADB-koden och gör ändringar till enligt den nya fel hanteringen
    + Automatisk MSI-autentisering för virtuella dator datorer lades till.
    + Korrigerar fel där skadade eller tomma modeller kan laddas upp på grund av misslyckade återförsök.
    + Korrigerade felet där `DataReference` namn ändras när `DataReference` läget ändras (till exempel vid anrop `as_upload` , `as_download` eller `as_mount` ).
    + Gör `mount_point` och `target_path` valfritt för `FileDataset.mount` och `FileDataset.download` .
    + Det går inte att hitta tidsstämpel-kolumnen som ska utföras om Time-seriens relaterade API anropas utan en begränsad tidsstämpel-kolumn som är tilldelad eller kolumner som tilldelats tids stämpling utelämnas.
    + Tids serie kolumner ska tilldelas med en kolumn vars typ är datum, annars förväntas ett undantags fel
    + Tids serie kolumner för att tilldela API "with_timestamp_columns" kan ta inget värde med ett kolumn namn för tidsstämpel, vilket raderar tidigare tilldelade Timestamp-kolumner.
    + Undantag utlöstes när en grov kornig het eller en detaljerad tids stämplings kolumn ignoreras med indikation för att användaren som släpper kan utföras efter att ha tagit bort kolumnen tidsstämpel i släppa listan eller anropa with_time_stamp med inget värde för att frisläppa Timestamp-kolumner
    + Undantag utlöstes när en grov kornig het eller en detaljerad tids stämplings kolumn inte ingår i listan Behåll kolumner med indikation för den användare som håller på att göras efter att ha angett kolumnen tidsstämpel i kolumnen Behåll kolumn eller anropa with_time_stamp med inget värde för att frisläppa Timestamp-kolumner.
    + Loggning har lagts till för en registrerad modell storlek.
  + **azureml-explain-model**
    + Åtgärdad varning har skrivits ut till konsolen när python-paketet "packning" inte är installerat: "Använd äldre versioner än lightgbm som stöds, uppgradera till version som är större än 2.2.1"
    + Förklaring av den fasta nedladdnings modellen med horisontell partitionering för globala förklaringar med många funktioner
    + Välklarande härma-förklaring saknas initierings exempel på utdata förklaring
    + Fast oåterkalleligt fel vid uppsättnings egenskaper vid överföring med förklarings klient med två olika typer av modeller
    + En get_raw param lades till i förklaringen. förklara () så att en bedömnings förklaring kan returnera både de tillverkade och råa värdena.
  + **azureml-träna-automl**
    + Introducerade offentliga API: er från AutoML för stödjande förklaringar från `automl` förklara SDK – nyare sätt att stödja AutoML-förklaringar genom att koppla från AutoML funktionalisering och förklara SDK-integrerad stöd för RAW-förklaring från azureml förklara SDK för AutoML-modeller.
    + Tar bort azureml-standardvärden från miljöer med Fjärrutbildning.
    + Ändrade standard platsen för cachelagring från FileCacheStore baserat på en-AzureFileCacheStore för AutoML på Azure Databricks kod Sök väg.
    + Korrigerad korrekt validering av indata om de anges i ett data flödes format
  + **azureml-train-core**
    + Återställningen source_directory_data_store utfasningen.
    + Möjligheten att åsidosätta azureml installerade paket versioner har lagts till.
    + Stöd för Dockerfile har lagts till i `environment_definition` parametern i uppskattningar.
    + Förenklade utbildnings parametrar i uppskattningar.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Ny webb miljö (för hands version) för Azure Machine Learning arbets ytor
Med den nya webb upplevelsen kan data experter och data tekniker slutföra sin slut punkt till slut punkt för Machine Learning-livscykel från förberedelser och visualisera data för att träna och distribuera modeller på en enda plats.

![Azure Machine Learning arbets ytans användar gränssnitt (för hands version)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Viktiga funktioner:**

Med det här nya Azure Machine Learning-gränssnittet kan du nu:
+ Hantera dina antecknings böcker eller länka ut till Jupyter
+ [Köra automatiserade ML-experiment](tutorial-first-experiment-automated-ml.md)
+ [Skapa data uppsättningar från lokala filer, data lager &-webbfiler](how-to-create-register-datasets.md)
+ Utforska & förbereda data uppsättningar för skapande av modell
+ Övervaka data driften för dina modeller
+ Visa de senaste resurserna från en instrument panel

I samband med den här versionen stöds följande webbläsare: Chrome, Firefox, Safari och Microsoft Edge Preview.

**Kända problem:**

1. Uppdatera webbläsaren om du ser "något gick fel! Det gick inte att läsa in Chunk-filer när distribution pågår.

1. Kan inte ta bort eller byta namn på filen i antecknings böcker och filer. Under den offentliga för hands versionen kan du använda Jupyter UI eller terminalen i Notebook VM för att utföra uppdaterings fil åtgärder. Eftersom det är ett monterat nätverks fil system alla ändringar, återspeglas de virtuella datorerna i Notebook direkt i arbets ytan Notebook.

1. För SSH till den virtuella Notebook-datorn:
   1. Hitta de SSH-nycklar som skapades under installationen av den virtuella datorn. Eller Sök efter nycklarna i Azure Machine Learning arbets ytan > öppna fliken Compute > leta upp den virtuella Notebook-datorn i listan > öppna dess egenskaper: kopiera nycklarna från dialog rutan.
   1. Importera dessa offentliga och privata SSH-nycklar till den lokala datorn.
   1. Använd dem för SSH i den virtuella Notebook-datorn.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK för python v-1.0.60

+ **Nya funktioner**
  + Introducerade FileDataset, som refererar till en eller flera filer i dina data lager eller offentliga URL: er. Filerna kan vara i valfritt format. FileDataset ger dig möjlighet att ladda ned eller montera filerna i din beräkning. 
  + Stöd för pipeline-yaml har lagts till för PythonScript steg, Adla steg, Databricks steg, DataTransferStep och AzureBatch steg

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + AutoArima är nu en förslags bara pipeline för för hands version.
    + Förbättrad fel rapportering för Prognosticering.
    + Förbättrad loggning genom att använda anpassade undantag i stället för allmän i prognos aktiviteterna.
    + Den här kontrollen har tagits bort max_concurrent_iterations för att vara mindre än det totala antalet iterationer.
    + AutoML-modeller returnerar nu AutoMLExceptions
    + Den här versionen förbättrar körnings prestandan för automatiserade lokala Machine Learning-körningar.
  + **azureml-core**
    + Introducera Dataset.get_all (arbets yta), som returnerar en ord lista `TabularDataset` och `FileDataset` objekt som har registrerats med registrerings namnet.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Presentera `parition_format` som argument till `Dataset.Tabular.from_delimited_files` och `Dataset.Tabular.from_parquet.files` . Partitionsinformation för varje data Sök väg extraheras till kolumner baserat på det angivna formatet. {column_name} skapar en sträng kolumn och {column_name: ÅÅÅÅ/MM/dd/HH/mm/SS} skapar kolumnen DateTime, där ÅÅÅÅ, MM, DD, HH, mm och SS används för att extrahera år, månad, dag, timme, minut och sekund för datum/tid-typen. Partition_format bör starta från positionen för den första partitionen tills fil Sök vägen är slut. Till exempel, med sökvägen ".. /USA/2019/01/01/data.csv "där partitionen är per land och tid, partition_format = '/{Country}/{PartitionDate: ÅÅÅÅ/MM/DD}/data.csv ' skapar sträng kolumnen land med värdet" USA "och datetime-kolumnen" PartitionDate "med värdet" 2019-01-01 ".
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Presentera `partition_format` som argument till `Dataset.Tabular.from_delimited_files` och `Dataset.Tabular.from_parquet.files` . Partitionsinformation för varje data Sök väg extraheras till kolumner baserat på det angivna formatet. {column_name} skapar en sträng kolumn och {column_name: ÅÅÅÅ/MM/dd/HH/mm/SS} skapar kolumnen DateTime, där ÅÅÅÅ, MM, DD, HH, mm och SS används för att extrahera år, månad, dag, timme, minut och sekund för datum/tid-typen. Partition_format bör starta från positionen för den första partitionen tills fil Sök vägen är slut. Till exempel, med sökvägen ".. /USA/2019/01/01/data.csv "där partitionen är per land och tid, partition_format = '/{Country}/{PartitionDate: ÅÅÅÅ/MM/DD}/data.csv ' skapar sträng kolumnen land med värdet" USA "och datetime-kolumnen" PartitionDate "med värdet" 2019-01-01 ".
    + `to_csv_files` och- `to_parquet_files` metoder har lagts till i `TabularDataset` . Dessa metoder möjliggör konvertering mellan a `TabularDataset` och a `FileDataset` genom att konvertera data till filer med det angivna formatet.
    + Logga automatiskt in i bas avbildnings registret när du sparar en Dockerfile som genereras av Model. Package ().
    + gpu_support är inte längre nödvändigt. AML identifierar och använder nu NVIDIA Docker-tillägget när det är tillgängligt. Den kommer att tas bort i en framtida version.
    + Stöd har lagts till för att skapa, uppdatera och använda PipelineDrafts.
    + Den här versionen förbättrar körnings prestandan för automatiserade lokala Machine Learning-körningar.
    + Användare kan fråga mått från körnings historik efter namn.
    + Förbättrad loggning genom att använda anpassade undantag i stället för allmän i prognos aktiviteterna.
  + **azureml-explain-model**
    + Feature_maps parameter har lagts till i den nya MimicWrapper, vilket gör det möjligt för användare att hämta rå funktions förklaringar.
    + Uppladdningar av data uppsättningar är nu inaktiverade som standard för förklarings överföring och kan aktive ras igen med upload_datasets = True
    + Har lagt till "is_law" filtrerings parametrar i förklarings listan och nedladdnings funktioner.
    + Lägger till metoden `get_raw_explanation(feature_maps)` i både globala och lokala förklarings objekt.
    + En versions kontroll har lagts till i lightgbm med den utskrivna varningen om lägre version
    + Optimerad minnes användning vid batch-förklaring
    + AutoML-modeller returnerar nu AutoMLExceptions
  + **azureml-pipeline-core**
    + Stöd har lagts till för att skapa, uppdatera och använda PipelineDrafts – kan användas för att underhålla föränderligt-pipeline-definitioner och använda dem interaktivt för att köra
  + **azureml-träna-automl**
    + Funktionen har skapats för att installera vissa versioner av GPU-kompatibel pytorch v 1.1.0, :::no-loc text="cuda"::: toolkit 9,0, pytorch-transformatorer, vilket krävs för att aktivera Bert/XLNet i fjärrkörnings miljön för python.
  + **azureml-train-core**
    + Tidigt fel i vissa fel definitions fel i det här området direkt i SDK i stället för på Server sidan.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning data prep SDK v 1.1.14
+ **Fel korrigeringar och förbättringar**
  + Aktiverade skrivning till ADLS/ADLSGen2 med hjälp av rå sökväg och autentiseringsuppgifter.
  + En bugg har åtgärd ATS som `include_path=True` inte fungerade för `read_parquet` .
  + Ett fast `to_pandas_dataframe()` fel orsakades av undantaget "ogiltigt egenskaps värde: hostSecret".
  + Ett fel har åtgärd ATS där filer inte kunde läsas på DBFS i Spark-läge.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK för python v-1.0.57
+ **Nya funktioner**
  + Aktive rad `TabularDataset` för användning av AutomatedML. Mer information `TabularDataset` finns på https://aka.ms/azureml/howto/createdatasets .

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Nu kan du uppdatera TLS/SSL-certifikatet för poängsättnings slut punkten som distribueras i AKS-kluster både för Microsoft-genererade och kund certifikat.
  + **azureml-automl-core**
    + Åtgärdat problem i AutoML var rader med saknade etiketter togs inte bort korrekt.
    + Förbättrad fel loggning i AutoML; fullständiga fel meddelanden kommer nu alltid att skrivas till logg filen.
    + AutoML har uppdaterat paket fästen för att inkludera `azureml-defaults` , `azureml-explain-model` och `azureml-dataprep` . AutoML kommer inte längre att varna vid paket matchnings fel (förutom `azureml-train-automl` paket).
    + Ett problem har åtgärd ATS i `timeseries`  där ka-delningar är av samma storlek, vilket gör att lager plats beräkningen inte kan köras.
    + När du kör Ensemble-iteration för inlärnings typen kors validering, om vi har problem med att ladda ned de modeller som har tränats på hela data uppsättningen, hade vi en inkonsekvens mellan modellens vikt och de modeller som har matats in i röstnings ensemblen.
    + Korrigerade felet, utlöses när inlärnings-och/eller verifierings etiketter (y och y_valid) har angetts i formatet Pandas dataframe, men inte som numpy-matris.
    + Har åtgärdat problemet med prognos uppgifter när inget har påträffats i de booleska kolumnerna i ingångs tabeller.
    + Tillåt AutoML-användare att släppa inlärnings serier som inte är tillräckligt långa vid prognoser. – Tillåt AutoML-användare att släppa kärnor från den test uppsättning som inte finns i träningen när du skapar prognoser.
  + **azureml-core**
    + Åtgärdat problem med blob_cache_timeout parameter ordning.
    + Externa anpassade och transformera undantags typer har lagts till i systemfel.
    + Stöd har lagts till för Key Vault hemligheter för fjärrkörningar. Lägg till en azureml. Core. nyckel valvs klass för att lägga till, hämta och lista hemligheter från det nyckel valv som är associerat med din arbets yta. Åtgärder som stöds är:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.keyvault.Keyvault.set_secret (namn, värde)
      + azureml.core.keyvault.Keyvault.set_secrets (secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret (namn)
      + azureml.core.keyvault.Keyvault.get_secrets (secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets ()
    + Ytterligare metoder för att hämta standard nyckel valv och få hemligheter under fjärrkörning:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.run.Run.get_secret (namn)
      + azureml.core.run.Run.get_secrets (secrets_list)
    + Ytterligare parametrar för åsidosättning har lagts till för Submit-HyperDrive CLI-kommandot.
    + Bättre tillförlitlighet för API-anrop för att expandera nya begär Anden biblioteks undantag.
    + Lägg till stöd för att skicka körningar från en skickad körning.
    + Ett problem med en SAS-token med fast förfallo tid i FileWatcher, som gjorde att filer slutade laddas upp efter att deras ursprungliga token hade upphört
    + Import av HTTP CSV/TSV-filer som stöds i data uppsättning python SDK.
    + Föråldrad-metoden arbetsyte. Setup (). Varnings meddelande som visas för användare som föreslår användning av Create () eller Get ()/from_config () i stället.
    + Lade till Environment.add_private_pip_wheel (), som möjliggör överföring av privata anpassade python-paket `whl` till arbets ytan och att använda dem på ett säkert sätt för att bygga/materialisera miljön.
    + Nu kan du uppdatera TLS/SSL-certifikatet för poängsättnings slut punkten som distribueras i AKS-kluster både för Microsoft-genererade och kund certifikat.
  + **azureml-explain-model**
    + Parameter har lagts till för att lägga till ett modell-ID till förklaringar vid uppladdning.
    + Taggar har lagts `is_raw` till i förklaringar i minnet och uppladdning.
    + Stöd för pytorch och tester har lagts till för azureml-förklarar-modell-paketet.
  + **azureml-opendatasets**
    + Stöd för att identifiera och logga automatiska test miljöer.
    + Klasser har lagts till för att hämta befolkning efter region och post.
  + **azureml-pipeline-core**
    + Etikett egenskapen har lagts till i definitionerna för indata och utdata.
  + **azureml-telemetry**
    + En felaktig konfiguration av telemetri har åtgärd ATS.
  + **azureml-träna-automl**
    + Ett fel uppstod vid fel vid installationen av fel, det gick inte att logga in "fel"-fältet för installations körningen och därför sparades inte i den överordnade körningen "fel".
    + Åtgärdat problem i AutoML var rader med saknade etiketter togs inte bort korrekt.
    + Tillåt AutoML-användare att släppa inlärnings serier som inte är tillräckligt långa vid prognoser.
    + Tillåt AutoML-användare att släppa kärnor från test uppsättningen som inte finns i inlärnings uppsättningen vid prognostisering.
    + Nu AutoMLStep passerar genom `automl` konfigurationen till Server delen för att undvika eventuella problem vid ändringar eller tillägg av nya konfigurations parametrar.
    + AutoML data Guardrail finns nu som offentlig för hands version. Användaren ser en data Guardrail-rapport (för klassificerings-och Regressions aktiviteter) efter utbildning och kan också komma åt den via SDK-API.
  + **azureml-train-core**
    + Stöd för Torch 1,2 har lagts till i PyTorch-uppskattningen.
  + **azureml-widgets**
    + Förbättrade diagram för förvirrings mat ris för klassificerings träning.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning data prep SDK v 1.1.12
+ **Nya funktioner**
  + Listor över strängar kan nu skickas in som inmatade `read_*` metoder.

+ **Fel korrigeringar och förbättringar**
  + Prestanda för `read_parquet` har förbättrats vid körning i Spark.
  + Ett problem har åtgärd ATS där `column_type_builder` det inte gick att skapa en enskild kolumn med tvetydiga datum format.

### <a name="azure-portal"></a>Azure Portal
+ **Förhands gransknings funktion**
  + Logg-och utdatafilen är nu tillgängliga för att köra informations sidor. Filerna strömmar uppdateringar i real tid när växling vid förhands visning är aktive rad.
  + Möjligheten att ange kvot på en arbets ytans nivå släpps i för hands versionen. AmlCompute kvoter tilldelas på prenumerations nivå, men nu kan du distribuera den kvoten mellan arbets ytor och tilldela den till en rättvis delning och styrning. Klicka bara på bladet **användning och kvoter** i det vänstra navigerings fältet på arbets ytan och välj fliken **Konfigurera kvoter** . Du måste vara prenumerations administratör för att kunna ange kvoter på arbets ytans nivå eftersom detta är en åtgärd över arbets ytan.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK för python v-1.0.55

+ **Nya funktioner**
  + Token-baserad autentisering stöds nu för anrop som görs till poäng slut punkten som distribueras på AKS. Vi kommer att fortsätta att stödja den aktuella nyckelbaserade autentiseringen och användarna kan använda någon av dessa autentiseringsmekanismer i taget.
  + Möjlighet att registrera en blob-lagring som ligger bakom det virtuella nätverket (VNet) som ett data lager.

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdar ett fel där verifierings storleken för ka-delningar är liten och resulterar i dåliga förutsägande eller sanna diagram för regression och Prognosticering.
    + Loggningen av prognos uppgifter på fjärrdatorn körs bättre, och nu tillhandahålls användaren med ett omfattande fel meddelande om körningen misslyckades.
    + Fasta problem av `Timeseries` om preprocess-flaggan är true.
    + Det gick inte att utföra några validerings fel meddelanden i prognos data mer åtgärds bara.
    + Minskad minnes förbrukning av AutoML körs genom att släppa och/eller Lazy inläsning av data uppsättningar, särskilt i mellan processens upphämtningar
  + **azureml-contrib-förklara-modell**
    + Lade till model_task flagga till förklaringar som gör att användaren kan åsidosätta standard logik för automatisk härledning för modell typ
    + Ändringar i widgeten: installeras automatiskt med `contrib` , ingen mer `nbextension` installations-/aktiverings support förklaring med global funktions betydelse (till exempel Permutative)
    + Instrument panels ändringar:-Box ritas och violin ritas, förutom att `beeswarm` Rita på sammanfattnings sidan – mycket snabbare åter givning av `beeswarm` observationer på skjutreglaget "Top-k"-ändring – användbart meddelande som förklarar hur översta-k är beräknade – användbara anpassningsbara meddelanden i stället för diagram när data inte anges
  + **azureml-core**
    + Metoden Model. Package () har lagts till för att skapa Docker-avbildningar och Dockerfiles som kapslar in modeller och deras beroenden.
    + Uppdaterade lokala webbtjänster för att acceptera InferenceConfigs som innehåller miljö objekt.
    + Fast Model. register () genererar ogiltiga modeller när "." (för den aktuella katalogen) skickas som model_path-parametern.
    + Lägg till Run.submit_child, funktionen speglar experimentet. submit när du anger Kör som överordnad för den skickade underordnade körningen.
    + Stöd för konfigurations alternativ från modell. registrera i Run.register_model.
    + Möjlighet att köra JAR-jobb på ett befintligt kluster.
    + Stöder nu instance_pool_id-och cluster_log_dbfs_path parametrar.
    + Stöd har lagts till för användning av ett miljö objekt när en modell distribueras till en WebService. Environment-objektet kan nu anges som en del av InferenceConfig-objektet.
    + Lägg till appinsifht-mappning för nya regioner – västra-väst-usanorracentrala
    + Lade till dokumentation för alla attribut i alla data lager klasser.
    + Blob_cache_timeout parameter har lagts till `Datastore.register_azure_blob_container` .
    + Save_to_directory-och load_from_directory-metoder har lagts till i azureml. Core. Environment. Environment.
    + Har lagt till kommandona "AZ ml miljö hämtning" och "AZ ml-miljö registrera" i CLI.
    + Environment.add_private_pip_wheel metod har lagts till.
  + **azureml-explain-model**
    + Data uppsättnings spårning har lagts till i förklaringar med data uppsättnings tjänsten (för hands version).
    + Minskad standard grupp storlek vid strömning av globala förklaringar från 10 000 till 100.
    + Om du lägger till model_task flagga i förklaringar för att låta användaren åsidosätta standard logiken för automatisk härledning för modell typ.
  + **azureml-mlflow**
    + Fast bugg i mlflow.azureml.build_image där kapslade kataloger ignoreras.
  + **azureml-pipeline-steps**
    + Möjlighet att köra JAR-jobb på befintliga Azure Databricks-kluster har lagts till.
    + Stöd instance_pool_id och cluster_log_dbfs_path parametrar har lagts till för DatabricksStep-steget.
    + Stöd har lagts till för pipeline-parametrar i DatabricksStep-steget.
  + **azureml-träna-automl**
    + Har lagts till `docstrings` för de Ensemble-relaterade filerna.
    + Uppdaterade dokument till ett mer lämpligt språk för `max_cores_per_iteration` och `max_concurrent_iterations`
    + Loggningen av prognos uppgifter på fjärrdatorn körs bättre, och nu tillhandahålls användaren med ett omfattande fel meddelande om körningen misslyckades.
    + Tog bort get_data från pipelinen `automlstep` Notebook.
    + Stöd har startats `dataprep` i `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning data prep SDK v 1.1.10

+ **Nya funktioner**
  + Du kan nu begära att köra vissa kontroller (till exempel histogram, punkt diagram osv.) i vissa kolumner.
  + Ett parallellisera-argument har lagts till i `append_columns` . Om värdet är true kommer data att läsas in i minnet, men körningen körs parallellt. om det här värdet är False kommer körningen att strömmas, men en enkel tråd.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK för python v-1.0.53

+ **Nya funktioner**
  + Automatiserad Machine Learning stöder nu utbildning av ONNX-modeller på fjärrdatorns mål
  + Azure Machine Learning kan nu återuppta utbildningen från en tidigare körnings-, kontroll punkts-eller modell fil.
    + Lär dig hur du [använder uppskattningar för att återuppta inlärningen från en tidigare körning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + CLI-kommandon "modell distribution" och "tjänst uppdatering" accepterar nu parametrar, konfigurationsfiler eller en kombination av de två. Parametrar har företräde framför attribut i filer.
    + Modell beskrivningen kan nu uppdateras efter registreringen
  + **azureml-automl-core**
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (aktuell senaste).
    + Lägga till stöd för NimbusML-uppskattningar & pipelines som ska användas inom AutoML-uppskattningar.
    + Åtgärda ett fel i urvals proceduren för ensemblen som inte nödvändigt vis växer till den resulterande ensemblen även om poängen var konstant.
    + Aktivera åter användning av vissa featurizations över ka-delningar för prognos uppgifter. Detta påskyndar körningen av installations programmet på ungefär samma sätt som n_cross_validations för dyra featurizations som lags och rullande fönster.
    + Åtgärda ett problem om tiden ligger utanför Pandas-tidsintervallet som stöds. Vi höjer nu en DataException om tiden är mindre än PD. Timestamp. min eller större än PD. Timestamp. Max
    + Med prognostisering kan du nu använda olika frekvenser i träna och test uppsättningar om de kan justeras. Till exempel kan "kvartals vis som börjar i januari" och "kvartals vis start i oktober" justeras.
    + Egenskapen "parametrar" har lagts till i TimeSeriesTransformer.
    + Ta bort gamla undantags klasser.
    + I prognos uppgifter `target_lags` accepterar-parametern nu ett heltals värde eller en lista med heltal. Om heltalet angavs skapas bara en fördröjning. Om en lista anges kommer de unika värdena för lags att tas. target_lags = [1, 2, 2, 4] kommer att skapa lags av en, två och fyra perioder.
    + Åtgärda felet om att förlora kolumn typer efter transformeringen (fel länkad);
    + I `model.forecast(X, y_query)` , kan y_query vara en objekt typ som inte innehåller några (n) i början (#459519).
    + Lägg till förväntade värden i `automl` utdata
  + **azureml-contrib-datadrift**
    +  Förbättringar av exempel på bärbara datorer, inklusive växla till azureml-OpenData uppsättningar i stället för azureml-contrib-OpenData uppsättningar och prestanda förbättringar när data berikas
  + **azureml-contrib-förklara-modell**
    + Argument för fasta omvandlingar för LIME-förklaringar för rå funktions prioritet i azureml-contrib-Restore-Model-paket
    + Du har lagt till segment till bild förklaringar i bild förklaring för paketet AzureML-contrib-re-Model
    + Lägg till scipy-sparse-stöd för LimeExplainer
    + Har lagts `batch_size` till i härmar `include_local=False` -förklaringen när, för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Korrigering för att anropa set_featurizer_timeseries_params (): förutsägelse värde typ ändring och null-kontroll – Lägg till antecknings bok för `timeseries`  upplärda
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (aktuell senaste).
  + **azureml-core**
    + Har lagt till möjligheten att bifoga DBFS-datalager i AzureML CLI
    + Korrigerade felet med data lager uppladdning där en tom mapp skapas om den `target_path` startas med `/`
    + Åtgärdat `deepcopy` problem i ServicePrincipalAuthentication.
    + Har lagt till kommandona "AZ ml Environment show" och "AZ ml Environment List" i CLI.
    + Miljöer har nu stöd för att ange en base_dockerfile som ett alternativ till redan skapat base_image.
    + Den oanvända RunConfiguration-inställningen auto_prepare_environment har marker ATS som föråldrad.
    + Modell beskrivningen kan nu uppdateras efter registreringen
    + Bugfix: modell och avbildning ta bort nu innehåller mer information om hur du hämtar överordnade objekt som är beroende av dem om borttagningen Miss lyckas på grund av ett överordnat beroende.
    + Fast bugg som skrev tom varaktighet för distributioner som inträffar när du skapar en arbets yta för vissa miljöer.
    + Förbättrade fel undantag för att skapa arbets ytor. Så att användarna inte ser "det går inte att skapa arbets ytan. Det gick inte att hitta... " som meddelandet och se i stället det faktiska fel meddelandet.
    + Lägg till stöd för token-autentisering i AKS-webbtjänster.
    + Lägg till `get_token()` metod till `Webservice` objekt.
    + CLI-stöd har lagts till för hantering av Machine Learning-datauppsättningar.
    + `Datastore.register_azure_blob_container` Om du vill kan du `blob_cache_timeout` använda ett värde (i sekunder) som konfigurerar blobfuses monterings parametrar för att aktivera förfallo tid för cacheminnet för det här data lagret. Standardvärdet är ingen tids gräns, till exempel när en BLOB läses, kommer den att stanna kvar i det lokala cacheminnet tills jobbet är klart. De flesta jobb föredrar den här inställningen, men vissa jobb behöver läsa mer data från en stor data uppsättning än vad som får plats på deras noder. För dessa jobb kan du justera den här parametern för att lyckas. Var försiktig när du justerar den här parametern: om du ställer in värdet för lågt kan det leda till dåliga prestanda, eftersom data som används i en epok går ut innan de används igen. Alla läsningar görs från Blob Storage/Network i stället för det lokala cacheminnet, vilket negativt påverkar inlärnings tiderna.
    + Modell beskrivningen kan nu uppdateras korrekt efter registreringen
    + Modell-och bild borttagning innehåller nu mer information om överordnade objekt som är beroende av dem, vilket gör att borttagningen Miss fungerar
    + Förbättra resursutnyttjande för fjärrkörningar med hjälp av azureml. mlflow.
  + **azureml-explain-model**
    + Argument för fasta omvandlingar för LIME-förklaringar för rå funktions prioritet i azureml-contrib-Restore-Model-paket
    + Lägg till scipy-sparse-stöd för LimeExplainer
    + lagt till en linjär förklaring av form, samt en annan nivå till förklaring av tabell för att förklara linjära modeller
    + för att efterlikna förklarare i förklarings modell bibliotek, åtgärdat fel när include_local = falskt för sparse-indata
    + Lägg till förväntade värden i `automl` utdata
    + åtgärds prioritet för fast permutation när omvandlings argument har angetts för att hämta funktioner för RAW-funktioner
    + har lagts `batch_size` till i härmar `include_local=False` -förklaringen när, för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel
    + för modell för förklarande av modeller är fasta blackbox-förklaringar där Pandas dataframe Indatatyp krävs för förutsägelse
    + Ett fel har åtgärd ATS där `explanation.expected_values` skulle ibland returnera ett flyttal i stället för en lista med ett flyttal.
  + **azureml-mlflow**
    + Förbättra prestanda för mlflow.set_experiment (experiment_name)
    + Åtgärda fel vid användning av InteractiveLoginAuthentication för mlflow tracking_uri
    + Förbättra resursutnyttjande för fjärrkörningar med hjälp av azureml. mlflow.
    + Förbättra dokumentationen för azureml-mlflow-paketet
    + Korrigerings fel där mlflow.log_artifacts ("my_dir") sparar artefakter under "my_dir/<artefakt-sökvägar>" i stället för "<artefakt-sökvägar>"
  + **azureml-opendatasets**
    + PIN-kod `pyarrow` `opendatasets` till gamla versioner (<0.14.0) på grund av ett minnes problem som nyligen har introducerats där.
    + Flytta azureml-contrib-opendataset till azureml-opendataset.
    + Tillåt att öppna data uppsättnings klasser registreras på Azure Machine Learning arbets yta och utnyttja funktionerna för AML-datauppsättning sömlöst.
    + Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.
  + **azureml-pipeline-steps**
    + DBFS data lager stöds nu för indata och utdata i DatabricksStep.
    + Uppdaterad dokumentation för Azure Batch steg med avseende på indata/utdata.
    + I AzureBatchStep ändrades *delete_batch_job_after_finish* standardvärdet till *Sant*.
  + **azureml-telemetry**
    +  Flytta azureml-contrib-opendataset till azureml-opendataset.
    + Tillåt att öppna data uppsättnings klasser registreras på Azure Machine Learning arbets yta och utnyttja funktionerna för AML-datauppsättning sömlöst.
    + Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.
  + **azureml-träna-automl**
    + Uppdaterad dokumentation på get_output som visar den faktiska retur typen och ger ytterligare information om hur du hämtar nyckel egenskaper.
    + Uppdatera NimbusML-beroendet till 1.2.0-versionen (aktuell senaste).
    + Lägg till förväntade värden i `automl` utdata
  + **azureml-train-core**
    + Strängar har nu accepterats som beräknings mål för automatisk justering av den automatiska parametern
    + Den oanvända RunConfiguration-inställningen auto_prepare_environment har marker ATS som föråldrad.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning data prep SDK v 1.1.9

+ **Nya funktioner**
  + Stöd har lagts till för att läsa en fil direkt från en http-eller HTTPS-URL.

+ **Fel korrigeringar och förbättringar**
  + Förbättrat fel meddelande vid försök att läsa en Parquet-datauppsättning från en fjärrkälla (som inte stöds för närvarande).
  + En bugg har åtgärd ATS vid skrivning till Parquet-filformat i ADLS gen 2 och uppdatering av ADLS gen 2-behållare i sökvägen.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Visuellt gränssnitt
+ **Förhandsgranskningsfunktioner**
  + Modulen "kör R-skript" har lagts till i Visual Interface.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK för python v-1.0.48

+ **Nya funktioner**
  + **azureml-opendatasets**
    + **azureml-contrib-OpenData uppsättningar** är nu tillgängliga som **azureml-opendataset**. Det gamla paketet kan fortfarande fungera, men vi rekommenderar att du använder **azureml-OpenData uppsättningar** för att flytta framåt för bättre funktioner och förbättringar.
    + Med det nya paketet kan du registrera öppna data uppsättningar som data uppsättning i Azure Machine Learning arbets ytan och utnyttja de funktioner som data uppsättningen erbjuder.
    + Den innehåller också befintliga funktioner som att använda öppna data uppsättningar som Pandas/SPARK-dataframes och plats anslutningar för en viss data uppsättning som väder.

+ **Förhandsgranskningsfunktioner**
    + HyperDriveConfig kan nu acceptera pipeline-objekt som en parameter för att stöda en inställning för hel parameter med en pipeline.

+ **Fel korrigeringar och förbättringar**
  + **azureml-träna-automl**
    + Korrigerade felet om att förlora kolumn typer efter omvandlingen.
    + Korrigerade felet för att tillåta y_query vara en objekt typ som inte innehåller några/inga (några) i början.
    + Har åtgärdat problemet i urvals proceduren för ensemblen som inte nödvändigt vis skulle växa till den resulterande ensemblen även om poängen var konstant.
    + Åtgärdade problemet med Tillåt list_models och blockera list_models inställningar i AutoMLStep.
    + Ett problem har åtgärd ATS som förhindrade användningen av för bearbetning när AutoML skulle ha använts i samband med Azure ML-pipelines.
  + **azureml-opendatasets**
    + Flyttade azureml-contrib-opendataset till azureml-opendataset.
    + Tillåtna öppna data uppsättnings klasser som ska registreras på Azure Machine Learning arbets ytan och utnyttja AML-datauppsättnings funktionerna sömlöst.
    + Förbättrad NoaaIsdWeather-prestanda i en icke-SPARK-version nämnvärt.
  + **azureml-explain-model**
    + Uppdaterad online-dokumentation för tolknings objekt.
    + Har lagts `batch_size` till i härmar `include_local=False` -förklaringen när, för att strömma globala förklaringar i batchar för att förbättra körnings tiden för DecisionTreeExplainableModel för bibliotek med modell förklaringar.
    + Vi har åtgärdat problemet där `explanation.expected_values` skulle ibland returnera ett flyttal i stället för en lista med ett flyttal.
    + Förväntade värden har lagts till i `automl` utmatningen för härma-förklaringen i förklarar modell bibliotek.
    + Fast permutation-funktions prioritet när omvandlings argument har angetts för att få till gång till funktioner för RAW-funktioner.
  + **azureml-core**
    + Möjligheten att bifoga DBFS-datalager har lagts till i AzureML CLI.
    + Åtgärdade problemet med data lager uppladdning där en tom mapp skapas om den `target_path` startas med `/` .
    + Jämförelse av två data uppsättningar har Aktiver ATS.
    + Modell och avbildning ta bort nu innehåller mer information om hur du hämtar överordnade objekt som är beroende av dem om borttagningen Miss lyckas på grund av ett överordnat beroende.
    + Föråldrade inställningen för oanvända RunConfiguration i auto_prepare_environment.
  + **azureml-mlflow**
    + Förbättrad resursutnyttjande för fjärrkörningar som använder azureml. mlflow.
    + Förbättrad dokumentation av azureml-mlflow-paketet.
    + Ett problem har åtgärd ATS där mlflow.log_artifacts ("my_dir") skulle spara artefakter under "my_dir/Artifact-Paths" i stället för "artefakt Sök vägar".
  + **azureml-pipeline-core**
    + Parametern hash_paths för alla steg i pipeline är föråldrad och kommer att tas bort i framtiden. Som standard är innehållet i source_directory hash-kodat (förutom filer som anges i `.amlignore` eller `.gitignore` )
    + Fortsatte att förbättra modul-och ModuleStep för att stödja beräknings-/regionsspecifika moduler, för att förbereda för RunConfiguration-integrering och andra ändringar för att låsa upp beräkning av beräknings typens modul i pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: förbättrad dokumentation med avseende på indata/utdata.
    + AzureBatchStep: ändrade delete_batch_job_after_finish standardvärdet till sant.
  + **azureml-train-core**
    + Strängar accepteras nu som beräknings mål för automatisk justering av den automatiska inställningen.
    + Föråldrade inställningen för oanvända RunConfiguration i auto_prepare_environment.
    + Föråldrade parametrar `conda_dependencies_file_path` och deras `pip_requirements_file_path` prioriterade respektive `conda_dependencies_file` `pip_requirements_file` .
  + **azureml-opendatasets**
    + Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK för python v-1.0.33 har släppts.

+ Azure ML Maskinvaruaccelererade modeller på [FPGAs](how-to-deploy-fpga-web-service.md) är allmänt tillgängligt.
  + Nu kan du [använda paketet azureml-acceleration-Models](how-to-deploy-fpga-web-service.md) för att:
    + Träna vikterna för ett djup neurala-nätverk som stöds (ResNet 50, ResNet 152, DenseNet-121, VGG-16 och SSD-VGG)
    + Använda transfer Learning med de DNN som stöds
    + Registrera modellen med Modellhantering tjänst och Använd modellen
    + Distribuera modellen till en virtuell Azure-dator med en FPGA i ett Azure Kubernetes service-kluster (AKS)
  + Distribuera behållaren till en [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md) server enhet
  + Betygs ätt dina data med gRPC-slutpunkten med det här [exemplet](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatiserad maskininlärning

+ Funktion som sveper för att aktivera dynamiskt tillägg :::no-loc text="featurizers"::: för prestanda optimering. Ny :::no-loc text="featurizers"::: : arbets inbäddning, vikt för bevis, mål kodning, text måls kodning, kluster avstånd
+ Smart CV för hantering av träna/giltig Split i automatiserad ML
+ Få minnes optimerings ändringar och förbättringar av körnings prestanda
+ Prestanda förbättring i modell förklaring
+ ONNX modell konvertering för lokal körning
+ Stöd för under sampling har lagts till
+ Intelligent stopp när inga avslutnings kriterier har definierats
+ Staplade ensembler

+ Prognostisering för tidsserier
  + Ny predict prediktion-funktion
  + Nu kan du använda kors validering av rullande ursprung i Time Series-data
  + Nya funktioner som har lagts till för att konfigurera Time Series-lags
  + Nya funktioner som har lagts till för att stödja löpande fönster mängd funktioner
  + Ny helgdags identifiering och upplärda när lands koden definieras i experiment inställningarna

+ Azure Databricks
  + Aktive rad tids serie prognoser och modell explainabilty/tolknings kapacitet
  + Du kan nu avbryta och återuppta (fortsätta) automatiserade ML-experiment
  + Stöd har lagts till för multicore-bearbetning

### <a name="mlops"></a>MLOps
+ **Lokal distribution & fel sökning för Poäng behållare**<br/> Nu kan du distribuera en ML-modell lokalt och iterera snabbt på bedömnings filen och beroenden för att se till att de fungerar som förväntat.

+ **Introducerade InferenceConfig & Model. Deploy ()**<br/> Modell distribution har nu stöd för att ange en källmapp med ett Entry-skript, samma som en RunConfig.  Dessutom har modell distributionen förenklats till ett enda kommando.

+ **Git-referens spårning**<br/> Kunder har begärt grundläggande funktioner för git-integrering under en viss tid eftersom det hjälper till att upprätthålla en fullständig Gransknings logg. Vi har implementerat spårning över större entiteter i Azure ML för git-relaterade metadata (lagrings platsen, commit, Clean State). Den här informationen samlas in automatiskt av SDK och CLI.

+ **Modell profilering & validerings tjänst**<br/> Kunderna har ofta svårt att på rätt sätt anpassa den beräkning som är associerad med deras härlednings tjänst. Med vår modell profilerings tjänst kan kunden tillhandahålla exempel indata och vi ska profilera över 16 olika processor-/minnes konfigurationer för att fastställa optimal storlek för distribution.

+ **Ta med din egen bas avbildning för att få en härledning**<br/> En annan vanlig klagomål var svårt att flytta från experimentering till att dela om beroenden. Med vår nya bas avbildnings delnings funktion kan du nu återanvända dina bas avbildningar, beroenden och alla, för att kunna hantera störningar. Detta bör påskynda distributionen och minska mellanrummet från insidan till den yttre slingan.

+ **Förbättrad generering av Swagger-scheman**<br/> Vår tidigare generations Swagger-metod var fel känslig och omöjlig att automatisera. Vi har ett nytt infogat sätt att generera Swagger-scheman från valfri python-funktion via dekoratörer. Vi har öppen källkod för den här koden och vårt schema generations protokoll är inte kopplat till Azure ML-plattformen.

+ **Azure ML CLI är allmänt tillgänglig (GA)**<br/> Modeller kan nu distribueras med ett enda CLI-kommando. Vi har en gemensam feedback från kunden att ingen distribuerar en ML-modell från en Jupyter Notebook. [**Referens dokumentationen för cli**](./reference-azure-machine-learning-cli.md) har uppdaterats.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK för python v-1.0.30 har släppts.

[`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint)Introducerades för att lägga till en ny version av en publicerad pipeline samtidigt som du behåller samma slut punkt.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Nu kan du skicka om ett befintligt skript som körs på ett befintligt fjärrberäknings kluster.
  + Nu kan du köra en publicerad pipeline med nya parametrar på fliken pipeliner.
  + Körnings informationen stöder nu ett nytt visnings program för Snapshot-filen. Du kan visa en ögonblicks bild av katalogen när du skickade en speciell körning. Du kan också hämta den antecknings bok som har skickats för att starta körningen.
  + Du kan nu avbryta överordnade körningar från Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK för python v-1.0.23

+ **Nya funktioner**
  + Azure Machine Learning SDK stöder nu python 3,7.
  + Azure Machine Learning DNN-uppskattningar har nu inbyggt stöd för flera versioner. Till exempel `TensorFlow` kan en uppskattning nu acceptera en `framework_version` parameter och användarna kan ange version 1,10 eller 1,12. Om du vill ha en lista över de versioner som stöds av din aktuella SDK-version, kan `get_supported_versions()` du anropa den önskade Ramverks klassen (till exempel `TensorFlow.get_supported_versions()` ).
  En lista över de versioner som stöds av den senaste SDK-versionen finns i [dokumentationen för DNN-uppskattning](/python/api/azureml-train-core/azureml.train.dnn).

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK för python v-1.0.21

+ **Nya funktioner**
  + Med metoden *azureml.Core.Run.create_children* kan du skapa flera underordnade körningar med ett enda anrop vid låg latens.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK för python v-1.0.18

 + **Ändringar**
   + Azureml-tensorboard-paketet ersätter azureml-contrib-tensorboard.
   + I den här versionen kan du konfigurera ett användar konto på ditt hanterade beräknings kluster (amlcompute) medan du skapar det. Detta kan göras genom att de här egenskaperna skickas i konfigurationen. Du hittar mer information i [referens dokumentationen för SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning data prep SDK v 1.0.17

+ **Nya funktioner**
  + Har nu stöd för att lägga till två numeriska kolumner för att generera en resulterande kolumn med uttrycks språket.

+ **Fel korrigeringar och förbättringar**
  + Förbättrad dokumentation och parameter kontroll för random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning data prep SDK v 1.0.16

+ **Fel korrigering**
  + Åtgärdat problem med tjänstens huvud namn som orsakades av en API-ändring.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK för python v-1.0.17

+ **Nya funktioner**
  + Azure Machine Learning har nu det första klass stödet för populär DNN Framework-kedja. Användning av [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) klass användare kan enkelt träna och distribuera kedje modeller.
    + Lär dig hur du [Kör distribuerad utbildning med ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Lär dig hur du kör en inställning för min [parameter med en kedja med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines har lagt till möjlighet att utlösa en pipeline-körning baserat på data lager ändringar. [Antecknings boken](https://aka.ms/pl-schedule) för pipeline-schemat uppdateras för att visa den här funktionen.

+ **Fel korrigeringar och förbättringar**
  + Vi har lagt till stöd i Azure Machine Learning pipelines för att ställa in source_directory_data_store-egenskapen till ett önskat data lager (till exempel en blob-lagring) på [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) som anges i [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep). Som standard använder Azure File Store som lagrings data lager, som kan köra begränsnings problem när ett stort antal steg körs samtidigt.

### <a name="azure-portal"></a>Azure Portal

+ **Nya funktioner**
  + Ny dra och släpp tabell redigerings miljö för rapporter. Användare kan dra en kolumn från en källa till tabell området där en förhands granskning av tabellen kommer att visas. Kolumnerna kan ordnas om.
  + Nya loggar fil visare
  + Länkar till experiment körningar, Compute, modeller, avbildningar och distributioner från fliken aktiviteter

## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](overview-what-is-azure-ml.md).
