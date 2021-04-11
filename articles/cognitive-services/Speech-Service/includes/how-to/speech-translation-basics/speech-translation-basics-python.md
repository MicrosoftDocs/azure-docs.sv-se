---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: bea61ac9a8c5700cd4f18cfb567b054de6b5613d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104505"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att identifiera mänskligt tal och översätta den till andra språk. I den här snabb starten lär du dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativ tal översättning. Den här snabb starten behandlar ämnen, inklusive:

* Översättning av tal till text
* Översätta tal till flera mål språk
* Utföra direkt tal-till-tal-Översättning

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [exempel på python-snabb starts exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/python) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Beroende på din plattform följer du anvisningarna under Hämta avsnittet om <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">tal-SDK</a> i artikeln _om tal SDK_ .

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande `import` instruktioner överst i python-koden.

```python
import os
import azure.cognitiveservices.speech as speechsdk
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data och miljövariabler

Exempel käll koden i den här artikeln är beroende av miljövariabler för lagring av känsliga data, till exempel nyckel och region för tal resurs prenumerationen. Filen python-kod innehåller två värden som tilldelas från värd datorns miljövariabler, nämligen `SPEECH__SUBSCRIPTION__KEY` och `SPEECH__SERVICE__REGION` . Båda dessa variabler finns i det globala omfånget, vilket gör dem tillgängliga i funktions definitionen för kod filen. Mer information om miljövariabler finns i [miljövariabler och program konfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```python
speech_key, service_region = os.environ['SPEECH__SUBSCRIPTION__KEY'], os.environ['SPEECH__SERVICE__REGION']
```

## <a name="create-a-speech-translation-configuration"></a>Skapa en konfiguration för tal Översättning

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechTranslationConfig`][config] . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!TIP]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechTranslationConfig`][config] :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

Låt oss ta en titt på hur en [`SpeechTranslationConfig`][config] har skapats med hjälp av en nyckel och region. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

```python
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal översättning anger indata (eller käll språket). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. I din kod interagerar du med [`SpeechTranslationConfig`][config] instansen och tilldelar `speech_recognition_language` egenskapen.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    # Source (input) language
    translation_config.speech_recognition_language = from_language
```

[`speech_recognition_language`][recognitionlang]Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="add-translation-language"></a>Lägg till översättnings språk

En annan vanlig uppgift för tal översättning är att ange språk för mål översättning, minst ett krävs men multiplar stöds. Följande kodfragment anger både franska och tyska som översättnings språk mål.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = "it-IT"

    # Translate to languages. See, https://aka.ms/speech/sttt-languages
    translation_config.add_target_language("fr")
    translation_config.add_target_language("de")
```

Med varje anrop till [`add_target_language`][addlang] anges ett nytt mål översättnings språk. Med andra ord, när tal identifieras från käll språket, är varje mål översättning tillgänglig som en del av den resulterande översättnings åtgärden.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättnings tolk

När du har skapat en [`SpeechTranslationConfig`][config] är nästa steg att initiera en [`TranslationRecognizer`][recognizer] . När du initierar en måste [`TranslationRecognizer`][recognizer] du skicka den `translation_config` . Konfigurationsobjektet ger de autentiseringsuppgifter som krävs av tal tjänsten för att verifiera din begäran.

Om du känner igen tal med hjälp av enhetens standard mikrofon [`TranslationRecognizer`][recognizer] ska du se vad som ska se ut så här:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
```

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`][audioconfig] och ange `audio_config` parametern när du initierar [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Lär dig hur du hämtar enhets-ID: t för din enhet för ljud inspelning](../../../how-to-select-audio-input-devices.md).

Först ska du referera till `AudioConfig` objektet på följande sätt:

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(use_default_microphone=True)
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

Om du vill ange en ljudfil i stället för att använda en mikrofon måste du ändå ange en `audioConfig` . Men när du skapar en [`AudioConfig`][audioconfig] , i stället för att anropa med `use_default_microphone=True` , kommer du att anropa med `filename="path-to-file.wav"` och ange `filename` parametern.

```python
def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    audio_config = speechsdk.audio.AudioConfig(filename="path-to-file.wav")
    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config, audio_config=audio_config)
```

## <a name="translate-speech"></a>Översätt tal

För att översätta tal är tal-SDK: n beroende av en mikrofon eller en inspelning av ljud filen. Tal igenkänning inträffar före tal översättning. När alla objekt har initierats anropar du funktionen recognize-Once och hämtar resultatet.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'RECOGNIZED "{from_language}": {result.text}\n' +
            f'TRANSLATED into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

