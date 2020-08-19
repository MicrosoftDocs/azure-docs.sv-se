---
title: Viktig information – tal tjänst
titleSuffix: Azure Cognitive Services
description: En körnings logg av funktioner för igenkänning av tal tjänster, förbättringar, fel korrigeringar och kända problem.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 8df54adf8f3aeaa58e30d6d9218cec8bec8c1121
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525929"
---
# <a name="speech-service-release-notes"></a>Viktig information om Speech service

## <a name="text-to-speech-2020-august-release"></a>Text till tal 2020 – augusti-utgåva

### <a name="new-features"></a>Nya funktioner

* **NEURALA TTS: ny tal format för `en-US` Aria röst**. AriaNeural kan ge ett ljud som en newscaster när de läser nyheter. Formatet "newscast-formell" låter mer allvarligt, medan formatet "newscast-vardaglig" är mer avslappnad och informell. Se [hur du använder tal formatet i SSML](speech-synthesis-markup.md).

* **Anpassad röst: en ny funktion släpps automatiskt för att kontrol lera kvaliteten på tränings data**. När du överför dina data kommer systemet att undersöka olika aspekter av dina ljud-och avskrifts data och automatiskt korrigera eller filtrera problem för att förbättra kvaliteten på röst modellen. Detta täcker Ljudets volym, brus nivån, tal igenkännings precisionen, justeringen av tal med normaliserad text, tystnad i ljudet, förutom ljud-och skript formatet. 

* **Skapa ljud innehåll: en uppsättning nya funktioner för att aktivera mer kraftfulla funktioner för röst justering och ljud hantering**.

    * Uttal: funktionen för inuttals justering uppdateras till den senaste fonem-uppsättningen. Du kan välja rätt fonem-element från biblioteket och förfina uttal av de ord som du har valt. 

    * Ladda ned: funktionen "Ladda ned"/"Exportera" har förbättrats för att ge stöd för att generera ljud per stycke. Du kan redigera innehållet i samma fil-SSML, samtidigt som du genererar flera ljud resultat. Fil strukturen för "Ladda ned" är också raffinerad. Nu kan du enkelt hämta alla ljud i en mapp. 

    * Aktivitets status: export upplevelsen med flera filer har förbättrats. När du exporterar flera filer tidigare kommer hela aktiviteten att Miss lyckas om en av filerna har misslyckats. Men nu kommer alla andra filer att exporteras. Uppgifts rapporten är omfattande och mer detaljerad och strukturerad information. Du kan kontrol lera loggarna för alla misslyckade filer och meningar nu med rapporten. 

    * SSML-dokumentation: länkat till SSML-dokument för att hjälpa dig att kontrol lera reglerna för hur du använder alla justerings funktioner.

* **Röst listans API har uppdaterats med ett användarvänligt visnings namn och de tal format som stöds för neurala-röster**.

### <a name="general-tts-voice-quality-improvements"></a>Vanliga förbättringar av tal röst kvalitet

* Minskat uttal-fel i Word-nivå% för `ru-RU` (fel som minskas med 56%) och `sv-SE` (fel minskat med 49%)

* Förbättrat Polyphony ord som läses på `en-US` neurala-röster med 40%. Exempel på Polyphony-ord är "Read", "Live", "content", "Record", "Object" osv. 

* Förbättrade frågans naturliga ton i `fr-FR` . MOS (genomsnittligt yttrande Poäng): + 0,28

* Uppdaterade vocoders för följande röster, med åter givningen av förbättringarna och den övergripande prestanda hastigheten med 40%.

    | Nationell inställning | Röst |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun – Hi |

### <a name="bug-fixes"></a>Felkorrigeringar

* Ett antal buggar har åtgärd ATS med verktyget för att skapa ljud innehåll 
    * Åtgärdat problem med automatisk uppdatering. 
    * Korrigerade problem med röst format i zh-CN i den Asien, sydöstra regionen.
    * Fast stabilitets problem, inklusive ett export fel med taggen Break och fel i skiljetecken.    

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nya språk som är tal till text: 2020 – augusti version
Inläsning av tal till text 26 nya språk i augusti: 2 europeiska språk `cs-CZ` och `hu-HU` , 5 engelska lokala och 19 spanska nationella inställningar som avser de flesta länder i södra Amerika. Nedan visas en lista över de nya språken. Se den fullständiga språk listan [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support).

| Nationell inställning  | Språk                          |
|---------|-----------------------------------|
| `cs-CZ` | Tjeckiska (Tjeckien)            | 
| `en-HK` | Engelska (Hongkong)               | 
| `en-IE` | Engelska (Irland)                 | 
| `en-PH` | Engelska (Filippinerna)             | 
| `en-SG` | Engelska (Singapore)               | 
| `en-ZA` | Engelska (Sydafrika)            | 
| `es-AR` | Spanska (Argentina)               | 
| `es-BO` | Spanska (Bolivia)                 | 
| `es-CL` | Spanska (Chile)                   | 
| `es-CO` | Spanska (Colombia)                | 
| `es-CR` | Spanska (Costa Rica)              | 
| `es-CU` | Spanska (Kuba)                    | 
| `es-DO` | Spanska (Dominikanska republiken)      | 
| `es-EC` | Spanska (Ecuador)                 | 
| `es-GT` | Spanska (Guatemala)               | 
| `es-HN` | Spanska (Honduras)                | 
| `es-NI` | Spanska (Nicaragua)               | 
| `es-PA` | Spanska (Panama)                  | 
| `es-PE` | Spanska (Peru)                    | 
| `es-PR` | Spanska (Puerto Rico)             | 
| `es-PY` | Spanska (Paraguay)                | 
| `es-SV` | Spanska (El Salvador)             | 
| `es-US` | Spanska (USA)                     | 
| `es-UY` | Spanska (Uruguay)                 | 
| `es-VE` | Spanska (Venezuela)               | 
| `hu-HU` | Ungerska (Ungern)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK-1.13.0:2020 – juli version

