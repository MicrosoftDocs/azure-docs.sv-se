---
title: Tolka & förklara ML-modeller i Python (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar till hur din maskininlärningsmodell avgör funktions vikt och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 07/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 2d60c6dbedb24847b95ce268bedafcb073421319
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576524"
---
# <a name="use-the-interpretability-package-to-explain-ml-models--predictions-in-python-preview"></a>Använd tolkningspaketet för att förklara ML-modeller & förutsägelser i Python (förhandsversion)

I den här guiden lär du dig att använda interpretability-paketet för python-SDK Azure Machine Learning för att utföra följande uppgifter:


* Förklara hela modellbeteendet eller enskilda förutsägelser på din personliga dator lokalt.

* Aktivera tolkningstekniker för utformade funktioner.

* Förklara beteendet för hela modellen och enskilda förutsägelser i Azure.

* Använd en visualiseringsinstrumentpanel för att interagera med dina modellförklaringar.

* Distribuera en bedömningsförklarare tillsammans med din modell för att observera förklaringar vid inferens.



Mer information om de tolkningstekniker och maskininlärningsmodeller som stöds finns i [Modelltolkning](how-to-machine-learning-interpretability.md) i Azure Machine Learning och [notebook-exempel.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Generera värde för funktions prioritet på din personliga dator 
I följande exempel visas hur du använder tolkningspaketet på din personliga dator utan att kontakta Azure-tjänster.

1. Installera paketet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```

2. Träna en exempelmodell i en lokal Jupyter Notebook.

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

3. Anropa förklararen lokalt.
   * Initiera ett förklararobjekt genom att skicka din modell och vissa träningsdata till förklararens konstruktor.
   * Om du vill göra dina förklaringar och visualiseringar mer informativa kan du välja att skicka in funktionsnamn och utdataklassnamn om du utför klassificering.

   Följande kodblock visar hur du instansierar ett förklararobjekt med `TabularExplainer` `MimicExplainer` , och `PFIExplainer` lokalt.
   * `TabularExplainer` anropar en av de tre SHAP-förklararna under ( `TreeExplainer` `DeepExplainer` , , eller `KernelExplainer` ).
   * `TabularExplainer` väljer automatiskt den lämpligaste för ditt användningsfall, men du kan anropa var och en av dess tre underliggande förklarare direkt.

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

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Förklara hela modellbeteendet (global förklaring) 

Se följande exempel för att få hjälp med att hämta de aggregerade (globala) egenskapsvärdena för prioritet.

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Förklara en enskild förutsägelse (lokal förklaring)
Hämta de enskilda egenskapsvärdena för prioritet för olika datapunkter genom att anropa förklaringar för en enskild instans eller en grupp med instanser.
> [!NOTE]
> `PFIExplainer` stöder inte lokala förklaringar.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Raw-funktionstransformningar

Du kan välja att få förklaringar vad gäller råa, otransformerade funktioner i stället för utformade funktioner. För det här alternativet skickar du din pipeline för funktionstransformering till förklararen i `train_explain.py` . I annat fall ger förklararen förklaringar när det gäller utformade funktioner.

Formatet för transformationer som stöds är samma som det som beskrivs i [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). I allmänhet stöds alla transformningar så länge de fungerar på en enda kolumn så att det är tydligt att de är en-till-många.

Få en förklaring av råa funktioner med hjälp av `sklearn.compose.ColumnTransformer` en eller med en lista över anpassade transformerstupplar. I följande exempel används `sklearn.compose.ColumnTransformer` .

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

Om du vill köra exemplet med listan över anpassade transformeringstupplar använder du följande kod:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generera värden för egenskaps prioritet via fjärrkörningar

I följande exempel visas hur du kan använda klassen `ExplanationClient` för att aktivera modelltolkning för fjärrkörningar. Det liknar begreppsmässigt den lokala processen, förutom att du:

* Använd i `ExplanationClient` fjärrkörningen för att ladda upp tolkningskontexten.
* Ladda ned kontexten senare i en lokal miljö.

1. Installera paketet `azureml-interpret`.
    ```bash
    pip install azureml-interpret
    ```
1. Skapa ett träningsskript i en lokal Jupyter Notebook. Till exempel `train_explain.py`.

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

1. Konfigurera en Azure Machine Learning Compute som beräkningsmål och skicka träningskörningen. Se [Skapa och hantera Azure Machine Learning för beräkningskluster](how-to-create-attach-compute-cluster.md) för instruktioner. [Exempelanteckningsböckerna kan också vara användbara.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation)

1. Ladda ned förklaringen i din lokala Jupyter Notebook.

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

När du har hämtat förklaringarna i ditt lokala Jupyter Notebook kan du använda visualiseringarna på instrumentpanelen med förklaringar för att förstå och tolka din modell. Om du vill läsa in instrumentpanelswidgeten för förklaringar i Jupyter Notebook använder du följande kod:

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, datasetX=x_test)
```

Visualiseringarna stöder förklaringar av både utformade och råa funktioner. Rådataförklaringar baseras på funktionerna från den ursprungliga datauppsättningen och de utformade förklaringarna baseras på funktionerna från datauppsättningen med funktionsteknik tillämpad.

När du försöker tolka en modell med avseende på den ursprungliga datauppsättningen rekommenderar vi att du använder råa förklaringar eftersom varje egenskaps prioritet motsvarar en kolumn från den ursprungliga datauppsättningen. Ett scenario där utformade förklaringar kan vara användbara är när du undersöker effekten av enskilda kategorier från en kategorisk funktion. Om en one-hot-kodning tillämpas på en kategorisk funktion innehåller de resulterande utformade förklaringarna ett annat prioritetsvärde per kategori, ett per one-hot engineered-funktion. Detta kan vara användbart när du begränsar vilken del av datamängden som är mest informativ för modellen.

> [!NOTE]
> Tekniker och rådata förklaringar beräknas sekventiellt. Först skapas en utformad förklaring baserat på modellen och pipelinen för featurisering. Sedan skapas den råa förklaringen baserat på den utformade förklaringen genom att aggregera vikten av utformade funktioner som kommer från samma råa funktion.

### <a name="create-edit-and-view-dataset-cohorts"></a>Skapa, redigera och visa datamängdskohorter

Det översta menyfliksområdet visar övergripande statistik för din modell och dina data. Du kan dela upp och dela upp dina data i datamängdskohorter, eller undergrupper, för att undersöka eller jämföra modellens prestanda och förklaringar i dessa definierade undergrupper. Genom att jämföra datamängdens statistik och förklaringar i dessa undergrupper kan du få en känsla av varför möjliga fel inträffar i en grupp jämfört med en annan.

[![Skapa, redigera och visa datamängdskohorter](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-cohorts.gif#lightbox)

### <a name="understand-entire-model-behavior-global-explanation"></a>Förstå hela modellbeteendet (global förklaring) 

De första tre flikarna i förklaringsinstrumentpanelen ger en övergripande analys av den tränade modellen tillsammans med dess förutsägelser och förklaringar.

#### <a name="model-performance"></a>Modellprestanda
Utvärdera modellens prestanda genom att utforska distributionen av dina förutsägelsevärden och värdena för modellens prestandamått. Du kan undersöka din modell ytterligare genom att titta på en jämförelseanalys av dess prestanda för olika kohorter eller undergrupper i datauppsättningen. Välj filter längs y-värde och x-värde för att klippa ut mellan olika dimensioner. Visa mått som precision, precision, träffsäkerhet, falsk positiv frekvens (FPR) och falsk negativ frekvens (FNR).

[![Fliken Modellprestanda i förklaringsvisualiseringen](./media/how-to-machine-learning-interpretability-aml/model-performance.gif)](./media/how-to-machine-learning-interpretability-aml/model-performance.gif#lightbox)

#### <a name="dataset-explorer"></a>Datauppsättningsutforskaren
Utforska din datamängdsstatistik genom att välja olika filter längs X-, Y- och färgaxlar för att dela upp dina data längs olika dimensioner. Skapa datamängdskohorter ovan för att analysera datamängdsstatistik med filter som förutsagt resultat, datauppsättningsfunktioner och felgrupper. Använd kugghjulsikonen i det övre högra hörnet i diagrammet för att ändra diagramtyper.

[![Fliken Datauppsättningsutforskaren i förklaringsvisualiseringen](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif)](./media/how-to-machine-learning-interpretability-aml/dataset-explorer.gif#lightbox)

#### <a name="aggregate-feature-importance"></a>Aggregera funktions prioritet
Utforska viktiga funktioner som påverkar dina övergripande modellförutsägelser (kallas även global förklaring). Använd skjutreglaget för att visa fallande prioritetsvärden för funktioner. Välj upp till tre kohorter för att se deras egenskapsvärden sida vid sida. Klicka på någon av funktionsstaplar i diagrammet för att se hur värdena för den valda funktionspåverkansmodellen förutsägelser i beroenderitningen nedan.

[![Fliken Aggregera funktions prioritet i förklaringsvisualiseringen](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif)](./media/how-to-machine-learning-interpretability-aml/aggregate-feature-importance.gif#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Förstå enskilda förutsägelser (lokal förklaring) 

På den fjärde fliken på förklaringsfliken kan du gå in på detalj i en enskild datapunkt och deras enskilda egenskapsvikter. Du kan läsa in ett diagram med den enskilda egenskapsviktpunkten för en datapunkt genom att klicka på någon av de enskilda datapunkterna i det huvudsakliga punktdiagramet eller välja en specifik datapunkt i panelguiden till höger.

|Tomt|Description|
|----|-----------|
|Individuell egenskaps prioritet|Visar viktiga funktioner i top-k för en enskild förutsägelse. Hjälper till att illustrera det lokala beteendet för den underliggande modellen på en specifik datapunkt.|
|What-If analys|Tillåter ändringar av funktionsvärden för den valda verkliga datapunkten och observerar resulterande ändringar i förutsägelsevärdet genom att generera en hypotetisk datapunkt med de nya funktionsvärdena.|
|Individuell villkorlig förväntan (ICE)|Tillåter att funktionsvärdet ändras från ett minimivärde till ett maxvärde. Hjälper till att illustrera hur datapunktens förutsägelse ändras när en funktion ändras.|

[![Individuell egenskaps prioritet och Vad om-fliken i förklaringsinstrumentpanelen](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif)](./media/how-to-machine-learning-interpretability-aml/individual-tab.gif#lightbox)

> [!NOTE]
> Det här är förklaringar som baseras på många approximeringar och är inte "orsak" till förutsägelser. Utan strikt matematisk robusthet i kausal slutsatsledning rekommenderar vi inte att användarna fattar verkliga beslut baserat på funktionsperturationer i What-If verktyg. Det här verktyget är främst till för att förstå din modell och felsökning.

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualisering i Azure Machine Learning-studio

Om du slutför stegen [för fjärrtolkning](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (ladda upp genererade förklaringar till Azure Machine Learning Run History) kan du visa visualiseringarna på instrumentpanelen med förklaringar [i Azure Machine Learning-studio](https://ml.azure.com). Den här instrumentpanelen är en enklare version av instrumentpanelswidgeten som genereras i jupyter-anteckningsboken. What-If datapunktsgenerering och ICE-diagram inaktiveras eftersom det inte finns någon aktiv beräkning i Azure Machine Learning-studio som kan utföra sina realtidsberäkningar.

Om datauppsättningen, globala och lokala förklaringar är tillgängliga, fyller data i alla flikar. Om endast en global förklaring är tillgänglig inaktiveras fliken Individuell egenskaps prioritet.

Följ någon av dessa sökvägar för att få åtkomst till instrumentpanelen med förklaringar i Azure Machine Learning-studio:

* **Experimentfönstret** (förhandsversion)
  1. Välj **Experiment** i det vänstra fönstret för att se en lista över experiment som du har kört Azure Machine Learning.
  1. Välj ett visst experiment för att visa alla körningar i experimentet.
  1. Välj en körning och sedan fliken **Explanations (Förklaringar)** till instrumentpanelen för förklaringsvisualiseringen.

   [![Instrumentpanel för visualisering med aggregerad egenskaps prioritet i AzureML Studio i experiment](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png)](./media/how-to-machine-learning-interpretability-aml/model-explanation-dashboard-aml-studio.png#lightbox)

* **Fönstret** Modeller
  1. Om du registrerade din ursprungliga modell genom att följa stegen i [Distribuera modeller med Azure Machine Learning](./how-to-deploy-and-where.md)kan du välja **Modeller** i det vänstra fönstret för att visa den.
  1. Välj en modell och sedan fliken **Förklaringar för** att visa instrumentpanelen med förklaringar.

## <a name="interpretability-at-inference-time"></a>Tolkning vid slutsatsledningstid

Du kan distribuera förklararen tillsammans med den ursprungliga modellen och använda den vid härledningstiden för att ange de enskilda egenskapsvärdena (lokal förklaring) för alla nya datapunkter. Vi erbjuder också enklare bedömningsförklarare för att förbättra tolkningsprestanda vid inferenstid, vilket för närvarande endast stöds i Azure Machine Learning SDK. Processen för att distribuera en förklarare för lättare poäng liknar distributionen av en modell och omfattar följande steg:

1. Skapa ett förklaringsobjekt. Du kan till exempel använda `TabularExplainer` :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Skapa en bedömningsförklarare med förklaringsobjektet.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Konfigurera och registrera en avbildning som använder bedömningsförklararmodellen.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Som ett valfritt steg kan du hämta bedömningsförklararen från molnet och testa förklaringarna.

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

1. Distribuera avbildningen till ett beräkningsmål genom att följa dessa steg:

   1. Om det behövs registrerar du den ursprungliga förutsägelsemodellen genom att följa stegen [i Distribuera modeller med Azure Machine Learning](./how-to-deploy-and-where.md).

   1. Skapa en bedömningsfil.

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

         Den här konfigurationen beror på kraven för din modell. I följande exempel definieras en konfiguration som använder en processorkärna och en GB minne.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Skapa en fil med miljöberoenden.

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

   1. Skapa en anpassad dockerfile med g++ installerat.

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

   Om du vill ta bort en distribuerad webbtjänst använder du `service.delete()` .

## <a name="troubleshooting"></a>Felsökning

* **Glesa data stöds inte:** Modellförklaringens instrumentpanel går sönder/saktar ned avsevärt med ett stort antal funktioner, och därför stöder vi för närvarande inte gles dataformat. Dessutom uppstår allmänna minnesproblem med stora datamängder och ett stort antal funktioner. 

* **Prognosmodeller** som inte stöds med modellförklaringar: Tolkningsbarhet, bästa modellförklaring, är inte tillgängligt för AutoML-prognosexperiment som rekommenderar följande algoritmer som den bästa modellen: TCNForecaster, AutoArima,Antal, ExponentialSmoothing, Average, Naive, Seasonal Average och Seasonal Naive. AutoML-prognostiserade har regressionsmodeller som stöder förklaringar. I förklaringsinstrumentpanelen stöds dock inte fliken "Individuell egenskaps prioritet" för prognostiserade data på grund av komplexiteten i deras datapipelines.

* **Lokal förklaring för dataindex:** Förklaringsinstrumentpanelen har inte stöd för att relatera lokala prioritetsvärden till en radidentifierare från den ursprungliga valideringsdatauppsättningen om datauppsättningen är större än 5 000 datapunkter eftersom instrumentpanelen slumpmässigt nedsamplar data. Instrumentpanelen visar dock funktionsvärden för rådata för varje datapunkt som skickas till instrumentpanelen under fliken Individuell egenskapsvikt. Användare kan mappa lokala prioriteter tillbaka till den ursprungliga datauppsättningen genom att matcha rådatauppsättningens funktionsvärden. Om storleken på valideringsdatamängden är mindre än 5 000 exempel motsvarar funktionen i `index` AzureML Studio indexet i valideringsdatamängden.

* **What-if/ICE-diagram** som inte stöds i studio: What-If- och ICE-diagram (Individual Conditional Expectation) stöds inte i Azure Machine Learning-studio på fliken Explanations (Förklaringar) eftersom den uppladdade förklaringen behöver en aktiv beräkning för att beräkna om förutsägelser och sannolikheter för perturerade funktioner. Den stöds för närvarande i Jupyter Notebooks när den körs som en widget med sdk:n.


## <a name="next-steps"></a>Nästa steg

[Läs mer om modelltolkning](how-to-machine-learning-interpretability.md)

[Ta en Azure Machine Learning notebook-exempel på tolkning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)
