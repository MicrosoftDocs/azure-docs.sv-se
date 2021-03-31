---
title: Talarigenkänning översikt – tal tjänst
titleSuffix: Azure Cognitive Services
description: Talarigenkänning innehåller algoritmer som verifierar och identifierar högtalare genom sina unika röst egenskaper med röst biometry. Talarigenkänning används för att besvara frågan "Vem talar?". Den här artikeln är en översikt över fördelarna och funktionerna i Talarigenkännings tjänsten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: högtalar igenkänning, röst biometry
ms.openlocfilehash: d07a9960c8a586fa137b4b717afbf91740c265d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015332"
---
# <a name="what-is-speaker-recognition-preview"></a>Vad är Talarigenkänning (för hands version)?

Tjänsten Talarigenkänning tillhandahåller algoritmer som verifierar och identifierar högtalare genom sina unika röst egenskaper med röst biometry. Talarigenkänning används för att besvara frågan "Vem talar?". Du ger ljudutbildnings data för en enskild talare, vilket skapar en registrerings profil baserat på högtalar röstens unika egenskaper. Du kan sedan stryka ljud röst exempel mot den här profilen för att kontrol lera att högtalaren är samma person (högtalar verifiering) eller kors kontroll ljud röst exempel mot en *grupp* med registrerade högtalar profiler, för att se om den matchar någon profil i gruppen (högtalar identifiering). Som kontrast Diarization grupperar [högtalare](batch-transcription.md#speaker-separation-diarization) segment av ljud efter talare i en batch-åtgärd.

## <a name="speaker-verification"></a>Talarverifiering

Talarverifiering effektiviserar processen med att verifiera en registrerad högtalar identitet med antingen lösen fraser eller röst ingångar i fritext. Det kan användas för att kontrol lera att personer är säkra, friktions lösa kund engagemang i en mängd olika lösningar, från kund identitets verifiering i samtals Center till kontakt med mindre funktions åtkomst.

### <a name="how-does-speaker-verification-work"></a>Hur fungerar Talarverifiering?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Talarverifiering flödes schema.":::

En högtalar verifiering kan vara antingen text beroende eller text oberoende. **Text beroende** verifiering innebär att högtalare måste välja samma lösen fras som ska användas vid både registrerings-och verifierings faserna. **Text oberoende** verifiering innebär att talare kan tala på vardagligt språk i registrerings-och verifierings fraser.

För **text beroende** verifiering registreras högtalar rösten genom att säga en lösen fras från en uppsättning fördefinierade fraser. Röst funktioner extraheras från ljud inspelningen för att bilda en unik röst signatur, medan den valda lösen frasen också känns igen. Tillsammans används röst signaturen och lösen frasen för att verifiera högtalaren. 

**Text oberoende** verifiering har inga begränsningar för vad högtalaren står under registreringen eller i ljud exemplet som ska kontrol leras eftersom den bara extraherar röst funktioner till poängen. 

API: erna är inte avsedda att avgöra om ljudet kommer från en Live-person eller en imiterad/inspelning av en registrerad talare. 

## <a name="speaker-identification"></a>Talaridentifiering

Talaridentifiering används för att fastställa en okänd högtalar identitet i en grupp med registrerade högtalare. Med Talaridentifiering kan du använda tal till enskilda högtalare och låsa upp värdet från scenarier med flera högtalare, till exempel:

* Support lösningar för fjärran sluten Mötes produktivitet 
* Bygg anpassning av enheter med flera användare

### <a name="how-does-speaker-identification-work"></a>Hur fungerar Talaridentifiering?

Registrering av högtalar identifiering är **text oberoende**, vilket innebär att det inte finns några begränsningar för vad högtalaren står i ljudet. På samma sätt som Talarverifiering, i registrerings fasen, spelas högtalar rösten in och röst funktioner extraheras för att bilda en unik röst under skrift. I identifierings fasen jämförs exemplet med ininspelnings röst med en angiven lista med registrerade röster (upp till 50 i varje begäran).

## <a name="data-security-and-privacy"></a>Data säkerhet och sekretess

Högtalar registrerings data lagras i ett skyddat system, inklusive tal ljudet för registrering och funktionerna för röst-signaturer. Tal ljudet för registrering används bara när algoritmen uppgraderas och funktionerna måste extraheras igen. Tjänsten behåller inte tal inspelningen eller de extraherade röst funktionerna som skickas till tjänsten under igenkännings fasen. 

Du kan styra hur länge data ska bevaras. Du kan skapa, uppdatera och ta bort registrerings data för enskilda högtalare via API-anrop. När prenumerationen tas bort, tas även alla högtalar registrerings data som är associerade med prenumerationen bort. 

Precis som med alla Cognitive Services-resurser måste utvecklare som använder tjänsten Talarigenkänning vara medvetna om Microsofts principer för kund information. Du bör se till att du har fått rätt behörighet från användarnas Talarigenkänning. Mer information finns på [sidan Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   på Microsoft Trust Center. 

## <a name="common-questions-and-solutions"></a>Vanliga frågor och lösningar

| Fråga | Lösning |
|---------|----------|
| Vilka scenarier kan Talarigenkänning användas för? | Call Center kundverifiering, Voice-baserad patient incheckning, Mötes avskrift, anpassning av flera användare enheter|
| Vad är skillnaden mellan identifiering och verifiering? | Identifiering är en process för att identifiera vilken medlem från en grupp av högtalare som talar. Verifiering handlar om att bekräfta att en talare matchar en känd eller **registrerad** röst.|
| Vad är skillnaden mellan text beroende och text oberoende verifiering? | Text beroende verifiering kräver en speciell pass fras för både registrering och igenkänning. För text oberoende verifiering krävs ett längre röst exempel för registrering, men allt kan ses, inklusive under igenkänning.|
| Vilka språk stöds? | Engelska, franska, spanska, kinesiska, tyska, italienska, japanska och portugisiska |
| Vilka Azure-regioner stöds? | Talarigenkänning är en för hands versions tjänst och är för närvarande endast tillgänglig i regionen USA, västra.|
| Vilka ljudformat stöds? | Mono 16-bitars, 16kHz PCM-kodad WAV |
| Att **acceptera** och **avvisa** svar är inte korrekt, Hur justerar du tröskelvärdet? | Eftersom det optimala tröskelvärdet varierar mycket med scenarier, bestämmer API: et om du vill acceptera eller avvisa genom att bara baseras på ett standard tröskelvärde på 0,5. Avancerade användare uppmanas att åsidosätta standard beslutet och finjustera resultatet baserat på ditt eget scenario. |
| Kan du registrera en talare flera gånger? | Ja, för text beroende verifiering kan du registrera en högtalare upp till 50 gånger. För text oberoende verifiering eller högtalar identifiering kan du registrera dig med upp till 300 sekunders ljud. |
| Vilka data lagras i Azure? | Ljud för registrering lagras i tjänsten tills röst profilen [tas bort](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments). Igenkänning av ljud exempel behålls inte eller lagras inte. |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> * I artikeln Talarigenkänning [grundläggande](./get-started-speaker-recognition.md) information hittar du en körning av vanliga design mönster som du kan använda i dina program.
> * Se [video självstudien](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) för verifiering av text oberoende högtalare.