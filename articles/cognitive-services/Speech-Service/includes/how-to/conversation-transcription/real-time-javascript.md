---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 10/20/2020
ms.author: trbye
ms.openlocfilehash: 502729e4c90a6d6ffa424ea7f379e2b426b3e3e0
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444492"
---
## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK för Java Script </a>. Använd följande instruktioner, beroende på plattform:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare </a>

## <a name="create-voice-signatures"></a>Skapa röst under skrifter

Det första steget är att skapa röst signaturer för konversations deltagarna så att de kan identifieras som unika högtalare. `.wav`Ljud filen för indata för att skapa röst-signaturer ska vara 16-bitars, 16 kHz samplings frekvens och mono-format (Single Channel). Den rekommenderade längden för varje ljud sampling är mellan trettio sekunder och två minuter. `.wav`Filen bör vara ett exempel på **en persons** röst så att en unik röst profil skapas.

I följande exempel visas hur du skapar en röst-signatur med [hjälp av REST API](https://aka.ms/cts/signaturegenservice) i Java Script. Observera att du måste ersätta verklig information för din `subscriptionKey` , `region` och sökvägen till en exempel `.wav` fil.

```javascript
const fs = require('fs');
const axios = require('axios');
const formData = require('form-data');
 
const subscriptionKey = 'your-subscription-key';
const region = 'your-region';
 
async function createProfile() {
    let form = new formData();
    form.append('file', fs.createReadStream('path-to-voice-sample.wav'));
    let headers = form.getHeaders();
    headers['Ocp-Apim-Subscription-Key'] = subscriptionKey;
 
    let url = `https://signature.${region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData`;
    let response = await axios.post(url, form, { headers: headers });
    
    // get signature from response, serialize to json string
    return JSON.stringify(response.data.Signature);
}
 
async function main() {
    // use this voiceSignature string with conversation transcription calls below
    let voiceSignatureString = await createProfile();
    console.log(voiceSignatureString);
}
main();
```

Om du kör skriptet returneras en röst signatur sträng i variabeln `voiceSignatureString` . Kör funktionen två gånger så att du kan använda två strängar som inmatade för variablerna `voiceSignatureStringUser1` och `voiceSignatureStringUser2` nedan.

> [!NOTE]
> Det går **bara** att skapa röst signaturer med REST API.

## <a name="transcribe-conversations"></a>Skriva över konversationer

Följande exempel kod visar hur du kan skriva om konversationer i real tid för två högtalare. Det förutsätter att du redan har skapat strängar med röst-signaturer för varje talare som visas ovan. Ersätt verklig information för `subscriptionKey` , `region` och sökvägen `filepath` för det ljud som du vill skriva.

Den här exempel koden gör följande:

* Skapar en push-ström som ska användas för avskrift och skriver exempel `.wav` filen till den.
* Skapar en `Conversation` med `createConversationAsync()` .
* Skapar en `ConversationTranscriber` med hjälp av konstruktorn.
* Lägger till deltagare i konversationen. Strängarna `voiceSignatureStringUser1` och `voiceSignatureStringUser2` ska visas som utdata från stegen ovan.
* Registrerar händelser och påbörjar avskriften.

```javascript
(function() {
    "use strict";
    var sdk = require("microsoft-cognitiveservices-speech-sdk");
    var fs = require("fs");
    
    var subscriptionKey = "your-subscription-key";
    var region = "your-region";
    var filepath = "audio-file-to-transcribe.wav"; // 8-channel audio
    
    // create the push stream and write file to it
    var pushStream = sdk.AudioInputStream.createPushStream();
    fs.createReadStream(filepath).on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
    
    var speechTranslationConfig = sdk.SpeechTranslationConfig.fromSubscription(subscriptionKey, region);
    var audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    speechTranslationConfig.speechRecognitionLanguage = "en-US";
    
    // create conversation and transcriber
    var conversation = sdk.Conversation.createConversationAsync(speechTranslationConfig, "myConversation");
    var transcriber = new sdk.ConversationTranscriber(audioConfig);
    
    // attach the transcriber to the conversation
    transcriber.joinConversationAsync(conversation,
    function () {
        // add first participant using voiceSignature created in enrollment step
        var user1 = sdk.Participant.From("user1@example.com", "en-us", voiceSignatureStringUser1);
        conversation.addParticipantAsync(user1,
        function () {
            // add second participant using voiceSignature created in enrollment step
            var user2 = sdk.Participant.From("user2@example.com", "en-us", voiceSignatureStringUser2);
            conversation.addParticipantAsync(user2,
            function () {
                transcriber.sessionStarted = function(s, e) {
                console.log("(sessionStarted)");
                };
                transcriber.sessionStopped = function(s, e) {
                console.log("(sessionStopped)");
                };
                transcriber.canceled = function(s, e) {
                console.log("(canceled)");
                };
                transcriber.transcribed = function(s, e) {
                console.log("(transcribed) text: " + e.result.text);
                console.log("(transcribed) speakerId: " + e.result.speakerId);
                };
    
                // begin conversation transcription
                transcriber.startTranscribingAsync(
                function () { },
                function (err) {
                    console.trace("err - starting transcription: " + err);
                });
        },
        function (err) {
            console.trace("err - adding user1: " + err);
        });
    },
    function (err) {
        console.trace("err - adding user2: " + err);
    });
    },
    function (err) {
    console.trace("err - " + err);
    });
}()); 
```