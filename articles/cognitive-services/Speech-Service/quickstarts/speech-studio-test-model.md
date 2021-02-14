---
title: Testa en modell med ljudfiler – tal Studio
titleSuffix: Azure Cognitive Services
description: I den här instruktionen använder du tal Studio för att testa igenkänning av tal i en ljudfil.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: v-demjoh
ms.openlocfilehash: 93a0c171497faa9e87457a03da0b78003feaa533
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362275"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Testa en modell med hjälp av en ljudfil i tal Studio

I den här instruktionen använder du tal Studio för att konvertera tal från en ljudfil till text. Med tal Studio kan du testa, jämföra, förbättra och distribuera tal igenkännings modeller med relaterad text, ljud med medmärkta avskrifter och vägledning som du anger.

## <a name="prerequisites"></a>Förutsättningar

Innan du använder tal Studio [följer du de här anvisningarna för att skapa ett Azure-konto och prenumerera på tal tjänsten](../custom-speech-overview.md#set-up-your-azure-account). Den här enhetliga prenumerationen ger dig åtkomst till tal översättning av tal till text, text till tal, tal översättning och tal Studio.

## <a name="download-an-audio-file"></a>Ladda ned en ljudfil

Följ de här stegen för att ladda ned en ljudfil som innehåller tal och paketera den i en zip-fil.

1. Hämta **[exempel filen WAV från den här länken](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** genom att högerklicka på länken och välja **Spara länk som**. Klicka på **Spara** för att ladda ned `whatstheweatherlike.wav` filen.
2. Skapa en zip-fil med ett zip-verktyg `whatstheweatherlike.zip` som innehåller den fil som du har hämtat genom att använda en Utforskaren eller ett terminalfönster med ett zip-verktyg `whatstheweatherlike.wav` . I Windows kan du öppna Utforskaren, navigera till `Downloads` mappen, högerklicka `whatstheweatherliike.wav` , klicka på **Skicka till**, klicka på **komprimerad (zippad) mapp** och trycka på RETUR för att acceptera standard fil namnet.

## <a name="create-a-project-in-the-speech-studio"></a>Skapa ett projekt i tal Studio

Följ de här stegen om du vill skapa ett projekt som innehåller din zip av en ljudfil.

1. Öppna [tal Studio](https://speech.microsoft.com/)och klicka på **nytt projekt**. Skriv ett namn för projektet och klicka på **skapa**. Ditt projekt visas i listan Custom Speech.
2. Klicka på namnet på projektet. På fliken data klickar du på **överför data**.
3. Tal data typen är som standard **endast ljud**, så klicka på **Nästa**.
4. Namnge din nya tal data uppsättning `MyZipOfAudio` och klicka på **Nästa**.
5. Klicka på **Bläddra filer...**, navigera till `whatstheweatherlike.zip` filen och klicka på **Öppna**.
6. Klicka på knappen **Överför**. Webbläsaren laddar upp din zip-fil till tal Studio och tal Studio bearbetar innehållet.

## <a name="test-a-model"></a>Testa en modell

När tal Studio bearbetar innehållet i din zip-fil kan du spela upp käll ljudet och undersöka avskriften för att söka efter fel eller utelämnanden. Följ dessa steg om du vill undersöka avskrifts kvaliteten i webbläsaren.

1. Klicka på fliken **test** och klicka på **Lägg till test**.
2. I det här testet kontrollerar vi kvaliteten på endast ljuddata, så klicka på **Nästa** för att acceptera test typen.
3. Ge testet ett namn `MyModelTest` och klicka på **Nästa**.
4. Klicka på alternativ knappen till vänster om `MyZipOfAudio` och klicka på **Nästa**.
5. List rutan **modell 1** använder den senaste igenkännings modellen som standard, så klicka på **skapa**. När innehållet i din ljud data uppsättning har bearbetats ändras test statusen till **slutförd**.
6. Klicka på **MyModelTest**. Resultatet av tal igenkänning visas. Klicka på den högra pilen i cirkeln för att höra ljudet och jämför vad du hör till texten med cirkeln.

## <a name="download-detailed-results"></a>Hämta detaljerade resultat

Du kan hämta filer som beskriver avskrifter i mycket större detalj. Filerna innehåller en lexikalisk form av tal i ljudfilerna och JSON-filer som innehåller information om förskjutning, varaktighet och avskrifts säkerhet för varje ord. Följ dessa steg om du vill se de här filerna.

1. Klicka på **Hämta**.
2. I dialog rutan Ladda ned avmarkerar du **ljud** och klickar på **Ladda ned**.
3. Zippa upp den hämtade ZIP-filen och granska de extraherade filerna.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att förbättra precisionen för tal igenkänning genom att [träna en anpassad modell](../how-to-custom-speech-test-and-train.md).