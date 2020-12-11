---
title: Justera dina modellers egenskaper
titleSuffix: Azure Machine Learning
description: Justera de breda parametrarna för djup inlärning och maskin inlärnings modeller med hjälp av Azure Machine Learning.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a1a0b89dee5a6d57e6f317c5f6c8b61ffeda3c33
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029081"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Justera dina modellers egenskaper med Azure Machine Learning


Automatisera effektiv inställning av HyperDrive med hjälp av Azure Machine Learning [-paket](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py). Lär dig hur du utför de steg som krävs för att justera de båda parametrarna med [Azure Machine Learning SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py):

1. Definiera parameter Sök utrymmet
1. Ange ett primärt mått som ska optimeras  
1. Ange tidig avslutnings princip för körningar med lågt utförande
1. Allokera resurser
1. Starta ett experiment med den definierade konfigurationen
1. Visualisera inlärnings körningar
1. Välj den bästa konfigurationen för din modell

## <a name="what-are-hyperparameters"></a>Vad är mina parametrar?

Det **finns justerbara parametrar som** gör det möjligt att styra modell inlärnings processen. Med neurala-nätverk bestämmer du till exempel antalet dolda lager och antalet noder i varje lager. Modell prestanda beror på mycket olika grundparametrar.

 **Justering** av min parameter är en process för att hitta konfigurationen av de egenskaper som ger bästa prestanda. Processen är vanligt vis kostsam och manuell.

Med Azure Machine Learning kan du automatisera inställningen för att justera och köra experiment parallellt för att effektivt optimera de båda parametrarna.


## <a name="define-the-search-space"></a>Definiera Sök utrymmet

Justera disponeringsparametrarna genom att utforska intervallet av de värden som definierats för varje-parameter.

Disponeringsparametrarna kan vara diskreta eller kontinuerliga och har en fördelning av värden som beskrivs av ett [parameter uttryck](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?preserve-view=true&view=azure-ml-py).

### <a name="discrete-hyperparameters"></a>Diskreta egenskaper 

Diskreta egenskaper anges som en `choice` mellan diskreta värden. `choice` kan vara:

* ett eller flera kommaavgränsade värden
* ett `range` objekt
* valfritt godtyckligt `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

I det här fallet är `batch_size` ett av värdena [16, 32, 64, 128] och `number_of_hidden_layers` någon av värdena [1, 2, 3, 4].

Följande avancerade diskreta egenskaper kan också anges med hjälp av en distribution:

* `quniform(low, high, q)` -Returnerar ett värde som Round (Uniform (låg, hög)/q) * q
* `qloguniform(low, high, q)` -Returnerar ett värde som Round (exp (Uniform (låg, hög))/q) * q
* `qnormal(mu, sigma, q)` -Returnerar ett värde som Round (normal (MU, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` -Returnerar ett värde som Round (exp (normal (MU, Sigma))/q) * q

### <a name="continuous-hyperparameters"></a>Kontinuerliga egenskaper 

De kontinuerliga disponeringsparametrarna anges som en distribution över ett kontinuerligt värde intervall:

* `uniform(low, high)` -Returnerar ett värde enhetligt fördelat mellan låg och hög
* `loguniform(low, high)` -Returnerar ett värde som ritats enligt exp (Uniform (låg, hög)) så att logaritmen för returvärdet är enhetligt distribuerad
* `normal(mu, sigma)` – Returnerar ett verkligt värde som normalt distribueras med medelvärdet My och standard avvikelsen Sigma
* `lognormal(mu, sigma)` -Returnerar ett värde som ritats enligt exp (normal (MU, Sigma)) så att logaritmen för returvärdet normalt distribueras

Ett exempel på en parameter utrymmes definition:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Den här koden definierar ett Sök utrymme med två parametrar – `learning_rate` och `keep_probability` . `learning_rate` har en normal distribution med medel värde 10 och en standard avvikelse på 3. `keep_probability` har en enhetlig distribution med ett lägsta värde på 0,05 och ett högsta värde på 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Sampling av områdets parameter utrymme

Ange parameter samplings metoden som ska användas över området för den här parametern. Azure Machine Learning stöder följande metoder:

* Slumpmässig provtagning
* Rutnäts sampling
* Bayesiansk sampling

#### <a name="random-sampling"></a>Slumpmässig provtagning

