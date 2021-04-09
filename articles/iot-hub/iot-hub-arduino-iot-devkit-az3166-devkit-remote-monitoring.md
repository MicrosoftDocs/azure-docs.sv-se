---
title: Anslut MXChip IoT DevKit till Azure IoT Hub Remote Monitoring
description: I den här självstudien får du lära dig hur du skickar status för sensorer på IoT DevKit-AZ3166 till Azure IoT Remote Monitoring Solution Accelerator.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785024"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Ansluta MXChip IoT DevKit till Azure IoT Remote Monitor Solution Accelerator

I den här självstudien får du lära dig hur du kör en exempel-app på din DevKit för att skicka sensor data till din Azure IoT-lösning för fjärr styrnings lösningar.

[MXChip IoT-DevKit](https://aka.ms/iot-devkit) är en allt-i-ett-Arduino-kompatibelt kort med omfattande kring utrustning och sensorer. Du kan utveckla för den med hjälp av [Visual Studio Code Extension för Arduino](https://aka.ms/arduino). Och det levereras med en växande [projekt katalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) som hjälper dig att hjälpa dig att skapa prototyp sakernas internet (IoT)-lösningar som drar nytta av Microsoft Azure-tjänster.

## <a name="what-you-need"></a>Detta behöver du

Slutför [komma igång guiden](./iot-hub-arduino-iot-devkit-az3166-get-started.md) för att:

* Låt din DevKit vara ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

En aktiv Azure-prenumeration. Om du inte har någon kan du registrera dig via någon av följande två metoder:

* Aktivera ett [kostnads fritt 30-dagars utvärderings Microsoft Azure konto](https://azure.microsoft.com/free/)

* Anspråk på din [Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) om du är MSDN eller Visual Studio-prenumerant

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Skapa en lösnings Accelerator för Azure IoT-fjärrövervakning

1. Gå till [webbplatsen för Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/) och klicka på **skapa en ny lösning**.

   ![Välj Azure IoT Solution Accelerator-typ](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Som standard skapar det här exemplet en S2-IoT Hub när den skapar en IoT-lösning för övervakning av lösningar. Om den här IoT-hubben inte används med ett stort antal enheter, rekommenderar vi starkt att du degraderar det från S2 till S1 och tar bort IoT-lösningen för fjärrövervakning så att den relaterade IoT Hub också kan tas bort när du inte längre behöver den. 

2. Välj **fjärr styrning**.

3. Ange ett lösnings namn, Välj en prenumeration och en region och klicka sedan på **skapa lösning**. Det kan ta en stund att tillhandahålla lösningen.
  
   ![Skapa lösning](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. När etableringen är klar klickar du på **Starta**. Vissa simulerade enheter skapas för lösningen under etablerings processen. Klicka på **enheter** för att checka ut dem.

   ![Instrumentpanel](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Konsol](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klicka på **Lägg till en enhet**.

6. Klicka på **Lägg till ny** för **Anpassad enhet**.
  
   ![Lägg till en ny enhet](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klicka på **Låt mig definiera mitt eget enhets-ID**, ange `AZ3166` och klicka sedan på **skapa**.
  
   ![Skapa enhet med ID](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Anteckna **IoT Hub värdnamn** och klicka på **Slutför**.

## <a name="open-the-remotemonitoring-sample"></a>Öppna exemplet RemoteMonitoring

1. Koppla bort DevKit från datorn, om den är ansluten.

2. Starta VS Code.

3. Anslut DevKit till din dator. VS Code identifierar DevKit automatiskt och öppnar följande sidor:

   * Introduktions sidan för DevKit.
   * Arduino-exempel: praktiska exempel för att komma igång med DevKit.

4. Expandera exempel avsnittet för vänster **ARDUINO** , bläddra till **exempel för MXCHIP AZ3166 > AzureIoT** och välj **RemoteMonitoring**. Den öppnar ett nytt VS Code-fönster med en projektmapp i det.

   > [!NOTE]
   > Om du råkar stänga fönstret kan du öppna det igen. Använd `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) för att öppna kommando-paletten, Skriv **Arduino** och leta upp och välj **Arduino: exempel**.

## <a name="provision-required-azure-services"></a>Tillhandahåll nödvändiga Azure-tjänster

Kör uppgiften via `Ctrl+P` (MacOS:) i lösnings fönstret `Cmd+P` genom att ange `task cloud-provision` i text rutan tillhandahållen.

I VS Code-terminalen guidar en interaktiv kommando rad dig genom etableringen av de nödvändiga Azure-tjänsterna.

![Etablera Azure-resurser](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Bygg och ladda upp enhets koden

1. Använd `Ctrl+P` (MacOS: `Cmd + P` ) och ange **uppgiften konfiguration-enhets anslutning**.

2. Terminalen frågar om du vill använda en anslutnings sträng som hämtas från `task cloud-provision` steget. Du kan också ange din egen enhets anslutnings sträng genom att klicka på "Skapa ny..."

3. Terminalen anger att du vill ange konfigurations läge. Det gör du genom att hålla ned knapp A och sedan trycka och släppa återställnings knappen. Skärmen visar DevKit-ID och ' Configuration '.

   ![Ingående anslutnings sträng](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. När `task config-device-connection` du är klar klickar `F1` du för att läsa in vs Code-kommandon och väljer `Arduino: Upload` . VS Code börjar verifiera och ladda upp Arduino-skissen.
  
   ![Verifiering och uppladdning av Arduino-skissen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

DevKit startas om och börjar köra koden.

## <a name="test-the-project"></a>Testa projektet

När exempel appen körs skickar DevKit sensor data via WiFi till din Azure IoT-lösning för övervakning av fjärr styrning. Följ dessa steg om du vill se resultatet:

1. Gå till Azure IoT-lösningen för fjärrövervakning av lösningar och klicka på **instrument panel**.

2. I konsolen för fjärr styrning visas din DevKit-sensor status.

   ![Sensor data i Azure IoT Remote Monitoring Solution Accelerator](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Ändra enhets-ID

Om du vill ändra hårdkodad- **AZ3166** till ett anpassat enhets-ID i koden ändrar du den kodrad som visas i exemplet för [fjärrövervakning](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2).

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa [vanliga frågor och svar om IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller kontakta oss med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ansluter en DevKit-enhet till din Azure IoT-lösning för fjärrövervakning och visualiserar sensor data, så är följande förslag på nästa steg:

* [Översikt över Azure IoT Solution Accelerators](/azure/iot-suite/)

* [IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/)