Mer information om tal till text finns i [grunderna i tal igenkänning](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad tal igenkänning och översättning innehåller resultatet alla översättningar i en ord lista. [`translations`][translations]Ord listans nyckel är mål översättnings språket och värdet är den översatta texten. Identifierat tal kan översättas och sedan syntetiseras på ett annat språk (tal-till-tal).

### <a name="event-based-synthesis"></a>Händelse-baserad syntes

`TranslationRecognizer`Objektet visar en `Synthesizing` händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet av översättnings igenkänningen. Om du översätter till flera språk, se [manuell syntes](#manual-synthesis). Ange syntes rösten genom att tilldela en [`voice_name`][voicename] och ange en händelse hanterare för `Synthesizing` händelsen, Hämta ljudet. I följande exempel sparas det översatta ljudet som en *. wav* -fil.

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, Lägg **inte** till flera mål översättnings språk. Dessutom bör det [`voice_name`][voicename] vara samma språk som mål översättnings språket, till exempel, `"de"` kan mappas till `"de-DE-Hedda"` .

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_language = 'en-US', 'de'

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    translation_config.add_target_language(to_language)

    # See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translation_config.voice_name = "de-DE-Hedda"

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)

    def synthesis_callback(evt):
        size = len(evt.result.audio)
        print(f'Audio synthesized: {size} byte(s) {"(COMPLETED)" if size == 0 else ""}')

        if size > 0:
            file = open('translation.wav', 'wb+')
            file.write(evt.result.audio)
            file.close()

    recognizer.synthesizing.connect(synthesis_callback)

    print(f'Say something in "{from_language}" and we\'ll translate into "{to_language}".')

    result = recognizer.recognize_once()
    print(get_result_text(reason=result.reason, result=result))

def get_result_text(reason, result):
    reason_format = {
        speechsdk.ResultReason.TranslatedSpeech:
            f'Recognized "{from_language}": {result.text}\n' +
            f'Translated into "{to_language}"": {result.translations[to_language]}',
        speechsdk.ResultReason.RecognizedSpeech: f'Recognized: "{result.text}"',
        speechsdk.ResultReason.NoMatch: f'No speech could be recognized: {result.no_match_details}',
        speechsdk.ResultReason.Canceled: f'Speech Recognition canceled: {result.cancellation_details}'
    }
    return reason_format.get(reason, 'Unable to recognize speech')

translate_speech_to_text()
```

### <a name="manual-synthesis"></a>Manuell syntes

[`translations`][translations]Ord listan kan användas för att syntetisera ljud från översättnings texten. Iterera igenom varje översättning och syntetisera översättningen. När du skapar en `SpeechSynthesizer` instans `SpeechConfig` måste dess [`speech_synthesis_voice_name`][speechsynthesisvoicename] egenskap ha angetts till önskad röst för objektet. I följande exempel översätts till fem språk och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala-språk.

```python
import os
import azure.cognitiveservices.speech as speechsdk

speech_key, service_region = os.environ['SPEECH__SERVICE__KEY'], os.environ['SPEECH__SERVICE__REGION']
from_language, to_languages = 'en-US', [ 'de', 'en', 'it', 'pt', 'zh-Hans' ]

def translate_speech_to_text():
    translation_config = speechsdk.translation.SpeechTranslationConfig(
            subscription=speech_key, region=service_region)

    translation_config.speech_recognition_language = from_language
    for lang in to_languages:
        translation_config.add_target_language(lang)

    recognizer = speechsdk.translation.TranslationRecognizer(
            translation_config=translation_config)
    
    print('Say something...')
    result = recognizer.recognize_once()
    synthesize_translations(result=result)

def synthesize_translations(result):
    language_to_voice_map = {
        "de": "de-DE-KatjaNeural",
        "en": "en-US-AriaNeural",
        "it": "it-IT-ElsaNeural",
        "pt": "pt-BR-FranciscaNeural",
        "zh-Hans": "zh-CN-XiaoxiaoNeural"
    }
    print(f'Recognized: "{result.text}"')

    for language in result.translations:
        translation = result.translations[language]
        print(f'Translated into "{language}": {translation}')

        speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
        speech_config.speech_synthesis_voice_name = language_to_voice_map.get(language)
        
        audio_config = speechsdk.audio.AudioOutputConfig(filename=f'{language}-translation.wav')
        speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)
        speech_synthesizer.speak_text_async(translation).get()

translate_speech_to_text()
```

Mer information om tal syntes finns i [grunderna för tal syntes](../../../get-started-text-to-speech.md).

[config]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig
[audioconfig]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.audio.audioconfig
[recognizer]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig
[addlang]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#add-target-language-language--str-
[translations]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.translationrecognitionresult#translations
[voicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.translation.speechtranslationconfig#voice-name
[speechsynthesisvoicename]: /python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#speech-synthesis-voice-name