[Slumpmässig sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?preserve-view=true&view=azure-ml-py) stöder diskreta och kontinuerliga disponeringsparametrarna. Det har stöd för tidig avslutning av låga prestanda körningar. Vissa användare utför en inledande sökning med slumpmässig sampling och förfina sedan Sök utrymmet för att förbättra resultaten.

I slumpmässig sampling väljs värdena för båda parametrarna slumpmässigt från det definierade Sök utrymmet. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rutnäts sampling

[Rutnäts sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?preserve-view=true&view=azure-ml-py) stöder diskreta egenskaper. Använd rutnäts sampling om du kan budgetera till en fullständig sökning över Sök utrymmet. Har stöd för tidig avslutning av låga prestanda körningar.

Utför en enkel rutnäts sökning över alla möjliga värden. Det går bara att använda rutnäts sampling med `choice` disponeringsparametrarna. Följande utrymme har exempelvis sex exempel:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesiansk sampling

[Bayesian-sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?preserve-view=true&view=azure-ml-py) baseras på algoritmen för Bayesian-optimering. Det väljer exempel baserat på hur tidigare exempel som utförs, så att nya exempel förbättrar det primära måttet.

Bayesian-sampling rekommenderas om du har tillräckligt med budget för att utforska området för den här parametern. För bästa resultat rekommenderar vi ett maximalt antal körningar som är större än eller lika med 20 gånger antalet egenskaper som är justerade. 

Antalet samtidiga körningar påverkar justerings processens effektivitet. Ett mindre antal samtidiga körningar kan leda till bättre samplings konvergens, eftersom den mindre graden av parallellitet ökar antalet körningar som förmånen från tidigare slutförda körningar.

Bayesian-sampling stöder bara `choice` , `uniform` och `quniform` distributioner över Sök utrymmet.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Ange primärt mått

Ange det [primära mått](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?preserve-view=true&view=azure-ml-py) som du vill att inställningen för min parameter ska optimera. Varje tränings körning utvärderas för det primära måttet. Principen för tidig avslutning använder det primära måttet för att identifiera prestanda körningar.

Ange följande attribut för ditt primära mått:

* `primary_metric_name`: Namnet på det primära måttet måste exakt matcha namnet på det mått som loggats av övnings skriptet
* `primary_metric_goal`: Det kan vara antingen `PrimaryMetricGoal.MAXIMIZE` eller `PrimaryMetricGoal.MINIMIZE` och bestämmer om det primära måttet ska maximeras eller minimeras när körningarna utvärderas. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

I det här exemplet maximeras "precision".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Logg mått för justering av en parametriserad parameter

Utbildnings skriptet för din modell **måste** logga det primära måttet under modell träningen så att Hyperdrive kan komma åt den för justering av en parameter.

Logga det primära måttet i ditt utbildnings skript med följande exempel-kodfragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Tränings skriptet beräknar `val_accuracy` och loggar det som primärt mått "noggrannhet". Varje gången måttet loggas tas det emot av tjänsten för justering av tids parametrar. Det är upp till dig att fastställa frekvensen för rapportering.

