---
title: Exempel program & ML-genom gångar
titleSuffix: Azure Data Science Virtual Machine
description: I de här exemplen och genom gången lär du dig hur du hanterar vanliga uppgifter och scenarier med Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cda5dfd936243602775e1f4f965032b9d746b0b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100519770"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Exempel på Azure Data Science-Virtual Machines

Azure Data Science Virtual Machines (Dsvm) innehåller en omfattande uppsättning exempel kod. De här exemplen omfattar Jupyter-anteckningsböcker och skript i språk som python och R.
> [!NOTE]
> Mer information om hur du kör Jupyter-anteckningsböcker på dina virtuella datorer för data vetenskap finns i avsnittet [Access Jupyter](#access-jupyter) .

## <a name="prerequisites"></a>Förutsättningar

För att kunna köra dessa exempel måste du ha etablerad en [Ubuntu data science Virtual Machine](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Tillgängliga exempel
| Exempel kategori | Beskrivning | Platser |
| ------------- | ------------- | ------------- |
| R-språk  | Exempel illustrerar scenarier som hur du ansluter till Azure-baserade moln data lager och hur du jämför R-och Microsoft Machine Learning Server med öppen källkod. De förklarar också hur du operationalisera modeller på Microsoft Machine Learning Server och SQL Server. <br/> [R-språk](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Python-språk  | Exempel beskriver scenarier som hur du ansluter med Azure-baserade moln data lager och hur du arbetar med Azure Machine Learning.  <br/> [Python-språk](#python-language) | <br/>`~notebooks` <br/><br/>|
| Julia-språk  | Innehåller en detaljerad beskrivning av hur du ritar och djupar inlärning i Julia. Förklarar också hur du anropar C och python från Julia. <br/> [Julia-språk](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Azure Machine Learning  | Visar hur du skapar modeller för maskin inlärning och djup inlärning med Machine Learning. Distribuera modeller var som helst. Använd automatisk justering av maskin inlärning och intelligenta parametrar. Använd även modell hantering och distribuerad utbildning. <br/> [Machine Learning](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| PyTorch-anteckningsböcker  | Exempel på djup inlärning som använder PyTorch-baserade neurala-nätverk. Bärbara datorer sträcker sig från nybörjare till avancerade scenarier.  <br/> [PyTorch-anteckningsböcker](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  En mängd olika neurala-nätverks exempel och tekniker som implementeras med hjälp av TensorFlow-ramverket. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Exempel på djup inlärning som publicerats av Cognitive Toolkits teamet på Microsoft.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Exempel på djup inlärning som använder Caffe2-baserade neurala-nätverk. Flera antecknings böcker bekanta användare med Caffe2 och hur de används effektivt. Exempel på hur du skapar och skapar data uppsättningar är avbildningar. De omfattar också regression och hur man använder förtränade modeller. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Python-baserade exempel som använder H2O för verkliga problem scenarier. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| SparkML-språk  | Exempel som använder funktionerna i Apache Spark MLLib Toolkit via pySpark och MMLSpark: Microsoft Machine Learning för Apache Spark på Apache Spark 2. x.  <br/> [SparkML-språk](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost | Standard maskin inlärnings exempel i XGBoost för scenarier som klassificering och regression. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Åtkomst Jupyter 

Välj **Jupyter** -ikonen på Skriv bordet eller program menyn för att komma åt Jupyter. Du kan också komma åt Jupyter i en Linux-utgåva av en DSVM. Om du vill komma åt fjärr anslutning från en webbläsare går du till `https://<Full Domain Name or IP Address of the DSVM>:8000` på Ubuntu.

Använd följande vägledning om du vill lägga till undantag och göra Jupyter åtkomst tillgänglig via en webbläsare:


![Aktivera Jupyter-undantag](./media/ubuntu-jupyter-exception.png)


Logga in med samma lösen ord som du använder för att logga in på Data Science Virtual Machine.
<br/>

**Jupyter Home**
<br/>![Jupyter Home](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>R-språk 
<br/>![R-exempel](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Python-språk
<br/>![Python-exempel](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Julia-språk 
<br/>![Julia-exempel](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Azure Machine Learning 
<br/>![Azure Machine Learning exempel](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![PyTorch-exempel](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![TensorFlow-exempel](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![CNTK-exempel](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![caffe2-exempel](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![H2O-exempel](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![SparkML-exempel](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost 
<br/>![XGBoost-exempel](./media/xgboost-samples.png)<br/>

