---
title: Kända problem & fel sökning
titleSuffix: Azure Machine Learning
description: Få hjälp med att hitta och korrigera fel eller fel i Azure Machine Learning. Lär dig om kända problem, fel sökning och lösningar.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, contperf-fy20q4
ms.date: 11/09/2020
ms.openlocfilehash: e383ac260a67c7334b806612325ed0b6a9fbbef9
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030985"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Kända problem och felsökning i Azure Machine Learning

Den här artikeln hjälper dig att felsöka kända problem som kan uppstå när du använder Azure Machine Learning. 

Mer information om fel sökning finns i [Nästa steg](#next-steps) i slutet av den här artikeln.

> [!TIP]
> Fel eller andra problem kan vara resultatet av [resurs kvoter](how-to-manage-quotas.md) som du stöter på när du arbetar med Azure Machine Learning. 

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

Ibland kan det vara bra om du kan ange diagnostikinformation när du ber om hjälp. Så här visar du några loggar: 
1. Besök [Azure Machine Learning Studio](https://ml.azure.com). 
1. Välj **experiment** på vänster sida 
1. Välj ett experiment.
1. Välj en körning.
1. Välj **utdata + loggar** överst.

> [!NOTE]
> Azure Machine Learning loggar information från en rad olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör övnings jobbet. Många av dessa loggar dokumenteras inte. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.


## <a name="installation-and-import"></a>Installation och import
                           
* **PIP-installation: beroenden är inte garanterat konsekventa med en installation med en rad:** 

   Detta är en känd begränsning i PIP, eftersom den inte har en fungerande beroende lösare när du installerar som en enskild rad. Det första unika beroendet är det enda som det ser ut. 

   I följande kod `azureml-datadrift` och `azureml-train-automl` båda installeras med en enda rad pip-installation. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   I det här exemplet måste vi säga att `azureml-datadrift` version > 1,0 och `azureml-train-automl` kräver version < 1,2. Om den senaste versionen av `azureml-datadrift` är 1,3 uppgraderas båda paketen till 1,3, oavsett `azureml-train-automl` paket kravet för en äldre version. 

   För att se till att rätt versioner är installerade för dina paket kan du installera med flera rader som i följande kod. Ordningen är inte ett problem här eftersom pip degraderas explicit som en del av nästa rad anrop. Och därmed tillämpas rätt versions beroenden.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **Förklarings paketet är inte garanterat att installeras när du installerar azureml-träna-automl-client:** 
   
   När du kör en fjärran sluten AutoML med modell förklaring aktive rad visas ett fel meddelande "installera azureml-deklarning-Model-paket för modell förklaringar". Detta är ett känt problem. Som en lösning följer du ett av stegen nedan:
  
  1. Installera azureml – förklara-Model lokalt.
   ```
      pip install azureml-explain-model
   ```
  2. Inaktivera funktionen för välklare ring helt genom att skicka model_explainability = falskt i AutoML-konfigurationen.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Panda-fel: visas vanligt vis under AutoML experiment:**
   
   När du konfigurerar din miljö manuellt med hjälp av pip kan du lägga märke till felaktiga attribut (särskilt från Pandas) på grund av att paket versioner som inte stöds installeras. För att förhindra sådana fel måste du [Installera AUTOML SDK med hjälp av automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Öppna en Anaconda-prompt och klona GitHub-lagringsplatsen för en uppsättning exempel antecknings böcker.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. CD till mappen "How-to-use-azureml/automatisk-Machine-Learning där exempel antecknings böckerna extraherades och kör sedan:
    
    ```bash
    automl_setup
    ```
    
* **Fel: "varumärke" när du kör AutoML i det lokala beräknings-eller Azure Databrickss klustret**

    Om en ny miljö skapades efter 10 juni 2020, med hjälp av SDK-1.7.0 eller tidigare, kan det hända att träningen Miss känner till felet på grund av en uppdatering i py-cpuinfo-paketet. (Miljöer som skapats den 10 juni 2020 påverkas inte, eftersom experiment körs på fjärrberäkning eftersom de cachelagrade inlärnings avbildningarna används.) Undvik det här problemet genom att göra något av följande två steg:
    
    * Uppdatera SDK-versionen till 1.8.0 eller senare (detta degraderar också py-cpuinfo till 5.0.0):
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Nedgradera den installerade versionen av py-cpuinfo till 5.0.0:
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Fel meddelande: det går inte att avinstallera ' PyYAML '**

    Azure Machine Learning SDK för python: PyYAML är ett `distutils` installerat projekt. Därför kan vi inte korrekt avgöra vilka filer som tillhör den om det finns en delvis avinstallation. Om du vill fortsätta att installera SDK och ignorera det här felet använder du:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Det gick inte att installera Azure Machine Learning SDK med ett undantag: ModuleNotFoundError: ingen modul med namnet ' ruamel ' eller ' ImportError: ingen modul med namnet ruamel. yaml '**
   
   Det här problemet uppstår vid installationen av Azure Machine Learning SDK för python på den senaste pip-versionen (>20.1.1) i bas miljön för Conda för alla versioner av Azure Machine Learning SDK för python. Se följande lösningar:

    * Undvik att installera python SDK i Conda-bas miljön, i stället för att skapa din Conda-miljö och installera SDK på den nyligen skapade användar miljön. Den senaste pip-versionen bör fungera på den nya Conda-miljön.

    * För att skapa avbildningar i Docker, där du inte kan växla bort från Conda-bas miljön, måste du fästa pip<= 20.1.1 i Docker-filen.

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **Databricks-problem vid installation av paket**

    Azure Machine Learning SDK-installationen Miss lyckas på Azure Databricks när fler paket är installerade. Vissa paket, till exempel `psutil` , kan orsaka konflikter. Undvik installations fel genom att installera paket genom att frysa biblioteks versionen. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning SDK. Du kan också uppleva det här problemet med andra bibliotek. Exempel:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Du kan också använda init-skript om du behåller problem med att installera mot python-bibliotek. Den här metoden stöds inte officiellt. Mer information finns i [kluster omfång init-skript](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Import fel i Databricks: det går inte att importera namnet `Timedelta` från `pandas._libs.tslibs`**: om du ser det här felet när du använder automatisk maskin inlärning, kör du två följande rader i din bärbara dator:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Import fel i Databricks: det finns ingen modul med namnet ' Pandas. Core. index '**: om du ser det här felet när du använder automatisk maskin inlärning:

    1. Kör det här kommandot för att installera två paket i Azure Databricks klustret:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Koppla från och återanslut sedan klustret till din bärbara dator.
    
    Om de här stegen inte löser problemet kan du försöka med att starta om klustret.

* **Databricks FailToSendFeather**: om du ser ett `FailToSendFeather` fel när du läser data på Azure Databricks kluster kan du läsa följande lösningar:
    
    * Uppgradera `azureml-sdk[automl]` paketet till den senaste versionen.
    * Lägg till `azureml-dataprep` version 1.1.8 eller senare.
    * Lägg till `pyarrow` version 0,11 eller senare.
    
## <a name="create-and-manage-workspaces"></a>Skapa och hantera arbets ytor

> [!WARNING]
> Det finns inte stöd för att flytta Azure Machine Learning arbets ytan till en annan prenumeration eller flytta den ägande prenumerationen till en ny klient. Detta kan orsaka fel.

* **Azure Portal**: 
  * Om du går direkt till din arbets yta från en resurs länk från SDK eller Azure Portal kan du inte Visa standard **översikts** sidan med prenumerations information i tillägget. I det här scenariot kan du inte heller byta till en annan arbets yta. Om du vill visa en annan arbets yta går du direkt till [Azure Machine Learning Studio](https://ml.azure.com) och söker efter namnet på arbets ytan.
  * Alla till gångar (data uppsättningar, experiment, beräkningar och så vidare) är bara tillgängliga i [Azure Machine Learning Studio](https://ml.azure.com). De är *inte* tillgängliga från Azure Portal.

* **Webbläsare som stöds i Azure Machine Learning Studio-webb portalen**: Vi rekommenderar att du använder den senaste webbläsaren som är kompatibel med operativ systemet. Följande webbläsare stöds:
  * Microsoft Edge (den nya Microsoft Edge, senaste versionen. Inte Microsoft Edge Legacy)
  * Safari (senaste versionen, endast Mac)
  * Chrome (senaste versionen)
  * Firefox (senaste versionen)

## <a name="set-up-your-environment"></a>Konfigurera din miljö

* **Problem med att skapa AmlCompute**: det är en sällsynt risk att vissa användare som har skapat sin Azure Machine Learning-arbetsyta från Azure Portal innan ga-versionen kanske inte kan skapa AmlCompute på arbets ytan. Du kan antingen utlösa en supportbegäran mot tjänsten eller skapa en ny arbets yta via portalen eller SDK för att häva blockeringen direkt.

* **Azure Container Registry stöder för närvarande inte Unicode-tecken i resurs grupp namn**: det är möjligt att ACR-begärandena inte kan utföras eftersom resurs gruppens namn innehåller Unicode-tecken. För att undvika det här problemet rekommenderar vi att du skapar en ACR i en resurs grupp med en annan resurs.

## <a name="work-with-data"></a>Arbeta med data

### <a name="overloaded-azurefile-storage"></a>Överlagrad AzureFile-lagring

Använd följande lösningar om du får ett fel meddelande `Unable to upload project files to working directory in AzureFile because the storage is overloaded` .

Om du använder fil resurs för andra arbets belastningar, till exempel data överföring, är rekommendationen att använda blobbar så att fil resursen är kostnads fri att användas för att skicka körningar. Du kan också dela upp arbets belastningen mellan två olika arbets ytor.

### <a name="passing-data-as-input"></a>Skicka data som indata

*  **TypeError: FileNotFound: det finns ingen sådan fil eller katalog**: det här felet uppstår om fil Sök vägen som du anger inte är den plats där filen finns. Du måste kontrol lera att det sätt som du refererar till filen är konsekvent med var du monterade data uppsättningen på beräknings målet. För att säkerställa ett deterministiskt tillstånd rekommenderar vi att du använder den abstrakta sökvägen när du monterar en data uppsättning till ett beräknings mål. I följande kod monterar du till exempel data uppsättningen under roten i fil systemet för beräknings målet `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Om du inte tar med det inledande snedstrecket "/" måste du ange prefix för arbets katalogen t. ex. `/mnt/batch/.../tmp/dataset` på beräknings målet för att ange var du vill att data uppsättningen ska monteras.

### <a name="mount-dataset"></a>Montera data uppsättning
* **Det gick inte att initiera data uppsättningen: väntar på att en tids gräns för monterings punkten skulle bli klar**: 
  * Om du inte har några utgående regler för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview) och använder `azureml-sdk>=1.12.0` , uppdatera `azureml-dataset-runtime` och det är beroenden som de senaste för den speciella del versionen, eller om du använder den i en körning, måste du återskapa din miljö så att den kan ha den senaste korrigeringen med korrigeringen. 
  * Om du använder `azureml-sdk<1.12.0` uppgraderar du till den senaste versionen.
  * Om du har utgående NSG-regler kontrollerar du att det finns en utgående regel som tillåter all trafik för tjänst tag gen `AzureResourceMonitor` .

### <a name="data-labeling-projects"></a>Data märknings projekt

|Problem  |Lösning  |
|---------|---------|
|Det går bara att använda data uppsättningar som skapats på BLOB-datalager.     |  Detta är en känd begränsning i den aktuella versionen.       |
|När projektet har skapats visar projektet "initiera" under en längre tid.     | Uppdatera sidan manuellt. Initieringen bör fortsätta ungefär 20 Datapoints per sekund. Bristen på Autouppdatering är ett känt problem.         |
|När du ska granska bilder visas inte nyligen märkta bilder.     |   Om du vill läsa in alla märkta bilder väljer du den **första** knappen. Den **första** knappen tar dig tillbaka till början av listan, men läser in alla etiketterade data.      |
|Tryck på ESC-tangenten när etiketter för objekt identifiering skapar en etikett för noll storlek i det övre vänstra hörnet. Det går inte att skicka etiketter i det här läget.     |   Ta bort etiketten genom att klicka på kryss rutan bredvid det.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Data riktnings övervakare

Begränsningar och kända problem för data avvikelse Övervakare:

* Tidsintervallet vid analys av historiska data är begränsat till 31 intervall av övervaknings frekvens inställningen. 
* Begränsning av 200 funktioner, om inte en funktions lista inte har angetts (alla funktioner används).
* Beräknings storleken måste vara tillräckligt stor för att data ska kunna hanteras.
* Se till att data uppsättningen har data inom start-och slutdatum för en specifik övervaknings körning.
* Data uppsättnings övervakare fungerar bara på data uppsättningar som innehåller 50 rader eller mer.
* Kolumner eller funktioner i data uppsättningen klassificeras som kategoriska eller numeriska baserat på villkoren i följande tabell. Om funktionen inte uppfyller dessa villkor, t. ex. en kolumn av typen String med >100 unika värden, släpps funktionen från vår algoritm för data avvikelser, men är fortfarande profilerad. 

    | Funktions typ | Datatyp | Villkor | Begränsningar | 
    | ------------ | --------- | --------- | ----------- |
    | Kategoriska | sträng, bool, int, Float | Antalet unika värden i funktionen är mindre än 100 och mindre än 5% av antalet rader. | Null behandlas som sin egen kategori. | 
    | Numeriska | int, Float | Värdena i funktionen är av en numerisk datatyp och uppfyller inte villkoret för en kategoriska-funktion. | Funktionen utelämnas om >15% av värdena är null. | 

* När du har [skapat en data riktnings övervakare](how-to-monitor-datasets.md) men inte kan se data på sidan för **data uppsättnings övervakning** i Azure Machine Learning Studio kan du prova följande.

    1. Kontrol lera om du har valt rätt datum intervall överst på sidan.  
    1. På fliken **data uppsättnings övervakning** väljer du länken experimentera för att kontrol lera körnings statusen.  Den här länken är längst till höger i tabellen.
    1. Om körningen har slutförts kontrollerar du driv rutins loggarna för att se hur många mått som har genererats eller om det finns några varnings meddelanden.  Hitta driv rutins loggar på fliken **utdata + loggar** när du har klickat på ett experiment.

* Om SDK `backfill()` -funktionen inte genererar förväntade utdata kan det bero på ett autentiseringsfel.  När du skapar beräkningen för att skicka in den här funktionen ska du inte använda `Run.get_context().experiment.workspace.compute_targets` .  Använd i stället [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) , till exempel följande för att skapa den beräkning som du skickar till den `backfill()` funktionen: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Azure Machine Learning Designer

### <a name="dataset-visualization-in-the-designer"></a>Data uppsättnings visualisering i designern

När du har registrerat en data uppsättning på till gångs sidan för **data uppsättningar** eller med SDK kan du hitta den under **data uppsättnings** kategorin i listan till design ytan.

Men när du drar data uppsättningen till arbets ytan och visualiseringen kan det hända att det inte går att visualisera på grund av följande orsaker:

- För närvarande kan du endast visualisera tabell data uppsättning i designern. Om du registrerar en fil data uppsättning utanför designern kan du inte visualisera den i designer-arbetsytan.
- Din data uppsättning lagras i ett virtuellt nätverk (VNet). Om du vill visualisera måste du aktivera arbets ytans hanterade identitet för data lagret.
    1. Gå till det relaterade data lagret och klicka på uppdatera **autentiseringsuppgifter** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Uppdatera"::: autentiseringsuppgifter
    1. Välj **Ja** för att aktivera hanterad identitet för arbets ytan.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Aktivera hanterad identitet för arbets yta":::

### <a name="long-compute-preparation-time"></a>Förberedelse tid för lång beräkning

Det kan vara några minuter eller till och med längre första gången du ansluter till eller skapar ett beräknings mål. 

Från modell data insamlaren kan det ta upp till (men vanligt vis mindre än) 10 minuter för data att komma in på ditt Blob Storage-konto. Vänta 10 minuter för att se till att cellerna nedan kommer att köras.

```python
import time
time.sleep(600)
```

### <a name="log-for-real-time-endpoints"></a>Logg för slut punkter i real tid

Loggar över real tids slut punkter är kund information. För fel sökning av slut punkter i real tid kan du använda följande kod för att aktivera loggar. 

Se mer information om övervakning av webb tjänst slut punkter i [den här artikeln](./how-to-enable-app-insights.md#query-logs-for-deployed-models).

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
Om du har flera klient organisationer kan du behöva lägga till följande autentiserings kod innan `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>Inlärningsmodeller

* **ModuleErrors (ingen modul med namnet)**: om du kör i ModuleErrors när du skickar experiment i Azure ml, innebär det att utbildnings skriptet förväntar sig att ett paket ska installeras men inte läggs till. När du har angett paket namnet installerar Azure ML paketet i den miljö som används för att köra din utbildning. 

    Om du använder uppskattningar för att skicka experiment kan du ange ett paket namn via `pip_packages` eller `conda_packages` parameter i uppskattningen baserat på från vilken källa du vill installera paketet. Du kan också ange en YML-fil med alla dina beroenden med `conda_dependencies_file` eller lista alla dina pip-krav i en txt-fil med hjälp av `pip_requirements_file` parametern. Om du har ett eget Azure ML-miljö objekt som du vill åsidosätta standard avbildningen som används av uppskattaren kan du ange den miljön via- `environment` parametern för den uppskattade konstruktorn.

    Azure ML tillhandahåller också branschspecifika uppskattningar för TensorFlow, PyTorch, Kedjorer och SKLearn. Genom att använda dessa uppskattningar ser du till att de viktigaste Ramverks beroendena är installerade för din räkning i miljön som används för utbildning. Du kan välja att ange extra beroenden enligt beskrivningen ovan. 
 
    Azure ML-underhållna Docker-avbildningar och deras innehåll kan visas i [azureml-behållare](https://github.com/Azure/AzureML-Containers).
    De Ramverks-/regionsspecifika beroendena visas i respektive Framework-dokumentation – [kedjar](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks).

    > [!Note]
    > Om du tror att ett visst paket är tillräckligt vanligt för att läggas till i Azure ML-underhållna bilder och miljöer kan du generera ett GitHub-problem i [azureml-behållare](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (namn har inte definierats), AttributeError (objektet har inget attribut)**: det här undantaget bör komma från dina utbildnings skript. Du kan titta på loggfilerna från Azure Portal för att få mer information om det angivna namnet inte är definierat eller ett attributvärde. Från SDK kan du använda `run.get_details()` för att titta på fel meddelandet. Detta visar även alla loggfiler som genererats för din körning. Se till att ta en titt på ditt utbildnings skript och åtgärda felet innan du skickar om körningen. 

* **Horovod har stängts av**: i de flesta fall, om du stöter på "AbortedError: Horovod har stängts av" det här undantaget innebär det ett underliggande undantag i en av de processer som orsakade att Horovod stängdes. Varje rang i MPI-jobbet hämtar en egen dedikerad loggfilen i Azure ML. De här loggarna heter `70_driver_logs`. Vid distribuerad träning får loggnamnen suffixet `_rank` så att du enklare kan se skillnad på loggarna. Om du vill hitta det exakta fel som orsakade Horovod stänger du igenom alla loggfiler och letar efter `Traceback` i slutet av driver_log-filerna. Med en av de här filerna får du det faktiska underliggande undantaget. 

* **Körning eller experimentering**: experiment kan arkiveras med hjälp av metoden [experiment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) eller från fliken experiment i Azure Machine Learning Studio-klienten via "arkivera experiment"-knappen. Den här åtgärden döljer experimentet från List frågor och vyer, men tar inte bort den.

    Permanent borttagning av enskilda experiment eller körningar stöds för närvarande inte. Mer information om hur du tar bort arbets ytans till gångar finns i [Exportera eller ta bort data för Machine Learning service-arbetsytan](how-to-export-delete-data.md).

* **Mått dokumentet är för stort**: Azure Machine Learning har interna gränser för mått objekts storlek som kan loggas samtidigt från en utbildnings körning. Om du ser felet ”Måttdokumentet är för stort” när ett mått med listor som värdetyp kan du prova att dela upp listan i mindre segment, till exempel:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internt sammanfogar Azure ML blocken med samma måttnamn i en sammanhängande lista.

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

* **Den senaste uppgraderingen av AutoML-beroenden till nyare versioner kommer att vara en ny kompatibilitet**: från och med version 1.13.0 av SDK, läses inte modeller in i äldre SDK: er på grund av inkompatibilitet mellan de äldre versioner som vi har fäst i våra tidigare paket och de nyare versionerna som vi fäster nu. Du får ett fel meddelande som:
  * Modulen hittades inte: t. ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Import fel: t. ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Fel i attribut: t. ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Undvik det här problemet genom att göra något av följande två steg beroende på din AutoML SDK-utbildnings version:
  1. Om din version av AutoML SDK-utbildningen är större än 1.13.0, behöver du `pandas == 0.25.1` och `sckit-learn==0.22.1` . Om det finns en versions konflikt uppgraderar du scikit-lära och/eller Pandas för att korrigera versionen enligt nedan:
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. Om din version av AutoML SDK-utbildningen är mindre än eller lika med 1.12.0, behöver du `pandas == 0.23.4` och `sckit-learn==0.20.3` . Om det finns en versions konflikt kan du nedgradera scikit – lära och/eller Pandas för att korrigera versionen enligt nedan:
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```
 
* **Prognosticering R2-poängen är alltid noll**: det här problemet uppstår om de angivna tränings data har tids serier som innehåller samma värde för de sista `n_cv_splits`  +  `forecasting_horizon` data punkterna. Om det här mönstret förväntas i din tids serie kan du växla det primära måttet till normaliserat rot genomsnitts fel i roten.
 
* **TensorFlow**: från och med version 1.5.0 av SDK installerar automatiserad Machine Learning inte TensorFlow-modeller som standard. Installera TensorFlow och Använd det med dina automatiserade ML-experiment genom att installera TensorFlow = = 1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Experiment diagram**: binära klassificerings diagram (precision-återkallande, Roc, kurva osv.) som visas i automatiserade ml experiment-iterationer återges inte korrekt i användar gränssnittet sedan 4/12. Diagram observationer visar för närvarande inversa resultat, där modeller med bättre prestanda visas med lägre resultat. En lösning är under undersökning.

* **Databricks avbryter en automatiserad maskin inlärnings körning**: när du använder automatiserade funktioner för maskin inlärning på Azure Databricks, för att avbryta en körning och starta en ny experiment körning, startar du om Azure Databricks klustret.

* **Databricks >10 iterationer för automatisk maskin inlärning**: i automatiserade inställningar för maskin inlärning, om du har fler än 10 iterationer, anger du `show_output` `False` när du skickar in körningen.

* **Databricks-widget för Azure Machine Learning SDK och automatisk maskin inlärning**: widgeten Azure Machine Learning SDK stöds inte i en Databricks Notebook eftersom antecknings böckerna inte kan parsa HTML-widgetar. Du kan visa widgeten i portalen genom att använda den här python-koden i din Azure Databricks Notebook-cell:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup Miss lyckas**: 
    * I Windows kör du automl_setup från en Anaconda-prompt. Använd den här länken för att [Installera Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Se till att Conda 64-bit är installerad, i stället för 32-bitars genom att köra `conda info` kommandot. `platform`Ska vara `win-64` för Windows eller `osx-64` Mac.
    * Se till att Conda 4.4.10 eller senare är installerat. Du kan kontrol lera versionen med kommandot `conda -V` . Om du har installerat en tidigare version kan du uppdatera den med hjälp av kommandot: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Om `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` felet uppstår installerar du build Essentials med hjälp av kommandot `sudo apt-get install build-essential` .
      * Skicka ett nytt namn som den första parametern till automl_setup för att skapa en ny Conda-miljö. Visa befintliga Conda-miljöer med `conda env list` och ta bort dem med `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh Miss lyckas**: om automl_setup_linus. sh Miss lyckas på Ubuntu Linux med felet: `unable to execute 'gcc': No such file or directory`-
  1. Se till att de utgående portarna 53 och 80 är aktiverade. På en virtuell Azure-dator kan du göra detta från Azure Portal genom att välja den virtuella datorn och klicka på nätverk.
  2. Kör kommandot: `sudo apt-get update`
  3. Kör kommandot: `sudo apt-get install build-essential --fix-missing`
  4. Kör `automl_setup_linux.sh` igen

* **konfiguration. ipynb Miss lyckas**:
  * För lokala Conda kontrollerar du först att automl_setup har körts.
  * Kontrol lera att subscription_id är korrekt. Hitta subscription_id i Azure Portal genom att välja alla tjänster och sedan prenumerationer. Tecknen "<" och ">" ska inte tas med i subscription_id svärdet. Har till exempel `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` det giltiga formatet.
  * Se till att deltagar-eller ägar åtkomst till prenumerationen.
  * Kontrol lera att regionen är en av de regioner som stöds:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` `southcentralus` .
  * Se till att du har åtkomst till regionen med hjälp av Azure Portal.
  
* **import AutoMLConfig Miss lyckas**: paket ändringar i den automatiserade Machine Learning-versionen 1.0.76, som kräver att den tidigare versionen avinstallerades innan du uppdaterar till den nya versionen. Om `ImportError: cannot import name AutoMLConfig` felet inträffar efter att du har uppgraderat från en SDK-version före v-1.0.76 till v 1.0.76 eller senare, löser du felet genom att köra: `pip uninstall azureml-train automl` och sedan `pip install azureml-train-auotml` . Skriptet automl_setup. cmd gör detta automatiskt. 

* **Workspace.from_config Miss lyckas**: om anropen ws = Workspace.from_config () Miss lyckas –
  1. Se till att konfigurationen. ipynb Notebook har körts.
  2. Om antecknings boken körs från en mapp som inte finns under den mapp där `configuration.ipynb` kördes, kopierar du mappen aml_config och filen config.jsden innehåller till den nya mappen. Workspace.from_config läser config.jsför mappen Notebook eller den överordnade mappen.
  3. Om en ny prenumeration, resurs grupp, arbets yta eller region används, se till att du kör `configuration.ipynb` antecknings boken igen. Att ändra config.jsdirekt fungerar bara om arbets ytan redan finns i den angivna resurs gruppen under den angivna prenumerationen.
  4. Ändra arbets ytan, resurs gruppen eller prenumerationen om du vill ändra region. `Workspace.create` kommer inte att skapa eller uppdatera en arbets yta om den redan finns, även om den angivna regionen är annorlunda.
  
* **Exempel på antecknings bok Miss lyckas**: om en exempel antecknings bok Miss lyckas med ett fel där egenskapen, metoden eller biblioteket inte finns:
  * Kontrol lera att rätt kernel har valts i Jupyter Notebook. Kerneln visas i det övre högra hörnet på antecknings sidan. Standardvärdet är azure_automl. Observera att kärnan sparas som en del av antecknings boken. Så om du växlar till en ny Conda-miljö måste du välja den nya kerneln i antecknings boken.
      * För Azure Notebooks bör det vara python 3,6. 
      * För lokala Conda-miljöer bör det vara namnet på den Conda-miljö som du angav i automl_setup.
  * Se till att antecknings boken är för den SDK-version som du använder. Du kan kontrol lera SDK-versionen genom att köra `azureml.core.VERSION` i en Jupyter Notebook-cell. Du kan hämta tidigare versioner av exempel antecknings böckerna från GitHub genom att klicka på `Branch` knappen, välja `Tags` fliken och sedan välja versionen.

* **Numpy-importen Miss lyckas i Windows**: vissa Windows-miljöer ser ett fel vid inläsning av numpy med den senaste python-versionen 3.6.8. Om du ser det här problemet kan du prova med python version 3.6.7.

* **Numpy-importen Miss lyckas**: kontrol lera TensorFlow-versionen i den automatiserade ml Conda-miljön. Versioner som stöds är < 1,13. Avinstallera TensorFlow från miljön om versionen är >= 1,13 du kan kontrol lera versionen av TensorFlow och avinstallera enligt följande:
  1. Starta ett kommando gränssnitt, aktivera Conda-miljön där automatiserade ml-paket är installerade.
  2. Ange `pip freeze` och leta efter `tensorflow` , om den hittas, ska den version som visas vara < 1,13
  3. Om den listade versionen inte är en version som stöds går du till `pip uninstall tensorflow` kommando tolken och anger y för bekräftelse.

## <a name="deploy--serve-models"></a>Distribuera och hantera modeller

Utför följande åtgärder för följande fel:

|Fel  | Lösning  |
|---------|---------|
|Bild skapande problem vid distribution av webb tjänst     |  Lägg till "pynacl = = 1.2.1" som ett pip-beroende till Conda-filen för avbildnings konfiguration       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Ändra SKU: n för virtuella datorer som används i distributionen till en som har mer minne. |
|FPGA-problem     |  Du kommer inte att kunna distribuera modeller på FPGAs förrän du har begärt och godkänts för FPGA-kvoten. Om du vill begära åtkomst fyller du i formuläret kvot förfrågan: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Uppdatera Azure Machine Learning-komponenter i AKS-kluster

Uppdateringar av Azure Machine Learning-komponenter som är installerade i ett Azure Kubernetes service-kluster måste appliceras manuellt. 

Du kan tillämpa dessa uppdateringar genom att koppla från klustret från arbets ytan Azure Machine Learning och sedan återansluta klustret till arbets ytan. Om TLS är aktiverat i klustret måste du ange TLS/SSL-certifikatet och den privata nyckeln när du återansluter klustret. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Om du inte längre har TLS/SSL-certifikatet och den privata nyckeln, eller om du använder ett certifikat som har genererats av Azure Machine Learning, kan du hämta filerna innan du kopplar från klustret genom att ansluta till klustret med hjälp av `kubectl` och hämta hemligheten `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes lagrar hemligheterna i Base-64-kodat format. Du behöver Base-64-avkoda- `cert.pem` och- `key.pem` komponenterna i hemligheterna innan du ger dem `attach_config.enable_ssl` . 

### <a name="detaching-azure-kubernetes-service"></a>Kopplar från Azure Kubernetes-tjänsten

Om du använder Azure Machine Learning Studio, SDK eller Azure CLI-tillägget för Machine Learning för att koppla från ett AKS-kluster tas inte AKS-klustret bort. Information om hur du tar bort klustret finns i [använda Azure CLI med AKS](../aks/kubernetes-walkthrough.md#delete-the-cluster).

### <a name="webservices-in-azure-kubernetes-service-failures"></a>Webservice i Azure Kubernetes service-problem

Många webbtjänstfel i Azure Kubernetes Service kan felsökas genom att ansluta till klustret med hjälp av `kubectl`. Du kan hämta `kubeconfig.json` ett Azure Kubernetes service-kluster genom att köra

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Autentiseringsfel

Om du utför en hanterings åtgärd på ett beräknings mål från ett Fjärrjobb får du ett av följande fel: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Till exempel visas ett fel meddelande om du försöker skapa eller ansluta ett beräknings mål från en ML-pipeline som skickas för fjärrkörning.

## <a name="missing-user-interface-items-in-studio"></a>Användar gränssnitts objekt saknas i Studio

Rollbaserad åtkomst kontroll i Azure kan användas för att begränsa åtgärder som du kan utföra med Azure Machine Learning. Dessa begränsningar kan förhindra att användar gränssnitts objekt visas i Azure Machine Learning Studio. Om du till exempel har tilldelats en roll som inte kan skapa en beräknings instans visas inte alternativet för att skapa en beräknings instans i Studio.

Mer information finns i [Hantera användare och roller](how-to-assign-roles.md).

## <a name="compute-cluster-wont-resize"></a>Det går inte att ändra storlek på beräknings klustret

Om ditt Azure Machine Learning Compute-kluster ser fastnat i storleks ändring (0-> 0) för Node-tillståndet kan detta bero på att Azure-resursens lås är låst.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Nästa steg

Se fler fel söknings artiklar för Azure Machine Learning:

* [Fel sökning av Docker-distribution med Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Felsöka pipelines för Machine Learning](how-to-debug-pipelines.md)
* [Felsöka klassen ParallelRunStep från Azure Machine Learning SDK](how-to-debug-parallel-run-step.md)
* [Interaktiv fel sökning av en beräknings instans för Machine Learning med VS Code](how-to-debug-visual-studio-code.md)
* [Använd Application Insights för att felsöka pipelines för Machine Learning](./how-to-log-pipelines-application-insights.md)
