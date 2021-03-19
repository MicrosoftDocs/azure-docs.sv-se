---
title: Anpassa en språk modell i Video Indexer – Azure
titleSuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad som är en språk modell i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047139"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Anpassa en språk modell med Video Indexer

Video Indexer stöder automatisk tal igenkänning genom integrering med Microsoft- [Custom Speech service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Du kan anpassa språk modellen genom att överföra anpassnings text, nämligen text från den domän vars vokabulär du vill att motorn ska anpassa sig till. När du tränar din modell kommer nya ord som visas i anpassnings texten att kännas igen, förutsatt att det är standard uttal och språk modellen lär sig nya sannolika sekvenser av ord. Anpassade språk modeller stöds för engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska, portugisiska, hindi och koreanska. 

Låt oss ta ett ord som är mycket speciellt, till exempel "Kubernetes" (i Azure Kubernetes-tjänsten) som exempel. Eftersom ordet är nytt för Video Indexer identifieras det som "communities". Du måste träna modellen för att kunna identifiera den som "Kubernetes". I andra fall finns orden, men språk modellen förväntar sig inte att de ska visas i en viss kontext. Till exempel är "container service" inte en 2-ord-sekvens som en icke specialiserad språk modell känner igen som en specifik uppsättning ord.

Du kan välja att ladda upp ord utan sammanhang i en lista i en textfil. Detta betraktas som delvis anpassning. Du kan också ladda upp text fil (er) av dokumentation eller meningar som är relaterade till ditt innehåll för bättre anpassning.

Du kan använda API: erna för Video Indexer eller webbplatsen för att skapa och redigera anpassade språk modeller, enligt beskrivningen i ämnena i avsnittet [Nästa steg](#next-steps) i det här avsnittet.

## <a name="best-practices-for-custom-language-models"></a>Metod tips för anpassade språk modeller

Video Indexer lär dig på grund av sannolikheten för kombinationer av ord, så att du får bättre information:

* Ge många riktiga exempel på meningar som de skulle talas.
* Lägg endast till en mening per rad, inte mer. Annars kommer systemet att se sannolikhet över meningar.
* Det går bra att infoga ett ord som en mening för att förstärka ordet mot andra, men systemet lär sig bäst från fullständiga meningar.
* När du introducerar nya ord eller akronymer kan du, om möjligt, ge så många exempel på användning i en hel mening för att ge så mycket kontext som möjligt till systemet.
* Försök att ställa in flera anpassnings alternativ och se hur de fungerar för dig.
* Undvik upprepning av exakt samma mening flera gånger. Det kan skapa en förskjutning mot resten av indatamängden.
* Undvik att ta med ovanliga symboler (~, # @% &) när de kommer att tas bort. De meningar som de visas i tas också bort.
* Undvik att placera för stora indata, t. ex. hundratals tusentals meningar, eftersom detta kommer att leda till att ökningen späds ut.

## <a name="next-steps"></a>Nästa steg

[Anpassa språk modellen med hjälp av API: er](customize-language-model-with-api.md)

[Anpassa språk modellen med hjälp av webbplatsen](customize-language-model-with-website.md)
