---
title: Avpivotera transformering i data flöde för mappning
description: Azure Data Factory mappning av unpivot-transformering för data flöde
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93040200"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Avpivotera transformering i data flöde för mappning

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Använd unpivot i data flöde för ADF-mappning som ett sätt att omvandla en normaliserad data uppsättning till en mer normaliserad version genom att expandera värden från flera kolumner i en enskild post till flera poster med samma värden i en enda kolumn.

![Skärm bild som visar unpivot-markering från menyn.](media/data-flow/unpivot1.png "Avpivotera alternativ 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Dela upp efter

![Skärm bild som visar unpivot-inställningarna med fliken Dela upp grupper valt.](media/data-flow/unpivot5.png "Avpivotera alternativ 2")

Ange först de kolumner som du vill dela upp för unpivot-aggregering. Ange en eller flera kolumner för att dela upp med +-tecknet bredvid kolumn listan.

## <a name="unpivot-key"></a>Unpivot-nyckel

![Skärm bild som visar unpivot-inställningarna med fliken unpivot-tangenten vald.](media/data-flow/unpivot6.png "Avpivotera alternativ 3")

Unpivot-nyckeln är den kolumn som används för ADF från kolumn till rad. Som standard kommer varje unikt värde i data uppsättningen för det här fältet att pivoteras till en rad. Du kan också ange värden från data uppsättningen som du vill pivotera till rad värden.

## <a name="unpivoted-columns"></a>Kolumner som inte har pivoteras

![Skärm bild som visar unpivot-inställningarna med fliken Data förhands granskning vald.](media/data-flow//unpivot7.png "Avpivotera alternativ 4")

Slutligen väljer du kolumn namnet för att lagra värdena för staplade kolumner som omvandlas till rader.

Valfritt Du kan släppa rader med null-värden.

Till exempel är SumCost det kolumn namn som väljs i exemplet som delas ovan.

![Bild som visar kolumnerna PO, Vendor och frukt före och efter en unipivot-omvandling med hjälp av kolumnen frukt som unipivot-nyckeln.](media/data-flow/unpivot3.png)

Om du ställer in kolumn ordningen till "normal&quot; grupperas alla nya kolumner som inte har pivoteras från ett enda värde. Om du anger kolumnernas ordning till &quot;lateral&quot; grupperas nya, staplade kolumner som genereras från en befintlig kolumn.

![Skärm bild som visar resultatet av omvandlingen.](media/data-flow//unpivot7.png &quot;Avpivotera alternativ 5")

I den sista pivottabellen med nedpivoterade data visas kolumn summor nu i separata rad värden.

## <a name="next-steps"></a>Nästa steg

Använd [Pivot-transformeringen](data-flow-pivot.md) för att pivotera rader till kolumner.
