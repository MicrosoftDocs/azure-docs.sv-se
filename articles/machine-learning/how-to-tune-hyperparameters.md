---
title: Justera en modell med hyperparametrar
titleSuffix: Azure Machine Learning
description: Automatisera justering av hyperparametrar för djupinlärnings- och maskininlärningsmodeller med hjälp av Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ec01561c5c44c850b32187629552b1bdb99537e7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819186"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Hyperparameterjustering av en modell med Azure Machine Learning

Automatisera effektiv justering av hyperparametrar med hjälp Azure Machine Learning [HyperDrive-paketet](/python/api/azureml-train-core/azureml.train.hyperdrive). Lär dig hur du utför de steg som krävs för att finjustera hyperparametrar med [Azure Machine Learning SDK:](/python/api/overview/azure/ml/)

1. Definiera parametersökutrymmet
1. Ange ett primärt mått som ska optimeras  
1. Ange princip för tidig avslutning för körningar med låg prestanda
1. Skapa och tilldela resurser
1. Starta ett experiment med den definierade konfigurationen
1. Visualisera träningskörningarna
1. Välj den bästa konfigurationen för din modell

## <a name="what-is-hyperparameter-tuning"></a>Vad är justering av hyperparametrar?

**Hyperparametrar är justerbara** parametrar som gör att du kan styra modellträningsprocessen. Med neurala nätverk bestämmer du till exempel antalet dolda lager och antalet noder i varje lager. Modellens prestanda är kraftigt beroende av hyperparametrar.

 **Justering av hyperparameter,** även kallat optimering av **hyperparameter,** är en process för att hitta konfigurationen av hyperparametrar som ger bästa möjliga prestanda. Processen är vanligtvis beräkningsmässigt dyr och manuell.

Azure Machine Learning kan du automatisera justering av hyperparametrar och köra experiment parallellt för att effektivt optimera hyperparametrar.


## <a name="define-the-search-space"></a>Definiera sökutrymmet

Finjustera hyperparametrar genom att utforska intervallet med värden som definierats för varje hyperparameter.

Hyperparametrar kan vara diskreta eller kontinuerliga och har en fördelning av värden som beskrivs av ett [parameteruttryck](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions).

### <a name="discrete-hyperparameters"></a>Diskreta hyperparametrar

Diskreta hyperparametrar anges som en `choice` bland diskreta värden. `choice` kan vara:

* ett eller flera kommaavgränsade värden
* ett `range` -objekt
* godtyckliga `list` objekt


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

I det här fallet tar ett av `batch_size` värdena [16, 32, 64, 128] och ett av `number_of_hidden_layers` värdena [1, 2, 3, 4].

Följande avancerade diskreta hyperparametrar kan också anges med hjälp av en distribution:

* `quniform(low, high, q)` - Returnerar ett värde som round(uniform(low, high) /q) * q
* `qloguniform(low, high, q)` – Returnerar ett värde som round(exp(uniform(low, high)) /q) * q
* `qnormal(mu, sigma, q)` - Returnerar ett värde som round(normal(mu, sigma) /q) * q
* `qlognormal(mu, sigma, q)` – Returnerar ett värde som round(exp(normal(mu, sigma)) /q) * q

### <a name="continuous-hyperparameters"></a>Kontinuerliga hyperparametrar 

Kontinuerliga hyperparametrar anges som en distribution över ett kontinuerligt värdeintervall:

* `uniform(low, high)` – Returnerar ett värde som är jämnt fördelat mellan lågt och högt
* `loguniform(low, high)` – Returnerar ett värde som ritats enligt exp(uniform(low, high)) så att logaritmen för returvärdet är jämnt fördelat
* `normal(mu, sigma)` – Returnerar ett verkligt värde som normalt distribueras med medelvärdet mu och standardavvikelsen sigma
* `lognormal(mu, sigma)` – Returnerar ett värde som ritats enligt exp(normal(mu, sigma)) så att logaritmen för returvärdet normalt distribueras

