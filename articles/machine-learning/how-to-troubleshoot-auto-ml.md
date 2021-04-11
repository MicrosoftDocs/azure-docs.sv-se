---
title: Felsöka automatiserade ML-experiment
titleSuffix: Azure Machine Learning
description: Lär dig hur du felsöker och löser problem i automatiserade maskin inlärnings experiment.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 2e6e4e468adec76d50242b31922e4db1c7e910ab
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210490"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Felsöka automatiserade ML-experiment i python

I den här hand boken får du lära dig att identifiera och lösa kända problem i automatiserade maskin inlärnings experiment med [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro).

## <a name="version-dependencies"></a>Versions beroenden

**`AutoML` beroenden till nyare paket versioner bryter kompatibiliteten**. När SDK-versionen har 1.13.0 laddas inte modeller i äldre SDK: er på grund av inkompatibilitet mellan äldre versioner som är fästa i tidigare `AutoML` paket och nyare versioner som fästs idag.

Oväntade fel, till exempel:

* Modulen hittade inte fel som,

  `No module named 'sklearn.decomposition._truncated_svd`

* Importera fel som,

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Attributändringar som,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

Lösningarna är beroende av din `AutoML` SDK-utbildnings version:

* Om din `AutoML` SDK-tränings version är större än 1.13.0, behöver du `pandas == 0.25.1` och `scikit-learn==0.22.1` .

    * Om det finns en versions konflikt uppgraderar du scikit-lära och/eller Pandas för att rätta till versionen med följande,

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Om din `AutoML` SDK-tränings version är mindre än eller lika med 1.12.0, behöver du `pandas == 0.23.4` och `sckit-learn==0.20.3` .
  * Om det finns en versions konflikt kan du nedgradera scikit – lära och/eller Pandas för att rätta till version med följande,
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Konfiguration

`AutoML` paket ändringar sedan version 1.0.76 kräver att den tidigare versionen avinstallerades innan du uppdaterar till den nya versionen.

* **`ImportError: cannot import name AutoMLConfig`**

    Om det här felet uppstår efter att du har uppgraderat från en SDK-version före v-1.0.76 till v 1.0.76 eller senare, löser du felet genom att köra: `pip uninstall azureml-train automl` och sedan `pip install azureml-train-automl` . Skriptet automl_setup. cmd gör detta automatiskt.

