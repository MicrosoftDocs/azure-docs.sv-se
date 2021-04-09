---
title: Tolka & förklara ML-modeller i python (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar för hur din Machine Learning-modell fastställer funktions prioritet och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: fda1bc2ef0a112a8a32ba7c4caebf29028c8cdd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "98222759"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Använd tolknings paketet till att förklara ML-modeller & förutsägelser i python (för hands version)



I den här instruktions guiden lär du dig att använda tolknings paketet för Azure Machine Learning python SDK för att utföra följande uppgifter:


* Förklara hela modell beteendet eller enskilda förutsägelser på din personliga dator lokalt.

* Aktivera tolknings tekniker för tekniker som har funktioner.

* Förklara beteendet för hela modellen och enskilda förutsägelser i Azure.

* Använd en instrument panel för visualiseringar för att interagera med dina modell förklaringar.

* Distribuera en bedömnings förklaring tillsammans med din modell för att studera förklaringar under inferencing.



Mer information om vilka tolknings tekniker och maskin inlärnings modeller som stöds finns i [modell tolkning i Azure Machine Learning](how-to-machine-learning-interpretability.md) och [exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generera funktions prioritets värde på din personliga dator 
I följande exempel visas hur du använder tolknings paketet på din personliga dator utan att kontakta Azure-tjänster.

1. Installera paketet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Träna en exempel modell i en lokal Jupyter Notebook.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

3. Anropa förklaringen lokalt.
   * Om du vill initiera ett förklarings objekt skickar du din modell och några tränings data till förklaringens konstruktor.
   * Om du vill göra dina förklaringar och visualiseringar mer informativa kan du välja att skicka in funktions namn och utgångs klass namn om du gör klassificering.

   Följande kod block visar hur du instansierar ett förklarings objekt med `TabularExplainer` , `MimicExplainer` och `PFIExplainer` lokalt.
   * `TabularExplainer` anropar en av de tre SHAP-förklaringarna under ( `TreeExplainer` , `DeepExplainer` eller `KernelExplainer` ).
   * `TabularExplainer` väljer automatiskt den lämpligaste för ditt användnings fall, men du kan anropa var och en av de tre underliggande förklaringarna direkt.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    eller

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns.
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```

    eller

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Förklara hela modell beteendet (global förklaring) 

Se följande exempel för att få hjälp med att hämta mängd funktions värden (global).

```python

# you can use the training data or the test data here, but test data would allow you to use Explanation Exploration
global_explanation = explainer.explain_global(x_test)

# if you used the PFIExplainer in the previous step, use the next line of code instead
# global_explanation = explainer.explain_global(x_train, true_labels=y_train)

# sorted feature importance values and feature names
sorted_global_importance_values = global_explanation.get_ranked_global_values()
sorted_global_importance_names = global_explanation.get_ranked_global_names()
dict(zip(sorted_global_importance_names, sorted_global_importance_values))

# alternatively, you can print out a dictionary that holds the top K feature names and values
global_explanation.get_feature_importance_dict()
```

### <a name="explain-an-individual-prediction-local-explanation"></a>Förklara en individuell förutsägelse (lokal förklaring)
Hämta de enskilda funktions prioritets värdena för olika Datapoints genom att anropa förklaringar för en enskild instans eller en grupp av instanser.
> [!NOTE]
> `PFIExplainer` stöder inte lokala förklaringar.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Transformeringar av RAW-funktioner

Du kan välja att få förklaringar vad gäller RAW, ej transformerade funktioner i stället för de som har funktioner. För det här alternativet skickar du din funktions omvandlings pipeline till förklaringen i `train_explain.py` . Annars innehåller förklaringen förklaringar vad gäller de tekniker som finns.

Formatet på omvandlingar som stöds är detsamma som det beskrivs i [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). I allmänhet stöds alla omvandlingar så länge de arbetar på en enda kolumn så att de är tydliga.

Få en förklaring till RAW-funktioner med hjälp av en `sklearn.compose.ColumnTransformer` eller med en lista över monterade transformatorer. I följande exempel används `sklearn.compose.ColumnTransformer` .

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Om du vill köra exemplet med listan över monterade transformatorer, använder du följande kod:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generera funktions prioritets värden via fjärrkörningar

I följande exempel visas hur du kan använda- `ExplanationClient` klassen för att aktivera modell tolkning för fjärrkörningar. Det påminner om den lokala processen, förutom du:

* Använd `ExplanationClient` i fjärrkörning för att överföra tolknings kontexten.
* Ladda ned kontexten senare i en lokal miljö.

1. Installera paketet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Skapa ett utbildnings skript i en lokal Jupyter Notebook. Till exempel `train_explain.py`.

    ```python
    from azureml.interpret import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=feature_names, 
                                 classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

1. Konfigurera en Azure Machine Learning Compute som ditt beräknings mål och skicka din utbildnings körning. Instruktioner finns i [skapa och hantera Azure Machine Learning beräknings kluster](how-to-create-attach-compute-cluster.md) . Du kan också hitta [exempel på bärbara datorer](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) .

1. Hämta förklaringen i din lokala Jupyter Notebook.

    ```python
    from azureml.interpret import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```


## <a name="visualizations"></a>Visualiseringar

När du har laddat ned förklaringarna i din lokala Jupyter Notebook kan du använda visualiserings instrument panelen för att förstå och tolka din modell. Använd följande kod för att läsa in widgeten visualiserings instrument panel i Jupyter Notebook:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Visualiseringen stöder förklaringar för både funktioner som är tillverkade och råa. Råa förklaringar baseras på funktionerna från den ursprungliga data uppsättningen och de utformade förklaringarna baseras på funktionerna från data uppsättningen med funktions teknik som tillämpas.

När du försöker tolka en modell med avseende på den ursprungliga data uppsättningen rekommenderar vi att du använder obehandlade förklaringar eftersom varje funktions prioritet motsvarar en kolumn från den ursprungliga data uppsättningen. Ett scenario där de praktiska förklaringarna kan vara användbara är när du undersöker effekten av enskilda kategorier från en kategoriska-funktion. Om en enkel kodning används för en kategoriska-funktion, kommer de skapade förklaringarna att innehålla ett annat prioritets värde per kategori, en per funktion med en snabb funktion. Detta kan vara användbart när du begränsar vilken del av data uppsättningen som är mest informativ för modellen.

> [!NOTE]
> Utformade och råa förklaringar beräknas i tur och ordning. Först en utformad förklaring skapas baserat på modell-och funktionalisering-pipeline. Sedan skapas den råa förklaringen baserat på den här utformade förklaringen genom att aggregera betydelsen av de funktioner som har utvecklats från samma RAW-funktion.

### <a name="create-edit-and-view-dataset-cohorts"></a>Skapa, redigera och visa data uppsättning kohorter

Det övre menyfliksområdet visar den övergripande statistiken för din modell och dina data. Du kan segmentera och identifiera dina data i data uppsättningen kohorter eller under grupper för att undersöka eller jämföra din modells prestanda och förklaringar över dessa definierade under grupper. Genom att jämföra din data uppsättnings statistik och förklaringar över dessa under grupper kan du få en uppfattning om varför möjliga fel inträffar i en grupp jämfört med en annan.

[![Skapa, redigera och visa data uppsättnings kohorter](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Förstå hela modell beteendet (global förklaring) 

De tre första flikarna i förklarings instrument panelen ger en övergripande analys av den tränade modellen tillsammans med dess förutsägelser och förklaringar.

#### <a name="model-performance"></a>Modellprestanda
Utvärdera modellens prestanda genom att utforska distributionen av dina förutsägelse värden och värdena för dina modell prestanda mått. Du kan undersöka din modell ytterligare genom att titta på en jämför ande analys av dess prestanda över olika kohorter eller under grupper i din data uppsättning. Välj filter utmed y-Value och x-Value för att skära över olika dimensioner. Visa mått som precision, precision, återkalla, falsk positiv taxa (på) och falskt negativ kostnad (FNR).

[![Fliken modell prestanda i visualiseringen förklaring](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Data uppsättnings Utforskaren
Utforska din data uppsättnings statistik genom att välja olika filter längs X-, Y-och färg axlarna för att segmentera dina data utmed olika dimensioner. Skapa data uppsättnings kohorter ovan för att analysera data mängds statistik med filter som förutsägande resultat, data uppsättnings funktioner och fel grupper. Använd kugg hjuls ikonen i det övre högra hörnet i grafen för att ändra diagram typer.

[![Fliken Data uppsättnings Utforskaren i förklarings visualiseringen](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Mängd funktions prioritet
Utforska de viktigaste viktiga funktionerna som påverkar de övergripande modell förutsägelserna (kallas även global förklaring). Använd skjutreglaget för att Visa fallande funktions prioritets värden. Välj upp till tre kohorter för att Visa funktions prioritets värden sida vid sida. Klicka på någon av funktions staplarna i diagrammet för att se hur värdena i den valda funktionen påverkar modell förutsägelsen i området beroende.

[![Fliken mängd funktions prioritet i visualiserings visualiseringen](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Förstå enskilda förutsägelser (lokal förklaring) 

Den fjärde fliken på fliken förklaring gör det möjligt att öka detalj nivån för en enskild Datapoint och deras enskilda funktions betydelse. Du kan läsa in prioritets kurvan för enskilda funktioner för alla data punkter genom att klicka på någon av de enskilda data punkterna i huvud punkt ritningen eller välja en viss Datapoint i panel guiden till höger.

|Basera|Beskrivning|
|----|-----------|
|Enskild funktions prioritet|Visar viktiga viktiga funktioner för en individuell förutsägelse. Hjälper till att illustrera den underliggande modellens lokala beteende på en viss data punkt.|
|What-If analys|Tillåter ändringar av funktions värden för den valda verkliga data punkten och observerar resulterande ändringar i förutsägelse värde genom att generera en hypotetisk Datapoint med de nya funktions värdena.|
|Individuell villkorlig förväntad (ICE)|Tillåter funktions värdes ändringar från ett minsta värde till ett högsta värde. Hjälper dig att illustrera hur data punktens förutsägelse ändras när en funktion ändras.|

[![Individuell funktions prioritet och fliken information på förklarings instrument panelen](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Dessa är förklaringar baserat på många ungefärliger och är inte "orsak" av förutsägelser. Utan strikt matematisk robusthet av orsakssamband kan vi inte meddela användarna om att fatta beslut i real tid baserat på funktionen perturbations i What-If-verktyget. Det här verktyget är främst avsett för att förstå din modell och fel sökning.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisering i Azure Machine Learning Studio

Om du slutför stegen för [Fjärrtolkning](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (överföring av genererad förklaring till Azure Machine Learning körnings historik) kan du Visa instrument panelen för visualiseringar i [Azure Machine Learning Studio](https://ml.azure.com). Den här instrument panelen är en enklare version av instrument panelen för visualiseringar som beskrivs ovan. What-If-Datapoint-generering och ICE-observationer inaktive ras eftersom det inte finns någon aktiv beräkning i Azure Machine Learning Studio som kan utföra sina real tids beräkningar.

Om data uppsättningen, globala och lokala förklaringar är tillgängliga, fyller data i alla flikar. Om endast en global förklaring är tillgänglig inaktive ras fliken prioritet för enskilda funktioner.

Följ någon av dessa sökvägar för att få åtkomst till instrument panelen för visualiseringar i Azure Machine Learning Studio:

* **Experiment** fönstret (förhands granskning)
  1. Välj **experiment** i det vänstra fönstret om du vill se en lista över experiment som du har kört på Azure Machine Learning.
  1. Välj ett särskilt experiment för att visa alla körningar i experimentet.
  1. Välj en körning och sedan fliken **förklaringar** till instrument panelen förklarings visualisering.

   [![Instrument panelen för visualiseringar med mängd funktions prioritet i AzureML Studio i experiment](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* Fönstret **modeller**
  1. Om du har registrerat din ursprungliga modell genom att följa stegen i [Distribuera modeller med Azure Machine Learning](./how-to-deploy-and-where.md), kan du välja **modeller** i det vänstra fönstret för att visa den.
  1. Välj en modell och sedan fliken **förklaringar** för att visa instrument panelen för förklarings visualisering.

## <a name="interpretability-at-inference-time"></a>Tolkning vid en fördröjning

Du kan distribuera förklaringen tillsammans med den ursprungliga modellen och använda den vid en uppstarts tid för att tillhandahålla de enskilda funktions prioritets värdena (lokal förklaring) för nya Datapoint. Vi erbjuder även välklarade bedömnings förklaringar för att förbättra tolknings prestanda vid fördröjning, som för närvarande endast stöds i Azure Machine Learning SDK. Processen för att distribuera en undervisad resultat förklaring liknar att distribuera en modell och innehåller följande steg:

1. Skapa ett förklarings objekt. Du kan till exempel använda `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Skapa en bedömnings förklaring med förklarings objekt.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurera och registrera en avbildning som använder bedömnings modellen bedömning.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Som ett valfritt steg kan du hämta bedömnings förklaringen från molnet och testa förklaringen.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuera avbildningen till ett beräknings mål genom att följa dessa steg:

   1. Om det behövs registrerar du den ursprungliga förutsägelse modellen genom att följa stegen i [Distribuera modeller med Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Skapa en bedömnings fil.

         ```python
         %%writefile score.py
         import json
         import numpy as np
         import pandas as pd
         import os
         import pickle
         from sklearn.externals import joblib
         from sklearn.linear_model import LogisticRegression
         from azureml.core.model import Model
          
         def init():
         
            global original_model
            global scoring_model
             
            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

         def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
         ```
   1. Definiera distributionskonfigurationen.

         Den här konfigurationen beror på modellens krav. I följande exempel definieras en konfiguration som använder en processor kärna och en GB minne.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Skapa en fil med miljö beroenden.

         ```python
         from azureml.core.conda_dependencies import CondaDependencies

         # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

         azureml_pip_packages = ['azureml-defaults', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

         # specify CondaDependencies obj
         myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                          pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                          pin_sdk_version=False)


         with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

         with open("myenv.yml","r") as f:
            print(f.read())
         ```

   1. Skapa en anpassad Dockerfile med g + + installerat.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Distribuera den skapade avbildningen.
   
         Den här processen tar cirka fem minuter.

         ```python
         from azureml.core.webservice import Webservice
         from azureml.core.image import ContainerImage

         # use the custom scoring, docker, and conda files we created above
         image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                         docker_file="dockerfile",
                                                         runtime="python",
                                                         conda_file="myenv.yml")

         # use configs and models generated above
         service = Webservice.deploy_from_model(workspace=ws,
                                             name='model-scoring-service',
                                             deployment_config=aciconfig,
                                             models=[scoring_explainer_model, original_model],
                                             image_config=image_config)

         service.wait_for_deployment(show_output=True)
         ```

1. Testa distributionen.

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Rensa.

   Använd om du vill ta bort en distribuerad webb tjänst `service.delete()` .

## <a name="troubleshooting"></a>Felsökning

* **Sparse-data stöds inte**: modell förklarings instrument panelen bryts/sänks långsamt med ett stort antal funktioner, och därför har vi för närvarande inte stöd för sparse-dataformat. Dessutom uppstår allmänna minnes problem med stora data uppsättningar och många funktioner. 

* **Prognos modeller som inte stöds med modell förklaringar**: tolkning, bästa modell förklaring är inte tillgängligt för AutoML prognos experiment som rekommenderar följande algoritmer som bästa modell: TCNForecaster, AutoArima, Prophet, ExponentialSmoothing, Average, Naive, säsongs genomsnitt och säsongs Naive. AutoML-Prognosticering har Regressions modeller som stöder förklaringar. Men på förklarings instrument panelen är fliken "individuell funktions prioritet" bara stöd för prognoser på grund av komplexiteten i sina datapipeliner.

* **Lokal förklaring för data index**: förklarings instrument panelen stöder inte relaterade lokala prioritets värden till ett rad-ID från den ursprungliga validerings data uppsättningen om den data uppsättningen är större än 5000 Datapoints eftersom instrument panelen slumpmässigt Nedsamplar data. Instrument panelen visar dock funktions värden för RAW-datauppsättningen för varje Datapoint som överförts till instrument panelen under fliken individuell funktions prioritet. Användare kan mappa lokala prioriteter tillbaka till den ursprungliga data uppsättningen genom att matcha värdena för RAW-datauppsättningen. Om verifierings data uppsättningens storlek är mindre än 5000 exempel `index` motsvarar funktionen i azureml Studio indexet i verifierings data uppsättningen.

* **Konsekvens-och Ice-observationer som inte stöds i Studio**: What-If och enskilda Ice-observationer (villkorligt förväntat) stöds inte i Azure Machine Learning Studio under fliken förklaringar eftersom den uppladdade förklaringen kräver en aktiv beräkning för att beräkna om förutsägelser och sannolikheten för perturbed funktioner. Den stöds för närvarande i Jupyter Notebooks när den körs som en widget med hjälp av SDK.


## <a name="next-steps"></a>Nästa steg

[Lär dig mer om modell tolkning](how-to-machine-learning-interpretability.md)

[Ta en titt på exempel antecknings böcker för Azure Machine Learning tolkning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
