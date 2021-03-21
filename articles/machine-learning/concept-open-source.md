---
title: Maskin inlärnings integrering med öppen källkod
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder python-ramverk med öppen källkod för att träna, distribuera och hantera maskin inlärnings lösningar från slut punkt till slut punkt i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223082"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Integrering med öppen källkod med Azure Machine Learning projekt

Du kan träna, distribuera och hantera maskin inlärnings processen från slut punkt till slut punkt i Azure Machine Learning med hjälp av python-bibliotek och plattformar med öppen källkod.  Använd utvecklingsverktyg som Jupyter Notebooks och Visual Studio Code för att dra nytta av befintliga modeller och skript i Azure Machine Learning.  

I den här artikeln får du lära dig mer om de här biblioteken och plattformarna med öppen källkod.

## <a name="train-open-source-machine-learning-models"></a>Träna maskin inlärnings modeller med öppen källkod

I övningen för Machine Learning kan du använda algoritmer för dina data för att få en uppgift eller lösa ett problem. Beroende på problemet kan du välja olika algoritmer som bäst passar uppgiften och dina data. Mer information om olika grenar av Machine Learning finns i [artikeln djup inlärning vs Machine Learning](./concept-deep-learning-vs-machine-learning.md) och [Machine Learning algorithm lathund blad](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Bevara data sekretess med differentiell sekretess

Du behöver data för att träna en maskin inlärnings modell. Ibland är data känsliga och det är viktigt att se till att data är säkra och privata. Differentiell sekretess är en teknik för att bevara informationens konfidentialitet i en data uppsättning. Mer information finns i artikeln om att [bevara data integritet](concept-differential-privacy.md). 

Verktyg med differentiell sekretess med öppen källkod som [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) hjälper dig att [bevara data integriteten](how-to-differential-privacy.md) i Azure Machine Learning lösningar.

### <a name="classical-machine-learning-scikit-learn"></a>Klassisk Machine Learning: scikit – lär dig

För utbildnings uppgifter som involverar klassiska Machine Learning-algoritmer, sådan klassificering, klustring och regression kan du använda något som Scikit – lär dig. Information om hur du tränar en blomster klassificerings modell finns i [artikeln om att träna med Scikit – lära](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Neurala-nätverk: PyTorch, TensorFlow, keras

Machine Learning-algoritmer med öppen källkod som kallas neurala-nätverk, en delmängd av Machine Learning, är användbara för att träna djup inlärnings modeller i Azure Machine Learning.

Ramverk för djup inlärning med öppen källkod och instruktions guider är:

 *  [PyTorch](https://github.com/pytorch/pytorch): [träna en bild klassificerings modell för djup inlärning med hjälp av transfer Learning](how-to-train-pytorch.md) 
 *  [TensorFlow](https://github.com/tensorflow/tensorflow): [identifiera handskrivna siffror med TensorFlow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [Bygg ett neurala-nätverk för att analysera avbildningar med keras](how-to-train-keras.md)

Utbildning en djup inlärnings modell från grunden kräver ofta stora mängder tid, data och beräknings resurser. Du kan använda en genväg till inlärnings processen med hjälp av transfer Learning. Överförings inlärning är en teknik som används för att lösa ett problem med ett annat men relaterat problem. Det innebär att du kan använda en befintlig modell som kan användas. Mer information om överförings Inlärning finns i [artikeln djup inlärning vs Machine Learning](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) .

### <a name="reinforcement-learning-ray-rllib"></a>Förstärka inlärning: Ray RLLib

Förstärknings inlärning är en artificiell informations teknik som tränar modeller med hjälp av åtgärder, tillstånd och förmåner: förstärka inlärnings agenter lär sig att ta en uppsättning fördefinierade åtgärder som maximerar de angivna förmånerna baserat på miljöns aktuella tillstånd. 

[Ray RLLib](https://github.com/ray-project/ray) -projektet innehåller en uppsättning funktioner som ger hög skalbarhet under hela inlärnings processen. Den iterativa processen är både tids-och resurs intensiv som förstärknings bara utbildnings agenter försöker lära sig det bästa sättet att få en uppgift.  Ray RLLib stöder också djup inlärnings ramverk som TensorFlow och PyTorch.  

Information om hur du använder Ray RLLib med Azure Machine Learning finns i [så här tränar du en förstärknings inlärnings modell](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Övervaka modell prestanda: TensorBoard

Om du tränar en enskild eller flera modeller krävs visualisering och inspektion av önskade mått för att se till att modellen fungerar som förväntat. Du kan [använda TensorBoard i Azure Machine Learning för att spåra och visualisera experiment mått](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Ramverk för tolknings bara och verkliga modeller

Machine Learning-system används i olika områden i samhället, till exempel bank, utbildning och hälso vård. Det är därför viktigt att dessa system kan vara konto bara för de förutsägelser och rekommendationer som de gör för att förhindra oavsiktliga konsekvenser.

Ramverk med öppen källkod som [InterpretML](https://github.com/interpretml/interpret/) och Fairlearn ( https://github.com/fairlearn/fairlearn) arbeta med Azure Machine Learning för att skapa mer genomskinliga och rättvist maskin inlärnings modeller.

Mer information om hur du skapar verkliga och tolknings bara modeller finns i följande artiklar:

- [Modelltolkning i Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [Tolka och förklara maskin inlärnings modeller](how-to-machine-learning-interpretability-aml.md)
- [Förklara AutoML-modeller](how-to-machine-learning-interpretability-automl.md)
- [Minimera skälighet i Machine Learning-modeller](concept-fairness-ml.md)
- [Använda Azure Machine Learning till till gångs modell skälighet](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Modelldistribution

När modeller har tränats och är redo för produktion måste du välja hur den ska distribueras. Azure Machine Learning tillhandahåller olika distributions mål. Mer information finns i [artikeln om och distribution](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Standardisera modell format med ONNX

Efter utbildningen serialiseras innehållet i modellen, till exempel sparade parametrar, till en fil. Varje ramverk har ett eget format för serialisering. När du arbetar med olika ramverk och verktyg, innebär det att du måste distribuera modeller enligt ramverkets krav. För att standardisera den här processen kan du använda ONNX-formatet (Open neurala Network Exchange). ONNX är ett format med öppen källkod för artificiell intelligens-modeller. ONNX stöder samverkan mellan ramverk. Det innebär att du kan träna en modell i ett av de många populära ramverken för maskin inlärning som PyTorch, konvertera den till ONNX-format och använda ONNX-modellen i ett annat ramverk som ML.NET.

Mer information om ONNX och hur du använder ONNX-modeller finns i följande artiklar:

- [Skapa och påskynda ML-modeller med ONNX](concept-onnx.md)
- [Använda ONNX-modeller i .NET-program](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Paketera och distribuera modeller som behållare

Container tekniker som Docker är ett sätt att distribuera modeller som webb tjänster. Behållare är ett plattforms-och resurs oberoende sätt att bygga och dirigera åter förreproducerbara program miljöer. Med de här kärn teknikerna kan du använda [förkonfigurerade miljöer](./how-to-use-environments.md), [förkonfigurerade behållar avbildningar](./how-to-deploy-custom-docker-image.md) eller anpassade för att distribuera dina Machine Learning-modeller till till exempel [Kubernetes-kluster](./how-to-deploy-azure-kubernetes-service.md?tabs=python). För GPU-intensiva arbets flöden kan du använda verktyg som NVIDIA Triton-Härlednings Server för att [göra förutsägelser med hjälp av GPU: er](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Säker distribution med homomorphic-kryptering

Att skydda distributioner är en viktig del av distributions processen. Använd python-biblioteket med öppen källkod för att [distribuera krypterade inferencing-tjänster](how-to-homomorphic-encryption-seal.md) `encrypted-inference` . `encrypted inferencing`Paketet innehåller bindningar baserade på [Microsoft Seal](https://github.com/Microsoft/SEAL), ett homomorphic-krypterings bibliotek.

## <a name="machine-learning-operations-mlops"></a>Machine Learning åtgärder (MLOps)

Machine Learning åtgärder (MLOps), som ofta betraktas som DevOps för maskin inlärning, gör att du kan bygga mer genomskinliga, elastiska och återställnings bara arbets flöden för Machine Learning. Se [artikeln Vad är MLOps](./concept-model-management-and-deployment.md) för att lära dig mer om MLOps. 

Med DevOps-metoder som kontinuerlig integrering (CI) och kontinuerlig distribution (CD) kan du automatisera livs cykeln för maskin inlärning och samla in styrnings data runt dem. Du kan definiera din [Machine Learning CI/CD-pipeline i GitHub-åtgärder](./how-to-github-actions-machine-learning.md) för att köra Azure Machine Learning utbildning och distributions uppgifter. 

Att samla in program varu beroenden, Mät värden, metadata, data och modell version är en viktig del av MLOps-processen för att skapa genomskinliga, återställnings bara och gransknings bara pipeliner. För den här uppgiften kan du [använda MLFlow i Azure Machine Learning](how-to-use-mlflow.md) samt när du [tränar maskin inlärnings modeller i Azure Databricks](./how-to-use-mlflow-azure-databricks.md). Du kan också [distribuera MLflow-modeller som en Azure-webbtjänst](how-to-deploy-mlflow-models.md). 
