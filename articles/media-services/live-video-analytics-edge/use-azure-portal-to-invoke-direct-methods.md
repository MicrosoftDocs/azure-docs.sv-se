---
title: Använda Azure Portal för att anropa direkta metoder
description: Den här artikeln är en översikt som använder Azure Portal för att anropa direkta metoder.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492662"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Använda Azure Portal för att anropa direkta metoder

IoT Hub ger dig möjlighet att anropa [direkta metoder](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) på gräns enheter från molnet. LVA-modulen (live video analys i IoT Edge) visar flera [direkta metoder](./direct-methods.md) som kan användas för att definiera, distribuera och instansiera olika arbets flöden för att analysera direktsänd video.

I den här artikeln får du lära dig hur du anropar direkta metod anrop i real tids video analys för en IoT Edge modul via Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

* Du har live video analys på IoT Edge modul som körs på din Edge-enhet med antingen de metoder som beskrivs i [snabb start: Real video analys på IoT Edge](./get-started-detect-motion-emit-events-quickstart.md) eller med hjälp av [portalen.](./deploy-iot-edge-device.md)

* Du förstår [live video analys](./overview.md) och [Media Graph-konceptet](./media-graph-concept.md).

## <a name="invoking-direct-methods-via-azure-portal"></a>Anropa direkt metoder via Azure Portal

Var och en av de [direkta metoder](./direct-methods.md) som exponeras av lva-modulen kan anropas via Azure Portal. Stegen nedan ger information om en direkt metod. Du kan anropa andra direkta metoder med liknande steg. Varje direkt metod kräver dock en speciell JSON-text.

Använd `GraphTopologyList` metod anropet för att hämta en lista över alla Graph-topologier som för närvarande har distribuerats i live video analys i IoT Edge modul. Använd följande steg för att anropa den här direkta metoden:

1. Logga in på Azure Portal
1. Hitta den relevanta resurs gruppen från portalens start sida för att hitta din IoT Hub, eller om du vet att du IoT Hub, väljer du den.
    ![resurs grupp på portalens start sida](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. På sidan IoT Hub väljer du IoT Edge under automatisk enhets hantering för att visa de olika enhets-ID: na. Välj relevant enhets-ID för att visa en lista över modulerna som körs på enheten.
    ![Sidan IoT Hub](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Välj video analys i IoT Edge-modulen för att Visa konfigurations sidan.<br><br>
    ![Välj video analys i IoT Edge-modulen för att Visa konfigurations Sidan](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Välj på meny alternativet Direct-metod. <br><br>
    ![Klicka på meny alternativet direkt metod](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Du kan behöva lägga till ett värde i anslutnings Strängs avsnitten som du kan se på den aktuella sidan. Du behöver inte dölja eller ändra något i avsnittet inställnings namn. Det är OK att låta det vara offentligt.

1. Skriv *GraphTopologyList* i fältet **metod namn** .
1. Kopiera och klistra in JSON-filen nedan i fältet **nytto Last** .
    ```json
    {
    "@apiVersion": "2.0"
    }
    ```
1. Välj knappen **anropa metod** överst på sidan.<br><br>
    ![knappen anropa metod](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. Du bör se ett status 200-meddelande i **resultat** fältet.<br><br>
    ![tids gräns för anslutning](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Svar

| Villkor             | Statuskod | Detaljerad felkod |
|-----------------------|-------------|---------------------|
| Klart               | 200         | Ej tillämpligt                 |
| Allmänna användar fel   | 400 intervall   |                     |
| Allmänna Server fel | 500 intervall   |                     |

## <a name="next-steps"></a>Nästa steg

Du hittar fler direkta metoder på sidan [direkta metoder](./direct-methods.md) .

> [!NOTE]
> En graf-instans instansierar en speciell topologi, så se till att du har rätt frågetopologi uppsättning innan du skapar en diagram instans.

[Snabb start: identifiera händelser för motion-generering](./get-started-detect-motion-emit-events-quickstart.md) är en god referens för att förstå den exakta sekvensen av direkta metod anrop att göras.
