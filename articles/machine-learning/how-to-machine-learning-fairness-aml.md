---
title: Utvärdera ML-modeller skälighet i python (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du bedömer och minimerar skälighet för dina maskin inlärnings modeller med Fairlearn och Azure Machine Learning python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: mesameki
author: mesameki
ms.reviewer: luquinta
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, responsible-ml
ms.openlocfilehash: 70ad5d6d88dabb51e022a1fc5c011341b06f02fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640680"
---
# <a name="use-azure-machine-learning-with-the-fairlearn-open-source-package-to-assess-the-fairness-of-ml-models-preview"></a>Använd Azure Machine Learning med Fairlearn-paketet med öppen källkod för att utvärdera skälighet för ML-modeller (för hands version)

I den här instruktions guiden får du lära dig att använda python-paketet [Fairlearn](https://fairlearn.github.io/) med öppen källkod med Azure Machine Learning för att utföra följande uppgifter:

* Utvärdera skälighet för dina modell förutsägelser. Mer information om skälighet i Machine Learning finns i [artikeln skälighet i Machine Learning](concept-fairness-ml.md).
* Ladda upp, Visa och ladda ned skälighet Assessment Insights till/från Azure Machine Learning Studio.
* Se en instrument panel för skälighet utvärdering i Azure Machine Learning Studio för att interagera med din modell (er) skälighet Insights.

>[!NOTE]
> Skälighet-utvärderingen är inte en helt teknisk övning. **Det här paketet kan hjälpa dig att utvärdera skälighet för en Machine Learning-modell, men bara du kan konfigurera och fatta beslut om hur modellen ska prestera.**  Även om det här paketet hjälper till att identifiera kvantitativa mått för att utvärdera skälighet, måste utvecklare av Machine Learning-modeller också utföra en kvalitativ analys för att utvärdera skälighet för sina egna modeller.

## <a name="azure-machine-learning-fairness-sdk"></a>Azure Machine Learning skälighet SDK 

Azure Machine Learning skälighet SDK, `azureml-contrib-fairness` integrerar python-paketet med öppen källkod, [Fairlearn](http://fairlearn.github.io), i Azure Machine Learning. Om du vill veta mer om Fairlearn-integrering i Azure Machine Learning kan du titta på de här [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness). Mer information om Fairlearn finns i [exempel guiden](https://fairlearn.org/v0.6.0/auto_examples/) och exempel på [bärbara datorer](https://github.com/fairlearn/fairlearn/tree/master/notebooks). 

Använd följande kommandon för att installera- `azureml-contrib-fairness` och- `fairlearn` paketen:
```bash
pip install azureml-contrib-fairness
pip install fairlearn==0.4.6
```
Senare versioner av Fairlearn bör också fungera i följande exempel kod.



## <a name="upload-fairness-insights-for-a-single-model"></a>Ladda upp skälighet Insights för en enskild modell

I följande exempel visas hur du använder skälighet-paketet. Vi kommer att överföra modell skälighet Insights till Azure Machine Learning och se instrument panelen för skälighet-utvärdering i Azure Machine Learning Studio.

1. Träna en exempel modell i Jupyter Notebook. 

    För data uppsättningen använder vi den välkända data uppsättningen från den välkända uppsättningen, som vi hämtar från OpenML. Vi Föreställ dig att vi har ett problem med besluts fattandet med etiketten som visar om en person har återbetalat ett tidigare lån. Vi kommer att träna en modell att förutse om tidigare osett-individer kommer att återbetala ett lån. Sådan modell kan användas för att fatta beslut om lån.

    ```python
    import copy
    import numpy as np
    import pandas as pd

    from sklearn.compose import ColumnTransformer
    from sklearn.datasets import fetch_openml
    from sklearn.impute import SimpleImputer
    from sklearn.linear_model import LogisticRegression
    from sklearn.model_selection import train_test_split
    from sklearn.preprocessing import StandardScaler, OneHotEncoder
    from sklearn.compose import make_column_selector as selector
    from sklearn.pipeline import Pipeline
    
    from fairlearn.widget import FairlearnDashboard

    # Load the census dataset
    data = fetch_openml(data_id=1590, as_frame=True)
    X_raw = data.data
    y = (data.target == ">50K") * 1
    
    # (Optional) Separate the "sex" and "race" sensitive features out and drop them from the main data prior to training your model
    X_raw = data.data
    y = (data.target == ">50K") * 1
    A = X_raw[["race", "sex"]]
    X = X_raw.drop(labels=['sex', 'race'],axis = 1)
    
    # Split the data in "train" and "test" sets
    (X_train, X_test, y_train, y_test, A_train, A_test) = train_test_split(
        X_raw, y, A, test_size=0.3, random_state=12345, stratify=y
    )

    # Ensure indices are aligned between X, y and A,
    # after all the slicing and splitting of DataFrames
    # and Series
    X_train = X_train.reset_index(drop=True)
    X_test = X_test.reset_index(drop=True)
    y_train = y_train.reset_index(drop=True)
    y_test = y_test.reset_index(drop=True)
    A_train = A_train.reset_index(drop=True)
    A_test = A_test.reset_index(drop=True)

    # Define a processing pipeline. This happens after the split to avoid data leakage
    numeric_transformer = Pipeline(
        steps=[
            ("impute", SimpleImputer()),
            ("scaler", StandardScaler()),
        ]
    )
    categorical_transformer = Pipeline(
        [
            ("impute", SimpleImputer(strategy="most_frequent")),
            ("ohe", OneHotEncoder(handle_unknown="ignore")),
        ]
    )
    preprocessor = ColumnTransformer(
        transformers=[
            ("num", numeric_transformer, selector(dtype_exclude="category")),
            ("cat", categorical_transformer, selector(dtype_include="category")),
        ]
    )

    # Put an estimator onto the end of the pipeline
    lr_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                LogisticRegression(solver="liblinear", fit_intercept=True),
            ),
        ]
    )

    # Train the model on the test data
    lr_predictor.fit(X_train, y_train)

    # (Optional) View this model in Fairlearn's fairness dashboard, and see the disparities which appear:
    from fairlearn.widget import FairlearnDashboard
    FairlearnDashboard(sensitive_features=A_test, 
                       sensitive_feature_names=['Race', 'Sex'],
                       y_true=y_test,
                       y_pred={"lr_model": lr_predictor.predict(X_test)})
    ```

2. Logga in på Azure Machine Learning och registrera din modell.
   
    Skälighet-instrumentpanelen kan integreras med registrerade eller oregistrerade modeller. Registrera din modell i Azure Machine Learning med följande steg:
    ```python
    from azureml.core import Workspace, Experiment, Model
    import joblib
    import os
    
    ws = Workspace.from_config()
    ws.get_details()

    os.makedirs('models', exist_ok=True)
    
    # Function to register models into Azure Machine Learning
    def register_model(name, model):
        print("Registering ", name)
        model_path = "models/{0}.pkl".format(name)
        joblib.dump(value=model, filename=model_path)
        registered_model = Model.register(model_path=model_path,
                                        model_name=name,
                                        workspace=ws)
        print("Registered ", registered_model.id)
        return registered_model.id

    # Call the register_model function 
    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    ```

3. Precompute skälighet-mått.

    Skapa en instrument panels ord lista med Fairlearn- `metrics` paketet. `_create_group_metric_set`Metoden innehåller argument som liknar konstruktorn för instrument panelen, förutom att de känsliga funktionerna skickas som en ord lista (för att säkerställa att namnen är tillgängliga). Vi måste också ange typen av förutsägelse (binär klassificering i det här fallet) när du anropar den här metoden.

    ```python
    #  Create a dictionary of model(s) you want to assess for fairness 
    sf = { 'Race': A_test.race, 'Sex': A_test.sex}
    ys_pred = { lr_reg_id:lr_predictor.predict(X_test) }
    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Ladda upp de förberäknade skälighet-måtten.
    
    Nu `azureml.contrib.fairness` ska du importera paket för att utföra uppladdningen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Skapa ett experiment, sedan en körning och ladda upp instrument panelen till den:
    ```python
    exp = Experiment(ws, "Test_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness insights of Logistic Regression Classifier"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```
5. Kontrol lera skälighet-instrumentpanelen från Azure Machine Learning Studio

    Om du slutför de föregående stegen (laddar upp genererade skälighet insikter till Azure Machine Learning) kan du Visa skälighet-instrumentpanelen i [Azure Machine Learning Studio](https://ml.azure.com). Den här instrument panelen är samma visualiserings instrument panel som finns i Fairlearn, vilket gör att du kan analysera skillnaderna mellan den känsliga funktionens under grupper (t. ex. hane vs. hona).
    Följ någon av dessa sökvägar för att få åtkomst till instrument panelen för visualiseringar i Azure Machine Learning Studio:

    * **Experiment fönstret (förhands granskning)**
    1. Välj **experiment** i det vänstra fönstret om du vill se en lista över experiment som du har kört på Azure Machine Learning.
    1. Välj ett särskilt experiment för att visa alla körningar i experimentet.
    1. Välj en körning och sedan fliken **skälighet** till instrument panelen förklarings visualisering.
    1. När du har landning på fliken **skälighet** klickar du på ett **skälighet-ID** på menyn till höger.
    1. Konfigurera din instrument panel genom att välja känsligt attribut, prestanda mått och skälighet-mått för mark på skälighet-utvärderings sidan.
    1. Växla diagram typ från en till en annan för att Observera att både **allokeringar** skadas och **kvaliteten på tjänsten** skadas.



    [![Skälighet instrument panels tilldelning](./media/how-to-machine-learning-fairness-aml/dashboard-1.png)](./media/how-to-machine-learning-fairness-aml/dashboard-1.png#lightbox)
    
    [![Service kvalitet för skälighet-instrumentpanel](./media/how-to-machine-learning-fairness-aml/dashboard-2.png)](./media/how-to-machine-learning-fairness-aml/dashboard-2.png#lightbox)
    * **Fönstret modeller**
    1. Om du har registrerat din ursprungliga modell genom att följa föregående steg kan du välja **modeller** i det vänstra fönstret för att visa den.
    1. Välj en modell och klicka sedan på fliken **skälighet** för att visa instrument panelen för förklarings visualisering.

    Om du vill veta mer om instrument panelen för visualiseringar och vad den innehåller kan du kolla Fairlearn [user guide](https://fairlearn.org/v0.6.0/user_guide/assessment.html#fairlearn-dashboard).

## <a name="upload-fairness-insights-for-multiple-models"></a>Ladda upp skälighet Insights för flera modeller

Om du vill jämföra flera modeller och se hur deras skälighet-utvärdering skiljer sig åt, kan du skicka mer än en modell till instrument panelen för visualiseringar och jämföra sina skälighet-kompromisser.

1. Träna dina modeller:
    
    Nu skapar vi en andra klassificerare baserat på en support Vectors dator uppskattning och laddar upp en skälighet-instrumentpanel med hjälp av Fairlearn- `metrics` paketet. Vi förutsätter att den tidigare tränade modellen fortfarande är tillgänglig.


    ```python
    # Put an SVM predictor onto the preprocessing pipeline
    from sklearn import svm
    svm_predictor = Pipeline(
        steps=[
            ("preprocessor", copy.deepcopy(preprocessor)),
            (
                "classifier",
                svm.SVC(),
            ),
        ]
    )

    # Train your second classification model
    svm_predictor.fit(X_train, y_train)
    ```

2. Registrera dina modeller

    Nästa registrera båda modellerna i Azure Machine Learning. För enkelhetens skull kan du lagra resultaten i en ord lista, som mappar `id` i den registrerade modellen (en sträng i `name:version` formatet) till själva för dikteringen:

    ```python
    model_dict = {}

    lr_reg_id = register_model("fairness_logistic_regression", lr_predictor)
    model_dict[lr_reg_id] = lr_predictor

    svm_reg_id = register_model("fairness_svm", svm_predictor)
    model_dict[svm_reg_id] = svm_predictor
    ```

3. Läs in Fairlearn-instrumentpanelen lokalt

    Innan du laddar upp skälighet Insights i Azure Machine Learning kan du undersöka dessa förutsägelser i en lokalt anropad Fairlearn-instrumentpanel. 



    ```python
    #  Generate models' predictions and load the fairness dashboard locally 
    ys_pred = {}
    for n, p in model_dict.items():
        ys_pred[n] = p.predict(X_test)

    from fairlearn.widget import FairlearnDashboard

    FairlearnDashboard(sensitive_features=A_test, 
                    sensitive_feature_names=['Race', 'Sex'],
                    y_true=y_test.tolist(),
                    y_pred=ys_pred)
    ```

3. Precompute skälighet-mått.

    Skapa en instrument panels ord lista med Fairlearn- `metrics` paketet.

    ```python
    sf = { 'Race': A_test.race, 'Sex': A_test.sex }

    from fairlearn.metrics._group_metric_set import _create_group_metric_set

    dash_dict = _create_group_metric_set(y_true=Y_test,
                                        predictions=ys_pred,
                                        sensitive_features=sf,
                                        prediction_type='binary_classification')
    ```
4. Ladda upp de förberäknade skälighet-måtten.
    
    Nu `azureml.contrib.fairness` ska du importera paket för att utföra uppladdningen:

    ```python
    from azureml.contrib.fairness import upload_dashboard_dictionary, download_dashboard_by_upload_id
    ```
    Skapa ett experiment, sedan en körning och ladda upp instrument panelen till den:
    ```python
    exp = Experiment(ws, "Compare_Two_Models_Fairness_Census_Demo")
    print(exp)

    run = exp.start_logging()

    # Upload the dashboard to Azure Machine Learning
    try:
        dashboard_title = "Fairness Assessment of Logistic Regression and SVM Classifiers"
        # Set validate_model_ids parameter of upload_dashboard_dictionary to False if you have not registered your model(s)
        upload_id = upload_dashboard_dictionary(run,
                                                dash_dict,
                                                dashboard_name=dashboard_title)
        print("\nUploaded to id: {0}\n".format(upload_id))
        
        # To test the dashboard, you can download it back and ensure it contains the right information
        downloaded_dict = download_dashboard_by_upload_id(run, upload_id)
    finally:
        run.complete()
    ```


    Precis som i föregående avsnitt kan du följa en av Sök vägarna som beskrivs ovan (via **experiment** eller **modeller**) i Azure Machine Learning Studio för att få åtkomst till visualiserings instrument panelen och jämföra de två modellerna med skälighet och prestanda.


## <a name="upload-unmitigated-and-mitigated-fairness-insights"></a>Ladda upp minimerade och minimerade skälighet-insikter

Du kan använda Fairlearn för [](https://fairlearn.org/v0.6.0/user_guide/mitigation.html)att jämföra deras genererade, försämrade modell (er) till den ursprungliga minimerade modellen och navigera i prestanda-/skälighet-kompromisser mellan jämförda modeller.

Om du vill se ett exempel som demonstrerar användningen av algoritmen för [Rutnäts sökning](https://fairlearn.org/v0.6.0/user_guide/mitigation.html#grid-search) (som skapar en samling av minimerade modeller med olika skälighet och prestanda handeln) kan du kolla in den här [exempel antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/fairness/fairlearn-azureml-mitigation.ipynb). 

Genom att överföra flera modeller skälighet insikter i en enda körning kan du jämföra modeller med avseende på skälighet och prestanda. Du kan klicka på någon av de modeller som visas i modell jämförelse diagrammet för att se detaljerade skälighet insikter för den aktuella modellen.


[![Modell jämförelse Fairlearn-instrumentpanel](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png)](./media/how-to-machine-learning-fairness-aml/multi-model-dashboard.png#lightbox)
    

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om modell skälighet](concept-fairness-ml.md)

[Kolla Azure Machine Learning skälighet-exempel Notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness)