Mer information om loggnings värden i modell inlärnings körningar finns i [Aktivera loggning i Azure ml-utbildning](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Ange princip för tidig avslutning

Avsluta automatiskt dåligt utförande-körningar med en tidig avslutnings princip. Tidig avslutning förbättrar beräknings effektiviteten.

Du kan konfigurera följande parametrar som styr när en princip tillämpas:

* `evaluation_interval`: frekvensen för att tillämpa principen. Varje tillfälle ska tränings skriptet logga in det primära måttet som ett intervall. En `evaluation_interval` av 1 tillämpar principen varje gång utbildnings skriptet rapporterar det primära måttet. En `evaluation_interval` av 2 tillämpar principen varannan gång. Om inget värde anges `evaluation_interval` anges värdet 1 som standard.
* `delay_evaluation`: fördröjer den första princip utvärderingen för ett angivet antal intervall. Det här är en valfri parameter som förhindrar att utbildningar avslutas i förtid genom att tillåta att alla konfigurationer körs i ett minsta antal intervall. Om det här alternativet anges gäller principen varje multipel av evaluation_interval som är större än eller lika med delay_evaluation.

Azure Machine Learning stöder följande tidiga avslutnings principer:
* [Bandit-princip](#bandit-policy)
* [Median stoppar princip](#median-stopping-policy)
* [Princip för att välja trunkering](#truncation-selection-policy)
* [Ingen avslutnings princip](#no-termination-policy-default)


### <a name="bandit-policy"></a>Bandit-princip

[Bandit-principen](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?preserve-view=true&view=azure-ml-py#&preserve-view=truedefinition) baseras på slack-eller slack och utvärderings intervall. Bandit avbryter körningar där det primära måttet inte ligger inom den angivna slack-faktorn/slack svärdet jämfört med den bästa körningen.

> [!NOTE]
> Bayesian-sampling stöder inte tidig uppsägning. När du använder Bayesian-sampling anger du `early_termination_policy = None` .

Ange följande konfigurations parametrar:

* `slack_factor` eller `slack_amount` : det tillåtna slacket i förhållande till bästa möjliga utbildning körs. `slack_factor` anger det tillåtna slacket som ett förhållande. `slack_amount` anger det tillåtna slacket som ett absolut belopp, i stället för en kvot.

    Anta till exempel att en bandit-princip används vid intervall 10. Anta att den bästa presterande körningen vid intervall 10 rapporterade ett primärt mått är 0,8 med målet att maximera det primära måttet. Om principen anger en/ett `slack_factor` i 0,2, körs all utbildning vars bästa mått vid intervallet 10 är mindre än 0,66 (0,8/(1 + `slack_factor` )) avslutas.
* `evaluation_interval`: (valfritt) frekvensen för att tillämpa principen
* `delay_evaluation`: (valfritt) fördröjer den första princip utvärderingen för ett angivet antal intervall


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall när mått rapporteras, från och med utvärderings intervallet 5. Alla körningar vars bästa mått är mindre än (1/(1 + 0,1) eller 91% av den bästa körningen avbryts.

### <a name="median-stopping-policy"></a>Median stoppar princip

[Median stopp](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?preserve-view=true&view=azure-ml-py) är en princip för tidig avslutning som baseras på löpande medelvärden av primära mått som rapporteras av körningarna. Den här principen beräknar löpande medelvärden för alla utbildnings körningar och avslutar körningar med primära Mät värden som är lägre än median genomsnittet.

Den här principen använder följande konfigurations parametrar:
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer den första princip utvärderingen för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall som börjar med utvärderings intervallet 5. En körning avslutas med intervallet 5 om det bästa primära måttet är sämre än median värdet för de löpande medelvärdena över intervall 1:5 för alla utbildnings körningar.

### <a name="truncation-selection-policy"></a>Princip för att välja trunkering

[Avtrunkering av markering](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?preserve-view=true&view=azure-ml-py) avbryter en procent andel av de lägsta körnings körningarna i varje utvärderings intervall. Körningarna jämförs med det primära måttet. 

Den här principen använder följande konfigurations parametrar:

* `truncation_percentage`: procent andelen av de lägsta körnings körningarna som avslutas vid varje utvärderings intervall. Ett heltals värde mellan 1 och 99.
* `evaluation_interval`: (valfritt) frekvensen för att tillämpa principen
* `delay_evaluation`: (valfritt) fördröjer den första princip utvärderingen för ett angivet antal intervall


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall som börjar med utvärderings intervallet 5. En körning avslutas med intervall 5 om dess prestanda vid intervall 5 är i den lägsta 20% av prestandan för alla körningar vid intervallet 5.

### <a name="no-termination-policy-default"></a>Ingen avslutnings princip (standard)

Om ingen princip har angetts, kommer tjänsten för egenskaps justering att låta all utbildning köras på Slutför.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Plocka en princip för tidig avslutning

* För en restriktiv princip som ger besparingar utan att avsluta löftes jobb, bör du överväga en princip för att stoppa en median med `evaluation_interval` 1 och `delay_evaluation` 5. Dessa är restriktiva inställningar som kan ge cirka 25%-35% besparingar utan förlust av primärt mått (baserat på våra utvärderings data).
* Om du vill ha mer aggressiva besparingar använder du bandit-principen med en mindre tillåten slack eller trunkering princip med en större trunkning i procent.

## <a name="allocate-resources"></a>Allokera resurser

Kontrol lera resurs budgeten genom att ange det maximala antalet körningar.

* `max_total_runs`: Högsta antal körnings körningar. Måste vara ett heltal mellan 1 och 1000.
* `max_duration_minutes`: (valfritt) maximal varaktighet, i minuter, för justerings experimentet för den här parametern. Körs efter att den här varaktigheten har annullerats.

>[!NOTE] 
>Om både `max_total_runs` och `max_duration_minutes` anges, avbryts experimentets inställnings experiment när den första av dessa två tröskelvärden uppnås.

Dessutom kan du ange det maximala antalet körnings körningar som ska köras samtidigt under justerings sökningen för din parameter.

* `max_concurrent_runs`: (valfritt) maximalt antal körningar som kan köras samtidigt. Om detta inte anges körs alla igång parallellt. Om det anges måste det vara ett heltal mellan 1 och 100.

>[!NOTE] 
>Antalet samtidiga körningar är gated på de resurser som är tillgängliga i det angivna beräknings målet. Se till att Compute-målet har de tillgängliga resurserna för önskad samtidighet.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Den här koden konfigurerar experimentet för inställning av parameter till att använda maximalt 20 Totalt antal körningar som kör fyra konfigurationer i taget.

## <a name="configure-experiment"></a>Konfigurera experiment

Ange följande om du vill [Konfigurera ett experiment för inställning av en parameter](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?preserve-view=true&view=azure-ml-py) :
* Det definierade Sök utrymmet för den här ytan
* Principen för tidig avslutning
* Primärt mått
* Resurs beläggnings inställningar
* ScriptRunConfig `src`

ScriptRunConfig är det utbildnings skript som kommer att köras med exempel på sina grundparametrar. Den definierar resurserna per jobb (enstaka eller flera noder) och det beräknings mål som ska användas.

> [!NOTE]
>Det beräknings mål som anges i `src` måste ha tillräckligt med resurser för att uppfylla samtidiga nivåer. Mer information om ScriptRunConfig finns i [Konfigurera inlärnings körningar](how-to-set-up-training-targets.md).

Konfigurera ditt inställnings experiment för en parameter:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Skicka experiment

När du har definierat konfigurationen för konfiguration av den egna parametern, [skicka in experimentet](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truesubmit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Varm kom igång med ditt inställnings experiment för en parameter (valfritt)

Att hitta bästa möjliga parameter värden för din modell kan vara en iterativ process. Du kan återanvända kunskap från de fem föregående körningarna för att påskynda justeringen av en parameter.


Varm start hanteras på olika sätt beroende på samplings metoden:
- **Bayesian-sampling**: försök från föregående körning används som tidigare kunskap för att välja nya prover och för att förbättra det primära måttet.
- Stick **provs-eller** **Rutnäts sampling**: tidiga uppsägningar använder information från tidigare körningar för att fastställa dåligt utförda körningar. 

Ange listan över överordnade körningar som du vill komma igång med.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Om du avbryter ett justerings experiment på en parameter kan du återuppta inlärnings körningar från den senaste kontroll punkten. Ditt utbildnings skript måste dock hantera kontroll punkts logik.

Utbildnings körningen måste använda samma konfiguration för den här parametern och montera mapparna utdata. Övnings skriptet måste godkänna `resume-from` argumentet, som innehåller de kontroll punkter eller modeller som du kan använda för att återuppta utbildningen. Du kan återuppta enskilda inlärnings körningar med följande kodfragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Du kan konfigurera ditt inställnings experiment för att komma igång med ett tidigare experiment eller återuppta enskilda inlärnings körningar med valfria parametrar `resume_from` och `resume_child_runs` i konfigurationen:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=src,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Visualisera experiment

Använd [widgeten Notebook](/python/api/azureml-widgets/azureml.widgets.rundetails?preserve-view=true&view=azure-ml-py) för att visualisera förloppet för din utbildning. Följande kodfragment visualiserar alla dina för inställnings justeringar på en plats i en Jupyter Notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Den här koden visar en tabell med information om inlärnings körningarna för var och en av konfigurationerna för de olika parametrarna.

![justerings tabell för grundparameter](./media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

Du kan också visualisera prestanda för var och en av körningarna när inlärningen fortskrider. 

![ritytans justerings område](./media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

Du kan visuellt identifiera korrelationen mellan prestanda och värden för enskilda egenskaper med hjälp av en parallell Koordinats kurva. 

[![parametrar för att justera parallella koordinater](./media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

Du kan också visualisera alla dina inställnings justeringar för din parameter i Azure-webbportalen. Mer information om hur du visar ett experiment i portalen finns i [så här spårar du experiment](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Hitta den bästa modellen

När alla konfigurations inställningar för den här inställningen har slutförts, identifierar du bästa möjliga konfiguration och värden för den andra parametern:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Exempel på Notebook
Referera till träna-Real-parameter-* Notebooks i den här mappen:
* [How-to-use-azureml/ml-Framework](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra ett experiment](how-to-track-experiments.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)