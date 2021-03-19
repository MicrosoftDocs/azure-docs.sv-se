---
title: Batch-test TSV-format – QnA Maker
titleSuffix: Azure Cognitive Services
description: Förstå TSV-formatet för batch-testning
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132134"
---
# <a name="batch-testing-tsv-format"></a>Batch-test av TSV-format

Batch-testning är tillgänglig från [käll koden](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) eller som en [nedladdnings bar körbar fil zippad](https://aka.ms/qna_btzip). Kommandots format för att köra batch-testet är:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Förväntat värde|
|--|--|
|1|namn på TSV-fil formaterad med [TSV-indatafält](#tsv-input-fields)|
|2|URI för slut punkt, med din-värd från sidan publicera i QnA Maker-portalen.|
|3|SLUT punkt – nyckel, hittades på sidan publicera i QnA Maker portalen.|
|4|namnet på TSV-filen som skapats av batch-testet för resultat.|

Använd följande information för att förstå och implementera TSV-formatet för batch-testning. 

## <a name="tsv-input-fields"></a>Indatatyper för TSV

|Fält för TSV-indatafiler|Kommentarer|
|--|--|
|KBID|Ditt KB-ID finns på publicerings sidan.|
|Fråga|Frågan som en användare skulle ange.|
|Metadatataggar|valfri|
|Övre parameter|valfri| 
|Förväntat svars-ID|valfri|

![Indataformat för TSV-fil för batch-testning.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Fält för TSV-utdata 

|Parametrar för TSV output-filer|Kommentarer|
|--|--|
|KBID|Ditt KB-ID finns på publicerings sidan.|
|Fråga|Frågan som anges i indatafilen.|
|Svar|Främsta svar från din kunskaps bas.|
|Svars-ID|Svars-ID|
|Poäng|Förutsägelse Poäng för svar. |
|Metadatataggar|associerad med returnerat svar|
|Förväntat svars-ID|valfritt (endast när förväntat svars-ID anges)|
|Omdömes etikett|valfritt, värdena kan vara: korrekt eller felaktigt (endast när förväntat svar anges)|
