---
title: Förklaring i automatiserad ML (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du får förklaringar för hur din automatiserade ML-modell fastställer funktions prioritet och gör förutsägelser när du använder Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, automl, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.date: 07/09/2020
ms.openlocfilehash: 709c85bed4a028c6c168c79cd9fffd6b7b40cb68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100008051"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning-preview"></a>Tolkning: modellförklaringar i automatiserad maskininlärning (förhandsversion)



I den här artikeln får du lära dig hur du får förklaringar för automatisk maskin inlärning (AutoML) i Azure Machine Learning. AutoML hjälper dig att förstå funktions betydelsen hos de modeller som genereras. 

Alla SDK-versioner efter 1.0.85 anges `model_explainability=True` som standard. I SDK-version 1.0.85 och tidigare versioner måste användare ange `model_explainability=True` i `AutoMLConfig` objektet för att kunna använda modell tolkning. 

I den här artikeln kan du se hur du:

- Gör tolkningen under utbildningen för bästa modell eller modell.
- Aktivera visualiseringar så att du kan se mönster i data och förklaringar.
- Implementera tolkning under härledning eller poängsättning.

## <a name="prerequisites"></a>Förutsättningar

- Tolknings funktioner. Kör `pip install azureml-interpret` för att hämta det nödvändiga paketet.
- Kunskap om att skapa AutoML experiment. Mer information om hur du använder Azure Machine Learning SDK finns i [själv studie kursen om regressions modell](tutorial-auto-train-models.md) eller hur du [konfigurerar AutoML experiment](how-to-configure-auto-train.md).

## <a name="interpretability-during-training-for-the-best-model"></a>Tolkning under utbildning för bästa modell

Hämta förklaringen från `best_run` , som innehåller förklaringar för både rå och tillverkade funktioner.

> [!NOTE]
> Tolkning, bästa modell förklaring, är inte tillgänglig för experiment med automatisk ML-prognoser som rekommenderar följande algoritmer som bästa modell: 
> * TCNForecaster
> * AutoArima
> * ExponentialSmoothing
> * Prophet
> * Genomsnitt 
> * Naive
> * Säsongs genomsnitt 
> * Säsongs Naive

### <a name="download-the-engineered-feature-importances-from-the-best-run"></a>Hämta de funktioner som är utformade för den som är bäst från bästa körning

Du kan använda `ExplanationClient` för att ladda ned de tekniker som är de som är utformade från artefakt lagret för `best_run` . 

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="download-the-raw-feature-importances-from-the-best-run"></a>Hämta den råa funktions betydelsen från den bästa körningen

Du kan använda `ExplanationClient` för att hämta rå funktions förklaringar från artefakt lagret för `best_run` .

```python
from azureml.interpret import ExplanationClient

client = ExplanationClient.from_run(best_run)
raw_explanations = client.download_model_explanation(raw=True)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Tolkning under utbildning för vilken modell som helst 

När du beräknar modell förklaringar och visualiserar dem är du inte begränsad till en befintlig modell förklaring för en AutoML modell. Du kan också få en förklaring till din modell med olika test data. Stegen i det här avsnittet visar hur du beräknar och visualiserar den kapacitet som är utformad utifrån dina test data.

### <a name="retrieve-any-other-automl-model-from-training"></a>Hämta alla andra AutoML-modeller från utbildning

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Konfigurera modell förklaringarna

Används `automl_setup_model_explanations` för att hämta de utformade och råa förklaringarna. `fitted_model`Kan generera följande objekt:

- Aktuella data från utbildade eller test exempel
- Namn listor för en förutformad funktion
- Klasser som går att hitta i kolumnen med etiketter i klassificerings scenarier

`automl_explainer_setup_obj`Listan innehåller alla strukturer från ovanstående.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Initiera utredaren för härma för funktions prioritet

Om du vill generera en förklaring för AutoML-modeller använder du- `MimicWrapper` klassen. Du kan initiera MimicWrapper med följande parametrar:

- Installations objekt för förklaring
- Din arbets yta
- En surrogat modell som förklarar `fitted_model` AutoML-modellen

MimicWrapper tar också `automl_run` objektet där de utformade förklaringarna kommer att laddas upp.

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

### <a name="use-mimic-explainer-for-computing-and-visualizing-engineered-feature-importance"></a>Använd efterlikna förklaringar för beräkning och visualisering av utnyttjad funktions prioritet

Du kan anropa `explain()` metoden i MimicWrapper med de transformerade test exemplen för att få funktions vikten för de genererade funktionerna. Du kan också logga in på [Azure Machine Learning Studio](https://ml.azure.com/) för att visa instrument panelens visualisering av funktions prioritets värden för de genererade funktionerna av AutoML featurizers.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Använd efterlikna förklaringar för beräkning och visualisering av rå funktions prioritet

Du kan anropa `explain()` metoden i MimicWrapper med de transformerade test exemplen för att få funktions vikten för RAW-funktionerna. I [Machine Learning Studio](https://ml.azure.com/)kan du Visa instrument panelens visualisering av funktions prioritets värden för RAW-funktionerna.

```python
raw_explanations = explainer.explain(['local', 'global'], get_raw=True,
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform,
                                     raw_eval_dataset=automl_explainer_setup_obj.X_test_raw)
print(raw_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-inference"></a>Tolkning under härledning

I det här avsnittet får du lära dig att operationalisera en AutoML-modell med den förklaring som användes för att beräkna förklaringarna i föregående avsnitt.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrera modellen och bedömnings förklaringen

Använd `TreeScoringExplainer` för att skapa en bedömnings förklaring för att beräkna de funktioner som används vid en fördröjning. Du initierar bedömnings förklaringen med `feature_map` som har beräknats tidigare. 

Spara bedömnings förklararen och registrera sedan modellen och bedömnings förklaringen med Modellhantering tjänsten. Kör följande kod:

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Skapa Conda-beroenden för att konfigurera tjänsten

Skapa sedan de nödvändiga miljö beroendena i behållaren för den distribuerade modellen. Observera att azureml-defaults med version >= 1.0.45 måste anges som ett pip-beroende, eftersom det innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

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

### <a name="create-the-scoring-script"></a>Skapa bedömnings skriptet

Skriv ett skript som läser in din modell och ger förutsägelser och förklaringar baserat på en ny data uppsättning.

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

Distribuera tjänsten med hjälp av Conda-filen och bedömnings filen från föregående steg.

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

### <a name="inference-with-test-data"></a>Härledning med test data

En härledning med vissa test data för att se det förväntade värdet från AutoML-modellen, stöds för närvarande endast i Azure Machine Learning SDK. Visa funktions betydelse som bidrar till ett förutsägande värde. 

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

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualisera för att identifiera mönster i data och förklaringar i utbildnings tid

Du kan visualisera funktions prioritets diagrammet i din arbets yta i [Machine Learning Studio](https://ml.azure.com). När din AutoML-körning är klar väljer du **Visa modell information** för att visa en speciell körning. Välj fliken **förklaringar** om du vill visa instrument panelen förklarings visualisering.

[![Machine Learning tolknings arkitektur](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png)](./media/how-to-machine-learning-interpretability-automl/automl-explanation.png#lightbox)

Mer information om förklaringens visualiseringar och vissa områden finns i [instruktionen om att tolka dokument](how-to-machine-learning-interpretability-aml.md).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan aktivera modell förklaringar och funktions prioritet i områden i Azure Machine Learning SDK förutom Automatisk maskin inlärning finns i avsnittet [begrepp om tolkning](how-to-machine-learning-interpretability.md).
