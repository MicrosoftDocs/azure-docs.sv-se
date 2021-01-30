---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 39cead8306ece6638f85813efc318dd41a6faf8c
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214706"
---
I den här snabb starten lär du dig att använda tal enheter SDK för Linux för att bygga en tal aktive rad produkt eller använda den som en [Avskrifts](../conversation-transcription.md) enhet för konversation. För närvarande stöds endast [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) .

Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Den här guiden kräver ett [Azure Cognitive Services](../overview.md#try-the-speech-service-for-free) -konto med en tjänst resurs för tal. 

Käll koden för [exempel programmet](https://aka.ms/sdsdk-download-JRE) ingår i tal enheter SDK. Det finns också [på GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* Operativ system: 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Endast [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) .
* En Azure-prenumerationsnyckel för tjänsten Speech. [Skaffa en kostnadsfritt](../overview.md#try-the-speech-service-for-free).
* Ladda ned den senaste versionen av [tal enheter SDK](https://aka.ms/sdsdk-download-JRE) för Java och extrahera zip-filen till din arbets katalog.
   > [!NOTE]
   > Den här snabb starten förutsätter att appen extraheras till/home/wcaltest/JRE-Sample-Release

Se till att dessa beroenden är installerade innan du startar Sol förmörkelse.

* I Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* På Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

* På RHEL/CentOS:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - På RHEL/CentOS 7 följer du anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

Konversations avskrift är för närvarande endast tillgängligt för "en-US" och "zh-CN" i regionerna "Central" och "asienöstra". Du måste ha en tal nyckel i någon av dessa regioner för att kunna använda konversations avskrifter.

Om du planerar att använda de avsikter behöver du en LUIS-prenumeration [(Language Understanding service)](../../luis/luis-how-to-azure-subscription.md) . Om du vill veta mer om LUIS och avsikts igenkänning läser du [känna igen tal avsikter med Luis, C#](../how-to-recognize-intents-from-speech-csharp.md). En [exempel modell för Luis](https://aka.ms/sdsdk-luis) är tillgänglig för den här appen.

## <a name="create-and-configure-the-project"></a>Skapa och konfigurera projektet

1. Starta Eclipse.

1. I fältet **arbets yta** i **sol förmörkelses IDE-start**, anger du namnet på en ny arbets ytans katalog. Välj sedan **Starta**.

   ![Skärm bild som visar Start programmet för Sol förmörkelse.](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Efter en liten stund visas huvudfönstret i Eclipse IDE. Stäng välkomstskärmen om en sådan visas.

1. Skapa ett nytt projekt från meny raden för Sol förmörkelse genom att välja **Arkiv**  >  **nytt**  >  **Java-projekt**. Om det inte är tillgängligt väljer du **projekt** och sedan **Java-projekt**.

1. Guiden **nytt Java-projekt** startar. **Bläddra** efter exempel projektets plats. Välj **Slutför**.

   ![Skärmbild av guiden Nytt Java-projekt](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Konfigurera**  >  **konvertera till Maven-projekt** från snabb menyn. Välj **Slutför**.

   ![Skärmbild av Paketutforskaren](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Öppna filen pom.xml och redigera den.

    I slutet av filen, före stängnings tag gen `</project>` , skapa `repositories` och `dependencies` -element, som visas här, och se till att de matchar den `version` aktuella versionen:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.15.0</version>
        </dependency>
    </dependencies>
   ```

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Egenskaper** och sedan **Kör/Felsök inställningar**  >  **ny...** > **Java-program**. 

1. Fönstret **Redigera konfiguration** visas. I fältet **namn** anger du **main** och använder **Sök** efter **huvud klassen** för att hitta och välja **com. Microsoft. cognitiveservices. Speech. Samples. FunctionsList**.

   ![Skärm bild av redigera start konfiguration](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Kopiera ljud binärfilerna för mål arkitekturen från **Linux-arm** eller **linux-x64** till Java-projektets plats, t. ex. **/Home/wcaltest/JRE-Sample-release**

1. I fönstret **Redigera konfiguration** väljer du också sidan **miljö** och **ny**. Fönstret **ny miljö variabel** visas. I fältet **namn** anger du **LD_LIBRARY_PATH** och i fältet **värde** anger du mappen som innehåller *. so-filer, till exempel **/Home/wcaltest/JRE-Sample-release**

1. Kopiera `kws.table` och `participants.properties` till Project-mappens **mål/klasser**


## <a name="configure-the-sample-application"></a>Konfigurera exempelprogrammet

1. Lägg till din tal prenumerations nyckel i käll koden. Om du vill prova avsikts igenkänning lägger du också till din [language Understanding tjänst](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) prenumerations nyckel och program-ID.

   För tal-och LUIS går din information till `FunctionsList.java` :

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Om du använder en konversations avskrift krävs även din information om din röst nyckel och region i `Cts.java` :

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. Standard nyckelordet (Keyword) är "Computer". Du kan också prova något av de andra angivna nyckelorden, t. ex. "Machine" eller "Assistant". Resursfiler för dessa alternativa nyckelord finns i avsnittet om tal enheter SDK i mappen nyckelord. Innehåller till exempel `/home/wcaltest/JRE-Sample-Release/keyword/Computer` de filer som används för nyckelordet "dator".

   > [!TIP]
   > Du kan också [skapa ett anpassat nyckelord](../custom-keyword-basics.md).

    Om du vill använda ett nytt nyckelord uppdaterar du följande rad i `FunctionsList.java` och kopierar nyckelordet till din app. Om du till exempel vill använda nyckelordet "dator" från nyckelords paketet `machine.zip` :

   * Kopiera `kws.table` filen från zip-paketet till projektmappen **/-klasserna** i Project-mappen.

   * Uppdatera `FunctionsList.java` med nyckelords namnet:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Köra exempel programmet från Sol förmörkelse

1. **Kör**  >  **Kör** på Meny raden för Sol förmörkelse 

1. Exempel programmet för tal enheter SDK startar och visar följande alternativ:

   ![Skärm bild som visar ett exempel på ett exempel på ett tal för exempel program och alternativ.](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Prova den nya demonstrationen av **konversations avskrift** . Börja skriva med **session**  >  **Start**. Som standard är alla gäst. Men om du har deltagares röst under skrifter kan de placeras `participants.properties` i i projektmappen i Project **-** mappen. Om du vill generera röst signaturen tittar du på Skicka [konversationer (SDK)](../how-to-use-conversation-transcription.md).

   ![Skärm bild som visar ett program för demo konversations avskrifter.](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Skapa och kör fristående program

1. Högerklicka på ditt projekt i **Package Explorer**. Välj **Exportera**. 
1. **Export** fönstret visas. Expandera **Java** och välj **körbara jar-fil** och välj sedan **Nästa**.

   ![Skärm bild som visar export fönstret.](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. **KÖRBARA jar File export** -fönstret visas. Välj ett **export mål** för programmet och välj sedan **Slutför**.
 
   ![Skärm bild som visar körbara JAR File export-fönstret.](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Lägg till `kws.table` och `participants.properties` i målmappen som väljs ovan eftersom de här filerna behövs av programmet.

1. Ange LD_LIBRARY_LIB till den mapp som innehåller *. so-filer

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Köra det fristående programmet

     ```bash
     java -jar SpeechDemo.jar
     ```