---
title: Lokaliserings stöd med Microsoft Azure Maps
description: Se vilka regioner Azure Maps stöder med tjänster som kartor, sökning, routning, väder och trafik incidenter. Lär dig hur du konfigurerar parametern View.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 50e5d0721eb14d1fcdfad26aaf081bfa370e954e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904524"
---
# <a name="localization-support-in-azure-maps"></a>Lokaliserings stöd i Azure Maps

Azure Maps stöder olika språk och vyer baserat på land/region. Den här artikeln innehåller språk och vyer som stöds för att hjälpa dig att hjälpa dig med din Azure Maps implementering.


## <a name="azure-maps-supported-languages"></a>Azure Maps språk som stöds

Azure Maps har lokaliserats på olika typer av tjänster för sina tjänster. Följande tabell innehåller de språk koder som stöds för varje tjänst.  
  

| ID         | Name                   |  Maps | Sök | Routning | Vädret | Trafik incidenter | JS-kart kontroll |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| AF – ZA      | Afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arabiska                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| BN – BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| BN – IN      | Bangla (Indien)         |       |       |         |     ✓    |                   |                |
| BS-BA      | Bosniska                 |       |       |         |     ✓    |                   |                |
| EU-ES      | Baskiska                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bulgariska              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalanska                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Kinesiska (förenklad)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Kinesiska (Hongkong SAR)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Kinesiska (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Kroatiska               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tjeckiska                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Danska                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| NL-vara      | Nederländska (Belgien)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Nederländska (Nederländerna)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en – AU      | Engelska (Australien)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Engelska (Nya Zeeland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Engelska (Storbritannien) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| sv-SE      | Engelska (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estniska               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finska                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Franska                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Franska (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galiciska               |       |    ✓   |         |         |                   |                |
| de-DE      | Tyska                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grekiska                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu – IN      | Gujarati                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebreiska                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Ungerska              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| är-är      | Isländska              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonesiska             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italienska                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japanska               |       |        |         |     ✓    |                   |                |
| KN-IN      | Kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazakiska                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreanska                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| ES – 419     | Latinska spanska |       |    ✓   |         |         |                   |                |
| lv-LV      | Lettiska                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litauiska             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK – MK      | Makedonska             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malajiska (latinsk)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | Marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norska (bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutrala mark sanningen-officiella språk för alla regioner i lokala skript om det är tillgängligt |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutral mark sanningen – Latin exonyms. Latinska skript kommer att användas om det är tillgängligt |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polska                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugisiska (Brasilien)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugisiska (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Punjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Rumänska               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Ryska                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Serbiska (kyrillisk)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | Serbiska (latinsk) (Serbien)        |       |       |         |     SR-latn    |                   |                |
| sk-SK      | Slovakiska             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| SL-SL      | Slovenska              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ES – MX      | Spanska (Mexiko)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Svenska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta med      | Tamiliska (Indien)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (Indien)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thailändska                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turkiska                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrainska               |       |    ✓   |         |     ✓    |                   |                |
| din-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbekiska                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamesiska             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps vyer som stöds

> [!Note]
> Den 1 augusti 2019 släpptes Azure Maps i följande länder/regioner:
>  * Argentina
>  * Indien
>  * Marocko
>  * Pakistan
>
> Efter den 1 augusti 2019 definierar **visnings** parametern det returnerade kart innehållet för de nya regionerna/länderna som anges ovan. Azure Maps **View** -parametern (kallas även för "användar region parameter") är en ISO-3166-landskod som visar rätt kartor för landet/regionen och som anger vilken uppsättning geografi innehåll som anges av polypolitiskt som returneras via Azure Maps Services, inklusive kant linjer och etiketter som visas på kartan. 

Se till att ställa in parametern **Visa** som krävs för REST-API: er och SDK: er som dina tjänster använder.
  

### <a name="rest-apis"></a>REST-API: er
  
Se till att du har ställt in parametern Visa efter behov. Visa parameter anger vilken uppsättning av ett interpolitiskt innehåll som ska returneras via Azure Maps Services. 

Berörda Azure Maps REST-tjänster:
    
 * Panelen Hämta karta
 * Hämta kart bild 
 * Hämta en sökning i fuzzy
 * Hämta Sök-POI
 * Hämta Sök POI kategori
 * Hämta Sök i närheten
 * Hämta Sök adress
 * Hämta Sök adressen strukturerad
 * Hämta omvänd söknings adress
 * Hämta Sök adress omvänt kors gatan
 * Publicera sökning i geometri
 * Publicera söknings adress batch
 * Publicera omvänd kommando adress
 * Publicera sökning längs väg
 * Publicera sökning i fuzzy-batch

 
### <a name="sdks"></a>SDK:er

Kontrol lera att du har ställt in parametern **Visa** efter behov och att du har den senaste versionen av Web SDK och Android SDK. Berörda SDK: er:

 * Azure Maps webb-SDK
 * Azure Maps Android SDK

Som standard har parametern View angetts till **Unified**, även om du inte har definierat den i begäran. Bestäm platsen för dina användare. Ange sedan parametern **View** korrekt för den platsen. Du kan också ange "Visa = Auto", som returnerar kartdata baserat på IP-adressen för begäran.  Parametern **View** i Azure Maps måste användas i enlighet med tillämplig lagstiftning, inklusive de lagar om mappning av land/region där kartor, bilder och andra data och innehåll från tredje part som du har behörighet att komma åt via Azure Maps görs tillgängligt.


Följande tabell innehåller vyer som stöds.

| Visa         | Beskrivning                            |  Maps | Sök | JS-Kartkontroll |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Förenade Arabemiraten (arabisk vy)    |   ✓   |        |     ✓          |
| AR           | Argentina (argentinsk vy)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrain (arabisk vy)                 |   ✓   |        |     ✓          |
| IN           | Indien (indiska vyn)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arabisk vy)                    |   ✓   |        |     ✓          |
| JO           | Jordanien (arabisk vy)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (arabisk vy)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arabisk vy)                 |   ✓   |        |     ✓          |
| MA           | Marocko (marockansk View)                |   ✓   |   ✓     |     ✓          |
| OM           | Oman (arabisk vy)                    |   ✓   |        |     ✓          |
| PK           | Pakistan (pakistansk vy)              |   ✓   |    ✓    |     ✓          |
| PS           | Palestinska myndigheten (arabisk vy)    |   ✓   |        |     ✓          |
| QA           | Qatar (arabisk vy)                   |   ✓   |        |     ✓          |
| SA           | Saudiarabien (arabisk vy)            |   ✓   |        |     ✓          |
| SY           | Syrien (arabisk vy)                   |   ✓   |        |     ✓          |
| CHANSEN           | Jemen (arabisk vy)                   |   ✓   |        |     ✓          |
| Automatisk         | Returnera mappnings data baserat på IP-adressen för begäran.|   ✓   |    ✓   |     ✓          |
| Gemensam      | Enhetlig vy (andra)                  |   ✓   |   ✓     |     ✓          |
