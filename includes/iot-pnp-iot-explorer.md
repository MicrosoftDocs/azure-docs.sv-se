---
author: ericmitt
ms.author: ericmitt
ms.service: iot-pnp
ms.topic: include
ms.date: 07/13/2020
ms.openlocfilehash: 3ab9161c006a88a254b247a921512698ed46fa95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87352813"
---
1. Öppna Azure IoT Explorer.

1. På sidan **IoT Hub** , om du inte redan har lagt till en anslutning till din IoT-hubb, väljer du **+ Lägg till anslutning**. Ange anslutnings strängen för IoT-hubben som du skapade tidigare och välj **Spara**.

1. På sidan **IoT plug and Play inställningar** väljer du **+ Lägg till > lokal mapp** och väljer den lokala mappen *modeller* där du sparade dina modellvariabler.

1. På sidan **IoT Hub** klickar du på namnet på den hubb som du vill arbeta med. Du ser en lista över enheter som är registrerade i IoT Hub.

1. Klicka på **enhets-ID** för enheten som du skapade tidigare.

1. Menyn till vänster visar de olika typerna av information som är tillgänglig för enheten.

1. Välj **IoT plug and Play-komponenter** för att Visa modell information för enheten.

1. Du kan visa de olika komponenterna i enheten. Standard komponenten och eventuella ytterligare. Välj en komponent att arbeta med.

1. Välj sidan **telemetri** och välj sedan **Starta** för att visa de telemetridata som enheten skickar för den här komponenten.

1. Välj sidan **egenskaper (skrivskyddad)** om du vill visa de skrivskyddade egenskaperna som rapporter ATS för den här komponenten.

1. Välj sidan **egenskaper (skrivbar)** om du vill visa de skrivbara egenskaperna som du kan uppdatera för den här komponenten.

1. Välj en egenskap efter dess **namn**, ange ett nytt värde för den och välj **Uppdatera önskat värde**.

1. Visa det nya värdet genom att välja knappen **Uppdatera** .

1. Välj sidan **kommandon** om du vill visa alla kommandon för den här komponenten.

1. Välj det kommando som du vill testa ange parametern om det finns någon. Välj **kommandot Skicka** för att anropa kommandot på enheten. Du kan se att enheten svarar på kommandot i kommando tolkens fönster där exempel koden körs.
