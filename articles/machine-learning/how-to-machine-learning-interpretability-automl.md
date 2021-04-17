---
title: Förklarande i automatiserad ML (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar av hur din automatiserade ML-modell avgör funktions vikten och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 3258a1d53c4aa5010758bcd93ef32c53611f4684
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576473"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Tolkning: modellförklaringar i automatiserad maskininlärning (förhandsversion)


I den här artikeln får du lära dig hur du får förklaringar av automatiserad maskininlärning (automatiserad ML) i Azure Machine Learning med hjälp av Python SDK. Automatiserad ML hjälper dig att förstå funktions vikten av de modeller som genereras. 

Alla SDK-versioner efter 1.0.85 anges `model_explainability=True` som standard. I SDK-version 1.0.85 och tidigare versioner måste användare ange i -objektet `model_explainability=True` för att kunna använda `AutoMLConfig` modelltolkning. 


I den här artikeln kan du se hur du:

- Utför tolkningsbarhet under träningen för bästa modell eller valfri modell.
- Aktivera visualiseringar som hjälper dig att se mönster i data och förklaringar.
- Implementera tolkning vid slutsatsledning eller bedömning.

## <a name="prerequisites"></a>Förutsättningar

- Tolkningsfunktioner. Kör `pip install azureml-interpret` för att hämta det nödvändiga paketet.
- Kunskap om att skapa automatiserade ML-experiment. Mer information om hur du använder SDK Azure Machine Learning finns i den här självstudien om [regressionsmodeller](tutorial-auto-train-models.md) eller så kan du läsa om hur du [konfigurerar automatiserade ML-experiment.](how-to-configure-auto-train.md)

## <a name="interpretability-during-training-for-the-best-model"></a>Tolkningsbarhet under träning för den bästa modellen

Hämta förklaringen från `best_run` , som innehåller förklaringar till både råa och utformade funktioner.

> [!NOTE]
> Tolkningsbarhet, bästa modellförklaring, är inte tillgänglig för experiment med automatisk ML-prognostiserade data som rekommenderar följande algoritmer som den bästa modellen: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Profeten
> * Genomsnitt 
> * Naive
> * Säsongsgenomsnitt 
> * Säsongsberoende Naive

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Ladda ned de tekniker som är viktiga för funktionen från den bästa körningen

Du kan använda `ExplanationClient` för att ladda ned förklaringarna av den utformade funktionen från artefaktarkivet för `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Ladda ned råa funktions prioriteter från den bästa körningen

Du kan använda `ExplanationClient` för att ladda ned raw-funktionsförklaringarna från artefaktarkivet för `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Tolkningsbarhet under träning för alla modeller 

När du beräknar modellförklaringar och visualiserar dem är du inte begränsad till en befintlig modellförklaring för en AutoML-modell. Du kan också få en förklaring för din modell med olika testdata. Stegen i det här avsnittet visar hur du beräknar och visualiserar viktiga funktioner baserat på dina testdata.

### <a name="retrieve-any-other-automl-model-from-training"></a>Hämta andra AutoML-modeller från träningen

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Konfigurera modellförklaringarna

Använd `automl_setup_model_explanations` för att hämta de tekniker och råa förklaringarna. kan `fitted_model` generera följande objekt:

- Aktuella data från tränade eller testexempel
- Namnlistor för utformade funktioner
- Sökbara klasser i din märkta kolumn i klassificeringsscenarier

`automl_explainer_setup_obj`innehåller alla strukturer från listan ovan.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initiera Mimic Explainer för funktions prioritet

Om du vill skapa en förklaring av automatiserade ML-modeller använder du `MimicWrapper` klassen . Du kan initiera MimicWrapper med följande parametrar:

- Förklaringsinstallationsobjektet
- Din arbetsyta
- En surrogatmodell som förklarar den automatiserade `fitted_model` ML-modellen

MimicWrapper tar även objektet `automl_run` där de utformade förklaringarna kommer att laddas upp.

```python
from azureml.interpret import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator,
                         explainable_model=automl_explainer_setup_obj.surrogate_model, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes,
                         explainer_kwargs=automl_explainer_setup_obj.surrogate_model_params)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Använda Mimic Explainer för att beräkna och visualisera viktiga funktioner

Du kan anropa metoden i MimicWrapper med de transformerade testexempelna för att hämta `explain()` funktionens betydelse för de genererade utformade funktionerna. Du kan också logga in [på Azure Machine Learning-studio](https://ml.azure.com/) för att visa förklaringar av instrumentpanelens visualisering av funktionsvärdenas prioritetsvärden för de genererade utformade funktionerna av automatiserade ML-funktionsfunktioner.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```
För modeller som tränas med automatiserad ML kan du få den bästa modellen med hjälp `get_output()` av metoden och beräkningsförklaringarna lokalt.  Du kan visualisera förklaringsresultaten med `ExplanationDashboard` från `interpret-community` paketet.

```python
best_run, fitted_model = remote_run.get_output()

from azureml.train.automl.runtime.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations
automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train,
                                                             X_test=X_test, y=y_train,
                                                             task='regression')

from interpret.ext.glassbox import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel,
                         init_dataset=automl_explainer_setup_obj.X_transform, run=best_run,
                         features=automl_explainer_setup_obj.engineered_feature_names,
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
                         
pip install interpret-community[visualization]

engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, datasetX=automl_explainer_setup_obj.X_test_transform)

 

raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(raw_explanations.get_feature_importance_dict()),
from interpret_community.widget import ExplanationDashboard
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, datasetX=automl_explainer_setup_obj.X_test_raw)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Använda Mimic Explainer för att beräkna och visualisera råa funktioners betydelse

Du kan anropa metoden `explain()` i MimicWrapper med de transformerade testexempelna för att hämta funktionens betydelse för råa funktioner. I Machine Learning [Studio kan](https://ml.azure.com/)du visa instrumentpanelens visualisering av funktionernas prioritetsvärden för råa funktioner.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Tolkningsbarhet under slutsatsledning

I det här avsnittet får du lära dig att operationalisera en automatiserad ML-modell med förklararen som användes för att beräkna förklaringarna i föregående avsnitt.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrera modellen och bedömningsförklararen

Använd för `TreeScoringExplainer` att skapa bedömningsförklararen som beräknar de utformade egenskapsvärdena för prioritet vid inferenstiden. Du initierar bedömningsförklararen med `feature_map` som beräknades tidigare. 

Spara bedömningsförklararen och registrera sedan modellen och bedömningsförklararen med Modellhantering tjänsten. Kör följande kod:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Skapa conda-beroenden för att konfigurera tjänsten

Skapa sedan nödvändiga miljöberoenden i containern för den distribuerade modellen. Observera att azureml-defaults med version >= 1.0.45 måste anges som ett pip-beroende, eftersom det innehåller de funktioner som krävs för att vara värd för modellen som en webbtjänst.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="create-the-scoring-script"></a>Skapa bedömningsskriptet

Skriv ett skript som läser in din modell och skapar förutsägelser och förklaringar baserat på en ny databatch.

```python
%%writefile score.py
import joblib
import pandas as pd
from azureml.core.model import Model
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations


def init():
    global automl_model
    global scoring_explainer

    # Retrieve the path to the model file using the model name
    # Assume original model is named automl_model
    automl_model_path = Model.get_model_path('automl_model')
    scoring_explainer_path = Model.get_model_path('scoring_explainer')

    automl_model = joblib.load(automl_model_path)
    scoring_explainer = joblib.load(scoring_explainer_path)


def run(raw_data):
    data = pd.read_json(raw_data, orient='records')
    # Make prediction
    predictions = automl_model.predict(data)
    # Setup for inferencing explanations
    automl_explainer_setup_obj = automl_setup_model_explanations(automl_model,
                                                                 X_test=data, task='classification')
    # Retrieve model explanations for engineered explanations
    engineered_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform)
    # Retrieve model explanations for raw explanations
    raw_local_importance_values = scoring_explainer.explain(automl_explainer_setup_obj.X_test_transform, get_raw=True)
    # You can return any data type as long as it is JSON-serializable
    return {'predictions': predictions.tolist(),
            'engineered_local_importance_values': engineered_local_importance_values,
            'raw_local_importance_values': raw_local_importance_values}
