---
title: Vuxet, racy, gory-innehåll – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifiering av vuxet innehåll i bilder med hjälp Visuellt innehåll API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778867"
---
# <a name="detect-adult-content"></a>Identifiera vuxet innehåll

Visuellt innehåll kan identifiera vuxet material i bilder så att utvecklare kan begränsa visningen av dessa bilder i sin programvara. Innehållsflaggor tillämpas med en poäng mellan noll och en så att utvecklare kan tolka resultaten enligt sina egna inställningar.

> [!NOTE]
> En stor del av den här funktionen erbjuds [av Azure Content Moderator tjänsten.](../content-moderator/overview.md) Se det här alternativet för lösningar på mer rigorösa scenarier för innehållsmoderering, till exempel textmoderering och arbetsflöden för mänsklig granskning.

## <a name="content-flag-definitions"></a>Definitioner för innehållsflagga

Klassificeringen "vuxet" innehåller flera olika kategorier:

- **Vuxna** bilder är explicit sexuellt till sin natur och visar ofta nuditet och sexuellt innehåll.
- **Racy-bilder** är sexuellt suggestiva till sin natur och innehåller ofta mindre sexuellt explicit innehåll än bilder som taggats som **Vuxna**.
- **Gory-bilder** visar blod/gore.

## <a name="use-the-api"></a>Använda API:et

Du kan identifiera vuxet innehåll med [hjälp Analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API. När du lägger till värdet för i `Adult` **frågeparametern visualFeatures** returnerar API:et tre booleska egenskaper &mdash; `isAdultContent` , och i `isRacyContent` dess `isGoryContent` &mdash; JSON-svar. Metoden returnerar också motsvarande egenskaper &mdash; `adultScore` , och som `racyScore` representerar `goreScore` &mdash; förtroendepoäng mellan noll och ett för varje respektive kategori.

- [Snabbstart: Visuellt innehåll REST API eller klientbibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
