---
title: Felsöka talet SDK-tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal-SDK.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "74815407"
---
# <a name="troubleshoot-the-speech-sdk"></a>Felsöka Speech SDK

Den här artikeln innehåller information som hjälper dig att lösa problem som kan uppstå när du använder tal-SDK.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Fel: WebSocket-uppgraderingen misslyckades med ett autentiseringsfel (403)

Du kanske har fel slut punkt för din region eller tjänst. Kontrol lera URI: n för att kontrol lera att den är korrekt.

Det kan också finnas ett problem med din prenumerations nyckel eller autentiseringstoken. Mer information finns i nästa avsnitt.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>Fel: HTTP 403 förbud eller HTTP 401 obehörig

Det här felet beror ofta på autentiseringsproblem. Anslutnings begär Anden utan giltig `Ocp-Apim-Subscription-Key` eller `Authorization` rubrik avvisas med statusen 403 eller 401.

* Om du använder en prenumerations nyckel för autentisering kan du se felet på grund av följande:

    - Prenumerations nyckeln saknas eller är ogiltig
    - Du har överskridit din prenumerations användnings kvot

* Om du använder en autentiseringstoken för autentisering kan du se felet på grund av följande:

    - Autentiseringstoken är ogiltig
    - Autentiseringstoken har upphört att gälla

### <a name="validate-your-subscription-key"></a>Verifiera din prenumerations nyckel

Du kan kontrol lera att du har en giltig prenumerations nyckel genom att köra något av följande kommandon.

> [!NOTE]
> Ersätt `YOUR_SUBSCRIPTION_KEY` och `YOUR_REGION` med din egen prenumerations nyckel och tillhör ande region.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

Om du har angett en giltig prenumerations nyckel returnerar kommandot en autentiseringstoken, annars returneras ett fel.

### <a name="validate-an-authorization-token"></a>Verifiera en autentiseringstoken

Om du använder en autentiseringstoken för autentisering kan du köra ett av följande kommandon för att kontrol lera att autentiseringstoken fortfarande är giltig. Token är giltiga i 10 minuter.

> [!NOTE]
> Ersätt `YOUR_AUDIO_FILE` med sökvägen till den förspelade ljud filen. Ersätt `YOUR_ACCESS_TOKEN` med den autentiseringstoken som returnerades i föregående steg. Ersätt `YOUR_REGION` med rätt region.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

Om du har angett en giltig autentiseringstoken returnerar kommandot avskriften för ljud filen, annars returneras ett fel.

---

## <a name="error-http-400-bad-request"></a>Fel: HTTP 400 Felaktig begäran

Det här felet uppstår vanligt vis när begär ande texten innehåller ogiltiga ljud data. Det finns endast stöd för WAV-format. Kontrol lera också begärans rubriker för att se till att du anger lämpliga värden för `Content-Type` och `Content-Length` .

## <a name="error-http-408-request-timeout"></a>Fel: tids gräns för HTTP 408-begäran

Felet beror förmodligen på att inga ljud data skickas till tjänsten. Felet kan också bero på nätverks problem.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>"RecognitionStatus" i svaret är "InitialSilenceTimeout"

Det här problemet beror vanligt vis på ljuddata. Du kan se det här felet på grund av följande:

* Det finns en lång utsträckning av tystnads tiden i början av ljudet. I så fall stoppar tjänsten igenkänningen efter några sekunder och returnerar `InitialSilenceTimeout` .

* Ljudet använder ett codec-format som inte stöds, vilket gör att ljud data behandlas som tystnad.

## <a name="next-steps"></a>Nästa steg

* [Läs viktig information](releasenotes.md)
