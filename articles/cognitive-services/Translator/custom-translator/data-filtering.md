---
title: Data filtrering – anpassad översättare
titleSuffix: Azure Cognitive Services
description: När du skickar dokument som ska användas för att träna ett anpassat system, genomgår dokumenten en serie bearbetnings-och filtrerings steg för att förbereda för utbildning.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1076a4fe3a460fa07e061e9ec0ec41b088ec7eca
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507271"
---
# <a name="data-filtering"></a>Datafiltrering

När du skickar dokument som ska användas för att träna ett anpassat system, genomgår dokumenten en serie bearbetnings-och filtrerings steg för att förbereda för utbildning. Dessa steg beskrivs här. Kunskapen om filtreringen kan hjälpa dig att förstå antalet meningar som visas i en anpassad översättare samt de steg du kan vidta för att förbereda dokumenten för utbildning med anpassad översättare.

## <a name="sentence-alignment"></a>Meningsjustering
Om dokumentet inte finns i XLIFF-, TMX-eller ALIGN-format justerar den anpassade översättningen meningarna i käll-och mål dokumenten till varandra, mening per mening. Anpassad översättare utför inte dokument justering – det följer ditt namn på dokumenten för att hitta det matchande dokumentet för det andra språket. I dokumentet försöker anpassad översättare hitta motsvarande mening på det andra språket. Den använder dokument markering som inbäddade HTML-taggar för att hjälpa till med justeringen.  

Om du ser en stor avvikelse mellan antalet meningar i käll-och mål dokumenten kanske inte dokumentet har varit parallellt på den första platsen, eller för andra orsaker kunde inte justeras. Dokument par med stor skillnad (>10%) av meningar på varje sida se till att de är i själva verket parallellt. Anpassad översättare visar en varning bredvid dokumentet om antalet meningar skiljer sig från suspiciously.  


## <a name="deduplication"></a>Deduplicering
Anpassad översättare tar bort meningarna som finns i test-och justerings dokument från tränings data.Borttagningen sker dynamiskt i övnings körningen, inte i steget data bearbetning. Med anpassad översättare rapporteras antalet meningar till dig i projekt översikten före borttagningen.  

## <a name="length-filter"></a>Längd filter
* Ta bort meningar med endast ett ord på endera sidan.
* Ta bort meningar med fler än 100 ord på endera sidan.Kinesiska, japanska och koreanska är undantagna.
* Ta bort meningar med färre än 3 tecken. Kinesiska, japanska och koreanska är undantagna.
* Ta bort meningar med mer än 2000 tecken för kinesiska, japanska och koreanska.
* Ta bort meningar med färre än 1% alpha-tecken.
* Ta bort lexikon poster som innehåller fler än 50 ord.

## <a name="white-space"></a>Blanksteg
* Ersätt en sekvens med blank stegs tecken, inklusive tabbar och CR/LF-sekvenser med ett enda blank stegs tecken.
* Ta bort inledande eller avslutande blank steg i meningen

## <a name="sentence-end-punctuation"></a>Avslutande punkt för mening
Ersätt flera skiljetecken i slutet med en enda instans.  

## <a name="japanese-character-normalization"></a>Japanskt Character-normalisering
Konvertera hel bredds bokstäver och siffror till halv bredds tecken.

## <a name="unescaped-xml-tags"></a>Avbrotts bara XML-taggar
Filtrering transformerar avbrotts bara taggar till undantagna Taggar:
* `&lt;` bli `&amp;lt;`
* `&gt;` bli `&amp;gt;`
* `&amp;` bli `&amp;amp;`

## <a name="invalid-characters"></a>Ogiltiga tecken
Anpassad översättare tar bort meningar som innehåller Unicode-tecken U + FFFD. Tecken U + FFFD anger en kodnings konvertering som inte kunde konverteras.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell i en](how-to-train-model.md) anpassad översättare.
