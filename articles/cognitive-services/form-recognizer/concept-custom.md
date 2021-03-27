---
title: Anpassade modeller – formulär igenkänning
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör anpassade modeller i formulär igenkännings-API – användning och begränsningar.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 74ced2ecadb5ccfe5cdb7966550e469ae4f8ab31
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612270"
---
# <a name="form-recognizer-custom-models"></a>Anpassade modeller för formulär tolken

Formulär tolken använder avancerad maskin inlärnings teknik för att analysera och extrahera data från formulär och dokument. En formulär tolks modell är en representation av extraherade data som används som referens för att analysera ditt speciella innehåll. Det finns två typer av formulär igenkännings modeller:

* **Anpassade modeller**. Formulär tolkens anpassade modeller representerar extraherade data från _formulär_ som är specifika för ditt företag. Anpassade modeller måste tränas för att analysera dina distinkta formulär data.

* **Färdiga modeller**. Formulär tolken stöder för närvarande färdiga modeller för _kvitton, visitkort, identifierings kort_ och _fakturor_. Förbyggda modeller identifierar och extraherar information från dokument bilder och returnerar extraherade data i ett strukturerat JSON-utdata.

## <a name="what-does-a-custom-model-do"></a>Vad gör en anpassad modell?

Med formulär tolken kan du träna en modell som extraherar information från formulär som är relevanta för ditt användnings fall. Du behöver bara fem exempel av samma formulär typ för att komma igång. Din anpassade modell kan tränas med eller utan etiketterade data uppsättningar.

## <a name="create-use-and-manage-your-custom-model"></a>Skapa, använda och hantera din anpassade modell

På en hög nivå är stegen för att skapa, träna och använda din anpassade modell följande:

> [!div class="nextstepaction"]
> [1. sammanställa din utbildnings data uppsättning](build-training-data-set.md#custom-model-input-requirements)

Att skapa en anpassad modell börjar med att upprätta din utbildnings data uppsättning. Du behöver minst fem slutförda formulär av samma typ för din exempel data uppsättning. De kan vara av olika filtyper och innehålla både text och hand skrift. Formulären måste vara av samma typ av dokument och följa kraven för [indatamängden](build-training-data-set.md#custom-model-input-requirements) för formulär igenkänning.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. Ladda upp din utbildnings data uppsättning](build-training-data-set.md#upload-your-training-data)

Du måste ladda upp dina utbildnings data till en Azure Blob Storage-behållare. Om du inte vet hur du skapar ett Azure Storage-konto med en behållare kan du *läsa* [Azure Storage snabb start för Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Använd den kostnads fria pris nivån (F0) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. träna din anpassade modell](quickstarts/client-library.md#train-a-custom-model)

Du kan träna din modell [utan](quickstarts/client-library.md#train-a-model-without-labels) eller [med](quickstarts/client-library.md#train-a-model-with-labels) etiketterade data uppsättningar. Omärkta data uppsättningar förlitar sig enbart på layout-API: et för att identifiera och identifiera nyckelinformation utan att ha lagt till mänsklig indata. Etiketterade data uppsättningar förlitar sig även på layout-API: t, men extra mänsklig indata ingår som dina egna etiketter och fält platser. Om du vill använda både märkta och omärkta data börjar du med minst fem ifyllda formulär av samma typ för de märkta tränings data och lägger sedan till omärkta data i den data uppsättning som krävs.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. analysera dokument med din anpassade modell](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Testa din nya självtränade modell genom att använda ett formulär som inte var en del av data uppsättningen för träning. Du kan fortsätta att göra ytterligare utbildningar för att förbättra prestandan för din anpassade modell.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. hantera dina anpassade modeller](quickstarts/client-library.md#manage-custom-models)

Du kan när som helst visa en lista över alla anpassade modeller under din prenumeration, hämta information om en anpassad modell eller ta bort en anpassad modell från ditt konto.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om klient biblioteket för formulär tolken genom att utforska vår API-referens dokumentation.
> [!div class="nextstepaction"]
> [Forms igenkännings-API-referens](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
>
