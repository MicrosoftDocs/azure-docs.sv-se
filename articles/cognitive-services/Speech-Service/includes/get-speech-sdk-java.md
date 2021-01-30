---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 2ab636679e59536a2ddfaa8603dc2da45811cd2f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214699"
---
:::row:::
    :::column span="3":::
        Java SDK för Android paketeras som ett <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (Android-bibliotek) <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, som innehåller nödvändiga bibliotek och nödvändiga Android-behörigheter. Den finns i en maven-lagringsplats på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.15.0` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Om du vill använda paketet från Android Studio-projektet gör du följande ändringar:

1. I filen *build. gradle* för projekt nivå lägger du till följande i `repositories` avsnittet:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Lägg till följande i avsnittet i filen *build. gradle* för modulnivå `dependencies` :
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
  ```

Java SDK är också en del av [tal enhets-SDK: n](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Ytterligare resurser

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Android-Specific källkod för Java snabb start <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Java Runtime (JRE) snabb starts käll kod <span class="docon docon-navigate-external x-hidden-focus"></span></a>
