---
title: Vuxen, vågat, fullständig-innehåll – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifiering av olämpligt innehåll i avbildningar med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96532626"
---
# <a name="detect-adult-content"></a>Identifiera vuxen innehåll

Visuellt innehåll kan identifiera vuxna material i bilder så att utvecklare kan begränsa visningen av dessa bilder i program varan. Innehålls flaggor används med ett resultat mellan noll och en så att utvecklare kan tolka resultaten enligt sina egna inställningar.

> [!NOTE]
> Mycket av den här funktionen erbjuds av [Azure Content moderator](../content-moderator/overview.md) -tjänsten. Se det här alternativet för lösningar till fler rigorösa innehålls redigerings scenarier, till exempel text moderator och mänsklig gransknings arbets flöden.

## <a name="content-flag-definitions"></a>Definition av innehålls flagga

Klassificeringen "vuxen" innehåller flera olika kategorier:

- **Vuxna** bilder är av sexuell karaktär och ofta visar nakenhet och sexuella handlingar.
- **Vågat** -avbildningar är sexuellt bevarande i naturen och innehåller ofta mindre sexuellt innehåll än bilder som taggats som **vuxen**.
- **Fullständig** -bilder visar blod/Gore.

## <a name="use-the-api"></a>Använda API:et

Du kan identifiera innehåll för vuxna med API för att [analysera avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . När du lägger till värdet för `Adult` i **visualFeatures** -Frågeparametern returnerar API tre booleska egenskaper &mdash; `isAdultContent` , `isRacyContent` och `isGoryContent` &mdash; i dess JSON-svar. Metoden returnerar också motsvarande egenskaper &mdash; `adultScore` , `racyScore` och `goreScore` &mdash; som representerar förtroende poängen mellan noll och en för varje respektive kategori.

- [Snabb start: Visuellt innehåll REST API-eller klient bibliotek](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
