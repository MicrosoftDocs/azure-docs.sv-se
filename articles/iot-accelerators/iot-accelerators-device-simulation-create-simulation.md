---
title: Skapa en ny IoT-simulering – Azure | Microsoft Docs
description: I den här självstudien använder du enhetssimulering för att skapa och köra en ny simulering.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: df658a95069f39026daab7afb14c5f1d0f47c5a5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713902"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Självstudie: Skapa och köra en IoT-enhetssimulering

I den här självstudien använder du enhetssimulering för att skapa och köra en ny IoT-simulering med hjälp av en eller flera simulerade enheter.

I den här kursen får du:

>[!div class="checklist"]
> * Visa alla aktiva och historiska simuleringar
> * Skapa och köra en ny simulering
> * Visa mått för en simulering
> * Stoppa en simulering

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att följa den här självstudien behöver du en distribuerad instans av enhetssimuleringen i din Azure-prenumeration.

Om du inte har distribuerat enhetssimuleringen ännu kan du se [Distribution av enhetssimulering](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) på GitHub.

## <a name="view-simulations"></a>Visa simuleringar

Välj **Simuleringar** på menyraden. Sidan **Simuleringar** visar information om alla tillgängliga simuleringar. Från den här sidan kan du se simuleringar som för närvarande körs och dem som kördes tidigare. Om du vill köra en tidigare simulering igen klickar du på simuleringspanelen så att simuleringsinformationen öppnas:

![Simuleringar](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Skapa en simulering

Skapa en simulering genom att klicka på **+ Ny simulering** i det övre högra hörnet.

En simulering består av en eller flera enhetsmodeller. Enhetsmodellen definierar beteendet, telemetrin och meddelandeformatet för den enhet som ska simuleras.

Lägg till en enhetsmodell genom att klicka på **+ Lägg till en enhetstyp** och välja enhetsmodellen i listan. Simuleringen kan ha fler än en enhetsmodell. Varje enhetsmodell kan ha olika antal enheter och meddelandefrekvens.

När formuläret för den nya simuleringen är klart klickar du på **Starta simulering** för att starta simuleringen.

Beroende på antalet simulerade enheter kan det ta några minuter för simuleringen att konfigureras och startas:

![Ny simulering](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Stoppa en simulering

När du klickar på **Starta simulering** visas sidan med simuleringsinformation. Den här sidan visar simuleringsstatistik och -mått live från IoT Hub. Du kan även navigera till den här sidan genom att klicka på simuleringspanelen på sidan **Simuleringar**.

Om du vill stoppa en simulering klickar du på **Stoppa simulering** i åtgärdsfältet längst uppe till höger.

![Stoppa simulering](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa, köra och stoppa en simulering. Du lärde dig även hur du visar simuleringsinformation. Om du vill veta mer om hur du kör simuleringar kan du fortsätta till nästa självstudie:

> [!div class="nextstepaction"]
> [Skapa en anpassad simulerad enhet](iot-accelerators-device-simulation-create-custom-device.md)
