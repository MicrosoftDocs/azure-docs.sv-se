---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.openlocfilehash: 5eb2517b4b71b61dd70406f345c005d007d9e3ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91535973"
---
Med entiteten pattern.any kan du söka efter friformsdata där formuleringen i entiteten gör det svårt att fastställa var entiteten slutar baserat på resten av yttrandet.

En personal-app hjälper anställda att hitta företags formulär.

|Yttrande|
|--|
|Var finns **HRF-123456**?|
|Vem skapade **HRF 123234**?|
|Har **HRF 456098** publicerats på franska?|

Varje formulär har dock både ett formaterat namn, som används i tabellen ovan, samt ett eget namn, till exempel `Request relocation from employee new to the company 2018 version 5`.

Yttranden med det egna formulärnamnet ser ut så här:

|Yttrande|
|--|
|Var finns **Begär flytt från medarbetare som är ny i företaget 2018 version 5**?|
|Vem skapade **”Begär flytt från medarbetare som är ny i företaget 2018 version 5”**?|
|Har **Begära flytt från medarbetare som är ny i företagets 2018 version 5** publicerats på franska?|

Den varierande längden innehåller ord som kan förvirra LUIS om var entiteten slutar. Genom att använda en Pattern.any-entitet i ett mönster kan du ange formulärnamnets början och slut, så att LUIS kan extrahera formulärnamnet korrekt.

|Exempelmall för yttranden|
|--|
|Var finns {FormName}[?]|
|Vem skapade {FormName}[?]|
|Har {FormName} publicerats på franska[?]|

Gransknings mönster. eventuell [Referensinformation](../reference-entity-pattern-any.md)