Ett exempel på en definition av ett parameterutrymme:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Den här koden definierar ett sökutrymme med två parametrar – `learning_rate` och `keep_probability` . `learning_rate` har en normalfördelning med medelvärdet 10 och en standardavvikelse på 3. `keep_probability` har en enhetlig fördelning med ett minsta värde på 0,05 och ett maxvärde på 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Sampling av hyperparameterutrymmet

Ange vilken parametersamplingsmetod som ska användas över hyperparameterutrymmet. Azure Machine Learning stöder följande metoder:

* Stickprov
* Rutnätssampling
* Bayesiansk sampling

#### <a name="random-sampling"></a>Stickprov

[Slumpmässig sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) stöder diskreta och kontinuerliga hyperparametrar. Den stöder tidig avslutning av körningar med låg prestanda. Vissa användare gör en inledande sökning med slumpmässig sampling och förfinar sedan sökutrymmet för att förbättra resultaten.

Vid slumpmässig sampling väljs hyperparametervärden slumpmässigt från det definierade sökutrymmet. 

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

#### <a name="grid-sampling"></a>Rutnätssampling

[Rutnätssampling](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) stöder diskreta hyperparametrar. Använd rutnätssampling om du kan budgetera för att göra en fullständig sökning i sökutrymmet. Stöder tidig avslutning av körningar med låg prestanda.

Rutnätssampling gör en enkel rutnätssökning över alla möjliga värden. Rutnätssampling kan bara användas `choice` med hyperparametrar. Följande utrymme har till exempel sex exempel:

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

[Bayesiansk sampling](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) baseras på den bayesiska optimeringsalgoritmen. Den väljer exempel baserat på hur tidigare exempel gjorde, så att nya exempel förbättrar det primära måttet.

Bayesiansk sampling rekommenderas om du har tillräckligt med budget för att utforska hyperparameterutrymmet. För bästa resultat rekommenderar vi ett maximalt antal körningar som är större än eller lika med 20 gånger antalet hyperparametrar som justeras. 

Antalet samtidiga körningar påverkar justeringsprocessens effektivitet. Ett mindre antal samtidiga körningar kan leda till bättre samplingskonvergens, eftersom den mindre graden av parallellitet ökar antalet körningar som drar nytta av tidigare slutförda körningar.

Bayesiansk sampling stöder `choice` endast `uniform` distributioner av , `quniform` och över sökutrymmet.

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

Ange det [primära mått som](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) du vill optimera hyperparameterjusteringen för. Varje träningskörning utvärderas för det primära måttet. Principen för tidig avslutning använder det primära måttet för att identifiera körningar med låg prestanda.

Ange följande attribut för ditt primära mått:

* `primary_metric_name`: Namnet på det primära måttet måste exakt matcha namnet på måttet som loggas av träningsskriptet
* `primary_metric_goal`: Det kan vara `PrimaryMetricGoal.MAXIMIZE` antingen `PrimaryMetricGoal.MINIMIZE` eller och avgöra om det primära måttet maximeras eller minimeras när körningarna utvärderas. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Det här exemplet maximerar "noggrannheten".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Loggmått för justering av hyperparametrar

Träningsskriptet för din modell måste **logga** det primära måttet under modellträningen så att HyperDrive kan komma åt det för justering av hyperparametrar.

Logga det primära måttet i träningsskriptet med följande exempelfragment:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Träningsskriptet beräknar och `val_accuracy` loggar det som det primära måttet "precision". Varje gång måttet loggas tas det emot av hyperparameterjusteringstjänsten. Det är upp till dig att fastställa rapporteringsfrekvensen.

Mer information om loggningsvärden i modellträningskörningar finns i [Aktivera loggning i Azure ML-träningskörningar.](how-to-log-view-metrics.md)

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Ange princip för tidig avslutning

Avsluta automatiskt dåligt presterande körningar med en princip för tidig avslutning. Tidig avslutning förbättrar beräkningseffektiviteten.

Du kan konfigurera följande parametrar som styr när en princip tillämpas:

