---
title: Dataplacering
description: Beskriver data placering när du använder Azure Remote rendering.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576925"
---
# <a name="azure-remote-rendering-data-residency"></a>Placering för Azure Remote rendering-data 
I den här artikeln förklaras begreppet data placering och hur det gäller för Azure Remote rendering. 

## <a name="data-residency"></a>Dataplacering 
I Azure fjärrrendering används användare som tillhandahöll Azure Blob-behållare för modell lagring. När modellen inte används finns den kvar i den användare som tillhandahöll Azure Blob Storage region. När modellen används för åter givning kopieras data till den region som användaren väljer när den startar åter givnings sessionen.

## <a name="next-steps"></a>Nästa steg
Om du vill lära dig hur du konfigurerar en Azure Blob Storage-behållare för Azure-fjärrrendering kan du kolla in [konvertera en modell för åter givning](../quickstarts/convert-model.md).
