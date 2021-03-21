---
title: Hantera diagrammet mappa data flöde
description: Hur du effektivt hanterar och redigerar diagrammet mappa data flöde
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: f55364fe0eabae4bb1698bd655cde8d3e01b127a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590468"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Hantera diagrammet mappa data flöde

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mappning av data flöden skapas med en design yta som är en data flödes graf. I grafen är omvandlings logiken byggd från vänster till höger och ytterligare data strömmar läggs till överst. Om du vill lägga till en ny omvandling väljer du plus tecknet längst ned till höger i en befintlig omvandling.

![Arbetsyta](media/data-flow/canvas-2.png)

När dina data flödar mer komplexa använder du följande mekanismer för att effektivt navigera och hantera data flödes diagrammet. 

## <a name="moving-transformations"></a>Flytta transformeringar

I mappnings data flöden kallas en uppsättning ansluten omvandlings logik som en **data ström**. Fältet **inkommande ström** avgör vilken data ström som används för att mata in den aktuella omvandlingen. Varje omvandling har en eller två inkommande strömmar beroende på dess funktion och representerar en utdataström. Utmatnings schema för inkommande strömmar avgör vilka kolumn-metadata som kan refereras av den aktuella omvandlingen.

![Flytta nod](media/data-flow/move-nodes.png "flytta nod")

Till skillnad från pipeline-arbetsytan redige ras inte data flödes omvandlingar med en dra och släpp-modell. Om du vill ändra den inkommande strömmen eller "flytta" en omvandling, väljer du ett annat värde i list rutan **inkommande Stream** . När du gör det flyttas alla efterföljande omvandlingar tillsammans med den redigerade omvandlingen. Diagrammet kommer automatiskt att uppdateras för att visa det nya logiska flödet. Om du ändrar inkommande data ström till en omvandling som redan har en underordnad omvandling, skapas en ny gren eller parallell data ström. Lär dig mer om [nya grenar i mappnings data flödet](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Dölj graf och Visa diagram

När du redigerar din omvandling kan du expandera konfigurations panelen för att ta hela arbets ytan och dölja grafen. Klicka på den övre, övre kanten som finns på höger sida av arbets ytan.

![Dölj diagram](media/data-flow/hide-graph.png "Dölj diagram")

När grafen är dold kan du flytta mellan omvandlingar i en ström genom att klicka på **Nästa** eller **föregående**. Visa grafen genom att klicka på den nedåtriktade ikonen.

![Visa diagram](media/data-flow/show-graph.png "Visa diagram")

## <a name="searching-for-transformations"></a>Söker efter omvandlingar

Om du snabbt vill hitta en omvandling i grafen klickar du på **Sök** ikonen ovanför zoomnings inställningen.

![Skärm bild som visar Sök knappen.](media/data-flow/search-1.png "Sök diagram")

Du kan söka efter omvandlings namn eller beskrivning för att hitta en omvandling.

![Skärm bild som visar Sök text rutan.](media/data-flow/search-2.png "Sök diagram")

## <a name="hide-reference-nodes"></a>Dölj noder i referens

Om ditt data flöde har kopplings-, lookup-, exists-eller union-transformationer, visar data flödet referens-noderna till alla inkommande data strömmar. Om du vill minimera mängden lodrätt utrymme kan du minimera dina Reference-noder. Det gör du genom att högerklicka på arbets ytan och välja **Dölj Reference Nodes**.

![Dölj noder i referens](media/data-flow/hide-reference-nodes.png "Dölj noder i referens")

## <a name="next-steps"></a>Nästa steg

När du har fyllt i data flödes logiken aktiverar du [fel söknings läge](concepts-data-flow-debug-mode.md) och testar det i en data förhands granskning.
