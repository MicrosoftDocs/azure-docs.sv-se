---
title: Så här använder du Table-taggar för att träna din anpassade formulär modell – formulär tolken
titleSuffix: Azure Cognitive Services
description: Lär dig hur du effektivt använder övervakad tabell etiketts etiketter.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467912"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Använd Table-taggar för att träna din anpassade formulär modell

I den här artikeln lär du dig hur du tränar din anpassade formulär modell med Table-Taggar (etiketter). Vissa scenarier kräver mer komplexa etiketter än att bara justera nyckel/värde-par. Sådana scenarier innehåller information om hur du extraherar information från formulär med komplexa hierarkiska strukturer eller hur du påträffar objekt som inte identifieras automatiskt och extraheras av tjänsten. I dessa fall kan du använda Table-taggar för att träna din anpassade formulär modell.

## <a name="when-should-i-use-table-tags"></a>När ska jag använda Table-Taggar?

Här följer några exempel på när du använder tabell Taggar:

- Det finns data som du vill extrahera presenteras som tabeller i formulären och tabell strukturen är meningsfull. Varje rad i tabellen representerar till exempel ett objekt och varje kolumn i raden representerar en speciell funktion i objektet. I det här fallet kan du använda en tabell tagg där en kolumn representerar funktioner och en rad representerar information om varje funktion.
- Det finns data som du vill extrahera som inte visas i vissa formulär fält, men semantiskt, data kan passa i ett tvådimensionellt rutnät. Ditt formulär har till exempel en lista över personer och innehåller, ett förnamn, ett efter namn och en e-postadress. Vill du extrahera den här informationen. I det här fallet kan du använda en TABLE-tagg med förnamn, efter namn och e-postadress som kolumner och varje rad fylls med information om en person från listan.

> [!NOTE]
> Formulär tolken hittar och extraherar alla tabeller i dina dokument automatiskt om tabellerna är taggade eller inte. Därför behöver du inte märka varje tabell från ditt formulär med en TABLE-tagg och tabell taggarna behöver inte replikera strukturen för den tabell som finns i formuläret. Tabeller som extraheras automatiskt av formulär igenkänning tas med i avsnittet pageResults i JSON-utdata.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Skapa en TABLE-tagg med form OCR-testverktyget (FOTT)
<!-- markdownlint-disable MD004 -->
* Bestäm om du vill ha en **dynamisk** eller **fast storleks** tabell tagg. Om antalet rader varierar från dokument till dokument använder du en dynamisk tabell-tagg. Om antalet rader är konsekvent i dokumenten använder du en tabell tagg med fast storlek.
* Om din TABLE-tagg är dynamisk definierar du kolumn namnen och data typen och formatet för varje kolumn.
* Om din tabell har fast storlek definierar du kolumn namnet, rad namnet, data typen och formatet för varje tagg.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Konfigurera en tabell tagg":::

## <a name="label-your-table-tag-data"></a>Etikettera dina tabell märkes data

* Om ditt projekt har en tabell tagg kan du öppna etikett panelen och fylla i taggen på samma sätt som du skulle märka nyckel värde fält.
:::image type="content" source="media/table-labeling.png" alt-text="Etikett med Table-Taggar":::

## <a name="next-steps"></a>Nästa steg

Följ vår snabb start för att träna och använda din anpassade formulär igenkännings modell:

> [!div class="nextstepaction"]
> [Träna med etiketter med hjälp av verktyget för exempel märkning](quickstarts/label-tool.md)

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](overview.md)
>
