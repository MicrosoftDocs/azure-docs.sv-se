---
title: Ansluta till din Azure percept-DK över seriell
description: Lär dig hur du konfigurerar en seriell anslutning till din Azure percept DK med SparaTillFil och en seriell USB-till-TTL-kabel
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663819"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Ansluta till din Azure percept-DK över seriell

Följ stegen nedan för att konfigurera en seriell anslutning till din Azure percept-DK via [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> Försök **inte** ansluta din devkit över seriellt, förutom vid extrema haverier (t. ex. du har brickorat enheten). Att ta bort transport örens hölje för att ansluta den seriella kabeln är mycket svårt och delar Wi-Fi antenn kablar.

## <a name="prerequisites"></a>Förutsättningar

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Värd dator
- Azure percept DK
- [Seriell kabel för USB till TTL](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="Seriell kabel för USB till TTL.":::

## <a name="initiate-the-serial-connection"></a>Initiera seriell anslutning

1. Om operatörs kortet är anslutet till en 80/20-järnväg tar du bort den från spåret med hjälp av hex-nyckeln (ingår i välkomst kortet devkit).

1. Ta bort skruvarna på underside för transport örens hölje och extrahera moder kortet.

    > [!WARNING]
    > Om du tar bort moder kortet bryts Wi-Fi antennens kablar. Fortsätt **inte** med den seriella anslutningen om det inte är sista utväg att återställa enheten.

1. Ta bort heatsink.

1. Ta bort bygel-tavlan från GPIO-stiften.

    > [!TIP]
    > Anteckna riktningen på bygeln innan du tar bort den. Du kan till exempel rita en pil på eller koppla en klister till byglarna som pekar på kretsen för referens. Bygel-tavlan är inte nyckelbaserad och kan vara oavsiktligt ansluten när du monterar om kortet.

1. Anslut [USB-till-TTL-kabeln](https://www.adafruit.com/product/954) till GPIO-stiften på moder kortet som visas nedan. Observera att den röda kabeln inte är ansluten.

    - Anslut den svarta kabeln (GND) till PIN 6.
    - Anslut den vita kabeln (RX) till PIN-kod 8.
    - Anslut den gröna kabeln (TX) till PIN-koden 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Anslutningar för seriella operatörs kort.":::

1. Slå på din devkit och Anslut USB-sidan av den seriella kabeln till din dator.

1. I Windows går du till **Start**  ->  **Windows Update inställningar**  ->  **Visa valfria uppdateringar**  ->  **driv rutins uppdateringar**. Sök efter en seriell till USB-uppdatering i listan, markera kryss rutan bredvid den och klicka på **Ladda ned och installera**.  

1. Öppna sedan Windows Enhetshanteraren (**Starta**  ->  **Enhetshanteraren**). Gå till **portar** och klicka på **USB till UART** för att öppna **Egenskaper**. Notera vilken COM-port enheten är ansluten till.

1. Klicka på fliken **port inställningar** . Kontrol lera att **bitar per sekund** är inställt på 115200.

1. Öppna PuTTY. Ange följande och klicka på **Öppna** för att ansluta till din devkit via serie:

    1. Seriell linje: COM [port #]
    1. Hastighet: 115200
    1. Anslutnings typ: serie

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="Fönstret SparaTillFil-session med valda serie parametrar.":::

## <a name="next-steps"></a>Nästa steg

Om du vill uppdatera en enhet som inte kan startas över seriellt med [USB-till-TTL-kabeln](https://www.adafruit.com/product/954)kan du läsa mer i USB-uppdaterings hand boken för andra situationer än standard situationer.

[comment]: # (Lägg till en länk till en USB-uppdaterings guide när den är tillgänglig.)