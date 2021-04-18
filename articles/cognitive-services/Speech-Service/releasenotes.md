---
title: Release Notes – Speech Service
titleSuffix: Azure Cognitive Services
description: En logg över funktionsutgåningar, förbättringar, felkorrigeringar och kända problem som körs i Speech Service.
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: e02fbe0f77a53b7d3f8f3ce420bf46189dc85a65
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600989"
---
# <a name="speech-service-release-notes"></a>Versionsanteckningar för Speech Service

## <a name="text-to-speech-2021-april-release"></a>Text till tal-version 2021–april

**Neural TTS är tillgängligt i 21 regioner**

- **Tolv nya regioner har lagts** till – Neural TTS är nu tillgängligt i dessa nya 12 regioner: , , , , , , `Japan East` , , , `Japan West` , , , `Korea Central` , `North Central US` , `North Europe` `South Central US` `Southeast Asia` `UK South` `west Central US` `West Europe` `West US` `West US 2` . Här [finns](regions.md#text-to-speech) en fullständig lista över 21 regioner som stöds.

## <a name="text-to-speech-2021-march-release"></a>Text till tal-version 2021–mars

**Nya språk och röster har lagts till för neural TTS**

- Sex **nya** språk har introducerats – 12 nya röster på 6 nya språk läggs till i listan över neurala TTS-språk: Nia i `cy-GB` Kingdom (United Kingdom), Aled in `cy-GB` Kingdom, Rosa på engelska `en-PH` (Eng. "Eng." (På engelska), James på engelska `en-PH` (IoT), Charline på `fr-BE` franska (Baskiska), Gerard på franska `fr-BE` (Nederländerna), Dena i `nl-BE` nederländska (Nederländerna), Arnaud i nederländska `nl-BE` (Nederländerna), Polina i `uk-UA` nederländska (Baskiska), Ostap i `uk-UA` nederländska (Nederländska), Uzma i `ur-PK` Urdu (Istan), Asad i `ur-PK` Urdu (Korsning).

- Fem språk från förhandsversionen till **GA** – 10 röster på 5 språk som introducerades 2020–november är nu GA: Kert i etaska `et-EE` (10 röster), Colm i `ga-IE` Irland), Lettiska `lv-LV` (Lettiska) och IaaS (1966), Joseph `lt-LT` in `mt-MT` 10 (1996).

- **Ny mansröst har lagts till för franska (Kanada)** – En ny röst För franska `fr-CA` (Kanada).

- **Kvalitetsförbättring** – Minskning av uttalsfelfrekvensen på `hu-HU` ungerska – 48,17 %, `nb-NO` norska – 52,76 %, `nl-NL` nederländska (Nederländerna) – 22,11 %.

I den här versionen stöder vi nu totalt 142 neurala röster över 60 språk/språk. Dessutom finns över 70 standardröster tillgängliga på 49 språk/språk. Gå [till Språkstöd](language-support.md#text-to-speech) för en fullständig lista.

**Hämta ansiktsställningshändelser för att animera tecken**

[Viseme-händelsen läggs](how-to-speech-synthesis-viseme.md) till i Neural TTS, vilket gör att användarna kan hämta ansiktsställningssekvensen och varaktigheten från syntetiserat tal. Viseme kan användas för att styra förflyttningen av 2D- och 3D-avatarmodeller, vilket passar perfekt för mysförflyttningar till syntetiserat tal. Nu fungerar viseme bara för en-US-AriaNe voice.

**Lägg till bokmärkeselementet i Speech Synthesis Markup Language (SSML)**

Med [bokmärkeselementet](speech-synthesis-markup.md#bookmark-element) kan du infoga anpassade markörer i SSML för att hämta förskjutningen för varje markör i ljudströmmen. Den kan användas för att referera till en specifik plats i text- eller taggsekvensen.

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK 1.16.0: version 2021–mars

> [!NOTE]
> Speech SDK för Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned den [här.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

#### <a name="new-features"></a>Nya funktioner

- **C++/C#/Java/Python:** Har flyttats till den senaste versionen av GStreamer (1.18.3) för att lägga till stöd för att transkribera medieformat i Windows, Linux och Android. Se dokumentationen [här.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)
- **C++/C#/Java/Objective-C/Python:** Stöd har lagts till för avkodning av komprimerat TTS/syntetiserat ljud till SDK. Om du ställer in utdataljudformatet på PCM och GStreamer är tillgängligt i systemet begär SDK automatiskt komprimerat ljud från tjänsten för att spara bandbredd och avkoda ljudet på klienten. Du kan ange `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` till för att inaktivera den här `false` funktionen. Information för [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.propertyid?view=azure-dotnet), [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.propertyid?view=azure-java-stable) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxpropertyid) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?view=azure-python).
- **JavaScript:** Node.js användare kan nu använda [ `AudioConfig.fromWavFileInput` API:et](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromWavFileInput_File_). Detta åtgärdar [GitHub-#252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252).
- **C++/C#/Java/Objective-C/Python:** Metoden har lagts till för `GetVoicesAsync()` att TTS ska returnera alla tillgängliga syntesröster. Information för [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet#methods), [Java,](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-java-stable#methods) [Objective-C](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoiceasync)och [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?view=azure-python#methods).
- **C++/C#/Java/JavaScript/Objective-C/Python:** En händelse har lagts till för TTS/talsyntes för att returnera `VisemeReceived` synkron viseme-animering. Se dokumentationen [här.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme)
- **C++/C#/Java/JavaScript/Objective-C/Python:** Händelse har `BookmarkReached` lagts till för TTS. Du kan ange bokmärken i indata-SSML och hämta ljudförskjutningar för varje bokmärke. Se dokumentationen [här.](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element)
- **Java:** Stöd har lagts till för API:er för talarigenkänning. Information [här.](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable)
- **C++/C#/Java/JavaScript/Objective-C/Python:** Två nya ljudformat för utdata har lagts till med WebM-containern för TTS (Webm16Khz16BitMonoOpus och Webm24Khz16BitMonoOpus). Det här är bättre format för direktuppspelning av ljud med Codec-koden. Information för [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-java-stable), [JavaScript,](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?view=azure-node-latest) [Objective-C,](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat) [Python](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-python).
- **C++/C#/Java: Stöd** har lagts till för att hämta röstprofil för talarigenkänningsscenario. Information för [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/speakerrecognizer), [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-dotnet)och [Java](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer?view=azure-java-stable).
- **C++/C#/Java/Objective-C/Python:** Stöd har lagts till för separat delat bibliotek för ljudmi mikrofon och talarkontroll. På så sätt kan du använda SDK i miljöer som inte har nödvändiga beroenden för ljudbibliotek.
- **Objective-C/Swift:** Stöd för modulramverk med umbrella-rubrik har lagts till. På så sätt kan du importera Speech SDK som en modul i iOS/Mac Objective-C/Swift-appar. Det här [åtgärdar GitHub-#452](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452).
- **Python:** Stöd för [Python 3.9](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) har lagts till och stöd för Python 3.5 har tagits bort per Pythons livslängd för [3,5](https://devguide.python.org/devcycle/#end-of-life-branches).

#### <a name="improvements"></a>Förbättringar

- Som en del av vårt arbete med flera versioner för att minska Speech SDK:ns minnesanvändning och diskfotavtryck är Android-binärfiler nu 3 till 5 % mindre.
- Förbättrad noggrannhet, läsbarhet och se-även-avsnitt i vår C#-referensdokumentation [här](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech?view=azure-dotnet).

#### <a name="bug-fixes"></a>Felkorrigeringar

- **JavaScript:** Stora WAV-filrubriker parsas nu korrekt (ökar rubriksegmentet till 512 byte). Detta [åtgärdar GitHub-#962](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962).
- **JavaScript:** Korrigerade problem med mikrofontidsinställningen om mic-strömmen slutar före stoppigenkänning, vilket åtgärdar ett problem med att taligenkänning inte fungerar i Firefox.
- **JavaScript:** Vi hanterar nu initierings promise korrekt när webbläsaren tvingar mic att stänga av innan turnOn slutförs.
- **JavaScript:** Vi ersatte URL-beroendet med url-parse. Detta [åtgärdar GitHub-#264](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264).
- **Android:** Fasta återanrop fungerar inte när `minifyEnabled` har angetts till true.
- **C++/C#/Java/Objective-C/Python:** är korrekt inställt på underliggande `TCP_NODELAY` socket-IO för TTS för att minska svarstiden.
- **C++/C#/Java/Python/Objective-C/Go:** En tillfällig krasch har åtgärdats när igenkännaren förstörs precis efter att en igenkänning startades.
- **C++/C#/Java: En tillfällig** krasch i destruktionen av talar igen har åtgärdats.

#### <a name="samples"></a>Exempel

- **JavaScript:** [Webbläsarexempel kräver](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) inte längre separat nedladdning av JavaScript-biblioteksfilen.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Speech CLI (även kallat SPX): version 2021–mars

> [!NOTE]
> Kom igång med Azure Speech-tjänstens kommandoradsgränssnitt (CLI) [här.](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-basics) Med CLI kan du använda Azure Speech-tjänsten utan att behöva skriva någon kod.

#### <a name="new-features"></a>Nya funktioner

- Kommandot `spx intent` har lagts till för avsiktsigenkänning och ersätter `spx recognize intent` .
- Identifiera och avsikt kan nu använda Azure Functions för att beräkna ordfelfrekvensen med hjälp av `spx recognize --wer url <URL>` .
- Recognize kan nu mata ut resultat som ENT-filer med hjälp av `spx recognize --output vtt file <FILENAME>` .
- Känslig nyckelinformation döljs nu i felsöknings-/utförliga utdata.
- URL-kontroll och felmeddelande för innehållsfält har lagts till i batch-transkriptionen create.

**COVID-19 förkortad testning:**

Allt eftersom den pågående pandemin fortsätter att kräva att våra tekniker arbetar hemifrån har manuella verifieringsskript före pandemin minskat avsevärt. Vi testar på färre enheter med färre konfigurationer, och sannolikheten för att miljöspecifika buggar som skörs igenom kan ökas. Vi validerar fortfarande rigoröst med en stor uppsättning automatisering. I händelse av osannolika att vi missade något kan du meddela oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Håll dig felfri!

## <a name="text-to-speech-2021-february-release"></a>Text till tal-version 2021–februari

**Anpassad neural röst GA**

Anpassad neural röst är GA i februari på 13 språk: kinesiska (mandarin, förenklad), engelska (Australien), engelska (Indien), engelska (Storbritannien), engelska (USA), franska (Kanada), franska (Frankrike), tyska (Tyskland), italienska (Italien), japanska (Japan), koreanska (Korea), portugisiska (Brasilien), spanska (Mexiko) och spanska (Spanien). Läs mer om [vad som Anpassad neural röst](custom-neural-voice.md) och hur du använder det på ett ansvarsfullt [sätt.](concepts-guidelines-responsible-deployment-synthetic.md) Anpassad neural röst kräver registrering och Microsoft kan begränsa åtkomsten baserat på Microsofts behörighetskriterier. Läs mer om [den begränsade åtkomsten](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context).  

## <a name="speech-sdk-1150-2021-january-release"></a>Speech SDK 1.15.0: version 2021–januari

> [!NOTE]
> Speech SDK för Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned [den här](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Sammanfattning av markeringar**
- Mindre minne och diskfotavtryck gör SDK effektivare.
- Utdataformat med högre återgivning är tillgängliga för anpassad privat förhandsversion av neural röst.
- Avsiktsanalys kan nu returnera mer än den främsta avsikten, vilket ger dig möjlighet att göra en separat utvärdering om kundens avsikt.
- Röstassistenten eller roboten är nu enklare att konfigurera och du kan få den att sluta lyssna direkt och få större kontroll över hur den svarar på fel.
- Förbättrad enhetsprestanda genom att göra komprimering valfri.
- Använd Speech SDK i Windows ARM/ARM64.
- Förbättrad felsökning på låg nivå.
- Utvärderingsfunktionen för uttal är nu mer allmänt tillgänglig.
- Flera felkorrigeringar för att åtgärda problem DU, våra värdefulla kunder, har flaggat på GitHub! Tack! Fortsätt att lämna feedback!

**Förbättringar**
- Speech SDK är nu mer effektivt och enkelt. Vi har startat ett arbete med flera versioner för att minska Minnesanvändning och diskfotavtryck för Speech SDK. Som ett första steg gjorde vi betydande minskningar av filstorleken i delade bibliotek på de flesta plattformar. Jämfört med version 1.14:
  - 64-bitars UWP-kompatibla Windows-bibliotek är ungefär 30 % mindre.
  - 32-bitars Windows-bibliotek har ännu inte någon storleksförbättring.
  - Linux-bibliotek är 20–25 % mindre.
  - Android-bibliotek är 3–5 % mindre.

**Nya funktioner**
- **Alla:** Nya 48KHz-utdataformat är tillgängliga för den privata förhandsversionen av anpassad neural röst via API:et för TTS-talsyntes: Audio48Khz192KBitRateMonoMp3, audio-48khz-192kbitrate-mono-mp3, Audio48Khz96KBitRateMonoMp3, audio-48khz-96kbitrate-mono-mp3, Raw48Khz16BitMonoPcm, raw-48khz-16bit-mono-pcm, Riff48Khz16BitMonoPcm, riff-48khz-16bit-mono-pcm.
- **Alla:** Anpassad röst är också enklare att använda. Stöd har lagts till för att ställa in anpassad röst via `EndpointId` ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [Objective-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [Python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Innan den här ändringen behövde anpassade röstanvändare ange slutpunktens URL via `FromEndpoint` metoden . Nu kan kunder använda metoden `FromSubscription` precis som offentliga röster och sedan ange distributions-ID:t genom att ange `EndpointId` . Det gör det enklare att konfigurera anpassade röster. 
- **C++/C#/Java/Objective-C/Python:** Hämta mer än avsikten top från `IntentRecognizer` . Nu har det stöd för konfigurering av JSON-resultatet som innehåller alla avsikter och inte bara avsikten med högst poäng via metoden med `LanguageUnderstandingModel FromEndpoint` hjälp av `verbose=true` URI-parametern. Det här [åtgärdar GitHub-#880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Se uppdaterad dokumentation [här.](./get-started-intent-recognition.md#add-a-languageunderstandingmodel-and-intents)
- **C++/C#/Java:** Gör så att röstassistenten eller roboten slutar lyssna omedelbart. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) har nu en `StopListeningAsync()` -metod som medföljer `ListenOnceAsync()` . Detta stoppar omedelbart ljudinspelningen och väntar smidigt på ett resultat, vilket gör det perfekt för användning med "stoppa nu"-knapptryckningsscenarier.
- **C++/C#/Java/JavaScript:** Gör så att röstassistenten eller roboten reagerar bättre på underliggande systemfel. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [JavaScript](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) har nu en ny `TurnStatusReceived` händelsehanterare. Dessa valfria händelser motsvarar varje lösning på roboten och rapporterar körningsfel när de inträffar, t.ex. på grund av ohanterade undantag, tidsgränser eller nätverksavbrott mellan Direct Line Speech och [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) roboten. `TurnStatusReceived` gör det enklare att svara på feltillstånd. Om en robot till exempel tar för lång tid på en serverdatabasfråga (t.ex. att leta upp en produkt) kan klienten veta att den känner igen med `TurnStatusReceived` "sorry, I didn't quite get that, could you please try again" eller något liknande.
- **C++/C#**: Använd Speech SDK på fler plattformar. [Speech SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) stöder nu Windows ARM/ARM64 desktop native binaries (UWP stöds redan) för att göra Speech SDK mer användbart på fler datortyper.
- **Java:** [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) har nu en `setSpeechActivityTemplate()` -metod som oavsiktligt undantagits från språket tidigare. Detta motsvarar att ange egenskapen och begär att alla framtida Bot Framework aktiviteter som kommer från Direct Line Speech-tjänsten sammanfogar det angivna innehållet i `Conversation_Speech_Activity_Template` deras JSON-nyttolaster.
- **Java:** Förbättrad felsökning på låg nivå. Klassen [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection) har nu en händelse som liknar andra `MessageReceived` programmeringsspråk (C++, C#). Den här händelsen ger lågnivååtkomst till inkommande data från tjänsten och kan vara användbar för diagnostik och felsökning.
- **JavaScript:** Enklare konfiguration för röstassistenter och robotar via , som nu har - och -fabriksmetoder som förenklar användningen av anpassade tjänstplatser jämfört med [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) att ange egenskaper `fromHost()` `fromEndpoint()` manuellt. Vi har även standardiserat valfri specifikation av `botId` för att använda en icke-standardrobot i konfigurationsfabrikerna.
- **JavaScript:** Förbättrad prestanda för enheten genom en tillagd strängkontrollegenskap för websocket-komprimering. Av prestandaskäl har vi inaktiverat websocket-komprimering som standard. Detta kan återenablerats för scenarier med låg bandbredd. Mer information [finns här.](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid) Detta [åtgärdar GitHub-#242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **JavaScript: Stöd** har lagts till för utvärdering av uttal för att möjliggöra utvärdering av taluttal. Se snabbstarten [här.](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript)

**Felkorrigeringar**
- **Alla** (utom JavaScript): En regression har åtgärdats i version 1.14, där för mycket minne allokerades av identifieraren.
- **C++**: Ett skräpinsamlingsproblem har åtgärdats `DialogServiceConnector` med , vilket åtgärdar [GitHub-#794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#**: Ett problem med trådavstängning som gjorde att objekt blockerades i ungefär en sekund när de tagits bort har åtgärdats.
- **C++/C#/Java: Ett** undantag som hindrade ett program från att ange en token för talauktorisering eller aktivitetsmall mer än en gång på en `DialogServiceConnector` har åtgärdats.
- **C++/C#/Java: En** identifierarkrasch har åtgärdats på grund av ett rastillstånd i en borttagning.
- **JavaScript:** [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) respekterade inte tidigare den `botId` valfria parameter som `BotFrameworkConfig` anges i fabrikerna. Detta gjorde det nödvändigt att ange `botId` frågesträngsparametern manuellt för att använda en robot som inte är standard. Buggen har korrigerats och värden som angetts till 's fabriker kommer att `botId` `BotFrameworkConfig` respekteras och användas, inklusive nya `fromHost()` `fromEndpoint()` och tillägg. Detta gäller även för `applicationId` parametern för `CustomCommandsConfig` .
- **JavaScript:** [GitHub-problem har åtgärdats #881](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), vilket tillåter återanvändning av identifierarobjekt.
- **JavaScript:** Ett problem har åtgärdats som gjorde att SKU:t skickades flera gånger i en `speech.config` TTS-session, vilket slösade bandbredden.
- **JavaScript:** Förenklad felhantering vid mikrofonauktorisering, vilket gör att mer beskrivande meddelande bubblar upp när användaren inte har tillåtit mikrofonindata i webbläsaren.
- **JavaScript:** [GitHub-problem har åtgärdats #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) där typfel i `ConversationTranslator` och orsakade ett `ConversationTranscriber` kompileringsfel för TypeScript-användare.
- **Objective-C:** Ett problem har åtgärdats där GStreamer-versionen misslyckades för iOS på Xcode 11.4, vilket löste [GitHub-#911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python:** [GitHub-problem har åtgärdats #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870), vilket tar bort "DeprecationWarning: imp-modulen är inaktuell till förmån för importlib".

**Exempel**
- [Från-fil-exemplet för JavaScript-webbläsaren](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) använder nu filer för taligenkänning. Detta åtgärdar [GitHub-problem #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Speech CLI (även kallat SPX): version 2021–januari

**Nya funktioner**
- Speech CLI är nu tillgängligt som [ett NuGet-paket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) och kan installeras via .NET CLI som ett globalt .NET-verktyg som du kan anropa från gränssnittet/kommandoraden.
- [Lagringsplatsen för Custom Speech DevOps-mallen](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) har uppdaterats för att använda Speech CLI för sina Custom Speech-arbetsflöden.

**Covid-19** förkortad testning: Allt eftersom den pågående pandemin fortsätter att kräva att våra tekniker arbetar hemifrån har manuella verifieringsskript före pandemin minskat avsevärt. Vi testar på färre enheter med färre konfigurationer, och sannolikheten för att miljöspecifika buggar som skörs igenom kan ökas. Vi validerar fortfarande rigoröst med en stor uppsättning automatisering. I händelse av osannolika att vi missade något kan du meddela oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Håll dig felfri!

## <a name="text-to-speech-2020-december-release"></a>Text till tal-version 2020–december

**Nya neurala röster i GA och förhandsversion**

Släppte 51 nya röster för totalt 129 neurala röster över 54 språk/språk:

- **46** nya röster i GA-språk: Shakir på arabiska (), hamstrad på arabiska (Saudiarabien), Boriistan i arabiska (1966) och 155 röster (2006) samt Jippe (2006) och 100 000 000 000 personer (2006). `ar-EG` `ar-SA` in German `bg-BG` `ca-ES` `cs-CZ` `da-DK` `de-AT` (Canada), Jan in `de-CH` German (Schweiz), Nestoras `el-GR` in Grekiska (Påve), `en-CA` Hindan (Indien), Mohan i `en-IE` `en-IN` `en-IN` Telugu (Indien), Prabhat på engelska `en-IN` (Indien)), Valluvar i `en-IN` Tamil (Indien), Enric `es-ES` inArbe (Spanien), Kert i etask `et-EE` (mv), Harri på finska `fi-FI` (Färdigt), Selma på finska `fi-FI` (Petma), Fabrice i franska `fr-CH` (Schweiz), Colm i `ga-IE` Schweiz (Irland), Avri i Hebreiska (Israel), Srecko i ar bemanning `he-IL` `hr-HR` (bemanning), tamas i ungerska `hu-HU` (eten), Gadis i indonesien `id-ID` (Indonesien),Skapas i arabel (1966), 1855(1666), `lt-LT` Osman `lv-LV` `ms-MY` `mt-MT` i malajiska (Malaysia), Joseph i 1955 , Finn på `nb-NO` norska, Bokmål (Norska), Pernille på `nb-NO` norska, Bokmål (Brasilien), Fenna i nederländska (Nederländerna), Maarten i nederländska `nl-NL` `nl-NL` (Nederländerna), Agnieszka i polska `pl-PL` (nederländska), Marek i polska `pl-PL` (nederländska), duarte i portugisiska `pt-BR` (Brasilien), Raquel på portugisiska `pt-PT` (potugal), Rumänska `ro-RO` (rumänska), Dmitry i ryska `ru-RU` (Ryssland), Svetlana i ryska (Ryssland),Istan i Slovakiska `ru-RU` `sk-SK` (Nederländska), Rok i `sl-SI` nederländskan (Förskansna), `sv-SE` NamMinh in Chinese `sv-SE` Cantonese (Hongkong), Niwat på thailändska (Taiwan), Ahmet på turkiska `th-TH` (NamMinh i Vietnam), `tr-TR` `vi-VN` HxooChen i Taiwan `zh-TW` (Taiwan), YunJhe på `zh-TW` taiwanesiska mandarin (Taiwan), HiuMaan på kinesiska `zh-HK` cantonesiska (Hongkong), WanIntern på kinesiska `zh-HK` cantonesiska (Hongkong).

- **5** nya röster i förhandsversionen: Kert i 1955 års `et-EE` språk, Colm i `ga-IE` Pål (Irland), Dar in `lv-LV` Lettiska (Lettiska), Lettiska ( Lettiska), Mönska `lt-LT` (Pål) och Joseph `mt-MT` i Sint (Island).

I den här versionen stöder vi nu totalt 129 neurala röster över 54 språk/språk. Dessutom finns över 70 standardröster tillgängliga på 49 språk/språk. Den [fullständiga listan finns](language-support.md#text-to-speech) i Språkstöd.

**Uppdateringar för Skapa ljudinnehåll**
- Förbättrat användargränssnitt för röstval med röstkategorier och detaljerade röstbeskrivningar. 
- Aktiverade intonationsjustering för alla neurala röster över olika språk.
- Automatisk lokalisering av användargränssnittet baserat på webbläsarens språk.
- Aktiverade `StyleDegree` kontroller för alla `zh-CN` neurala röster.
Gå till [Skapa ljudinnehåll för](https://speech.microsoft.com/audiocontentcreation) att kolla in de nya funktionerna. 

**Uppdateringar för zh-CN-röster**
- Uppdaterade alla `zh-CN` neurala röster för att stödja engelsktalande.
- Aktiverade alla `zh-CN` neurala röster för att stödja justering av intonation. SSML eller Skapa ljudinnehåll kan användas för att justera för bästa intonation.
- Uppdaterade alla `zh-CN` neurala röster i flera format för att stödja `StyleDegree` kontroll. Känslointensiteten (mjuk eller stark) kan justeras.
- Har `zh-CN-YunyeNeural` uppdaterats för att stödja flera format som kan utföra olika känslor.

## <a name="text-to-speech-2020-november-release"></a>Text till tal-version 2020–november

**Nya språk och röster i förhandsversion**
- **Fem nya röster och språk** introduceras i den neurala TTS-portföljen. De är: Grace in Grace (Lagra), Ona i Eta (1966), Anu in 1975 (1966), Orla in Ireland (Ireland) och Everita i Lettiska (Lettiska).
- **Fem nya `zh-CN` röster med flera format och roller stöder**: Xiaohan, Xiaxo, Xiaorui, Xiaoxuan och Yunxi.

> Dessa röster är tillgängliga i offentlig förhandsversion i tre Azure-regioner: USA, östra; Asien, sydöstra och Europa, västra.

**Neural TTS-container – GA**
- Med neural TTS-container kan utvecklare köra talsyntes med de mest naturliga digitala rösterna i sin egen miljö för specifika säkerhets- och datastyrningskrav. Kontrollera [hur du installerar Speech-containrar.](speech-container-howto.md) 

**Nya funktioner**
- **Anpassad röst:** har aktiverat användare för att kopiera en röstmodell från en region till en annan; slutpunktsavstängning som stöds och återupptas. Gå till [portalen](https://speech.microsoft.com/customvoice) här.
- [Stöd för SSML-tystnadstaggar.](speech-synthesis-markup.md#add-silence) 
- Allmänna förbättringar av TTS-röstkvalitet: Förbättrad uttalsprecision på ordnivå i nb-NO. Minskat uttalsfel på 53 %.

> Läs mer på [den här tekniska bloggen.](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604)

## <a name="text-to-speech-2020-october-release"></a>Text till tal-version 2020–oktober

**Nya funktioner**
- Jens har stöd för ett nytt `newscast` format. Se [hur du använder talformaten i SSML](speech-synthesis-markup.md#adjust-speaking-styles).
- **Neurala röster har uppgraderats till HiFiNet-vocoder, med högre ljudåtergivning och snabbare synteshastighet.** Det här är till fördel för kunder vars scenario förlitar sig på hi-fi-ljud eller långa interaktioner, inklusive videodubbning, ljudböcker eller utbildningsmaterial online. [Läs mer om berättelsen och hör röstexempel på vår tech community-blogg](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Anpassad röst](https://speech.microsoft.com/customvoice)  &  [Skapa ljudinnehåll Studio](https://speech.microsoft.com/audiocontentcreation) lokaliserat till 17 språk**. Användarna kan enkelt växla användargränssnittet till ett lokalt språk för en mer användarvänlig upplevelse.   
- **Skapa ljudinnehåll:** Lade till stilgradskontroll för Xia styrenhet; Förfinade den anpassade brytfunktionen för att inkludera inkrementella avbrott på 50 ms. 

**Allmänna förbättringar av TTS-röstkvalitet**
- Förbättrad uttalsprecision på ordnivå `pl-PL` i (minskning av felfrekvens: 51 %) och `fi-FI` (felfrekvensminskning: 58 %)
- Förbättrad `ja-JP` läsning av enstaka ord för ordlistescenariot. Minskat uttalsfel med 80 %.
- `zh-CN-XiaoxiaoNeural`: Förbättrad sentiment/CustomerService/Newscast/Igenkänning/Stil med röstkvalitet.
- `zh-CN`: Förbättrat Uttal och ljus ton samt förfinad rymdproody, vilket avsevärt förbättrar läsbarheten. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK 1.14.0: version 2020–oktober

> [!NOTE]
> Speech SDK för Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned den [här.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Nya funktioner**
- **Linux:** Stöd har lagts till för Debian 10 och Ubuntu 20.04 LTS.
- **Python/Objective-C:** Stöd har lagts till för `KeywordRecognizer` API:et. Dokumentationen finns [här.](./custom-keyword-basics.md)
- **C++/Java/C#**: Stöd har lagts till för att ange `HttpHeader` valfri nyckel/värde via `ServicePropertyChannel::HttpHeader` .
- **JavaScript:** Stöd har lagts till för `ConversationTranscriber` API:et. Läs dokumentationen [här.](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript) 
- **C++/C#**: Ny metod har lagts `AudioDataStream FromWavFileInput` till (för att läsa . WAV-filer) [här (C++)](/cpp/cognitive-services/speech/audiodatastream) [och här (C#).](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream)
-  **C++/C#/Java/Python/Objective-C/Swift:** Lade till en metod för att `stopSpeakingAsync()` stoppa text till tal-syntes. Läs referensdokumentationen [här (C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), här [(C#)](/dotnet/api/microsoft.cognitiveservices.speech), här [(Java)](/java/api/com.microsoft.cognitiveservices.speech), [här (Python)](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)och här [(Objective-C/Swift).](/objectivec/cognitive-services/speech/)
- **C#, C++, Java:** En funktion har lagts till i klassen som kan användas för att övervaka `FromDialogServiceConnector()` `Connection` anslutnings- och frånkopplingshändelser för `DialogServiceConnector` . Läs referensdokumentationen [här (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [här (C++)](/cpp/cognitive-services/speech/connection)och [här (Java).](/java/api/com.microsoft.cognitiveservices.speech.connection)
- **C++/C#/Java/Python/Objective-C/Swift:** Stöd har lagts till för uttalsbedömning, som utvärderar taluttal och ger talare feedback om noggrannheten och flängden hos talat ljud. Läs dokumentationen [här.](how-to-pronunciation-assessment.md)

**Ändring som inte har ändrats**
- **JavaScript:** PullAudioOutputStream.read() har en ändring av returtypen från ett internt promise till ett internt JavaScript-löfte.

**Felkorrigeringar**
- **Alla**: Regressionen 1,13 har åtgärdats `SetServiceProperty` i där värden med vissa specialtecken ignorerades.
- **C#**: Windows-konsolexempel har åtgärdats Visual Studio 2019 kunde inte hitta interna DLL:er.
- **C#**: Krasch med minneshantering har åtgärdats om dataströmmen används som `KeywordRecognizer` indata.
- **ObjectiveC/Swift:** Krasch med minneshantering har åtgärdats om dataströmmen används som igenkänningsinmatning.
- **Windows:** Problem med samexistens har åtgärdats med BT HFP/A2DP på UWP.
- **JavaScript:** Åtgärdade mappning av sessions-ID:er för att förbättra loggning och hjälp med interna felsöknings-/tjänstkorrelationer.
- **JavaScript:** Korrigering har lagts till `DialogServiceConnector` för inaktivering `ListenOnce` av anrop efter det första anropet.
- **JavaScript:** Ett problem har åtgärdats där resultatutdata någonsin bara skulle vara "enkla".
- **JavaScript:** Problem med kontinuerlig igenkänning har åtgärdats i Safari på macOS.
- **JavaScript:** Processorbelastningsminskning för dataflödesscenario med hög begäran.
- **JavaScript:** Tillåt åtkomst till information om resultatet av röstprofilregistreringen.
- **JavaScript:** Korrigering har lagts till för kontinuerlig igenkänning i `IntentRecognizer` .
- **C++/C#/Java/Python/Swift/ObjectiveC:** Felaktig URL har åtgärdats för australien, östra och brazilsouth i `IntentRecognizer` .
- **C++/C#**: Läggs till `VoiceProfileType` som ett argument när du skapar ett `VoiceProfile` -objekt.
- **C++/C#/Java/Python/Swift/ObjectiveC: Potentialen har** åtgärdats `SPX_INVALID_ARG` när du försöker läsa från en viss `AudioDataStream` position.
- **IOS:** Krasch med taligenkänning i Unity har åtgärdats

**Exempel**
- **ObjectiveC:** Exempel för nyckelordsigenkänning har lagts [till här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples)
- **C#/JavaScript:** Snabbstart för samtalstranskriskription har lagts [till här (C#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) [och här (JavaScript).](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription)
- **C++/C#/Java/Python/Swift/ObjectiveC:** Exempel för uttalsbedömning har lagts [till här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin:** Uppdaterad snabbstart till senaste Visual Studio [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Känt problem**
- DigiCert Global Root G2-certifikat stöds inte som standard i HoloLens 2 och Android 4.4 (KitKat) och måste läggas till i systemet för att Speech SDK ska fungera. Certifikatet kommer att läggas till i HoloLens 2 OS-avbildningar inom en snar framtid. Android 4.4-kunder måste lägga till det uppdaterade certifikatet i systemet.

**Covid-19 förkortad testning:** På grund av att vi har arbetat via fjärrstyrning under de senaste veckorna kan vi inte utföra så mycket manuell verifieringstestning som vanligt. Vi har inte gjort några ändringar som vi tror kan ha brutit något, och alla våra automatiserade tester har godkänts. Om vi osannolikt missade något kan du berätta det för oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Håll dig felfri!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Speech CLI (även kallat SPX): version 2020–oktober
SPX är kommandoradsgränssnittet för att använda Azure Speech-tjänsten utan att skriva kod. Ladda ned den senaste versionen [här.](./spx-basics.md) <br>

**Nya funktioner**
- `spx csr dataset upload --kind audio|language|acoustic` – skapa datauppsättningar från lokala data, inte bara från URL:er.
- `spx csr evaluation create|status|list|update|delete` – jämför nya modeller med grundläggande sanning/andra modeller.
- `spx * list` – stöder icke-sidindelade funktioner (kräver inte --top X --skip X).
- `spx * --http header A=B` – stöd för anpassade huvuden (tillagt för Office för anpassad autentisering). 
- `spx help` – förbättrad text- och bakåt-tick-textfärg kodad (blå).

## <a name="text-to-speech-2020-september-release"></a>Text till tal-version 2020–september

### <a name="new-features"></a>Nya funktioner

* **Neural TTS** 
    * **Utökat för att stödja 18 nya språk/språk.** De är slovakiska, tjeckiska, tyska (Schweiz), tyska (Schweiz), grekiska, engelska (Irland), franska (Schweiz), hebreiska, ungerska, ungerska, indonesiska, malajiska, rumänska, slovakiska, slovakiska, tamil, Telugu och vietnamesiska. 
    * **Släppte 14 nya röster för att utöka variationen på de befintliga språken.** Se [fullständigt språk och röstlista.](language-support.md#neural-voices)
    * **Nya talformat för `en-US` och `zh-CN` röster.** Jens, den nya rösten på engelska (USA), stöder chattrobot, kundtjänst och assistentstilar. 10 nya talformat är tillgängliga med vår zh-CN-röst XiaoXiao. Dessutom stöder xiaoXiao neurala röst `StyleDegree` justering. Se [hur du använder talformaten i SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Containrar: Neural TTS-container släpps i offentlig förhandsversion med 16 röster tillgängliga på 14 språk.** Läs mer om [hur du distribuerar Speech-containrar för neural TTS](speech-container-howto.md)  

Läs det [fullständiga tts-meddelandet om TTS-uppdateringar för Ignite 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Text till tal-version 2020–augusti

### <a name="new-features"></a>Nya funktioner

* **Neural TTS: nytt talformat för `en-US` Aria voice**. AriaNeatur kan låta som en nyhetslyssnare när du läser nyheter. Det "nyhetssändnings formella" formatet låter mer allvarligt, medan "newscast-casual"-stilen är mer avslappnad och informell. Se [hur du använder talformaten i SSML](speech-synthesis-markup.md).

* **Anpassad röst: en ny funktion lanseras för att automatiskt kontrollera träningsdatakvaliteten**. När du laddar upp dina data undersöker systemet olika aspekter av dina ljud- och avskriftsdata och åtgärdar eller filtrerar automatiskt problem för att förbättra röstmodellens kvalitet. Detta omfattar volymen av ditt ljud, brusnivån, uttalsprecisionen för tal, justering av tal med normaliserad text, tystnad i ljudet, förutom ljud- och skriptformatet. 

* **Skapa ljudinnehåll: en uppsättning nya funktioner som möjliggör kraftfullare funktioner för röstjustering och ljudhantering.**

    * Uttal: uttalsjusteringsfunktionen uppdateras till den senaste telefonuppsättningen. Du kan välja rätt fonme-element från biblioteket och förfina uttalet av de ord som du har valt. 

    * Ladda ned: Ljudfunktionen "Ladda ned"/"Exportera" har förbättrats för att ge stöd för generering av ljud enligt stycke. Du kan redigera innehåll i samma fil/SSML samtidigt som du genererar flera ljudutdata. Filstrukturen för "Ladda ned" förfinas också. Nu kan du enkelt hämta alla ljudfiler i en mapp. 

    * Uppgiftsstatus: Exportupplevelsen för flera filer har förbättrats. Om en av filerna har misslyckats när du exporterar flera filer tidigare misslyckas hela aktiviteten. Men nu exporteras alla andra filer. Uppgiftsrapporten är berikad med mer detaljerad och strukturerad information. Du kan nu söka i loggarna efter alla filer och meningar som misslyckats med rapporten. 

    * SSML-dokumentation: länkad till SSML-dokument som hjälper dig att kontrollera reglerna för hur du använder alla justeringsfunktioner.

* **API:et för röstlista har uppdaterats för att innehålla ett användarvänligt visningsnamn och de talformat som stöds för neurala röster.**

### <a name="general-tts-voice-quality-improvements"></a>Allmänna förbättringar av TTS-röstkvalitet

* Minskat uttalsfel på ordnivå % `ru-RU` för (fel som minskats med 56 %) och `sv-SE` (fel minskade med 49 %)

* Förbättrad polycyklisk ordläsning på `en-US` neurala röster med 40 %. Exempel på polycykliska ord är "read", "live", "content", "record", "object" osv. 

* Förbättrade frågetonens naturliga natur i `fr-FR` . MOS (Mean Opinion Score) gain: +0,28

* Vocoders för följande röster har uppdaterats med återgivningsförbättringar och en övergripande prestandahastighet på 40 %.

    | Nationell inställning | Röst |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Så här ser det ut |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Felkorrigeringar

* Åtgärdat ett antal buggar med Skapa ljudinnehåll verktyget 
    * Ett problem med automatisk uppdatering har åtgärdats. 
    * Problem med röstformat i zh-CN i Asien, sydöstra regionen har åtgärdats.
    * Ett stabilitetsproblem har åtgärdats, inklusive ett exportfel med taggen "break" och fel i skiljetecken.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nya språk för tal till text: 2020–augusti
Tal till text släppte 26 nya språk i augusti: 2 europeiska språk och , 5 engelska språk och 19 spanska språk som täcker de flesta `cs-CZ` `hu-HU` Sydamerika. Nedan visas en lista över de nya språken. Se den fullständiga språklistan [här.](./language-support.md)

| Nationell inställning  | Språk                          |
|---------|-----------------------------------|
| `cs-CZ` | Tjeckiska (Tjeckien)            | 
| `en-HK` | Engelska (Hongkong)               | 
| `en-IE` | Engelska (Irland)                 | 
| `en-PH` | Engelska (På engelska)             | 
| `en-SG` | Engelska (Singapore)               | 
| `en-ZA` | Engelska (Sydafrika)            | 
| `es-AR` | Spanska (Argentina)               | 
| `es-BO` | Spanska (Spanien)                 | 
| `es-CL` | Spanska (Brasilien)                   | 
| `es-CO` | Spanska (Spanien)                | 
| `es-CR` | Spanska (Kurs)              | 
| `es-CU` | Spanska (Spanien)                    | 
| `es-DO` | Spanska (Folkrepubliken Republiken)      | 
| `es-EC` | Spanska (Spanien)                 | 
| `es-GT` | Spanska (Spanien)               | 
| `es-HN` | Spanska (Spanien)                | 
| `es-NI` | Spanska (Spanien)               | 
| `es-PA` | Spanska (Spanien)                  | 
| `es-PE` | Spanska (Spanien)                    | 
| `es-PR` | Spanska (Aria).             | 
| `es-PY` | Spanska (Argentina)                | 
| `es-SV` | Spanska (ElIsten)             | 
| `es-US` | Spanska (USA)                     | 
| `es-UY` | Spanska (Argentina)                 | 
| `es-VE` | Spanska (Spanien)               | 
| `hu-HU` | Ungerska (Ungern)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0: 2020–juli

> [!NOTE]
> Speech SDK för Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned och installera det [härifrån.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

**Nya funktioner**
- **C#**: Stöd har lagts till för asynkron samtalstranskriskription. Se dokumentationen [här.](./how-to-async-conversation-transcription.md)  
- **JavaScript:** Har Talarigenkänning stöd för [både webbläsare](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) och [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **JavaScript:** Stöd har lagts till för automatisk språkidentifiering/språk-ID. Se dokumentationen [här.](./how-to-automatic-language-detection.md?pivots=programming-language-javascript)
- **Objective-C:** Stöd har lagts till för [konversations- och](./multi-device-conversation.md) samtalstranskriskription [för flera enheter.](./conversation-transcription.md) 
- **Python:** Stöd för komprimerat ljud har lagts till för Python i Windows och Linux. Se dokumentationen [här.](./how-to-use-codec-compressed-audio-input-streams.md) 

**Felkorrigeringar**
- **Alla**: Ett problem som gjorde att KeywordRecognizer inte kunde flytta dataströmmarna efter en igenkänning har åtgärdats.
- **Alla**: Ett problem har åtgärdats som gjorde att dataströmmen som erhölls från keywordRecognitionResult inte innehöll nyckelordet .
- **Alla**: Ett problem har åtgärdats som gjorde att SendMessageAsync egentligen inte skickade meddelandet via kabel efter att användarna har väntat på det.
- **Alla**: En krasch i Talarigenkänning-API:er har åtgärdats när användare anropar metoden VoiceProfileClient::SpeakerRecEnrollProfileAsync flera gånger och inte väntade på att anropen skulle slutföras.
- **Alla**: Aktivera filloggning i VoiceProfileClient- och SpeakerRecognizer-klasser har åtgärdats.
- **JavaScript:** Ett [problem med](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74) begränsning har åtgärdats när webbläsaren minimeras.
- **JavaScript:** Åtgärdat [ett problem](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78) med en minnesläcka i strömmar.
- **JavaScript:** Cachelagring har lagts till för OCSP-svar från NodeJS.
- **Java:** Ett problem som gjorde att BigInteger-fält alltid returnerade 0 har åtgärdats.
- **iOS:** Ett problem med [att](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) publicera Speech SDK-baserade appar i iOS-App Store.

**Exempel**
- **C++**: Exempelkod har lagts till för Talarigenkänning [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**Covid-19 förkortad testning:** På grund av att vi har arbetat via fjärrstyrning under de senaste veckorna kan vi inte utföra så mycket manuell verifieringstestning som vanligt. Vi har inte gjort några ändringar som vi tror kan ha brutit något, och alla våra automatiserade tester har godkänts. Om vi osannolikt missade något kan du berätta det för oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Håll dig felfri!

## <a name="text-to-speech-2020-july-release"></a>Text till tal-version 2020–juli

### <a name="new-features"></a>Nya funktioner

* **Neural TTS, 15** nya neurala röster: De nya röster som lagts till i neurala TTS-portföljen är Salma på arabiska `ar-EG` (Tecken), Zariari på `ar-SA` arabiska (Arabiska (Förenade Arabemiraten), Adde (Spanien), Makel i Danska `ca-ES` (Brasilien), Neerja på engelska (Indien), Noora på finska `da-DK` `es-IN` `fi-FI` (Finn), Swara in `hi-IN` Hindi (India), Colette in `nl-NL` Dutch (Nederländerna), Zofia in `pl-PL` Polish (Portugal), Fernanda in `pt-PT` Portuguese (Portugal), Dariri in `ru-RU` Russian (Russian (Ryssland), Hillevi in `sv-SE` Svenska (Sweden), Achara in `th-TH` Thailändska (Pipe), HiuGaai in `zh-HK` Chinese (Cantonese, Traditionell) och HostoYu på `zh-TW` kinesiska (taiwanesiska mandarin). Kontrollera alla [språk som stöds.](./language-support.md#neural-voices)  

* **Anpassad röst, effektiviserad** rösttestning med träningsflödet för att förenkla användarupplevelsen: Med den nya testfunktionen testas varje röst automatiskt med en fördefinierad testuppsättning som är optimerad för varje språk för att täcka allmänna scenarier och röstassistentscenarier. Dessa testuppsättningar väljs noggrant och testas för att inkludera vanliga användningsfall och fonem på språket. Dessutom kan användarna fortfarande välja att ladda upp sina egna testskript när de tränar en modell.

* **Skapa ljudinnehåll: en uppsättning nya funktioner släpps för att möjliggöra kraftfullare röstjustering och ljudhanteringsfunktioner**

    * `Pitch`, `rate` och har förbättrats för att stödja justering med ett `volume` fördefinierat värde, till exempel långsam, medel och snabb. Nu är det enkelt för användarna att välja ett konstant värde för ljudredigeringen.

    ![Ljudjustering](media/release-notes/audio-tuning.png)

    * Användare kan nu granska `Audio history` för sin arbetsfil. Med den här funktionen kan användarna enkelt spåra allt genererat ljud som är relaterat till en fungerande fil. De kan kontrollera historikversionen och jämföra kvaliteten vid justering samtidigt. 

    ![Ljudhistorik](media/release-notes/audio-history.png)

    * Funktionen `Clear` är nu mer flexibel. Användare kan rensa en specifik justeringsparameter samtidigt som andra parametrar är tillgängliga för det valda innehållet.  

    * En självstudievideo lades till på [landningssidan för att](https://speech.microsoft.com/audiocontentcreation) hjälpa användarna att snabbt komma igång med TTS-röstjustering och ljudhantering. 

### <a name="general-tts-voice-quality-improvements"></a>Allmänna förbättringar av TTS-röstkvalitet

* Förbättrad TTS-vocoder i för högre återgivning och kortare svarstider.

    * uppdaterades Till en ny vocoder som uppnådde `it-IT` en ökning på +0,464 CMOS (jämförelsetalspoäng för yttrande) i röstkvalitet, 40 % snabbare i syntes och 30 % minskning vid första bytesvarstid. 
    * Xia ctrlao har uppdaterats till den nya `zh-CN` vocoder med +0148 CMOS-vinst för den allmänna domänen, +0,348 för nyhetssändningsformatet och +0,195 för det stilrena formatet. 

* Uppdaterade `de-DE` modeller `ja-JP` och röstmodeller för att göra TTS-utdata mer naturliga.
    
    * Uppdaterade Såja i `de-DE` med den senaste prosodymodelleringsmetoden är MOS-vinsten (Mean Opinion Score) +0,13. 
    * Uppdaterade Löpande i `ja-JP` med en ny prosodymodell för accenter, MOS-vinsten (Mean Opinion Score) är +0,19;  

* Förbättrad uttalsprecision på ordnivå på fem språk.

    | Språk | Minskning av uttalsfel |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>Felkorrigeringar

* Valutaläsning
    * Problemet med valutaläsning för och har `es-ES` åtgärdats `es-MX`
     
    | Språk | Indata | Läs ut efter förbättring |
    |---|---|---|
    | `es-MX` | 1,58 USD | un peso cincuenta y ocho centavos |
    | `es-ES` | 1,58 USD | un d avmarkera cincuenta y ocho centavos |

    * Stöd för negativ valuta (t.ex. "-325 €" ) i följande språk: `en-US` , , , , , `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

* Förbättrad adressläsning i `pt-PT` .
* Åtgärdat problem med Natasha ( ) och `en-AU` Libby ( `en-UK` ) uttal på ordet "for" och "fyra".  
* Åtgärdat buggar i Skapa ljudinnehåll verktyg
    * Den ytterligare och oväntade pausen efter det andra stycket är fast.  
    * Funktionen "Ingen avbrott" läggs tillbaka från en regressionsbugg. 
    * Problemet med slumpmässig uppdatering av Speech Studio har åtgärdats.  

### <a name="samplessdk"></a>Exempel/SDK

* JavaScript: Åtgärdar uppspelningsproblem i Firefox och Safari på macOS och iOS. 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1: version 2020–juni
**Speech CLI (även kallat SPX)**
-   Hjälpsökningsfunktioner i CLI har lagts till:
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   Har uppdaterats för att fungera med nyligen distribuerade v3.0 Batch- och Custom Speech-API:er:
    -   `spx help batch examples`
    -   `spx help csr examples`

**Nya funktioner**
-   **C \# , C++**: Talarigenkänning Förhandsversion: Den här funktionen möjliggör talaridentifiering (vem talar?) och talarverifiering (är talaren som de utger sig för att vara?). Börja med en [översikt](./speaker-recognition-overview.md), läs [artikeln Talarigenkänning grundläggande om ,](./get-started-speaker-recognition.md)eller [API-referensdokumenten](/rest/api/speakerrecognition/).

**Felkorrigeringar**
-   **C \# , C++**: Fast mikrofoninspelning fungerade inte i 1.12 i talarigenkänning.
-   **JavaScript:** Korrigeringar för text till tal i Firefox och Safari på macOS och iOS.
-   Korrigering för krasch i windows-programverifieraren för åtkomstöverträdelse vid samtalstranskriskription vid användning av åttakanalsströmmar.
-   Korrigering för krascher i windows-programverifierarens åtkomstöverträdelse på konversationsöversättningar med flera enheter.

**Exempel**
-   **C#**: [Kodexempel för](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) talarigenkänning.
-   **C++**: [Kodexempel för](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) talarigenkänning.
-   **Java:** [Kodexempel för](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) avsiktsigenkänning på Android. 

**Covid-19 förkortad testning:** På grund av att vi har arbetat på distans under de senaste veckorna kan vi inte göra så mycket manuell verifieringstestning som vanligt. Vi har inte gjort några ändringar som vi tror kan ha brutit något, och alla våra automatiserade tester har godkänts. I händelse av osannolika att vi missade något kan du meddela oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Håll dig felfri!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0: 2020-maj
**Speech CLI (kallas även SPX)**
- **SPX** är ett nytt kommandoradsverktyg som gör att du kan utföra igenkänning, syntes, översättning, batchtranskribering och anpassad talhantering från kommandoraden. Använd den för att testa Speech Service eller för att skripta de Speech Service-uppgifter som du behöver utföra. Ladda ned verktyget och läs dokumentationen [här.](./spx-overview.md)

**Nya funktioner**
- **Go:** Nytt Go-språkstöd för [taligenkänning](./get-started-speech-to-text.md?pivots=programming-language-go) och [anpassad röstassistent](./quickstarts/voice-assistants.md?pivots=programming-language-go). Konfigurera utvecklingsmiljön [här.](./quickstarts/setup-platform.md?pivots=programming-language-go) Exempelkod finns i avsnittet Exempel nedan. 
- **JavaScript:** Webbläsarstöd för text till tal har lagts till. Se dokumentationen [här.](./get-started-text-to-speech.md?pivots=programming-language-JavaScript)
- **C++, C#, Java:** Nya objekt och `KeywordRecognizer` API:er som stöds på Windows,Android, Linux & iOS-plattformar. Läs dokumentationen [här.](./custom-keyword-overview.md) Exempelkod finns i avsnittet Exempel nedan. 
- **Java:** Konversation med flera enheter har lagts till med översättningsstöd. Se [referensdokumenten här.](/java/api/com.microsoft.cognitiveservices.speech.transcription)

**Förbättringar & optimeringar**
- **JavaScript:** Optimerad implementering av webbläsarens mikrofon som förbättrar noggrannheten för taligenkänning.
- **Java:** Omstrukturerade bindningar med direkt JNI-implementering utan SWIG. Den här ändringen minskar bindningsstorleken med 10 gånger för alla Java-paket som används för Windows, Android, Linux och Mac och underlättar ytterligare utveckling av Speech SDK Java-implementeringen.
- **Linux:** Uppdaterad [supportdokumentation](./speech-sdk.md?tabs=linux) med den senaste specifika RHEL 7-informationen.
- Förbättrad anslutningslogik för att försöka ansluta flera gånger när tjänst- och nätverksfel uppstår.
- Uppdaterade [portal.azure.com](https://portal.azure.com) Snabbstart för tal för att hjälpa utvecklare att ta nästa steg i Azure Speech-resan.

**Felkorrigeringar**
- **C#, Java:** Ett problem med [att](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) läsa in SDK-bibliotek på Linux ARM har åtgärdats (både 32-bitars och 64-bitars).
- **C#**: Ett explicit bortskaffande av interna referenser för TranslationRecognizer-, IntentRecognizer- och Connection-objekt har åtgärdats.
- **C#**: Livslängdshantering för ljudinmatning har åtgärdats för ConversationTranscriber-objekt.
- Åtgärdat ett problem `IntentRecognizer` där resultatorsaken inte har angetts korrekt vid igenkänning av avsikter från enkla fraser.
- Åtgärdat ett problem `SpeechRecognitionEventArgs` där resultatförskjutningen inte har angetts korrekt.
- Ett konkurrenstillstånd där SDK försökte skicka ett nätverksmeddelande innan websocket-anslutningen öppnades har åtgärdats. Reproducerbar för vid `TranslationRecognizer` tillägg av deltagare.
- Minnesläckor har åtgärdats i nyckelords recognizer-motorn.

**Exempel**
- **Go:** Snabbstarter för taligenkänning [och anpassad](./get-started-speech-to-text.md?pivots=programming-language-go) [röstassistent har lagts till.](./quickstarts/voice-assistants.md?pivots=programming-language-go) Hitta exempelkod [här.](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples) 
- **JavaScript:** Snabbstarter har lagts [till för text till tal,](./get-started-text-to-speech.md?pivots=programming-language-javascript)översättning och [Avsiktsigenkänning](./get-started-intent-recognition.md?pivots=programming-language-javascript). [](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)
- Exempel på nyckelordsigenkänning [för \# C](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) och [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**Covid-19 förkortad testning:** På grund av att vi har arbetat fjärranslutet under de senaste veckorna kan vi inte göra lika mycket manuell verifieringstestning som vanligt. Vi har inte gjort några ändringar som vi tror kan ha brutit något, och alla våra automatiserade tester har godkänts. Om vi missade något kan du meddela oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br>
Håll dig felfri!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0: 2020-mars
**Nya funktioner**
- Linux: Stöd har lagts till för Red Hat Enterprise Linux (RHEL)/CentOS [](./how-to-configure-rhel-centos-7.md) 7 x64 med instruktioner om hur du konfigurerar systemet för Speech SDK.
- Linux: Stöd har lagts till för .NET Core C# på Linux ARM32 och ARM64. Läs mer [här](./speech-sdk.md?tabs=linux). 
- C#, C++: Har lagts `UtteranceId` till i , ett konsekvent ID för alla `ConversationTranscriptionResult` mellanliggande och sista taligenkänningsresultat. Information för [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: Stöd har lagts till för `Language ID` . Se speech_sample.py på [GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: Stöd för komprimerat ljudindataformat har lagts till på Windows-plattformen för alla win32-konsolprogram. Mer [information finns här.](./how-to-use-codec-compressed-audio-input-streams.md) 
- JavaScript: Stöd för talsyntes (text till tal) i NodeJS. Lär dig mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- JavaScript: Lägg till nya API:er för att möjliggöra granskning av alla meddelanden som skickas och tas emot. Lär dig mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Felkorrigeringar**
- C#, C++: Ett problem har åtgärdats så `SendMessageAsync` att nu skickar binärmeddelande som binär typ. Information för [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: Ett problem har åtgärdats där användning av händelsen kan orsaka `Connection MessageReceived` krasch om tas bort före `Recognizer` `Connection` objektet. Information för [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: Ljudbuffertstorleken från mikrofonen minskade från 800 ms till 100 ms för att förbättra svarstiden.
- Android: Ett problem har [åtgärdats](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) med x86 Android-emulatorn i Android Studio.
- JavaScript: Stöd har lagts till för regioner i Kina med `fromSubscription` API:et. Mer [information finns här.](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-) 
- JavaScript: Lägg till mer felinformation för anslutningsfel från NodeJS.
        
**Exempel**
- Unity: Det offentliga exemplet för avsiktsigenkänning har åtgärdats, där LUIS json-importen misslyckades. Mer [information finns här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369)
- Python: Exempel har lagts till för `Language ID` . Information [här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py)
    
**Covid19 förkortad testning:** På grund av att vi har arbetat fjärranslutet under de senaste veckorna kan vi inte göra lika mycket manuella verifieringstestning av enheter som vanligt. Vi kunde till exempel inte testa mikrofonindata och talarutdata på Linux, iOS och macOS. Vi har inte gjort några ändringar som vi tror kan ha brutit något på dessa plattformar och alla våra automatiserade tester har godkänts. I händelse av osannolika att vi missade något kan du meddela oss på [GitHub.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen)<br> Tack för ditt fortsatt stöd. Som alltid kan du skicka frågor eller feedback på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) eller [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Håll dig felfri!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: 2020–februari

**Nya funktioner**

 - Python-paket har lagts till för att stödja den nya 3.8-versionen av Python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64-stöd (C++, C#, Java, Python).
   > [!NOTE] 
   > Kunder måste konfigurera OpenSSL enligt [dessa instruktioner.](./how-to-configure-openssl-linux.md)
 - Linux ARM32-stöd för Debian och Ubuntu.
 - DialogServiceConnector stöder nu en valfri "bot ID"-parameter på BotFrameworkConfig. Den här parametern tillåter användning av flera Direct Line Speech robotar med en enda Azure Speech-resurs. Utan den angivna parametern används standardroboten (enligt Direct Line Speech av konfigurationssidan för Direct Line Speech kanal).
 - DialogServiceConnector har nu egenskapen SpeechActivityTemplate. Innehållet i den här JSON-strängen används av Direct Line Speech för att fylla i en mängd olika fält som stöds i alla aktiviteter som når en Direct Line Speech-robot, inklusive aktiviteter som genereras automatiskt som svar på händelser som taligenkänning.
 - TTS använder nu prenumerationsnyckeln för autentisering, vilket minskar den första bytesvarstiden för det första syntesresultatet när du har skapat en syntetiserare.
 - Uppdaterade taligenkänningsmodeller för 19 språk för en genomsnittlig minskning av ordfelfrekvensen på 18,6 % (es-ES, es-MX, fr-CA, fr-FR, it-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, nb-NO, fi-FL, ru-RU, pl-PL, ca-ES, zh-TW, th-TH, pt-PT, tr-TR). De nya modellerna ger betydande förbättringar över flera domäner, inklusive diktering, Call-Center transkription och videoindexeringsscenarier.

**Felkorrigeringar**

 - Bugg där Conversation Transcriber inte väntade korrekt i JAVA-API:er har åtgärdats 
 - Korrigering av Android x86-emulator för Xamarin [GitHub-problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)
 - Lägg till saknad (Get| Ange)Egenskapsmetoder till AudioConfig
 - Åtgärda en TTS-bugg där audioDataStream inte kunde stoppas när anslutningen misslyckas
 - Att använda en slutpunkt utan region skulle orsaka USP-fel för konversationsöversättning
 - ID-generering i Universella Windows-program använder nu en korrekt unik GUID-algoritm. Det var tidigare och oavsiktligt standardvärdet för en snedställda implementering som ofta gav kollisioner över stora uppsättningar interaktioner.
 
 **Exempel**
 
 - Unity-exempel för att använda Speech SDK med [Unity-mikrofon och push-lägesströmning](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)

**Andra ändringar**

 - [OpenSSL-konfigurationsdokumentationen har uppdaterats för Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0: version 2020–januari

**Nya funktioner**

- Konversation med flera enheter: anslut flera enheter till samma tal- eller textbaserade konversation och översätt eventuellt meddelanden som skickas mellan dem. Läs mer i den [här artikeln.](multi-device-conversation.md) 
- Stöd för nyckelordsigenkänning har lagts till för Android .aar-paketet och stöd har lagts till för x86- och x64-varianter. 
- Objective-C: och `SendMessage` metoder som lagts till i `SetMessageProperty` `Connection` -objektet. Se dokumentationen [här.](/objectivec/cognitive-services/speech/spxconnection)
- TTS C++ api stöder nu som syntestextinmatning, vilket tar bort behovet av att konvertera en wstring till sträng innan `std::wstring` den vidarebefordras till SDK: n. Mer information [finns här.](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync) 
- C#: [Språk-ID](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) [och källspråkkonfiguration](./how-to-specify-source-language.md?pivots=programming-language-csharp) är nu tillgängliga.
- JavaScript: En funktion har lagts till `Connection` för objekt för att skicka anpassade meddelanden från Speech Service som återanrop. `receivedServiceMessage`
- JavaScript: Stöd har lagts `FromHost API` till för att underlätta användningen med lokala containrar och nationella moln. Se dokumentationen [här.](speech-container-howto.md)
- JavaScript: Vi respekterar `NODE_TLS_REJECT_UNAUTHORIZED` nu tack vare ett bidrag från [orgads](https://github.com/orgads). Mer information [finns här.](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)

**Icke-bakåtkompatibla ändringar**

- `OpenSSL` har uppdaterats till version 1.1.1b och är statiskt länkad till Speech SDK-kärnbiblioteket för Linux. Detta kan orsaka en avbrott om din `OpenSSL` inkorg inte har installerats `/usr/lib/ssl` i katalogen i systemet. Läs dokumentationen [under](how-to-configure-openssl-linux.md) Speech SDK-dokumentationen för att lösa problemet.
- Vi har ändrat datatypen som returneras för C# från till för att tillåta åtkomst till när `WordLevelTimingResult.Offset` `int` `long` `WordLevelTimingResults` taldata är längre än 2 minuter.
- `PushAudioInputStream` och `PullAudioInputStream` skickar nu wav-rubrikinformation till Speech Service baserat på `AudioStreamFormat` , som eventuellt angavs när de skapades. Kunderna måste nu använda det [ljudindataformat som stöds.](how-to-use-audio-input-streams.md) Andra format får icke-optimala igenkänningsresultat eller kan orsaka andra problem. 

**Felkorrigeringar**

- Se uppdateringen `OpenSSL` under Större ändringar ovan. Vi har åtgärdat både en tillfällig krasch och ett prestandaproblem (låsförseningar under hög belastning) i Linux och Java. 
- Java: Förbättrade objektstängning i scenarier med hög samtidighet.
- Omstrukturerade vårt NuGet-paket. Vi har tagit bort de tre kopiorna av och under lib-mapparna, vilket gör NuGet-paketet mindre och snabbare att ladda ned, och vi har lagt till rubriker som behövs för att kompilera vissa `Microsoft.CognitiveServices.Speech.core.dll` `Microsoft.CognitiveServices.Speech.extension.kws.dll` interna C++-appar.
- Snabbstartsexempel har åtgärdats [här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp) Dessa avslutas utan att undantagsfelet "mikrofon hittades" visas i Linux, macOS eller Windows.
- EN SDK-krasch med långa taligenkänningsresultat på vissa kodsökvägar som det [här exemplet har åtgärdats.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)
- Ett SDK-distributionsfel i Azure Web App-miljön har åtgärdats för att [åtgärda det här kundproblemet.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)
- Åtgärdat ett TTS-fel vid användning av flera `<voice>` taggar eller taggar för att åtgärda `<audio>` [kundproblemet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433). 
- Åtgärdat ett TTS 401-fel när SDK:n har återställts från en paus.
- JavaScript: En cirkulär import av ljuddata har åtgärdats tack vare ett bidrag från [euirim](https://github.com/euirim). 
- JavaScript: stöd har lagts till för att ange tjänstegenskaper, som lades till i 1.7.
- JavaScript: ett problem har åtgärdats där ett anslutningsfel kan leda till kontinuerliga, misslyckade websocket-återanslutningsförsök.

**Exempel**

- Exempel på nyckelordsigenkänning för Android har lagts [till här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)
- TTS-exempel har lagts till för serverscenariot [här.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)
- Snabbstarter för konversationer med flera enheter har lagts till för C# och C++ [här.](quickstarts/multi-device-conversation.md)

**Andra ändringar**

- Optimerad SDK-kärnbiblioteksstorlek på Android.
- SDK i 1.9.0 och senare stöder båda typerna och i fältet version av `int` `string` röstsignatur för Conversation Transcriber.

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: version 2019–november

**Nya funktioner**

- Ett `FromHost()` API har lagts till för att underlätta användningen med lokala containrar och nationella moln.
- Automatisk Språkidentifiering för taligenkänning (i Java och C++) har lagts till
- -objekt `SourceLanguageConfig` har lagts till för taligenkänning som används för att ange förväntade källspråk (i Java och C++)
- Stöd `KeywordRecognizer` har lagts till för Windows (UWP), Android och iOS via NuGet- och Unity-paketen
- Java API för fjärrkonversation har lagts till för transkription av konversation i asynkrona batchar.

**Icke-bakåtkompatibla ändringar**

- Conversation Transcriber-funktioner flyttades under namnrymden `Microsoft.CognitiveServices.Speech.Transcription` .
- Delar av konversations transkriberingsmetoderna flyttas till en ny `Conversation` klass.
- Bort ignorerat stöd för 32-bitars (ARMv7 och x86) iOS

**Felkorrigeringar**

- Korrigering för krasch om lokal `KeywordRecognizer` används utan en giltig prenumerationsnyckel för Speech-tjänsten

**Exempel**

- Xamarin-exempel för `KeywordRecognizer`
- Unity-exempel för `KeywordRecognizer`
- C++ och Java-exempel för automatisk Språkidentifiering.

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: 2019-september

**Nya funktioner**

- Betastöd har lagts till för Xamarin på Universell Windows-plattform (UWP), Android och iOS
- Stöd för iOS har lagts till för Unity
- `Compressed`Indatastöd har lagts till för AData, Mu linux, FLAC på Android, iOS och Linux
- Har `SendMessageAsync` lagts till i klassen för att skicka ett meddelande till `Connection` tjänsten
- Har `SetMessageProperty` lagts till i klassen för att ange egenskapen för ett `Connection` meddelande
- TTS-bindningar har lagts till för Java (JRE och Android), Python, Swift och Objective-C
- TTS har lagt till uppspelningsstöd för macOS, iOS och Android.
- Information om "ordgräns" har lagts till för TTS.

**Felkorrigeringar**

- Åtgärdat IL2CPP-kom build-problem på Unity 2019 för Android
- Problem med felaktiga huvuden i wav-filindata som bearbetas felaktigt har åtgärdats
- Problem med UID:er som inte är unika i vissa anslutningsegenskaper har åtgärdats
- Åtgärdat några varningar om nullbarhetsspecificerare i Swift-bindningar (kan kräva små kodändringar)
- En bugg som gjorde att websocket-anslutningar stängdes under nätverksbelastning har åtgärdats
- Ett problem har åtgärdats på Android som ibland resulterar i dubbletter av visnings-ID:er som används av `DialogServiceConnector`
- Förbättringar av stabiliteten för anslutningar över interaktioner med flera turn och rapportering av fel (via `Canceled` händelser) när de inträffar med `DialogServiceConnector`
- `DialogServiceConnector` -sessionen startar kommer nu att tillhandahålla händelser korrekt, inklusive vid anrop `ListenOnceAsync()` under en aktiv `StartKeywordRecognitionAsync()`
- Åtgärdat en krasch som är associerad `DialogServiceConnector` med aktiviteter som tas emot

**Exempel**

- Snabbstart för Xamarin
- Uppdaterad CPP-snabbstart med Linux ARM64-information
- Uppdaterad Unity-snabbstart med iOS-information

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: version 2019–juni

**Exempel**

- Snabbstartsexempel för Text till tal i UWP och Unity
- Snabbstartsexempel för Swift på iOS
- Unity-exempel för taligenkänning & Avsiktsigenkänning översättning
- Uppdaterade snabbstartsexempel för `DialogServiceConnector`

**Förbättringar/ändringar**

- Namnområde för dialogruta:
  - `SpeechBotConnector` har bytt namn till `DialogServiceConnector`
  - `BotConfig` har bytt namn till `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` har mappats om till `DialogServiceConfig::FromBotSecret()`
  - Alla befintliga Direct Line Speech-klienter fortsätter att stödjas efter namnbytet
- Uppdatera TTS REST-adaptern för att stödja proxy, beständig anslutning
- Förbättra felmeddelandet när en ogiltig region skickas
- Swift/Objective-C:
  - Förbättrad felrapportering: Metoder som kan resultera i ett fel finns nu i två versioner: En som exponerar ett objekt för felhantering och en som `NSError` ylar ett undantag. Den förstnämnda exponeras för Swift. Den här ändringen kräver anpassningar av befintlig Swift-kod.
  - Förbättrad händelsehantering

**Felkorrigeringar**

- Korrigering för TTS: där framtida `SpeakTextAsync` returneras utan att vänta tills ljudåtergivningen är klar
- Korrigering för att knava strängar i C# för att aktivera fullständigt språkstöd
- Korrigering för .NET Core-appproblem vid inläsning av kärnbibliotek med net461-målramverket i exempel
- Korrigering för tillfälliga problem med att distribuera interna bibliotek till utdatamappen i exempel
- Korrigering för stängning av webbsocket på ett tillförlitligt sätt
- Korrigering för möjlig krasch när en anslutning öppnas under hög belastning på Linux
- Korrigering för saknade metadata i ramverkspaketet för macOS
- Korrigering för problem med `pip install --user` i Windows

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

Det här är en felkorrigering som endast påverkar den inbyggda/hanterade SDK:n. Det påverkar inte JavaScript-versionen av SDK.

**Felkorrigeringar**

- Åtgärda FromSubscription när det används med samtalstranskriskription.
- Åtgärda bugg i nyckelords spotting för röstassistenter.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0: version 2019–maj

**Nya funktioner**

- Keyword spotting (KVS) är nu tillgängligt för Windows och Linux. FUNKTIONERNA I KVS kan fungera med alla typer av mikrofoner, officiellt STÖD FÖR MOBILENHETER, men är för närvarande begränsade till de mikrofonmatriser som finns i Azure Kinect DK-maskinvaran eller Speech Devices SDK.
- Funktionen för frastips är tillgänglig via SDK:n. Mer information finns [här.](./get-started-speech-to-text.md)
- Funktionen för samtalstranskriskription är tillgänglig via SDK:n. Här [kan du se](./conversation-transcription.md).
- Lägg till stöd för röstassistenter via Direct Line Speech kanal.

**Exempel**

- Exempel för nya funktioner eller nya tjänster som stöds av SDK har lagts till.

**Förbättringar/ändringar**

- Lade till olika igenkänneregenskaper för att justera tjänstens beteende eller tjänstresultat (som maskering av svordomar med mera).
- Du kan nu konfigurera recognizer via standardkonfigurationsegenskaperna, även om du har skapat identifieraren `FromEndpoint` .
- Objective-C: egenskapen `OutputFormat` har lagts till i `SPXSpeechConfiguration` .
- SDK stöder nu Debian 9 som en Linux-distribution.

**Felkorrigeringar**

- Ett problem har åtgärdats där talarresursen instruerades för tidigt i text till tal.

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

Det här är en felkorrigering som endast påverkar den inbyggda/hanterade SDK:n. Det påverkar inte JavaScript-versionen av SDK.

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

Det här är en version med endast JavaScript. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

- Förhindra att webbpaketet läser in https-proxy-agent.

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019-april

**Nya funktioner**

- SDK stöder nu text till tal-tjänsten som en betaversion. Det stöds på Windows och Linux Desktop från C++ och C#. Mer information finns i [översikten över text till tal.](text-to-speech.md#get-started)
- SDK stöder nu MP3- och Streaming/OGG-ljudfiler som indataströmfiler. Den här funktionen är endast tillgänglig i Linux från C++ och C# och är för närvarande i betaversion (mer information [finns här).](how-to-use-codec-compressed-audio-input-streams.md)
- Speech SDK för Java, .NET Core, C++ och Objective-C har fått stöd för macOS. Objective-C-stödet för macOS är för närvarande i betaversion.
- iOS: Speech SDK för iOS (Objective-C) publiceras nu också som CocoaPod.
- JavaScript: Stöd för mikrofoner som inte är standard som indataenhet.
- JavaScript: Proxystöd för Node.js.

**Exempel**

- Exempel för att använda Speech SDK med C++ och med Objective-C på macOS har lagts till.
- Exempel som visar användningen av text till tal-tjänsten har lagts till.

**Förbättringar/ändringar**

- Python: Ytterligare egenskaper för igenkänningsresultat visas nu via `properties` egenskapen .
- För ytterligare stöd för utveckling och felsökning kan du omdirigera SDK-loggning och diagnostikinformation till en loggfil (mer information [finns här).](how-to-use-logging.md)
- JavaScript: Förbättra prestanda för ljudbearbetning.

**Felkorrigeringar**

- Mac/iOS: En bugg som ledde till en lång väntan när det inte gick att upprätta en anslutning till Speech-tjänsten har åtgärdats.
- Python: förbättra felhanteringen för argument i Python-återanrop.
- JavaScript: Fel tillståndsrapportering för tal avslutades vid RequestSession.

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1: uppdatering 2019–februari

Det här är en felkorrigering som endast påverkar den inbyggda/hanterade SDK:n. Det påverkar inte JavaScript-versionen av SDK: n.

**Felkorrigering**

- En minnesläcka vid användning av mikrofonindata har åtgärdats. Strömbaserade indata eller filindata påverkas inte.

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: version 2019–februari

**Nya funktioner**

- Speech SDK stöder val av indatami mikrofon via `AudioConfig` klassen . På så sätt kan du strömma ljuddata till Speech-tjänsten från en mikrofon som inte är standard. Mer information finns i dokumentationen som beskriver valet av [ljudinspelningsenhet.](how-to-select-audio-input-devices.md) Den här funktionen är ännu inte tillgänglig från JavaScript.
- Speech SDK stöder nu Unity i en betaversion. Ge feedback via problemavsnittet i [GitHub-exempeldatabasen](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (skrivbord eller Universell Windows-plattform-program) och Android (ARM32/64, x86). Mer information finns i vår [Unity-snabbstart.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity)
- Filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (som levererades i tidigare versioner) behövs inte längre. Funktionen är nu integrerad i kärn-SDK:n.

**Exempel**

Följande nya innehåll är tillgängligt på vår [exempeldatabas:](https://aka.ms/csspeech/samples)

- Ytterligare exempel för `AudioConfig.FromMicrophoneInput` .
- Ytterligare Python-exempel för avsiktsigenkänning och översättning.
- Ytterligare exempel för att använda `Connection` objektet i iOS.
- Ytterligare Java-exempel för översättning med ljudutdata.
- Nytt exempel för användning av [Batch-transkription REST API](batch-transcription.md).

**Förbättringar/ändringar**

- Python
  - Förbättrad parameterverifiering och felmeddelanden i `SpeechConfig` .
  - Lägg till stöd för `Connection` -objektet.
  - Stöd för 32-bitars Python (x86) i Windows.
  - Speech SDK för Python är slut på betaversion.
- iOS
  - SDK:n har nu skapats mot iOS SDK version 12.1.
  - SDK stöder nu iOS-versionerna 9.2 och senare.
  - Förbättra referensdokumentationen och åtgärda flera egenskapsnamn.
- JavaScript
  - Lägg till stöd för `Connection` -objektet.
  - Lägga till typdefinitionsfiler för paketerat JavaScript
  - Inledande support och implementering för frastips.
  - Returnera egenskapssamling med tjänst-JSON för igenkänning
- Windows DLL-filer innehåller nu en versionsresurs.
- Om du skapar en identifierare kan `FromEndpoint` du lägga till parametrar direkt i slutpunktens URL. Med `FromEndpoint` hjälp av kan du inte konfigurera identifieraren via standardkonfigurationsegenskaperna.

**Felkorrigeringar**

- Tomt proxy-användarnamn och proxylösenord hanterades inte korrekt. Om du i den här versionen anger ett proxynamn och proxylösenord till en tom sträng skickas de inte vid anslutning till proxyn.
- SessionId som skapades av SDK:n var inte alltid helt slumpmässigt för vissa &nbsp; språk/miljöer. Slumpmässig generator-initiering har lagts till för att åtgärda problemet.
- Förbättra hanteringen av auktoriseringstoken. Om du vill använda en auktoriseringstoken anger du `SpeechConfig` i och lämnar prenumerationsnyckeln tom. Skapa sedan igen recognizer som vanligt.
- I vissa fall `Connection` släpptes inte objektet korrekt. Det här problemet har åtgärdats.
- JavaScript-exemplet har åtgärdats för att stödja ljudutdata för översättningssyntes även i Safari.

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

Det här är en version med endast JavaScript. Inga funktioner har lagts till. Följande korrigeringar har gjorts:

- Utlös strömmen vid turn.end, inte vid speech.end.
- Åtgärda bugg i ljudpumpen som inte schemaläggde nästa skicka om den aktuella utsänden misslyckades.
- Åtgärda kontinuerlig igenkänning med autentiseringstoken.
- Felkorrigering för olika identifierare/slutpunkter.
- Dokumentationsförbättringar.

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0: 2018-December

**Nya funktioner**

- Python
  - Stöd för Beta-versionen av Python (3.5 och senare) är tillgänglig i den här versionen. Mer information finns här](quickstart-python.md).
- JavaScript
  - Speech SDK för JavaScript har öppen källkod. Källkoden finns på [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  - Vi stöder nu Node.js mer information finns [här.](./get-started-speech-to-text.md)
  - Längdbegränsningen för ljudsessioner har tagits bort. Återanslutning sker automatiskt under omslaget.
- `Connection` Objekt
  - Från `Recognizer` kan du komma åt ett `Connection` -objekt. Med det här objektet kan du uttryckligen initiera tjänstanslutningen och prenumerera på anslutnings- och frånkopplingshändelser.
    (Den här funktionen är ännu inte tillgänglig från JavaScript och Python.)
- Stöd för Ubuntu 18.04.
- Android
  - ProGuard-stöd har aktiverats under APK-generering.

**Förbättringar**

- Förbättringar i den interna trådanvändningen, vilket minskar antalet trådar, lås och mutex.
- Förbättrad felrapportering/information. I flera fall har felmeddelanden inte spridits hela vägen ut.
- Uppdaterade utvecklingsberoenden i JavaScript för att använda uppdaterade moduler.

**Felkorrigeringar**

- Minnesläckor har åtgärdats på grund av en typmatchning i `RecognizeAsync` .
- I vissa fall har undantag läckts.
- Åtgärda minnesläcka i översättningshändelseargument.
- Ett låsningsproblem vid återanslutning i långvariga sessioner har åtgärdats.
- Åtgärdat ett problem som kunde leda till att slutresultatet saknades för misslyckade översättningar.
- C#: Om en åtgärd inte väntades i huvudtråden var det möjligt att identifieraren togs bort innan `async` den asynkrona uppgiften slutfördes.
- Java: Ett problem har åtgärdats som resulterade i en krasch på den virtuella Java-datorn.
- Objective-C: Fast uppräkningsmappning; RecognizedIntent returnerades i stället för `RecognizingIntent` .
- JavaScript: Ställ in standardutdataformatet på "simple" i `SpeechConfig` .
- JavaScript: Ta bort inkonsekvens mellan egenskaper på konfigurationsobjektet i JavaScript och andra språk.

**Exempel**

- Uppdaterade och åtgärdade flera exempel (till exempel utdataröster för översättning osv.).
- Lade Node.js exempel i [exempeldatabasen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**Nya funktioner**

- Stöd för Android x86/x64.
- Proxystöd: I objektet kan du nu anropa en funktion för att ange `SpeechConfig` proxyinformation (värdnamn, port, användarnamn och lösenord). Den här funktionen är ännu inte tillgänglig i iOS.
- Förbättrad felkod och meddelanden. Om en igenkänning returnerade ett fel har detta redan `Reason` angetts (i avbruten händelse) eller `CancellationDetails` (i igenkänningsresultat) till `Error` . Den avbokningshändelsen innehåller nu ytterligare två medlemmar, `ErrorCode` och `ErrorDetails` . Om servern returnerade ytterligare felinformation med det rapporterade felet blir den nu tillgänglig i de nya medlemmarna.

**Förbättringar**

- Ytterligare verifiering har lagts till i konfigurationen av identifieraren och ytterligare felmeddelande har lagts till.
- Förbättrad hantering av lång tystnad mitt i en ljudfil.
- NuGet-paket: för .NET Framework projekt förhindrar det att du skapar med AnyCPU-konfiguration.

**Felkorrigeringar**

- Flera undantag som hittades i identifierare har åtgärdats. Dessutom fångas undantag och konverteras till `Canceled` händelser.
- Åtgärda en minnesläcka i egenskapshanteringen.
- En bugg där en ljudindatafil kan krascha identifieraren har åtgärdats.
- Åtgärdat en bugg där händelser kunde tas emot efter en sessionsstopphändelse.
- Vissa rastillstånd i trådning har åtgärdats.
- Åtgärdat ett iOS-kompatibilitetsproblem som kan leda till en krasch.
- Stabilitetsförbättringar för Stöd för Android-mikrofon.
- Åtgärdat en bugg där en identifierare i JavaScript ignorerar igenkänningsspråket.
- En bugg som förhindrar inställning av `EndpointId` (i vissa fall) i JavaScript har åtgärdats.
- Parameterordningen i AddIntent i JavaScript har ändrats och `AddIntent` JavaScript-signatur saknas.

**Exempel**

- C++ och C#-exempel för användning av pull- och push-dataströmmar har lagts till på [exempeldatabasen](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

Förbättringar av tillförlitlighet och felkorrigeringar:

- Ett potentiellt allvarligt fel har åtgärdats på grund av ett rastillstånd i apparaten för att ta bort igenklysning
- Ett potentiellt allvarligt fel har åtgärdats när ej inlästa egenskaper inträffar.
- Ytterligare fel- och parameterkontroll har lagts till.
- Objective-C: Ett möjligt allvarligt fel som orsakats av namn som åsidosätts i NSString har åtgärdats.
- Objective-C: Justerad synlighet för API
- JavaScript: Åtgärdat angående händelser och deras nyttolaster.
- Dokumentationsförbättringar.

I vår [exempeldatabas](https://aka.ms/csspeech/samples)lades ett nytt exempel för JavaScript till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: version 2018–september

**Nya funktioner**

- Stöd för Objective-C i iOS. Kolla in vår [Objective-C-snabbstart för iOS.](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone)
- Stöd för JavaScript i webbläsare. Kolla in vår [JavaScript-snabbstart.](./get-started-speech-to-text.md)

**Icke-bakåtkompatibla ändringar**

- I den här versionen introduceras ett antal större ändringar.
  Mer [information finns på](https://aka.ms/csspeech/breakingchanges_1_0_0) den här sidan.

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-augusti

**Nya funktioner**

- UWP-appar som skapats med Speech SDK kan nu skicka Certifieringspaket för Windows-program (WACK).
  Kolla in [UWP-snabbstarten.](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp)
- Stöd för .NET Standard 2.0 på Linux (Ubuntu 16.04 x64).
- Experimentell: Stöd för Java 8 i Windows (64-bitars) och Linux (Ubuntu 16.04 x64).
  Ta en titt [på Java Runtime Environment snabbstarten](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Funktionell ändring**

- Exponera ytterligare felinformation om anslutningsfel.

**Icke-bakåtkompatibla ändringar**

- I Java (Android) kräver `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` funktionen inte längre en sökvägsparameter. Nu identifieras sökvägen automatiskt på alla plattformar som stöds.
- Get-accessor för egenskapen i `EndpointUrl` Java och C# har tagits bort.

**Felkorrigeringar**

- I Java implementeras nu resultatet av ljudsyntesen på översättnings recognizer.
- En bugg som kan orsaka inaktiva trådar och ett ökat antal öppna och oanvända socketar har åtgärdats.
- Ett problem har åtgärdats där en långvarig igenkänning kunde avslutas mitt i överföringen.
- Ett rastillstånd vid avstängning av känner igen har åtgärdats.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-juli

**Nya funktioner**

- Stöd för Android-plattformen (API 23: Android 6.0 Marshmallow eller senare). Kolla in [Android-snabbstarten.](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)
- Stöd för .NET Standard 2.0 i Windows. Ta en titt på [snabbstarten för .NET Core.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)
- Experimentell: Stöd för UWP i Windows (version 1709 eller senare).
  - Kolla in [UWP-snabbstarten.](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)
  - Observera att UWP-appar som skapats med Speech SDK ännu inte skickar Certifieringspaket för Windows-program (WACK).
- Stöd för långvarig igenkänning med automatisk återanslutning.

**Funktionella ändringar**

- `StartContinuousRecognitionAsync()` stöder långvarig igenkänning.
- Igenkänningsresultatet innehåller fler fält. De förskjuts från ljudstarten och -varaktigheten (båda i tick) för den identifierade texten och ytterligare värden som representerar igenkänningsstatus, till exempel `InitialSilenceTimeout` och `InitialBabbleTimeout` .
- Stöd för AuthorizationToken för att skapa fabriksinstanser.

**Icke-bakåtkompatibla ändringar**

- `NoMatch`Igenkänningshändelser: Händelsetypen sammanfogades i `Error` händelsen.
- SpeechOutputFormat i C# har bytt namn till `OutputFormat` för att hålla sig i linje med C++.
- Returtypen för vissa metoder i gränssnittet `AudioInputStream` har ändrats något:
  - I Java returnerar `read` metoden nu i stället för `long` `int` .
  - I C# returnerar `Read` metoden nu i stället för `uint` `int` .
  - I C++returnerar `Read` metoderna och nu i stället för `GetFormat` `size_t` `int` .
- C++: Instanser av ljudindataströmmar kan nu bara skickas som en `shared_ptr` .

**Felkorrigeringar**

- Felaktiga returvärden i resultatet har åtgärdats vid `RecognizeAsync()` en tids slut.
- Beroendet av Media Foundation-bibliotek i Windows har tagits bort. SDK:n använder nu Core Audio-API:er.
- Dokumentationskorrigering: En [regionssida har lagts](regions.md) till för att beskriva de regioner som stöds.

**Känt problem**

- Speech SDK för Android rapporterar inte talsyntesresultat för översättning. Det här problemet kommer att åtgärdas i nästa version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-juni

**Funktionella ändringar**

- AudioInputStream

  En identifierare kan nu använda en ström som ljudkälla. Mer information finns i den relaterade [i guiden](how-to-use-audio-input-streams.md).

- Detaljerat utdataformat

  När du skapar ett `SpeechRecognizer` kan du begära eller mata ut `Detailed` `Simple` format. `DetailedSpeechRecognitionResult`innehåller förtroendepoäng, tolkad text, rå lexikal form, normaliserad form och normaliserad form med maskerat svordomar.

**Ändring som inte har ändrats**

- har `SpeechRecognitionResult.Text` ändrats till `SpeechRecognitionResult.RecognizedText` från i C#.

**Felkorrigeringar**

- Åtgärdade ett möjligt återanropsproblem i USP-lagret under avstängningen.
- Om en identifierare förbrukade en ljudindatafil höll den på i filhandtaget längre än nödvändigt.
- Flera dödlägen mellan meddelandepumpen och identifieraren har tagits bort.
- `NoMatch`Uttjäna ett resultat när svaret från tjänsten har time out .
- Media Foundation-biblioteken i Windows läses in fördröjs. Det här biblioteket krävs endast för mikrofonindata.
- Uppladdningshastigheten för ljuddata är begränsad till ungefär dubbelt så mycket som den ursprungliga ljudhastigheten.
- I Windows har C# .NET-sammansättningar nu ett starkt namn.
- Dokumentationskorrigering: `Region` krävs information för att skapa en identifierare.

Fler exempel har lagts till och uppdateras hela tiden. Den senaste uppsättningen exempel finns på [GitHub-lagringsplatsen för Speech SDK-exempel.](https://aka.ms/csspeech/samples)

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-may release

Den här versionen är den första offentliga förhandsversionen av Cognitive Services Speech SDK.