* `evaluation_interval`: frekvensen för att tillämpa principen. Varje gång träningsskriptet loggar det primära måttet räknas som ett intervall. En `evaluation_interval` av 1 tillämpar principen varje gång träningsskriptet rapporterar det primära måttet. En `evaluation_interval` av 2 tillämpar principen varannan gång. Om inget anges anges `evaluation_interval` till 1 som standard.
* `delay_evaluation`: fördröjer den första principutvärderingen för ett angivet antal intervall. Det här är en valfri parameter som förhindrar förtidsavslut av träningskörningar genom att tillåta att alla konfigurationer körs under ett minsta antal intervall. Om det anges tillämpas principen varje multipel av evaluation_interval som är större än eller lika med delay_evaluation.

Azure Machine Learning stöder följande principer för tidig avslutning:
* [Princip för et](#bandit-policy)
* [Median för stoppprincip](#median-stopping-policy)
* [Princip för val av trunkering](#truncation-selection-policy)
* [Ingen avslutningsprincip](#no-termination-policy-default)


### <a name="bandit-policy"></a>Så här fungerar principen

[Principen baseras](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) på Slack-faktor/slack-belopp och utvärderingsintervall. Vid körningen avslutas körningen när det primära måttet inte ligger inom den angivna slack-faktorn/slack-mängden för den mest lyckade körningen.

> [!NOTE]
> Bayesiansk sampling stöder inte tidig avslutning. När du använder Bayesiansk sampling anger du `early_termination_policy = None` .

Ange följande konfigurationsparametrar:

* `slack_factor` eller `slack_amount` : slack tillåts med avseende på den bäst presterande träningskörningen. `slack_factor` anger det tillåtna slack som ett förhållande. `slack_amount` anger slack som en absolut mängd i stället för ett förhållande.

    Tänk dig till exempel att en Princip tillämpas med intervall 10. Anta att den bäst presterande körningen med intervall 10 rapporterade ett primärt mått är 0,8 med målet att maximera det primära måttet. Om principen anger 0,2 avslutas alla träningskörningar vars bästa mått med intervall 10 är mindre än `slack_factor` 0,66 (0,8/(1+ `slack_factor` )).
* `evaluation_interval`: (valfritt) frekvensen för att tillämpa principen
* `delay_evaluation`: (valfritt) fördröjer den första principutvärderingen för ett angivet antal intervall


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall när mått rapporteras, med början vid utvärderingsintervallet 5. Körningar vars bästa mått är mindre än (1/(1+0.1) eller 91 % av den bäst presterande körningen avslutas.

### <a name="median-stopping-policy"></a>Median för stoppprincip

[Medianstopp](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) är en princip för tidig avslutning som baseras på löpande medelvärden av primära mått som rapporteras av körningarna. Den här principen beräknar medelvärden för alla träningskörningar och stoppar körningar vars primära måttvärde är sämre än medianvärdet för medelvärdena.

Den här principen använder följande konfigurationsparametrar:
* `evaluation_interval`: frekvensen för att tillämpa principen (valfri parameter).
* `delay_evaluation`: fördröjer den första principutvärderingen för ett angivet antal intervall (valfri parameter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall som börjar vid utvärderingsintervallet 5. En körning stoppas med intervall 5 om det bästa primära måttet är sämre än medianvärdet för de löpande medelvärdena över intervallen 1:5 för alla träningskörningar.

### <a name="truncation-selection-policy"></a>Princip för val av trunkering

[Trunkeringsvalet avbryter](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) en procentandel av de lägst presterande körningarna vid varje utvärderingsintervall. Körningar jämförs med hjälp av det primära måttet. 

Den här principen använder följande konfigurationsparametrar:

* `truncation_percentage`: procentandelen av de lägst presterande körningarna som avslutas vid varje utvärderingsintervall. Ett heltalsvärde mellan 1 och 99.
* `evaluation_interval`: (valfritt) frekvensen för att tillämpa principen
* `delay_evaluation`: (valfritt) försenar den första principutvärderingen för ett angivet antal intervall


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

I det här exemplet tillämpas principen för tidig avslutning vid varje intervall som börjar vid utvärderingsintervallet 5. En körning avslutas med intervall 5 om dess prestanda vid intervall 5 är på den lägsta 20 % av prestandan för alla körningar med intervall 5.

### <a name="no-termination-policy-default"></a>Ingen avslutningsprincip (standard)

Om ingen princip anges låter hyperparameterjusteringstjänsten alla träningskörningar köras tills de har slutförts.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Välja en princip för tidig avslutning

* För en försiktig policy som ger besparingar utan att avsluta löftet bör du överväga en median för stoppprincip med `evaluation_interval` 1 och `delay_evaluation` 5. Det här är en försiktig inställning som kan ge besparingar på cirka 25–35 % utan förlust av primärt mått (baserat på våra utvärderingsdata).
* För mer aggressiv besparing kan du använda Grupprincip med en mindre tillåtna slack- eller trunkeringsvalsprincip med en större trunkeringsprocent.

## <a name="create-and-assign-resources"></a>Skapa och tilldela resurser

Kontrollera resursbudgeten genom att ange det maximala antalet träningskörningar.

* `max_total_runs`: Maximalt antal träningskörningar. Måste vara ett heltal mellan 1 och 1000.
* `max_duration_minutes`: (valfritt) Maximal varaktighet, i minuter, för hyperparameterjusteringsexperimentet. Körs efter att den här varaktigheten har avbrutits.

>[!NOTE] 
>Om både `max_total_runs` och `max_duration_minutes` anges avslutas hyperparameterjusteringsexperimentet när det första av dessa två tröskelvärden nås.

Ange dessutom det maximala antalet träningskörningar som ska köras samtidigt under sökningen med hyperparameterjustering.

* `max_concurrent_runs`: (valfritt) Maximalt antal körningar som kan köras samtidigt. Om inget anges startar alla körningar parallellt. Om detta anges måste vara ett heltal mellan 1 och 100.

>[!NOTE] 
>Antalet samtidiga körningar är begränsat till de resurser som är tillgängliga i det angivna beräkningsmålet. Kontrollera att beräkningsmålet har de tillgängliga resurserna för önskad samtidighet.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Den här koden konfigurerar experimentet för justering av hyperparametrar till att använda högst 20 körningar, vilket kör fyra konfigurationer i taget.

## <a name="configure-hyperparameter-tuning-experiment"></a>Konfigurera experimentet för justering av hyperparameter

Ange [följande för att konfigurera](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) ditt experiment för justering av hyperparametrar:
* Det definierade sökutrymmet för hyperparametrar
* Din princip för tidig avslutning
* Det primära måttet
* Inställningar för resursallokering
* ScriptRunConfig `script_run_config`

ScriptRunConfig är det träningsskript som körs med de provade hyperparametrarna. Den definierar resurserna per jobb (en eller flera noder) och beräkningsmålet som ska användas.

> [!NOTE]
>Beräkningsmålet som används `script_run_config` i måste ha tillräckligt med resurser för att uppfylla samtidighetsnivån. Mer information om ScriptRunConfig finns i [Konfigurera träningskörningar.](how-to-set-up-training-targets.md)

Konfigurera ditt experiment för justering av hyperparametrar:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

anger `HyperDriveConfig` de parametrar som skickas till `ScriptRunConfig script_run_config` . skickar `script_run_config` i sin tur parametrar till träningsskriptet. Kodfragmentet ovan är från exempelanteckningsboken [Train (Träna) och hyperparameter tune (hyperparameterjustering) och distribuera med PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch). I det här exemplet `learning_rate` `momentum` justeras parametrarna och . Tidig stopp av körningar bestäms av en , som stoppar en körning vars primära mått `BanditPolicy` hamnar utanför `slack_factor` (se [Klassreferens förPolicy).](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy) 

Följande kod från exemplet visar hur de finjusterade värdena tas emot, parsas och skickas till träningsskriptens `fine_tune_model` funktion:

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Varje hyperparameterkörning startar om träningen från grunden, inklusive återskapande av modellen och _alla datainläsare._ Du kan minimera kostnaden genom att använda en Azure Machine Learning pipeline eller en manuell process för att göra så mycket förberedelse av data som möjligt innan träningen körs. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Skicka experiment för justering av hyperparameter

När du har definierat konfigurationen för hyperparameterjusteringen [skickar du experimentet](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Justering av hyperparameter för varmstart (valfritt)

Att hitta de bästa hyperparametervärdena för din modell kan vara en iterativ process. Du kan återanvända kunskap från de fem föregående körningarna för att påskynda finjustering av hyperparametrar.

Varm start hanteras på olika sätt beroende på samplingsmetod:
- **Bayesiansk sampling:** Studier från föregående körning används som tidigare kunskaper för att välja nya exempel och för att förbättra det primära måttet.
- **Slumpmässig sampling eller** **rutnätssampling:** Tidig avslutning använder kunskap från tidigare körningar för att fastställa körningar med dåligt prestanda. 

Ange listan över överordnade körningar som du vill starta från.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Om ett experiment med hyperparameterjustering avbryts kan du återuppta träningskörningar från den senaste kontrollpunkten. Träningsskriptet måste dock hantera kontrollpunktslogik.

Träningskörningen måste använda samma hyperparameterkonfiguration och monterade utdatamapparna. Träningsskriptet måste acceptera argumentet som innehåller kontrollpunkten eller modellfilerna `resume-from` som träningskörningen ska återupptas från. Du kan återuppta enskilda träningskörningar med hjälp av följande kodfragment:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

Du kan konfigurera hyperparameterjusteringsexperimentet till att börja om från ett tidigare experiment eller återuppta enskilda träningskörningar med hjälp av de valfria parametrarna `resume_from` `resume_child_runs` och i -konfiguration:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Visualisera körningar av finjustering av hyperparametrar

Du kan visualisera dina hyperparameterjusteringskörningar i Azure Machine Learning-studio, eller så kan du använda en notebook-widget.

### <a name="studio"></a>Studio

Du kan visualisera alla dina hyperparameterjusteringskörningar i [Azure Machine Learning-studio](https://ml.azure.com). Mer information om hur du visar ett experiment i portalen finns i [Visa körningsposter i studio](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal).

- **Måttdiagram:** Den här visualiseringen spårar de mått som loggas för varje underordnad hyperdrive-körning under hyperparameterjusteringens varaktighet. Varje rad representerar en underordnad körning och varje punkt mäter det primära måttvärdet vid den iterationen av körningen.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Diagram över mått för justering av hyperparameter":::

- **Diagram över parallella koordinater:** Den här visualiseringen visar korrelationen mellan primärmåttsprestanda och enskilda hyperparametervärden. Diagrammet är interaktivt via förflyttning av axlar (klicka och dra med axeletiketten) och genom att markera värden över en enda axel (klicka och dra lodrätt längs en enda axel för att markera ett intervall med önskade värden).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Diagram över parallella koordinater för justering av hyperparameter":::

- **2-dimensionellt punktdiagram:** Den här visualiseringen visar korrelationen mellan två enskilda hyperparametrar tillsammans med deras associerade primära måttvärde.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="2-dimensionellt punktdiagram för justering av hyparameter":::

- **3-dimensionellt punktdiagram:** Den här visualiseringen är samma som 2D men tillåter tre hyperparameterdimensioner för korrelation med det primära måttvärdet. Du kan också klicka och dra för att omorientera diagrammet om du vill visa olika korrelationer i 3D-utrymme.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="3-dimensionellt punktdiagram för justering av hyparameter":::

### <a name="notebook-widget"></a>Anteckningsbok-widget

Använd [Notebook-widgeten](/python/api/azureml-widgets/azureml.widgets.rundetails) för att visualisera förloppet för dina träningskörningar. Följande kodfragment visualiserar alla finjusteringar av hyperparametrar på ett och samma ställe i en Jupyter Notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Den här koden visar en tabell med information om träningskörningarna för var och en av hyperparameterkonfigurationerna.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Justeringstabell för hyperparameter":::

Du kan också visualisera prestanda för var och en av körningarna när träningen fortskrider.

## <a name="find-the-best-model"></a>Hitta den bästa modellen

När alla hyperparameterjusteringskörningar har slutförts identifierar du de konfigurations- och hyperparametervärden som presterar bäst:

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

## <a name="sample-notebook"></a>Exempelanteckningsbok

Se notebook-filer för train-hyperparameter-* i den här mappen:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg
* [Spåra ett experiment](how-to-log-view-metrics.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
