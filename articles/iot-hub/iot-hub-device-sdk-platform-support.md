---
title: 'Plattforms stöd för Azure IoT Device SDK: er | Microsoft Docs'
description: 'Enhets-SDK: er med öppen källkod finns tillgängliga på GitHub i C, .NET (C#), Java, Node.js och python för att ansluta enheter till Azure IoT Hub och enhets etablerings tjänsten (DPS).'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: b8f8403617a5fc8b442e8a3f74ab9226fe1479fe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588768"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Plattforms stöd för Azure IoT Device SDK: er

Microsoft strävar efter att ständigt utöka universum av Azure IoT Hub-kapabla enheter. Microsoft publicerar enhets-SDK: er med öppen källkod på GitHub för att kunna ansluta enheter till Azure IoT Hub och enhets etablerings tjänsten. Enhets-SDK: er är tillgängliga för C, .NET (C#), Java, Node.js och python. Microsoft testar varje SDK för att säkerställa att det körs på de konfigurationer som stöds och som beskrivs i avsnittet Microsoft-SDK: er [och enhets plattforms stöd](#microsoft-sdks-and-device-platform-support) .

Förutom enhets-SDK: erna tillhandahåller Microsoft flera andra vägar som gör det möjligt för kunder och utvecklare att ansluta sina enheter till Azure IoT:

* Microsoft samarbetar med flera partner företag för att hjälpa dem att publicera utvecklings paket, baserat på Azure IoT C SDK, för sina maskinvaruplattformar.

* Microsoft samarbetar med Microsoft-betrodda partners för att tillhandahålla en ständigt växande uppsättning enheter som har testats och certifierats för Azure IoT. En aktuell lista över dessa enheter finns i [katalogen Azure Certified för IoT-enheter](https://catalog.azureiotsolutions.com/).

* Microsoft tillhandahåller ett plattforms abstraktions lager (PAL) i Azure IoT Hub Device C SDK som hjälper utvecklare att enkelt identifiera SDK: n för plattformen. Mer information finns i [C SDK-porting-vägledningen](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Det här avsnittet innehåller information om de Microsoft-SDK: er och de plattforms konfigurationer som de stöder, samt var och en av de andra alternativen som anges ovan.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft-SDK: er och enhets plattforms stöd

Microsoft publicerar SDK: er med öppen källkod på GitHub för följande språk: C, .NET (C#), Node.js, Java och python. SDK: er och deras beroenden visas i det här avsnittet. SDK: er stöds på alla enhets plattformar som uppfyller dessa beroenden.

För var och en av de listade SDK: erna, Microsoft:

* Skapar och kör kontinuerligt tester mot den huvudsakliga grenen av relevant SDK i GitHub på flera populära plattformar.  För att tillhandahålla test täckning för olika kompilator versioner, testar vi vanligt vis mot den senaste LTS-versionen och den mest populära versionen.

* Tillhandahåller installations vägledning eller installations paket om tillämpligt.

* Har fullständigt stöd för SDK: er på GitHub med kod med öppen källkod, en väg för kund avgifter och produkt team engagemang med GitHub-problem.

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C Device SDK](https://github.com/Azure/azure-iot-sdk-c) testas med och stöder följande konfigurationer.

| Operativsystem                  | TLS-bibliotek                  | Ytterligare krav                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL eller BearSSL | Berkeley Sockets</br></br>POSIX (Portable Opera ting System Interface)                       |
| iOS 12,2            | OpenSSL                      | XCode som emuleras i OSX 10.13.4                                                               |
| Windows 10-serien   | SChannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [MXChip IoT dev-paket](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere OS     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 eller ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT Hub python-enhetens SDK](https://github.com/Azure/azure-iot-sdk-python) testas med och stöder följande konfigurationer.

| Operativsystem                  | Kompilatorn                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,5 eller senare |
| macOS High Sierra   | Python 2,7. *, 3,5 eller senare |
| Windows 10-serien   | Python 2,7. *, 3,5 eller senare |

Endast python-version 3.5.3 eller senare har stöd för asynkrona API: er, vi rekommenderar att du använder version 3,7 eller senare.

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .net (C#) Device SDK](https://github.com/Azure/azure-iot-sdk-csharp) testas med och stöder följande konfigurationer.

| Operativsystem                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop-och Server-SKU: er   | .NET Core 2,1, .NET Framework 4.5.1 eller .NET Framework 4,7 |

.NET SDK kan också användas med Windows IoT Core med [Azure Device agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) eller [en anpassad NTService som kan använda RPC för att kommunicera med UWP-program](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT Hub Node.js Device SDK](https://github.com/Azure/azure-iot-sdk-node) testas med och stöder följande konfigurationer.

| Operativsystem                  | Node-version    |
|---------------------|-----------------|
| Linux               | LTS och aktuell |
| Windows 10-serien   | LTS och aktuell |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java Device SDK](https://github.com/Azure/azure-iot-sdk-java) testas med och stöder följande konfigurationer.

| Operativsystem                     | Java-version |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10-serien x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Partner utvecklings paket som stöds

Microsoft samarbetar med olika partner för att tillhandahålla utvecklings paket för flera mikroprocessor arkitekturer. Dessa partner har hamnat på Azure IoT C SDK till sin plattform. Partner skapar och underhåller Platform Abstraction Layer (PAL) för SDK: n. Microsoft samarbetar med dessa partner för att ge utökad support.

| Partner             | Enheter                            | Länk                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP – Azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT-modem     | [Qualcomm LTE för IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| S:T mikroelektroniska | STM32L4-serien <br/> STM32F4-serien <br/>  STM32F7-serien <br/>  STM32L4 Discovery Kit for IoT-nod    | [X-KUB – AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO – AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Support](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF Start fönstret </br> CC3220S Start fönstret </br> CC3235SF Start fönstret </br> CC3235S Start fönstret </br> MSP432E4 Start fönstret | [Azure IoT-plugin-program för SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E-forum](https://e2e.ti.com) <br/> [TI E2E-forumet för CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E-forumet för MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Porting för Microsoft Azure IoT C SDK

Om din enhets plattform inte omfattas av något av de föregående avsnitten kan du överväga att använda Azure IoT C SDK. Att transportera C SDK: n kräver främst att du implementerar plattforms abstraktions lagret (PAL) för SDK: n. PAL definierar primitiver som ger en koppling mellan din enhet och högre nivå funktioner i SDK: n. Mer information finns i [rikt linjer för porting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft-partner och certifierade Azure IoT-enheter

Microsoft arbetar med ett antal partner för att kontinuerligt expandera Azure IoT-universum med Azure IoT-testade och certifierade enheter.

* Information om hur du bläddrar i Azure IoT Certified-enheter finns [Microsoft Azure Certified for IoT enhets katalog](https://catalog.azureiotsolutions.com/).

* Mer information om Azure-certifierad för IoT-eko system finns i [delta i det certifierade eko systemet Certified for IoT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Ansluta till IoT Hub utan SDK

Om du inte kan använda någon av IoT Hub enhets-SDK: er kan du ansluta direkt till IoT Hub med ett kommunikations protokoll som stöds. Mer information finns i [utveckla utan en Azure IoT SDK](iot-hub-devguide-no-sdk.md).

## <a name="support-and-other-resources"></a>Support och andra resurser

Om du får problem när du använder SDK: er för Azure IoT-enheter finns det flera sätt att söka efter stöd sammanfattande nedan. Fullständig information om alla support alternativ finns i [Azure IoT-support och hjälp alternativ](../iot-fundamentals/iot-support-help.md). 

**Rapporterings buggar** – buggar i enhets-SDK: erna kan rapporteras på sidan problem i relevant GitHub-projekt. Du kan snabbt göra sitt sätt från projektet i till produkt uppdateringar.

* [Problem med Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK-problem](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problem med Java SDK i Azure IoT Hub](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problem med Azure IoT Hub Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problem med Azure IoT Hub python SDK](https://github.com/Azure/azure-iot-sdk-python/issues)

**Tekniska frågor** – du kan ställa tekniska frågor om både [Microsoft Q&A](/answers/topics/azure-iot-sdk.html) och [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) med hjälp av taggen *Azure-IoT-SDK*.

**Microsofts kund support team** – användare som har ett [support](https://azure.microsoft.com/support/plans/) avtal kan engagera Microsofts kund support team genom att skapa en ny supportbegäran direkt från [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Funktions förfrågningar** – Azure IoT Feature-begäranden spåras via produktens [röst sida för användare](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="next-steps"></a>Nästa steg

* [SDK:er för tjänst och enhet](iot-hub-devguide-sdks.md)
* [Vägledning för porting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)