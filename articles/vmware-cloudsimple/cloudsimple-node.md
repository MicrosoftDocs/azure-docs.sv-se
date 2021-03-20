---
title: Översikt över Azure VMware-lösning av CloudSimple-noder
description: 'Lär dig mer om CloudSimple-koncept, inklusive noder, etablerade noder, ett privat moln och VMware-lösning av CloudSimple-noder SKU: er.'
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140844"
---
# <a name="cloudsimple-nodes-overview"></a>Översikt över CloudSimple-noder

Noder är Bygg blocken i ett privat moln. En nod är:

* En dedikerad beräknings värd för Bare Metal där en VMware ESXi-hypervisor installeras  
* En beräknings enhet som du kan etablera eller reservera för att skapa privata moln
* Tillgängligt för etablering eller reserv i en region där CloudSimple-tjänsten är tillgänglig

Du skapar ett privat moln från de etablerade noderna. Om du vill skapa ett privat moln måste du ha minst tre noder av samma SKU. Lägg till ytterligare noder för att expandera ett privat moln.  Du kan lägga till noder i ett befintligt kluster eller skapa ett nytt kluster genom att etablering av noder i Azure Portal och associera dem med CloudSimple-tjänsten.  Alla etablerade noder är synliga under CloudSimple-tjänsten.  

## <a name="provisioned-nodes"></a>Etablerade noder

Etablerade noder ger kapacitet för att betala per användning. Med etablerings noderna kan du snabbt skala ditt VMware-kluster på begäran. Du kan lägga till noder vid behov eller ta bort en etablerad nod för att skala ned VMware-klustret. Etablerade noder debiteras per månad och debiteras för prenumerationen där de är etablerade.

* Om du betalar för Azure-prenumerationen via kredit kort faktureras kortet direkt.
* Om du faktureras per faktura visas avgifterna på nästa faktura.

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware-lösning av CloudSimple Nodes SKU

Följande typer av noder är tillgängliga för etablering eller reservation.

| SKU           | CS28-nod                 | CS36-nod                 | CS36m-nod                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| Region        | Östra USA, västra USA            | Östra USA, västra USA            | Europa, västra                 |
| Processor           | 2x 2,2 GHz, 28 kärnor (56 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) | 2x 2,3 GHz, 36 kärnor (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| Cachelagra disk    | 1,6 – TB NVMe                 | 3,2 – TB NVMe                 | 3,2 – TB NVMe                 |
| Kapacitets disk | 5,625 TB RAW                | 11,25 TB RAW                | 15,36 TB RAW                |
| Lagringstyp  | Alla Flash                   | Alla Flash                   | Alla Flash                   |

## <a name="limits"></a>Gränser

Följande Node-gränser gäller för privata moln.

| Resurs | Gräns |
|----------|-------|
| Minsta antal noder för att skapa ett privat moln | 3 |
| Maximalt antal noder i ett kluster i ett privat moln | 16 |
| Maximalt antal noder i ett privat moln | 64 |
| Minsta antal noder i ett nytt kluster | 3 |

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [etablerar noder](create-nodes.md)
* Lär dig mer om [privata moln](cloudsimple-private-cloud.md)
