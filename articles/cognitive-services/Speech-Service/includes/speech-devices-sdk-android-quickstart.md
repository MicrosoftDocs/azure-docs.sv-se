---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 3b473d82eb879955a6cbdce58ba66176f5c6d6f5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214167"
---
I den här snabb starten lär du dig att använda tal enheter SDK för Android för att bygga en tal aktive rad produkt eller använda den som en [Avskrifts](../conversation-transcription.md) enhet för konversation.

Den här guiden kräver ett [Azure Cognitive Services](../overview.md#try-the-speech-service-for-free) -konto med en tjänst resurs för tal.

Käll koden för exempel programmet ingår i tal enheter SDK. Det finns också [på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar använda tal enheter SDK måste du:

- Följ instruktionerna som medföljer ditt [utvecklings paket](../get-speech-devices-sdk.md) för att starta enheten.

- Hämta den senaste versionen av [tal enheter SDK](../speech-devices-sdk.md)och extrahera zip-filen till din arbets katalog.

  > [!NOTE]
  > Den här snabb starten förutsätter att appen extraheras till C:\SDSDK\Android-Sample-Release

- Så här hämtar du en [Azure-prenumerations nyckel för tal tjänsten](../overview.md#try-the-speech-service-for-free)

- Om du planerar att använda svars avskriften måste du använda en [cirkulär mikrofon enhet](../get-speech-devices-sdk.md) och den här funktionen är för närvarande endast tillgänglig för "en-US" och "zh-cn" i regionerna, "Central" och "asienöstra". Du måste ha en tal nyckel i någon av dessa regioner för att kunna använda konversations avskrifter.

- Om du planerar att använda tal tjänsten för att identifiera avsikter (eller åtgärder) från användarens yttranden behöver du en [Luis-prenumeration (Language Understanding service)](../../luis/luis-how-to-azure-subscription.md) . Om du vill veta mer om LUIS och avsikts igenkänning läser du [känna igen tal avsikter med Luis, C#](../how-to-recognize-intents-from-speech-csharp.md).

  Du kan [skapa en enkel Luis-modell](../../luis/index.yml) eller använda exempel Luis-modellen LUIS-example.jspå. Exempel modellen för LUIS finns på [hämtnings platsen för tal enheter SDK](https://aka.ms/sdsdk-luis). Om du vill överföra modellens JSON-fil till [Luis-portalen](https://www.luis.ai/home)väljer du **Importera ny app** och väljer sedan JSON-filen.

- Installera [Android Studio](https://developer.android.com/studio/) och [VYSOR](https://vysor.io/download/) på din dator.

## <a name="set-up-the-device"></a>Konfigurera enheten

1. Starta Vysor på datorn.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Enheten ska visas under **Välj en enhet**. Välj knappen **Visa** bredvid enheten.

1. Anslut till ditt trådlösa nätverk genom att välja mappikonen och välj sedan **Inställningar**  >  **WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Om ditt företag har principer för att ansluta enheter till Wi-Fi systemet måste du skaffa MAC-adressen och kontakta IT-avdelningen om hur du ansluter den till företagets Wi-Fi.
   >
   > Du hittar MAC-adressen för dev kit genom att välja ikonen filmapp på Skriv bordet i dev kit.
   >
   > ![Filmapp för Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Välj **inställningar**. Sök efter "Mac-adress" och välj sedan **MAC-adress**  >  **Advanced WLAN**. Skriv ned MAC-adressen som visas längst ned i dialog rutan.
   >
   > ![Vysor MAC-adress](../media/speech-devices-sdk/qsg-11.png)
   >
   > Vissa företag kan ha en tids gräns för hur länge en enhet kan anslutas till Wi-Fi systemet. Du kan behöva utöka dev-paketets registrering med Wi-Fi systemet efter ett angivet antal dagar.

## <a name="run-the-sample-application"></a>Köra exempelprogrammet

Om du vill verifiera installationen av Development Kit, skapa och installera exempel programmet:

1. Starta Android Studio.

1. Välja **Öppna ett befintligt Android Studio-projekt**.

   ![Android Studio – öppna ett befintligt projekt](../media/speech-devices-sdk/qsg-5.png)

1. Gå till C:\SDSDK\Android-Sample-Release\example. Klicka på **OK** för att öppna exempel projektet.

1. Konfigurera gradle för att referera till tal-SDK: n. Följande filer finns under **Gradle-skript** i Android Studio.

    Uppdatera **build. gradle (projekt: exempel)**, allprojects-blocket måste matcha nedan, genom att lägga till maven-raderna.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Uppdatera **build. gradle (modulen: app)** genom att lägga till den här raden i avsnittet beroenden. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
    ```
    
1. Lägg till din tal prenumerations nyckel i käll koden. Om du vill prova avsikts igenkänning lägger du också till din [language Understanding tjänst](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerations nyckel och program-ID.

   För tal-och LUIS går din information till MainActivity. java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Om du använder en konversations avskrift krävs även din information om din röst nyckel och region i konversationen. java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Standard nyckelordet är "Computer". Du kan också prova något av de andra angivna nyckelorden, t. ex. "Machine" eller "Assistant". Resursfiler för dessa alternativa nyckelord finns i avsnittet om tal enheter SDK i mappen nyckelord. Till exempel innehåller C:\SDSDK\Android-Sample-Release\keyword\Computer de filer som används för nyckelordet "dator".

   > [!TIP]
   > Du kan också [skapa ett anpassat nyckelord](../custom-keyword-basics.md).

   Om du vill använda ett nytt nyckelord uppdaterar du följande två rader i `MainActivity.java` och kopierar nyckelords paketet till din app. Om du till exempel vill använda nyckelordet "dator" från nyckelords paketet kws-machine.zip:

   - Kopiera nyckelords paketet till mappen "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets \" .
   - Uppdatera `MainActivity.java` med nyckelordet och paket namnet:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Uppdatera följande rader, som innehåller geometri inställningarna för mikrofonen:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   I den här tabellen visas de värden som stöds:

   | Variabel | Innebörd | Tillgängliga värden |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Konfiguration av fysisk mikrofon | För ett cirkulärt dev-paket: `Circular6+1` |
   |          |         | För en linjär dev-sats: `Linear4` |
   | `SelectedGeometry` | Konfiguration av program varu MIC | För ett cirkulärt dev-paket som använder alla MICS: `Circular6+1` |
   |          |         | För ett cirkulärt dev-paket som använder fyra MICS: `Circular3+1` |
   |          |         | För en linjär dev-sats som använder alla MICS: `Linear4` |
   |          |         | För en linjär dev-sats som använder två MICS: `Linear2` |

1. Om du vill skapa programmet väljer du **kör app** på menyn **Kör** . Dialog rutan **Välj distributions mål** visas.

1. Välj din enhet och välj sedan **OK** för att distribuera programmet till enheten.

   ![Dialog rutan Välj distributions mål](../media/speech-devices-sdk/qsg-7.png)

1. Exempel programmet för tal enheter SDK startar och visar följande alternativ:

   ![Exempel program och alternativ för exempel på tal enheter SDK](../media/speech-devices-sdk/qsg-8.png)

1. Prova den nya demonstrationen av konversations avskrift. Börja skriva med "Starta session". Som standard är alla gäst. Men om du har deltagares röst-signaturer kan de placeras i en fil `/video/participants.properties` på enheten. Om du vill generera röst signaturen tittar du på Skicka [konversationer (SDK)](../how-to-use-conversation-transcription.md).

   ![Avskrifts program för demo konversation](../media/speech-devices-sdk/qsg-15.png)

1. Olika!

## <a name="troubleshooting"></a>Felsökning

Om du inte kan ansluta till tal enheten. Skriv följande kommando i kommando tolkens fönster. Den kommer att returnera en lista över enheter:

```powershell
 adb devices
```

> [!NOTE]
> Det här kommandot använder Android fel söknings bryggan, `adb.exe` som är en del av den Android Studio installationen. Det här verktyget finns i C:\Users \[ användar namn] \AppData\Local\Android\Sdk\platform-tools. Du kan lägga till den här katalogen i sökvägen så att det blir enklare att anropa `adb` . Annars måste du ange den fullständiga sökvägen till din installation av adb.exe i varje kommando som anropar `adb` .
>
> Om du ser ett fel `no devices/emulators found` kan du kontrol lera att USB-kabeln är ansluten och se till att en hög kvalitets kabel används.