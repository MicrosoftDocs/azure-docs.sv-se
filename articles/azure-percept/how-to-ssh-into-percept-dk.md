---
title: Ansluta till din Azure percept DK över SSH
description: Lär dig hur du kan använda SSH i din Azure percept DK med SparaTillFil
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096623"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Ansluta till din Azure percept DK över SSH

Följ stegen nedan för att konfigurera en SSH-anslutning till din Azure percept-DK via [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Förutsättningar

- En Windows-, Linux-eller OS X-baserad värd dator med Wi-Fi kapacitet
- En SSH-klient
    - Om värddatorn kör Windows är [SparaTillFil](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) en effektiv SSH-klient och kommer att användas i den här guiden.
    - Om värddatorn kör Linux eller OS X ingår SSH-tjänster i dessa operativ system och kan köras utan ett separat klient program. Mer information om hur du kör SSH-tjänster finns i produkt dokumentationen för operativ systemet.
- Azure percept DK
- Konfigurera ett SSH-inloggningsnamn under den [inspelade Azure PERCEPT DK-upplevelsen](./quickstart-percept-dk-set-up.md)

## <a name="initiate-the-ssh-connection"></a>Initiera SSH-anslutningen

1. Starta Azure percept DK (dev-paketet)

1. Om ditt dev Kit redan är anslutet till ett nätverk via Ethernet eller Wi-Fi går du vidare till nästa steg. Annars ansluter värddatorn direkt till dev-paketets Wi-Fi åtkomst punkt, precis som att ansluta till något annat Wi-Fi nätverk:
    - **nätverks namn**: SCZ-xxxx (där "xxxx" är de sista fyra siffrorna i dev-paketets Mac-nätverks adress)
    - **lösen ord**: hittar du på Välkomst kortet som medföljde dev kit

    > [!WARNING]
    > När du är ansluten till Azure percept DK Wi-Fi åtkomst punkten kommer värddatorn tillfälligt att förlora anslutningen till Internet. Aktiva video konferens anrop, webdirektuppspelning eller andra nätverksbaserade upplevelser avbryts tills steg 3 i den inspelade Azure percept DK-upplevelsen har slutförts.

1. Öppna PuTTY. Ange följande och klicka på **Öppna** för ssh i din devkit:

    1. Värdnamn: 10.1.1.1
    1. Port: 22
    1. Anslutnings typ: SSH

    > [!NOTE]
    > **Värd namnet** är ENHETens IP-adress. Om ditt dev-paket är anslutet till dev-paketets Wi-Fi åtkomst punkt, blir IP-adressen 10.1.1.1. Om ditt dev kit är anslutet via Ethernet använder du den lokala IP-adressen för enheten, som du kan hämta från Ethernet-routern eller hubben. Om enheten är ansluten via Wi-Fi måste du använda den IP-adress som tillhandahölls under den [inspelade Azure PERCEPT DK-upplevelsen](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Avbildning.":::

1. Logga in på den SparaTillFil-terminalen med SSH-användarnamnet och lösen ordet som skapades under den här insikts upplevelsen.

## <a name="next-steps"></a>Nästa steg

När du har anslutit till din Azure percept DK via SSH kan du utföra en mängd olika uppgifter, inklusive fel sökning, USB-uppdateringar och köra verktyget DiagTool eller SoftAP.