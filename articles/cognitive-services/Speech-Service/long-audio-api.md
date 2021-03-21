---
title: Tjänst för långa ljud-API – tal
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618414"
---
# <a name="long-audio-api"></a>Långt ljud-API

Den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal (till exempel: ljud böcker, nyhets artiklar och dokument). Det här API: t returnerar inte syntetiskt ljud i real tid, i stället är det förväntat att du ska söka efter svar och använda de utdata som de görs tillgängliga från tjänsten. Till skillnad från text till tal-API som används av talet SDK, kan det långa ljud-API: et skapa syntetiskt ljud som är längre än 10 minuter, vilket gör det idealiskt för utgivare och ljud innehålls plattformar för att skapa ett långt ljud innehåll som ljud böcker i en batch.

Ytterligare fördelar med den långa ljud-API: et:

* Syntetiskt tal som returneras av tjänsten använder de bästa neurala-rösterna.
* Du behöver inte distribuera en röst slut punkt när den syntetiserar röster i ingen real tids batch-läge.

> [!NOTE]
> API för långa ljud stöder nu både [offentliga neurala-röster](./language-support.md#neural-voices) och [anpassade neurala-röster](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Arbetsflöde

När du använder det långa ljud-API: et skickar du normalt en textfil eller filer som ska syntetiseras, avsöker efter status. om statusen är klar kan du ladda ned ljud uppspelningen.

Det här diagrammet innehåller en översikt över arbets flödet.

![Arbets flödes diagram för långt ljud-API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Förbered innehåll för syntes

När du förbereder text filen måste du se till att den:

* Är antingen oformaterad text (. txt) eller SSML text (. txt)
* Kodas som [UTF-8 med byte ordnings tecken (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Är en enskild fil, inte ett zip
* Innehåller fler än 400 tecken för oformaterad text eller 400 [fakturerbara tecken](./text-to-speech.md#pricing-note) för SSML text och färre än 10 000 stycken
  * För oformaterad text separeras varje stycke genom att trycka på **RETUR/retur** -Visa [text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * För SSML text betraktas varje SSML-enhet som ett stycke. SSML bitar separeras av olika stycken – Visa [SSML text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)

## <a name="sample-code"></a>Exempelkod
Resten av den här sidan fokuserar på python, men exempel kod för lång ljud-API finns på GitHub för följande programmeringsspråk:

* [Exempel kod: python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exempel kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exempel kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Python-exempel

Det här avsnittet innehåller python-exempel som visar den grundläggande användningen av det långa ljud-API: et. Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan kodfragmentet till en fil med namnet `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Dessa bibliotek används för att skapa HTTP-begäran och anropar text-till-tal-ljud syntes REST API.

### <a name="get-a-list-of-supported-voices"></a>Hämta en lista över röster som stöds

Om du vill hämta en lista över vilka röster som stöds skickar du en GET-begäran till `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Med den här koden kan du få en fullständig lista över röster för en speciell region eller slut punkt som du kan använda.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Ersätt följande värden:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänsten. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus` ). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).

Du ser ett utdata som ser ut så här:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Om **Properties. publicAvailable** är **True** är rösten en offentlig neurala röst. Annars är det en anpassad neurala röst.

### <a name="convert-text-to-speech"></a>Omvandla text till tal

Förbered en textfil, antingen i oformaterad text eller SSML text, och Lägg sedan till följande kod i `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` är en valfri parameter. Om den här parametern inte anges genereras ljud utmatningarna per stycke. Du kan också sammanfoga ljuden till 1 utdata genom att ange parametern. 
> `outputFormat` är också valfritt. Som standard är ljud uppspelningen inställd på riff-16khz-bitarsläge-mono-PCM. Mer information om ljud format som stöds finns i [format för ljud uppspelning](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Ersätt följande värden:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänsten. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus` ). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<input_file_path>` med sökvägen till text filen som du har för berett för text till tal.
* Ersätt `<locale>` med det önskade utmatnings språket. Mer information finns i [språk stöd](language-support.md#neural-voices).

Använd en av de röster som returnerades av ditt föregående anrop till `/voices` slut punkten.

* Om du använder en offentlig neurala-röst ersätter du `<voice_name>` med den önskade utdata-rösten.
* Om du vill använda en anpassad neurala-röst ersätter du `voice_identities` variabeln med följande och ersätter `<voice_id>` med den `id` anpassade neurala rösten.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Du ser ett utdata som ser ut så här:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Om du har fler än 1 indatafiler måste du skicka flera begär Anden. Det finns vissa begränsningar som måste vara medvetna.
> * Klienten får skicka upp till **5** förfrågningar till server per sekund för varje Azure-prenumerations konto. Om den överskrider begränsningen får klienten en 429-felkod (för många begär Anden). Minska antalet begär Anden per sekund.
> * Servern kan köra och köa upp till **120** förfrågningar för varje Azure-prenumerations konto. Om den överskrider begränsningen returnerar servern en 429-felkod (för många begär Anden). Vänta och Undvik att skicka ny begäran förrän vissa begär Anden har slutförts.

URL: en i utdata kan användas för att hämta status för begäran.

### <a name="get-information-of-a-submitted-request"></a>Hämta information om en skickad begäran

Skicka en GET-begäran till URL: en som returnerades av föregående steg för att få status för en skickad syntes förfrågan.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
Utdata ser ut så här:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Från `status` -egenskapen kan du läsa status för den här begäran. Begäran kommer att starta från `NotStarted` status, sedan ändras till `Running` och slutligen bli `Succeeded` eller `Failed` . Du kan använda en loop för att avsöka denna API tills statusen blir `Succeeded` .

### <a name="download-audio-result"></a>Hämta ljud resultat

När en syntes-begäran lyckas kan du hämta ljud resultatet genom att anropa GET `/files` API.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Ersätt `<request_id>` med ID för begäran som du vill hämta resultatet. Den finns i svar på föregående steg.

Utdata ser ut så här:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
Utdata innehåller information om 2 filer. Den med `"kind": "LongAudioSynthesisScript"` är inmatade skript som skickas. Den andra med `"kind": "LongAudioSynthesisResult"` är resultatet av den här begäran.
Resultatet är zip som innehåller de ljudutdata som genereras, tillsammans med en kopia av inmatnings texten.

Båda filerna kan hämtas från URL: en i sina `links.contentUrl` Egenskaper.

### <a name="get-all-synthesis-requests"></a>Hämta alla syntes förfrågningar

Du kan hämta en lista över alla skickade förfrågningar med följande kod:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

Utdata kommer att vara som:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` egenskapen innehåller en lista över syntes begär Anden. Listan innehåller en sid brytning med en maximal sid storlek på 100. Om det finns fler än 100 begär Anden `"@nextLink"` kommer en egenskap att tillhandahållas för att hämta nästa sida i den sid brytnings lista.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Du kan också anpassa sid storlek och Skip-nummer genom att ange `skip` och `top` i URL-parameter.

### <a name="remove-previous-requests"></a>Ta bort tidigare begär Anden

Tjänsten håller upp till **20 000** förfrågningar för varje Azure-prenumerations konto. Om ditt begär ande belopp överskrider den här begränsningen kan du ta bort tidigare begär Anden innan du gör nya. Om du inte tar bort befintliga förfrågningar får du ett fel meddelande.

Följande kod visar hur du tar bort en angiven syntes förfrågan.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Om begäran har tagits bort kommer svars status koden att vara HTTP 204 (inget innehåll).

```console
response.status_code: 204
```

> [!NOTE]
> Begär Anden med statusen `NotStarted` eller `Running` kan inte tas bort eller tas bort.

Slutfört `long_audio_synthesis_client.py` är tillgängligt på [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>HTTP-statuskoder

I följande tabell beskrivs HTTP-svars koderna och meddelanden från REST API.

| API | HTTP-statuskod | Beskrivning | Lösning |
|-----|------------------|-------------|----------|
| Skapa | 400 | Röst syntesen är inte aktive rad i den här regionen. | Ändra tal prenumerations nyckeln med en region som stöds. |
|        | 400 | Endast **standard** tal prenumerationen för den här regionen är giltig. | Ändra tal prenumerations nyckeln till pris nivån "standard". |
|        | 400 | Överskrid gränsen för 20 000-begäran för Azure-kontot. Ta bort några förfrågningar innan du skickar nya. | Servern kommer att ha upp till 20 000 förfrågningar för varje Azure-konto. Ta bort några begär Anden innan du skickar nya. |
|        | 400 | Det går inte att använda den här modellen i röst syntesen: {modelID}. | Kontrol lera att status för {modelID} är korrekt. |
|        | 400 | Regionen för begäran matchar inte regionen för modellen {modelID}. | Kontrol lera att {modelID} s region stämmer överens med begärans region. |
|        | 400 | Röst syntesen stöder bara text filen i UTF-8-kodningen med byte-ordnings markören. | Kontrol lera att indatafilerna är i UTF-8-kodning med markör för byte-ordning. |
|        | 400 | Endast giltiga SSML-indata är tillåtna i röst syntes förfrågan. | Kontrol lera att SSML-uttrycken är korrekta. |
|        | 400 | Det gick inte att hitta röst namnet {voiceName} i indatafilen. | Röst namnet för SSML är inte justerat med modell-ID: t. |
|        | 400 | Antalet stycken i indatafilen ska vara mindre än 10 000. | Kontrol lera att antalet stycken i filen är mindre än 10 000. |
|        | 400 | Indatafilen får innehålla mer än 400 tecken. | Kontrol lera att indatafilen överskrider 400 tecken. |
|        | 404 | Det går inte att hitta modellen som har deklarerats i röst syntes definitionen: {modelID}. | Kontrol lera att {modelID} är korrekt. |
|        | 429 | Överskrida den aktiva röst syntes gränsen. Vänta tills några begär Anden har slutförts. | Servern kan köra och köa upp till 120 förfrågningar för varje Azure-konto. Vänta och Undvik att skicka nya begär Anden förrän vissa begär Anden har slutförts. |
| Alla       | 429 | Det finns för många begär Anden. | Klienten får skicka upp till 5 förfrågningar till server per sekund för varje Azure-konto. Minska antalet begär Anden per sekund. |
| Ta bort    | 400 | Röst syntes aktiviteten används fortfarande. | Du kan bara ta bort begär Anden som har **slutförts** eller **misslyckats**. |
| GetByID   | 404 | Det går inte att hitta den angivna entiteten. | Kontrol lera att syntes-ID: t är korrekt. |

## <a name="regions-and-endpoints"></a>Regioner och slut punkter

Den långa ljud-API: n är tillgänglig i flera regioner med unika slut punkter.

| Region | Slutpunkt |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indien, centrala | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Sydostasien | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Storbritannien, södra | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa, västra | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Format för ljud uppspelning

Vi har stöd för flexibla format för ljud uppspelning. Du kan generera ljud utmatningar per stycke eller sammanfoga ljud utmatningarna till en enda utmatning genom att ange parametern "concatenateResult". Följande format för ljudutdata stöds av den långa ljud-API: et:

> [!NOTE]
> Standard ljud formatet är riff-16khz-bitarsläge-mono-PCM.

* riff-8khz-bitarsläge-mono-PCM
* riff-16khz-bitarsläge-mono-PCM
* riff-24khz-bitarsläge-mono-PCM
* riff-48kHz-bitarsläge-mono-PCM
* Audio-16khz-32kbitrate-mono-MP3
* Audio-16khz-64kbitrate-mono-MP3
* Audio-16khz-128kbitrate-mono-MP3
* Audio-24khz-48kbitrate-mono-MP3
* Audio-24khz-96kbitrate-mono-MP3
* Audio-24khz-160kbitrate-mono-MP3
