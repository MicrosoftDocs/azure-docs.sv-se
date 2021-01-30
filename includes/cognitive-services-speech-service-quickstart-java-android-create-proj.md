---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: b987f98281c298da2d634c686d740faf3dda3502
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214512"
---
1. Starta Android Studio och välj **starta ett nytt Android Studio projekt** i **välkomst** fönstret.

    ![Skärmbild av Android Studio-välkomstfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Guiden **Välj projekt** visas. Välj **telefon, surfplatta** och **Tom aktivitet** i rutan aktivitets val. Välj **Nästa**.

   ![Skärm bild av guiden Välj projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. På skärmen **Konfigurera ditt projekt** anger du *snabb start* som **namn** och anger *samples.Speech.cognitiveservices.Microsoft.com* som **paket namn**. Välj sedan en projekt katalog. För **lägsta API-nivå** väljer du **API 23: Android 6,0 (Marshmallow)**. Låt alla andra kryss rutor vara tydliga och välj **Slutför**.

   ![Skärm bild av guiden Konfigurera ditt projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Det tar en stund för Android Studio att förbereda det nya projektet för Android. Konfigurera sedan projektet så att du vet om Azure Cognitive Services Speech SDK och använder Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

Den aktuella versionen av Cognitive Services Speech SDK är 1.15.0.

Speech SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller nödvändiga bibliotek samt Android behörigheter som krävs.
Den finns i en maven-lagringsplats på https: \/ /csspeechstorage.blob.Core.Windows.net/Maven/.

Konfigurera ditt projekt för att använda Speech SDK. Öppna fönstret **projekt struktur** genom att välja **fil**  >  **projekt struktur** på Android Studio meny raden. Gör följande ändringar i fönstret **projekt struktur** :

1. I listan till vänster i fönstret väljer du **Projekt**. Redigera standard inställningarna för **biblioteks lagring** genom att lägga till ett kommatecken och vår maven-lagringsplats-URL inom enkla citat tecken: "https: \/ /csspeechstorage.blob.Core.Windows.net/maven/"

   ![Skärmbild av projektstrukturfönstret](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. På samma skärm väljer du **app** på vänster sida. Välj sedan fliken **Beroenden** högst upp i fönstret. Välj det gröna plus tecknet ( **+** ) och välj **beroende för bibliotek** på den nedrullningsbara menyn.

   ![Skärm bild av biblioteks beroende](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. I fönstret som visas anger du namn och version för tal SDK för Android, *com. Microsoft. cognitiveservices. Speech: client-SDK: 1.15.0*. Välj sedan **OK**.
   Tal-SDK bör läggas till i listan över beroenden nu, som du ser:

   ![Skärm bild av tal-SDK i listan över beroenden](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Välj fliken **Egenskaper** . För både **käll** -och **mål**-kompatibilitet väljer du **1,9**.

   ![Skärm bild av kompatibilitet och kompatibilitet för kompatibilitet](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Välj **OK** för att stänga **projekt struktur** fönstret och verkställa ändringarna i projektet.
