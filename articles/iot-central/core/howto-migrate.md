---
title: Migrera ett v2 Azure IoT Central-program till v3 | Microsoft Docs
description: Som administratör lär du dig hur du migrerar ditt v2 Azure IoT Central-program till v3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702733"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrera ditt v2 IoT Central-program till v3

*Den här artikeln gäller för administratörer.*

För närvarande är det ett v3-program när du skapar ett nytt IoT Central-program. Om du tidigare har skapat ett program kan det vara v2 om du har skapat det, beroende på när du skapade det. I den här artikeln beskrivs hur du migrerar en v2 till ett v3-program för att vara säker på att du använder de senaste IoT Central funktionerna.

Information om hur du identifierar versionen av ett IoT Central program finns i [om ditt program](howto-get-app-info.md).

Stegen för att migrera ett program från v2 till v3 är:

1. Skapa ett nytt v3-program från v2-programmet.
1. Konfigurera v3-programmet.
1. Ta bort till v2-program.

## <a name="create-a-new-v3-application"></a>Skapa ett nytt v3-program

Använd migreringsguiden för att skapa ett nytt v3-program.

IoT Central stöder inte migrering till ett befintligt v3-program. Om du vill flytta befintliga enheter automatiskt använder du migreringsguiden för att skapa ditt v3-program.

Guiden Migrera:

- Skapar ett nytt v3-program.
- Kontrollerar att enhetens mallar är kompatibla med v3.
- Kopierar alla dina enhets mallar till det nya v3-programmet.
- Kopierar alla användare och roll tilldelningar till det nya v3-programmet.

> [!NOTE]
> För att säkerställa enhetskompatibilitet med v3 blir primitiva typer i enhets mal len objekt egenskaper. Du behöver inte göra några ändringar i enhets koden.

Du måste vara administratör för att migrera ett program till v3.

1. I menyn **Administration** väljer du **migrera till ett v3-program**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Skärm bild som visar guiden Migrera program":::

1. Ange ett nytt **program namn** och ändra sedan den automatiskt genererade  **URL:** en. URL: en får inte vara samma som den aktuella v2-programmets URL. Du kan dock ändra URL: en senare när du har tagit bort ditt v2-program.

1. Välj **skapa en ny v3-app**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Skärm bild som visar alternativen i guiden Migrera program":::

    Det kan ta flera minuter att slutföra den här processen, beroende på antalet och komplexiteten för dina enhets mallar.

    > [!Warning]
    > Det går inte att skapa ditt v3-program om en enhets mall har fält som börjar med ett tal eller som innehåller något av följande tecken (,,,,,,,,,,,,,,,,,,, `+` `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\` ). DTDL för enhets mal len som v3-program använder tillåter inte dessa tecken. Uppdatera din enhets mall för att lösa problemet innan du migrerar till v3.

1. När den nya v3-appen är klar väljer du **öppna den nya appen** för att öppna den.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Skärm bild som visar guiden Migrera program efter programmigreringen":::

## <a name="configure-the-v3-application"></a>Konfigurera v3-programmet

När du har skapat det nya v3-programmet gör du ändringar i konfigurationen innan du flyttar dina enheter från v2-programmet till v3-programmet.

> [!TIP]
> Ägna en stund åt att [bekanta dig med v3](overview-iot-central-tour.md#navigate-your-application) eftersom det har några skillnader jämfört med den tidigare versionen.

Här följer några rekommenderade konfigurations steg för att tänka på:

- [Konfigurera instrument paneler](howto-add-tiles-to-your-dashboard.md)
- [Konfigurera data export](howto-export-data.md)
- [Konfigurera regler och åtgärder](quick-configure-rules.md)
- [Anpassa programmets användar gränssnitt](howto-customize-ui.md)

När ditt v3-program är konfigurerat för att uppfylla dina behov är du redo att flytta dina enheter från ditt v2-program till ditt v3-program.

## <a name="move-your-devices-to-the-v3-application"></a>Flytta dina enheter till v3-programmet

När det här steget är slutfört kommunicerar alla enheter bara med det nya v3-programmet.

> [!IMPORTANT]
> Innan du flyttar dina enheter till ditt v3-program tar du bort alla enheter som du har skapat i v3-programmet.

Det här steget flyttar alla befintliga enheter till ditt nya v3-program. Enhets data kopieras inte.

Välj **Flytta alla enheter** för att börja flytta enheterna. Det här steget kan ta flera minuter att slutföra.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Skärm bild som visar guiden Migrera program med alternativet flytta enheter":::

När flyttningen är klar startar du om alla enheter för att se till att de ansluter till det nya v3-programmet.

> [!WARNING]
> Ta inte bort ditt v3-program eftersom ditt v2-program inte kan sluta fungera.

## <a name="delete-your-old-v2-application"></a>Ta bort ditt gamla v2-program

När du har verifierat att allt fungerar som förväntat i ditt nya v3-program, tar du bort ditt gamla v2-program. Det här steget ser till att du inte får faktureras för ett program som du inte längre använder.

> [!Note]
> Om du vill ta bort ett program måste du ha behörighet att ta bort resurser i den Azure-prenumeration som du valde när du skapade programmet. Läs mer i [använda rollbaserad åtkomst kontroll för att hantera åtkomst till dina Azure-prenumerations resurser](../../role-based-access-control/role-assignments-portal.md).

1. I ditt v2-program väljer du fliken **Administration** på menyn
2. Välj **ta bort** för att ta bort ditt IoT Central program permanent. Med det här alternativet raderas alla data som är associerade med programmet permanent.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du migrerar ditt program är det föreslagna nästa steg att granska [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md) för att se vad som har ändrats i v3.