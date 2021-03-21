---
title: Skapa instrument paneler för Azure IoT Central | Microsoft Docs
description: Lär dig hur du skapar och hanterar dina instrument paneler.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6fc99470fdc52a2dc6553056f305226f8348550c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366729"
---
# <a name="create-and-manage-multiple-dashboards"></a>Skapa och hantera flera instrument paneler

**Instrument panelen** är sidan som läses in när du först navigerar till ditt program. Ett **verktyg** i ditt program definierar standard program instrument panelen för alla användare. Du kan också skapa en egen, anpassad program instrument panel. Du kan ha flera instrument paneler som visar olika data och växlar mellan dem.

Om du är **administratör** för programmet kan du också skapa upp till 10 instrument paneler på program nivå för att dela med andra användare av programmet. Endast **Administratörer** kan skapa, redigera och ta bort instrument paneler på program nivå.  

## <a name="create-dashboard"></a>Skapa instrument panel

På följande skärm bild visas instrument panelen i ett program som skapats från den **anpassade program** mal len. Du kan ersätta standard instrument panelen med en personlig instrument panel, eller om du är administratör, en annan instrument panel för program nivå. Det gör du genom att välja **+ ny** längst upp till vänster på sidan.

> [!div class="mx-imgBorder"]
> ![Instrument panel för program som baseras på mallen "anpassat program"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Om du väljer **+ ny** öppnas instrument panels redigeraren. I redigeraren kan du ge instrument panelen ett namn och välja objekt i biblioteket. Biblioteket innehåller panelerna och de primitiver som du kan använda för att anpassa instrument panelen.

> [!div class="mx-imgBorder"]
> ![Instrument panels bibliotek](media/howto-create-personal-dashboards/dashboard-library.png)

Om du är **administratör** för programmet får du möjlighet att skapa en instrument panel för personliga instrument eller en instrument panel för program nivå. Om du skapar en instrument panel på personliga nivåer kan du bara se den. Om du skapar en instrument panel för program nivå kan alla användare av programmet se den. När du har angett en rubrik och valt den typ av instrument panel som du vill skapa, kan du spara och lägga till paneler senare. Eller om du är redo och har lagt till en mall och en enhets instans kan du gå vidare och skapa din första panel.  

> [!div class="mx-imgBorder"]
> ![Konfigurera enhets information – formulär med Detaljer för temperatur](media/howto-create-personal-dashboards/device-details.png)

Du kan till exempel lägga till en **telemetri** -panel för enhetens aktuella temperatur. Så här gör du:

1. Välj en **mall för enhet**
1. Välj en enhet från **enheter** för den enhet som du vill se på en panel i instrument panelen. Sedan visas en lista över enhetens egenskaper som kan användas på panelen.
1. Om du vill skapa panelen på instrument panelen väljer du **temperatur** och drar den till instrument panels ytan. Du kan också markera kryss rutan bredvid **temperatur** och välja **Lägg till panel**. Följande skärm bild visar hur du väljer en enhets mall och en enhet och sedan skapar en panel för temperatur telemetri på instrument panelen.
1. Välj **Spara** längst upp till vänster för att spara ändringarna på instrument panelen.

> [!div class="mx-imgBorder"]
> ![Fliken instrument panel med information om temperatur panelen](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Nu när du visar din personliga instrument panel visas den nya panelen med **temperatur** inställningen för enheten:

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar den nya panelen med temperatur inställningen för enheten.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Du kan utforska andra panel typer i biblioteket för att lära dig hur du kan anpassa dina personliga instrument paneler ytterligare.

Mer information om hur du använder paneler i Azure IoT Central finns i [lägga till paneler på instrument panelen](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Hantera instrument paneler

Du kan ha flera personliga instrument paneler och växla mellan dem eller välja någon av standard instrument panelerna för programmet:

> [!div class="mx-imgBorder"]
> ![Växla mellan instrument paneler](media/howto-create-personal-dashboards/switch-dashboards.png)

Du kan redigera dina personliga instrument paneler och ta bort eventuella instrument paneler som du inte längre behöver. Om du är **administratör** kan du även redigera eller ta bort instrument paneler på program nivå.

> [!div class="mx-imgBorder"]
> ![Ta bort instrument paneler](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar och hanterar personliga instrument paneler kan du [lära dig hur du hanterar dina program inställningar](howto-manage-preferences.md).
