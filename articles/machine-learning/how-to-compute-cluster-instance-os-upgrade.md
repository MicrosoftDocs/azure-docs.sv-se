---
title: Uppgradera värd operativ system för beräknings kluster och instans
titleSuffix: Azure Machine Learning
description: Uppgradera värd operativ systemet för beräknings kluster och beräknings instans från Ubuntu 16,04 LTS till 18,04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5f853c19015a70d596c32532a9c280c785b4597b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609995"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Uppgradera beräknings instans och beräknings kluster värd operativ system

Azure Machine Learning __beräknings kluster__ och __beräknings instans__ är hanterad beräknings infrastruktur. Som en hanterad tjänst hanterar Microsoft värd-OS och de paket och program versioner som är installerade.

Värd operativ systemet för beräknings kluster och beräknings instans har Ubuntu 16,04 LTS. Den **30 April 2021** slutar Ubuntu support för 16,04. Från och med den __15 mars 2021__ kommer Microsoft automatiskt att uppdatera värd operativ systemet till Ubuntu 18,04 LTS. Uppdatering till 18,04 säkerställer fortsatt säkerhets uppdateringar och support från Ubuntu-communityn. Mer information om Ubuntu-stöd för 16,04 finns i [Ubuntu release-bloggen](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * Värd operativ systemet är inte den OS-version som du kan ange för en [miljö](how-to-use-environments.md) när du tränar eller distribuerar en modell. Miljöer körs i Docker. Docker körs på värd operativ systemet.
> * Om du för närvarande använder Ubuntu 16,04-baserade miljöer för utbildning eller distribution rekommenderar Microsoft att du växlar till att använda Ubuntu 18,04-baserade avbildningar. Mer information finns i [så här använder du miljöer](how-to-use-environments.md) och [Azure Machine Learning containers-lagringsplatsen](https://github.com/Azure/AzureML-Containers/tree/master/base).
> * När du använder en Azure Machine Learning beräknings instans som baseras på Ubuntu 18,04, är den standardinställda python-versionen _python 3,8_.
## <a name="creating-new-resources"></a>Skapa nya resurser

Beräknings kluster eller beräknings instanser som skapats efter den __15 mars 2021__ använder Ubuntu 18,04 LTS som värd operativ system som standard. Du kan inte välja ett annat värd-OS.

## <a name="upgrade-existing-resources"></a>Uppgradera befintliga resurser

Om du har befintliga beräknings kluster eller beräknings instanser som skapats före den __15 mars 2021__ måste du vidta åtgärder för att uppgradera värd operativ systemet till Ubuntu 18,04:

* __Azure Machine Learning beräknings kluster__:

    * Om klustret har kon figurer ATS med __minsta antal noder = 0__ uppgraderas det automatiskt när alla jobb slutförs och det minskar till noll noder.
    * Om minsta antal __noder > 0__, ändra tillfälligt minsta noder till noll och Tillåt att klustret minskar till noll noder.

    Mer information om hur du ändrar de lägsta noderna finns i [AZ ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI-kommandot eller [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK-referensen.

* __Azure Machine Learning beräknings instans__: skapa en ny beräknings instans (som kommer att använda Ubuntu 18,04) och ta bort den gamla instansen.

    * Alla antecknings böcker som lagras i fil resursen för arbets ytor kan data lager av data uppsättningar nås från den nya beräknings instansen.
    * Om du har skapat anpassade Conda-miljöer kan du exportera dessa miljöer från den befintliga instansen och importera på den nya instansen. Information om export och import av Conda finns i [Conda-dokumentationen](https://docs.conda.io/) på docs.Conda.io.

    Mer information finns i avsnittet [Vad är beräknings instans](concept-compute-instance.md) och [skapa och hantera en Azure Machine Learning beräknings instans](how-to-create-manage-compute-instance.md) artiklar

## <a name="check-host-os-version"></a>Kontrol lera operativ system version för värd

Information om hur du kontrollerar värd operativ systemets version finns på Ubuntu community wiki-sidan för att [kontrol lera din Ubuntu-version](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Om du vill använda `lsb_release -a` kommandot från wikin kan du [använda en terminalsession på en beräknings instans](how-to-access-terminal.md).
## <a name="next-steps"></a>Nästa steg

Om du har några ytterligare frågor eller problem kan du kontakta oss på [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .