---
title: Funktionalisering med automatisk maskin inlärning
titleSuffix: Azure Machine Learning
description: Lär dig hur du funktionalisering inställningar för data i Azure Machine Learning och hur du anpassar funktionerna för dina automatiserade ML-experiment.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to,automl,contperf-fy21q2
ms.date: 12/18/2020
ms.openlocfilehash: c90ef9fe49a87c18c7f4f55175bafaebfd31d722
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98610309"
---
# <a name="data-featurization-in-automated-machine-learning"></a>Data funktionalisering i Automatisk maskin inlärning

Lär dig mer om inställningarna för data funktionalisering i Azure Machine Learning och hur du anpassar dessa funktioner för [automatiserade maskin inlärnings experiment](concept-automated-ml.md).

## <a name="feature-engineering-and-featurization"></a>Funktions teknik och funktionalisering

Tränings data består av rader och kolumner. Varje rad är en observations-eller post och kolumnerna för varje rad är de funktioner som beskriver varje post. Normalt är de funktioner som bäst kännetecknar mönstren i data markerade för att skapa förutsägande modeller.

Även om många av rå data fälten kan användas direkt för att träna en modell, är det ofta nödvändigt att skapa ytterligare (utformade) funktioner som ger information som bättre skiljer mönster i data. Den här processen kallas för **funktions teknik**, där användningen av domän kunskaper om data utnyttjas för att skapa funktioner som i sin tur hjälper Machine Learning-algoritmer att lära sig bättre. 

I Azure Machine Learning tillämpas teknikerna för data skalning och normalisering för att göra det enklare att använda funktionen. Tillsammans kallas dessa tekniker och den här funktionen för **funktionalisering** i AUTOMATISERAde ml-experiment.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan vet hur du konfigurerar ett automatiserat ML experiment. Information om konfiguration finns i följande artiklar:

