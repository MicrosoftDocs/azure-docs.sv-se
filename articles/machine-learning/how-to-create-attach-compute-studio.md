---
title: Skapa utbildning & distribuera beräkningar (Studio)
titleSuffix: Azure Machine Learning
description: Använd Studio för att skapa utbildnings resurser för utbildning och distribution (beräknings mål) för maskin inlärning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: ab7a74166e85f2ba9fd73e7323cf9cd200cf32e4
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031036"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Skapa beräknings mål för modell utbildning och distribution i Azure Machine Learning Studio

I den här artikeln får du lära dig hur du skapar och hanterar Compute-mål i Azure Machine Studio.  Du kan också skapa och hantera beräknings mål med:

* Azure Machine Learning Learning SDK eller CLI-tillägget för Azure Machine Learning
  * [Beräkninsinstans](how-to-create-manage-compute-instance.md)
  * [Beräkningskluster](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes service-kluster](how-to-create-attach-kubernetes.md)
  * [Andra beräknings resurser](how-to-attach-compute-targets.md)
* [Vs Code-tillägget](how-to-manage-resources-vscode.md#compute-clusters) för Azure Machine Learning.


## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag
* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md)

## <a name="whats-a-compute-target"></a>Vad är ett beräknings mål? 

Med Azure Machine Learning kan du träna din modell på en mängd olika resurser eller miljöer, som sammankallas för [__beräknings mål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräknings mål kan vara en lokal dator eller en moln resurs, t. ex. en Azure Machine Learning Compute, Azure HDInsight eller en virtuell dator.  Du kan också skapa beräknings mål för modell distribution enligt beskrivningen i ["var och hur du distribuerar dina modeller"](how-to-deploy-and-where.md).

## <a name="view-compute-targets"></a><a id="portal-view"></a>Visa beräknings mål

Gör så här om du vill se alla beräknings mål för din arbets yta:

1. Navigera till [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Under __Hantera__ väljer du __Compute__.

1. Välj flikar längst upp för att visa varje typ av beräknings mål.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Visa lista med beräknings mål":::

## <a name="create-compute-target"></a><a id="portal-create"></a>Skapa beräknings mål

Följ föregående steg för att visa listan över beräknings mål. Använd sedan de här stegen för att skapa ett beräknings mål:

1. Välj fliken längst upp som motsvarar den typ av beräkning som du vill skapa.

1. Om du inte har några beräknings mål väljer du  **skapa** i mitten av sidan.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Skapa beräknings mål":::

1. Om du ser en lista över beräknings resurser väljer du **+ ny** ovanför listan.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Välj nytt":::


1. Fyll i formuläret för din beräknings typ:

  * [Beräkninsinstans](#compute-instance)
  * [Beräknings kluster](#amlcompute)
  * [Härlednings kluster](#inference-clusters)
  * [Kopplad beräkning](#attached-compute)

1. Välj __Skapa__.

1. Visa status för åtgärden Skapa genom att välja Compute-målet i listan:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Visa beräknings status från en lista":::


### <a name="compute-instance"></a>Beräkninsinstans

Använd [stegen ovan](#portal-create) för att skapa beräknings instansen.  Fyll sedan i formuläret enligt följande:

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Skapa en ny beräknings instans":::


|Fält  |Beskrivning  |
|---------|---------|
|Namn på beräkning     |  <li>Namnet måste vara mellan 3 och 24 tecken långt.</li><li>Giltiga tecken är gemener och versaler, siffror och  **-** tecken.</li><li>Namnet måste börja med en bokstav</li><li>Namnet måste vara unikt för alla befintliga beräkningar i en Azure-region. En avisering visas om det namn du väljer inte är unikt</li><li>Om **-**  tecknet används måste det följas av minst en bokstav senare i namnet</li>     |
|Typ av virtuell dator |  Välj processor eller GPU. Det går inte att ändra den här typen när den har skapats     |
|Storlek för virtuell dator     |  Storleken på virtuella datorer som stöds kan vara begränsad i din region. Kontrol lera [tillgänglighets listan](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Aktivera/inaktivera SSH-åtkomst     |   SSH-åtkomst är inaktive rad som standard.  SSH-åtkomst får inte vara. ändras efter att den har skapats. Se till att aktivera åtkomst om du planerar att felsöka interaktivt med [vs Code Remote](how-to-set-up-vs-code-remote.md)   |
|Avancerade inställningar     |  Valfritt. Konfigurera ett virtuellt nätverk. Ange **resurs gruppen**, det **virtuella nätverket** och **under nätet** för att skapa beräknings instansen i ett Azure-Virtual Network (VNet). Mer information finns i [nätverks kraven](./how-to-secure-training-vnet.md) för VNet.  |

### <a name="compute-clusters"></a><a name="amlcompute"></a> Beräknings kluster

Skapa ett beräknings kluster med en enda eller flera noder för din utbildning, batch-inferencing eller förstärka inlärnings arbets belastningar. Använd [stegen ovan](#portal-create) för att skapa beräknings klustret.  Fyll sedan i formuläret enligt följande:


|Fält  |Beskrivning  |
|---------|---------|
|Namn på beräkning     |  <li>Namnet måste vara mellan 3 och 24 tecken långt.</li><li>Giltiga tecken är gemener och versaler, siffror och  **-** tecken.</li><li>Namnet måste börja med en bokstav</li><li>Namnet måste vara unikt för alla befintliga beräkningar i en Azure-region. En avisering visas om det namn du väljer inte är unikt</li><li>Om **-**  tecknet används måste det följas av minst en bokstav senare i namnet</li>     |
|Typ av virtuell dator |  Välj processor eller GPU. Det går inte att ändra den här typen när den har skapats     |
|Prioritet för virtuell dator | Välj **dedikerad** eller **låg prioritet**.  Virtuella datorer med låg prioritet är billigare men garanterar inte Compute-noderna. Jobbet kan vara avblockerat.
|Storlek för virtuell dator     |  Storleken på virtuella datorer som stöds kan vara begränsad i din region. Kontrol lera [tillgänglighets listan](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Minsta antalet noder | Minsta antal noder som du vill etablera. Om du vill ha ett dedikerat antal noder anger du detta antal här. Spara pengar genom att ange minimivärdet till 0 så att du inte betalar för några noder när klustret är inaktivt. |
|Maximalt antal noder | Maximalt antal noder som du vill etablera. Beräkningen skalas automatiskt till maximalt antal noder när ett jobb skickas. |
|Avancerade inställningar     |  Valfritt. Konfigurera ett virtuellt nätverk. Ange **resurs gruppen**, det **virtuella nätverket** och **under nätet** för att skapa beräknings instansen i ett Azure-Virtual Network (VNet). Mer information finns i [nätverks kraven](./how-to-secure-training-vnet.md) för VNet.   Bifoga även [hanterade identiteter](#managed-identity) för att ge åtkomst till resurser     |

#### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Konfigurera hanterad identitet

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

När klustret skapas eller när du redigerar beräknings kluster information går du till **Avancerade inställningar** och växlar **tilldela en hanterad identitet** och anger en tilldelad identitet eller användardefinierad identitet.

#### <a name="managed-identity-usage"></a>Hanterad identitets användning

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

### <a name="inference-clusters"></a>Härlednings kluster

> [!IMPORTANT]
> Att använda Azure Kubernetes-tjänsten med Azure Machine Learning har flera konfigurations alternativ. Vissa scenarier, till exempel nätverk, kräver ytterligare konfiguration och konfiguration. Mer information om hur du använder AKS med Azure ML finns i [skapa och ansluta ett Azure Kubernetes service-kluster](how-to-create-attach-kubernetes.md).

Skapa eller koppla ett Azure Kubernetes service-kluster (AKS) för storskaliga inferencing. Använd [stegen ovan](#portal-create) för att skapa AKS-klustret.  Fyll sedan i formuläret enligt följande:


|Fält  |Beskrivning  |
|---------|---------|
|Namn på beräkning     |  <li>Namn måste anges. Namnet måste innehålla mellan 2 och 16 tecken. </li><li>Giltiga tecken är gemener och versaler, siffror och  **-** tecken.</li><li>Namnet måste börja med en bokstav</li><li>Namnet måste vara unikt för alla befintliga beräkningar i en Azure-region. En avisering visas om det namn du väljer inte är unikt</li><li>Om **-**  tecknet används måste det följas av minst en bokstav senare i namnet</li>     |
|Kubernetes Service | Välj **Skapa ny** och fyll i resten av formuläret.  Eller Välj **Använd befintlig** och välj sedan ett befintligt AKS-kluster från din prenumeration.
|Region |  Välj den region där klustret ska skapas |
|Storlek för virtuell dator     |  Storleken på virtuella datorer som stöds kan vara begränsad i din region. Kontrol lera [tillgänglighets listan](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Kluster syfte  | Välj **produktion** eller **dev-test** |
|Antal noder | Antalet noder som multipliceras med den virtuella datorns antal kärnor (virtuella processorer) måste vara större än eller lika med 12. |
| Konfiguration av nätverk | Välj **Avancerat** för att skapa beräkningen i ett befintligt virtuellt nätverk. Mer information om AKS i ett virtuellt nätverk finns i [nätverks isolering under utbildning och härledning med privata slut punkter och virtuella nätverk](./how-to-secure-inferencing-vnet.md). |
| Aktivera SSL-konfiguration | Använd detta för att konfigurera SSL-certifikatet på beräkningen |

### <a name="attached-compute"></a>Kopplad beräkning

Om du vill använda beräknings mål som skapats utanför arbets ytan Azure Machine Learning måste du koppla dem. Om du kopplar ett beräknings mål blir det tillgängligt för din arbets yta.  Använd **kopplad beräkning** för att koppla ett beräknings mål för **träning**.  Använd utsättare **kluster** för att ansluta ett AKS-kluster för **inferencing**.

Använd [stegen ovan](#portal-create) för att koppla en beräkning.  Fyll sedan i formuläret enligt följande:

1. Ange ett namn för beräknings målet. 
1. Välj den typ av beräkning som ska bifogas. Alla beräknings typer kan inte kopplas från Azure Machine Learning Studio. De beräknings typer som för närvarande kan bifogas för utbildning är:
    * En virtuell fjärrdator
    * Azure Databricks (används i maskin inlärnings pipeliner)
    * Azure Data Lake Analytics (används i maskin inlärnings pipeliner)
    * Azure HDInsight

1. Fyll i formuläret och ange värden för de obligatoriska egenskaperna.

    > [!NOTE]
    > Microsoft rekommenderar att du använder SSH-nycklar, som är säkrare än lösen ord. Lösen ord är utsatta för angrepp med brute force. SSH-nycklar är beroende av kryptografiska signaturer. Information om hur du skapar SSH-nycklar för användning med Azure Virtual Machines finns i följande dokument:
    >
    > * [Skapa och använda SSH-nycklar på Linux eller macOS](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Skapa och Använd SSH-nycklar i Windows](../virtual-machines/linux/ssh-from-windows.md)

1. Välj __bifoga__. 


## <a name="next-steps"></a>Nästa steg

När ett mål har skapats och kopplats till din arbets yta, använder du det i din [körnings konfiguration](how-to-set-up-training-targets.md) med ett `ComputeTarget` objekt:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Använd beräknings resursen för att [skicka en utbildnings körning](how-to-set-up-training-targets.md).
* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Lär dig hur du [effektivt justerar disponeringsparametrarna](how-to-tune-hyperparameters.md) för att bygga bättre modeller.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* [Använda Azure Machine Learning med virtuella Azure-nätverk](./how-to-network-security-overview.md)