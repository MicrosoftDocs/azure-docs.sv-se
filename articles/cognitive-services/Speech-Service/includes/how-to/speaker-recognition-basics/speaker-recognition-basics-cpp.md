---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: f8b29da09f5da4a30378cc0228fecad63d196162
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444609"
---
I den här snabb starten lär du dig grundläggande design mönster för Talarigenkänning med hjälp av tal-SDK, inklusive:

* Text beroende och text oberoende verifiering
* Högtalar identifiering för att identifiera ett röst exempel bland en grupp röster
* Ta bort röst profiler

En övergripande överblick över tal igenkännings koncept finns i [översikts](../../../speaker-recognition-overview.md) artikeln.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen för C++](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> Talarigenkänning stöds för närvarande *bara* i Azure tal-resurser som skapats i `westus` regionen.

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Använd följande instruktioner, beroende på plattform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=linux&pivots=programming-language-cpp" target="_blank">Linux </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=macos&pivots=programming-language-cpp" target="_blank">macOS </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=windows&pivots=programming-language-cpp" target="_blank">Aktivitets </a>

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln lägger du till följande-instruktioner överst i. cpp-filen.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="dependencies":::

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/cpp/cognitive-services/speech/speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="get_speech_config":::

## <a name="text-dependent-verification"></a>Text beroende verifiering

Talarverifiering är en bekräftelse på att en talare matchar en känd eller **registrerad** röst. Det första steget är att **Registrera** en röst profil, så att tjänsten har något att jämföra framtida röst exempel mot. I det här exemplet registrerar du profilen med en **text beroende** strategi, som kräver att en unik lösen fras används för både registrering och verifiering. En lista över lösen fraser som stöds finns i [referens dokumenten](/rest/api/speakerrecognition/) .

### <a name="textdependentverification-function"></a>Funktionen TextDependentVerification

Börja med att skapa `TextDependentVerification` funktionen.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_dependent_verification":::

Den här funktionen skapar ett [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) -objekt med [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) -metoden. Observera att det finns tre [typer](/cpp/cognitive-services/speech/microsoft-cognitiveservices-speech-namespace#enum-voiceprofiletype) av `VoiceProfile` :

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

I det här fallet skickar du `VoiceProfileType::TextDependentVerification` till `CreateProfileAsync` .

Sedan anropar du två hjälp funktioner som du definierar härnäst `AddEnrollmentsToTextDependentProfile` och `SpeakerVerify` . Anropa slutligen [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) för att rensa profilen.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funktionen AddEnrollmentsToTextDependentProfile

Definiera följande funktion för att registrera en röst profil.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_dependent":::

I den här funktionen registrerar du ljud exempel i en `while` slinga som spårar antalet återstående prover och krävs för registrering. I varje iteration uppvisar [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) att du pratar lösen frasen i mikrofonen och lägger till exemplet i röst profilen.

### <a name="speakerverify-function"></a>Funktionen SpeakerVerify

Definiera `SpeakerVerify` enligt följande.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_verify":::

I den här funktionen skapar du ett [SpeakerVerificationModel](/cpp/cognitive-services/speech/speakerverificationmodel) -objekt med metoden [SpeakerVerificationModel:: FromProfile](/cpp/cognitive-services/speech/speakerverificationmodel#fromprofile) som skickar i [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) -objektet som du skapade tidigare.

Sedan uppmanas du [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) dig att prata lösen frasen igen, men den här gången kommer den att verifieras mot din röst profil och returnerar ett likhets resultat mellan 0,0-1.0. [SpeakerRecognitionResult](/cpp/cognitive-services/speech/speakerrecognitionresult) -objektet returnerar också `Accept` eller `Reject` , baserat på om lösen frasen matchar eller inte.

## <a name="text-independent-verification"></a>Text oberoende verifiering

I motsats till **text beroende** verifiering, **text oberoende** verifiering:

* Kräver ingen viss lösen fras för att kunna läsas, något kan ses över
* Kräver inte tre ljud exempel *, men kräver* 20 sekunders total ljud

### <a name="textindependentverification-function"></a>Funktionen TextIndependentVerification

Börja med att skapa `TextIndependentVerification` funktionen.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_verification":::

Precis som `TextDependentVerification` funktionen skapar den här funktionen ett [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) -objekt med [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) -metoden.

I det här fallet skickar du `VoiceProfileType::TextIndependentVerification` till `CreateProfileAsync` .

Sedan anropar du två hjälp funktioner: `AddEnrollmentsToTextIndependentProfile` , som du definierar härnäst och `SpeakerVerify` , som du definierade redan. Anropa slutligen [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) för att rensa profilen.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definiera följande funktion för att registrera en röst profil.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="add_enrollments_independent":::

I den här funktionen registrerar du ljud exempel i en `while` slinga som spårar antalet sekunder av återstående ljud och krävs för registrering. I varje iteration uppvisar [EnrollProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#enrollprofileasync) att du talar i mikrofonen och lägger till exemplet i röst profilen.

## <a name="speaker-identification"></a>Talaridentifiering

Talaridentifiering används för att bestämma **vem som** talar från en specifik grupp med registrerade röster. Processen liknar **text oberoende verifiering**, med den största skillnaden att kunna verifiera mot flera röst profiler samtidigt, i stället för att verifiera mot en enskild profil.

### <a name="textindependentidentification-function"></a>Funktionen TextIndependentIdentification

Börja med att skapa `TextIndependentIdentification` funktionen.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="text_independent_indentification":::

Precis som `TextDependentVerification` `TextIndependentVerification` -och-funktionerna skapar den här funktionen ett [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) -objekt med [CreateProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#createprofileasync) -metoden.

I det här fallet skickar du `VoiceProfileType::TextIndependentIdentification` till `CreateProfileAsync` .

Sedan anropar du två hjälp funktioner: `AddEnrollmentsToTextIndependentProfile` , som du definierade redan och `SpeakerIdentify` , som du definierar härnäst. Anropa slutligen [DeleteProfileAsync](/cpp/cognitive-services/speech/voiceprofileclient#deleteprofileasync) för att rensa profilen.

### <a name="speakeridentify-function"></a>Funktionen SpeakerIdentify

Definiera `SpeakerIdentify` funktionen enligt följande.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="speaker_identify":::

I den här funktionen skapar du ett [SpeakerIdentificationModel](/cpp/cognitive-services/speech/speakeridentificationmodel) -objekt med metoden [SpeakerIdentificationModel:: FromProfiles](/cpp/cognitive-services/speech/speakeridentificationmodel#fromprofiles) . `SpeakerIdentificationModel::FromProfiles` accepterar en lista med [VoiceProfile](/cpp/cognitive-services/speech/voiceprofile) -objekt. I det här fallet ska du bara skicka det `VoiceProfile` objekt som du skapade tidigare. Men om du vill kan du skicka flera `VoiceProfile` objekt, varje registrerad med ljud exempel från en annan röst.

Härnäst, [SpeechRecognizer:: RecognizeOnceAsync](/cpp/cognitive-services/speech/speechrecognizer#recognizeonceasync) gör att du kan tala igen. Den här gången jämförs din röst med de registrerade röst profilerna och returnerar den liknande röst profilen.

## <a name="main-function"></a>Huvud funktion

Definiera slutligen `main` funktionen enligt följande.

:::code language="cpp" source="~/cognitive-services-quickstart-code/cpp/speech/speaker-recognition.cpp" id="main":::

Den här funktionen anropar bara de funktioner som du definierade tidigare. Först, men skapar det ett [VoiceProfileClient](/cpp/cognitive-services/speech/voiceprofileclient) -objekt och ett [SpeakerRecognizer](/cpp/cognitive-services/speech/speakerrecognizer) -objekt.

```
auto speech_config = GetSpeechConfig();
auto client = VoiceProfileClient::FromConfig(speech_config);
auto recognizer = SpeakerRecognizer::FromConfig(speech_config, audio_config);
```

`VoiceProfileClient`Används för att skapa, registrera och ta bort röst profiler. `SpeakerRecognizer`Används för att validera tal exempel mot en eller flera registrerade röst profiler.

## <a name="changing-audio-input-type"></a>Ändrar typ av ljud inspelning

I exemplen i den här artikeln används standard mikrofonen för enheten som indata för ljud exempel. I scenarier där du behöver använda ljudfiler i stället för mikrofon indata, ändrar du dock bara följande rad:

```
auto audio_config = Audio::AudioConfig::FromDefaultMicrophoneInput();
```

till:

```
auto audio_config = Audio::AudioConfig::FromWavFileInput(path/to/your/file.wav);
```

Eller Ersätt all användning av `audio_config` med [ljud:: AudioConfig:: FromWavFileInput](/cpp/cognitive-services/speech/audio-audioconfig#fromwavfileinput). Du kan också använda blandade indata med en mikrofon för registrering och filer för verifiering, till exempel.