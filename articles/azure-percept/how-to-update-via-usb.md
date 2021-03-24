---
title: Uppdatera din Azure percept DK över en USB-anslutning
description: Lär dig hur du uppdaterar Azure percept DK över en USB-anslutning
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887758"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Så här uppdaterar du Azure percept DK över en USB-anslutning

Även om du använder OTA-uppdateringar (över-Air) är den bästa metoden för att hålla ditt utvecklings pakets operativ system och inbyggd program vara uppdaterad, finns det scenarier där du behöver uppdatera (eller "blinka") dev-paketet via en USB-anslutning:

- Det går inte att uppdatera en OTA på grund av anslutning eller andra tekniska problem
- Enheten måste återställas till fabriks läget igen

Den här guiden visar hur du kan uppdatera ditt utvecklings pakets operativ system och inbyggd program vara via en USB-anslutning.

> [!WARNING]
> Om du uppdaterar ditt dev-paket via USB raderas alla befintliga data på enheten, inklusive AI-modeller och behållare.
>
> Följ alla instruktioner i ordning. Att hoppa över steg kan leda till att ditt dev kit är i ett oanvändbart tillstånd.

## <a name="prerequisites"></a>Förutsättningar

- En Azure percept DK
- En Windows-, Linux-eller OS X-baserad värd dator med Wi-Fi kapacitet och en tillgänglig USB-C-eller USB-A-port
- En USB-C till USB-kabel (valfritt, säljs separat)
- En SSH-inloggning som skapats under [installationen av Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Hämta program varu verktyg och uppdateringsfiler

1. [Verktyget NXP uuu](https://github.com/NXPmicro/mfgtools/releases). Hämta den **senaste versionen** uuu.exe-filen (för Windows) eller uuu-filen (för Linux) under fliken **till gångar** .

1. [7-zip](https://www.7-zip.org/). Den här program varan kommer att användas för att extrahera RAW-bildfilen från den komprimerade filen XZ. Hämta och installera lämplig. exe-fil.

1. [Hämta uppdateringsfilerna](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Se till att alla tre Bygg artefakter finns:
    - Azure-percept-DK-*&lt; versions &gt; nummer*. RAW. XZ
    - fast-HAB-FW. RAW
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Konfigurera din miljö

1. Skapa en mapp/katalog på värddatorn på en plats som är lätt att komma åt via kommando raden.

1. Kopiera UUU-verktyget (**uuu.exe** eller **uuu**) till den nya mappen.

1. Extrahera filen **Azure-percept-DK-*&lt; version Number &gt;*. RAW** från den komprimerade filen genom att högerklicka på **Azure-percept-DK-*&lt; version Number &gt;*. RAW. XZ** och välja **7 zip** - &gt; **extrahering här**.

1. Flytta den extraherade **Azure-percept-DK-*&lt; version &gt; Number*. RAW-** filen, **fast-HAB-FW. RAW** och **emmc_full.txt** till mappen som innehåller uuu-verktyget.

## <a name="update-your-device"></a>Uppdatera din enhet

1. [SSH till ditt dev-paket](./how-to-ssh-into-percept-dk.md).

1. Öppna sedan en kommando tolk i Windows (**Starta**  >  **cmd**) eller en Linux-terminal och navigera till mappen där uppdateringsfiler och uuu-verktyget lagras. Ange följande kommando i kommando tolken eller terminalen för att förbereda datorn för att ta emot en flashable-enhet:

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Koppla från Azure percept Vision-enheten från operatörens USB C-port.

1. Anslut den angivna USB-C-kabeln till nätverkskortets USB-C-port och värd datorns USB-C-port. Om datorn bara har en USB-A-port ansluter du en USB-C till USB-A-kabel (säljs separat) till frakt kortet och värddatorn.

1. I kommando tolken för SSH-klienten anger du följande kommandon:

    1. Ställ in enhet till USB-uppdaterings läge:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Starta om enheten. Installationen av uppdateringen påbörjas.

        ```bash
        sudo reboot -f
        ```

1. Gå tillbaka till den andra kommando tolken eller terminalen. När uppdateringen är avslutad visas ett meddelande med ```Success 1    Failure 0``` :

    > [!NOTE]
    > Efter uppdateringen återställs enheten till fabriks inställningarna och du kommer att förlora Wi-Fi anslutningen och SSH-inloggningen.

1. Stäng av frakt kortet när uppdateringen är klar. Koppla från USB-kabeln från datorn.  

## <a name="next-steps"></a>Nästa steg

Arbeta med [installations upplevelsen för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) för att konfigurera om enheten.