* **automl_setup Miss lyckas**

  * I Windows kör du automl_setup från en Anaconda-prompt. [Installera Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Se till att Conda 64-bitars version 4.4.10 eller senare är installerad. Du kan kontrol lera biten med `conda info` kommandot. `platform`Ska vara `win-64` för Windows eller `osx-64` Mac. Om du vill kontrol lera versionen använder du kommandot `conda -V` . Om du har installerat en tidigare version kan du uppdatera den med hjälp av kommandot: `conda update conda` . Så här kontrollerar du 32-bitars genom att köra 

  * Se till att Conda har installerats. 

  * Linux `gcc: error trying to exec 'cc1plus'`

    1. Om `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` felet uppstår installerar du gcc build-verktyg för din Linux-distribution. Använd till exempel kommandot på Ubuntu `sudo apt-get install build-essential` .

    1. Skicka ett nytt namn som den första parametern till automl_setup för att skapa en ny Conda-miljö. Visa befintliga Conda-miljöer med `conda env list` och ta bort dem med `conda env remove -n <environmentname>` .

* **automl_setup_linux. sh Miss lyckas**: om automl_setup_linus. sh Miss lyckas på Ubuntu Linux med felet: `unable to execute 'gcc': No such file or directory`

  1. Se till att de utgående portarna 53 och 80 är aktiverade. På en virtuell Azure-dator kan du göra detta från Azure Portal genom att välja den virtuella datorn och klicka på **nätverk**.
  1. Kör kommandot: `sudo apt-get update`
  1. Kör kommandot: `sudo apt-get install build-essential --fix-missing`
  1. Kör `automl_setup_linux.sh` igen

* **konfiguration. ipynb Miss lyckas**:

  * För lokala Conda kontrollerar du först att `automl_setup` har körts.
  * Kontrol lera att subscription_id är korrekt. Hitta subscription_id i Azure Portal genom att välja alla tjänster och sedan prenumerationer. Tecknen "<" och ">" ska inte tas med i subscription_id svärdet. Har till exempel `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` det giltiga formatet.
  * Se till att deltagar-eller ägar åtkomst till prenumerationen.
  * Kontrol lera att regionen är en av de regioner som stöds:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` `southcentralus` .
  * Se till att du har åtkomst till regionen med hjälp av Azure Portal.
  
* **Workspace.from_config Miss lyckas**:

  Om anropet `ws = Workspace.from_config()` Miss lyckas:

  1. Se till att konfigurationen. ipynb Notebook har körts.
  1. Om antecknings boken körs från en mapp som inte finns under den mapp där `configuration.ipynb` kördes, kopierar du mappen aml_config och filen config.jsden innehåller till den nya mappen. Workspace.from_config läser config.jsför mappen Notebook eller den överordnade mappen.
  1. Om en ny prenumeration, resurs grupp, arbets yta eller region används, se till att du kör `configuration.ipynb` antecknings boken igen. Att ändra config.jsdirekt fungerar bara om arbets ytan redan finns i den angivna resurs gruppen under den angivna prenumerationen.
  1. Om du vill ändra region ändrar du arbets ytan, resurs gruppen eller prenumerationen. `Workspace.create` kommer inte att skapa eller uppdatera en arbets yta om den redan finns, även om den angivna regionen är annorlunda.

## <a name="tensorflow"></a>TensorFlow

Från och med version 1.5.0 av SDK installerar automatiserad Machine Learning inte TensorFlow-modeller som standard. Installera TensorFlow och Använd det med dina automatiserade ML-experiment genom att installera `tensorflow==1.12.0` via `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>Numpy-problem

* **`import numpy` Miss lyckas i Windows**: vissa Windows-miljöer ser ett fel vid inläsning av numpy med den senaste python-versionen 3.6.8. Om du ser det här problemet kan du prova med python version 3.6.7.

* **`import numpy` Miss lyckas**: kontrol lera TensorFlow-versionen i den automatiserade ml Conda-miljön. Versioner som stöds är < 1,13. Avinstallera TensorFlow från miljön om versionen är >= 1,13.

Du kan kontrol lera versionen av TensorFlow och avinstallera på följande sätt:

  1. Starta ett kommando gränssnitt, aktivera Conda-miljön där automatiserade ml-paket är installerade.
  1. Ange `pip freeze` och leta efter `tensorflow` , om den hittas, ska den version som visas vara < 1,13
  1. Om den listade versionen inte är en version som stöds går du till `pip uninstall tensorflow` kommando gränssnittet och anger y för bekräftelse.

## `jwt.exceptions.DecodeError`

Exakt fel meddelande: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

För SDK-versioner <= 1.17.0 kan installationen resultera i en version av PyJWT som inte stöds. Kontrol lera att PyJWT-versionen i den automatiserade ml Conda-miljön är en version som stöds. Det är PyJWT version < 2.0.0.

Du kan kontrol lera versionen av PyJWT på följande sätt:

1. Starta ett kommando gränssnitt och aktivera Conda-miljön där automatiserade ML-paket är installerade.

1. Ange `pip freeze` och leta efter `PyJWT` , om den hittas, ska den version som visas < 2.0.0

Om den listade versionen inte är en version som stöds:

1. Överväg att uppgradera till den senaste versionen av AutoML SDK: `pip install -U azureml-sdk[automl]`

1. Om detta inte är livskraftigt, avinstallera PyJWT från miljön och installera rätt version på följande sätt:

    1. `pip uninstall PyJWT` i kommando tolken och ange `y` bekräftelse.
    1. Installera med `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Se så [här konfigurerar du ett automatiskt ml-experiment med Databricks](how-to-configure-databricks-automl-environment.md#troubleshooting).

## <a name="forecasting-r2-score-is-always-zero"></a>Prognosticering R2-poängen är alltid noll

 Det här problemet uppstår om de utbildnings data som tillhandahålls har tids serier som innehåller samma värde för de sista `n_cv_splits`  +  `forecasting_horizon` data punkterna.

Om det här mönstret förväntas i din tids serie kan du växla det primära måttet till **normaliserat rot genomsnitts fel i roten**.

## <a name="failed-deployment"></a>Misslyckad distribution

 För versioner <= 1.18.0 av SDK kan bas avbildningen som skapats för distribution Miss lyckas med följande fel: `ImportError: cannot import name cached_property from werkzeug` .

  Följande steg kan lösa problemet:

  1. Ladda ned modell paketet
  1. Packa upp paketet
  1. Distribuera med hjälp av unzippade till gångar

## <a name="azure-functions-application"></a>Azure Functions program
  
  Automatisk ML stöder för närvarande inte Azure Functions-program. 

## <a name="sample-notebook-failures"></a>Exempel på bärbara Notebook-problem

 Om en exempel antecknings bok Miss lyckas med ett fel så finns inte egenskapen, metoden eller biblioteket:

* Kontrol lera att rätt kernel har marker ATS i Jupyter Notebook. Kerneln visas i det övre högra hörnet på antecknings sidan. Standardvärdet är *azure_automl*. Kärnan sparas som en del av antecknings boken. Om du växlar till en ny Conda-miljö måste du välja den nya kerneln i antecknings boken.

  * För Azure Notebooks bör det vara python 3,6.
  * För lokala Conda-miljöer bör det vara namnet på den Conda-miljö som du angav i automl_setup.

* För att säkerställa att antecknings boken är för den SDK-version som du använder,
  * Kontrol lera SDK-versionen genom att köra `azureml.core.VERSION` i en Jupyter Notebook cell.
  * Du kan hämta tidigare versioner av exempel antecknings böckerna från GitHub med följande steg:
    1. Välj `Branch` knappen
    1. Navigera till `Tags` fliken
    1. Välj version

## <a name="next-steps"></a>Nästa steg

+ Lär dig mer om [hur du tränar en Regressions modell med automatisk maskin inlärning](tutorial-auto-train-models.md) eller [hur du tränar användning av automatiserad maskin inlärning på en fjär resurs](how-to-auto-train-remote.md).

+ Läs mer om [hur och var du distribuerar en modell](how-to-deploy-and-where.md).
