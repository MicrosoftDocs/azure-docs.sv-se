---
title: Konfigurera Kerberos Key Distribution Center proxy Windows Virtual Desktop – Azure
description: Konfigurera en Windows-pool för virtuella skriv bord för att använda en Kerberos Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 01/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f123659941eaeb3b8ceeb6a999abf836eb9cf5ea
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798480"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy"></a>Konfigurera en Kerberos Key Distribution Center proxy

I den här artikeln visas hur du konfigurerar en KDC-proxy (Kerberos Key Distribiution Center) för din värddator. Den här proxyn gör det möjligt för organisationer att autentisera med Kerberos utanför deras företags gränser. Du kan till exempel använda KDC-proxyn för att aktivera SmartCard-autentisering för externa klienter.

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
