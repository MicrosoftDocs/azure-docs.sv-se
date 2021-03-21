---
title: Dynamisk ord lista – översättare
titleSuffix: Azure Cognitive Services
description: Den här artikeln förklarar hur du använder funktionen för dynamisk ord lista i Azure Cognitive Services Translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: ebfccb6fed46db65f6a70937516552f5280c8016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897772"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Så här använder du en dynamisk ord lista

Om du redan vet vilken översättning du vill använda för ett ord eller en fras kan du ange den som markering i begäran. Den dynamiska ord listan är endast säker för sammansatta substantiv som rätt namn och produkt namn.

**Uttryck**

<msTrans: Ordbok översättning = "översättning av fras" >fras</msTrans: Ordbok>

**Signaturkrav**

* - `From` Och- `To` språken måste innehålla engelska och ett annat språk som stöds. 
* Du måste inkludera `From` parametern i din begäran om API-omvandling i stället för att använda funktionen för automatisk identifiering. 

**Exempel: sv-de:**

Käll ineffekt: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Mål utmatning: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Den här funktionen fungerar på samma sätt med och utan HTML-läge.

Använd funktionen sparsamt. Ett bättre sätt att anpassa översättning är att använda anpassad översättare. Anpassad översättare ger fullständig användning av kontext och statistisk sannolikhet. Om du har eller kan skapa tränings data som visar ditt arbete eller en fras i sammanhanget får du mycket bättre resultat. Du hittar mer information om anpassad översättare på [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .