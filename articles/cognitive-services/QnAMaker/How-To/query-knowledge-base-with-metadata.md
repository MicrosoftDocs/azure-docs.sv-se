---
title: Filtrera sammanhangsbaseradt med hjälp av metadata
titleSuffix: Azure Cognitive Services
description: QnA Maker filtrerar QnA par efter metadata.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103022144"
---
# <a name="filter-responses-with-metadata"></a>Filtrera svar med metadata

Med QnA Maker kan du lägga till metadata, i form av nyckel-och värdepar, till dina par frågor och svar. Du kan sedan använda den här informationen för att filtrera resultat till användar frågor och lagra ytterligare information som kan användas i uppföljnings konversationer.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Lagra frågor och svar med en QnA-entitet

Det är viktigt att förstå hur QnA Maker lagrar frågan och svars data. Följande bild visar en QnA-entitet:

![Illustration av en QnA-entitet](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Varje QnA-entitet har ett unikt och beständigt ID. Du kan använda ID: t för att göra uppdateringar till en viss QnA-entitet.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Använd metadata för att filtrera svar efter anpassade metadata-Taggar

Genom att lägga till metadata kan du filtrera Svaren efter dessa metadata-taggar. Lägg till kolumnen metadata från menyn **visnings alternativ** . Lägg till metadata i kunskaps basen genom att välja metadata- **+** ikonen för att lägga till ett metadata-par. Det här paret består av en nyckel och ett värde.

![Skärm bild av lägga till metadata](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrera resultat med strictFilters för metadata-Taggar

Fundera över användar frågan "när den här hotellet stängs?", där avsikten är underförstådd med restaurang "Paradise".

Eftersom resultat endast krävs för restaurang "Paradise" kan du ange ett filter i GenerateAnswer-anropet för metadata "restaurang namn". I följande exempel visas följande:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logiskt och som standard

Om du vill kombinera flera metadata filter i frågan lägger du till de ytterligare metadata-filtren i matrisen för `strictFilters` egenskapen. Som standard kombineras värdena logiskt (och). En logisk kombination kräver att alla filter matchar QnA-par i ordning för att paret ska returneras i svaret.

Detta motsvarar att använda `strictFiltersCompoundOperationType` egenskapen med värdet `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logiska eller använda strictFiltersCompoundOperationType-egenskapen

När du kombinerar flera filter för metadata, om du bara är bekymrad med en eller några av filtren som matchar, använder du `strictFiltersCompoundOperationType` egenskapen med värdet `OR` .

Detta gör att din kunskaps bas kan returnera svar när något filter matchar men inte returnerar svar som saknar metadata.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Metadata exempel i snabb starter

Läs mer om metadata i snabb starten för QnA Maker Portal för metadata:
* [Redigering – Lägg till metadata i QnA-paret](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Fråga förutsägelse – filtrera svar efter metadata](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Använd fråge-och svars resultat för att hålla konversations kontext

Svaret på GenerateAnswer innehåller motsvarande metadatainformation för den matchade frågan och svars paret. Du kan använda den här informationen i klient programmet för att lagra kontexten för den tidigare konversationen för användning i senare konversationer.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Analysera din kunskaps bank](../How-to/get-analytics-knowledge-base.md)
