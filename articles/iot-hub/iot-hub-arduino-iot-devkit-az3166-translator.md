---
title: Gör Translator med Azure Functions, Cognitive Services, IoT-DevKit
description: Använd mikrofonen på en IoT-DevKit för att få ett röst meddelande och Använd sedan Azure Cognitive Services för att bearbeta den i översatt text på engelska
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96024171"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Använda IoT DevKit-AZ3166 med Azure Functions och Cognitive Services för att göra en språk översättare

I den här artikeln får du lära dig att göra IoT-DevKit som en språk översättare med hjälp av [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/). Den registrerar din röst och översätter den till engelsk text som visas på DevKit-skärmen.

[MXChip IoT-DevKit](https://aka.ms/iot-devkit) är en allt-i-ett-Arduino-kompatibelt kort med omfattande kring utrustning och sensorer. Du kan utveckla för IT-enheter med hjälp av [Azure IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT tools](https://aka.ms/azure-iot-tools) Extension Pack i Visual Studio Code. [Projekt katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) innehåller exempel program som hjälper dig att prototypa IoT-lösningar.

## <a name="before-you-begin"></a>Innan du börjar

Utför stegen i den här självstudien genom att först utföra följande uppgifter:

* Förbered din DevKit genom att följa stegen i [ansluta IoT DevKit-AZ3166 till Azure IoT Hub i molnet](./iot-hub-arduino-iot-devkit-az3166-get-started.md).

## <a name="create-azure-cognitive-service"></a>Skapa Azure-tjänsten för inlärning

1. Klicka på **skapa en resurs** i Azure Portal och Sök efter **tal**. Fyll i formuläret för att skapa Speech Service.
  ![Tal tjänst](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Gå till den Speech-tjänst som du precis har skapat och klicka på avsnittet **nycklar** för att kopiera och anteckna **KEY1** för DevKit som har åtkomst till den.
  ![Kopiera nycklar](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Öppna exempel projekt

1. Se till att din IoT-DevKit **inte är ansluten** till din dator. Starta VS Code först och Anslut sedan DevKit till datorn.

1. Klicka `F1` för att öppna paletten kommando, skriv och välj **Azure IoT Device Workbench: öppna exempel.**.. Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench-exempel hittar du **DevKit Translator** och klickar på **Öppna exempel**. Sedan väljer du standard Sök vägen för att ladda ned exempel koden.
  ![Öppna exempel](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Använda tal tjänsten med Azure Functions

1. I VS Code, klickar `F1` du på, skriver och väljer **Azure IoT Device Workbench: etablera Azure-tjänster...**. ![ Etablera Azure-tjänster](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Följ stegen för att slutföra etableringen av Azure IoT Hub och Azure Functions.
   ![Etablerings steg](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Anteckna det Azure IoT Hub-enhetsnamn som du skapade.

1. Öppna `Functions\DevKitTranslatorFunction.cs` och uppdatera följande rader med kod med enhets namnet och nyckeln för tal tjänsten som du har antecknat.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Klicka på `F1` , skriv och välj **Azure IoT Device Workbench: distribuera till Azure.**... Om VS Code ber om bekräftelse för omdistribution klickar du på **Ja**.
   ![Distribuera varning](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Kontrollera att distributionen lyckas.
   ![Distributionen lyckades](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. I Azure Portal går du till avsnittet **Functions Apps** och letar rätt på Azure Function-appen som precis har skapats. Klicka på `devkit_translator` och klicka sedan på **</> Hämta funktions webb** adress för att kopiera URL: en.
   ![Kopiera funktions webb adress](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Klistra in webb adressen i `azure_config.h` filen.
   ![Azure config](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Om Function-appen inte fungerar som den ska kan du läsa avsnittet [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) .

## <a name="build-and-upload-device-code"></a>Bygg och ladda upp enhets kod

1. Växla DevKit till **konfigurations läge** genom att:
   * Håll ned knapp **A**.
   * Tryck på och släpp **återställnings** knappen.

   På skärmen visas DevKit-ID och **konfiguration**.

   ![Konfigurations läge för DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Klicka på `F1` , skriv och välj **Azure IoT enhet Workbench: Konfigurera enhets inställningar... > anslutnings sträng för konfigurations enhet**. Välj **välj IoT Hub enhets anslutnings sträng** för att konfigurera den till DevKit.
   ![Konfigurera anslutnings sträng](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Meddelandet visas när det har slutförts.
   ![Anslutnings strängen har kon figurer ATS](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Klicka på `F1` igen, skriv och välj **Azure IoT enhet Workbench: Ladda upp enhets kod**. Den börjar kompilera och ladda upp koden till DevKit.
   ![Enhets uppladdning](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Testa projektet

Efter appinitieringen följer du instruktionerna på DevKit-skärmen. Standardkällspråket är kinesiska.

Så här väljer du ett annat språk för översättning:

1. Tryck på A-knappen för att starta konfigurationsläget.

2. Tryck på B-knappen för att rulla bland alla källspråk som stöds.

3. Tryck på knapp A för att bekräfta valet av käll språk.

4. Tryck på och håll ned B-knappen medan du talar, och släpp sedan B-knappen för att starta översättningen.

5. Den översatta texten på engelska visas på skärmen.

![Bläddra för att välja språk](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Översättnings resultat](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

På skärmen för översättningsresultat kan du:

- Trycka på A-knappen och B-knappen för att rulla och välja källspråk.

- Trycka på B-knappen för att tala. Skicka rösten och få översättningstext genom att släppa B-knappen.

## <a name="how-it-works"></a>Så här fungerar det

![Mini-lösning-röst-till-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT-DevKit registrerar din röst och publicerar sedan en HTTP-begäran för att utlösa Azure Functions. Azure Functions anropar API: t för Speech Translator i kognitiva tjänster för att utföra översättningen. När Azure Functions hämtar översättnings texten, skickar den ett C2D-meddelande till enheten. Sedan visas översättningen på skärmen.

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa [vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du använder IoT-DevKit som en översättare genom att använda Azure Functions och Cognitive Services. I den här instruktionen har du lärt dig hur du:

> [!div class="checklist"]
> * Automatisera moln regler med hjälp av Visual Studio Code Task
> * Konfigurera anslutnings sträng för Azure IoT-enheter
> * Distribuera Azure-funktionen
> * Testa översättningen av röst meddelanden

Gå vidare till de andra självstudierna för att lära dig:

> [!div class="nextstepaction"]
> [Ansluta IoT DevKit AZ3166 till Azure IoT Remote Monitor Solution Accelerator](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)