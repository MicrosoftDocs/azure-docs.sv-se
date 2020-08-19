---
title: Vad är nytt i versionen?
titleSuffix: Azure Machine Learning
description: 'Lär dig mer om de senaste uppdateringarna för Azure Machine Learning och Machine Learning och data prepare SDK: er.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: d93e01cdec79f739367dc219d81028c1abc2d66e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508217"
---
# <a name="azure-machine-learning-release-notes"></a>Viktig information om Azure Machine Learning

I den här artikeln får du lära dig mer om Azure Machine Learning-versioner.  Information om fullständiga SDK-referenser finns på Azure Machine Learning huvud sidan [**för SDK för python**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) -referens.

Se [listan över kända problem](resource-known-issues.md) för att lära dig om kända buggar och lösningar.

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
    + Ny metod `set_pip_requirements()` för att ställa in hela pip-avsnittet på [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) en gång.
    + Aktivera registrering av autentiseringsuppgifter – mindre ADLS Gen2 data lager.
    + Förbättrat fel meddelande vid försök att ladda ned eller montera en felaktig data uppsättnings typ.
    + Uppdatera timeseries data uppsättning filter exempel på bärbara datorer med fler exempel på partition_timestamp som tillhandahåller filter optimering.
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
  + **azureml-träna-automl-runtime**
    + AutoML genererar en ny felkod från nu när innehållet ändras under läsning.
    + Problem med fasta ögonblicks bilder vid sändning av AutoML-körningar utan användardefinierade skript.
    + Fasta, underordnade körnings problem när data innehåller Nan och funktionalisering har inaktiverats.
  + **azureml-train-core**
    + Stöd har lagts till för att ange pip-alternativ (t. ex.--extra-index-URL) i PIP-krav filen som skickas till en- [`Estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) `pip_requirements_file` parameter.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK för python v-1.11.0

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Korrigera modell ramverk och modell ramverk överförs inte i körnings objekt i en registrerings Sök väg för cli-modellen
    + Åtgärda kommandot CLI amlcompute Identity show för att visa klient-ID och ägar-ID 
  + **azureml-train-automl-client**
    + Lade till get_best_child () i AutoMLRun för att hämta den bästa underordnade körningen för en AutoML-körning utan att hämta den associerade modellen.
    + ModelProxy-objektet har lagts till, vilket gör att förutsägelse eller prognos kan köras i en fjärran sluten tränings miljö utan att modellen laddas ned lokalt.
    + Ohanterade undantag i AutoML pekar nu på en känd HTTP-sida med kända problem, där mer information om felen kan hittas.
  + **azureml-core**
    + Modell namn får innehålla 255 tecken.
    + Miljö. get_image_details () returnerad objekt typ har ändrats. `DockerImageDetails` klass ersatt `dict` , bild information är tillgänglig från de nya klass egenskaperna. Ändringarna är bakåtkompatibla.
    + Åtgärda fel för miljön. from_pip_requirements () för att bevara beroende struktur
    + Ett fel uppstod där log_list skulle uppstå om en int-och Double-åtgärd inkluderades i samma lista.
    + Tänk på att om det finns beräknings mål som är kopplade till arbets ytan när du aktiverar privat länk på en befintlig arbets yta, kommer dessa mål inte att fungera om de inte ligger bakom samma virtuella nätverk som den privata slut punkten för arbets ytan.
    + Görs `as_named_input` valfri när du använder data uppsättningar i experiment och `as_mount` lagt `as_download` till och till `FileDataset` . Indatamängden skapas automatiskt om `as_mount` eller `as_download` anropas.
  + **azureml-automl-core**
    + Ohanterade undantag i AutoML pekar nu på en känd HTTP-sida med kända problem, där mer information om felen kan hittas.
    + Lade till get_best_child () i AutoMLRun för att hämta den bästa underordnade körningen för en AutoML-körning utan att hämta den associerade modellen.
    + ModelProxy-objektet har lagts till, vilket gör att förutsägelse eller prognos kan köras i en fjärran sluten tränings miljö utan att modellen laddas ned lokalt.
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
  + **azureml-träna-automl-runtime**
    + Förbättrade konsol utdata när bästa modell förklaringar inte fungerar.
    + Har bytt namn till Indataparametern "backlist_models" till "blocked_models".
      + Har bytt namn till Indataparametern "whitelist_models" till "allowed_models".
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
    + Aktive rad anpassad fyllnads rad för forcasting uppgifter
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
    + En verifiera parameter till dessa API: er genom att tillåta att verifiering hoppas över när data källan inte är tillgänglig från den aktuella beräkningen.
      + TabularDataset. time_before (end_time, include_boundary = True, validate = true)
      + TabularDataset. time_after (start_time, include_boundary = True, validate = true)
      + TabularDataset. time_recent (time_delta, include_boundary = True, validate = true)
      + TabularDataset. time_between (start_time, end_time, include_boundary = True, validate = true)
    + Stöd för Ramverks filtrering för modell lista har lagts till och NCD automl-exemplet har lagts till i Notebook tillbaka
    + För data lager. register_azure_blob_container och data lager. register_azure_file_share (endast alternativ som stöder SAS-token) har vi uppdaterat dokument strängarna för `sas_token` fältet som innehåller minimi kraven för behörighet för vanliga Läs-och skriv scenarier.
    + Föråldrad _with_auth param i ws. get_mlflow_tracking_uri ()
  + **azureml-mlflow**
    + Lägg till stöd för distribution av lokala file://-modeller med AzureML-MLflow
    + Föråldrad _with_auth param i ws. get_mlflow_tracking_uri ()
  + **azureml-opendatasets**
    + Nyligen publicerade Covid-19 spårnings data uppsättningar är nu tillgängliga med SDK: n
  + **azureml-pipeline-core**
    + Varning om utloggning när "azureml-defaults" inte ingår som en del av pip-beroendet
    + Förbättra antecknings åter givning.
    + Stöd för citerade rad brytningar har lagts till vid parsning av avgränsade filer till PipelineOutputFileDataset.
    + PipelineDataset-klassen är föråldrad. Mer information finns i https://aka.ms/dataset-deprecation. Lär dig hur du använder data uppsättning med pipeline, se https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline – steg**
    + Dok uppdateringar av azureml-pipeline – steg.
    +  Stöd har lagts till i ParallelRunConfig `load_yaml()` för att användare ska kunna definiera miljöer infogade i resten av konfigurationen eller i en separat fil
  + **azureml-träna-automl-client**.
    + Har tagit bort möjligheten att ange `enable_cache` som en del av AutoMLConfig
  + **azureml-träna-automl-runtime**
    + Begränsad tillgänglighet för multi-nodeed, multi-GPU distribuerade funktionalisering med BERT har lagts till.
    + Fel hantering lades till för inkompatibla paket i ADB-baserade automatiserade maskin inlärnings körningar.
  + **azureml-widgets**
    + Doc-uppdateringar av azureml-widgetar.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK för python v-1.8.0
  
  + **Förhandsgranskningsfunktioner**
    + **azureml-contrib-skälighet** `azureml-contrib-fairness` Paketet ger integrering mellan skälighet-utvärdering av öppen källkod och [Fairlearn](https://fairlearn.github.io) och Azure Machine Learning Studio. Paketet gör det särskilt möjligt att ladda upp instrument paneler för modell skälighet-utvärdering som en del av en AzureML-körning och visas i Azure Machine Learning Studio

+ **Fel korrigeringar och förbättringar**
  + **Azure-CLI-ml**
    + Stöd för att hämta loggar för init-behållare.
    + Nya CLI-kommandon har lagts till för hantering av ComputeInstance
  + **azureml-automl-core**
    + Användare kan nu aktivera stack-Ensemble iteration för timeseries-uppgifter med en varning om att det kan overfit.
    + Lade till en ny typ av användar undantag `azureml.automl.core.shared.exceptions.CacheStoreCorruptedException` som aktive ras om cachens innehåll har ändrats
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
    + Stöd användare har valt data lager för lagring av profilerings data
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
    + Bättre minnes hantering i händelse av OutOfMemory-problem för to_pandas_dataframe.
  + **azureml – tolka**
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
  + **azureml-pipeline – steg**
    + Dokument förbättringar av azureml-pipeline – steg-paket.
    + Passthru_automl_configs kravet för användning av mellanliggande data med AutoMLStep i pipelines har tagits bort.
  + **azureml-telemetry**
    + Dokument förbättringar för azureml-telemetri.
  + **azureml-train-automl-client**
    + Ett fel som `experiment.submit()` anropades två gånger på ett `AutoMLConfig` objekt resulterade i olika beteenden.
    + Användare kan nu aktivera stack-Ensemble iteration för timeseries-uppgifter med en varning om att det kan overfit.
    + Ändrade AutoML körnings beteende för att utlösa UserErrorException om tjänsten genererar användar fel
    + Åtgärdar en bugg som orsakade azureml_automl. log till att inte generera eller saknade loggar när ett AutoML-experiment utförs på ett fjärrberäknings mål.
    + För klassificerings data uppsättningar med obalanserade klasser, kommer vi att tillämpa Viktnings utjämning, om funktionen Sweeper avgör att Viktnings aktiviteten får bättre prestanda vid en viss tröskel vid Viktnings utjämning.
    + AutoML-körningar är nu markerade som underordnad körning av parallellt körnings steg.
  + **azureml-träna-automl-runtime**
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
  + **azureml – tolka**
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
      
+ **Bryta ändringar**
  + **azureml-pipeline-steps**
    + Föråldrad användning av azureml. dprep. data flöde som en giltig typ av indata för AutoMLConfig
  + **azureml-train-automl-client**
    + Föråldrad användning av azureml. dprep. data flöde som en giltig typ av indata för AutoMLConfig

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-core**
    + Åtgärdade felet där en varning kan skrivas ut under `get_output` den frågade användaren att nedgradera klienten.
    + Uppdaterad Mac som förlitar sig på cudatoolkit = 9.0 eftersom den inte är tillgänglig i version 10 ännu.
    + Ta bort begränsningar för phrophet-och xgboost-modeller när de tränas på fjärrberäkning.
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
    + Mycket förbättrad fel text i händelse av körnings fel för data mängder.
    + Stöd för privat länk CLI för arbets ytan har lagts till.
    + En valfri parameter har lagts `invalid_lines` till `Dataset.Tabular.from_json_lines_files` som gör det möjligt att ange hur rader som innehåller ogiltig JSON ska hanteras.
    + Vi kommer att bli föråldrad med körningen av data bearbetning i nästa version. Vi rekommenderar att du skapar ett faktiskt Amlcompute-kluster som ett beständigt beräknings mål och använder kluster namnet som beräknings mål i din körnings konfiguration. Se exempel på antecknings boken här: aka.ms/amlcomputenb
    + Betydligt förbättrade fel meddelanden vid körning av data uppsättningar.
  + **azureml-dataprep**
    + Varning för att uppgradera pyarrow-versionen mer explicit.
    + Förbättrad fel hantering och meddelande som returneras om du inte kan köra data flöde.
  + **azureml – tolka**
    + Dokumentations uppdateringar av azureml-tolknings paketet.
    + Fasta tolknings paket och antecknings böcker är kompatibla med den senaste sklearn-uppdateringen
  + **azureml-opendatasets**
    + returnera ingen när inga data returneras.
    + Förbättra prestanda för to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Snabb korrigering för ParallelRunStep där inläsning från YAML var bruten
    + ParallelRunStep släpps till allmän tillgänglighet – azureml. contrib. pipeline. steg har ett meddelande om förvarning och flyttar till azureml. pipeline. steg – nya funktioner är: 1. Data uppsättningar som PipelineParameter 2. Ny parameter run_max_retry 3. Konfigurerbar append_row utdatafilens fil namn
  + **azureml-pipeline – steg**
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
    + Påskynda Prophet/AutoArima-modellen i AutoML-prognoser genom att aktivera parallell montering för tids serien när data mängder har flera tids serier. För att kunna dra nytta av den här nya funktionen rekommenderar vi att du anger "max_cores_per_iteration =-1" (t. ex. med alla tillgängliga processor kärnor) i AutoMLConfig.
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
    + Aktiverade WASB-> BLOB-konverteringar i USGovernment-och Kina-moln.
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
  + **azureml – tolka**
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
    + Ett åtgärds fel med AutoML körs inte i show_output
  + **azureml-träna-automl-runtime**
    + Åtgärda ett fel i Ensemble-iterationer som förhindrade att tids gränsen för modell hämtningen skulle köras.
  + **azureml-train-core**
    + Korrigera skrivning i azureml. träna. DNN. Nccl-klassen.
    + Stöd för PyTorch version 1,5 i PyTorch-uppskattningen
    + Åtgärda problemet att det inte går att hämta Ramverks avbildningen i Fairfax region när du använder utbildnings Ramverks uppskattningar

  
## <a name="2020-05-04"></a>2020-05-04
**Ny Notebook-upplevelse**

Nu kan du skapa, redigera och dela Machine Learning-anteckningsböcker och filer direkt i Studio-webbupplevelsen av Azure Machine Learning. Du kan använda alla klasser och metoder som är tillgängliga i [Azure Machine Learning python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) inifrån dessa antecknings böcker kom igång [här](https://docs.microsoft.com/azure/machine-learning/how-to-run-jupyter-notebooks)

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
    
| Webbaserat verktyg  |     Beskrivning  | Utgåva | 
|---|---|---|
| Azure ML Studio-anteckningsböcker   |     Första redigering i klass för notebook-filer och stöd för alla åtgärder som är tillgängliga i Azure ML python SDK. | Basic & Enterprise  |   

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK för python v-1.4.0

+ **Nya funktioner**
  + AmlCompute-kluster har nu stöd för att konfigurera en hanterad identitet i klustret vid tidpunkten för etablering. Ange bara om du vill använda en tilldelad identitet eller en tilldelad identitet, och skicka en identityId i fallet med den senare. Du kan sedan konfigurera behörigheter för att få åtkomst till olika resurser, t. ex. lagrings-eller ACR på ett sätt som data bearbetningen används för säker åtkomst till data, i stället för en tokenbaserad metod som AmlCompute använder idag. Kolla in vår SDK-referens för mer information om parametrarna.
  

+ **Bryta ändringar**
  + AmlCompute-kluster har stöd för en förhands gransknings funktion med körnings skapande, som vi planerar om föråldrade i två veckor. Du kan fortsätta att skapa fasta beräknings mål som alltid genom att använda klassen Amlcompute, men den specifika metoden för att ange identifieraren "Amlcompute" som beräknings mål i Run config stöds inte inom en snar framtid. 

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Aktivera stöd för en typ som inte stöder hash när du beräknar antalet unika värden i en kolumn.
  + **azureml-core**
    + Förbättrad stabilitet vid läsning från Azure Blob Storage med hjälp av en TabularDataset.
    + Förbättrad dokumentation för- `grant_workspace_msi` parametern för `Datastore.register_azure_blob_store` .
    + Åtgärdat fel med `datastore.upload` som stöd för `src_dir` argumentet som slutar med en `/` eller `\` .
    + Ett åtgärds bara fel meddelande har lagts till vid överföring till ett Azure Blob Storage-datalager som inte har någon åtkomst nyckel eller SAS-token.
  + **azureml – tolka**
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
    + Påskyndar automatisk ARIMA-utbildning med hjälp av en villkorlig summering av kvadratmetoden (CSS) för en serie med längd som är längre än 100. Observera att den använda längden lagras som en konstant ARIMA_TRIGGER_CSS_TRAINING_LENGTH med TimeSeriesInternal-klassen på/src/azureml-automl-Core/azureml/automl/Core/Shared/constants.py
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
    +  Environment.docker. base_dockerfile accepterar sökväg. Om det går att matcha en fil kommer innehållet att läsas in i base_dockerfile miljö egenskap
    + Återställ automatiskt ömsesidigt uteslutande värden för base_image och base_dockerfile när användaren manuellt anger ett värde i Environment.docker
    + Lade till user_managed flagga i RSection som anger om miljön hanteras av användare eller av AzureML.
    + Data uppsättning: det gick inte att ladda fast data mängd om data Sök vägen innehåller Unicode-tecken.
    + Data uppsättning: förbättrad datauppsättning montering av cachelagring för att respektera det minsta disk utrymme som krävs i Azure Machine Learning Compute, vilket förhindrar att noden blir oanvändbar och gör att jobbet avbryts.
    + Data uppsättning: vi lägger till ett index för kolumnen Time Series när du har åtkomst till en Time Series-datauppsättning som en Pandas-dataframes, som används för att påskynda åtkomsten till Time Series-baserad data åtkomst.  Tidigare har indexet samma namn som kolumnen tidsstämpelkolumn, förvirrande användare om vilka är den faktiska tidsstämpelkolumn och som är indexet. Nu ger vi inte något speciellt namn till indexet eftersom det inte ska användas som en kolumn. 
    + Data uppsättning: problem med autentisering av fast data mängd i det suveräna molnet.
    + Data uppsättning: fast- `Dataset.to_spark_dataframe` felet för data uppsättningar som skapats från Azure postgresql-datalager.
  + **azureml – tolka**
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
  + **azureml-träna-automl-runtime**
    + Ytterligare telemetri kring åtgärder efter utbildning har lagts till.
    + stöd för sparse-AutoML E2E har lagts till
  + **azureml-opendatasets**
    + Ytterligare telemetri för tjänst övervakaren har lagts till.
    + Aktivera front dörren för BLOB för att öka stabiliteten 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK för python v-1.2.0

+ **Bryta ändringar**
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
  + **azureml – tolka**
    + Uppdaterade azureml-tolkning till tolkning – community 0,7. *
  + **azureml – SDK**
    + Ändra till beroenden med kompatibel version Tilde för att stödja korrigering i för hands versionen och stabila versioner.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK för python v-1.1.5

+ **Funktions utfasning**
  + **Python 2,7**
    + Senaste versionen som stöd för python 2,7

+ **Bryta ändringar**
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
  + **azureml – tolka**
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
  + **azureml-pipeline – steg**
    + Har flyttat `AutoMLStep` till `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
    + Exempel på dokumentation som lagts till för data uppsättning som PythonScriptStep-indata
  + **azureml-tensorboard**
    + uppdaterade azureml-tensorboard för att stödja tensorflow 2,0
    + Visa rätt port nummer när du använder en anpassad Tensorboard-port på en beräknings instans
  + **azureml-train-automl-client**
    + Ett problem har åtgärd ATS där vissa paket kan installeras vid felaktiga versioner av fjärrkörningar.
    + fast FeaturizationConfig åsidosätter problem som filtrerar anpassad funktionalisering-konfiguration.
  + **azureml-träna-automl-runtime**
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
  + **azureml – tolka**
    + uppdatera azureml – tolka till tolka – community 0,6. *
  + **azureml-mlflow**
    + Lägg till stöd för suveräna moln till azureml. mlflow
  + **azureml-pipeline-steps**
    + Flyttade `AutoMLStep` till `azureml-pipeline-steps package` . Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Ett problem har åtgärd ATS där vissa paket kan installeras vid felaktiga versioner av fjärrkörningar.
  + **azureml-träna-automl-runtime**
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
  + **azureml – tolka**
    + azureml-undantag har lagts till i azureml-tolka
    + fast DeepScoringExplainer-serialisering för keras-modeller
  + **azureml-pipeline-core**
    + I pipeline för batch-Poäng för batch används nu ParallelRunStep
  + **azureml-pipeline – steg**
    + Har flyttat `AutoMLStep` i `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Valfri parameter side_inputs lagts till i ParallelRunStep. Den här parametern kan användas för att montera en mapp på behållaren. Typer som stöds för närvarande är DataReference och PipelineData.
  + **azureml-tensorboard**
    + uppdaterade azureml-tensorboard för att stödja tensorflow 2,0
  + **azureml-train-automl-client**
    + fast FeaturizationConfig åsidosätter problem som filtrerar anpassad funktionalisering-konfiguration.
  + **azureml-träna-automl-runtime**
    + Har flyttat `AutoMLStep` i `azureml-pipeline-steps` paketet. Föråldrade `AutoMLStep` i `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Stöd för PyTorch version 1,4 i PyTorch-uppskattningen
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK för python v 1.1.0 RC0 (för hands version)

+ **Bryta ändringar**
  + **Semantisk versionshantering 2.0.0**
    + Från och med version 1,1 Azure ML python SDK antar 2.0.0 för semantisk versions hantering. [Läs mer här](https://semver.org/). Alla efterföljande versioner kommer att följa det nya numreringsschema och det semantiska versions kontraktet. 
  
+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + Ökad hastighet på funktionalisering.
    + Fast frekvens kontrollen under poängsättningen är nu i prognos uppgifterna att vi inte kräver strikt frekvens mellan tåg och test uppsättning.
  + **azureml-core**
    + Användaren kan nu ange ett värde för auth-nyckeln när nycklar för webtjänster återskapas.
  + **azureml – tolka**
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
    + Ändrade LocalWebservice. wait_for_deployment () för att kontrol lera status för den lokala Docker-behållaren innan du försöker pinga dess hälso slut punkt, vilket minskar den tid det tar att rapportera en misslyckad distribution.
    + Initieringen av en intern egenskap som används i LocalWebservice har åtgärd ATS. Läs in igen () när serviceobjektet skapas från en befintlig distribution med hjälp av LocalWebservice ()-konstruktorn.
    + Redigerat fel meddelande för klargörande.
    + En ny metod som kallas get_access_token () har lagts till i AksWebservice som kommer att returnera AksServiceAccessToken-objekt, som innehåller åtkomsttoken, uppdateras efter tidsstämpel, förfallo datum för timestamp och tokentyp. 
    + Föråldrad befintlig get_token ()-metod i AksWebservice som den nya metoden returnerar all information som den här metoden returnerar.
    + Ändrade utdata från AZ ml-kommandot Get-Access-token. Token har bytt namn till accessToken och refreshBy till refreshAfter. Har lagt till egenskaperna expiryOn och tokenType.
    + Fast get_active_runs
  + **azureml – förklara-modell**
    + uppdaterade Shap till 0.33.0 och tolkning – community till 0,4. *
  + **azureml – tolka**
    + uppdaterade Shap till 0.33.0 och tolkning – community till 0,4. *
  + **azureml-träna-automl-runtime**
    + Matthews korrelations faktor har lagts till som klassificerings mått för både binära och multiklass-klassificering.
    + Förbearbeta flagga från kod och ersatt med funktionalisering-funktionalisering är aktiverat som standard

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK för python v-1.0.83

+ **Nya funktioner**
  + Data uppsättning: Lägg till två alternativ `on_error` och `out_of_range_datetime` för `to_pandas_dataframe` att rapportera fel när data innehåller fel värden i stället för att fylla dem med `None` .
  + Arbets yta: har lagt till `hbi_workspace` flaggan för arbets ytor med känsliga data som möjliggör ytterligare kryptering och inaktiverar avancerad diagnostik på arbets ytor. Vi har också lagt till stöd för att ta med dina egna nycklar för den associerade Cosmos DB-instansen genom att ange `cmk_keyvault` `resource_cmk_uri` parametrarna och när du skapar en arbets yta, vilket skapar en Cosmos DB instans i prenumerationen när du skapar din arbets yta. [Läs mer här.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Fel korrigeringar och förbättringar**
  + **azureml-automl-runtime**
    + En regression har åtgärd ATS som gjorde att en TypeError skulle höjas när AutoML kördes på python-versioner under 3.5.4.
  + **azureml-core**
    + Det har åtgärd ATS ett fel i `datastore.upload_files` där den relativa sökvägen som inte startade med `./` inte kunde användas.
    + Utfasnings meddelanden för alla codepaths för bild klass har lagts till
    + Fast Modellhantering URL-konstruktion för Azure Kina 21Vianet-region.
    + Ett problem har åtgärd ATS där modeller som använder source_dir inte kan paketeras för Azure Functions.    
    + Ett alternativ har lagts till i [miljön. build_local ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) för att push-överföra en avbildning till azureml-arbetsyta behållare Registry
    + SDK har uppdaterats för att använda ett nytt token-bibliotek på Azure Synapse på ett sätt som är kompatibelt.
  + **azureml – tolka**
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
    + Lade till CreatedBy-information till modell-och tjänst objekt. Kan nås via <var> . created_by
    + Fast ContainerImage. Run (), som inte konfigurerades på rätt sätt i Docker-behållarens HTTP-port.
    + Gör `azureml-dataprep` valfritt för `az ml dataset register` CLI-kommando
    + En bugg har åtgärd ATS som `TabularDataset.to_pandas_dataframe` felaktigt skulle återgå till en alternativ läsare och skriva ut en varning.
  + **azureml – förklara-modell**
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

+ **Bryta ändringar**
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
  + **azureml – tolka**
    + uppdaterad version av tolka-community till 0.2.0
  + **azureml-pipeline – steg**
    + Dokumenterade värden för `runconfig_pipeline_params` i pipeline-steg för Azure Machine Learning.
  + **azureml-pipeline-core**
    + CLI-alternativet har lagts till för att hämta utdata i JSON-format för pipeline-kommandon.
  + **azureml-träna-automl**
    + Dela AzureML-träna-AutoML i två paket, ett klient paket AzureML-träna-AutoML-Client och ett ML-utbildnings paket AzureML-träna-AutoML-runtime
  + **azureml-train-automl-client**
    + Lade till en tunn klient för att skicka AutoML experiment utan att behöva installera några maskin inlärnings beroenden lokalt.
    + Korrigerad loggning av automatiskt identifierade lags, rullande fönster storlekar och maximala horisonter i fjärrkörningar.
  + **azureml-träna-automl-runtime**
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
    + Ett nytt API-amlcompute har lagts till. get_active_runs som returnerar en generator för körning och köade körningar på en specifik amlcompute.
    + Load Balancer typ har lagts till i MLC för AKS-typer.
    + Parametern append_prefix bool har lagts till för att download_files i run.py och download_artifacts_from_prefix i artifacts_client. Den här flaggan används för att selektivt förenkla den ursprungliga sökvägen så att bara fil-eller mappnamnet läggs till i output_directory
    + Åtgärda deserialiserings problem för `run_config.yml` med data uppsättnings användning.
    + När du anropar `keep_columns` eller `drop_columns` som resulterar i en Time Series-kolumn som släpps, tas även motsvarande funktioner bort för data uppsättningen.
  + **azureml – tolka**
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

Landnings sidan för samarbets ytan på [https://ml.azure.com](https://ml.azure.com) har förbättrats och anpassats som Azure Machine Learning Studio (för hands version).

Från Studio kan du träna, testa, distribuera och hantera Azure Machine Learning till gångar, till exempel data uppsättningar, pipeliner, modeller, slut punkter och mycket annat.

Få åtkomst till följande webbaserade redigerings verktyg från Studio:

| Webbaserat verktyg | Beskrivning | Utgåva |
|-|-|-|
| Notebook VM (för hands version) | Helt hanterad molnbaserad arbets Station | Basic & Enterprise |
| [Automatisk maskin inlärning](tutorial-first-experiment-automated-ml.md) (för hands version) | Ingen kod upplevelse för automatisering av maskin inlärnings modell utveckling | Stora företag |
| [Designer](concept-designer.md) (för hands version) | Verktyget dra och släpp Machine Learning-modellering som tidigare kallades designer | Stora företag |


### <a name="azure-machine-learning-designer-enhancements"></a>Förbättringar i Azure Machine Learning designer

+ Tidigare känt som det visuella gränssnittet 
+    11 nya [moduler](algorithm-module-reference/module-reference.md) , inklusive rekommendationer, klassificerare och utbildnings verktyg, inklusive funktions teknik, kors validering och data omvandling.

### <a name="r-sdk"></a>R SDK 
 
Data forskare och AI-utvecklare använder [Azure Machine Learning SDK för R](tutorial-1st-r-experiment.md) för att skapa och köra Machine Learning-arbetsflöden med Azure Machine Learning.

Azure Machine Learning SDK för R använder paketet för att `reticulate` binda till python SDK. Genom att binda direkt till python ger SDK för R dig åtkomst till kärn objekt och metoder som implementeras i python SDK från valfri R-miljö som du väljer.

Huvud funktionerna i SDK: n är:

+    Hantera molnresurser för övervakning, loggning och organisering av Machine Learning-experiment.
+    Träna modeller med moln resurser, inklusive GPU-accelererad modell träning.
+    Distribuera dina modeller som webservices på Azure Container Instances (ACI) och Azure Kubernetes service (AKS).

Fullständig dokumentation finns på [webbplatsen för paket](https://azure.github.io/azureml-sdk-for-r) .

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integration med Event Grid 

Azure Machine Learning är nu en resurs leverantör för Event Grid kan du konfigurera maskin inlärnings händelser via Azure Portal eller Azure CLI. Användare kan skapa händelser för slut för ande, modell registrering, modell distribution och data avvikelse upptäckt. Dessa händelser kan dirigeras till händelse hanterare som stöds av Event Grid för förbrukning. Mer information finns i artikeln om händelse [scheman](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning) och [självstudier](how-to-use-event-grid.md) i Machine Learning.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK för python v-1.0.72

+ **Nya funktioner**
  + Data uppsättningen har lagts till genom [**azureml-datadrift-**](https://docs.microsoft.com/python/api/azureml-datadrift) paketet, vilket möjliggör övervakning av Time Series-datauppsättningar för data drift eller andra statistiska förändringar över tid. Aviseringar och händelser kan utlösas om avvikelser upptäcks eller andra villkor för data är uppfyllda. Se [vår dokumentation](https://aka.ms/datadrift) för mer information.
  + Vi presenterar två nya versioner (kallas även SKU: er utbytbara) i Azure Machine Learning. I den här versionen kan du nu skapa en standard-eller Enterprise Azure Machine Learning-arbetsyta. Alla befintliga arbets ytor är standard versionen och du kan gå till Azure Portal eller Studio för att uppgradera arbets ytan när som helst. Du kan skapa antingen en Basic-eller Enterprise-arbetsyta från Azure Portal. Mer information finns i [vår dokumentation](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) . Från SDK kan du bestämma vilken version av din arbets yta som ska användas med hjälp av egenskapen "SKU" för ditt arbets objekt.
  + Vi har också gjort förbättringar för Azure Machine Learning beräkning – nu kan du visa mått för dina kluster (som till exempel totala antalet noder, köra noder, total kärn kvot) i Azure Monitor, förutom att Visa diagnostikloggar för fel sökning. Dessutom kan du också visa för tillfället pågående eller köade körningar i klustret och information, till exempel IP-adresser för de olika noderna i klustret. Du kan visa dessa antingen i portalen eller genom att använda motsvarande funktioner i SDK eller CLI.

  + **Förhandsgranskningsfunktioner**
    + Vi frigör för hands versions stöd för disk kryptering av din lokala SSD i Azure Machine Learning Compute. Kontakta ett tekniskt support ärende för att få din prenumeration att tillåta att den här funktionen används.
    + Offentlig för hands version av Azure Machine Learning batch-härledning. Azure Machine Learning batch-härledning riktas mot stora jobb som inte är tids känsliga. Med batch-härledning får du en kostnads effektiv skalnings beräkning med oöverträffade data flöden för asynkrona program. Den är optimerad för hög genom strömning, brand-och-glömma-härledning över stora data mängder.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
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
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI stöder nu modell paketering.
    + Lägg till utfasnings varning för inaktuella API: er för indata. Se meddelande om API-ändring för data uppsättning i https://aka.ms/tabular-dataset .
    + Ändra [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) till namn och version för returnerad registrering om data uppsättningen är registrerad.
    + Åtgärda ett fel som ScriptRunConfig med data uppsättning som argument kan inte användas upprepade gånger för att skicka experiment körning.
    + Data uppsättningar som hämtas under en körning spåras och visas på sidan körnings information eller genom att anropa [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) när körningen har slutförts.
    + Tillåt att mellanliggande data i Azure Machine Learning pipeline konverteras till data uppsättning i tabell format och används i [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Stöd har lagts till för distribution och paketering av modeller som stöds (ONNX, scikit-och TensorFlow) utan en InferenceConfig-instans.
    + Överskriven flagga för tjänst distribution (ACI och AKS) har lagts till i SDK och CLI. Om det här alternativet anges skrivs den befintliga tjänsten över om det redan finns en tjänst med namnet. Om tjänsten inte finns, skapas en ny tjänst.
    +  Modeller kan registreras med två nya ramverk, Onnx och Tensorflow. Modell registreringen tillåter indata från indata, exempel på utdata och resurs konfiguration för modellen.
    + Nya data lager har lagts till för Azure Database for MySQL. Exempel har lagts till för att använda Azure Database for MySQL i DataTransferStep i Azure Machine Learning pipelines.
    + Nya funktioner för att lägga till och ta bort taggar från experiment som lagts till för att ta bort taggar från körningar
    + Överskriven flagga för tjänst distribution (ACI och AKS) har lagts till i SDK och CLI. Om det här alternativet anges skrivs den befintliga tjänsten över om det redan finns en tjänst med namnet. Om tjänsten inte finns, skapas en ny tjänst.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Flyttad från `azureml-contrib-datadrift` i `azureml-datadrift`
    + Stöd har lagts till för övervakning av tids serie uppsättningar för drift och andra statistiska åtgärder
    + Nya metoder `create_from_model()` och `create_from_dataset()` till- [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) klassen. `create()`Metoden är föråldrad.
    + Justeringar av visualiseringar i python och UI i Azure Machine Learning Studio.
    + Stöd för schemaläggning per vecka och månatlig övervakning, förutom dagligen för data uppsättnings övervakare.
    + Stöd för bakfyllning av data övervaknings mått för att analysera historiska data för data uppsättnings övervakare.
    + Olika fel korrigeringar
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-nu behövs inte längre för att skicka en Azure Machine Learning pipeline-körning från pipelin `yaml` filen.
  + [**azureml-träna-automl**](/python/api/azureml-train-automl-runtime/)
    + Lägg till azureml – standardvärdet för automatiskt skapade Conda-kuvert för att lösa modell distributions problemet
    + AutoML-fjärrutbildningen innehåller nu azureml – standard för att tillåta åter användning av utbildnings miljö för en sådan härledning.
  + **azureml-train-core**
    + Stöd för PyTorch 1,3 har lagts till i [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) uppskattningen

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
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + API har lagts till för att avregistrera data uppsättningar. data uppsättning. [unregister_all_versions ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Har lagt till data mängds-API för att kontrol lera ändrad data tid. `dataset.data_changed_time`.
    + Att kunna använda `FileDataset` och `TabularDataset` som indata till `PythonScriptStep` , `EstimatorStep` och `HyperDriveStep` i Azure Machine Learning pipeline
    + Prestanda hos `FileDataset.mount` har förbättrats för mappar med ett stort antal filer
    + Att kunna använda [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) och [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) som indata till [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)och [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) i Azure Machine Learning pipelinen.
    + Prestanda för FileDataset. [montering ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) har förbättrats för mappar med ett stort antal filer
    + URL: en har lagts till för kända fel rekommendationer i körnings information.
    + Ett fel har åtgärd ATS i körningen. get_metrics där begär anden skulle uppstå om en körning hade för många underordnade
    + Ett fel har åtgärd ATS i [körningen. get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) där begär anden skulle uppstå om en körning hade för många underordnade
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
  + **azureml – förklara-modell**
    + Byter namn på AzureML-förklarar-Model-paket till AzureML-tolka, och behåller det gamla paketet för bakåtkompatibilitet för tillfället
    + fast `automl` bugg med obehandlade förklaringar har angetts till klassificerings uppgift i stället för regression som standard vid hämtning från ExplanationClient
    + Lägg till stöd för `ScoringExplainer` som ska skapas direkt med `MimicWrapper`
  + **azureml-pipeline-core**
    + Bättre prestanda för att skapa stora pipeliner
  + **azureml-train-core**
    + Stöd för TensorFlow 2,0 har lagts till i TensorFlow-uppskattningen
  + **azureml-träna-automl**
    + När du skapar ett [experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) objekt hämtas eller skapas experimentet i Azure Machine Learning-arbetsytan för körning av historik spårning. Experiment-ID: t och den arkiverade tiden fylls i experiment-objektet när det skapas. Exempel:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [Arkiv ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) och [återaktivera ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) är funktioner som kan anropas på ett experiment för att dölja och återställa experimentet som visas i UX eller som returneras som standard i ett anrop till List experiment. Om ett nytt experiment skapas med samma namn som ett arkiverat experiment, kan du byta namn på det arkiverade experimentet när du återaktiverar genom att skicka ett nytt namn. Det kan bara finnas ett aktivt experiment med ett angivet namn. Exempel:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Den statiska metod [listan ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) i experimentet kan ta ett namn filter och ViewType-filter. ViewType-värden är "ACTIVE_ONLY", "ARCHIVED_ONLY" och "alla". Exempel:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Stöd för användning av miljö för modell distribution och tjänst uppdatering.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Attributet show i klassen [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) stöder inte det valfria argumentet with_details längre. Attributet show visar bara data drifts-och data drifts bidrag i funktions kolumnerna.
    + DataDriftDetector-funktionen [get_output] https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) beteende ändringar:
      + Indataparametern start_time, end_time är valfria i stället för obligatorisk;
      + Genom att skriva in särskilda start_time och/eller end_time med en speciell run_id i samma anrop leder det till ett värde fel undantag eftersom de är ömsesidigt uteslutande.
      + Genom att skriva in en speciell start_time och/eller end_time returneras endast resultaten av schemalagda körningar.
      + Parametern daily_latest_only är föråldrad.
    + Stöd för hämtning av data uppsättningar-baserade datautdata.
  + **azureml – förklara-modell**
    + Byter namn på AzureML-förklara-Model-paket till AzureML-tolka, och behåller det gamla paketet för bakåtkompatibilitet.
    + fast AutoML-bugg med obehandlade förklaringar har angetts till klassificerings uppgift i stället för regression som standard vid hämtning från ExplanationClient.
    + Lägg till stöd för [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) som ska skapas direkt med MimicWrapper
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Bättre prestanda för att skapa stora pipeliner.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Stöd för TensorFlow 2,0 har lagts till i [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) -uppskattningen.
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
    + Tillagda granskade miljöer. Dessa miljöer har förkonfigurerats med bibliotek för vanliga Machine Learning-uppgifter och har för beretts och cachelagrats som Docker-avbildningar för snabbare körning. De visas som standard i [arbets ytans](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)lista över miljöer med prefixet "azureml".

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
    + Möjlighet att hämta SAS-URL till modellen i lagring via Model-objektet har lagts till. T. ex. modell. get_sas_url ()
    + Introducera `run.get_details()['datasets']` för att hämta data uppsättningar som är associerade med den skickade körningen
    + Lägg till API `Dataset.Tabular.from_json_lines_files` för att skapa en TabularDataset från JSON-filer. Om du vill veta mer om dessa tabell data i JSON-filer på TabularDataset kan du besöka https://aka.ms/azureml-data dokumentationen.
    + Ytterligare fält för VM-storlek (OS-disk, antal GPU: er) har lagts till i supported_vmsizes ()-funktionen
    + Ytterligare fält har lagts till i funktionen list_nodes () för att Visa körningen, den privata och den offentliga IP-adressen, porten osv.
    + Möjlighet att ange ett nytt fält under kluster etableringen – remotelogin_port_public_access som kan ställas in på aktive rad eller inaktive ras beroende på om du vill lämna SSH-porten öppen eller stängd vid tidpunkten för att skapa klustret. Om du inte anger det öppnas tjänsten Smartt, eller så stängs porten beroende på om du distribuerar klustret i ett virtuellt nätverk.
  + **azureml – förklara-modell**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Möjlighet att hämta SAS-URL till modellen i lagring via Model-objektet har lagts till. Ex: modell. [get_sas_url ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introducera körning. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)[' dataset '] för att hämta data uppsättningar som är associerade med den skickade körningen
    + Lägg till API `Dataset.Tabular` .[ from_json_lines_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) för att skapa en TabularDataset från JSON-filer. Om du vill veta mer om dessa tabell data i JSON-filer på TabularDataset kan du besöka https://aka.ms/azureml-data dokumentationen.
    + Ytterligare fält för VM-storlek (OS-disk, antal GPU: er) har lagts till i [supported_vmsizes ()-](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-) funktionen
    + Ytterligare fält har lagts till i funktionen [list_nodes ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) för att Visa körningen, den privata och den offentliga IP-adressen, porten osv.
    + Möjlighet att ange ett nytt fält under kluster [etableringen](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` som kan ställas in på aktive rad eller inaktive ras beroende på om du vill lämna SSH-porten öppen eller stängd vid tidpunkten för att skapa klustret. Om du inte anger det öppnas tjänsten Smartt, eller så stängs porten beroende på om du distribuerar klustret i ett virtuellt nätverk.
  + **azureml – förklara-modell**
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
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + En [antecknings bok](https://aka.ms/pl-modulestep) har lagts till för att beskriva [modulen](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)), [ModuleVersion och [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) har lagts till för R-skript som körs via AML-pipeline.
    + Parametrar för fast metadata parsas i [AzureBatchStep som orsakade fel meddelandet "tilldelningen av parametern SubscriptionId har inte angetts".
  + **[azureml-träna-automl](/python/api/azureml-train-automl-runtime/)**
    + Training_data validation_data label_column_name weight_column_name som indata.
    + Ett utfasnings meddelande har lagts till för [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) och [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK för python v-1.0.62

+ **Nya funktioner**
  + Introducerade `timeseries`  traiten på TabularDataset. Denna egenskap möjliggör enkel tidsstämpel-filtrering på data a TabularDataset, till exempel för att ta alla data mellan ett tidsintervall eller de senaste data. Om du vill veta mer om det här `timeseries`  TabularDataset kan du gå https://aka.ms/azureml-data till dokumentationen eller till https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb en exempel antecknings bok.
  + Träning med TabularDataset och FileDataset har Aktiver ATS. Besök https://aka.ms/dataset-tutorial ett exempel på en bärbar dator.

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
    + Korrigerade felet där `DataReference` namn ändras när `DataReference` läget ändras (t. ex. vid anrop `as_upload` , `as_download` eller `as_mount` ).
    + Gör `mount_point` och `target_path` valfritt för `FileDataset.mount` och `FileDataset.download` .
    + Det går inte att hitta tidsstämpel-kolumnen som ska utföras om Time-seriens relaterade API anropas utan en begränsad tidsstämpel-kolumn som är tilldelad eller kolumner som tilldelats tids stämpling utelämnas.
    + Tids serie kolumner ska tilldelas med en kolumn vars typ är datum, annars förväntas ett undantags fel
    + Tids serie kolumner för att tilldela API "with_timestamp_columns" kan ta inget värde med ett kolumn namn för tidsstämpel, vilket raderar tidigare tilldelade Timestamp-kolumner.
    + Undantag utlöstes när en grov kornig het eller en detaljerad tids stämplings kolumn ignoreras med indikation för att användaren som släpper kan utföras efter att ha tagit bort kolumnen tidsstämpel i släppa listan eller anropa with_time_stamp med inget värde för att frisläppa Timestamp-kolumner
    + Undantag utlöstes när en grov kornig het eller en detaljerad tids stämplings kolumn inte ingår i listan Behåll kolumner med indikation för den användare som håller på att göras efter att ha angett kolumnen tidsstämpel i kolumnen Behåll kolumn eller anropa with_time_stamp med inget värde för att frisläppa Timestamp-kolumner.
    + Loggning har lagts till för en registrerad modell storlek.
  + **azureml – förklara-modell**
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

         ```py
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
  + Introducerade FileDataset, som refererar till en eller flera filer i dina data lager eller offentliga URL: er. Filerna kan vara i valfritt format. FileDataset ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Om du vill veta mer om FileDataset kan du besöka https://aka.ms/file-dataset .
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
    + Presentera data uppsättning. get_all (arbets yta), som returnerar en ord lista `TabularDataset` och `FileDataset` objekt som har registrerats med registrerings namnet.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Presentera `parition_format` som argument till `Dataset.Tabular.from_delimited_files` och `Dataset.Tabular.from_parquet.files` . Partitionsinformation för varje data Sök väg extraheras till kolumner baserat på det angivna formatet. {column_name} skapar en sträng kolumn och {column_name: ÅÅÅÅ/MM/dd/HH/mm/SS} skapar kolumnen DateTime, där ÅÅÅÅ, MM, DD, HH, mm och SS används för att extrahera år, månad, dag, timme, minut och sekund för datum/tid-typen. Partition_format bör starta från positionen för den första partitionen tills fil Sök vägen är slut. Till exempel, med sökvägen ".. /USA/2019/01/01/data.csv "där partitionen är per land och tid, partition_format = '/{Country}/{PartitionDate: ÅÅÅÅ/MM/DD}/data.csv ' skapar sträng kolumnen land med värdet" USA "och datetime-kolumnen" PartitionDate "med värdet" 2019-01-01 ".
        ```py
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
  + **azureml – förklara-modell**
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
  + Aktive rad `TabularDataset` för användning av AutomatedML. Om du vill veta mer om `TabularDataset` kan du besöka https://aka.ms/azureml/howto/createdatasets .

+ **Fel korrigeringar och förbättringar**
  + **automl-client-core-nativeclient**
    + Korrigerade felet, utlöses när inlärnings-och/eller verifierings etiketter (y och y_valid) har angetts i formatet Pandas dataframe, men inte som numpy-matris.
    + Gränssnittet har uppdaterats för att skapa ett `RawDataContext` för att endast kräva data och `AutoMLBaseSettings` objektet.
    +  Tillåt AutoML-användare att släppa inlärnings serier som inte är tillräckligt långa vid prognoser. – Tillåt AutoML-användare att släppa kärnor från den test uppsättning som inte finns i träningen när du skapar prognoser.
  + **Azure-CLI-ml**
    + Nu kan du uppdatera TLS/SSL-certifikatet för poängsättnings slut punkten som distribueras i AKS-kluster både för Microsoft-genererade och kund certifikat.
  + **azureml-automl-core**
    + Ett problem har åtgärd ATS i AutoML där rader med etiketter som saknas har tagits bort felaktigt.
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
      + azureml. Core. Workspace. arbetsyte. get_default_keyvault ()
      + azureml. Core. nyckel valv. nyckel valv. set_secret (namn, värde)
      + azureml. Core. nyckel valv. nyckel valv. set_secrets (secrets_dict)
      + azureml. Core. nyckel valv. nyckel valv. get_secret (namn)
      + azureml. Core. nyckel valv. nyckel valv. get_secrets (secrets_list)
      + azureml. Core. nyckel valv. nyckel valv. list_secrets ()
    + Ytterligare metoder för att hämta standard nyckel valv och få hemligheter under fjärrkörning:
      + azureml. Core. Workspace. arbetsyte. get_default_keyvault ()
      + azureml. Core. Run. Run. get_secret (namn)
      + azureml. Core. Run. Run. get_secrets (secrets_list)
    + Ytterligare parametrar för åsidosättning har lagts till för Submit-HyperDrive CLI-kommandot.
    + Bättre tillförlitlighet för API-anrop för att expandera nya begär Anden biblioteks undantag.
    + Lägg till stöd för att skicka körningar från en skickad körning.
    + Ett problem med en SAS-token med fast förfallo tid i FileWatcher, som gjorde att filer slutade laddas upp efter att deras ursprungliga token hade upphört
    + Import av HTTP CSV/TSV-filer som stöds i data uppsättning python SDK.
    + Föråldrad-metoden arbetsyte. Setup (). Varnings meddelande som visas för användare som föreslår användning av Create () eller Get ()/from_config () i stället.
    + Miljön har lagts till. add_private_pip_wheel (), som möjliggör överföring av privata anpassade python-paket `whl` till arbets ytan och att använda dem på ett säkert sätt för att bygga/materialisera miljön.
    + Nu kan du uppdatera TLS/SSL-certifikatet för poängsättnings slut punkten som distribueras i AKS-kluster både för Microsoft-genererade och kund certifikat.
  + **azureml – förklara-modell**
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
    + Ett problem har åtgärd ATS i AutoML där rader med etiketter som saknas har tagits bort felaktigt.
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
  + Prestanda för `read_parquet` har förbättrats avsevärt vid körning i Spark.
  + Ett problem har åtgärd ATS där `column_type_builder` det inte gick att skapa en enskild kolumn med tvetydiga datum format.

### <a name="azure-portal"></a>Azure Portal
+ **Förhands gransknings funktion**
  + Logg-och utdatafilen är nu tillgängliga för att köra informations sidor. Filerna strömmar uppdateringar i real tid när växling vid förhands visning är aktive rad.
  + Möjligheten att ange kvot på en arbets ytans nivå släpps i för hands versionen. AmlCompute kvoter tilldelas på prenumerations nivå, men nu kan du distribuera den kvoten mellan arbets ytor och tilldela den till en rättvis delning och styrning. Klicka bara på bladet **användning och kvoter** i det vänstra navigerings fältet på arbets ytan och välj fliken **Konfigurera kvoter** . Observera att du måste vara prenumerations administratör för att kunna ange kvoter på arbets ytans nivå eftersom detta är en åtgärd över arbets ytan.

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
    + Ändringar i widget: installeras automatiskt med `contrib` , ingen mer `nbextension` installations-/aktiverings support förklaring med bara global funktions prioritet (t. ex. Permutative)
    + Instrument panels ändringar:-Box ritas och violin ritas, förutom att `beeswarm` Rita på sammanfattnings sidan – mycket snabbare åter givning av `beeswarm` observationer på skjutreglaget "Top-k"-ändring – användbart meddelande som förklarar hur översta-k är beräknade – användbara anpassningsbara meddelanden i stället för diagram när data inte anges
  + **azureml-core**
    + Metoden Model. Package () har lagts till för att skapa Docker-avbildningar och Dockerfiles som kapslar in modeller och deras beroenden.
    + Uppdaterade lokala webbtjänster för att acceptera InferenceConfigs som innehåller miljö objekt.
    + Fast Model. register () genererar ogiltiga modeller när "." (för den aktuella katalogen) skickas som model_path-parametern.
    + Lägg till kör. submit_child, speglar funktionen experiment. submit när du anger Kör som överordnad för den skickade underordnade körningen.
    + Stöd för konfigurations alternativ från modell. register i Run. register_model.
    + Möjlighet att köra JAR-jobb på ett befintligt kluster.
    + Stöder nu instance_pool_id-och cluster_log_dbfs_path parametrar.
    + Stöd har lagts till för användning av ett miljö objekt när en modell distribueras till en WebService. Environment-objektet kan nu anges som en del av InferenceConfig-objektet.
    + Lägg till appinsifht-mappning för nya regioner – västra-väst-usanorracentrala
    + Lade till dokumentation för alla attribut i alla data lager klasser.
    + Blob_cache_timeout parameter har lagts till `Datastore.register_azure_blob_container` .
    + Save_to_directory-och load_from_directory-metoder har lagts till i azureml. Core. Environment. Environment.
    + Har lagt till kommandona "AZ ml miljö hämtning" och "AZ ml-miljö registrera" i CLI.
    + Miljö. add_private_pip_wheel-metoden har lagts till.
  + **azureml – förklara-modell**
    + Data uppsättnings spårning har lagts till i förklaringar med data uppsättnings tjänsten (för hands version).
    + Minskad standard grupp storlek vid strömning av globala förklaringar från 10 000 till 100.
    + Om du lägger till model_task flagga i förklaringar för att låta användaren åsidosätta standard logiken för automatisk härledning för modell typ.
  + **azureml-mlflow**
    + Åtgärdat fel i mlflow. azureml. build_image där kapslade kataloger ignoreras.
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
  + Du kan nu begära att köra vissa kontroller (t. ex. histogram, punkt diagram osv.) i vissa kolumner.
  + Ett parallellisera-argument har lagts till i `append_columns` . Om värdet är true kommer data att läsas in i minnet, men körningen körs parallellt. om det här värdet är False kommer körningen att strömmas, men en enkel tråd.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK för python v-1.0.53

+ **Nya funktioner**
  + Automatiserad Machine Learning stöder nu utbildning av ONNX-modeller på fjärrdatorns mål
  + Azure Machine Learning kan nu återuppta utbildningen från en tidigare körnings-, kontroll punkts-eller modell fil.
    + Lär dig hur du [använder uppskattningar för att återuppta inlärningen från en tidigare körning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/training/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Fel korrigeringar och förbättringar**
  + **automl-client-core-nativeclient**
    + Åtgärda felet om återfick kolumn typer efter transformeringen (fel länkad);
    + Tillåt y_query vara en objekt typ som inte innehåller några (s) i början (#459519).
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
    + I prognos uppgifter `target_lags` accepterar-parametern nu ett heltals värde eller en lista med heltal. Om heltalet angavs skapas bara en fördröjning. Om en lista anges kommer de unika värdena för lags att tas. target_lags = [1, 2, 2, 4] kommer att skapa lags av en, 2 och 4 perioder.
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
    + Förbättrad arbets yta skapa fel undantag. Så att användarna inte ser "det går inte att skapa arbets ytan. Det gick inte att hitta... " som meddelandet och se i stället det faktiska fel meddelandet.
    + Lägg till stöd för token-autentisering i AKS-webbtjänster.
    + Lägg till `get_token()` metod till `Webservice` objekt.
    + CLI-stöd har lagts till för hantering av Machine Learning-datauppsättningar.
    + `Datastore.register_azure_blob_container` Om du vill kan du `blob_cache_timeout` använda ett värde (i sekunder) som konfigurerar blobfuses monterings parametrar för att aktivera förfallo tid för cacheminnet för det här data lagret. Standardvärdet är ingen tids gräns, d.v.s. När en BLOB läses, kommer den att stanna kvar i det lokala cacheminnet tills jobbet är klart. De flesta jobb föredrar den här inställningen, men vissa jobb behöver läsa mer data från en stor data uppsättning än vad som får plats på deras noder. För dessa jobb kan du justera den här parametern för att lyckas. Var försiktig när du justerar den här parametern: om du ställer in värdet för lågt kan det leda till dåliga prestanda, eftersom data som används i en epok går ut innan de används igen. Det innebär att alla läsningar görs från Blob Storage (dvs. nätverket) i stället för det lokala cacheminnet, vilket påverkar inlärnings tiderna negativt.
    + Modell beskrivningen kan nu uppdateras korrekt efter registreringen
    + Modell-och bild borttagning innehåller nu mer information om överordnade objekt som är beroende av dem, vilket gör att borttagningen Miss fungerar
    + Förbättra resursutnyttjande för fjärrkörningar med hjälp av azureml. mlflow.
  + **azureml – förklara-modell**
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
    + Förbättra prestanda för mlflow. set_experiment (experiment_name)
    + Åtgärda fel vid användning av InteractiveLoginAuthentication för mlflow tracking_uri
    + Förbättra resursutnyttjande för fjärrkörningar med hjälp av azureml. mlflow.
    + Förbättra dokumentationen för azureml-mlflow-paketet
    + Korrigerings fel där mlflow. log_artifacts ("my_dir") sparar artefakter under "my_dir/<artefakt sökvägar>" i stället för "<artefakt-sökvägar>"
  + **azureml-opendatasets**
    + PIN-kod `pyarrow` `opendatasets` till gamla versioner (<0.14.0) på grund av ett minnes problem som nyligen har introducerats där.
    + Flytta azureml-contrib-opendataset till azureml-opendataset.
    + Tillåt att öppna data uppsättnings klasser registreras på Azure Machine Learning arbets yta och utnyttja funktionerna för AML-datauppsättning sömlöst.
    + Förbättra NoaaIsdWeather-prestanda i en icke-SPARK-version avsevärt.
  + **azureml-pipeline – steg**
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
  + **azureml – förklara-modell**
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
    + Ett problem har åtgärd ATS där mlflow. log_artifacts ("my_dir") sparar artefakter under "my_dir/Artifact-Paths" i stället för "artefakt Sök vägar".
  + **azureml-pipeline-core**
    + Parametern hash_paths för alla steg i pipeline är föråldrad och kommer att tas bort i framtiden. Som standard är innehållet i source_directory hash-kodat (förutom filer som anges i. amlignore eller. gitignore)
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
  + Distribuera behållaren till en [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) server enhet
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

+ **Git-referens spårning**<br/> Kunder har begärt grundläggande funktioner för git-integrering under en viss tid eftersom det hjälper till att upprätthålla en gransknings historik från slut punkt till slut punkt. Vi har implementerat spårning över större entiteter i Azure ML för git-relaterade metadata (lagrings platsen, commit, Clean State). Den här informationen samlas in automatiskt av SDK och CLI.

+ **Modell profilering & validerings tjänst**<br/> Kunderna har ofta svårt att på rätt sätt anpassa den beräkning som är associerad med deras härlednings tjänst. Med vår modell profilerings tjänst kan kunden tillhandahålla exempel indata och vi ska profilera över 16 olika processor-/minnes konfigurationer för att fastställa optimal storlek för distribution.

+ **Ta med din egen bas avbildning för att få en härledning**<br/> En annan vanlig klagomål var svårt att flytta från experimentering till att dela om beroenden. Med vår nya bas avbildnings delnings funktion kan du nu återanvända dina bas avbildningar, beroenden och alla, för att kunna hantera störningar. Detta bör påskynda distributionen och minska mellanrummet från insidan till den yttre slingan.

+ **Förbättrad generering av Swagger-scheman**<br/> Vår tidigare generations Swagger-metod var fel känslig och omöjlig att automatisera. Vi har ett nytt infogat sätt att generera Swagger-scheman från valfri python-funktion via dekoratörer. Vi har öppen källkod för den här koden och vårt schema generations protokoll är inte kopplat till Azure ML-plattformen.

+ **Azure ML CLI är allmänt tillgänglig (GA)**<br/> Modeller kan nu distribueras med ett enda CLI-kommando. Vi har en gemensam feedback från kunden att ingen distribuerar en ML-modell från en Jupyter Notebook. [**Referens dokumentationen för cli**](https://aka.ms/azmlcli) har uppdaterats.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK för python v-1.0.30 har släppts.

[`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py)Introducerades för att lägga till en ny version av en publicerad pipeline samtidigt som du behåller samma slut punkt.

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
  + Azure Machine Learning DNN-uppskattningar har nu inbyggt stöd för flera versioner. Till exempel `TensorFlow`   kan en uppskattning nu acceptera en `framework_version` parameter och användarna kan ange version 1,10 eller 1,12. Om du vill ha en lista över de versioner som stöds av din aktuella SDK-version, kan `get_supported_versions()` du anropa den önskade Ramverks klassen (till exempel `TensorFlow.get_supported_versions()` ).
  En lista över de versioner som stöds av den senaste SDK-versionen finns i [dokumentationen för DNN-uppskattning](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK för python v-1.0.21

+ **Nya funktioner**
  + Metoden *azureml. Core. Run. create_children* gör det möjligt att skapa flera underordnade körningar under låg latens med ett enda anrop.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK för python v-1.0.18

 + **Ändringar**
   + Azureml-tensorboard-paketet ersätter azureml-contrib-tensorboard.
   + I den här versionen kan du konfigurera ett användar konto på ditt hanterade beräknings kluster (amlcompute) medan du skapar det. Detta kan göras genom att de här egenskaperna skickas i konfigurationen. Du hittar mer information i [referens dokumentationen för SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

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

  + Azure Machine Learning har nu det första klass stödet för populär DNN Framework-kedja. Användning av [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) klass användare kan enkelt träna och distribuera kedje modeller.
    + Lär dig hur du [Kör distribuerad utbildning med ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/training/distributed-chainer/distributed-chainer.ipynb)
    + Lär dig hur du kör en inställning för min [parameter med en kedja med HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning pipelines har lagt till möjlighet att utlösa en pipeline-körning baserat på data lager ändringar. [Antecknings boken](https://aka.ms/pl-schedule) för pipeline-schemat uppdateras för att visa den här funktionen.

+ **Fel korrigeringar och förbättringar**
  + Vi har lagt till stöd i Azure Machine Learning pipelines för att ställa in source_directory_data_store-egenskapen till ett önskat data lager (till exempel en blob-lagring) på [RunConfigurations](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) som anges i [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Som standard använder Azure File Store som lagrings data lager, som kan köra begränsnings problem när ett stort antal steg körs samtidigt.

### <a name="azure-portal"></a>Azure Portal

+ **Nya funktioner**
  + Ny dra och släpp tabell redigerings miljö för rapporter. Användare kan dra en kolumn från en källa till tabell området där en förhands granskning av tabellen kommer att visas. Kolumnerna kan ordnas om.
  + Nya loggar fil visare
  + Länkar till experiment körningar, Compute, modeller, avbildningar och distributioner från fliken aktiviteter

## <a name="next-steps"></a>Nästa steg

Läs översikten för [Azure Machine Learning](overview-what-is-azure-ml.md).