```

### <a name="deploy-the-service"></a>Distribuera tjänsten

Distribuera tjänsten med conda-filen och bedömningsfilen från föregående steg.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Slutsatsledning med testdata

Slutsats slutsats med vissa testdata för att se det förutsagda värdet från AutoML-modellen, som för närvarande endast stöds i Azure Machine Learning SDK. Visa funktions prioriteter som bidrar till ett förutsagt värde. 

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print('raw_local_importance_values:\n{}\n'.format(output['raw_local_importance_values']))
```

## <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualisera för att identifiera mönster i data och förklaringar vid träningstid

Du kan visualisera diagrammet för egenskaps prioritet på din arbetsyta [i Azure Machine Learning-studio](https://ml.azure.com). När AutoML-körningen är klar väljer du **Visa modellinformation för** att visa en specifik körning. Välj fliken **Förklaringar** för att se visualiseringarna på förklaringsinstrumentpanelen.

[![Machine Learning för tolkning](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Mer information om förklaringar av instrumentpanelsvisualiseringar och specifika diagram finns i [instrumentpanelens dokument om tolkningsbarhet.](how-to-machine-learning-interpretability-aml.md)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan aktivera modellförklaringar och funktionsbetydelse inom andra områden i Azure Machine Learning SDK än automatiserad maskininlärning finns i begreppsartikeln [om tolkning](how-to-machine-learning-interpretability.md).
