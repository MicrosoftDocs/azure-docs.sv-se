---
title: Skapa en anpassad simulerad enhet – Azure | Microsoft Docs
description: I den här självstudien använder du enhetssimulering för att skapa en anpassad simulerad enhet för användning i simuleringar.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 1fe86aef832223a7485036343b4b12d8bb526e06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852395"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>Självstudie: Skapa en anpassad simulerad enhet

I den här självstudien använder du enhetssimulering för att skapa en anpassad simulerad enhet för användning i simuleringar. För att komma igång med enhetssimulering kan du använda en av de inkluderade exemplen på simulerade enheter. Du kan även skapa anpassade simulerade enheter enligt beskrivningen i den här artikeln. Fler alternativ för anpassning finns i [Skapa en avancerad enhetsmodell](iot-accelerators-device-simulation-advanced-device.md).

I den här kursen får du:

>[!div class="checklist"]
> * Visa en lista över dina modeller för simulerad enhet
> * Skapa en anpassad simulerad enhet
> * Klona en enhetsmodell
> * Ta bort en enhetsmodell

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att följa den här självstudien behöver du en distribuerad instans av enhetssimulering i din Azure-prenumeration.

Om du inte redan har distribuerat enhets simuleringen kan du läsa [distribuera enhets simulering](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) på GitHub.

## <a name="view-your-device-models"></a>Visa dina enhetsmodeller

Välj **Enhetsmodeller** på menyraden. Sidan **Enhetsmodeller** visar en lista över alla tillgängliga enhetsmodeller i den här instansen av enhetssimulering:

![Enhetsmodeller](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>Skapa en enhetsmodell

Klicka på **+ Lägg till enhetsmodeller** i det övre högra hörnet på sidan:

![Lägga till enhetsmodell](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

I den här självstudien skapar du ett simulerat kylskåp som skickar data för både temperatur och luftfuktighet.

Fyll i formuläret med följande information:

| Inställning             | Värde                                                |
| ------------------- | ---------------------------------------------------- |
| Enhetsmodellnamn   | Kylskåp                                         |
| Modellbeskrivning   | Ett kylskåp med temperatur- och luftfuktighetssensorer |
| Version             | 1.0                                                  |

> [!NOTE]
> Enhetsmodellnamnet måste vara unikt.

Klicka på **+ Lägg till datapunkt** för att lägga till datapunkter för temperatur och luftfuktighet med följande värden:

| Datapunkt          | Beteende        | Minvärde | Maxvärde | Enhet |
| ------------------- | --------------- | --------- | --------- | ---- |
| Temperatur         | Slumpmässig          | -50       | 100       | F    |
| Luftfuktighet            | Slumpmässig          | 0         | 100       | %    |

Klicka på **Spara** för att spara enhetsmodellen.

![Skapa enhetsmodell](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

Kylskåpet ingår nu i listan över enhetsmodeller. Du kan behöva klicka på **Nästa** för att gå till en annan sida och se kylskåpet.

## <a name="clone-a-device-model"></a>Klona en enhetsmodell

Genom att klona en enhetsmodell kan du skapa en kopia av en befintlig enhetsmodell. Du kan sedan redigera kopian för att uppfylla dina specifika behov. Kloning sparar tid när du behöver skapa liknande enhetsmodeller.

Om du vill klona en enhetsmodell markerar du kryssrutan intill modellen och klickar sedan på **Klona** i åtgärdsfältet:

![Skärm bild som markerar den markerade modellen och klonings knappen.](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>Ta bort en enhetsmodell

Du kan ta bort anpassade enhetsmodeller. Om du vill ta bort en enhetsmodell markerar du kryssrutan intill modellen och klickar sedan på **Ta bort** i åtgärdsfältet:

![Ta bort enhetsmodell](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att skapa, klona och ta bort en anpassad enhetsmodell. Mer information om enhetsmodeller finns i följande instruktionsartikel:

> [!div class="nextstepaction"]
> [Skapa en avancerad enhetsmodell](iot-accelerators-device-simulation-advanced-device.md)