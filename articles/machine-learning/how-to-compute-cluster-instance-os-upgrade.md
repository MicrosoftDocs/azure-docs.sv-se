---
title: Uppgradera värdoperativsystemet för beräkningskluster och instanser
titleSuffix: Azure Machine Learning
description: Uppgradera värdoperativsystemet för beräkningskluster och beräkningsinstanser från Ubuntu 16.04 LTS till 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7445de8349d025679b1560e065ed15d9eec3b08f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872019"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Uppgradera beräkningsinstansen och beräkningsklustrets värdoperativsystem

Azure Machine Learning __beräkningskluster och__ __beräkningsinstanser__ är en hanterad beräkningsinfrastruktur. Som en hanterad tjänst hanterar Microsoft värdoperativsystemet och de paket och programvaruversioner som är installerade.

Värdoperativsystemet för beräkningskluster och beräkningsinstanser har varit Ubuntu 16.04 LTS. Den **30 april 2021** upphör Ubuntus stöd för 16.04. Från och __med 15 mars 2021__ uppdaterar Microsoft automatiskt värdoperativsystemet till Ubuntu 18.04 LTS. Uppdatering till 18.04 säkerställer kontinuerliga säkerhetsuppdateringar och stöd från Ubuntu-communityn. Den här uppdateringen kommer att distribueras i Azure-regioner och kommer att vara tillgänglig i alla regioner den __9 april 2021.__ Mer information om hur du slutar stödja Ubuntu för 16.04 finns i [Ubuntus lanseringsblogg.](https://wiki.ubuntu.com/Releases)

> [!TIP]
> * Värdoperativsystemet är inte den os-version som du kan ange för en [miljö när](how-to-use-environments.md) du tränar eller distribuerar en modell. Miljöer körs i Docker. Docker körs på värdoperativsystemet.
> * Om du för närvarande använder Ubuntu 16.04-baserade miljöer för träning eller distribution rekommenderar Microsoft att du byter till att använda Ubuntu 18.04-baserade avbildningar. Mer information finns i Använda [miljöer och lagringsplatsen för](how-to-use-environments.md) [Azure Machine Learning containrar.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * När du använder Azure Machine Learning-beräkningsinstans som baseras på Ubuntu 18.04 är _Python-standardversionen Python 3.8._
## <a name="creating-new-resources"></a>Skapa nya resurser

Beräkningskluster eller beräkningsinstanser som skapats efter __den 9 april 2021__ använder Ubuntu 18.04 LTS som värdoperativsystem som standard. Du kan inte välja ett annat värdoperativsystem.

## <a name="upgrade-existing-resources"></a>Uppgradera befintliga resurser

Om du har befintliga beräkningskluster eller beräkningsinstanser som skapats före __den 15 mars 2021__ måste du vidta åtgärder för att uppgradera värdoperativsystemet till Ubuntu 18.04. Beroende på vilken region du Azure Machine Learning från rekommenderar vi att du vidta dessa åtgärder efter __den 9 april 2021__ för att säkerställa att ändringarna har distribuerats till alla regioner:

* __Azure Machine Learning beräkningskluster:__

    * Om klustret är konfigurerat med __min noder = 0__ uppgraderas det automatiskt när alla jobb har slutförts och det minskar till noll noder.
    * Om __minsta antalet noder > 0__ ändrar du tillfälligt miniminoderna till noll och tillåter att klustret minskar till noll noder.

    Mer information om hur du ändrar minsta noder finns [i az ml computetarget update amlcompute Azure CLI command (az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) Azure CLI-kommandot) eller [IMLCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK-referensen.

* __Azure Machine Learning beräkningsinstans:__ Skapa en ny beräkningsinstans (som använder Ubuntu 18.04) och ta bort den gamla instansen.

    * Notebook-filer som lagras i arbetsytans filresurs, datalager och datauppsättningar är tillgängliga från den nya beräkningsinstansen.
    * Om du har skapat anpassade Conda-miljöer kan du exportera dessa miljöer från den befintliga instansen och importera dem på den nya instansen. Information om conda-export och -import finns i [Conda-dokumentationen](https://docs.conda.io/) på docs.conda.io.

    Mer information finns i artiklarna [Vad är beräkningsinstans och](concept-compute-instance.md) Skapa och hantera en Azure Machine Learning [beräkningsinstans](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Kontrollera värdoperativsystemets version

Information om hur du kontrollerar värdoperativsystemets version finns på Ubuntu community wiki-sidan om att [kontrollera din Ubuntu-version.](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)

> [!TIP]
> Om du vill `lsb_release -a` använda kommandot från wikin kan du använda en [terminalsession på en beräkningsinstans](how-to-access-terminal.md).
## <a name="next-steps"></a>Nästa steg

Om du har ytterligare frågor eller problem kan du kontakta oss på [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