**Obs!** Speech SDK i Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Hämta och installera den [här](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nya funktioner**
- **C#**: stöd har lagts till för asynkron konversations avskrift. Se dokumentationen [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-async-conversation-transcription).  
- **Java Script**: ytterligare Talarigenkänning stöd för både [webbläsare](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) och [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **Java Script**: stöd har lagts till för automatisk språk identifiering/språk-ID. Se dokumentationen [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-javascript).
- **Mål-C**: stöd har lagts till för konversations-och [konversations avskrifter](https://docs.microsoft.com/azure/cognitive-services/speech-service/conversation-transcription)i [flera enheter](https://docs.microsoft.com/azure/cognitive-services/speech-service/multi-device-conversation) . 
- **Python**: stöd för komprimerad ljud för python i Windows och Linux. Se dokumentationen [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 

**Fel korrigeringar**
- **Alla**: åtgärdat ett problem som gjorde att KeywordRecognizer inte flyttar vidarebefordrar data strömmar efter en igenkänning.
- **Alla**: åtgärdat ett problem som orsakade data strömmen som hämtades från en KeywordRecognitionResult som inte innehåller nyckelordet.
- **Alla**: åtgärdat ett problem som SendMessageAsync inte faktiskt skickar meddelandet över ledningen när slutanvändarna har slutat att vänta.
- **Alla**: åtgärdade en krasch i Talarigenkänning API: er när användare anropar VoiceProfileClient:: SpeakerRecEnrollProfileAsync-metoden flera gånger och väntade inte på att anropen skulle slutföras.
- **Alla**: åtgärdad aktivera fil loggning i VoiceProfileClient-och SpeakerRecognizer-klasser.
- **Java Script**: åtgärdat ett [problem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) med begränsning när webbläsaren minimeras.
- **Java Script**: åtgärdat ett [problem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) med minnes läckor på strömmar.
- **Java Script**: cachelagring har lagts till för OCSP-svar från NodeJS.
- **Java**: åtgärdat ett problem som gjorde att BigInteger-fält alltid returnerade 0.
- **iOS**: Åtgärdade ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) med publicering av tal SDK-baserade appar i iOS App Store.

**Exempel**
- **C++**: exempel kod har lagts till för Talarigenkänning [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!

## <a name="text-to-speech-2020-july-release"></a>Text till tal 2020 – juli version

### <a name="new-features"></a>Nya funktioner

* **NEURALA TTS, 15 nya neurala-röster**: de nya rösterna som läggs till i neurala TTS-portföljen är Salma i `ar-EG` arabiska (Egypten), Zariyah i `ar-SA` arabiska (Saudiarabien), Alba i `ca-ES` katalanska (Spanien), Christel i `da-DK` danska (Danmark), Neerja på `es-IN` engelska (Indien). Noora i `fi-FI` finska (Finland), swara i `hi-IN` hindi (Indien), Colette i `nl-NL` nederländska (Nederländerna), Zofia i `pl-PL` polska (Polen), Fernanda i `pt-PT` portugisiska (Portugal), Dariya i `ru-RU` ryska (Ryssland), Hillevi i `sv-SE` svenska (Sverige), Achara i `th-TH` thailändska (Thailand), HiuGaai på `zh-HK` kinesiska (kantonesiska, traditionell) och HsiaoYu i `zh-TW` kinesiska (Taiwan-mandariner). Kontrol lera alla [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices).  

* **Anpassad röst, strömlinjeformad röst testning med utbildnings flödet för att förenkla användar upplevelsen**: med den nya testnings funktionen testas varje röst automatiskt med en fördefinierad test uppsättning som är optimerad för varje språk och som kan användas i scenarier med allmänt röst och röst assistent. De här test uppsättningarna väljs noggrant och testas för att inkludera typiska användnings fall och fonem på språket. Förutom kan användarna fortfarande välja att överföra egna test skript när de tränar en modell.

* **Skapa ljud innehåll: en uppsättning nya funktioner släpps för att aktivera mer kraftfulla funktioner för röst justering och ljud hantering**

    * `Pitch`, `rate` och `volume` har förbättrats för att stödja justering med ett fördefinierat värde, t. ex. långsam, medelhög och snabb. Det är nu enkelt för användarna att välja ett konstant värde för ljud redigering.

    ![Ljud justering](media/release-notes/audio-tuning.png)

    * Användarna kan nu granska `Audio history` för sin arbets fil. Med den här funktionen kan användare enkelt spåra alla genererade ljud som är relaterade till en fungerande fil. De kan kontrol lera historik versionen och jämföra kvaliteten samtidigt som du justerar. 

    ![Ljud historik](media/release-notes/audio-history.png)

    * `Clear`Funktionen är nu mer flexibel. Användare kan rensa en viss justerings parameter samtidigt som andra parametrar är tillgängliga för det valda innehållet.  

    * En självstudie video lades till på [landnings sidan](https://speech.microsoft.com/audiocontentcreation) för att hjälpa användarna att snabbt komma igång med tal röst justering och ljud hantering. 

### <a name="general-tts-voice-quality-improvements"></a>Vanliga förbättringar av tal röst kvalitet

* Förbättrad text i TTS-vocoder för högre åter givning och lägre latens.

    * Uppdaterade Elsa i `it-IT` till en ny vocoder som uppnått + 0,464 CMOS (jämför medelvärde) vinst i röst kvalitet, 40% snabbare i syntes och 30% minskning vid första byte-fördröjning. 
    * Uppdaterade Xiaoxiao i `zh-CN` till den nya vocoder med + 0148 CMOS-vinst för den allmänna domänen + 0,348 för newscast-formatet och + 0,195 för Lyrical-formatet. 

* Uppdaterade `de-DE` och `ja-JP` röst modeller för att göra TTS-utdata mer naturlig.
    
    * Uppdaterad Katja i `de-DE` med den senaste prosody-modell metoden, MOS (Mean score score) är + 0,13. 
    * Uppdaterad Nanami i `ja-JP` med en ny prosody-modell med en ny färg modell, MOS (Mean score score) är + 0,19;  

* Bättre uttal av ord nivå på 5 språk.

    | Språk | Fel minskning av uttal |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 43 |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Felkorrigeringar

* Valuta läsning
    * Har åtgärdat problemet med valuta läsning för `es-ES` och `es-MX`
     
    | Språk | Indata | Avläsning efter förbättring |
    |---|---|---|
    | `es-MX` | $1,58 | cincuenta pesos y Ocho Centavos |
    | `es-ES` | $1,58 | un dólar cincuenta y Ocho Centavos |

    * Stöd för negativ valuta (t. ex. "-€325") i följande språk:,,,,, `en-US` `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Förbättrad adress läsning i `pt-PT` .
* Natasha ( `en-AU` ) och Libby ( `en-UK` ) uttal på ordet "for" och "fyra".  
* Fast buggar i verktyget för att skapa ljud innehåll
    * Den ytterligare och oväntade pausen efter det andra stycket har åtgärd ATS.  
    * Funktionen "ingen rast" läggs tillbaka från en Regressions bugg. 
    * Det slumpmässiga uppdaterings problemet för tal Studio är fast.  

### <a name="samplessdk"></a>Exempel/SDK

* Java Script: korrigerar uppspelnings problem i Firefox och Safari på macOS och iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK-1.12.1:2020 – juni version
**Tal-CLI (även kallat SPX)**
-   Tillagda hjälp Sök funktioner i CLI:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Uppdaterat för att fungera med nyligen distribuerade v 3.0 batch-och Custom Speech-API: er:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nya funktioner**
-   **C \# , C++**: Talarigenkänning för hands version: den här funktionen gör det möjligt att identifiera högtalare (Vem talar?) och en högtalar verifiering (är detta som han eller hon ansöker om att vara?). Börja med en [Översikt](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview), läs [artikeln Talarigenkänning grunder](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics)eller [referens dokument för API](https://docs.microsoft.com/rest/api/speakerrecognition/).

**Fel korrigeringar**
-   **C \# , C++**: fast mikrofon inspelning fungerade inte i 1,12 i högtalar igenkänning.
-   **Java Script**: korrigeringar för text till tal i Firefox och Safari på MacOS och iOS.
-   Korrigering för åtkomst överträdelse för Windows Application Verifier kraschar i konversations avskrifter när du använder 8-kanals data ström.
-   Korrigering för åtkomst överträdelse för Windows Application Verifier kraschar vid konversation med flera enheter.

**Exempel**
-   **C#**: [kod exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) för högtalar igenkänning.
-   **C++**: [kod exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) för högtalar igenkänning.
-   **Java**: [kod exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) för avsikts igenkänning på Android. 

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK-1.12.0:2020 – maj-version
**Tal-CLI (även känt som SPX)**
- **SPX** är ett nytt kommando rads verktyg som du kan använda för att utföra igenkänning, syntes, översättning, batch-avskrift och anpassad tal hantering från kommando raden. Använd den för att testa tal tjänsten eller för att skapa skript för de tal tjänst uppgifter som du behöver utföra. Hämta verktyget och Läs dokumentationen [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview).

**Nya funktioner**
- **Go**: nytt go-språkstöd för [tal igenkänning](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) och [anpassad röst assistent](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). Konfigurera din utvecklings miljö [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go). Exempel kod finns i avsnittet exempel nedan. 
- **Java Script**: stöd för webb läsar stöd för text till tal har lagts till. Se dokumentationen [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript).
- **C++, C#, Java**: nytt `KeywordRecognizer` objekt och API: er som stöds på Windows-, Android-, Linux & iOS-plattformar. Läs dokumentationen [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview). Exempel kod finns i avsnittet exempel nedan. 
- **Java**: fler konversationer har lagts till med stöd för översättning. Se referens dokumentet [här](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Förbättringar & optimeringar**
- **Java Script**: optimerad webbläsare mikrofon implementeringen förbättrar tal igenkännings precisionen.
- **Java**: omfaktorde bindningar med hjälp av direkt jni-implementering utan swig. Detta minskar med 10X storleks bindningar för alla Java-paket som används för Windows, Android, Linux och Mac och underlättar ytterligare utveckling av tal SDK Java-implementeringen.
- **Linux**: uppdaterad support [dokumentation](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux) med de senaste RHEL 7-speciella anteckningarna.
- Förbättrad anslutnings logik för att försöka ansluta flera gånger i händelse av tjänst-och nätverks fel.
- Uppdaterad start sidan för [Portal.Azure.com](https://portal.azure.com) -tal för att hjälpa utvecklare att ta nästa steg i Azure-tal resan.

**Fel korrigeringar**
- **C#, Java**: Åtgärdade ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) med att läsa in SDK-bibliotek på Linux arm (både 32 och 64 bitar).
- **C#**: fast explicit avyttring av egna referenser för TranslationRecognizer-, IntentRecognizer-och Connection-objekt.
- **C#**: hantering av livs längd för fasta ljud värden för ConversationTranscriber-objekt.
- Ett problem har åtgärd ATS där `IntentRecognizer` resultat orsaken inte angavs korrekt vid tolkning av avsikter från enkla fraser.
- Ett problem har åtgärd ATS där `SpeechRecognitionEventArgs` resultat förskjutning inte angavs korrekt.
- Ett tävlings villkor har åtgärd ATS där SDK försökte skicka ett nätverks meddelande innan WebSocket-anslutningen öppnades. Har reproducerats för `TranslationRecognizer` när deltagarna lades till.
- Fasta minnes läckor i nyckelords igenkännings motorn.

**Exempel**
- **Go**: nya snabb starter för [tal igenkänning](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go) och [anpassad röst assistent](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go). Hitta exempel kod [här](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **Java Script**: nya snabb starter för [text till tal](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript)-, [översättnings](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/translate-speech-to-text?pivots=programming-language-javascript)-och [Avsiktsigenkänning](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript).
- Exempel på nyckelords igenkänning för [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) och [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK-1.11.0:2020 – mars release
**Nya funktioner**
- Linux: stöd har lagts till för Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 med [instruktioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7) för hur du konfigurerar systemet för tal-SDK.
- Linux: stöd för .NET Core C# på Linux ARM32 och ARM64 har lagts till. Läs mer [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux). 
- C#, C++: lades till `UtteranceId` i `ConversationTranscriptionResult` , ett konsekvent ID för alla resultat från mellanliggande och slutlig tal igenkänning. Information för [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: stöd har lagts till för `Language ID` . Se speech_sample. py i [GitHub lagrings platsen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: stödet för komprimerad ljud inspelning har lagts till i Windows-plattformen för alla Win32-konsol program. Information [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). 
- Java Script: stöd för tal syntes (text till tal) i NodeJS. Läs mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- Java Script: Lägg till nya API: er för att aktivera inspektion av alla meddelanden som skickas och tagits emot. Läs mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Fel korrigeringar**
- C#, C++: åtgärdat ett problem `SendMessageAsync` skickar nu binärt meddelande som binär typ. Information för [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection).
- C#, C++: ett problem där händelse används `Connection MessageReceived` kan orsaka krasch om `Recognizer` tas bort före `Connection` objektet. Information för [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet), [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived).
- Android: ljudbuffertens storlek från mikrofonen minskade från 800ms till 100 ms för att förbättra svars tiden.
- Android: Åtgärdade ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) med x86 Android-emulator i Android Studio.
- Java Script: stöd har lagts till för regioner i Kina med `fromSubscription` API: et. Information [här](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-). 
- Java Script: Lägg till mer fel information för anslutnings fel från NodeJS.
        
**Exempel**
- Unit: det offentliga exemplet för avsikts igenkänning är fast, där LUIS JSON-importen misslyckades. Information [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: exempel tillagt `Language ID` . Information [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19 förkortad-testning:** Det gick inte att utföra så mycket manuell testning av enhets verifieringen på grund av att det går att fjärrstyra de senaste veckorna. Ett exempel på detta är att testa mikrofon ingång och högtalare på Linux, iOS och macOS. Vi har inte gjort några ändringar som vi tror skulle kunna ha avbrutit någonting på dessa plattformar och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Tack för din fortsatta support. Som alltid kan du skicka frågor eller feedback om [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) eller [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Håll dig frisk!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK-1.10.0:2020 – februari utgåva

**Nya funktioner**

 - Python-paket har lagts till för att stödja den nya 3,8-versionen av python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 support (C++, C#, Java, python).
   > [!NOTE] 
   > Kunderna måste konfigurera OpenSSL enligt [dessa instruktioner](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
 - Linux ARM32-stöd för Debian och Ubuntu.
 - DialogServiceConnector stöder nu en valfri "robot ID"-parameter på BotFrameworkConfig. Med den här parametern kan du använda flera direkt linje tal robotar med en enda Azure tal-resurs. Utan den angivna parametern används standard-boten (som fastställs av konfigurations sidan för direkt linjens tal kanal).
 - DialogServiceConnector har nu en SpeechActivityTemplate-egenskap. Innehållet i den här JSON-strängen används av direkt linje tal för att fylla i en rad olika fält som stöds i alla aktiviteter som når en direkt rad tal robot, inklusive aktiviteter som skapas automatiskt som svar på händelser som tal igenkänning.
 - TTS använder nu prenumerations nyckeln för autentisering, vilket minskar den första byte fördröjningen för det första syntes resultatet när du har skapat en synthesizer.
 - Uppdaterade tal igenkännings modeller för 19 språk i ett genomsnittligt antal fel i Word-felgrader på 18,6% (es-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, NB-NO, fi-fi, ru-RU, pl-PL, ca-ES, zh-TW, Th-PT, tr-TR). De nya modellerna ger avsevärda förbättringar i flera domäner, inklusive diktering, samtals Center avskrifter och video indexerings scenarier.

**Fel korrigeringar**

 - Åtgärdat fel där konversations program varan inte förväntades korrekt i JAVA-API: er 
 - Android x86 emulator-korrigering för Xamarin [GitHub-problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Lägg till saknas (Hämta | Ange) egenskaps metoder för att AudioConfig
 - Åtgärda ett TTS-fel där audioDataStream inte kunde stoppas när anslutningen Miss lyckas
 - Om du använder en slut punkt utan en region skulle det orsaka USP-fel för konversations översättare
 - ID-generering i universella Windows-program använder nu en lämplig unik GUID-algoritm. den tidigare och oavsiktligt har oavsiktligt valt en stubbed-implementering som ofta producerar kollisioner över stora uppsättningar interaktioner.
 
 **Exempel**
 
 - Enhets exempel för att använda tal-SDK med enhets [uppströmnings mikrofon och push-läge](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andra ändringar**

 - [Konfigurations dokumentation för OpenSSL har uppdaterats för Linux](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>1.9.0 för tal-SDK: 2020 – januari utgåva

**Nya funktioner**

- Konversation med flera enheter: Anslut flera enheter till samma tal-eller textbaserade konversationer och eventuellt översätta meddelanden som skickas mellan dem. Läs mer i [den här artikeln](multi-device-conversation.md). 
- Stöd för nyckelords igenkänning har lagts till för Android. AAR-paketet och stöd för x86 och x64 varianter har lagts till. 
- Mål-C: `SendMessage` och `SetMessageProperty` metoder som har lagts till i `Connection` objektet. Se dokumentationen [här](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection).
- TTS C++ API stöder nu `std::wstring` som text inskrivning, och du tar bort behovet av att konvertera en wstring till sträng innan du skickar den till SDK: n. Se information [här](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [språk-ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) och [käll språks konfiguration](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp) är nu tillgängliga.
- Java Script: en funktion har lagts till i `Connection` objekt för att klara genom anpassade meddelanden från tal tjänsten som motanrop `receivedServiceMessage` .
- Java Script: stöd har lagts till för `FromHost API` att under lätta användningen av lokal behållare och suveräna moln. Se dokumentationen [här](speech-container-howto.md).
- Java Script: vi följer nu `NODE_TLS_REJECT_UNAUTHORIZED` tack vare bidrag från [orgads](https://github.com/orgads). Se information [här](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Bryta ändringar**

- `OpenSSL` har uppdaterats till version 1.1.1 b och är statiskt länkat till tal SDK Core-biblioteket för Linux. Detta kan orsaka en paus om din inkorg `OpenSSL` inte har installerats till `/usr/lib/ssl` katalogen i systemet. Läs [vår dokumentation](how-to-configure-openssl-linux.md) under Speech SDK-dokument för att lösa problemet.
- Vi har ändrat data typen som returnerades för C# `WordLevelTimingResult.Offset` från `int` till för `long` att tillåta åtkomst till `WordLevelTimingResults` när taldata är längre än två minuter.
- `PushAudioInputStream` och `PullAudioInputStream` skickar nu information om WAV-huvud till tjänsten Speech baserat på `AudioStreamFormat` , som du kan ange när de skapades. Kunderna måste nu använda [formatet för ljud inspelning som stöds](how-to-use-audio-input-streams.md). Andra format kommer att få under optimala igenkännings resultat eller kan orsaka andra problem. 

**Fel korrigeringar**

- Se `OpenSSL` uppdateringen under bryta ändringar ovan. Vi har åtgärdat både en tillfällig krasch och ett prestanda problem (Lås konkurrens under hög belastning) i Linux och Java. 
- Java: förbättringar av objekt stängning i höga samtidighets scenarier.
- Omstrukturerat vårt NuGet-paket. Vi har tagit bort de tre kopiorna av `Microsoft.CognitiveServices.Speech.core.dll` och `Microsoft.CognitiveServices.Speech.extension.kws.dll` under lib-mappar, vilket gör att NuGet-paketet blir mindre och snabbare att ladda ned och vi har lagt till rubriker som behövs för att kompilera vissa C++ inbyggda appar.
- Fasta snabb starts exempel [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp). De stängdes utan att "Det gick inte att hitta" mikrofon hittades "på Linux, macOS, Windows.
- Fast SDK kraschar med långa tal igenkännings resultat på vissa kod sökvägar som [det här exemplet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp).
- Fast SDK-distributions fel i Azure-webbappens miljö för att åtgärda [det här kund problemet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396).
- Åtgärdat ett TTS-fel vid användning av flera `<voice>` taggar eller `<audio>` taggar för att hantera [det här kund problemet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Ett fel har åtgärd ATS i TTS 401 när SDK: n återställs från pausad.
- Java Script: fast en cirkulär import av ljuddata tack till ett bidrag från [euirim](https://github.com/euirim). 
- Java Script: stöd för att konfigurera tjänst egenskaper har lagts till i 1,7.
- Java Script: åtgärdat ett problem där ett anslutnings fel kan resultera i kontinuerliga misslyckade WebSocket-återanslutnings försök.

**Exempel**

- Exempel på nyckelords igenkänning har lagts till för Android [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo).
- Extra TTS-exempel för Server scenario [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs).
- Snabb starter för flera enheter har lagts till för C# och C++ [här](quickstarts/multi-device-conversation.md).

**Andra ändringar**

- Optimerad SDK kärn biblioteks storlek på Android.
- SDK i 1.9.0 och senare stöder både `int` och- `string` typerna i fältet Voice Signature-version för konversations beskrivare.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK-1.8.0:2019 – november release

**Nya funktioner**

- Ett API har lagts `FromHost()` till för att under lätta användningen av lokal behållare och suveräna moln.
- Automatisk käll Språkidentifiering har lagts till för tal igenkänning (i Java och C++)
- Objekt har lagts till `SourceLanguageConfig` för tal igenkänning, används för att ange förväntade käll språk (i Java och C++)
- Stöd har lagts till `KeywordRecognizer` för Windows (UWP), Android och iOS via NuGet-och Unity-paket
- Java API för fjärrkonversation har lagts till för att göra konversations avskrifter i asynkrona batchar.

**Bryta ändringar**

- Konversations beskrivare-funktioner flyttas under namnrymd `Microsoft.CognitiveServices.Speech.Transcription` .
- En del av konversations beskrivar metoderna flyttas till den nya `Conversation` klassen.
- Släppt stöd för 32-bitars (ARMv7 och x86) iOS

**Fel korrigeringar**

- Korrigering för krasch om Local `KeywordRecognizer` används utan en giltig prenumerations nyckel för röst tjänst

**Exempel**

- Xamarin-exempel för `KeywordRecognizer`
- Unity-exempel för `KeywordRecognizer`
- C++ och Java-exempel för automatisk käll Språkidentifiering.

## <a name="speech-sdk-170-2019-september-release"></a>1.7.0 för tal-SDK: 2019 – september version

**Nya funktioner**

- Beta stöd för Xamarin på Universell Windows-plattform (UWP), Android och iOS har lagts till
- IOS-stöd har lagts till för Unity
- `Compressed`Stöd för instöd för ALaw, Mulaw, flac på Android, iOS och Linux har lagts till
- Tillagt `SendMessageAsync` i `Connection` klass för att skicka ett meddelande till tjänsten
- Tillagt `SetMessageProperty` i `Connection` klass för att ange egenskapen för ett meddelande
- TTS-tillagda bindningar för Java (JRE och Android), python, Swift och mål-C
- TTS-stöd för uppspelning av OS för macOS, iOS och Android.
- Information om Word-gränser för TTS har lagts till.

**Fel korrigeringar**

- Fast IL2CPP build-problem på Unity 2019 för Android
- Ett åtgärds fel med felaktigt utformade huvuden i WAV-filen har bearbetats felaktigt
- Ett fast problem med UUID-nummer är inte unikt i vissa anslutnings egenskaper
- Åtgärdade några varningar om specificerare för null-värden i Swift-bindningar (kan kräva små kod ändringar)
- En bugg har åtgärd ATS som gjorde att WebSocket-anslutningar stängdes på ett smidigt sätt under nätverks belastning
- Ett problem har åtgärd ATS på Android som ibland resulterar i dubbla exponerings-ID: n som används av `DialogServiceConnector`
- Förbättringar av anslutningarnas stabilitet i flera olika interaktioner och rapportering av fel (via `Canceled` händelser) när de inträffar med `DialogServiceConnector`
- `DialogServiceConnector` Sessions-starten kommer nu att tillhandahålla händelser, inklusive vid anrop `ListenOnceAsync()` under en aktiv `StartKeywordRecognitionAsync()`
- Adresserat en krasch som är associerad med `DialogServiceConnector` aktiviteter som tas emot

**Exempel**

- Snabb start för Xamarin
- Uppdaterad CPP-snabb start med information om Linux-ARM64
- Uppdaterad enhets snabb start med iOS-information

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK-1.6.0:2019 – juni version

**Exempel**

- Snabb starts exempel för text till tal på UWP och Unity
- Snabb starts exempel för SWIFT på iOS
- Unity-exempel för tal & Avsiktsigenkänning och översättning
- Uppdaterade snabb starts exempel för `DialogServiceConnector`

**Förbättringar/ändringar**

- Namn område för dialog ruta:
  - `SpeechBotConnector` har bytt namn till `DialogServiceConnector`
  - `BotConfig` har bytt namn till `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` har mappats om till `DialogServiceConfig::FromBotSecret()`
  - Alla befintliga direkt linje tal klienter fortsätter att stödjas efter namnbytet
- Uppdatera TTS-kortet till stöd för proxy, beständig anslutning
- Förbättra fel meddelandet när en ogiltig region skickas
- SWIFT/mål-C:
  - Förbättrad fel rapportering: metoder som kan resultera i ett fel finns nu i två versioner: en som visar ett `NSError` objekt för fel hantering och ett som aktiverar ett undantag. Den tidigare visas för Swift. Den här ändringen kräver anpassningar av befintlig SWIFT-kod.
  - Förbättrad händelse hantering

**Fel korrigeringar**

- Korrigering för TTS: där `SpeakTextAsync` framtida returneras utan att vänta tills ljudet har Render ATS
- Korrigering för konvertering av strängar i C# för att aktivera stöd för fullt språk
- Åtgärda problem med .NET Core-appen för att läsa in kärn bibliotek med net461 Target Framework i exempel
- Åtgärda tillfälliga problem för att distribuera interna bibliotek till mappen utdata i exempel
- Korrigering för att webb uttag ska avslutas på ett tillförlitligt sätt
- Åtgärda möjliga krascher när du öppnar en anslutning under mycket tung belastning på Linux
- Korrigering för saknade metadata i Ramverks paketet för macOS
- Åtgärda problem med `pip install --user` Windows

## <a name="speech-sdk-151"></a>1.5.1 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Fel korrigeringar**

- Åtgärda FromSubscription när den används med konversations avskriftering.
- Åtgärda felet i Keyword upptäcka för röst assistenter.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK-1.5.0:2019 – maj-version

**Nya funktioner**

- Nyckelordet upptäcka (KWS) är nu tillgängligt för Windows och Linux. KWS-funktionen kan fungera med valfri mikrofon typ, officiellt KWS-support, men är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller i tal enheter SDK.
- Funktionen för fras tips är tillgänglig via SDK. Mer information finns [här](how-to-phrase-lists.md).
- Konversations avskrifts funktionerna är tillgängliga via SDK: n. Se [här](conversation-transcription-service.md).
- Lägg till stöd för röst assistenter med hjälp av kanalen för direkt linje tal.

**Exempel**

- Exempel för nya funktioner eller nya tjänster som stöds av SDK har lagts till.

**Förbättringar/ändringar**

- Har lagt till olika igenkännings egenskaper för att justera tjänstens beteende eller tjänst resultat (t. ex. maskering av svordomar och andra).
- Nu kan du konfigurera tolken via standard konfigurations egenskaperna, även om du har skapat tolken `FromEndpoint` .
- Mål-C: `OutputFormat` egenskapen har lagts till i `SPXSpeechConfiguration` .
- SDK stöder nu Debian 9 som en Linux-distribution.

**Fel korrigeringar**

- Ett problem har åtgärd ATS där högtalar resursen var destructed för tidigt i text-till-tal.

## <a name="speech-sdk-142"></a>1.4.2 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

## <a name="speech-sdk-141"></a>Tal-SDK 1.4.1

Detta är endast en JavaScript-version. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

- Förhindra att webb paketet läser in https-proxy-agenten.

## <a name="speech-sdk-140-2019-april-release"></a>Tal-SDK 1.4.0:2019 – april version

**Nya funktioner**

- SDK stöder nu text till tal-tjänsten som beta version. Den stöds i Windows och Linux Desktop från C++ och C#. Mer information hittar du i [text till tal-översikten](text-to-speech.md#get-started).
- SDK stöder nu MP3-och Opus/OGG-ljudfiler som strömmande indatafiler. Den här funktionen är endast tillgänglig i Linux från C++ och C# och finns för närvarande i beta version (mer information [här](how-to-use-codec-compressed-audio-input-streams.md)).
- Tal-SDK för Java, .NET Core, C++ och mål-C har fått stöd för macOS. Mål-C-supporten för macOS är för närvarande beta version.
- iOS: talet SDK för iOS (mål-C) har nu också publicerats som en CocoaPod.
- Java Script: stöd för mikrofon som inte är standard som en inmatad enhet.
- Java Script: proxy-stöd för Node.js.

**Exempel**

- Exempel på hur du använder tal-SDK med C++ och med mål-C på macOS har lagts till.
- Exempel som demonstrerar användningen av text till tal-tjänsten har lagts till.

**Förbättringar/ändringar**

- Python: ytterligare egenskaper för igenkännings resultat exponeras nu via `properties` egenskapen.
- Om du vill ha ytterligare stöd för utveckling och fel sökning kan du omdirigera information om SDK-loggning och-diagnostik i en loggfil (mer information [här](how-to-use-logging.md)).
- Java Script: förbättra prestanda för ljud bearbetning.

**Fel korrigeringar**

- Mac/iOS: ett fel som ledde till en lång väntan på att det inte gick att upprätta en anslutning till röst tjänsten har åtgärd ATS.
- Python: förbättra fel hanteringen för argument i python-motanrop.
- Java Script: fast felaktig tillstånds rapportering för tal slutar på RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>1.3.1 för tal-SDK: 2019 – februari uppdatering

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Fel korrigering**

- En minnes läcka har åtgärd ATS när mikrofonen används. Stream-eller File-indata påverkas inte.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK-1.3.0:2019 – februari utgåva

**Nya funktioner**

- Tal-SDK: n stöder val av mikrofon för indataportar genom- `AudioConfig` klassen. På så sätt kan du strömma ljud data till tal tjänsten från en mikrofon som inte är standard. Mer information finns i dokumentationen som beskriver valet av [enhet för ljud inspelning](how-to-select-audio-input-devices.md). Den här funktionen är inte tillgänglig ännu från Java Script.
- Talet SDK stöder nu Unity i en beta version. Ge feedback via avsnittet ärende i GitHub- [exempel lagrings plats](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (Skriv bords-eller Universell Windows-plattform program) och Android (ARM32/64, x86). Mer information finns i vår [snabb start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity).
- Filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererades i tidigare versioner) behövs inte längre. Funktionerna är nu integrerade i kärn-SDK: n.

**Exempel**

Följande nya innehåll är tillgängligt i vår [exempel lagrings plats](https://aka.ms/csspeech/samples):

- Ytterligare exempel för `AudioConfig.FromMicrophoneInput` .
- Ytterligare python-exempel för avsikts igenkänning och översättning.
- Ytterligare exempel för att använda `Connection` objektet i iOS.
- Ytterligare Java-exempel för översättning med ljud uppspelning.
- Nytt exempel för användning av [batch-Avskrifter REST API](batch-transcription.md).

**Förbättringar/ändringar**

- Python
  - Förbättrad parameter verifiering och fel meddelanden i `SpeechConfig` .
  - Lägg till stöd för `Connection` objektet.
  - Stöd för 32-bitars python (x86) i Windows.
  - Tal-SDK för python är av beta version.
- iOS
  - SDK är nu byggt mot iOS SDK version 12,1.
  - SDK stöder nu iOS version 9,2 och senare.
  - Förbättra referens dokumentationen och korrigera flera egenskaps namn.
- JavaScript
  - Lägg till stöd för `Connection` objektet.
  - Lägga till typ definitions filer för paketerade JavaScript-skript
  - Inledande support och implementering för fras tips.
  - Hämta egenskaps samling med tjänst-JSON för igenkänning
- Windows-DLL-filer innehåller nu en versions resurs.
- Om du skapar en igenkännings verktyg `FromEndpoint` kan du lägga till parametrar direkt i slut punkts-URL: en. `FromEndpoint`Du kan inte konfigurera tolken genom att använda standard konfigurations egenskaperna.

**Fel korrigeringar**

- Tomt användar namn för proxy och proxy-lösenord hanterades inte korrekt. Om du ställer in proxy-användarnamn och proxy-lösenord till en tom sträng i den här versionen skickas de inte vid anslutning till proxyservern.
- SessionId som skapats av SDK var inte alltid helt slumpmässigt för vissa språk &nbsp; /miljöer. Har lagt till en slumpmässig Generator initiering för att åtgärda problemet.
- Förbättra hanteringen av autentiseringstoken. Om du vill använda en autentiseringstoken anger du i `SpeechConfig` och låter prenumerations nyckeln vara tom. Skapa sedan tolken som vanligt.
- I vissa fall `Connection` släpptes inte objektet korrekt. Det här problemet har åtgärd ATS.
- JavaScript-exemplet har åtgärd ATS för att stödja ljud utdata för översättnings syntes också i Safari.

## <a name="speech-sdk-121"></a>Tal-SDK 1.2.1

Detta är endast en JavaScript-version. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

- Brand i slutet av strömmen vid turn. End, inte i tal. End.
- Åtgärda fel i ljud pumpen som inte schemalades nästa sändning om den aktuella sändningen misslyckades.
- Åtgärda kontinuerlig igenkänning med auth-token.
- Fel korrigering för olika tolkningar/slut punkter.
- Förbättringar av dokumentation.

## <a name="speech-sdk-120-2018-december-release"></a>1.2.0 för tal-SDK: 2018 – december version

**Nya funktioner**

- Python
  - Beta versionen av python-supporten (3,5 och senare) är tillgänglig i den här versionen. Mer information finns här] (snabb start-python.md).
- JavaScript
  - Tal-SDK för Java Script har öppnats med öppen källkod. Käll koden finns på [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Vi har nu stöd för Node.js. mer information finns [här](quickstart-js-node.md).
  - Längd begränsningen för ljudsessioner har tagits bort, åter anslutning sker automatiskt under locket.
- `Connection` jobbobjektet
  - Från kan `Recognizer` du komma åt ett `Connection` objekt. Med det här objektet kan du uttryckligen initiera tjänst anslutningen och prenumerera på att ansluta och koppla från händelser.
    (Den här funktionen är inte tillgänglig ännu från Java Script och python.)
- Stöd för Ubuntu 18,04.
- Android
  - Stöd för aktive rad proguard vid APK-generering.

**Bättre**

- Förbättringar i den interna tråd användningen, vilket minskar antalet trådar, lås, mutexer.
- Förbättrad fel rapportering/information. I flera fall har fel meddelanden inte spridits ut hela vägen.
- Uppdaterade utvecklings beroenden i Java Script för att använda aktuella moduler.

**Fel korrigeringar**

- Fasta minnes läckor på grund av ett typ matchnings fel i `RecognizeAsync` .
- I vissa fall läcker undantag.
- Korrigerar minnes läckor i översättnings händelse argument.
- Åtgärdat låsnings problem vid åter anslutning vid tids krävande sessioner.
- Ett problem som kan leda till saknat slut resultat för misslyckade översättningar har åtgärd ATS.
- C#: om en `async` åtgärd inte förväntades i huvud tråden var det möjligt att identifieraren kunde tas bort innan den asynkrona uppgiften slutfördes.
- Java: åtgärdat ett problem som resulterade i en krasch i Java VM.
- Mål-C: fast Enum-mappning; RecognizedIntent returnerades i stället för `RecognizingIntent` .
- Java Script: Ange Standardutdataformatet till "enkel" i `SpeechConfig` .
- Java Script: ta bort inkonsekvens mellan egenskaper i konfigurationsobjektet i Java Script och andra språk.

**Exempel**

- Uppdaterade och korrigerade flera exempel (till exempel utmatnings röster för översättning osv.).
- Lade till Node.js exempel i [exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>1.1.0 för tal-SDK

**Nya funktioner**

- Stöd för Android x86/x64.
- Stöd för proxy: i `SpeechConfig` objektet kan du nu anropa en funktion för att ange proxyinformation (värdnamn, port, användar namn och lösen ord). Den här funktionen är inte tillgänglig ännu på iOS.
- Förbättrad felkod och meddelanden. Om ett igenkännings fel returnerade ett fel angavs detta redan `Reason` (i avbruten händelse) eller `CancellationDetails` (i resultat) till `Error` . Den avbrutna händelsen innehåller nu två ytterligare medlemmar `ErrorCode` och `ErrorDetails` . Om servern returnerade ytterligare fel information med det rapporterade felet är den nu tillgänglig i de nya medlemmarna.

**Bättre**

- Ytterligare kontroll har lagts till i igenkännings konfigurationen och ytterligare fel meddelande har lagts till.
- Förbättrad hantering av lång tids tystnad i mitten av en ljudfil.
- NuGet-paket: för .NET Framework-projekt förhindrar det att du skapar med AnyCPU-konfiguration.

**Fel korrigeringar**

- Det finns flera undantag i identifierarna. Dessutom fångas och konverteras undantag till `Canceled` event.
- Åtgärda en minnes läcka i egenskaps hantering.
- Åtgärdat fel där en ljudfil i indatafilen skulle krascha tolken.
- Ett fel har åtgärd ATS där händelser kan tas emot efter en händelse för att stoppa sessionen.
- Vissa tävlings förhållanden har åtgärd ATS i trådar.
- Åtgärdat kompatibilitetsproblem med iOS som kan resultera i en krasch.
- Stabilitets förbättringar för stöd för Android-mikrofon.
- Ett fel har åtgärd ATS där en tolk i Java Script skulle ignorera tolknings språket.
- Åtgärdat ett fel som förhindrar inställning `EndpointId` (i vissa fall) i Java Script.
- Parameter ordningen ändrades i AddIntent i Java Script och en JavaScript-signatur som saknas har lagts till `AddIntent` .

**Exempel**

- C++ och C#-exempel har lagts till för hämtning och push-uppspelning i [exempel lagrings platsen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>1.0.1 för tal-SDK

Förbättringar av tillförlitlighet och fel korrigeringar:

- Det gick inte att åtgärda allvarligt fel på grund av ett tävlings tillstånd i att identifiera igenkänning
- Åtgärdat potentiellt allvarligt fel i händelse av unset egenskaper.
- Ytterligare fel och parameter kontroll har lagts till.
- Mål-C: åtgärdat allvarligt fel som orsakats av att namnet åsidosätts i NSString.
- Mål-C: anpassad synlighet för API
- Java Script: åtgärdat avseende händelser och deras nytto laster.
- Förbättringar av dokumentation.

I vår [exempel lagring](https://aka.ms/csspeech/samples)lades ett nytt exempel för Java Script till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – september version

**Nya funktioner**

- Stöd för mål-C på iOS. Kolla vår [mål-C-snabb start för iOS](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md).
- Stöd för Java Script i webbläsare. Kolla i vår [snabb start för Java Script](quickstart-js-browser.md).

**Bryta ändringar**

- I den här versionen introduceras ett antal avbrytande ändringar.
  Mer information finns på [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – augusti utgåva

**Nya funktioner**

- UWP-appar som skapats med Speech SDK kan nu skicka Windows app Certificate Kit (WACK).
  Ta en titt på snabb starten för [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp).
- Stöd för .NET standard 2,0 på Linux (Ubuntu 16,04 x64).
- Experimentell: stöder Java 8 på Windows (64-bitars) och Linux (Ubuntu 16,04 x64).
  Kolla in [Java Runtime Environment snabb start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre).

**Funktionell ändring**

- Visa ytterligare information om fel information om anslutnings fel.

**Bryta ändringar**

- I Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` kräver funktionen inte längre en Sök vägs parameter. Nu identifieras sökvägen automatiskt på alla plattformar som stöds.
- Get-accessorn för egenskapen `EndpointUrl` i Java och C# har tagits bort.

**Fel korrigeringar**

- I Java implementeras ljud sammanfattnings resultatet i översättnings tolken nu.
- Ett fel som kan orsaka inaktiva trådar och ett ökat antal öppna och oanvända Sockets har åtgärd ATS.
- Ett problem har åtgärd ATS, där en tids krävande igenkänning kan avslutas mitt i överföringen.
- Ett konkurrens villkor har åtgärd ATS i avstängningen av tolken.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – juli utgåva

**Nya funktioner**

- Stöd för Android-plattform (API 23: Android 6,0 Marshmallow eller högre). Kolla in [Android-snabb](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)starten.
- Stöd för .NET standard 2,0 i Windows. Kolla in [.net Core-snabb](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)starten.
- Experimentell: stöd för UWP i Windows (version 1709 eller senare).
  - Ta en titt på snabb starten för [UWP](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp).
  - Obs! UWP-appar som skapats med tal-SDK: n har inte överfört Windows app Certificate Kit (WACK).
- Stöd för tids krävande igenkänning med automatisk åter anslutning.

**Funktionella ändringar**

- `StartContinuousRecognitionAsync()` stöder tids krävande igenkänning.
- Igenkännings resultatet innehåller fler fält. De är förskjutna från början och varaktigheten för ljudet (både i Ticket) för den tolkade texten och ytterligare värden som representerar igenkännings status, till exempel `InitialSilenceTimeout` och `InitialBabbleTimeout` .
- Stöd för AuthorizationToken för att skapa fabriks instanser.

**Bryta ändringar**

- Igenkännings händelser: `NoMatch` händelse typen slogs samman i `Error` händelsen.
- SpeechOutputFormat i C# har bytt namn till `OutputFormat` för att hålla dig uppdaterad med C++.
- Retur typen för vissa metoder i gränssnittet har `AudioInputStream` ändrats något:
  - I Java `read` returnerar metoden nu i `long` stället för `int` .
  - I C# `Read` returnerar metoden nu i `uint` stället för `int` .
  - I C++ `Read` returneras och- `GetFormat` metoderna nu `size_t` i stället för `int` .
- C++: instanser av ljud inspelnings strömmar kan nu bara skickas som en `shared_ptr` .

**Fel korrigeringar**

- Korrigerade Felaktiga retur värden i resultatet vid `RecognizeAsync()` tids gränsen.
- Beroendet på Media Foundation-bibliotek i Windows har tagits bort. SDK använder nu centrala ljud-API: er.
- Dokumentations korrigering: en [region](regions.md) sida har lagts till för att beskriva de regioner som stöds.

**Känt problem**

- Tal-SDK för Android rapporterar inte tal syntes resultat för översättning. Det här problemet kommer att åtgärdas i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0:2018 – juni utgåva

**Funktionella ändringar**

- AudioInputStream

  En tolk kan nu använda en ström som ljud källa. Mer information finns i den närliggande [instruktions guiden](how-to-use-audio-input-streams.md).

- Detaljerat utdataformat

  När du skapar en `SpeechRecognizer` kan du begära `Detailed` eller `Simple` utdataformat. `DetailedSpeechRecognitionResult`Innehåller en säkerhets poäng, en tolkad text, en rå lexikal form, normaliserad form och normaliserad form med maskerade svordomar.

**Bryta ändring**

- Ändrat till `SpeechRecognitionResult.Text` från `SpeechRecognitionResult.RecognizedText` i C#.

**Fel korrigeringar**

- Ett möjligt problem med återanrop har åtgärd ATS i USP-skiktet under avstängning.
- Om en tolk förbrukar en ljud indatafil, behålls den på fil hanteringen längre än nödvändigt.
- Flera död lägen mellan meddelande pumpen och tolken har tagits bort.
- Utlös ett `NoMatch` resultat när svar från tjänsten har nått sin tids gräns.
- Media Foundation-biblioteken i Windows är fördröjda. Det här biblioteket krävs endast för mikrofon ineffekter.
- Uppladdnings hastigheten för ljuddata är begränsad till cirka två gånger den ursprungliga ljud hastigheten.
- I Windows är C# .NET-sammansättningar nu starkt namngivna.
- Korrigering av dokumentation: `Region` är nödvändig information för att skapa en tolk.

Fler exempel har lagts till och uppdateras ständigt. Den senaste uppsättningen exempel finns i [GitHub-lagringsplatsen Speech SDK samples](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733:2018 – maj release

Den här versionen är den första offentliga för hands versionen av Cognitive Services Speech SDK.
