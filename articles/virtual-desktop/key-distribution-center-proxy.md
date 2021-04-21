---
title: Konfigurera Kerberos Key Distribution Center proxy Windows Virtual Desktop – Azure
description: Konfigurera en värdpool Windows Virtual Desktop använda en Kerberos-Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9dce264b7f2c88aed11f5b82a61f83cbac6c9697
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785117"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Konfigurera en Kerberos Key Distribution Center proxy (förhandsversion)

> [!IMPORTANT]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och vi rekommenderar inte att du använder den för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Säkerhetsmedvetna kunder, till exempel finans- eller myndighetsorganisationer, loggar ofta in med smartkort. Smartkort gör distributioner säkrare genom att kräva multifaktorautentisering (MFA). Men för RDP-delen av en Windows Virtual Desktop-session kräver smartkort en direkt anslutning, eller "synlinje", med en Active Directory-domänkontrollant (AD) för Kerberos-autentisering. Utan den här direkta anslutningen kan användarna inte automatiskt logga in på organisationens nätverk från fjärranslutningar. Användare i en Windows Virtual Desktop-distribution kan använda KDC-proxytjänsten för att proxystyra autentiseringstrafiken och logga in via en fjärrdator. KDC-proxyn tillåter autentisering för Remote Desktop Protocol en Windows Virtual Desktop session, så att användaren kan logga in på ett säkert sätt. Detta gör arbetet hemifrån mycket enklare och gör att vissa scenarier för haveriberedskap kan köras smidigare.

Att konfigurera KDC-proxyn innebär dock vanligtvis att tilldela Windows Server Gateway-rollen i Windows Server 2016 eller senare. Hur använder du en Fjärrskrivbordstjänster för att logga in på Windows Virtual Desktop? Som svar på det tar vi en snabb titt på komponenterna.

Det finns två komponenter i Windows Virtual Desktop-tjänsten som måste autentiseras:

- Flödet i den Windows Virtual Desktop som ger användarna en lista över tillgängliga skrivbord eller program som de har åtkomst till. Den här autentiseringsprocessen sker Azure Active Directory, vilket innebär att den här komponenten inte är fokus i den här artikeln.
- RDP-sessionen som resulterar från att en användare väljer en av de tillgängliga resurserna. Den här komponenten använder Kerberos-autentisering och kräver en KDC-proxy för fjärranvändare.

Den här artikeln visar hur du konfigurerar feeden i Windows Virtual Desktop klienten i Azure Portal. Mer information om hur du konfigurerar fjärrskrivbordsgatewayrollen finns i [Distribuera fjärrskrivbordsgatewayrollen](/azure/virtual-desktop/rd-gateway-role).

## <a name="requirements"></a>Krav

Om du vill Windows Virtual Desktop en sessionsvärd med en KDC-proxy behöver du följande:

- Åtkomst till Azure Portal ett Azure-administratörskonto.
- Fjärrklientdatorerna måste köra antingen Windows 10 eller Windows 7 och ha [Windows Desktop-klienten](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) installerad. Webbklienten stöds för närvarande inte.
- Du måste ha en KDC-proxy installerad på datorn. Mer information om hur du gör det finns i [Konfigurera fjärrskrivbordsgatewayrollen för Windows Virtual Desktop](rd-gateway-role.md).
- Datorns operativsystem måste vara Windows Server 2016 eller senare.

När du har sett till att du uppfyller dessa krav är du redo att komma igång.

## <a name="how-to-configure-the-kdc-proxy"></a>Så här konfigurerar du KDC-proxyn

Så här konfigurerar du KDC-proxyn:

1. Logga in på Azure Portal som administratör.

2. Gå till Windows Virtual Desktop sidan.

3. Välj den värdpool som du vill aktivera KDC-proxyn för och välj **sedan RDP-egenskaper.**

    > [!div class="mx-imgBorder"]
    > ![En skärmbild av Azure Portal visar en användare som väljer värdpooler, sedan namnet på exempelvärdpoolen och sedan RDP-egenskaper.](media/rdp-properties.png)

4. Välj fliken **Avancerat** och ange sedan ett värde i följande format utan blanksteg:

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![En skärmbild som visar fliken Avancerat markerad med värdet angivet enligt beskrivningen i steg 4.](media/advanced-tab-selected.png)

5. Välj **Spara**.

6. Den valda värdpoolen bör nu börja utfärda RDP-anslutningsfiler som innehåller det kdcproxyname-värde som du angav i steg 4.

## <a name="next-steps"></a>Nästa steg

Information om hur du hanterar Fjärrskrivbordstjänster av KDC-proxyn och tilldelar rollen fjärrskrivbordsgateway finns i [Distribuera fjärrskrivbordsgatewayrollen](rd-gateway-role.md).

Om du är intresserad av att skala KDC-proxyservrarna kan du läsa om hur du ställer in hög tillgänglighet för KDC-proxy på Lägg till hög tillgänglighet för webb- och [gatewaywebbtjänsten](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)för fjärrskrivbord.
