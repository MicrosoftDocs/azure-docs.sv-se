---
title: Resurser som stöds för Azure Red Hat OpenShift 3.11
description: Förstå vilka Azure-regioner och virtuella dator storlekar som stöds av Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ad0837ae110b84cdff690fe76e13923a0ab60996
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635616"
---
# <a name="azure-red-hat-openshift-resources"></a>Resurser för att OpenShift i Azure Red Hat

I det här avsnittet visas de Azure-regioner och storlekar för virtuella datorer som stöds av Microsoft Azure Red Hat OpenShift 3,11-tjänsten.

## <a name="azure-regions"></a>Azure-regioner

Se [tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) för en aktuell lista över regioner där du kan distribuera Azure Red Hat OpenShift-kluster.

## <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

Här följer de storlekar för virtuella datorer som stöds som du kan ange för datornoderna i ditt Azure Red Hat OpenShift-kluster.

> [!Important]
> Varje virtuell dator har ett annat antal enheter som kan anslutas. Detta kan inte vara så långt klart som minne eller CPU-storlek.
> Alla VM-storlekar är inte tillgängliga i alla regioner. Även om API: et stöder den storlek som du anger kan du få ett fel meddelande om storleken inte är tillgänglig i den region som du anger.
> Se [aktuell lista över VM-storlekar som stöds per region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) för mer information.

## <a name="compute-node-sizes"></a>Compute Node-storlekar

Följande storlekar på Compute-noder stöds av den här OpenShift-REST API i Azure Red Hat:

|Storlek|Virtuell processor|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8-enheter v2|8|16 GB|
|Standard F16-enheter v2|16|32 GB|
|Standard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Huvud Node-storlekar

Följande Node-och Infrastructure Node-storlekar stöds av Azure Red Hat OpenShift-REST API:

|Storlek|Virtuell processor|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Nästa steg

Prova själv studie kursen [skapa en Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .
