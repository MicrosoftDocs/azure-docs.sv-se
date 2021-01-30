---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 8f5a8aa2b949ddd07208cac054147596512f783f
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213629"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

> [!div class="checklist"]
> * [Skapa en Azure tal-resurs](../../../../overview.md#try-the-speech-service-for-free)
> * [Konfigurera utvecklings miljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md)
> * Skapa en robot som är ansluten till den [direkta linjens tal kanal](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Kontrol lera att du har åtkomst till en mikrofon för ljud inspelning
>
  > [!NOTE]
  > Se [listan över regioner som stöds för röst assistenter](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

## <a name="setup-your-environment"></a>Konfigurera din miljö

Uppdatera filen go. mod med den senaste SDK-versionen genom att lägga till den här raden
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.15.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod
Ersätt innehållet i käll filen (t. ex. `quickstart.go` ) med följande, som innehåller:

- "Main"-paket definition
- Importera nödvändiga moduler från tal-SDK
- variabler för att lagra bot-information som kommer att ersättas senare i den här snabb starten
- en enkel implementering med mikrofonen för ljud inspelning
- händelse hanterare för olika händelser som utförs under en tal interaktion

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Ersätt `YOUR_SUBSCRIPTION_KEY` värdena och `YOUR_BOT_REGION` med faktiska värden från tal resursen.

- Navigera till Azure Portal och öppna din tal resurs
- Under **nycklar och slut punkt** till vänster finns det två tillgängliga prenumerations nycklar
    - Använd antingen ett som `YOUR_SUBSCRIPTION_KEY` värde för ersättning
- Under **översikten** till vänster noterar du regionen och mappar den till regions-ID: n
    - Använd regions identifieraren som `YOUR_BOT_REGION` värde ersättning, till exempel: `"westus"` för **västra USA**

   > [!NOTE]
   > Se [listan över regioner som stöds för röst assistenter](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) och se till att dina resurser distribueras i någon av dessa regioner.

   > [!NOTE]
   > Information om hur du konfigurerar din bot finns i dokumentationen till bot Framework för [den direkta rad igenkännings kanalen](/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Kod förklaring
Tal prenumerations nyckeln och regionen krävs för att skapa ett tal konfigurations objekt. Konfigurationsobjektet krävs för att instansiera ett tal igenkännings objekt.

Tolks instansen visar flera olika sätt att känna igen tal. I det här exemplet känns tal kontinuerligt igen. Den här funktionen gör att röst tjänsten vet att du skickar många fraser för igenkänning och när programmet slutar att känna igen tal. När resultatet har ställts in, kommer koden att skriva dem till-konsolen.

## <a name="build-and-run"></a>Skapa och köra
Nu har du konfigurerat för att skapa projektet och testa din anpassade röst assistent med hjälp av tal tjänsten.
1. Bygg ditt projekt, t. ex. **"go Build"**
2. Kör modulen och tala en fras eller mening i enhetens mikrofon. Ditt tal överförs till den direkta rad igenkännings kanalen och skrivs till text, som visas som utdata.


> [!NOTE]
> Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]