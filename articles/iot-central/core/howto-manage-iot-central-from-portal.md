---
title: Hantera IoT Central från Azure Portal | Microsoft Docs
description: Den här artikeln beskriver hur du skapar och hanterar dina IoT Central-program från Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 2af97206db00d683ab409710bc71a3b5048bf6ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658473"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Hantera IoT Central från Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa och hantera IoT Central-program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) kan du använda [Azure Portal](https://portal.azure.com) för att hantera dina program.

## <a name="create-iot-central-applications"></a>Skapa IoT Central program

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]


Om du vill skapa ett program går du till [Azure Portal](https://ms.portal.azure.com) och väljer **skapa en resurs**.

I **Sök i Marketplace** -fältet skriver du *IoT Central*:

![Hanterings Portal: Sök](media/howto-manage-iot-central-from-portal/image0a1.png)

Välj panelen **IoT Central program** i Sök resultaten:

![Hanteringsportal: Sök Resultat](media/howto-manage-iot-central-from-portal/image0b1.png)

Välj nu **skapa**:

![Hanterings Portal: IoT Central resurs](media/howto-manage-iot-central-from-portal/image0c1.png)

Fyll i alla fält i formuläret. Det här formuläret liknar det formulär som du fyller i för att skapa program på webbplatsen för [Azure IoT Central Application Manager](https://aka.ms/iotcentral) . Mer information finns i snabb starten för att [skapa ett IoT Central-program](quick-deploy-iot-central.md) .

![Skapa IoT Central formulär](media/howto-manage-iot-central-from-portal/image6a.png)

**Plats** är den [geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill skapa ditt program. Normalt bör du välja den plats som är fysiskt närmast dina enheter för att få optimala prestanda. Azure IoT Central är för närvarande tillgängligt i regionerna **Australien**, **Asien och Stillahavsområdet**, **Europa**, **USA**, **Storbritannien** och **Japan** . När du har valt en plats kan du inte flytta programmet till en annan plats senare.

När du har fyllt i alla fält väljer du **skapa**.

## <a name="manage-existing-iot-central-applications"></a>Hantera befintliga IoT Central-program

Om du redan har ett Azure IoT Central-program kan du ta bort det eller flytta det till en annan prenumeration eller resurs grupp i Azure Portal.

> [!NOTE]
> Program som har skapats med den *kostnads fria* planen kräver inte några Azure-prenumerationer och därför hittar du inte dem i din Azure-prenumeration på Azure Portal. Du kan bara se och hantera kostnads fria appar från IoT Central Portal.

Kom igång genom att välja **alla resurser** i portalen. Välj **Visa dolda typer** och börja skriva namnet på programmet i **Filtrera efter namn** för att hitta det. Välj sedan det IoT Central program som du vill hantera.

Välj **IoT Central programmets URL** för att navigera till programmet:

![Skärm bild som visar sidan "Översikt" med IoT Central programmets URL markerat.](media/howto-manage-iot-central-from-portal/image3.png)

Om du vill flytta programmet till en annan resurs grupp väljer du **ändra** bredvid resurs gruppen. På sidan **Flytta resurser** väljer du den resurs grupp som du vill flytta programmet till:

![Skärm bild som visar sidan "Översikt" där "resurs grupp (ändra)" har marker ATS.](media/howto-manage-iot-central-from-portal/image4a.png)

Om du vill flytta programmet till en annan prenumeration väljer du  **ändra** bredvid prenumerationen. På sidan **Flytta resurser** väljer du den prenumeration som du vill flytta programmet till:

![Hanterings Portal: resurs hantering](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central-program från Azure Portal, är det här det föreslagna nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)