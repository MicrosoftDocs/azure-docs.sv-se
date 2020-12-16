---
title: Anslut ditt Azure IoT Central-program med Dynamics 365 Field Services | Microsoft Docs
description: Lär dig hur du skapar en lösning från slut punkt till slut punkt med Azure IoT Central-och Dynamics 365-fälttjänster
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1d098f56bbadfe115620580c8d93fb6dd021550d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586682"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Bygg lösningen från slut punkt till slut punkt med Azure IoT Central-och Dynamics 365-fälttjänster 
Som ett verktyg kan du aktivera integrering av ditt Azure IoT Central-program till andra företags system. 

I en ansluten lösning för avfalls hantering kan du till exempel optimera sändningen av Last bilar för skräp insamling. Optimeringen kan göras baserat på IoT sensorer-data från anslutna avfalls lager platser. I ditt [IoT Central anslutet avfalls hanterings program](./tutorial-connected-waste-management.md) kan du konfigurera regler och åtgärder och ange att de ska utlösa skapandet av aviseringar i Dynamics Field service. Det här scenariot uppnås med hjälp av Power Automation, som kommer att konfigureras direkt i IoT Central för automatisering av arbets flöden mellan program och tjänster. Dessutom kan information skickas tillbaka till Azure IoT Central, baserat på tjänst aktiviteter i fält tjänst. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Så här ansluter du ditt Azure IoT Central-program med Dynamics 365 Field Services 

Du kan enkelt implementera integrerings processerna baserat på en ren konfigurations upplevelse:
* Azure IoT Central kan skicka information om enhets avvikelser till anslutna fält tjänster (som en IoT-avisering) för diagnos.
* Ansluten fält tjänst kan skapa ärenden eller arbets order som utlöses från enhets avvikelser.
* Med den anslutna fält tjänsten kan du schemalägga tekniker för inspektion för att förhindra drift stopp incidenter.
* Instrument panelen för Azure IoT Central-enheten kan uppdateras med relevant tjänst och schemaläggnings information.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [mallar för IoT Central myndigheter](./overview-iot-central-government.md)
* Läs mer om [IoT Central](../core/overview-iot-central.md)
* Läs mer om [Dynamics 365 Field Services](/dynamics365/field-service/cfs-iot-overview)