- För en kod-första upplevelse: [Konfigurera automatiserade ml-experiment med hjälp av Azure Machine Learning SDK för python](how-to-configure-auto-train.md).
- För att få en kod med låg kod eller ingen kod: [skapa, granska och distribuera automatiserade maskin inlärnings modeller med hjälp av Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Konfigurera funktionalisering

I varje automatiserad maskin inlärnings experiment tillämpas [automatiska skalnings-och normaliserings metoder](#featurization) på dina data som standard. Dessa metoder är typer av funktionalisering som hjälper *vissa* algoritmer som är känsliga för funktioner i olika skalor. Du kan aktivera fler funktionalisering, t. ex. *saknade värden Imputation*, *encoding* och *transformationer*.

> [!NOTE]
> Steg för automatisk Machine Learning-funktionalisering (till exempel funktion normalisering, hantering av data som saknas eller konvertering av text till numeriska) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma funktionalisering-steg som tillämpas på din indata automatiskt.

För experiment som du konfigurerar med python SDK, kan du aktivera eller inaktivera funktionalisering-inställningen och ytterligare ange de funktionalisering-steg som ska användas för experimentet. Om du använder Azure Machine Learning Studio, se [stegen för att aktivera funktionalisering](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

I följande tabell visas de accepterade inställningarna för `featurization` i [klassen AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Funktionalisering-konfiguration | Beskrivning|
------------- | ------------- |
|`"featurization": 'auto'`| Anger att steg som en del av processen för att bearbeta [data guardrails](#data-guardrails) och [funktionalisering](#featurization) ska göras automatiskt. Den här inställningen är standardinställningen.|
|`"featurization": 'off'`| Anger att funktionalisering-steg inte ska göras automatiskt.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Anger att anpassade funktionalisering-steg ska användas. [Lär dig hur du anpassar funktionalisering](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatisk funktionalisering

I följande tabell sammanfattas de tekniker som automatiskt tillämpas på dina data. Dessa tekniker används för experiment som har kon figurer ATS med hjälp av SDK eller Studio. Om du vill inaktivera det här beteendet ställer `"featurization": 'off'` du in i `AutoMLConfig` objektet.

> [!NOTE]
> Om du planerar att exportera AutoML-modeller till en ONNX- [modell](concept-onnx.md)stöds bara de funktionalisering-alternativ som anges med en asterisk ("*") i ONNX-formatet. Lär dig mer om [att konvertera modeller till ONNX](how-to-use-automl-onnx-model-dotnet.md).

|Funktionalisering- &nbsp; steg| Beskrivning |
| ------------- | ------------- |
|**Släpp hög kardinalitet eller inga varians funktioner*** |Släpp dessa funktioner från utbildning och validerings uppsättningar. Gäller för funktioner med alla värden som saknas, med samma värde för alla rader eller med hög kardinalitet (till exempel hash-värden, ID: n eller GUID).|
|**Imputerade värden som saknas*** |För numeriska funktioner måste du räkna ut med medelvärdet av värdena i kolumnen.<br/><br/>För kategoriska-funktioner ska du räkna med det vanligaste värdet.|
|**Generera fler funktioner** _ |För DateTime-funktioner: år, månad, dag, veckodag, dag på år, kvartal, vecka på år, timme, minut och sekund.<br><br> _For prognostisering av aktiviteter, * dessa ytterligare DateTime-funktioner skapas: ISO Year, halvårs vis, kalender månad som sträng, vecka, veckodag som sträng, dag i kvartal, dag på år, fm/em (0 om timme är före 12.00 (12 PM), 1), fm/em som sträng, timme på dagen (12-HR-bas)<br/><br/>För text funktioner: term frekvens baserat på unigrams, bigram och trigrams. Läs mer om [hur detta görs med Bert.](#bert-integration)|
|**Transformera och koda***|Transformera numeriska funktioner med några få unika värden i kategoriska-funktioner.<br/><br/>En-frekvent kodning används för kategoriska-funktioner med låg kardinalitet. En-frekvent-hash-kodning används för kategoriska-funktioner med hög kardinalitet.|
|**Word-inbäddningar**|En text upplärda konverterar vektorer med text-token till menings vektorer med hjälp av en förtränad modell. Varje ords inbäddnings vektor i ett dokument sammanställs med resten för att skapa en dokument funktions vektor.|
|**Kluster avstånd**|Tågen a k: kluster modell på alla numeriska kolumner. Ger nya *k* nya funktioner (en ny numerisk funktion per kluster) som innehåller avståndet för varje exempel till centroid för varje kluster.|

## <a name="data-guardrails"></a>Data guardrails

*Data guardrails* hjälper dig att identifiera potentiella problem med dina data (till exempel saknade värden eller [obalanser i klassen](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). De hjälper dig också att vidta korrigerande åtgärder för förbättrade resultat.

Data guardrails tillämpas:

- **För SDK-experiment**: När parametrarna `"featurization": 'auto'` eller `validation=auto` anges i `AutoMLConfig` objektet.
- **För Studio experiment**: när automatisk funktionalisering har Aktiver ATS.

Du kan granska data guardrails för ditt experiment:

- Genom att ställa in `show_output=True` när du skickar ett experiment med hjälp av SDK.

- I Studio går du till fliken **data guardrails** i din AUTOMATISERAde ml-körning.

### <a name="data-guardrail-states"></a>Data guardrail-tillstånd

Data guardrails visar ett av tre tillstånd:

|Stat| Beskrivning |
|----|---- |
|**Parametrarna**| Inga data problem upptäcktes och ingen åtgärd krävs av dig. |
|**Klar**| Ändringarna tillämpades på dina data. Vi rekommenderar att du läser igenom de korrigerande åtgärder som AutoML vidtog, för att säkerställa att ändringarna överensstämmer med de förväntade resultaten. |
|**Aviserad**| Ett data problem upptäcktes men kunde inte åtgärdas. Vi rekommenderar att du ändrar och åtgärdar problemet.|

### <a name="supported-data-guardrails"></a>Guardrails data som stöds

I följande tabell beskrivs de data guardrails som för närvarande stöds och de associerade status som du kan se när du skickar in experimentet:

Guardrail|Status|Villkor &nbsp; för &nbsp; utlösare
---|---|---
**Funktions värden saknas Imputation** |Parametrarna <br><br><br> Klart| Inga funktions värden som saknas hittades i dina tränings data. Läs mer om [saknat värde för Imputation.](./how-to-use-automated-ml-for-ml-models.md#customize-featurization) <br><br> De funktions värden som saknas har identifierats i dina utbildnings data och tillräknades.
**Funktions hantering med hög kardinalitet** |Parametrarna <br><br><br> Klart| Dina indata analyserades och inga funktioner för hög kardinalitet upptäcktes. <br><br> Funktioner med hög kardinalitet upptäcktes i dina indata och hanterades.
**Verifiering av delad hantering** |Klart| Verifierings konfigurationen har angetts till `'auto'` och tränings data innehöll *färre än 20 000 rader*. <br> Varje iteration av den tränade modellen verifierades med hjälp av kors validering. Läs mer om [verifierings data](./how-to-configure-auto-train.md#training-validation-and-test-data). <br><br> Verifierings konfigurationen har angetts till `'auto'` och tränings data innehöll *mer än 20 000 rader*. <br> Indata har delats in i en tränings data uppsättning och en validerings data uppsättning för att verifiera modellen.
**Identifiering av klass balansering** |Parametrarna <br><br><br><br>Aviserad <br><br><br>Klart | Dina indata analyserades och alla klasser är balanserade i dina tränings data. En data uppsättning anses vara bal anse rad om varje klass har en god representation i data uppsättningen, mätt enligt antal och samplings förhållandet. <br><br> Obalanserade klasser upptäcktes i dina indata. Åtgärda problem med balanseringen om du vill åtgärda modell kompensation. Läs mer om [obalanserade data](./concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data).<br><br> Obalanserade klasser upptäcktes i dina indata och den svepande logiken har bestämt sig för att tillämpa balansering.
**Identifiering av minnes problem** |Parametrarna <br><br><br><br> Klart |<br> De valda värdena (horisont, fördröjning, rullande fönster) har analyser ATS och inga potentiella minnes problem har identifierats. Lär dig mer om [Prognosticering-konfigurationer](./how-to-auto-train-forecast.md#configuration-settings)för tids serier. <br><br><br>De valda värdena (horisont, fördröjning, rullande fönster) har analyser ATS och kan orsaka att experimentet får slut på minne. Inställningarna för fördröjning eller rullande fönster har inaktiverats.
**Frekvens identifiering** |Parametrarna <br><br><br><br> Klart |<br> Tids serien analyserades och alla data punkter justeras med den frekvens som upptäcktes. <br> <br> Tids serien analyserades och data punkter som inte överensstämmer med den identifierade frekvensen upptäcktes. Dessa data punkter togs bort från data uppsättningen. Lär dig mer om [förberedelse av data för tids serie prognoser](./how-to-auto-train-forecast.md#preparing-data).

## <a name="customize-featurization"></a>Anpassa funktionalisering

Du kan anpassa dina funktionalisering-inställningar för att säkerställa att de data och funktioner som används för att träna din ML-modell leder till relevanta förutsägelser.

Om du vill anpassa featurizations anger du `"featurization": FeaturizationConfig` i ditt `AutoMLConfig` objekt. Om du använder Azure Machine Learning Studio för experimentet går du till [instruktions artikeln](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Information om hur du anpassar funktionalisering för olika typer av prognoser finns i [prognosen How-to](how-to-auto-train-forecast.md#customize-featurization).

Anpassningar som stöds är:

|Anpassning|Definition|
|--|--|
|**Uppdatering av kolumn syfte**|Åsidosätt den automatiskt identifierade funktions typen för den angivna kolumnen.|
|**Transformering av parameter uppdatering** |Uppdatera parametrarna för den angivna transformeraren. Stöder för närvarande *imputerade* (medel, mest frekventa och median) och *HashOneHotEncoder*.|
|**Ta bort kolumner** |Anger kolumner som ska släppas från att bearbetas.|
|**Block transformatorer**| Anger block-transformatorer som ska användas i funktionalisering-processen.|

>[!NOTE]
> Funktionen **släpp kolumner** är inaktuell från och med SDK-version 1,19. Släpp kolumner från data uppsättningen som en del av data rengöringen, innan du använder den i ditt automatiserade ML-experiment. 

Skapa `FeaturizationConfig` objektet med hjälp av API-anrop:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Funktionalisering-transparens

Varje AutoML-modell har funktionalisering tillämpats automatiskt.  Funktionalisering innehåller automatiserad funktions teknik (när `"featurization": 'auto'` ) och skalning och normalisering, som sedan påverkar den valda algoritmen och dess värden för dess parameter. AutoML har stöd för olika metoder för att se till att du har insyn i vad som gällde för din modell.

Överväg detta exempel på prognostisering:

+ Det finns fyra ingångs funktioner: A (numeriskt), B (numeriskt), C (numeriskt), D (DateTime).
+ Numerisk funktion C ignoreras eftersom det är en ID-kolumn med alla unika värden.
+ Numeriska funktioner A och B innehåller värden som saknas och anges därför av medelvärdet.
+ DateTime-funktionen D är bearbetas till 11 olika tekniker funktioner.

För att få den här informationen använder du `fitted_model` utdata från din automatiserade ml experiment körning.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Automatiserad funktions teknik 
`get_engineered_feature_names()`En lista med namn på de bevisade funktionerna returneras.

  >[!Note]
  >Använd ' timeseriestransformer ' för aktiviteten = ' Prognosticering ', Använd annars ' datatransformer ' för uppgiften ' regression ' eller ' klassificering '.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Den här listan innehåller alla bevarade funktions namn. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()`En funktionalisering-Sammanfattning av alla inmatade funktioner hämtas.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Utdata

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Utdata|Definition|
   |----|--------|
   |RawFeatureName|Inmatad funktion/kolumn namn från den angivna data uppsättningen.|
   |TypeDetected|Identifierad datatyp för indata-funktionen.|
   |Släpper|Anger om inmatad funktion har släppts eller använts.|
   |EngineeringFeatureCount|Antal funktioner som genererats via automatiserad funktion teknik Transforms.|
   |Transformeringar|Lista över omvandlingar som används för inmatade funktioner för att generera funktioner som har utvecklats.|

### <a name="scaling-and-normalization"></a>Skalning och normalisering

Om du vill förstå skalningen/normaliseringen och den valda algoritmen med dess biparameter värden använder du `fitted_model.steps` . 

Följande exempel på utdata körs `fitted_model.steps` för en vald körning:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Använd den här hjälp funktionen om du vill ha mer information: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Den här hjälp funktionen returnerar följande utdata för en viss körning med `LogisticRegression with RobustScalar` som en specifik algoritm.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Sannolikhet för förutsägelse av klass

Modeller som skapas med hjälp av automatiserade ML alla har wrapper-objekt som speglar funktioner från deras ursprungs klass med öppen källkod. De flesta omslutna objekt för klassificerings modell som returnerades av automatisk ML implementera `predict_proba()` funktionen, som accepterar ett mat ris data exempel för matriser eller sparse-data (X-värden) och returnerar en n-dimensionell matris av varje sampel och dess respektive klass sannolikhet.

Förutsatt att du har hämtat den bästa körningen och den monterade modellen med samma anrop från ovan kan du anropa `predict_proba()` direkt från den monterade modellen och tillhandahålla ett `X_test` exempel i lämpligt format beroende på modell typen.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Om den underliggande modellen inte stöder `predict_proba()` funktionen eller om formatet är felaktigt, kommer ett modell Klasss-särskilt undantag att genereras. I referens dokumenten [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) och [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) finns exempel på hur funktionen implementeras för olika modell typer.

<a name="bert-integration"></a>

## <a name="bert-integration-in-automated-ml"></a>BERT-integrering i automatiserad ML

[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) används i funktionalisering-lagret för AutoML. Om en kolumn i det här lagret innehåller Fritext eller andra typer av data, t. ex. tidsstämplar eller enkla tal, tillämpas funktionalisering på motsvarande sätt.

För BERT är modellen finjusterad och tränad användning av etiketter som användaren anger. Härifrån är dokument inbäddningar utdata som andra funktioner, t. ex. tidsstämpel-baserade funktioner, veckodag. 


### <a name="steps-to-invoke-bert"></a>Steg för att anropa BERT

Ange  `enable_dnn: True` automl_settings och Använd en GPU-beräkning ( `vm_size = "STANDARD_NC6"` eller en högre GPU) för att anropa Bert. Om en processor beräkning används aktiverar AutoML BiLSTM DNN upplärda i stället för BERT.

AutoML vidtar följande steg för BERT. 

1. **För bearbetning och tokenisering av alla text kolumner**. Transformeraren "StringCast" kan till exempel hittas i den slutliga modellens funktionalisering-Sammanfattning. Ett exempel på hur du kan skapa modellens funktionalisering-sammanfattning finns i [den här antecknings boken](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b).

2. **Sammanfoga alla text kolumner till en enda text kolumn**, och därför `StringConcatTransformer` i den slutliga modellen. 

    Vår implementering av BERT begränsar den totala text längden för ett utbildnings exempel till 128-token. Det innebär att alla text kolumner som är sammanfogade bör helst vara högst 128 tokens. Om det finns flera kolumner bör varje kolumn rensas så att villkoret är uppfyllt. Annars trunkerar den sammansatta kolumner med längden >128-token BERT tokenizer-lagret denna inmatare till 128-token.

3. **Som en del av funktionen sveper AutoML jämför BERT mot bas linjen (ord uppsättnings funktioner) i ett exempel på data.** Den här jämförelsen avgör om BERT skulle ge precisions förbättringar. Om BERT fungerar bättre än bas linjen använder AutoML BERT för text funktionalisering för hela data. I så fall visas `PretrainedTextDNNTransformer` i den slutliga modellen.

BERT körs vanligt vis längre än andra featurizers. För bättre prestanda rekommenderar vi att du använder "STANDARD_NC24r" eller "STANDARD_NC24rs_V3" för sina RDMA-funktioner. 

AutoML kommer att distribuera BERT-utbildning över flera noder om de är tillgängliga (upp till högst åtta noder). Detta kan göras i ditt `AutoMLConfig` objekt genom `max_concurrent_iterations` att ange parametern till högre än 1. 

## <a name="supported-languages-for-bert-in-automl"></a>Språk som stöds för BERT i autoML 

AutoML stöder för närvarande cirka 100 språk och, beroende på data uppsättningens språk, väljer autoML lämplig BERT-modell. För tyska data använder vi den tyska BERT-modellen. För engelska använder vi den engelska BERT-modellen. För alla andra språk använder vi den flerspråkiga BERT-modellen.

I följande kod utlöses den tyska BERT-modellen, eftersom data uppsättnings språket har angetts till `deu` , den tre bokstavs språk koden för tyska enligt [ISO-klassificering](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar dina automatiserade ML-experiment:

    * För en kod-första upplevelse: [Konfigurera automatiserade ml-experiment med hjälp av Azure Machine Learning SDK](how-to-configure-auto-train.md).
    * För en miljö med låg kod eller ingen kod: [skapa dina automatiserade ml-experiment i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).

* Lär dig mer om [hur du tränar en Regressions modell med hjälp av automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar genom att använda Automatisk maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).
