---
title: Flera grenar i mappning av dataflöde
description: Replikera dataströmmar i mappa dataflöde med flera grenar
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 04/16/2021
ms.openlocfilehash: f9f2bf2e2204e6b74bb8a31ac856dbe276a6e983
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588759"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Skapa en ny gren i mappning av dataflöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Lägg till en ny gren för att göra flera uppsättningar åtgärder och transformningar mot samma dataström. Att lägga till en ny gren är användbart när du vill använda samma källa till för flera mottagare eller för att koppla samman data själv.

En ny gren kan läggas till från transformeringslistan på liknande sätt som andra transformningar. **Ny gren** är bara tillgänglig som en åtgärd när det finns en befintlig transformering efter den transformering som du försöker förgrena.

![Skärmbild som visar alternativet Ny gren i menyn Flera indata/utdata.](media/data-flow/new-branch2.png "Lägga till en ny gren")

I exemplet nedan läser dataflödet taxi resedata. Utdata sammanställda av både dag och leverantör krävs. I stället för att skapa två separata dataflöden som läses från samma källa kan en ny gren läggas till. På så sätt kan båda sammansättningarna köras som en del av samma dataflöde. 

![Skärmbild som visar dataflödet med två grenar från källan.](media/data-flow/new-branch.png "Lägga till en ny gren")

> [!NOTE]
> När du klickar på plustecknet (+) för att lägga till transformningar i grafen visas bara alternativet Ny gren när det finns efterföljande transformeringsblock. Det beror på att New Branch skapar en referens till den befintliga dataströmmen och kräver ytterligare uppströmsbearbetning för att fungera. Om du inte ser alternativet Ny gren lägger du först till en härledd kolumn eller annan transformering. Gå sedan tillbaka till föregående block så visas Ny gren som ett alternativ.

## <a name="next-steps"></a>Nästa steg

Efter förgrening kanske du vill använda [dataflödesomvandlar](data-flow-transformation-overview.md)
