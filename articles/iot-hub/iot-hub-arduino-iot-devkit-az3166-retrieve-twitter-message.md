---
title: Hämta ett Twitter-meddelande med Azure Functions | Microsoft Docs
description: Använd rörelse sensorn för att identifiera skakning och använda Azure Functions för att hitta en slumpmässig tweet med en hashtagg som du anger
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147903"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Skaka, skaka om för en tweet--hämta ett Twitter-meddelande med Azure Functions

I det här projektet får du lära dig hur du använder rörelse sensorn för att utlösa en händelse med hjälp av Azure Functions. Appen hämtar en slumpmässig tweet med en #hashtag som du konfigurerar i din Arduino-skiss. Tweeten visas på DevKit-skärmen.

## <a name="what-you-need"></a>Detta behöver du

Slutför [komma igång guiden](./iot-hub-arduino-iot-devkit-az3166-get-started.md) för att:

* Låt dina DevKit vara anslutna till Wi-Fi.
* Förbered utvecklings miljön.

En aktiv Azure-prenumeration. Om du inte har någon kan du registrera dig via någon av följande metoder:

* Aktivera ett [kostnads fritt 30-dagars utvärderings Microsoft Azure konto](https://azure.microsoft.com/free/)
* Anspråk på din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är en MSDN-eller Visual Studio-prenumerant

## <a name="open-the-project-folder"></a>Öppna projektmappen

Börja med att öppna projektmappen. 

### <a name="start-vs-code"></a>Starta VS-kod

* Kontrol lera att din DevKit är ansluten till din dator.

* Starta VS Code.

* Anslut DevKit till din dator.

   > [!NOTE]
   > När du startar VS Code kan du få ett fel meddelande om att Arduino IDE-eller relaterat Board-paketet inte kan hittas. Om det här felet uppstår stänger du VS Code och startar Arduino IDE igen. VS Code ska nu hitta Arduino IDE-sökvägen korrekt.

### <a name="open-the-arduino-examples-folder"></a>Öppna mappen Arduino exempel

Expandera avsnittet exempel på vänster sida **ARDUINO** , bläddra till **exempel för MXCHIP AZ3166 > AzureIoT** och välj **ShakeShake**. Ett nytt VS Code-fönster öppnas och mappen Project visas. Om du inte kan se avsnittet MXCHIP AZ3166 kontrollerar du att enheten är korrekt ansluten och startar om Visual Studio Code.  
![mini-lösningen-exempel](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

Du kan också öppna exempel projektet från kommando paletten. Klicka på `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) för att öppna kommando-paletten, Skriv **Arduino** och leta upp och välj **Arduino: exempel**.

## <a name="provision-azure-services"></a>Etablera Azure-tjänster

Kör uppgiften via `Ctrl+P` (MacOS:) i lösnings fönstret `Cmd+P` genom att ange `task cloud-provision` .

I VS Code-terminalen guidar en interaktiv kommando rad dig genom etableringen av de nödvändiga Azure-tjänsterna:

![Skärm bild som visar Visual Studio Code Terminals interaktiva kommando rad.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Om sidan låser sig i inläsnings statusen när du försöker logga in på Azure läser du [steget "inloggnings sidan låser sig" i vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>Ändra #hashtag

Öppna `ShakeShake.ino` och leta efter den här kodraden:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Ersätt strängen `iot` inom klammerparenteserna med önskad hashtagg. DevKit hämtar senare en slumpmässig tweet som innehåller de hashtagg som du anger i det här steget.

## <a name="deploy-azure-functions"></a>Distribuera Azure Functions

Använd `Ctrl+P` (MacOS: `Cmd+P` ) för att köra `task cloud-deploy` för att börja Distribuera Azure Functionss koden:

![Skärm bild som visar Visual Studio-kod där du kan köra aktivitet Cloud-Deploy för att distribuera Azure Functions kod.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Ibland fungerar Azure-funktionen kanske inte korrekt. För att lösa det här problemet när det uppstår, kontrol lera [avsnittet "kompileringsfel" i vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Bygg och ladda upp enhets koden

Sedan skapar du och laddar upp enhets koden.

### <a name="windows"></a>Windows

1. Används `Ctrl+P` för att köra `task device-upload` .

2. Terminalen anger att du vill ange konfigurations läge. Så här gör du:

   * Håll ned knapp A

   * Push och släpp knappen Återställ.

3. Skärmen visar DevKit-ID och ' Configuration '.

### <a name="macos"></a>macOS

1. Sätt DevKit i konfigurations läge:

   Håll ned knapp A och tryck sedan på knappen Återställ. Skärmen visar "konfiguration".

2. Använd `Cmd+P` för att köra `task device-upload` för att ange anslutnings strängen som hämtas från `task cloud-provision` steget.

### <a name="verify-upload-and-run"></a>Verifiera, ladda upp och köra

Nu har anslutnings strängen angetts, den verifierar och laddar upp appen och kör den sedan. 

1. VS Code börjar verifiera och ladda upp Arduino-skissen till din DevKit:

   ![Skärm bild som visar Visual Studio Code-verifiering och uppladdning av Arduino-skissen.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. DevKit startas om och börjar köra koden.

Du kan få fel meddelandet "Error: AZ3166: unknowed Package". Det här felet uppstår när tavla-paketets index inte uppdateras korrekt. Lös problemet genom att kontrol lera ["okänt paket" i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

När appen har initierats klickar du på och släpper knappen A och skakar sedan försiktigt DevKit-tavlan. Den här åtgärden hämtar en slumpmässig tweet som innehåller den hashtagg som du angav tidigare. Inom några sekunder visas en tweet på din DevKit-skärm:

### <a name="arduino-application-initializing"></a>Arduino-programmet initieras...

![Arduino – program-initiera](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Tryck på en för att skaka...

![Tryck-vid-och-skakning](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Redo att skaka...

![Redo att skaka](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Bearbetar...

![Bearbetar](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>Tryck på B för att läsa...

![Tryck-B-till-läsning](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Visa en slumpmässig tweet...

![Visa – en-slumpmässig-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Tryck på knappen igen och skaka sedan för en ny tweet.
- Tryck på knappen B för att bläddra igenom resten av tweeten.

## <a name="how-it-works"></a>Så här fungerar det

![Diagram visar en mobil enhet som skickar en händelse till Azure I o T-hubben som utlöser en Azure Function-app för att begära en tweet, som skickas tillbaka till appen och vidarebefordras till hubben och till den mobila enheten.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino-skissen skickar en händelse till Azure-IoT Hub. Den här händelsen utlöser Azure Functions-appen. Azure Functions-appen innehåller logiken för att ansluta till Twitter-API och hämta en tweet. Sedan radbryts tweet-texten i ett C2D-meddelande (Cloud-to-Device) och skickas tillbaka till enheten.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Valfritt: Använd din egen Twitter Bearer-token

I test syfte använder detta exempel projekt en förkonfigurerad Twitter Bearer-token. Det finns dock en [hastighets gräns](https://dev.twitter.com/rest/reference/get/search/tweets) för varje Twitter-konto. Följ dessa steg om du vill överväga att använda en egen token:

1. Gå till [Twitter Developer-portalen](https://dev.twitter.com/) för att registrera en ny Twitter-app.

2. [Hämta konsument nyckel och konsument hemligheter](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) för din app.

3. Använd ett [verktyg](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) för att generera en Twitter Bearer-token från dessa två nycklar.

4. I [Azure Portal](https://portal.azure.com/){: Target = "_blank"} går du till **resurs gruppen** och hittar Azure-funktionen (typ: App Service) för ditt "skaka, skaka"-projekt. Namnet innehåller alltid "skaka..." nollängd.

   ![Skärm bild av Azure Portal visar en app service för projektet.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Uppdatera koden för `run.csx` i **functions > shakeshake-CS** med din egen token:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Skärm bild som visar C#-koden för funktionen där du kan ange din token.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Spara filen och klicka på **Kör**.

## <a name="problems-and-feedback"></a>Problem och feedback

Så här felsöker du problem eller ger feedback. 

### <a name="problems"></a>Problem

Ett problem som du kan se om skärmen visar "ingen tweets" medan varje steg har körts utan problem. Det här villkoret inträffar vanligt vis första gången du distribuerar och kör exemplet, eftersom funktions programmet kräver något från några sekunder till så mycket som en minut för att kall starta appen. 

Eller, när koden körs, finns det vissa signaler som orsakar en omstart av appen. När det här tillståndet inträffar kan enhetens app få en tids gräns för att hämta tweeten. I det här fallet kan du prova en eller båda av dessa metoder för att lösa problemet:

1. Klicka på knappen Återställ på DevKit för att köra enhets appen igen.

2. I [Azure Portal](https://portal.azure.com/)letar du reda på den Azure Functions app som du skapade och startar om den:

   ![Skärm bild som visar Azure Portal med en Azure Functions-app och knappen starta om.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Om andra problem uppstår kan du läsa [vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till din Azure IoT-lösning för övervakning av fjärr styrning och hämtar en tweet, så är det här det föreslagna nästa steg:

* [Översikt över Azure IoT Remote Monitoring Solution Accelerator](/azure/iot-suite/)