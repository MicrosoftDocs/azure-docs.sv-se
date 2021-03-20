---
title: Ansluta en Azure Sphere enhet i Azure IoT Central | Microsoft Docs
description: Lär dig hur du ansluter en Azure Sphere-enhet (DevKit) till ett Azure IoT Central-program.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.author: sandeepu
author: sandeeppujar
ms.date: 04/30/2020
ms.custom: device-developer
ms.openlocfilehash: 770f6e56a669ab2d9b425a7a2879eeef5d37377b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92123431"
---
# <a name="connect-an-azure-sphere-device-to-your-azure-iot-central-application"></a>Ansluta en Azure Sphere-enhet till Azure IoT Central-programmet

*Den här artikeln gäller för enhets utvecklare.*

Den här artikeln visar hur du ansluter en Azure Sphere-enhet (DevKit) till ett Azure IoT Central-program.

Azure Sphere är en säker högnivåplattform för program med inbyggda funktioner för kommunikation och säkerhet för internetanslutna enheter. Den innehåller en säker, ansluten, korskopplad mikrostyrenhet enhet (MCU), ett anpassat operativ system med hög nivå Linux-baserat operativ system (OS) och en molnbaserad säkerhets tjänst som ger kontinuerlig och förnybar säkerhet. Mer information finns i [Vad är Azure Sphere?](/azure-sphere/product-overview/what-is-azure-sphere).

[Azure Sphere Development Kits](https://azure.microsoft.com/services/azure-sphere/get-started/) innehåller allt du behöver för att starta prototyper och utveckla Azure Sphere program. Azure IoT Central med Azure Sphere gör det möjligt att använda en stack från slut punkt till slut punkt för en IoT-lösning. Azure Sphere ger enhets support och IoT Central som en noll-kod, hanterad IoT-programplattform.

I den här instruktions artikeln kan du:

- Skapa en Azure Sphere enhet i IoT Central med hjälp av mallen för Azure Sphere DevKit-enhet från biblioteket.
- Förbered Azure Sphere DevKit-enhet för Azure IoT.
- Anslut Azure Sphere DevKit till Azure IoT Central.
- Visa Telemetrin från enheten i IoT Central.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln behöver du följande resurser:

- Ett Azure IoT Central-program.
- Visual Studio 2019, version 16,4 eller senare.
- En [Azure Sphere MT3620 Development Kit från Seeed Studios](/azure-sphere/hardware/mt3620-reference-board-design).

> [!NOTE]
> Om du inte har en fysisk enhet kan du efter det första steget hoppa över till det sista avsnittet för att testa en simulerad enhet.

## <a name="create-the-device-in-iot-central"></a>Skapa enheten i IoT Central

Så här skapar du en Azure Sphere enhet i IoT Central:

1. I ditt Azure IoT Central-program väljer du fliken **enhets mallar** och sedan **+ ny**. I avsnittet **Använd en aktuell enhets mall** väljer du **Azure Sphere exempel enhet**.

    :::image type="content" source="media/howto-connect-sphere/sphere-create-template.png" alt-text="Enhets mal len för Azure Sphere DevKit":::

1. I enhets mal len redigerar du vyn **Översikt** för att visa **temperatur** och **knapp tryckning**.

1. Välj visnings typ för **redigerings enhet och moln data** för att lägga till en annan vy som visar **status lampan** för Läs/skriv-egenskapen. Dra egenskapen **status lampa** till den tomma, prickade rektangeln på höger sida av formuläret. Välj **Spara**.

## <a name="prepare-the-device"></a>Förbered enheten

Innan du kan ansluta Azure Sphere DevKit-enheten till IoT Central måste du [Konfigurera enhets-och utvecklings miljön](https://github.com/Azure/azure-sphere-samples/tree/master/Samples/AzureIoT).

## <a name="connect-the-device"></a>Anslut enheten

Om du vill aktivera exemplet för att ansluta till IoT Central måste du [Konfigurera ett Azure IoT Central-program och sedan ändra exempel programmets manifest](https://aka.ms/iotcentral-sphere-git-readme).

## <a name="view-the-telemetry-from-the-device"></a>Visa Telemetrin från enheten

När enheten är ansluten till IoT Central kan du se Telemetrin på instrument panelen.

:::image type="content" source="media/howto-connect-sphere/sphere-view.png" alt-text="Instrument panel för Azure Sphere DevKit":::

## <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

Om du inte har någon fysisk Azure Sphere DevKit-enhet kan du skapa en simulerad enhet för att testa Azure IoT Central-programmet.

Så här skapar du en simulerad enhet:

- Välj **enheter > Azure IoT-sfär**
- Välj **+ Ny**.
- Ange ett unikt **enhets-ID** och ett eget **enhets namn**.
- Aktivera **simulerad** inställning.
- Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

Om du är enhets utvecklare är några förslag på nästa steg att:

- Läs om [enhets anslutning i Azure IoT Central](./concepts-get-connected.md)
- Lär dig hur du [övervakar enhets anslutningar med hjälp av Azure CLI](./howto-monitor-devices-azure-cli.md)