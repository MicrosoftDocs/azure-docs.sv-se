---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 167e33ff4a3af463e2537e2714e9e9bf5e125b61
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947263"
---
Behållaren tillhandahåller WebSocket-baserade frågor för slut punkts-API: er som nås via [tal-SDK: n](../index.yml). Som standard använder tal-SDK online Speech Services. Om du vill använda behållaren måste du ändra initierings metoden.

> [!TIP]
> När du använder tal-SDK med behållare behöver du inte ange resurs [prenumerations nyckeln för Azure tal eller en token för autentisering](../rest-speech-to-text.md#authentication).

Se exemplen nedan.

# <a name="c"></a>[C#](#tab/csharp)

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Använda det här anropet med behållar [värden](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Ändra från att använda det här Azure-moln initierings anropet:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Använda det här anropet med behållarens [slut punkt](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig):

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
