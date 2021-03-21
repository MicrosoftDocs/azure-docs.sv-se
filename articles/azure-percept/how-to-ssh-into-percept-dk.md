---
title: Ansluta till din Azure percept DK över SSH
description: Lär dig hur du kan använda SSH i din Azure percept DK med SparaTillFil
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721485"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Ansluta till din Azure percept DK över SSH

Följ stegen nedan för att konfigurera en SSH-anslutning till din Azure percept DK via OpenSSH eller [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Förutsättningar

- En Windows-, Linux-eller OS X-baserad värd dator med Wi-Fi kapacitet
- En SSH-klient (se nästa avsnitt för installations vägledning)
- En Azure percept DK (dev-paket)
- En SSH-inloggning som skapats under [installationen av Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Installera önskad SSH-klient

Om värddatorn kör Linux eller OS X ingår SSH-tjänster i dessa operativ system och kan köras utan ett separat klient program. Mer information om hur du kör SSH-tjänster finns i produkt dokumentationen för operativ systemet.

Om värddatorn kör Windows kan du välja mellan två alternativ för SSH-klienten: OpenSSH och SparaTillFil.

### <a name="openssh"></a>OpenSSH

Windows 10 innehåller en inbyggd SSH-klient med namnet OpenSSH som kan köras med ett enkelt kommando i en kommando tolk. Vi rekommenderar att du använder OpenSSH med Azure percept om det är tillgängligt för dig. Följ dessa steg om du vill kontrol lera om Windows-datorn har OpenSSH installerat:

1. Gå till **Start**  ->  **Inställningar**.

1. Välj **Appar**.

1. Under **appar & funktioner** väljer du **valfria funktioner**.

1. Skriv **openssh-klienten** i Sök fältet **installerade funktioner** . Om OpenSSH visas är klienten redan installerad och du kan gå vidare till nästa avsnitt. Om du inte ser OpenSSH klickar du på **Lägg till en funktion**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Skärm bild av inställningar som visar installations status för OpenSSH.":::

1. Välj **openssh-klient** och klicka på **Installera**. Nu kan du gå vidare till nästa avsnitt. Om OpenSSH inte är tillgänglig för installation på datorn följer du stegen nedan för att installera SparaTillFil, en SSH-klient från tredje part.

### <a name="putty"></a>PuTTY

Om din Windows-dator inte innehåller OpenSSH rekommenderar vi att du använder [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Slutför följande steg för att ladda ned och installera SparaTillFil:

1. Gå till [hämtnings sidan för SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Under **paketfiler** klickar du på filen 32-bitars eller 64-bitars. msi för att ladda ned installations programmet. Om du är osäker på vilken version du ska välja kan du läsa [vanliga frågor och svar](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Starta installations processen genom att klicka på installations programmet. Följ anvisningarna efter behov.

1. Grattis! Du har installerat den SparaTillFil-SSH-klienten.

## <a name="initiate-the-ssh-connection"></a>Initiera SSH-anslutningen

1. Slå på din Azure percept DK.

1. Om ditt dev Kit redan är anslutet till ett nätverk via Ethernet eller Wi-Fi går du vidare till nästa steg. Annars ansluter du värddatorn direkt till dev-paketets Wi-Fi åtkomst punkt. Som du ansluter till andra Wi-Fi nätverk, öppnar du nätverks-och Internet inställningar på datorn, klickar på följande nätverk och anger nätverks lösen ordet när du uppmanas till det:

    - **Nätverks namn**: beroende på operativ systemets version av dev kit, är namnet på Wi-Fi åtkomst punkten antingen **SCZ-xxxx** eller **APD-xxxx** (där "xxxx" är de sista fyra siffrorna i dev-paketets Mac-adress)
    - **Lösen ord**: hittar du på Välkomst kortet som medföljde dev kit

    > [!WARNING]
    > När du är ansluten till Azure percept DK Wi-Fi åtkomst punkten kommer värddatorn tillfälligt att förlora anslutningen till Internet. Aktiva video konferens anrop, webb direkt uppspelning eller andra nätverksbaserade upplevelser avbryts.

1. Slutför SSH-anslutnings processen enligt SSH-klienten.

### <a name="using-openssh"></a>Använda OpenSSH

1. Öppna en kommando tolk (**Starta**  ->  **kommando tolken**).

1. Ange följande i kommando tolken:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Om datorn är ansluten till dev-paketets Wi-Fi åtkomst punkt blir IP-adressen 10.1.1.1. Om ditt dev kit är anslutet via Ethernet använder du den lokala IP-adressen för enheten, som du kan hämta från Ethernet-routern eller hubben. Om ditt dev-paket är anslutet via Wi-Fi måste du använda den IP-adress som har tilldelats ditt dev-paket under [installationen av Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Om ditt dev kit är anslutet till ett Wi-Fi nätverk men du inte vet dess IP-adress, går du till Azure percept Studio och [öppnar enhetens video ström](./how-to-view-video-stream.md). I adress fältet på fliken Video Ströms läsare visas enhetens IP-adress.

1. Ange SSH-lösenordet när du uppmanas till det.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Skärm bild av inloggning med öppen SSH-Kommandotolken.":::

1. Om det här är första gången du ansluter till ditt dev-paket via OpenSSH kan du också uppmanas att godkänna värdens nyckel. Ange **Ja** för att godkänna nyckeln.

1. Grattis! Du har anslutit till ditt dev-paket via SSH.

### <a name="using-putty"></a>Använda SparaTillFil

1. Öppna PuTTY. Ange följande i fönstret **SparaTillFil-konfiguration** och klicka på **Öppna** för ssh i ditt dev-paket:

    1. Värdnamn: [IP-adress]
    1. Port: 22
    1. Anslutnings typ: SSH

    **Värd namnet** är ditt dev-pakets IP-adress. Om datorn är ansluten till dev-paketets Wi-Fi åtkomst punkt blir IP-adressen 10.1.1.1. Om ditt dev kit är anslutet via Ethernet använder du den lokala IP-adressen för enheten, som du kan hämta från Ethernet-routern eller hubben. Om ditt dev-paket är anslutet via Wi-Fi måste du använda den IP-adress som har tilldelats ditt dev-paket under [installationen av Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Om ditt dev kit är anslutet till ett Wi-Fi nätverk men du inte vet dess IP-adress, går du till Azure percept Studio och [öppnar enhetens video ström](./how-to-view-video-stream.md). I adress fältet på fliken Video Ströms läsare visas enhetens IP-adress.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Skärm bild av fönstret SparaTillFil-konfiguration.":::

1. En SparaTillFil-Terminal öppnas. När du uppmanas till det anger du ditt SSH-användarnamn och lösen ord i terminalen.

1. Grattis! Du har anslutit till ditt dev-paket via SSH.

## <a name="next-steps"></a>Nästa steg

När du har anslutit till din Azure percept DK via SSH kan du utföra en mängd olika uppgifter, inklusive [enhets fel sökning](./troubleshoot-dev-kit.md) och [USB-uppdateringar](./how-to-update-via-usb.md).