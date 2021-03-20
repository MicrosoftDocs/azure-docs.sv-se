---
title: Anpassa Azure IoT Central-ANVÄNDARGRÄNSSNITTET | Microsoft Docs
description: Anpassa temat och hjälp Länkar för ditt Azure IoT Central-program
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 660f6dfae23331455408ae7127274550a3a4cc3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90019843"
---
# <a name="customize-the-azure-iot-central-ui"></a>Anpassa Azure IoT Central UI

Den här artikeln beskriver hur du, som administratör, kan anpassa användar gränssnittet för ditt program genom att använda anpassade teman och ändra hjälp länkar så att de pekar på dina egna anpassade hjälp resurser. 

På följande skärm bild visas en sida med standard temat:

![Standard IoT Central-tema](./media/howto-customize-ui/standard-ui.png)

På följande skärm bild visas en sida med en anpassad skärm bild med de anpassade GRÄNSSNITTs elementen markerade:

![Anpassat IoT Central-tema](./media/howto-customize-ui/themed-ui.png)

## <a name="create-theme"></a>Skapa tema

Om du vill skapa ett anpassat tema navigerar du till sidan **Anpassa ditt program** i avsnittet **Administration** :

![IoT Central teman](./media/howto-customize-ui/themes.png)

På den här sidan kan du anpassa följande aspekter av ditt program:

### <a name="application-logo"></a>Program logo typ

En PNG-bild, som inte är större än 1 MB, med en genomskinlig bakgrund. Logo typen visas till vänster i namn listen för IoT Centrals programmet.

Om logo typens bild innehåller namnet på ditt program kan du dölja texten i program namnet. Mer information finns i [Hantera ditt program](howto-administer.md#change-application-name-and-url).

### <a name="browser-icon-favicon"></a>Webb läsar ikon (favicon)

En PNG-bild, inte större än 32 x 32 pixlar, med en genomskinlig bakgrund. En webbläsare kan använda den här avbildningen i adress fältet, historik, bok märken och fliken webbläsare.

### <a name="browser-colors"></a>Webb läsar färger

Du kan ändra färg på sidhuvudet och färgen som används för accent knappar och andra höjd punkter. Använd ett hexadecimalt färg värde med sex bokstäver i formatet `##ff6347` . Mer information om färg notation för **HEX-värde** finns i [HTML-färger](https://www.w3schools.com/html/html_colors.asp).

> [!NOTE]
> Du kan alltid återgå till standard alternativen på sidan **Anpassa ditt program** .

### <a name="changes-for-operators"></a>Ändringar för operatörer

Om en administratör skapar ett anpassat tema kan operatörer och andra användare av programmet inte längre välja ett tema i **Inställningar**.

## <a name="replace-help-links"></a>Ersätt hjälp länkar

Om du vill tillhandahålla anpassad hjälp information till dina operatörer och andra användare kan du ändra länkarna på programmets **Hjälp** -meny.

Om du vill ändra hjälp länkarna navigerar du till sidan **Anpassa hjälp** i avsnittet **Administration** :

![Anpassa IoT Central hjälp länkar](./media/howto-customize-ui/help-links.png)

Du kan också lägga till nya poster på Hjälp-menyn och ta bort standard poster:

![Anpassad IoT Central hjälp](./media/howto-customize-ui/custom-help.png)

> [!NOTE]
> Du kan alltid återgå till standard hjälp länkarna på sidan **Anpassa hjälp** .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du anpassar användar gränssnittet i ditt IoT Central-program, är det här några förslag på nästa steg:

- [Administrera ditt program](./howto-administer.md)
- [Lägga till paneler på din instrumentpanel](howto-add-tiles-to-your-dashboard.md)