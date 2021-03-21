---
title: Måttlig text med hjälp av API-Content Moderator för text redigering
titleSuffix: Azure Cognitive Services
description: 'Kontroll av textkontrollant med hjälp av API: et för text redigering i online-konsolen.'
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002008"
---
# <a name="moderate-text-from-the-api-console"></a>Måttlig text från API-konsolen

Använd [API: et för text redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) i Azure Content moderator för att skanna ditt text innehåll för svordomar och jämföra det med anpassade och delade listor.

## <a name="get-your-api-key"></a>Hämta din API-nyckel

Innan du kan testa API: et i online-konsolen behöver du din prenumerations nyckel. Detta finns på fliken **Inställningar** i rutan **OCP-APIM-Subscription-Key** . Mer information finns i [Översikt](overview.md).

## <a name="navigate-to-the-api-reference"></a>Gå till API-referensen

Gå till [API-referensen för text redigering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Sidan med **text skärmen** öppnas.

## <a name="open-the-api-console"></a>Öppna API-konsolen

För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

  ![Val av sid region för text på skärmen](images/test-drive-region.png)

  API **-konsolen för text skärmen** öppnas.

## <a name="select-the-inputs"></a>Välj indata

### <a name="parameters"></a>Parametrar

Välj de frågeparametrar som du vill använda på text skärmen. I det här exemplet använder du standardvärdet för **språk**. Du kan också lämna det tomt eftersom åtgärden automatiskt identifierar det sannolika språket som en del av körningen.

> [!NOTE]
> För parametern **språk** , tilldelar `eng` eller lämnar du den tom för att se det omhjälpande **klassificerings** svaret (förhands gransknings funktion). **Den här funktionen stöder enbart engelska**.
>
> För identifiering av **svordomar** kan du använda [ISO 639-3-koden](http://www-01.sil.org/iso639-3/codes.asp) för de språk som stöds i den här artikeln eller lämna det tomt.

För **Autokorrigering**, **PII** och **klassificering (för hands version)** väljer du **Sant**. Lämna fältet **listid** tomt.

  ![Frågeparametrar för text skärms konsolen](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Innehållstyp

För **innehålls typ** väljer du den typ av innehåll som du vill skärma. I det här exemplet använder du standard typen **text/oformaterad** innehålls typ. I rutan **OCP-APIM-Subscription-Key** anger du din prenumerations nyckel.

### <a name="sample-text-to-scan"></a>Exempel text att skanna

Skriv lite text i rutan **begär** text. I följande exempel visas ett avsiktligt skrivfel i texten.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>Analysera svaret

Följande svar visar de olika insikterna från API: et. Den innehåller potentiell svordom, personliga data, klassificering (för hands version) och den automatiskt korrigerade versionen.

> [!NOTE]
> Den datorbaserade klassificerings funktionen är i för hands version och har endast stöd för engelska.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
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
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

En detaljerad förklaring av alla avsnitt i JSON-svaret finns i vägledning för [text redigering](text-moderation-api.md) .

## <a name="next-steps"></a>Nästa steg

Använd REST API i din kod eller följ snabb starten för [.NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp) för att integrera med ditt program.