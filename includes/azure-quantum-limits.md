---
title: ta med fil
description: ta med fil
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948663"
---
### <a name="provider-limits--quota"></a>Provider begränsar & kvot

Azure Quantum service stöder både första och tredje parts tjänst leverantörer. Tredjepartsleverantörer äger sina gränser och kvoter. Användare kan Visa erbjudanden och gränser i Azure Portal när du konfigurerar tredjepartsleverantörer på leverantörs bladet. 

Du hittar de publicerade kvot gränserna för Microsofts första part Optimization Solutions-leverantör nedan. 

#### <a name="learn--develop-sku"></a>Lär dig & utveckla SKU

| Resurs | Gräns |
| --- | --- |
| CPU-baserade samtidiga jobb | upp till 5 samtidiga jobb |
| FPGA samtidiga jobb | upp till 2 samtidiga jobb |
| CPU-baserade problemlösnings timmar | 20 timmar per månad  |
| FPGA-baserade solver-timmar | 1 timme per månad  |

Om du använder lära dig & utveckla SKU: n **kan du inte** begära en ökning av kvot gränserna. I stället bör du byta till prestanda i skalnings-SKU.

#### <a name="performance-at-scale-sku"></a>Prestanda i Scale-SKU

| Resurs | Standardgräns | Övre gräns |
| --- | --- | --- |
| CPU-baserade samtidiga jobb | upp till 100 samtidiga jobb | samma som standard gräns |
| FPGA samtidiga jobb | upp till 10 samtidiga jobb | samma som standard gräns |
| Problemlösa rens timmar | 1 000 timmar per månad  | upp till 50 000 timmar per månad |

Kontakta Azure-supporten om du behöver göra en gräns ökning. 

Mer information finns på [sidan med priser för Azure Quantum](https://aka.ms/AQ/Pricing).
Information om erbjudanden från tredje part finns i den relevanta Provider-sidan i Azure Portal.
