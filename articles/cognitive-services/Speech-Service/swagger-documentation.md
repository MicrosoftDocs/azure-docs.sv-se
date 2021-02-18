---
title: Dokumentation om Swagger-tal service
titleSuffix: Azure Cognitive Services
description: 'Swagger-dokumentationen kan användas för att automatiskt generera SDK: er för ett antal programmeringsspråk. Alla åtgärder i vår tjänst stöds av Swagger'
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: d4369b66bacbe8cff4fc9712ffcd0cb5a370c439
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636276"
---
# <a name="swagger-documentation"></a>Dokumentation om Swagger

Tal tjänsten erbjuder en Swagger-specifikation för att interagera med en fåtal av REST-API: er som används för att importera data, skapa modeller, testa modell precision, skapa anpassade slut punkter, köa batch-avskrifter och hantera prenumerationer. De flesta åtgärder som är tillgängliga via [Custom Speech avsnittet i tal Studio](https://aka.ms/customspeech) kan slutföras via programmering med hjälp av dessa API: er.

> [!NOTE]
> Tal tjänsten har flera REST-API: er för [tal till text](rest-speech-to-text.md) och [text till tal](rest-text-to-speech.md).  
>
> Men endast [tal-till-text REST API v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) och v 2.0 dokumenteras i Swagger-specifikationen. Se de dokument som refereras till i föregående stycke för information om alla andra funktioner i tal tjänster REST-API: er.

## <a name="generating-code-from-the-swagger-specification"></a>Generera kod från Swagger-specifikationen

[Swagger-specifikationen](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) har alternativ som gör att du snabbt kan testa olika sökvägar. Ibland är det dock önskvärt att generera kod för alla sökvägar och skapa ett enda bibliotek med anrop som du kan basera framtida lösningar på. Låt oss ta en titt på processen för att skapa ett Python-bibliotek.

Du måste ange Swagger till regionen för din tal resurs. Du kan bekräfta regionen i **översikts** delen av dina tal resurs inställningar i Azure Portal. Den fullständiga listan över regioner som stöds finns [här](regions.md#speech-to-text).

1. I en webbläsare går du till Swagger-specifikationen för din [region](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Klicka på **API-definition** på sidan och klicka på **Swagger**. Kopiera webb adressen till sidan som visas.
1. I en ny webbläsare går du till [https://editor.swagger.io](https://editor.swagger.io)
1. Klicka på **Arkiv**, klicka på **Importera URL**, klistra in URL: en och klicka på **OK**.
1. Klicka på **generera klient** och välj **python**. Klient biblioteket laddas ned till datorn i en `.zip` fil.
1. Extrahera allt från nedladdningen. Du kan använda `tar -xf` för att extrahera allt.
1. Installera den extraherade modulen i python-miljön:  
      `pip install path/to/package/python-client`
1. Det installerade paketet heter `swagger_client` . Kontrol lera att installationen har utförts:  
       `python -c "import swagger_client"`

Du kan använda python-biblioteket som du genererade med [tal tjänst exemplen på GitHub](https://aka.ms/csspeech/samples).

## <a name="reference-documents"></a>Referens dokument

* [Swagger: tal till text REST API v 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API för tal-till-text](rest-speech-to-text.md)
* [Text-till-tal (REST API)](rest-text-to-speech.md)

## <a name="next-steps"></a>Nästa steg

* [Tal tjänst exempel på GitHub](https://aka.ms/csspeech/samples).
* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
