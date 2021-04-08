---
title: Text moderator – Content Moderator
titleSuffix: Azure Cognitive Services
description: Använd text redigering för möjlig oönskad text, personliga data och anpassade listor med villkor.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: pafarley
ms.openlocfilehash: ae49a8738ba711ac6c77f2e299852ad61f70be56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912913"
---
# <a name="learn-text-moderation-concepts"></a>Lär dig om text moderator koncept

Använd Content Moderatorens text redigerings modeller för att analysera text innehåll.

Du kan blockera, godkänna eller granska innehållet baserat på dina principer och tröskelvärden (se [granskningar, arbets flöden och jobb](./review-api.md) för att lära dig hur du ställer in mänsklig granskning). Använd text redigerings modeller för att förstärka mänsklig redaktör av miljöer där partner, anställda och konsumenter genererar text innehåll. Det kan vara chattrum, diskussionstavlor, chattrobotar, e-handelskataloger dokument.

Tjänstsvaret innehåller följande information:

- Svordomar: term-baserad matchning med inbyggd lista över flera svordomar på olika språk
- Klassificering: maskinvarustödd klassificering i tre kategorier
- Personuppgifter
- Automatiskt korrigerad text
- Ursprunglig text
- Språk

## <a name="profanity"></a>Olämpligt språk

Om API: n identifierar eventuella svordomar i något av de [språk som stöds](./language-support.md), inkluderas dessa villkor i svaret. Svaret innehåller också deras plats ( `Index` ) i den ursprungliga texten. `ListId`I följande exempel-JSON refererar de termer som finns i [anpassade term listor](try-terms-list-api.md) om de är tillgängliga.

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }
```

> [!NOTE]
> För parametern **språk** , tilldelar `eng` eller lämnar du den tom för att se det omhjälpande **klassificerings** svaret (förhands gransknings funktion). **Den här funktionen stöder enbart engelska**.
>
> För identifiering av **svordomar** kan du använda [ISO 639-3-koden](http://www-01.sil.org/iso639-3/codes.asp) för de språk som stöds i den här artikeln eller lämna det tomt.

## <a name="classification"></a>Klassificering

Content Moderator funktionen för maskin stöd för **text klassificering** stöder **endast engelska** och hjälper till att identifiera potentiellt oönskat innehåll. Det flaggade innehållet kan utvärderas som olämpligt beroende på kontext. Den förmedlar sannolikheten för varje kategori och kan rekommendera en mänsklig granskning. Funktionen använder en tränad modell för att identifiera eventuellt stötande, avvikande eller diskriminerande språk. Detta omfattar slang, förkortade ord, stötande och avsiktligt felstavade ord för granskning. 

Följande utdrag i JSON-extraheringen visar ett exempel på utdata:

```json
"Classification": {
    "ReviewRecommended": true,
    "Category1": {
        "Score": 1.5113095059859916E-06
    },
    "Category2": {
        "Score": 0.12747249007225037
    },
    "Category3": {
        "Score": 0.98799997568130493
    }
}
```

### <a name="explanation"></a>Förklaring

- `Category1` avser potentiell närvaro av ett språk som kan anses vara sexuellt explicit eller vuxna i vissa situationer.
- `Category2` avser potentiell närvaro av ett språk som kan anses vara sexuellt eller mogna i vissa situationer.
- `Category3` avser potentiell närvaro av ett språk som kan betraktas som stötande i vissa situationer.
- `Score` är mellan 0 och 1. Ju högre poäng, desto högre blir modellen för att förutsäga att kategorin kan vara tillämplig. Den här funktionen förlitar sig på en statistisk modell i stället för att manuellt koda resultat. Vi rekommenderar att du testar med ditt eget innehåll för att avgöra hur varje kategori anpassar sig efter dina behov.
- `ReviewRecommended` är antingen sant eller falskt beroende på de interna Poäng tröskelvärdena. Kunderna bör bedöma om de ska använda det här värdet eller bestämma anpassade tröskelvärden baserat på deras innehålls principer.

## <a name="personal-data"></a>Personuppgifter

Funktionen personliga data identifierar potentiell närvaro av den här informationen:

- E-postadress
- E-postadress till oss
- IP-adress
- Telefonnummer till USA

I följande exempel visas ett exempel svar:

```json
"pii":{
  "email":[
      {
        "detected":"abcdef@abcd.com",
        "sub_type":"Regular",
        "text":"abcdef@abcd.com",
        "index":32
      }
  ],
  "ssn":[

  ],
  "ipa":[
      {
        "sub_type":"IPV4",
        "text":"255.255.255.255",
        "index":72
      }
  ],
  "phone":[
      {
        "country_code":"US",
        "text":"6657789887",
        "index":56
      }
  ],
  "address":[
      {
        "text":"1 Microsoft Way, Redmond, WA 98052",
        "index":89
      }
  ]
}
```

## <a name="auto-correction"></a>Automatisk korrigering

Anta att indatamängden är ("lzay" och "f0x" är avsiktliga):

> Qu! CK bruna f0x-hopp över lzay hund.

Om du ber om automatisk korrigering innehåller svaret den korrigerade versionen av texten:

> Quick Jansson Fox hoppar över den Lazy hunden.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Skapa och hantera dina anpassade listor med villkor

Standardinställningen är att den globala listan över termer fungerar bra i de flesta fall, men du kanske vill skärmen mot de villkor som är specifika för dina affärs behov. Du kanske till exempel vill filtrera bort eventuella konkurrens märkes namn från inlägg av användare.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

I följande exempel visas matchnings List-ID:

```json
"Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }
```

Content Moderator innehåller ett [API för term listor](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) med åtgärder för att hantera anpassade term listor. Börja med [villkoret listar API-konsolen](try-terms-list-api.md) och Använd REST API kod exempel. Ta också en titt på [termen listar .net snabb start](term-lists-quickstart-dotnet.md) om du är bekant med Visual Studio och C#.

## <a name="next-steps"></a>Nästa steg

Testa API: erna med [API-konsolen för text redigering](try-text-api.md). Se även [granskningar, arbets flöden och jobb](./review-api.md) för att lära dig hur du ställer in mänsklig granskning.