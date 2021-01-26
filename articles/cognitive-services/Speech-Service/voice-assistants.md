---
title: Röst assistenter – tal tjänst
titleSuffix: Azure Cognitive Services
description: En översikt över funktionerna, funktionerna och begränsningarna för röst assistenter med hjälp av Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: fc85eec008ef099d63d538e4871a1a84573f5a18
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790453"
---
# <a name="what-is-a-voice-assistant"></a>Vad är röstassistent?

Röst assistenter som använder tal tjänsten gör det möjligt för utvecklare att skapa naturliga, mänskliga konversations gränssnitt för sina program och upplevelser.

Röst assistent tjänsten ger snabb, tillförlitlig interaktion mellan en enhet och en assistent implementering som använder antingen (1) [direkt linje tal](direct-line-speech.md) (via Azure bot service) för att lägga till röst funktioner till din robotar, eller (2) anpassade kommandon för röst kommando scenarier.

## <a name="choosing-an-assistant-solution"></a>Välja en assistent lösning

Det första steget för att skapa en röst assistent är att bestämma vad det ska göra. Tal tjänsten innehåller flera, kompletterande lösningar för att utforma dina assistenters interaktioner. Du kan lägga till röst-och röst funktioner i din flexibla och mångsidiga robot som skapats med hjälp av Azure Bot Service med den [direkta rad igenkännings](direct-line-speech.md) kanalen, eller använda enklast för att redigera en app med [anpassade kommandon](custom-commands.md) för scenarier med röst kommandon.

| Om du vill... | Överväg sedan... | Till exempel … |
|-------------------|------------------|----------------|
|Öppen konversation med robust kompetens integrering och fullständig distributions kontroll | Azure Bot Service robot med [direkt linje tal](direct-line-speech.md) kanal | <ul><li>"Jag måste gå till Seattle"</li><li>"Vilken typ av pizza kan jag beställa?"</li></ul>
|Röst kommando eller enkla samorienterade konversationer med förenklad redigering och värd tjänster | [Anpassade kommandon](custom-commands.md) | <ul><li>"Sätt på overhead-ljus"</li><li>"Gör 5 grader till varma"</li><li>Andra exempel [finns här](https://speech.microsoft.com/customcommands)</li></ul>

Vi rekommenderar [direkt linje tal](direct-line-speech.md) som det bästa valet om du inte är säker på vad du vill att din assistent ska hantera. Den erbjuder integrering med en omfattande uppsättning verktyg och redigerings hjälpmedel, till exempel den [virtuella assistenten och företags mal len](/azure/bot-service/bot-builder-enterprise-template-overview) och den [QNA Maker tjänsten](../qnamaker/overview/overview.md) för att bygga på vanliga mönster och använda dina befintliga kunskaps källor.

Med [anpassade kommandon](custom-commands.md) kan du enkelt bygga avancerade röst kommando program som är optimerade för röst-och första interaktions upplevelser. Det ger en enhetlig redigerings upplevelse, en automatisk värd modell och relativt lägre komplexitet, vilket hjälper dig att fokusera på att skapa den bästa lösningen för röst kommando scenarier.

   ![Jämförelse av Assistant-lösningar](media/voice-assistants/assistant-solution-comparison.png "Jämförelse av Assistant-lösningar")


## <a name="reference-architecture-for-building-a-voice-assistant-using-the-speech-sdk"></a>Referens arkitektur för att skapa en röst assistent med hjälp av talet SDK

   ![Koncept diagram för röst assistentens Dirigerings tjänst flöde](media/voice-assistants/overview.png "Röst assistent flödet")

## <a name="core-features"></a>Kärn funktioner

Oavsett om du väljer [direkt linje tal](direct-line-speech.md) eller [anpassade kommandon](custom-commands.md) för att skapa dina Assistant-interaktioner, kan du använda en omfattande uppsättning anpassnings funktioner för att anpassa din assistent till ditt varumärke, din produkt och din personlighet.

| Kategori | Funktioner |
|----------|----------|
|[Anpassat nyckelord](./custom-keyword-basics.md) | Användare kan starta konversationer med assistenter med ett anpassat nyckelord som "Hej contoso". En app gör detta med en anpassad nyckelords motor i tal-SDK: n, som kan konfigureras med ett anpassat nyckelord [som du kan generera här](./custom-keyword-basics.md). Röst assistenter kan använda kontroll av nyckelord på tjänst sidan för att förbättra noggrannheten för nyckelords aktiveringen (oberoende av enheten).
|[Tal till text](speech-to-text.md) | Röst assistenter konverterar ljud i real tid till identifierad text med [tal-till-text](speech-to-text.md) från tal tjänsten. Den här texten är tillgänglig, eftersom den har tilldelats till både din assistent implementering och ditt klient program.
|[Text till tal](text-to-speech.md) | Text svar från din assistent är syntetiskt med [text-till-tal](text-to-speech.md) från tal-tjänsten. Denna syntes görs sedan tillgänglig för ditt klient program som en ljud ström. Microsoft erbjuder möjlighet att bygga din egen anpassade, högkvalitativa neurala TTS-röst som ger en röst till ditt varumärke. [Kontakta oss](mailto:mstts@microsoft.com)om du vill veta mer.

## <a name="getting-started-with-voice-assistants"></a>Komma igång med röst assistenter

Vi erbjuder snabb starter som har utformats för att du ska kunna köra kod på mindre än 10 minuter. Den här tabellen innehåller en lista med snabb starter för röst assistenten, sorterade efter språk.

* [Snabb start: skapa en anpassad röst assistent med direkt linje tal](quickstarts/voice-assistants.md)
* [Snabb start: bygga en app med röst kommandon med anpassade kommandon](quickstart-custom-commands-application.md)

## <a name="sample-code-and-tutorials"></a>Exempel kod och självstudier

Exempel kod för att skapa en röst assistent finns på GitHub. Dessa exempel avser klient programmet för att ansluta till din assistent i flera populära programmeringsspråk.

* [Röst assistents exempel på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)
* [Självstudie: röst aktivera din assistent som skapats med Azure Bot Service med C#-talet SDK](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Självstudie: skapa ett program med anpassade kommandon med enkla röst kommandon](./how-to-develop-custom-commands-application.md)

## <a name="customization"></a>Anpassning

Röst assistenter som skapats med Azure Speech Services kan använda alla anpassnings alternativ.

* [Custom Speech](./custom-speech-overview.md)
* [Anpassad röst](how-to-custom-voice.md)
* [Anpassade nyckelord](custom-keyword-overview.md)

> [!NOTE]
> Anpassnings alternativen varierar efter språk/språk (se [språk som stöds](language-support.md)).

## <a name="next-steps"></a>Nästa steg

* [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)
* [Läs mer om anpassade kommandon](custom-commands.md)
* [Läs mer om direkt linje tal](direct-line-speech.md)
* [Hämta tal-SDK](speech-sdk.md)