---
title: Utveckla med AutoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: 'Lär dig hur du konfigurerar en utvecklings miljö i Azure Machine Learning och Azure Databricks. Använd Azure ML SDK: er för Databricks och Databricks med AutoML.'
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: daeb0d666c0dfb8306663da1d8d59dfba6adc359
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516931"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Konfigurera en utvecklings miljö med Azure Databricks och AutoML i Azure Machine Learning 

Lär dig hur du konfigurerar en utvecklings miljö i Azure Machine Learning som använder Azure Databricks och automatiserad ML.

Azure Databricks är perfekt för att köra storskaliga arbets flöden för storskalig dator inlärning på den skalbara Apache Sparks plattformen i Azure-molnet. Den ger en samarbets Notebook-baserad miljö med ett processor-eller GPU-baserat beräknings kluster.

Information om andra miljöer för maskin inlärnings utveckling finns i [Konfigurera python-utvecklings miljö](how-to-configure-environment.md).


## <a name="prerequisite"></a>Förutsättning

Azure Machine Learning arbets yta. Om du inte har någon kan du skapa en Azure Machine Learning arbets yta via [Azure Portal](how-to-manage-workspace.md), [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)och [Azure Resource Manager mallar](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks med Azure Machine Learning och AutoML

Azure Databricks integreras med Azure Machine Learning och dess AutoML-funktioner. 

Du kan använda Azure Databricks:

+ För att träna en modell med Spark-MLlib och distribuera modellen till ACI/AKS.
+ Med [automatiserade maskin inlärnings](concept-automated-ml.md) funktioner med hjälp av Azure ml SDK.
+ Som ett beräknings mål från en [Azure Machine Learning pipeline](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Konfigurera ett Databricks-kluster

Skapa ett [Databricks-kluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Vissa inställningar gäller endast om du installerar SDK för automatisk maskin inlärning på Databricks.

**Det tar några minuter att skapa klustret.**

Använd de här inställningarna:

| Inställning |Gäller för| Värde |
|----|---|---|
| Klusternamn |alltid| yourclustername |
| Databricks Runtime version |alltid| Runtime 7,1 (Scala 2,21, Spark 3.0.0) – inte ML|
| Python-version |alltid| 3 |
| Typ av arbetare <br>(fastställer Max antal samtidiga iterationer) |Automatisk ML<br>Only| Minnesoptimerade VM-prioritet |
| Arbetare |alltid| 2 eller senare |
| Aktivera automatisk skalning |Automatisk ML<br>Only| Avmarkera |

Vänta tills klustret körs innan du fortsätter.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>Lägg till Azure ML SDK i Databricks

När klustret har körts skapar du [ett bibliotek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) för att bifoga lämpligt Azure Machine Learning SDK-paket till klustret. 

Om du vill använda automatisk ML går du vidare till [lägga till Azure ml SDK med AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Högerklicka på den aktuella arbetsyte-mappen där du vill lagra biblioteket. Välj **skapa**  >  **bibliotek**.
    
    > [!TIP]
    > Om du har en gammal SDK-version avmarkerar du den från klustrets installerade bibliotek och flyttar till pappers korgen. Installera den nya SDK-versionen och starta om klustret. Om det uppstår ett problem efter omstarten, tar du bort och återansluter klustret.

1. Välj följande alternativ (inga andra SDK-installationer stöds)

   |Tillägg för SDK- &nbsp; paket &nbsp;|Källa|PyPi &nbsp; namn&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |För Databricks| Överför python-ägg eller PyPI | azureml – SDK [databricks]|

   > [!WARNING]
   > Inga andra SDK-tillägg kan installeras. Välj bara alternativet [ `databricks` ].

   * Välj inte **Anslut automatiskt till alla kluster**.
   * Välj  **Anslut** bredvid klustrets namn.

1. Övervaka fel tills status ändras till **kopplat**, vilket kan ta flera minuter.  Om det här steget Miss lyckas:

   Försök att starta om klustret genom att:
   1. Välj **kluster** i den vänstra rutan.
   1. I tabellen väljer du ditt kluster namn.
   1. På fliken **bibliotek** väljer du **starta om**.

   En lyckad installation ser ut så här: 

  ![Azure Machine Learning SDK för Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Lägg till Azure ML SDK med AutoML till Databricks
Om klustret skapades med Databricks Runtime 7,1 eller senare (*inte* ml) kör du följande kommando i den första cellen i antecknings boken för att installera AML SDK.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
För Databricks Runtime 7,0 och lägre installerar du Azure Machine Learning SDK med hjälp av [init-skriptet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>Konfigurations inställningar för AutoML

När du använder Azure Databricks lägger du till följande parametrar i AutoML config:

- ```max_concurrent_iterations``` baseras på antalet arbetsnoder i klustret.
- ```spark_context=sc``` baseras på standard-Spark-kontexten.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>ML bärbara datorer som fungerar med Azure Databricks

Prova:
+ Även om många exempel antecknings böcker är tillgängliga **fungerar bara [dessa exempel antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) med Azure Databricks.**

+ Importera de här exemplen direkt från din arbets yta. Se nedan: ![ Välj import ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ import panel](./media/how-to-configure-environment/azure-db-import.png)

+ Lär dig hur du [skapar en pipeline med Databricks som inlärnings beräkning](./how-to-create-machine-learning-pipelines.md).

## <a name="troubleshooting"></a>Felsökning

* **Det gick inte att installera paket**

    Azure Machine Learning SDK-installationen Miss lyckas på Azure Databricks när fler paket är installerade. Vissa paket, till exempel `psutil` , kan orsaka konflikter. Undvik installations fel genom att installera paket genom att frysa biblioteks versionen. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning SDK. Du kan också uppleva det här problemet med andra bibliotek. Exempel:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Du kan också använda init-skript om du behåller problem med att installera mot python-bibliotek. Den här metoden stöds inte officiellt. Mer information finns i [kluster omfång init-skript](/azure/databricks/clusters/init-scripts#cluster-scoped-init-scripts).

* **Import fel: det går inte att importera namnet `Timedelta` från `pandas._libs.tslibs`**: om du ser det här felet när du använder automatisk maskin inlärning, kör du två följande rader i din bärbara dator:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Import fel: ingen modul med namnet ' Pandas. Core. index '**: om du ser det här felet när du använder automatisk maskin inlärning:

    1. Kör det här kommandot för att installera två paket i Azure Databricks klustret:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Koppla från och återanslut sedan klustret till din bärbara dator.
    
    Om de här stegen inte löser problemet kan du försöka med att starta om klustret.

* **FailToSendFeather**: om du ser ett `FailToSendFeather` fel när du läser data på Azure Databricks kluster, se följande lösningar:
    
    * Uppgradera `azureml-sdk[automl]` paketet till den senaste versionen.
    * Lägg till `azureml-dataprep` version 1.1.8 eller senare.
    * Lägg till `pyarrow` version 0,11 eller senare.
  

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](tutorial-train-models-with-aml.md) på Azure Machine Learning med MNIST-datauppsättningen.
- Se [Azure Machine Learning SDK för python-referens](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).