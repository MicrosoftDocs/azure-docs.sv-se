---
title: Konfigurera Kerberos Key Distribution Center proxy Windows Virtual Desktop – Azure
description: Konfigurera en Windows-pool för virtuella skriv bord för att använda en Kerberos Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99219663"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurera en Kerberos Key Distribution Center proxy (förhands granskning)

> [!IMPORTANT]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
> Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln visar hur du konfigurerar en Key Distribution Center Kerberos-proxyserver (för hands version) för din värd pool. Den här proxyn gör det möjligt för organisationer att autentisera med Kerberos utanför deras företags gränser. Du kan till exempel använda KDC-proxyn för att aktivera SmartCard-autentisering för externa klienter.

## <a name="how-to-configure-the-kdc-proxy"></a>Så här konfigurerar du KDC-proxyn

Så här konfigurerar du KDC-proxyn:

1. Logga in på Azure Portal som administratör.

2. Gå till sidan Windows Virtual Desktop.

3. Välj den värd pool som du vill aktivera KDC-proxy för och välj sedan **RDP-egenskaper**.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av Azure Portal sidan som visar en användare som väljer lagringspooler, sedan namnet på exempel värd-poolen och sedan RDP-egenskaper.](media/rdp-properties.png)

4. Välj fliken **Avancerat** och ange sedan ett värde i följande format utan blank steg:

    > kdcproxyname: s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![En skärm bild som visar fliken Avancerat vald, med det värde som anges enligt beskrivningen i steg 4.](media/advanced-tab-selected.png)

5. Välj **Spara**.

6. Den valda poolen ska nu börja utfärda RDP-anslutningsfiler med det kdcproxyname-fält som du angav.

## <a name="next-steps"></a>Nästa steg

RDGateway-rollen i Fjärrskrivbordstjänster innehåller en KDC-proxyserver. Se [distribuera RD Gateway-rollen i Windows Virtual Desktop](rd-gateway-role.md) för att konfigurera en så att den är ett mål för det virtuella Windows-skrivbordet.
