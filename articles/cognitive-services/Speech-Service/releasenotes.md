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
ms.date: 03/18/2021
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 3b91f4206d7715f199d7b532853471ce179a7a9e
ms.sourcegitcommit: 91361cbe8fff7c866ddc4835251dcbbe2621c055
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "105726846"
---
# <a name="speech-service-release-notes"></a>Viktig information om Speech service

## <a name="speech-sdk-1160-2021-march-release"></a>Speech SDK-1.16.0:2021 – mars release

**Obs!** Speech SDK i Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned den [här](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Kända problem**

**C++/c #/Java**: `DialogServiceConnector` det går inte `CustomCommandsConfig` att använda en för att komma åt ett program med anpassade kommandon och kommer i stället att ha ett anslutnings fel. Detta kan du arbeta runt genom att manuellt lägga till ditt program-ID i begäran med `config.SetServiceProperty("X-CommandsAppId", "your-application-id", ServicePropertyChannel.UriQueryParameter)` . Det förväntade beteendet i `CustomCommandsConfig` kommer att återställas i nästa version.

**Sammanfattning av högdagrar**
- Mindre minne och disk utrymme gör SDK: n mer effektiv – den här gången var på Android.
- Förbättrat stöd för komprimerat ljud för både tal-till-text och text till tal, vilket skapar effektivare klient/server-kommunikation.
- Animerade tecken som talar med text till tal-röster kan nu flytta sina läppar och ansikten naturligt, efter vad de säger.
- Nya funktioner och förbättringar för att göra tal-SDK användbart för fler användnings fall och i fler konfigurationer.
- Flera fel korrigeringar för att åtgärda problem, våra värdefulla kunder har flaggat på GitHub! Tack! Se till att feedback kommer!

#### <a name="new-features"></a>Nya funktioner

- **C++/c #/Java/python**: flyttad till den senaste versionen av gstreamer (1.18.3) för att lägga till stöd för att skriva _ett_ medie format i Windows, Linux och Android. Se dokumentationen [här](/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams). Tidigare stöddes SDK bara för en delmängd av GStreamer-format som stöds. Detta ger dig flexibiliteten att använda ljud formatet som passar ditt användnings fall.
- **C++/c #/Java/Objective-C/python**: stöd har lagts till för att avkoda komprimerad TTS/syntetiskt ljud med SDK. Om du ställer in ljud format för indata till PCM och GStreamer är tillgängligt i systemet begär SDK automatiskt komprimerat ljud från tjänsten för att spara bandbredd och avkoda ljudet på klienten. Detta kan minska den bandbredd som behövs för ditt användnings fall. Du kan ange `SpeechServiceConnection_SynthEnableCompressedAudioTransmission` att `false` den här funktionen ska inaktive ras. Information för [C++](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#propertyid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.propertyid), [Java](/java/api/com.microsoft.cognitiveservices.speech.propertyid), [mål-C](/objectivec/cognitive-services/speech/spxpropertyid), [python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.propertyid?preserve-view=true&view=azure-python).
- **Java Script**: Node.js användare kan nu använda [ `AudioConfig.fromWavFileInput` API: et](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromWavFileInput_File_), så att kunderna kan skicka sökvägen på disken till en WAV-fil till SDK som SDK: n kommer att identifiera. Den här adressen [GitHub problem #252](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/252).
- **C++/c #/Java/Objective-C/python**: metoden har lagts till `GetVoicesAsync()` för TTS för att returnera alla tillgängliga syntes röster program mässigt. På så sätt kan du lista tillgängliga röster i ditt program eller program mässigt välja mellan olika röster. Information för [C++](/cpp/cognitive-services/speech/speechsynthesizer#getvoicesasync), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer#methods), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer#methods), [mål-C](/objectivec/cognitive-services/speech/spxspeechsynthesizer#getvoices)och [python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer?preserve-view=true&view=azure-python#methods).
- **C++/c #/Java/JavaScript/Objective-C/python**: händelsen har lagts till `VisemeReceived` för TTS/tal syntes för att returnera synkron viseme-animering. Med Visemes kan du skapa fler naturliga nyhets-och nyhets assistenter, mer interaktiva spel och tecknande tecken och mer intuitiva språkundervisnings videor. Personer med hörsel försämring kan också hämta ljud visuellt och "Lip-Read" av tal innehåll. Se dokumentationen [här](/azure/cognitive-services/speech-service/how-to-speech-synthesis-viseme).
- **C++/c #/Java/JavaScript/Objective-C/python**: `BookmarkReached` händelsen tillagd för TTS. Du kan ställa in bok märken i SSML för indata och hämta ljud förskjutningarna för varje bok märke. Du kan använda detta i ditt program för att vidta en åtgärd när vissa ord talas av text till tal. Se dokumentationen [här](/azure/cognitive-services/speech-service/speech-synthesis-markup#bookmark-element).
- **Java**: stöd har lagts till för API: er för högtalar igenkänning, så att du kan använda högtalar igenkänning från Java. Information [här](/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer).
- **C++/c #/Java/JavaScript/Objective-C/python**: lade till två nya ljud format för utdata med webm container för TTS (Webm16Khz16BitMonoOpus och Webm24Khz16BitMonoOpus). Detta är ett bättre format för strömning av ljud med Opus-kodeken. Information för [C++](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#speechsynthesisoutputformat), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechsynthesisoutputformat), [Java Script](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat), [mål-C](/objectivec/cognitive-services/speech/spxspeechsynthesisoutputformat), [python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesisoutputformat?preserve-view=true&view=azure-python).
- **C++/c #/Java/python**: stöd har lagts till i Linux för att tillåta att anslutningar lyckas i miljöer där nätverks åtkomst till listor över återkallade certifikat har blockerats. Detta möjliggör scenarier där du väljer att låta klient datorn bara ansluta till Azure Speech service. Se dokumentationen [här](/azure/cognitive-services/speech-service/how-to-configure-openssl-linux).
- **C++/c #/Java**: stöd har lagts till för att hämta röst profil för scenariot för föredrags igenkänning så att en app kan jämföra högtalar data med en befintlig röst profil. Information för [C++](/cpp/cognitive-services/speech/speakerrecognizer), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speakerrecognizer)och [Java](/java/api/com.microsoft.cognitiveservices.speech.speakerrecognizer). Den här adressen [GitHub problem #808](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/808).
- **Mål-C/Swift**: stöd har lagts till för modul Framework med paraply rubrik. På så sätt kan du importera tal-SDK som en modul i iOS/Mac mål-C/Swift-appar. Den här adressen [GitHub problem #452](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/452).
- **Python**: stöd har lagts till för [python 3,9](/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-python) och släppt stöd för python 3,5 per python: s [livs längd för 3,5](https://devguide.python.org/devcycle/#end-of-life-branches).

#### <a name="improvements"></a>Bättre

- **Java**: som en del av vår Multi-release-ansträngning för att minska tal SDK: s minnes användning och disk utrymme är Android-binärfiler nu 3% till 5% mindre.
- **C#**: förbättrad precision, läsbarhet och se även avsnitt i vår C#-referens dokumentation [här](/dotnet/api/microsoft.cognitiveservices.speech) för att förbättra SDK: n i c#.
- **C++/c #/Java/Objective-C/python**: flytta mikrofon-och högtalar kontroll till separat delat bibliotek. Detta tillåter användning av SDK i användnings fall som inte kräver ljud maskin vara, till exempel om du inte behöver en mikrofon eller högtalare för ditt användnings fall på Linux, behöver du inte installera libasound.

#### <a name="bug-fixes"></a>Felkorrigeringar

- **Java Script**: stora WAV-filhuvuden parsas nu korrekt (ökar huvud segmentet till 512 byte). Den här adressen [GitHub problem #962](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/962).
- **Java Script**: korrigerat problem med mikrofonens tids inställning om MIC-strömmen slutar innan den stoppas, kan du åtgärda ett problem med tal igenkänning som inte fungerar i Firefox.
- **Java Script**: nu ska du hantera initierings löfte när webbläsaren tvingar mikrofonen innan turnOn har slutförts.
- **Java Script**: vi har ersatt URL-beroende med URL-parsning. Den här adressen [GitHub problem #264](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/264).
- **Android**: fasta återanrop som inte fungerar när `minifyEnabled` har angetts till true.
- **C++/c #/Java/Objective-C/python**: `TCP_NODELAY` kommer att ställas in korrekt till underliggande socket IO för TTS för att minska svars tiden.
- **C++/c #/Java/python/Objective-C/go**: åtgärdade en tillfällig krasch när tolken har avbrutits precis efter att du påbörjat ett erkännande.
- **C++/c #/Java**: åtgärdat en tillfällig krasch vid destruktion av en högtalar tolk.

#### <a name="samples"></a>Exempel

- **Java Script**: [webb läsar exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/js/browser) kräver inte längre separat nedladdning av JavaScript-biblioteks fil.

## <a name="speech-cli-also-known-as-spx-2021-march-release"></a>Tal-CLI (kallas även SPX): 2021 mars release

**Obs!** kom igång med Azure Speech service Command Line Interface (CLI) [här](/azure/cognitive-services/speech-service/spx-basics). Med CLI kan du använda Azure Speech service utan att skriva någon kod.

#### <a name="new-features"></a>Nya funktioner

- Kommandot har lagts till `spx intent` för avsikts igenkänning, ersättning `spx recognize intent` .
- Identifiera och avsikt kan nu använda Azure Functions för att beräkna ord fel frekvensen med hjälp av `spx recognize --wer url <URL>` .
- Identifiera kan nu resultera i resultat som VTT-filer med hjälp av `spx recognize --output vtt file <FILENAME>` .
- Känslig viktig information döljs nu i fel sökning/utförliga utdata.
- URL-kontroll och fel meddelande för innehålls fält har lagts till i skapa batch-avskrifter.

**COVID – 19 förkortad-testning**:

Eftersom de pågående Pandemic fortfarande kräver att våra tekniker arbetar hemifrån, har Pandemic manuella verifierings skript avsevärt minskat. Vi testar på färre enheter med färre konfigurationer och sannolikheten för att miljöbaserade fel som är försenade kan ökas. Vi är fortfarande rigoröst validerade med en stor uppsättning Automation. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!



## <a name="speech-sdk-1150-2021-january-release"></a>1.15.0 för tal-SDK: 2021 – januari utgåva

**Obs!** Speech SDK i Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned den [här](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Sammanfattning av högdagrar**
- Mindre minne och disk utrymme gör SDK: n mer effektiv.
- Högre åter givning-utdataformat är tillgängliga för anpassad neurala Voice Private Preview.
- Avsikts igenkänning kan nu få tillbaka mer än det främsta syftet, vilket ger dig möjlighet att göra en separat utvärdering av kundens avsikt.
- Röst assistenten eller roboten är nu enklare att konfigurera, och du kan göra så att den slutar lyssna omedelbart och du får bättre kontroll över hur det svarar på fel.
- Förbättrad på enhets prestanda genom att göra komprimering valfritt.
- Använd talet SDK i Windows ARM/ARM64.
- Förbättrad fel sökning på låg nivå.
- Funktionen för uttal av uttal är nu allmänt tillgänglig.
- Flera fel korrigeringar för att åtgärda problem, våra värdefulla kunder har flaggat på GitHub! Tack! Se till att feedback kommer!

**Bättre**
- Talet SDK är nu mer effektivt och lätt. Vi har startat en multi-release-ansträngning för att minska tal SDK: s minnes användning och disk utrymme. Som ett första steg gjorde vi betydande fil storleks minskning i delade bibliotek på de flesta plattformar. Jämfört med 1,14-versionen:
  - 64-bitars UWP-kompatibla Windows-bibliotek är cirka 30% mindre.
  - 32-bitars Windows-bibliotek kan inte se storleks förbättringar ännu.
  - Linux-bibliotek är 20-25% mindre.
  - Android-bibliotek är 3-5% mindre.

**Nya funktioner**
- **Alla**: nya 48KHz-utdataformat som är tillgängliga för den privata förhands granskningen av anpassad neurala röst via tal syntes API: t för: Audio48Khz192KBitRateMonoMp3, Audio-48KHz-192kbitrate-mono-MP3, Audio48Khz96KBitRateMonoMp3, ljud-48KHz-96kbitrate-mono-MP3, Raw48Khz16BitMonoPcm, RAW-48KHz-bitarsläge-mono-PCM, Riff48Khz16BitMonoPcm, riff-mono-PCM.
- **Alla**: anpassad röst är också enklare att använda. Stöd har lagts till för att ställa in anpassad röst via `EndpointId` ([C++](/cpp/cognitive-services/speech/speechconfig#setendpointid), [C#](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.endpointid#Microsoft_CognitiveServices_Speech_SpeechConfig_EndpointId), [Java](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setendpointid#com_microsoft_cognitiveservices_speech_SpeechConfig_setEndpointId_String_), [Java Script](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#endpointId), [mål-C](/objectivec/cognitive-services/speech/spxspeechconfiguration#endpointid), [python](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#endpoint-id)). Före den här ändringen behövde anpassade röst användare ange slut punkts-URL: en via `FromEndpoint` metoden. Nu kan kunder använda `FromSubscription` metoden precis som offentliga röster och sedan ange distributions-ID: t genom att ange `EndpointId` . Detta fören klar installationen av anpassade röster. 
- **C++/c #/Java/Objective-C/python**: Hämta fler än det främsta avsikts stället från `IntentRecognizer` . Nu har det stöd för att konfigurera JSON-resultatet som innehåller alla avsikter och inte bara den översta bedömnings `LanguageUnderstandingModel FromEndpoint` metoden via metod genom att använda `verbose=true` URI-parameter. Den här adressen [GitHub problem #880](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/880). Se uppdaterad dokumentation [här](./quickstarts/intent-recognition.md#add-a-languageunderstandingmodel-and-intents).
- **C++/c #/Java**: gör så att röst assistenten eller roboten slutar lyssna. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector)) har nu en `StopListeningAsync()` metod att följa `ListenOnceAsync()` . Detta kommer omedelbart att stoppa ljud fångsten och vänta på ett resultat, vilket gör det perfekt för användning med knappen "stoppa nu" – Tryck på scenarier.
- **C++/c #/Java/JavaScript**: gör så att röst assistenten eller roboten reagerar bättre på underliggande systemfel. `DialogServiceConnector` ([C++](/cpp/cognitive-services/speech/dialog-dialogserviceconnector), [C#](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector), [Java Script](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector)) har nu en ny `TurnStatusReceived` händelse hanterare. Dessa valfria händelser motsvarar alla [`ITurnContext`](/dotnet/api/microsoft.bot.builder.iturncontext) lösningar på roboten och rapporterar inleder körnings fel när de inträffar, t. ex. på grund av ett ohanterat undantag, tids gräns eller nätverks minskning mellan direkt linje tal och bot. `TurnStatusReceived` gör det enklare att svara på fel tillstånd. Om en robot till exempel tar för lång tid på en databas fråga i Server delen (t. ex. genom att en produkt genomsöks), `TurnStatusReceived` kan klienten känna igen med "Tyvärr, jag fick inte det, kan du försöka igen" eller något liknande.
- **C++/c #**: Använd tal-SDK: n på fler plattformar. [Talet SDK NuGet-paketet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech) har nu stöd för Windows arm/arm64 Desktop Native-binärfiler (UWP stöds redan) för att göra tal-SDK mer användbar på fler dator typer.
- **Java**: [`DialogServiceConnector`](/java/api/com.microsoft.cognitiveservices.speech.dialog.dialogserviceconnector) har nu en `setSpeechActivityTemplate()` metod som oavsiktligt uteslöts från språket tidigare. Detta motsvarar att ställa in `Conversation_Speech_Activity_Template` egenskapen och begära att alla framtida robots Ramverks aktiviteter som kommer från den direkta rad igenkännings tjänsten sammanfogar det tillhandahållna innehållet till sina JSON-nyttolaster.
- **Java**: förbättrad fel sökning på låg nivå. [`Connection`](/java/api/com.microsoft.cognitiveservices.speech.connection)Klassen har nu en `MessageReceived` händelse, som liknar andra program språk (C++, C#). Den här händelsen ger åtkomst på låg nivå till inkommande data från tjänsten och kan vara användbar för diagnostik och fel sökning.
- **Java Script**: enklare installation för röst assistenter och robotar via [`BotFrameworkConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/botframeworkconfig) , som nu har `fromHost()` och är `fromEndpoint()` fabriks metoder som fören klar användningen av anpassade tjänst platser jämfört med att ange egenskaper manuellt. Vi har också standardiserad specifikation för `botId` att använda en icke-standardrobot över konfigurations faktorn.
- **Java Script**: förbättrad på enhets prestanda genom tillagd sträng kontroll egenskap för WebSocket-komprimering. Av prestanda skäl har vi inaktiverat WebSocket-komprimering som standard. Detta kan återaktiveras för scenarier med låg bandbredd. Mer information finns [här](/javascript/api/microsoft-cognitiveservices-speech-sdk/propertyid). Den här adressen [GitHub problem #242](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/242).
- **Java Script**: stöd för uttal-utvärdering har lagts till för att möjliggöra utvärdering av tal uttal. Se snabb starten [här](./how-to-pronunciation-assessment.md?pivots=programming-language-javascript).

**Fel korrigeringar**
- **Alla** (förutom java script): fast en regression i version 1,14, där för mycket minne allokerades av identifieraren.
- **C++**: åtgärdat ett skräp insamlings problem med `DialogServiceConnector` , adressering av [GitHub-problem #794](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/794).
- **C#**: Åtgärdade ett problem med tråd avstängning som orsakade objekt som skulle blockeras för ungefär en sekund när den togs bort.
- **C++/c #/Java**: åtgärdat ett undantag som hindrar ett program från att ställa in talindata-token eller aktivitets mal len mer än en gång i en `DialogServiceConnector` .
- **C++/c #/Java**: åtgärdade en tolk krasch på grund av ett tävlings tillstånd i Teardown.
- **Java Script**: har [`DialogServiceConnector`](/javascript/api/microsoft-cognitiveservices-speech-sdk/dialogserviceconnector) inte tidigare `botId` angett den valfria parametern som angavs i `BotFrameworkConfig` fabrikerna. Detta gör det nödvändigt att ange `botId` frågesträngs parametern manuellt för att använda en icke-standardrobot. Felet har korrigerats och `botId` de värden som angavs för `BotFrameworkConfig` fabrikerna kommer att behållas och användas, inklusive nya `fromHost()` och `fromEndpoint()` tillägg. Detta gäller även för- `applicationId` parametern för `CustomCommandsConfig` .
- **Java Script**: åtgärds #881 med fast [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/881), vilket tillåter åter användning av identifierings objekt.
- **Java Script**: åtgärdat ett problem där SKD skickades `speech.config` flera gånger i en TTS-session, slöseri med bandbredd.
- **Java Script**: förenklade fel hantering på mikrofon tillstånd, vilket gör att fler beskrivande meddelanden kan bubblas när användaren inte har tillåtit mikrofon indata i webbläsaren.
- **Java Script**: Fixed [GitHub Issue #249](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/249) där typ fel i `ConversationTranslator` och `ConversationTranscriber` orsakade ett kompileringsfel för typescript-användare.
- **Mål-C**: Åtgärdade ett problem där gstreamer-build misslyckades för iOS på Xcode 11,4, adressering [GitHub problem #911](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/911).
- **Python**: [problem #870](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/870)för fast GitHub, ta bort "DeprecationWarning: modulen IMP är föråldrad i importlib".

**Exempel**
- [Från-fil-exempel för JavaScript-webbläsare](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/quickstart/javascript/browser/from-file/index.html) använder nu filer för tal igenkänning. Den här adressen [GitHub problem #884](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/884).

## <a name="speech-cli-also-known-as-spx-2021-january-release"></a>Tal-CLI (även kallat SPX): 2021 – januari utgåva

**Nya funktioner**
- Tal-CLI är nu tillgängligt som ett [NuGet-paket](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech.CLI/) och kan installeras via .net CLI som ett globalt .net-verktyg som du kan anropa från skal-/kommando raden.
- [Mallen för Custom Speech DevOps-lagrings platsen](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) har uppdaterats för att använda tal-CLI för Custom Speech arbets flöden.

**COVID-19 förkortad-testning**: eftersom den pågående Pandemic fortsätter att kräva att våra tekniker arbetar hemifrån, är de manuella verifierings skripten i förväg minskat avsevärt. Vi testar på färre enheter med färre konfigurationer och sannolikheten för att miljöbaserade fel som är försenade kan ökas. Vi är fortfarande rigoröst validerade med en stor uppsättning Automation. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!

## <a name="text-to-speech-2020-december-release"></a>Text till tal 2020 – december version

**Nya neurala-röster i GA och Preview**

Lanserade 51 nya röster för totalt 129 neurala-röster över 54-språk/-språk:

- **46 nya röster i ga-nationella inställningar**: Shakir i `ar-EG` arabiska (Egypten), hamed i `ar-SA` arabiska (Saudiarabien), Borislav i `bg-BG` bulgariska (Bulgarien), Joana i `ca-ES` katalanska (Spanien). Antonin i `cs-CZ` tjeckiska (Tjeckien), Jeppe i `da-DK` danska (Danmark), Jonas på `de-AT` tyska (Österrike), Jan på `de-CH` tyska (Schweiz), Nestoras i `el-GR` grekiska (Grekland), Liam på `en-CA` engelska (Kanada), Connor på `en-IE` engelska (Irland), Madhur i `en-IN` hindi (Indien), Mohan i `en-IN` Telugu (Indien), Prabhat på `en-IN` engelska (Indien). Valluvar i `en-IN` Tamil (Indien), Enric i `es-ES` katalanska (Spanien), kert i `et-EE` estniska (Estland), Harri i `fi-FI` finska (Finland), Selma i `fi-FI` finska (Finland), Fabrice på `fr-CH` franska (Schweiz), Colm i `ga-IE` irländska (Irland), Avri i `he-IL` hebreiska (Israel), Srecko i `hr-HR` kroatiska (Kroatien), Tamas i `hu-HU` ungerska (Ungern), Gadis i `id-ID` Indonesiska (Indonesien), Leonas i `lt-LT` Litauiska (Litauen), Nils i `lv-LV` lettiska (Lettland), Osman i `ms-MY` malajiska (Malaysia), Joseph i `mt-MT` maltesiskhet (Malta) , Finsk i `nb-NO` norska, bokmål (Norge), Pernille i `nb-NO` norska, bokmål (Norge), Fenna i `nl-NL` nederländska (Nederländerna), Maarten i `nl-NL` nederländska (Nederländerna), Agnieszka i `pl-PL` polska (Polen), Marek i `pl-PL` polska (Polen), Duarte i `pt-BR` portugisiska (Brasilien), Raquel i `pt-PT` portugisiska (Potugal), Emil i `ro-RO` rumänska (Rumänien), Dmitry i `ru-RU` ryska (Ryssland). Svetlana i Ryssland `ru-RU` (Ryssland), Lukas i `sk-SK` slovakiska (Slovakien), Rok i `sl-SI` Slovenska (Slovenien), Mattias i `sv-SE` svenska (Sverige), Sofie i `sv-SE` svenska (Sverige), Niwat i `th-TH` thailändska (Thailand), Ahmet i `tr-TR` turkiska (Turkiet), NamMinh i `vi-VN` vietnamesiska (Vietnam), HsiaoChen i `zh-TW` taiwanesiska mandariner (Taiwan), YunJhe i `zh-TW` taiwanesiska mandariner (Taiwan), HiuMaan i `zh-HK` kinesiska kantonesiska (Hongkong), WanLung i `zh-HK` kinesiska kantonesiska (Hongkong).

- **fem nya röster i för hands versioner**: kert i `et-EE` estniska (Estland), Colm i `ga-IE` irländska (Irland), Nils i `lv-LV` lettiska (Lettland), Leonas i `lt-LT` Litauiska (Litauen), Joseph i `mt-MT` maltesiskhet (Malta).

I den här versionen har vi nu stöd för totalt 129 neurala-röster över 54-språk/-språk. Dessutom finns över 70 standard röster i 49-språk/-språk. Gå till [språk stöd](language-support.md#text-to-speech) för den fullständiga listan.

**Uppdateringar för skapande av ljud innehåll**
- Förbättrat röst markerings gränssnitt med röst kategorier och detaljerade röst beskrivningar. 
- Aktiverade intonation-justering för alla neurala-röster på olika språk.
- Automatisera UI-localizaiton baserat på språket i webbläsaren.
- Aktiverade `StyleDegree` kontroller för alla `zh-CN` neurala-röster.
Gå till [verktyget för att skapa ljud innehåll](https://speech.microsoft.com/audiocontentcreation) och ta en titt på de nya funktionerna. 

**Uppdateringar för zh-CN-röster**
- Uppdaterade alla `zh-CN` neurala-röster för att stödja engelska inläsning.
- Aktiverade alla `zh-CN` neurala-röster för att stödja intonation-justering. Verktyget för att skapa SSML eller ljud innehåll kan användas för att justera för bästa möjliga intonation.
- Uppdaterade alla `zh-CN` neurala-röster med flera stilar för att stödja `StyleDegree` kontrollen. Känslo-intensitet (Soft eller Strong) är justerbar.
- Uppdaterat `zh-CN-YunyeNeural` med stöd för flera stilar som kan utföra olika känslor.

## <a name="text-to-speech-2020-november-release"></a>Text till tal 2020 – november release

**Nya språk och röster i för hands versionen**
- **Fem nya röster och språk** introduceras i neurala TTS-portföljen. De är: Grace i maltesiska (Malta), på i litauiska (Litauen), Anu i estniska (Estland), Orla i irländska (Irland) och everita i lettiska (Lettland).
- **Fem nya `zh-CN` röster med flera stilar och roller stöder**: Xiaohan, Xiaomo, Xiaorui, Xiaoxuan och Yunxi.

> Dessa röster är tillgängliga i en offentlig för hands version i tre Azure-regioner: öster, Sydostasien och WestEurope.

**Neurala TTS-behållare GA**
- Med neurala TTS-behållare kan utvecklare köra tal syntes med de mest naturliga digitala rösterna i sin egen miljö för specifika säkerhets-och data styrnings krav. Se [hur du installerar tal behållare](speech-container-howto.md). 

**Nya funktioner**
- **Anpassad röst**: enabed-användare kan kopiera en röst modell från en region till en annan. slut punkts avstängning som stöds och återupptas. Gå till [portalen](https://speech.microsoft.com/customvoice) här.
- Stöd för [SSML](speech-synthesis-markup.md#add-silence) . 
- Förbättringar av tal röst kvalitet i tal: förbättrad precision på ord nivå i NB-nej. Minskat 53% uttal-fel.

> Läs mer på [den här Tech-bloggen](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-previews-five-new-languages-with/ba-p/1907604).

## <a name="text-to-speech-2020-october-release"></a>Text till tal 2020 – utgåva från oktober

**Nya funktioner**
- Jenny har stöd för ett nytt `newscast` format. Se [hur du använder tal formatet i SSML](speech-synthesis-markup.md#adjust-speaking-styles).
- **Neurala-röster som uppgraderats till HiFiNet vocoder, med högre ljud återgivning och snabbare syntes hastighet**. Detta fördelar kunder vars scenario förlitar sig på Hi-Fi-ljud eller långa interaktioner, inklusive video dubbing, ljud böcker eller utbildningsmaterial online. [Läs mer om artikeln och hör röst exemplen på vår Tech community-blogg](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860)
- **[Anpassad röst](https://speech.microsoft.com/customvoice)  &  [ljud innehålls skapande Studio](https://speech.microsoft.com/audiocontentcreation) lokaliseras till 17 språk**. Användare kan enkelt byta användar gränssnitt till ett lokalt språk för en mer användarvänlig upplevelse.   
- **Framställning av ljud innehåll**: lade till stil grad kontroll för XiaoxiaoNeural; Förfinat funktionen för anpassad brytning som innehåller stegvisa brytningar av 50ms. 

**Vanliga förbättringar av tal röst kvalitet**
- Bättre uttal av ord nivå i `pl-PL` (fel hastighets minskning: 51%) och `fi-FI` (minskning av fel frekvens: 58%)
- Förbättrad ord `ja-JP` läsning för ord listans scenario. Minskat uttal-fel med 80%.
- `zh-CN-XiaoxiaoNeural`: Förbättrad sentiment/CustomerService/newscast/Cheerful/Angry format röst kvalitet.
- `zh-CN`: Förbättrad Erhua-uttal och lätt ton och raffinerat utrymme prosody, vilket avsevärt förbättrar intelligibility. 

## <a name="speech-sdk-1140-2020-october-release"></a>Speech SDK-1.14.0:2020 – oktober utgåva

**Obs!** Speech SDK i Windows är beroende av den delade Microsoft Visual C++ Redistributable för Visual Studio 2015, 2017 och 2019. Ladda ned den [här](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads).

**Nya funktioner**
- **Linux**: stöd har lagts till för Debian 10-och Ubuntu 20,04-LTS.
- **Python/mål-C**: stöd har lagts till för `KeywordRecognizer` API: et. Dokumentationen kommer att vara [här](./custom-keyword-basics.md).
- **C++/Java/C #**: stöd har lagts till för att ange `HttpHeader` nyckel/värde via `ServicePropertyChannel::HttpHeader` .
- **Java Script**: stöd har lagts till för `ConversationTranscriber` API: et. Läs dokumentationen [här](./how-to-use-conversation-transcription.md?pivots=programming-language-javascript). 
- **C++/c #**: ny metod har lagts till `AudioDataStream FromWavFileInput` (läses. WAV-filer) [här (C++)](/cpp/cognitive-services/speech/audiodatastream) och [här (C#)](/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream).
-  **C++/c #/Java/python/Objective-C/Swift**: en metod har lagts `stopSpeakingAsync()` till för att stoppa text till tal-Sammanfattning. Läs referens dokumentationen [här (C++)](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace), [här (C#](/dotnet/api/microsoft.cognitiveservices.speech)), här ( [Java)](/java/api/com.microsoft.cognitiveservices.speech), här [(](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech)) och [här (mål-C/SWIFT)](/objectivec/cognitive-services/speech/).
- **C#, C++, Java**: en funktion har lagts `FromDialogServiceConnector()` till i `Connection` klassen som kan användas för att övervaka anslutnings-och från kopplings händelser för `DialogServiceConnector` . Läs referens dokumentationen [här (C#)](/dotnet/api/microsoft.cognitiveservices.speech.connection), [här (C++)](/cpp/cognitive-services/speech/connection)och [här (Java)](/java/api/com.microsoft.cognitiveservices.speech.connection).
- **C++/c #/Java/python/Objective-C/Swift**: stöd har lagts till för uttal av uttal, vilket utvärderar tal uttal och ger talare feedback om precisionen och Fluency av talade ljud. Läs dokumentationen [här](how-to-pronunciation-assessment.md).

**Bryta ändring**
- **Java Script**: PullAudioOutputStream. Read () har en retur typ ändring från ett internt löfte till ett ursprungligt JavaScript-löfte.

**Fel korrigeringar**
- **Alla**: fast 1,13 regression i `SetServiceProperty` där värden med vissa specialtecken ignorerades.
- **C#**: fasta Windows-konsol-exempel i Visual Studio 2019 som inte kan hitta inbyggda DLL-filer.
- **C#**: fast krasch med minnes hantering om Stream används som `KeywordRecognizer` indata.
- **ObjectiveC/Swift**: en fast krasch med minnes hantering om Stream används som identifierings data.
- **Windows**: åtgärdat samexistens med BT HFP/A2DP på UWP.
- **Java Script**: fast mappning av sessions-ID: n för att förbättra loggning och stöd i interna fel söknings-/tjänst korrelationer.
- **Java Script**: korrigering för `DialogServiceConnector` inaktive ring av `ListenOnce` anrop efter det första anropet görs.
- **Java Script**: ett fast problem där resultatet av resultat skulle ha varit "enkelt".
- **Java Script**: problem med fast löpande igenkänning i Safari på MacOS.
- **Java Script**: minskning av CPU-belastning för data flödes scenario med hög begäran.
- **Java Script**: Tillåt åtkomst till information om resultatet av röst profil registreringen.
- **Java Script**: korrigering för kontinuerlig igenkänning i har lagts till `IntentRecognizer` .
- **C++/c #/Java/python/Swift/ObjectiveC**: fast felaktig URL för australiaeast och centrala i `IntentRecognizer` .
- **C++/c #**: läggs till `VoiceProfileType` som ett argument när du skapar ett `VoiceProfile` objekt.
- **C++/c #/Java/python/Swift/ObjectiveC**: fast potential `SPX_INVALID_ARG` vid försök att läsa `AudioDataStream` från en viss position.
- **IOS**: fast krasch med tal igenkänning på Unity

**Exempel**
- **ObjectiveC**: exempel på nyckelords igenkänning har lagts till [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/speech-samples).
- **C#/JavaScript**: snabb start har lagts till för konversations avskrift [här (c#)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/conversation-transcription) och [här (Java Script)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/conversation-transcription).
- **C++/c #/Java/python/Swift/ObjectiveC**: exemplet har lagts till för uttal-utvärdering [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples)
- **Xamarin**: snabb start har uppdaterats till den senaste Visual Studio-mallen [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/xamarin).

**Känt problem**
- DigiCert global root G2-certifikat stöds inte som standard i HoloLens 2 och Android 4,4 (KitKat) och måste läggas till i systemet för att det ska gå att använda tal-SDK. Certifikatet kommer att läggas till i HoloLens 2 OS-avbildningar inom en snar framtid. Android 4,4-kunder måste lägga till det uppdaterade certifikatet i systemet.

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!

## <a name="speech-cli-also-known-as-spx-2020-october-release"></a>Tal-CLI (även kallat SPX): 2020 – oktober utgåva
SPX är kommando rads gränssnittet som används för att använda Azure Speech service utan att skriva kod. Hämta den senaste versionen [här](./spx-basics.md). <br>

**Nya funktioner**
- `spx csr dataset upload --kind audio|language|acoustic` – skapa data uppsättningar från lokala data, inte bara från URL: er.
- `spx csr evaluation create|status|list|update|delete` – Jämför nya modeller med bas linje sanningen/andra modeller.
- `spx * list` – stöder icke-växlad upplevelse (kräver inte--översta X--Skip X).
- `spx * --http header A=B` – stöder anpassade rubriker (har lagts till för Office för anpassad autentisering). 
- `spx help` – förbättrad text-och bakgrunds textfärgad kod (blå).

## <a name="text-to-speech-2020-september-release"></a>Text till tal 2020 – september version

### <a name="new-features"></a>Nya funktioner

* **Neurala TTS** 
    * **Utökad för att stödja 18 nya språk/nationella inställningar.** De är bulgariska, tjeckiska, tyska (Österrike), tyska (Schweiz), grekiska, engelska (Irland), franska (Schweiz), hebreiska, kroatiska, ungerska, indonesiska, malajiska, rumänska, slovakiska, slovenska, Tamil, Telugu och vietnamesiska. 
    * **Släppt 14 nya röster för att utöka sorten på de befintliga språken.** Se [hela språk och röst lista](language-support.md#neural-voices).
    * **Nya tal format för `en-US` och `zh-CN` röster.** Jenny, den nya rösten på engelska (US), stöder chattrobot, kund tjänst och assistent. 10 nya tal format är tillgängliga med vår zh-CN-röst, XiaoXiao. Dessutom stöder XiaoXiao neurala-röst `StyleDegree` justering. Se [hur du använder tal formatet i SSML](speech-synthesis-markup.md#adjust-speaking-styles).

* **Behållare: neurala TTS container lanserad i offentlig för hands version med 16 röster tillgängliga på 14 språk.** Läs mer om [hur du distribuerar tal behållare för NEURALA TTS](speech-container-howto.md)  

Läs [hela tillkänna givandet av TTS-uppdateringarna för antändning 2020](https://techcommunity.microsoft.com/t5/azure-ai/ignite-2020-neural-tts-updates-new-language-support-more-voices/ba-p/1698544) 

## <a name="text-to-speech-2020-august-release"></a>Text till tal 2020 – augusti-utgåva

### <a name="new-features"></a>Nya funktioner

* **NEURALA TTS: ny tal format för `en-US` Aria röst**. AriaNeural kan likna en nyhets Caster när de läser nyheter. Formatet "newscast-formell" låter mer allvarligt, medan formatet "newscast-vardaglig" är mer avslappnad och informell. Se [hur du använder tal formatet i SSML](speech-synthesis-markup.md).

* **Anpassad röst: en ny funktion släpps automatiskt för att kontrol lera kvaliteten på tränings data**. När du överför dina data kommer systemet att undersöka olika aspekter av dina ljud-och avskrifts data och automatiskt korrigera eller filtrera problem för att förbättra kvaliteten på röst modellen. Detta täcker Ljudets volym, brus nivån, tal igenkännings precisionen, justeringen av tal med normaliserad text, tystnad i ljudet, förutom ljud-och skript formatet. 

* **Skapa ljud innehåll: en uppsättning nya funktioner för att aktivera mer kraftfulla funktioner för röst justering och ljud hantering**.

    * Uttal: funktionen för inuttals justering uppdateras till den senaste fonem-uppsättningen. Du kan välja rätt fonem-element från biblioteket och förfina uttal av de ord som du har valt. 

    * Ladda ned: funktionen "Ladda ned"/"Exportera" har förbättrats för att ge stöd för att generera ljud per stycke. Du kan redigera innehållet i samma fil-SSML, samtidigt som du genererar flera ljud resultat. Fil strukturen för "Ladda ned" är också raffinerad. Nu kan du enkelt hämta alla ljudfiler i en mapp. 

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
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>Felkorrigeringar

* Ett antal buggar har åtgärd ATS med verktyget för att skapa ljud innehåll 
    * Åtgärdat problem med automatisk uppdatering. 
    * Korrigerade problem med röst format i zh-CN i den Asien, sydöstra regionen.
    * Fast stabilitets problem, inklusive ett export fel med taggen Break, och fel i interpunktion.

## <a name="new-speech-to-text-locales-2020-august-release"></a>Nya språk som är tal till text: 2020 – augusti version
Inläsning av tal till text 26 nya språk i augusti: 2 europeiska språk `cs-CZ` och `hu-HU` , 5 engelska lokala och 19 spanska nationella inställningar som avser de flesta länder i södra Amerika. Nedan visas en lista över de nya språken. Se den fullständiga språk listan [här](./language-support.md).

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
- **C#**: stöd har lagts till för asynkron konversations avskrift. Se dokumentationen [här](./how-to-async-conversation-transcription.md).  
- **Java Script**: ytterligare Talarigenkänning stöd för både [webbläsare](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) och [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition).
- **Java Script**: stöd har lagts till för automatisk språk identifiering/språk-ID. Se dokumentationen [här](./how-to-automatic-language-detection.md?pivots=programming-language-javascript).
- **Mål-C**: stöd har lagts till för konversations-och [konversations avskrifter](./conversation-transcription.md)i [flera enheter](./multi-device-conversation.md) . 
- **Python**: stöd för komprimerad ljud för python i Windows och Linux. Se dokumentationen [här](./how-to-use-codec-compressed-audio-input-streams.md). 

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
- **iOS**: åtgärdat ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702) med publicering av tal SDK-baserade appar i iOS App Store.

**Exempel**
- **C++**: exempel kod har lagts till för Talarigenkänning [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp).

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!

## <a name="text-to-speech-2020-july-release"></a>Text till tal 2020 – juli version

### <a name="new-features"></a>Nya funktioner

* **NEURALA TTS, 15 nya neurala-röster**: de nya rösterna som läggs till i neurala TTS-portföljen är Salma i `ar-EG` arabiska (Egypten), Zariyah i `ar-SA` arabiska (Saudiarabien), Alba i `ca-ES` katalanska (Spanien), Christel i `da-DK` danska (Danmark), Neerja på `es-IN` engelska (Indien). Noora i `fi-FI` finska (Finland), swara i `hi-IN` hindi (Indien), Colette i `nl-NL` nederländska (Nederländerna), Zofia i `pl-PL` polska (Polen), Fernanda i `pt-PT` portugisiska (Portugal), Dariya i `ru-RU` ryska (Ryssland), Hillevi i `sv-SE` svenska (Sverige), Achara i `th-TH` thailändska (Thailand), HiuGaai på `zh-HK` kinesiska (kantonesiska, traditionell) och HsiaoYu i `zh-TW` kinesiska (Taiwan-mandariner). Kontrol lera alla [språk som stöds](./language-support.md#neural-voices).  

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

* Bättre uttal av ord nivå på fem språk.

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

    * Stöd för negativ valuta (t. ex. "-325 &euro; ") i följande språk:,,,,, `en-US` `en-GB` `fr-FR` `it-IT` `en-AU` `en-CA` .

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
-   **C \# , C++**: Talarigenkänning för hands version: den här funktionen gör det möjligt att identifiera högtalare (Vem talar?) och talad talare (är den talare som de anspråks vara?). Börja med en [Översikt](./speaker-recognition-overview.md), läs [artikeln Talarigenkänning grunder](./get-started-speaker-recognition.md)eller [referens dokument för API](/rest/api/speakerrecognition/).

**Fel korrigeringar**
-   **C \# , C++**: fast mikrofon inspelning fungerade inte i 1,12 i högtalar igenkänning.
-   **Java Script**: korrigeringar för text till tal i Firefox och Safari på MacOS och iOS.
-   Korrigering för Windows Application Verifier åtkomst överträdelse vid konversations avskrift vid användning av åtta kanal strömmar.
-   Korrigering för åtkomst överträdelse för Windows Application Verifier kraschar vid konversation med flera enheter.

**Exempel**
-   **C#**: [kod exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition) för högtalar igenkänning.
-   **C++**: [kod exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition) för högtalar igenkänning.
-   **Java**: [kod exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition) för avsikts igenkänning på Android. 

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK-1.12.0:2020 – maj-version
**Tal-CLI (även känt som SPX)**
- **SPX** är ett nytt kommando rads verktyg som du kan använda för att utföra igenkänning, syntes, översättning, batch-avskrift och anpassad tal hantering från kommando raden. Använd den för att testa tal tjänsten eller för att skapa skript för de tal tjänst uppgifter som du behöver utföra. Hämta verktyget och Läs dokumentationen [här](./spx-overview.md).

**Nya funktioner**
- **Go**: nytt go-språkstöd för [tal igenkänning](./get-started-speech-to-text.md?pivots=programming-language-go) och [anpassad röst assistent](./quickstarts/voice-assistants.md?pivots=programming-language-go). Konfigurera din utvecklings miljö [här](./quickstarts/setup-platform.md?pivots=programming-language-go). Exempel kod finns i avsnittet exempel nedan. 
- **Java Script**: stöd för webb läsar stöd för text till tal har lagts till. Se dokumentationen [här](./get-started-text-to-speech.md?pivots=programming-language-JavaScript).
- **C++, C#, Java**: nytt `KeywordRecognizer` objekt och API: er som stöds på Windows-, Android-, Linux & iOS-plattformar. Läs dokumentationen [här](./custom-keyword-overview.md). Exempel kod finns i avsnittet exempel nedan. 
- **Java**: fler konversationer har lagts till med stöd för översättning. Se referens dokumentet [här](/java/api/com.microsoft.cognitiveservices.speech.transcription).

**Förbättringar & optimeringar**
- **Java Script**: optimerad webbläsare mikrofon implementeringen förbättrar tal igenkännings precisionen.
- **Java**: omfaktorde bindningar med hjälp av direkt jni-implementering utan swig. Den här ändringen minskar med 10X storleks bindningarna för alla Java-paket som används för Windows, Android, Linux och Mac och underlättar ytterligare utveckling av tal SDK Java-implementeringen.
- **Linux**: uppdaterad support [dokumentation](./speech-sdk.md?tabs=linux) med de senaste RHEL 7-speciella anteckningarna.
- Förbättrad anslutnings logik för att försöka ansluta flera gånger när tjänst-och nätverks fel uppstår.
- Uppdaterad start sidan för [Portal.Azure.com](https://portal.azure.com) -tal för att hjälpa utvecklare att ta nästa steg i Azure-tal resan.

**Fel korrigeringar**
- **C#, Java**: Åtgärdade ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587) med att läsa in SDK-bibliotek på Linux arm (både 32-bitars och 64-bitars).
- **C#**: fast explicit avyttring av egna referenser för TranslationRecognizer-, IntentRecognizer-och Connection-objekt.
- **C#**: hantering av livs längd för fasta ljud värden för ConversationTranscriber-objekt.
- Ett problem har åtgärd ATS där `IntentRecognizer` resultat orsaken inte angavs korrekt vid tolkning av avsikter från enkla fraser.
- Ett problem har åtgärd ATS där `SpeechRecognitionEventArgs` resultat förskjutning inte angavs korrekt.
- Ett tävlings villkor har åtgärd ATS där SDK försökte skicka ett nätverks meddelande innan WebSocket-anslutningen öppnades. Har reproducerats för `TranslationRecognizer` när deltagarna lades till.
- Fasta minnes läckor i nyckelords igenkännings motorn.

**Exempel**
- **Go**: nya snabb starter för [tal igenkänning](./get-started-speech-to-text.md?pivots=programming-language-go) och [anpassad röst assistent](./quickstarts/voice-assistants.md?pivots=programming-language-go). Hitta exempel kod [här](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples). 
- **Java Script**: nya snabb starter för [text till tal](./get-started-text-to-speech.md?pivots=programming-language-javascript)-, [översättnings](./get-started-speech-translation.md?pivots=programming-language-csharp&tabs=script)-och [Avsiktsigenkänning](./quickstarts/intent-recognition.md?pivots=programming-language-javascript).
- Exempel på nyckelords igenkänning för [C \# ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) och [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android).  

**COVID – 19 förkortad-testning:** På grund av att det går att fjärrans luta under de senaste veckorna kunde vi inte göra så mycket manuella verifierings testningar som vanligt. Vi har inte gjort några ändringar som vi tror skulle kunna ha trasigt något och våra automatiserade tester lyckades. Om vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br>
Håll dig frisk!

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK-1.11.0:2020 – mars release
**Nya funktioner**
- Linux: stöd har lagts till för Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 med [instruktioner](./how-to-configure-rhel-centos-7.md) för hur du konfigurerar systemet för tal-SDK.
- Linux: stöd för .NET Core C# på Linux ARM32 och ARM64 har lagts till. Läs mer [här](./speech-sdk.md?tabs=linux). 
- C#, C++: lades till `UtteranceId` i `ConversationTranscriptionResult` , ett konsekvent ID för alla resultat från mellanliggande och slutlig tal igenkänning. Information för [C#](/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult), [C++](/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult).
- Python: stöd har lagts till för `Language ID` . Se speech_sample. py i [GitHub lagrings platsen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console).
- Windows: stödet för komprimerad ljud inspelning har lagts till i Windows-plattformen för alla Win32-konsol program. Information [här](./how-to-use-codec-compressed-audio-input-streams.md). 
- Java Script: stöd för tal syntes (text till tal) i NodeJS. Läs mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech). 
- Java Script: Lägg till nya API: er för att aktivera inspektion av alla meddelanden som skickas och tagits emot. Läs mer [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript). 
        
**Fel korrigeringar**
- C#, C++: åtgärdat ett problem `SendMessageAsync` skickar nu binärt meddelande som binär typ. Information för [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_), [C++](/cpp/cognitive-services/speech/connection).
- C#, C++: ett problem där händelse används `Connection MessageReceived` kan orsaka krasch om `Recognizer` tas bort före `Connection` objektet. Information för [C#](/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived), [C++](/cpp/cognitive-services/speech/connection#messagereceived).
- Android: ljudbuffertens storlek från mikrofonen minskade från 800ms till 100 ms för att förbättra svars tiden.
- Android: Åtgärdade ett [problem](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563) med x86 Android-emulator i Android Studio.
- Java Script: stöd har lagts till för regioner i Kina med `fromSubscription` API: et. Information [här](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#fromsubscription-string--string-). 
- Java Script: Lägg till mer fel information för anslutnings fel från NodeJS.
        
**Exempel**
- Unit: det offentliga exemplet för avsikts igenkänning är fast, där LUIS JSON-importen misslyckades. Information [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369).
- Python: exempel tillagt `Language ID` . Information [här](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py).
    
**Covid19 förkortad-testning:** Det gick inte att utföra så mycket manuell testning av enhets verifieringen på grund av att det går att fjärrstyra de senaste veckorna. Vi kunde till exempel inte testa mikrofonens indata-och högtalar utdata på Linux, iOS och macOS. Vi har inte gjort några ändringar som vi tror skulle kunna ha avbrutit någonting på dessa plattformar och våra automatiserade tester lyckades. I det förmodade fallet att vi missade något, kan du berätta för oss på [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen).<br> Tack för din fortsatta support. Som alltid kan du skicka frågor eller feedback om [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) eller [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731).<br>
Håll dig frisk!

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK-1.10.0:2020 – februari utgåva

**Nya funktioner**

 - Python-paket har lagts till för att stödja den nya 3,8-versionen av python.
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 support (C++, C#, Java, python).
   > [!NOTE] 
   > Kunderna måste konfigurera OpenSSL enligt [dessa instruktioner](./how-to-configure-openssl-linux.md).
 - Linux ARM32-stöd för Debian och Ubuntu.
 - DialogServiceConnector stöder nu en valfri "robot ID"-parameter på BotFrameworkConfig. Med den här parametern kan du använda flera direkt linje tal robotar med en enda Azure tal-resurs. Utan den angivna parametern används standard-boten (som fastställs av konfigurations sidan för direkt linjens tal kanal).
 - DialogServiceConnector har nu en SpeechActivityTemplate-egenskap. Innehållet i den här JSON-strängen används av direkt linje tal för att fylla i en rad olika fält som stöds i alla aktiviteter som når en direkt rad tal robot, inklusive aktiviteter som skapas automatiskt som svar på händelser som tal igenkänning.
 - TTS använder nu prenumerations nyckeln för autentisering, vilket minskar den första byte fördröjningen för det första syntes resultatet när du har skapat en synthesizer.
 - Uppdaterade tal igenkännings modeller för 19 språk i ett genomsnittligt antal fel i Word-felgrader på 18,6% (es-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN, zh-HK, NB-NO, fi-fi, ru-RU, pl-PL, ca-ES, zh-TW, Th-PT, tr-TR). De nya modellerna ger avsevärda förbättringar i flera domäner, inklusive diktering, Call-Center avskrifter och video indexerings scenarier.

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

 - [Konfigurations dokumentation för OpenSSL har uppdaterats för Linux](./how-to-configure-openssl-linux.md)

## <a name="speech-sdk-190-2020-january-release"></a>1.9.0 för tal-SDK: 2020 – januari utgåva

**Nya funktioner**

- Konversation med flera enheter: Anslut flera enheter till samma tal-eller textbaserade konversationer och eventuellt översätta meddelanden som skickas mellan dem. Läs mer i [den här artikeln](multi-device-conversation.md). 
- Stöd för nyckelords igenkänning har lagts till för Android. AAR-paketet och stöd för x86 och x64 varianter har lagts till. 
- Mål-C: `SendMessage` och `SetMessageProperty` metoder som har lagts till i `Connection` objektet. Se dokumentationen [här](/objectivec/cognitive-services/speech/spxconnection).
- TTS C++ API stöder nu `std::wstring` som text inskrivning, och du tar bort behovet av att konvertera en wstring till sträng innan du skickar den till SDK: n. Se information [här](/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync). 
- C#: [språk-ID](./how-to-automatic-language-detection.md?pivots=programming-language-csharp) och [käll språks konfiguration](./how-to-specify-source-language.md?pivots=programming-language-csharp) är nu tillgängliga.
- Java Script: en funktion har lagts till i `Connection` objekt för att klara genom anpassade meddelanden från tal tjänsten som motanrop `receivedServiceMessage` .
- Java Script: stöd har lagts till för `FromHost API` att under lätta användningen av lokal behållare och suveräna moln. Se dokumentationen [här](speech-container-howto.md).
- Java Script: vi följer nu `NODE_TLS_REJECT_UNAUTHORIZED` tack vare bidrag från [orgads](https://github.com/orgads). Se information [här](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75).

**Icke-bakåtkompatibla ändringar**

- `OpenSSL` har uppdaterats till version 1.1.1 b och är statiskt länkat till tal SDK Core-biblioteket för Linux. Detta kan orsaka en paus om din inkorg `OpenSSL` inte har installerats till `/usr/lib/ssl` katalogen i systemet. Läs [vår dokumentation](how-to-configure-openssl-linux.md) under Speech SDK-dokument för att lösa problemet.
- Vi har ändrat data typen som returnerades för C# `WordLevelTimingResult.Offset` från `int` till för `long` att tillåta åtkomst till `WordLevelTimingResults` när taldata är längre än två minuter.
- `PushAudioInputStream` och `PullAudioInputStream` skickar nu information om WAV-huvud till tjänsten Speech baserat på `AudioStreamFormat` , som du kan ange när de skapades. Kunderna måste nu använda [formatet för ljud inspelning som stöds](how-to-use-audio-input-streams.md). Andra format får ett underoptimerat igenkännings resultat eller kan orsaka andra problem. 

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

**Icke-bakåtkompatibla ändringar**

- Konversations beskrivare-funktioner flyttas under namnrymd `Microsoft.CognitiveServices.Speech.Transcription` .
- Delar av konversations beskrivare-metoderna flyttas till en ny `Conversation` klass.
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
- Åtgärda möjliga krascher när du öppnar en anslutning under hög belastning på Linux
- Korrigering för saknade metadata i Ramverks paketet för macOS
- Åtgärda problem med `pip install --user` Windows

## <a name="speech-sdk-151"></a>1.5.1 för tal-SDK

Detta är en fel korrigerings version och påverkar endast det inbyggda/hanterade SDK: n. Den påverkar inte JavaScript-versionen av SDK: n.

**Fel korrigeringar**

- Åtgärda FromSubscription när den används med konversations avskriftering.
- Åtgärda fel i nyckelords igenkänning för röst assistenter.

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK-1.5.0:2019 – maj-version

**Nya funktioner**

- Nyckelords igenkänning är nu tillgängligt för Windows och Linux. Den här funktionen kan fungera med vilken typ av mikrofon som helst, men statsstöd är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller i tal enheter SDK.
- Funktionen för fras tips är tillgänglig via SDK. Mer information finns [här](./get-started-speech-to-text.md).
- Konversations avskrifts funktionerna är tillgängliga via SDK: n. Se [här](./conversation-transcription.md).
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
- Talet SDK stöder nu Unity i en beta version. Ge feedback via avsnittet ärende i GitHub- [exempel lagrings plats](https://aka.ms/csspeech/samples). Den här versionen stöder Unity på Windows x86 och x64 (Skriv bords-eller Universell Windows-plattform program) och Android (ARM32/64, x86). Mer information finns i vår [snabb start](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=unity).
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
  - Vi har nu stöd för Node.js. mer information finns [här](./get-started-speech-to-text.md).
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
- Det gick inte att åtgärda ett allvarligt fel när egenskaperna för att ta bort.
- Ytterligare fel och parameter kontroll har lagts till.
- Mål-C: åtgärdat allvarligt fel som orsakats av att namnet åsidosätts i NSString.
- Mål-C: anpassad synlighet för API
- Java Script: åtgärdat avseende händelser och deras nytto laster.
- Förbättringar av dokumentation.

I vår [exempel lagring](https://aka.ms/csspeech/samples)lades ett nytt exempel för Java Script till.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0:2018 – september version

**Nya funktioner**

- Stöd för mål-C på iOS. Kolla vår [mål-C-snabb start för iOS](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios/from-microphone).
- Stöd för Java Script i webbläsare. Kolla i vår [snabb start för Java Script](./get-started-speech-to-text.md).

**Icke-bakåtkompatibla ändringar**

- I den här versionen introduceras ett antal avbrytande ändringar.
  Mer information finns på [den här sidan](https://aka.ms/csspeech/breakingchanges_1_0_0) .

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0:2018 – augusti utgåva

**Nya funktioner**

- UWP-appar som skapats med Speech SDK kan nu skicka Windows app Certificate Kit (WACK).
  Ta en titt på snabb starten för [UWP](./get-started-speech-to-text.md?pivots=programming-language-chsarp&tabs=uwp).
- Stöd för .NET standard 2,0 på Linux (Ubuntu 16,04 x64).
- Experimentell: stöder Java 8 på Windows (64-bitars) och Linux (Ubuntu 16,04 x64).
  Kolla in [Java Runtime Environment snabb start](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=jre).

**Funktionell ändring**

- Visa ytterligare information om fel information om anslutnings fel.

**Icke-bakåtkompatibla ändringar**

- I Java (Android) `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` kräver funktionen inte längre en Sök vägs parameter. Nu identifieras sökvägen automatiskt på alla plattformar som stöds.
- Get-accessorn för egenskapen `EndpointUrl` i Java och C# har tagits bort.

**Fel korrigeringar**

- I Java implementeras ljud sammanfattnings resultatet i översättnings tolken nu.
- Ett fel som kan orsaka inaktiva trådar och ett ökat antal öppna och oanvända Sockets har åtgärd ATS.
- Ett problem har åtgärd ATS, där en tids krävande igenkänning kan avslutas mitt i överföringen.
- Ett konkurrens villkor har åtgärd ATS i avstängningen av tolken.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0:2018 – juli utgåva

**Nya funktioner**

- Stöd för Android-plattform (API 23: Android 6,0 Marshmallow eller högre). Kolla in [Android-snabb](./get-started-speech-to-text.md?pivots=programming-language-java&tabs=android)starten.
- Stöd för .NET standard 2,0 i Windows. Kolla in [.net Core-snabb](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore)starten.
- Experimentell: stöd för UWP i Windows (version 1709 eller senare).
  - Ta en titt på snabb starten för [UWP](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).
  - Obs! UWP-appar som skapats med tal-SDK: n har inte överfört Windows app Certificate Kit (WACK).
- Stöd för tids krävande igenkänning med automatisk åter anslutning.

**Funktionella ändringar**

- `StartContinuousRecognitionAsync()` stöder tids krävande igenkänning.
- Igenkännings resultatet innehåller fler fält. De är förskjutna från början och varaktigheten för ljudet (både i Ticket) för den tolkade texten och ytterligare värden som representerar igenkännings status, till exempel `InitialSilenceTimeout` och `InitialBabbleTimeout` .
- Stöd för AuthorizationToken för att skapa fabriks instanser.

**Icke-bakåtkompatibla ändringar**

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
